**Introduction**

To implement the LIP Endorsement Workflow in Python, we'll follow a systematic approach to translate each step of the workflow into Python code. We'll use the provided "common_usage_example.py" as a template and leverage the "common_data_reconciliation.py" and "common_component.py" scripts for common functions and validation.

Below is a detailed strategy to implement the workflow, including considerations for reading `.pro` files, data manipulation, and ensuring the logic aligns with the workflow steps. We'll also address the potential need for handling `.pro` files in Python.

---

**Workflow Implementation Strategy**

**1. Understanding the Data and File Formats**

- **Input Files:**
  - **MPF Files (`.pro` files):** These are the core data files used in the workflow. The data format file provided suggests that the `.pro` files are text-based and can be read line by line.
  - **Daily Sales File (`Daily Sales File query 0724.xlsx`):** This file contains sales data needing transformation and filtering.
  - **Not Used MPF List (`MPF Codes.xlsx`):** A list of MPF codes to be excluded.

- **Output Files:**
  - Various Excel files such as `Filtered IFBEG.xlsx`, `Filtered IFEND.xlsx`, `Final Run A.xlsx`, etc.

**Consideration for `.pro` Files:**

- Since `.pro` files are text-based and seem similar to CSV files, we can read them in Python using standard file reading methods.
- If the files follow a consistent delimiter (e.g., comma, tab), we can use pandas' `read_csv` with appropriate parameters.

**2. Setting Up the Python Environment**

- **Import Necessary Libraries:**
  - `pandas` for data manipulation.
  - `numpy` for numerical operations.
  - `os` for file operations.
  - `re` for regular expressions if needed.
  - Additional libraries such as `openpyxl` for Excel file handling.

- **Organize the Code Structure:**
  - Use the template provided in `common_usage_example.py`.
  - Place common functions in `common_component.py`.
  - Use `common_data_reconciliation.py` for data validation and reconciliation.

**3. Reading and Processing Input Files**

**3.1 Reading `.pro` Files**

- **Function to Read `.pro` Files:**

  ```python
  import pandas as pd

  def read_pro_file(file_path):
      with open(file_path, 'r') as file:
          lines = file.readlines()
      # Skip header lines or identify the starting point of data
      # Assuming data starts after a specific marker or at a certain line number
      data_start_line = identify_data_start(lines)
      data_lines = lines[data_start_line:]
      # Process lines into a list of dictionaries
      data = [process_line(line) for line in data_lines]
      # Convert to DataFrame
      df = pd.DataFrame(data)
      return df
  ```

- **Handling the Data Format:**

  - Use the data format information provided to define the columns and data types.
  - Create a mapping of column names and data types to ensure correct parsing.

**3.2 Reading Excel Files**

- Use `pd.read_excel()` to read Excel files such as the Daily Sales File and Not Used MPF List.

**4. Implementing the Workflow Steps**

We'll follow the "Data transformation steps" outlined in the CSV file. Each container represents a set of related operations.

---

**Container 1: Filter MPF for Endorsement Policies**

**Step 10.1: Import Daily Sales File and MPF Codes**

```python
# Read Daily Sales File
daily_sales_df = pd.read_excel('Inputs/Daily Sales File query 0724.xlsx')

# Read MPF Codes (Not Used MPF List)
mpf_codes_df = pd.read_excel('Inputs/MPF Codes.xlsx')
```

**Step 10.2: Select and Clean Columns in Daily Sales File**

```python
# Select required columns
daily_sales_df = daily_sales_df[['PERIOD_DATE', 'POLNO', 'APE']]

# Change data types
daily_sales_df['PERIOD_DATE'] = pd.to_datetime(daily_sales_df['PERIOD_DATE'])
daily_sales_df['POLNO'] = daily_sales_df['POLNO'].astype(str)
daily_sales_df['APE'] = daily_sales_df['APE'].astype(float)
```

**Step 10.3: Filter Daily Sales File Data**

- Apply necessary filters based on the criteria from the workflow.

```python
# Assuming specific filter criteria
daily_sales_filtered = daily_sales_df[
    (daily_sales_df['SomeColumn'] == 'SomeValue') & 
    (daily_sales_df['AnotherColumn'] > threshold_value)
]
```

