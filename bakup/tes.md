# workbook.py
import pandas as pd
import numpy as np
import os

def save_to_excel(df, file_path, sheet_name='Sheet1'):
    """Saves the DataFrame to an Excel file."""
    df.to_excel(file_path, sheet_name=sheet_name, index=False)

def filter_prusaver(dsf_endt_file):
    """Filter for 'PRUSaver' in 'PRODUCT_NAME_LDESC'."""
    df = pd.read_excel(dsf_endt_file)
    return df[df['PRODUCT_NAME_LDESC'].str[:8].str.upper() == 'PRUSAVER']

def group_prusaver(prusaver_df):
    """Group by POLNO and sum APE, rename APE to 'Endt PSA APE'."""
    return prusaver_df.groupby('POLNO')['APE'].sum().reset_index(name='Endt PSA APE')

def select_ifbeg_columns(filtered_ifbeg_file):
    """Select columns 'MPF Name', 'POL_NUMBER', 'PSA_PREM'."""
    df = pd.read_excel(filtered_ifbeg_file)
    df['POL_NUMBER'] = df['POL_NUMBER'].astype(str)
    return df[['MPF Name', 'POL_NUMBER', 'PSA_PREM']]

def join_datasets(ifbeg_df, prusaver_grouped_df):
    """Perform left and inner joins."""
    prusaver_grouped_df['POLNO'] = prusaver_grouped_df['POLNO'].astype(str)
    
    left_join_df = pd.merge(
        ifbeg_df,
        prusaver_grouped_df,
        left_on='POL_NUMBER',
        right_on='POLNO',
        how='left'
    )
    left_join_df['PSA Endt Ind'] = 0
    left_join_df = left_join_df.rename(columns={'PSA_PREM': 'Run C PSA Prem'})
    left_only_df = left_join_df[left_join_df['POLNO'].isna()].drop('POLNO', axis=1)
    
    inner_join_df = pd.merge(
        ifbeg_df,
        prusaver_grouped_df,
        left_on='POL_NUMBER',
        right_on='POLNO',
        how='inner'
    )
    inner_join_df['Run C PSA Prem'] = inner_join_df['PSA_PREM'] + inner_join_df['Endt PSA APE']
    inner_join_df['PSA Endt Ind'] = 1
    inner_join_df = inner_join_df.drop(['PSA_PREM', 'Endt PSA APE', 'POLNO'], axis=1)
    
    return pd.concat([left_only_df, inner_join_df], ignore_index=True)

def process_medical_benefits(filtered_ifbeg_file):
    """Process medical benefits data."""
    med_columns = ['MPF Name', 'POL_NUMBER', 'PVM_BEN', 'PEM_BEN', 'PFM_HIB', 
                   'PHL_BEN', 'MM_BEN', 'MM2_BEN', 'MM3_BEN', 'MM4_BEN', 
                   'MM5_BEN', 'PVMB_BEN', 'PEMB_BEN']
    df = pd.read_excel(filtered_ifbeg_file)
    benefit_columns = med_columns[2:]
    for col in benefit_columns:
        df[col] = pd.to_numeric(df[col], errors='coerce').fillna(0)

    melted_df = pd.melt(
        df, 
        id_vars=['MPF Name', 'POL_NUMBER'], 
        value_vars=benefit_columns, 
        var_name='Med Rider', 
        value_name='Benefit'
    )
    melted_df = melted_df[melted_df['Benefit'] != 0]
    med_card_counts = melted_df.groupby(['MPF Name', 'POL_NUMBER'])['Med Rider'].nunique().reset_index()
    med_card_counts = med_card_counts.rename(columns={'Med Rider': 'No of Med Card'})
    
    return melted_df.merge(med_card_counts, on=['MPF Name', 'POL_NUMBER'])

def create_med_upgrade(filtered_ifbeg_file, filtered_ifend_file):
    """Create Med Upgrade indicators."""
    med_columns = ['MPF Name', 'POL_NUMBER', 'PVM_BEN', 'PEM_BEN', 'PFM_HIB', 
                   'PHL_BEN', 'MM_BEN', 'MM2_BEN', 'MM3_BEN', 'MM4_BEN', 
                   'MM5_BEN', 'PVMB_BEN', 'PEMB_BEN']

    df_ifbeg = pd.read_excel(filtered_ifbeg_file)
    df_ifend = pd.read_excel(filtered_ifend_file)
    for col in med_columns[2:]:
        df_ifbeg[col] = pd.to_numeric(df_ifbeg[col], errors='coerce').fillna(0)
        df_ifend[col] = pd.to_numeric(df_ifend[col], errors='coerce').fillna(0)
        
    melted_ifbeg = pd.melt(
        df_ifbeg, 
        id_vars=['MPF Name', 'POL_NUMBER'], 
        value_vars=med_columns[2:], 
        var_name='Med Rider', 
        value_name='Benefit'
    )
    melted_ifend = pd.melt(
        df_ifend, 
        id_vars=['MPF Name', 'POL_NUMBER'], 
        value_vars=med_columns[2:], 
        var_name='Med Rider', 
        value_name='Benefit'
    )

    med_upgrade_df = pd.merge(
        melted_ifbeg,
        melted_ifend,
        on=['MPF Name', 'POL_NUMBER', 'Med Rider'],
        how='outer',
        suffixes=('_IFBEG', '_IFEND')
    )

    med_upgrade_df['Med Upgrade Ind'] = 0
    med_upgrade_df['Med Booster Ind'] = 0

    return med_upgrade_df

