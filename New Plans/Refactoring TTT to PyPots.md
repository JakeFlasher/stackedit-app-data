Below is a step-by-step guide (with detailed explanations and code) demonstrating how one might refactor or reshape a test-time training (TTT) model (originally a naïve RNN approach) into a structure aligned with how PyPOTS handles the “transformer” imputation, incorporating the same key ideas:  
1) Concatenate original time series values and missing masks in the embedding (as SAITS does).  
2) Use the same training approach with ORT and MIT losses (as in SAITS).  
3) Produce imputed outputs that fill unknown entries with the model’s predictions but leave known entries intact.  

Everything here is spelled out in detail so you can see how to replicate the example of PyPOTS’s “Transformer” approach for another model such as a test-time training RNN. Even if you ultimately replace the RNN with a different backbone (e.g. a GRU, LSTM, or another family of modules), the framework can remain the same.  

--------------------------------------------------------------------------------
1. UNDERSTAND THE PYPOPTS STRUCTURE FOR IMPUTATION
--------------------------------------------------------------------------------

• In PyPOTS’s “transformer” approach (files like model_imputation.py, core_imputatiuon.py), the key points are:  
  – The forward pass needs four main items in training:  
    1) X (partially observed data)  
    2) missing_mask (1 if observed, 0 if missing)  
    3) X_ori (the ground-truth data, used for the ORT and MIT losses)  
    4) indicating_mask (a mask telling which values are artificially masked for training, i.e. which are used for MIT Loss)  

  – X and missing_mask get sent into an embedding that concatenates them (like SAITS does).  
  – The embedding output is processed by one or more layers of the model’s main backbone (in the Transformer example, it’s a multi-head self-attention block).  
  – The final output is linearly projected back to the original data dimension.  
  – For all entries that are observed, the output is overwritten by the original observed values (so the model only tries to predict missing entries).  
  – ORT and MIT losses are computed in the same way as SAITS does.  

Therefore, to replicate “test-time training RNN” inside the same pipeline, we must:  
  – Write a wrapper PyTorch module (similar to _Transformer) that:  
    1) Embeds the inputs.  
    2) Passes the embedded vector through an RNN.  
    3) Projects hidden states back to the original data dimension.  
    4) Masks out the observed entries (the ones that are not missing).  
    5) Returns a dictionary containing the imputed_data and the losses if in training.  

--------------------------------------------------------------------------------
2. DETAILED MATH / LOGIC FOR “SAITS-LIKE” EMBEDDING + ORT+MIT
--------------------------------------------------------------------------------

Below is a summary of the key math:  

• Let X ∈ ℝ^(B×T×F) be the (batch_size × time_steps × features) time-series.  
• Let M ∈ {0,1}^(B×T×F) be the missing_mask with 1 for observed entries, 0 for missing.  
• In SAITS or PyPOTS, we construct an “augmented” input by concatenating X and M along the feature dimension:  

  (1)  X' = Concat(X, M)   // shape is (B, T, 2F)  