**Step 10.4: Group and Summarize APE**

```python
daily_sales_grouped = daily_sales_filtered.groupby(['POLNO', 'PRODUCT_NAME_LDESC'])['APE'].sum().reset_index()
```

**Step 10.5: Output DSF Endt.xlsx**

```python
daily_sales_grouped.to_excel('Outputs/DSF Endt.xlsx', index=False)
```

**Steps 10.6 to 10.15: Import and Process MPF Files**

- **Import IFBEG and IFEND MPFs:**

  ```python
  ifbeg_df = read_pro_file('Inputs/UXXXXX_IFBEG.PRO')
  ifend_df = read_pro_file('Inputs/UXXXXX_IFEND.PRO')
  ```

- **Process MPF Name and Filter SPCode:**

  ```python
  def process_mpf_df(mpf_df, mpf_codes_df):
      # Extract first 6 characters of 'File Name' column
      mpf_df['MPF Name'] = mpf_df['File Name'].str[:6]
      # Exclude MPFs listed in mpf_codes_df
      mpf_df = mpf_df[~mpf_df['MPF Name'].isin(mpf_codes_df['MPF Name'])]
      # Filter SPCode not equal to 51
      mpf_df = mpf_df[mpf_df['SPCODE'] != 51]
      return mpf_df

  ifbeg_processed = process_mpf_df(ifbeg_df, mpf_codes_df)
  ifend_processed = process_mpf_df(ifend_df, mpf_codes_df)
  ```

- **Map with Daily Sales POLNO:**

  ```python
  # Ensure POLNO is of the same type
  daily_sales_grouped['POLNO'] = daily_sales_grouped['POLNO'].astype(str)
  ifbeg_processed['POLNO'] = ifbeg_processed['POL_NUMBER'].astype(str)

  # Map with Daily Sales POLNO
  ifbeg_filtered = ifbeg_processed[ifbeg_processed['POLNO'].isin(daily_sales_grouped['POLNO'])]
  ```

- **Output Filtered IFBEG and IFEND:**

  ```python
  ifbeg_filtered.to_excel('Outputs/Filtered IFBEG.xlsx', index=False)
  ifend_filtered.to_excel('Outputs/Filtered IFEND.xlsx', index=False)
  ```

---

**Container 2: MPF Adjustment Compilation**

**Step 10.16 to 10.22: Run C Adjustment**

- **Filter for "PRUSaver" in Daily Sales File:**

  ```python
  prusaver_sales = daily_sales_grouped[daily_sales_grouped['PRODUCT_NAME_LDESC'] == 'PRUSaver']
  ```

- **Group and Sum APE:**

  ```python
  prusaver_ape = prusaver_sales.groupby('POLNO')['APE'].sum().reset_index()
  prusaver_ape.rename(columns={'APE': 'Endt PSA APE'}, inplace=True)
  ```

- **Select Columns from IFBEG and Map POLNO:**

  ```python
  columns_to_select = ['MPF Name', 'POL_NUMBER', 'PSA_PREM']
  ifbeg_selected = ifbeg_filtered[columns_to_select]
  ifbeg_selected['POLNO'] = ifbeg_selected['POL_NUMBER'].astype(str)

  run_c_adj = ifbeg_selected.merge(prusaver_ape, on='POLNO', how='left')
  ```

- **Add New Columns:**

  ```python
  run_c_adj['PSA Endt Ind'] = run_c_adj['Endt PSA APE'].apply(lambda x: 1 if pd.notnull(x) else 0)
  run_c_adj['Run C PSA Prem'] = run_c_adj.apply(
      lambda row: row['PSA_PREM'] + row['Endt PSA APE'] if row['PSA Endt Ind'] == 1 else row['PSA_PREM'], axis=1
  )
  ```

- **Output Run C Adjustment:**

  ```python
  run_c_adj.to_excel('Outputs/MPF Adj Values.xlsx', sheet_name='Run C Adj', index=False)
  ```

**Step 10.23 to 10.34: Med Upgrade List**