def calculate_total_ann_prem(df, prem_columns):
    """Calculate Total Annual Premium."""
    for col in prem_columns:
        if col in df.columns:
            df[col] = pd.to_numeric(df[col], errors='coerce').fillna(0)
    df['Total Ann Prem'] = df[[col for col in prem_columns if col in df.columns]].sum(axis=1)
    return df

def main():
    dsf_endt_file = 'Outputs/DSF Endt.xlsx'
    filtered_ifbeg_file = 'Outputs/MPFs/MPF Data/Filtered IFBEG.xlsx'
    filtered_ifend_file = 'Outputs/MPFs/MPF Data/Filtered IFEND.xlsx'
    
    prusaver_df = filter_prusaver(dsf_endt_file)
    prusaver_grouped_df = group_prusaver(prusaver_df)
    ifbeg_selected_df = select_ifbeg_columns(filtered_ifbeg_file)
    run_c_adj_df = join_datasets(ifbeg_selected_df, prusaver_grouped_df)
    
    save_to_excel(run_c_adj_df, 'Outputs/MPF Adj Values.xlsx', 'Run C Adj')

    med_ifbeg_df = process_medical_benefits(filtered_ifbeg_file)
    med_ifend_df = process_medical_benefits(filtered_ifend_file)

    med_upgrade_df = create_med_upgrade(filtered_ifbeg_file, filtered_ifend_file)
    save_to_excel(med_upgrade_df, 'Outputs/Med Upgrade.xlsx', 'Med Card Data')

    prem_columns = [f'ANN_PREM_{i}' for i in range(1, 21)] + \
                   [f'PRUALLOC_PREM_{i}' for i in range(1, 4)] + \
                   ['PSA_PREM']

    filtered_ifbeg_df = pd.read_excel(filtered_ifbeg_file)
    run_a_adj_df = calculate_total_ann_prem(filtered_ifbeg_df, prem_columns)
    save_to_excel(run_a_adj_df, 'Outputs/MPF Adj Values.xlsx', 'Run A Adj')

if __name__ == "__main__":
    main()

Sure! Here's a simplified version of the Python script with logging removed, separate functions for each task, and a clearer, more concise style.