• Then we pass X' through an “embedding” layer to transform from 2F → d_model, possibly adding positional embeddings:  

  (2)  E = SAITSEmbedding(X') ∈ ℝ^(B×T×d_model)  

• The backbone (Transformer, or RNN if we’re adopting TTT RNN) transforms E → H:  

  (3)  H = RNN(E)  // shape (B, T, d_model)  

  or in Transformer version: H = SelfAttention(E)  

• Then we linearly project H from d_model back to F:  

  (4)  R = W * H + b  // shape (B, T, F)  

  where W ∈ ℝ^(F×d_model), b ∈ ℝ^(F).  

• We create the final “imputed_data” by combining R with the observed part of X:  

  (5)  imputed_data = M ⊙ X + (1 - M) ⊙ R  

  (where ⊙ is elementwise multiplication).  

• Meanwhile, in training, an ORT+MIT loss is computed similarly to SAITS:  
  – ORT = ||(R - X_ori) ⊙ (1 - M_obs)||²   (squared error for the originally missing part)  
  – MIT = ||(R - X_ori) ⊙ IndicatingMask||² (squared error for artificially masked entries)  
  – total_loss = ORT_weight * ORT + MIT_weight * MIT  

  where:  
    – X_ori is the original ground truth with no artificial missingness.  
    – M_obs is the original missing mask.  
    – IndicatingMask is the artificial missing mask telling which entries are deliberately masked for training.  

If you’re new to PyTorch, the code below shows step by step how to implement these.  

--------------------------------------------------------------------------------
3. EXAMPLE CODE: A REFASHIONED NAÏVE “TTT RNN” FOR IMPUTATION 
--------------------------------------------------------------------------------

Below is an illustrative code snippet that tries to look structurally similar to the PyPOTS “transformer” code, but uses an RNN backbone for test-time training. We’ll use a simple GRU as an example. You can replace GRU with LSTM, plain RNN, or any other custom cell.

Note: The code snippet is long, but each part is annotated. It recommends how your “new TTT RNN” model might be placed in a file analogous to core_imputatiuon.py, with a class name like _TTT_RNN or something, then used exactly as the PyPOTS Transformer is used in model_imputation.py.

--------------------------------------------------------------------------------
CODE SNIPPET  
--------------------------------------------------------------------------------

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

######################################
# 1) SAITS-Like Embedding
######################################
class SaitsEmbedding(nn.Module):
    """
    This is a simplified version of the SAITS embedding from PyPOTS’s modules.
    We concatenate X and missing_mask, then optionally add positional encoding.
    """
    def __init__(self, in_dim, out_dim, with_pos=True, n_max_steps=100, dropout=0.0):
        super().__init__()
        self.in_dim = in_dim
        self.out_dim = out_dim
        self.with_pos = with_pos

        # Convert from in_dim to out_dim
        self.proj = nn.Linear(in_dim, out_dim)
        self.dropout = nn.Dropout(dropout)

        # Optional positional embedding (simplest version)
        if self.with_pos:
            self.pos_embedding = nn.Embedding(n_max_steps, out_dim)

    def forward(self, X, missing_mask):
        # X: (B, T, F)
        # missing_mask: (B, T, F), 1 if observed, 0 if missing
        # We create (B, T, 2F) by concatenating
        merged = torch.cat([X, missing_mask], dim=-1)  # shape: (B, T, 2F)

        out = self.proj(merged)  # shape: (B, T, out_dim)

        if self.with_pos:
            B, T, _ = out.shape
            # positions = 0..T-1
            positions = torch.arange(T, device=out.device).unsqueeze(0).expand(B, T)
            pos_emb = self.pos_embedding(positions)  # shape (B, T, out_dim)
            out = out + pos_emb

        out = self.dropout(out)
        return out

######################################
# 2) Loss for ORT+MIT
######################################
class SaitsLoss(nn.Module):
    """
    A minimal version of the SAITS loss that combines ORT and MIT.
    """
    def __init__(self, ORT_weight=1.0, MIT_weight=1.0):
        super().__init__()
        self.ORT_weight = ORT_weight
        self.MIT_weight = MIT_weight

    def forward(self, reconstruction, X_ori, missing_mask, indicating_mask):
        """
        reconstruction: (B, T, F)
        X_ori: (B, T, F)
        missing_mask: (B, T, F) the real missingness. 1=observed, 0=missing
        indicating_mask: (B, T, F) artificially masked ones (for MIT)
        """
        # ORT: original missing region
        #   -> M=0 means originally missing
        ORT_region = (1 - missing_mask)  # we want the originally missing
        ORT_mse = ((reconstruction - X_ori) * ORT_region) ** 2

        # MIT: artificially masked region
        MIT_region = indicating_mask
        MIT_mse = ((reconstruction - X_ori) * MIT_region) ** 2

        ORT_loss = ORT_mse.mean()
        MIT_loss = MIT_mse.mean()
        loss = self.ORT_weight * ORT_loss + self.MIT_weight * MIT_loss

        return loss, ORT_loss, MIT_loss

######################################
# 3) OUR “TTT RNN” FOR IMPUTATION
######################################
class _TTT_RNN(nn.Module):
    """
    An example RNN-based model that follows the same structure as _Transformer in PyPOTS:
    1) Embedding X + missing_mask
    2) RNN-based backbone
    3) Linear output projection
    4) Construct imputed_data
    5) Compute ORT+MIT if in training
    """
    def __init__(
        self,
        n_steps: int,
        n_features: int,
        n_layers: int,
        d_model: int,
        hidden_size: int,
        dropout: float,
        # ORT & MIT weights
        ORT_weight: float = 1.0,
        MIT_weight: float = 1.0,
    ):
        super().__init__()
        self.n_layers = n_layers
        self.n_features = n_features
        self.n_steps = n_steps
        self.ORT_weight = ORT_weight
        self.MIT_weight = MIT_weight

        # 1) Embedding: in_dim=2*n_features because we concat X and mask
        self.saits_embedding = SaitsEmbedding(
            in_dim=n_features * 2,
            out_dim=d_model,
            with_pos=True,
            n_max_steps=n_steps,
            dropout=dropout,
        )

        # 2) RNN-based backbone. We can do GRU or LSTM.
        #    Here we do a GRU with hidden_size = d_model for simplicity.
        #    We keep n_layers just as an example (stack multiple GRU layers).
        self.rnn = nn.GRU(
            input_size=d_model,
            hidden_size=hidden_size,
            num_layers=n_layers,
            batch_first=True,
            dropout=dropout if n_layers > 1 else 0.0,
        )

        # 3) Output projection back to F
        self.output_projection = nn.Linear(hidden_size, n_features)

        # 4) Define the SAITS-style loss
        self.saits_loss_func = SaitsLoss(ORT_weight, MIT_weight)

    def forward(self, inputs: dict, training: bool = True) -> dict:
        """
        inputs = {
          "X": (B, T, F),
          "missing_mask": (B, T, F),
          # The below two only appear in training:
          "X_ori": (B, T, F),
          "indicating_mask": (B, T, F)
        }
        """
        X = inputs["X"]                  # (B, T, F)
        missing_mask = inputs["missing_mask"]  # (B, T, F)

        # 1) Embedding
        embedding_out = self.saits_embedding(X, missing_mask)  # (B, T, d_model)

        # 2) RNN backbone forward
        #    If multiple layers, hidden shape is (n_layers, B, hidden_size)
        rnn_out, hidden = self.rnn(embedding_out)  # (B, T, hidden_size), hidden=(n_layers, B, hidden_size)

        # 3) Projection
        reconstruction = self.output_projection(rnn_out)  # (B, T, F)

        # 4) We fill in the originally observed parts from the real data
        imputed_data = missing_mask * X + (1 - missing_mask) * reconstruction

        # Build results dict
        results = {
            "imputed_data": imputed_data,
        }

        # 5) If training, compute ORT+MIT
        if training:
            X_ori = inputs["X_ori"]
            indicating_mask = inputs["indicating_mask"]
            loss, ORT_loss, MIT_loss = self.saits_loss_func(
                reconstruction, X_ori, missing_mask, indicating_mask
            )
            results["ORT_loss"] = ORT_loss
            results["MIT_loss"] = MIT_loss
            results["loss"] = loss  # This is the main item for backward()

        return results
```

Explanation of key points in the code:

• SaitsEmbedding  
  – This concatenates (X, missing_mask) along the last dimension to form [B, T, 2F].  
  – Then goes through a Linear(d_model).  
  – Optionally adds positional encoding (as the Transformer does in PyPOTS).  

• _TTT_RNN class  
  – initialization:  
    1) We store the parameters (n_steps, n_features, etc.).  
    2) We create SaitsEmbedding as above.  
    3) We create the RNN itself (in this example, a GRU). You can swap it for LSTM, etc.  
    4) We create a final linear projection to get shape (B, T, F).  
    5) We create a SaitsLoss instance to handle ORT and MIT.  

  – forward(...)  
    1) We embed X, missing_mask → shape (B, T, d_model).  
    2) We pass that through the GRU to get shape (B, T, hidden_size).  
    3) We linearly project to shape (B, T, F), calling that reconstruction.  
    4) We produce imputed_data by combining the model’s reconstruction for the missing parts with the original data for the observed parts.  
    5) If training, compute the ORT+MIT losses and store them in results.  

You can further wrap _TTT_RNN in a higher-level class (like TTTForImputation or TTTModel) just like TTTForCausalLM or TTTModel in TTT’s code or TTTModel in PyPOTS. That class would handle loading data, preparing batches, and so forth—mirroring what model_imputation.py does with the Transformer.

--------------------------------------------------------------------------------
4. PUTTING IT ALL TOGETHER
--------------------------------------------------------------------------------

With that single “_TTT_RNN” module in place, you can do what PyPOTS does with “model = _Transformer(...).” For example:

1) In a separate file “model_test_time_training.py,” do something like:

```python
import torch
from torch.utils.data import DataLoader
from .core_test_time_rnn import _TTT_RNN
from ...base import BaseNNImputer  # or similar from PyPOTS

class TTT_RNN_Imputer(BaseNNImputer):
    def __init__(self, 
                 n_steps, 
                 n_features,
                 n_layers,
                 d_model,
                 hidden_size,
                 dropout=0.0,
                 ORT_weight=1.0,
                 MIT_weight=1.0,
                 # plus any other hyperparams like epochs, batch_size, etc.
                 ):
        super().__init__(
            # pass higher-level hyperparams to BaseNNImputer
        )
        self.model = _TTT_RNN(
            n_steps=n_steps,
            n_features=n_features,
            n_layers=n_layers,
            d_model=d_model,
            hidden_size=hidden_size,
            dropout=dropout,
            ORT_weight=ORT_weight,
            MIT_weight=MIT_weight,
        )
        # set up self.optimizer, etc.

    def fit(self, train_set, val_set=None, file_type="hdf5"):
        # just like the Transformer’s fit(...) function in model_imputation.py
        # Step 1: wrap data in a DataLoader
        # Step 2: training loop with self.model
        pass

    def predict(self, test_set, file_type="hdf5"):
        # again, just like Transformer’s predict(...) in model_imputation.py
        # Step 1: wrap test data in DataLoader
        # Step 2: self.model.eval(); do forward; collect imputation
        pass
```

2) Now you have a new imputer class called TTT_RNN_Imputer that uses the same approach as the PyPOTS “Transformer” for partially observed time series but with a TTT-based RNN backbone. This means:  
   – You can pass your dataset (with X, missing_mask, X_ori, etc.) to it.  
   – You can train it with ORT+MIT.  
   – You can generate imputations consistently with the pipeline PyPOTS uses.  

--------------------------------------------------------------------------------
5. EXTENDING IT FURTHER: TEST-TIME TRAINING
--------------------------------------------------------------------------------

• The notion of “test-time training” (TTT) often means: at inference time, you might finetune or adapt the model on each test sample (or small batch) by using some self-supervised objective or partial known data to better adapt quickly.  
• By embedding this approach into this PyPOTS-like pipeline, you’d do something along the lines of:  
  – For each new sample x_test (with partial observations), you run a short number of gradient steps to refine the model’s parameters (or a subset of them) with the reconstruction objective on whichever parts are “known,” before generating an imputation.  

This can be integrated within the “predict(...)” method. For example, you could do something like:

1) Switch the model from eval mode → train mode to allow gradients.  
2) For each batch in the test set:  
   – For a fixed small number of steps, do forward + backward with your TTT objective (like MSE on the known part) on that batch alone.  
   – Then produce the final imputed_data.  

This ensures your model adapts to each test sample’s distribution. Of course, TTT can be tricky and you have to decide exactly which parameters to adapt, how many steps to do, etc. But the pipeline remains the same: the data shape, the embedding, the final reconstruction, and the missing/observed masks.

--------------------------------------------------------------------------------
SUMMARY
--------------------------------------------------------------------------------

• The key to “refactoring your test-time training model to align with the PyPOTS Transformer approach” is simply to replicate the “embedding + backbone + linear projection + reconstruction + ORT+MIT loss” pipeline.  
• The code snippet provided gives a direct example of building an RNN-based version of the “_Transformer” module in PyPOTS.  
• Then you can place it inside a “model_xxx.py” class that inherits from the same base class (BaseNNImputer) used by PyPOTS Transformers, making the usage/fit/predict flow consistent with other PyPOTS imputation methods.  
• If you want strict test-time training, integrate a small training loop in the predict(...) method to refine the model’s parameters using partial supervision.  

By following these steps, you’ll have a TTT-based RNN imputation model that fits seamlessly into the same pipeline as the “Transformer” in PyPOTS. And importantly, you won’t be skipping any of the details crucial for handling partially-observed time series (embedding, masking, ORT+MIT losses, patching outputs to keep original observed data).  

Hopefully this detailed breakdown clarifies exactly how to implement it in Python and PyTorch, step by step!
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk5NTM1ODI3MF19
-->