- **Process IFBEG and IFEND for Medical Upgrades:**

  ```python
  # Assuming 'Name' and 'Value' columns exist
  def process_med_upgrade(mpf_df):
      df = mpf_df[['MPF Name', 'POL_NUMBER', 'Name', 'Value']].copy()
      # Filter out zero values
      df = df[df['Value'] != 0]
      # Group and count
      df_grouped = df.groupby(['MPF Name', 'POL_NUMBER']).agg({
          'Name': 'count',
          'Value': 'sum'
      }).reset_index()
      df_grouped.rename(columns={'Name': 'No of Med Card', 'Value': 'Benefit'}, inplace=True)
      return df_grouped

  ifbeg_med_upgrade = process_med_upgrade(ifbeg_filtered)
  ifend_med_upgrade = process_med_upgrade(ifend_filtered)
  ```

- **Calculate Med Upgrade Indicators:**

  ```python
  med_upgrade = ifbeg_med_upgrade.merge(ifend_med_upgrade, on='POL_NUMBER', suffixes=('_IFBEG', '_IFEND'))
  med_upgrade['Med Upgrade Ind'] = med_upgrade.apply(
      lambda row: 1 if row['No of Med Card_IFEND'] > row['No of Med Card_IFBEG'] else 0, axis=1
  )
  med_upgrade['Med Booster Ind'] = med_upgrade.apply(
      lambda row: 1 if row['Benefit_IFEND'] > row['Benefit_IFBEG'] else 0, axis=1
  )
  ```

- **Filter for Med Upgrade Ind = 1 and Output:**

  ```python
  med_upgrade_list = med_upgrade[med_upgrade['Med Upgrade Ind'] == 1]
  med_upgrade_list.to_excel('Outputs/Med Upgrade.xlsx', sheet_name='Upgrade List', index=False)
  ```

**Step 10.35 to 10.44: Run A Adjustment**

- **Create Run A Adjustment by Merging Data:**

  ```python
  run_a_adj = ifbeg_filtered.copy()
  run_a_adj = run_a_adj.merge(med_upgrade[['POL_NUMBER', 'Med Upgrade Ind', 'Med Booster Ind']], on='POL_NUMBER', how='left')
  run_a_adj['Med Upgrade Ind'].fillna(0, inplace=True)
  run_a_adj['Med Booster Ind'].fillna(0, inplace=True)
  # Map with policies which dropped MA and Run C adjustment as per steps
  ```

- **Output Run A Adjustment:**

  ```python
  run_a_adj.to_excel('Outputs/MPF Adj Values.xlsx', sheet_name='Run A Adj', index=False)
  ```

**Step 10.45 to 10.49: Remove Negative Endorsements**

- **Calculate Total Annual Premium and Annual Premium Increase:**

  ```python
  ifbeg_filtered['Total Ann Prem'] = ifbeg_filtered['ANNUAL_PREM']  # Assuming 'ANNUAL_PREM' column exists
  run_a_adj['Total Ann Prem'] = run_a_adj['ANNUAL_PREM']
  combined_prem = ifbeg_filtered.merge(run_a_adj[['POL_NUMBER', 'Total Ann Prem']], on='POL_NUMBER', suffixes=('_IFBEG', '_RunAAdj'))
  combined_prem['Ann Prem Inc'] = combined_prem['Total Ann Prem_RunAAdj'] - combined_prem['Total Ann Prem_IFBEG']
  ```

- **Filter for Ann Prem Inc >= 0:**

  ```python
  non_neg_endt = combined_prem[combined_prem['Ann Prem Inc'] >= 0]
  non_neg_endt.to_excel('Outputs/Non-Neg Endt.xlsx', sheet_name='Non-Neg Endt List', index=False)
  ```

---

**Container 3: MPF Generation**

**Step 10.65 to 10.76: Generate Final Runs**