```python
import pandas as pd
import numpy as np
import os
import re
import csv

def save_to_excel(df, file_path, sheet_name='Sheet1'):
    """Saves the DataFrame to an Excel file."""
    df.to_excel(file_path, sheet_name=sheet_name, index=False)

def load_data():
    """Load necessary Excel files into DataFrames."""
    filtered_ifbeg_file = 'Outputs/MPFs/MPF Data/Filtered IFBEG.xlsx'
    non_neg_endt_file = 'Outputs/Non-Neg Endt.xlsx'
    mpf_adj_values_file = 'Outputs/MPF Adj Values.xlsx'
    
    filtered_ifbeg_df = pd.read_excel(filtered_ifbeg_file)
    non_neg_endt_df = pd.read_excel(non_neg_endt_file, sheet_name='Final Endt List')
    run_c_adj_df = pd.read_excel(mpf_adj_values_file, sheet_name='Run C Adj')
    run_a_adj_df = pd.read_excel(mpf_adj_values_file, sheet_name='Run A Adj')
    
    return filtered_ifbeg_df, non_neg_endt_df, run_c_adj_df, run_a_adj_df

def prepare_run_b(filtered_ifbeg_df, non_neg_endt_df):
    """Prepare Final Run B data."""
    filtered_ifbeg_df['POL_NUMBER'] = filtered_ifbeg_df['POL_NUMBER'].astype(str)
    non_neg_endt_df['POL_NUMBER'] = non_neg_endt_df['POL_NUMBER'].astype(str)
    
    final_run_b_df = filtered_ifbeg_df[filtered_ifbeg_df['POL_NUMBER'].isin(non_neg_endt_df['POL_NUMBER'])]
    save_to_excel(final_run_b_df, 'Outputs/MPFs/MPF Data/Final Run B.xlsx', 'MPF Data')
    
    return final_run_b_df

def prepare_run_c(final_run_b_df, run_c_adj_df):
    """Prepare Final Run C data."""
    run_c_adj_df = run_c_adj_df.rename(columns={'Run C PSA Prem': 'PSA_PREM', 'PSA Endt Ind':'PSA_Endt_Ind'})
    final_run_c_df = pd.merge(
        final_run_b_df,
        run_c_adj_df[['POL_NUMBER', 'PSA_PREM', 'PSA_Endt_Ind']],
        on='POL_NUMBER',
        how='left'
    )
    save_to_excel(final_run_c_df, 'Outputs/MPFs/MPF Data/Final Run C.xlsx', 'MPF Data')
    
    return final_run_c_df

def prepare_run_a(final_run_c_df, run_a_adj_df):
    """Prepare Final Run A data."""
    run_a_adj_df = run_a_adj_df.rename(columns={'Med Upgrade Ind': 'Med_Upgrade_Ind', 'Med Booster Ind':'Med_Booster_Ind'})
    
    left_cols = [...]  # Define left columns here
    right_cols = [...]  # Define right columns here
    
    final_run_a_df = pd.merge(final_run_c_df[left_cols], run_a_adj_df[right_cols], on='POL_NUMBER', how='inner')
    final_run_a_df = final_run_a_df.rename(columns=lambda x: x.replace('_x', ''))
    
    save_to_excel(final_run_a_df, 'Outputs/MPFs/MPF Data/Final Run A.xlsx', 'MPF Data')
    
    return final_run_a_df

def get_variable_type(dtype):
    """Return the variable type based on dtype."""
    if pd.api.types.is_integer_dtype(dtype):
        return 'I'
    elif pd.api.types.is_float_dtype(dtype):
        return 'N'
    else:
        return 'T255'

def store_mpfs_by_mpf_name(df, run_folder):
    """Stores dataframes split by 'MPF Name' into .PRO files."""
    for mpf_name, group_df in df.groupby('MPF Name'):
        safe_mpf_name = re.sub(r'[\\/*?:"<>|]', '', mpf_name)
        file_name = os.path.join(run_folder, f"{safe_mpf_name}.PRO")
        
        group_df = group_df.copy()
        if 'MPF Name' in group_df.columns:
            group_df.drop(columns=['MPF Name'], inplace=True)
        
        group_df['PROD_CD'] = mpf_name
        library = f'{mpf_name} Library'
        
        if mpf_name.startswith('U'):
            library = 'U Library'
        elif mpf_name.startswith('C'):
            library = 'C Library'
        
        numlines = len(group_df)
        group_df.columns = group_df.columns.astype(str)
        
        if group_df.columns.duplicated().any():
            group_df = group_df.loc[:, ~group_df.columns.duplicated()]
        
        variable_types = ['VARIABLE_TYPES'] + [get_variable_type(group_df[col].dtype) for col in group_df.columns]
        column_names = ['!'] + list(group_df.columns)
        
        with open(file_name, 'w', newline='', encoding='utf-8') as file:
            writer = csv.writer(file, delimiter=',', quoting=csv.QUOTE_MINIMAL, lineterminator='\n')
            writer.writerow(['IF MPF'])
            writer.writerow(['Output_Format', library] + [''] * (len(column_names) - 2))
            writer.writerow(['NUMLINES', f"{numlines}"])
            writer.writerow(variable_types + [''] * (len(column_names) - len(variable_types)))
            writer.writerow(column_names)
            
            for _, row in group_df.iterrows():
                data_row = ['*'] + [str(val).replace(',', ';').replace('\n', ' ') if not pd.isnull(val) else '' for val in row]
                writer.writerow(data_row)

def run_process():
    filtered_ifbeg_df, non_neg_endt_df, run_c_adj_df, run_a_adj_df = load_data()
    final_run_b_df = prepare_run_b(filtered_ifbeg_df, non_neg_endt_df)
    final_run_c_df = prepare_run_c(final_run_b_df, run_c_adj_df)
    final_run_a_df = prepare_run_a(final_run_c_df, run_a_adj_df)
    
    run_folders = {
        'Run A': 'Outputs/MPFs/Final MPF/Run A',
        'Run B': 'Outputs/MPFs/Final MPF/Run B',
        'Run C': 'Outputs/MPFs/Final MPF/Run C'
    }
    
    for run_name, folder_path in run_folders.items():
        os.makedirs(folder_path, exist_ok=True)
    
    store_mpfs_by_mpf_name(final_run_b_df, run_folders['Run B'])
    store_mpfs_by_mpf_name(final_run_c_df, run_folders['Run C'])
    store_mpfs_by_mpf_name(final_run_a_df, run_folders['Run A'])

if __name__ == "__main__":
    run_process()
```

### Key Changes:
1. **Modular Functions**: Each operation is encapsulated in its own function for better readability and maintenance.
2. **Removed Logging**: All logging statements have been removed to streamline the code.
3. **Concise Logic**: Simplified data processing with clear steps and concise variable names.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjAwOTM1MTYyMl19
-->