- **Prepare Data for Final Runs:**

  ```python
  # Load necessary data
  non_neg_endt = pd.read_excel('Outputs/Non-Neg Endt.xlsx', sheet_name='Final Endt List')
  run_c_adj = pd.read_excel('Outputs/MPF Adj Values.xlsx', sheet_name='Run C Adj')
  run_a_adj = pd.read_excel('Outputs/MPF Adj Values.xlsx', sheet_name='Run A Adj')

  # Final Run B
  final_run_b = ifbeg_filtered.merge(non_neg_endt, on='POL_NUMBER', how='inner')
  final_run_b.to_excel('Outputs/Final Run B.xlsx', index=False)

  # Final Run C
  final_run_c = final_run_b.merge(run_c_adj[['POL_NUMBER', 'Run C PSA Prem']], on='POL_NUMBER', how='left')
  final_run_c.to_excel('Outputs/Final Run C.xlsx', index=False)

  # Final Run A
  final_run_a = final_run_c.merge(run_a_adj[['POL_NUMBER', 'Med Upgrade Ind', 'Med Booster Ind']], on='POL_NUMBER', how='left')
  final_run_a.to_excel('Outputs/Final Run A.xlsx', index=False)
  ```

- **Group by MPF Name and Output MPFs:**

  ```python
  def output_mpfs(final_run_df, run_folder):
      grouped = final_run_df.groupby('MPF Name')
      for mpf_name, group in grouped:
          # Define the output file path
          output_path = f'Outputs/{run_folder}/{mpf_name}.PRO'
          # Write group to .pro file (adjust formatting as necessary)
          group.to_csv(output_path, index=False)
  
  # Create directories if they don't exist
  import os
  os.makedirs('Outputs/Run A', exist_ok=True)
  os.makedirs('Outputs/Run B', exist_ok=True)
  os.makedirs('Outputs/Run C', exist_ok=True)

  # Output MPFs for each run
  output_mpfs(final_run_b, 'Run B')
  output_mpfs(final_run_c, 'Run C')
  output_mpfs(final_run_a, 'Run A')
  ```

---

**Container 4: MPF Checks**

**Step 10.77 to 10.103: Perform Checks and Generate Reports**

- **Implement File Reading and Data Preparation:**

  ```python
  # Load DSF, Final Runs, and Adjustments
  dsf_endt = pd.read_excel('Outputs/DSF Endt.xlsx')
  final_run_a = pd.read_excel('Outputs/Final Run A.xlsx')
  final_run_b = pd.read_excel('Outputs/Final Run B.xlsx')
  final_run_c = pd.read_excel('Outputs/Final Run C.xlsx')
  ```

- **Assign Field Names and Clean Data:**

  ```python
  # Assign field names if necessary and perform data cleaning on runs
  ```

- **Perform Mapping and Calculate Differences:**

  ```python
  # Map and compare data between runs to identify discrepancies
  # Calculate differences and output checking results
  ```

- **Generate Summary Reports:**

  ```python
  # Generate reports for missing policies, premium movements, endorsements, etc.
  ```

**Validation and Reconciliation:**

- Use the `Data_Reconciliation` class from `common_data_reconciliation.py` to perform data validation at each significant step.

  ```python
  from common_data_reconciliation import Data_Reconciliation

  data_rec = Data_Reconciliation()
  data_rec.add_row_agg(final_run_a)
  data_rec.add_row_agg(final_run_b)
  data_rec.add_row_agg(final_run_c)
  data_rec.output_val_table('Validation_Report.csv')
  ```

---

**5. Additional Considerations**

- **Error Handling:**

  - Implement try-except blocks to catch and handle exceptions, especially when reading files or performing data merges.

- **Logging:**

  - Optional: Use Python's `logging` module to log progress, warnings, and errors during execution.

- **Performance Optimization:**

  - For large datasets, consider optimizing data processing by using pandas' efficient functions and avoiding loops.

- **Code Modularity:**

  - Encapsulate repeated tasks into functions for reusability.
  - Organize the code into modules or classes if appropriate.

**6. Testing and Verification**

- **Unit Tests:**

  - Write unit tests for critical functions to ensure they work as expected.

- **Data Verification:**

  - At each step, print or log summaries of data frames (e.g., shapes, head, tail) to verify correctness.

- **Final Checks:**

  - Compare the outputs with expected results or sample outputs provided.

---

**Conclusion**

By systematically translating each step of the LIP Endorsement Workflow into Python code and leveraging existing templates and functions, we can efficiently implement the workflow. The strategy above ensures that all implementation logic is carefully examined, and attention is paid to every detail, aligning with the workflow described.

Remember to adapt the code snippets to fit the actual data and any specific requirements, and validate the logic thoroughly during implementation.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNjg5MzE0OThdfQ==
-->