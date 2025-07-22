# shell-week-1
# Green house gas emission prediction ml
from google.colab import files
uploaded = files.upload()

import io
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Set seaborn style for better visuals
sns.set(style='whitegrid')

# Automatically get the uploaded filename
filename = list(uploaded.keys())[0]

# Read the uploaded CSV
df = pd.read_csv(io.BytesIO(uploaded[filename]))

# Display the first few rows
print("🔹 First 5 rows of the dataset:")
display(df.head())

# Dataset info
print("\n🔹 Dataset Info:")
df.info()

# Statistical summary
print("\n🔹 Statistical Summary:")
display(df.describe())

# Column names
print("\n🔹 Column Names:")
print(df.columns.tolist())

# Missing values
print("\n🔹 Missing Values in Each Column:")
print(df.isnull().sum())

# Drop missing values
df = df.dropna()

# Drop duplicates
print("\n🔹 Number of Duplicate Rows:")
print(df.duplicated().sum())
df = df.drop_duplicates()

print("\n✅ Data cleaning completed: missing values dropped, duplicates removed.")

# Standardize column names
#print("\n🔎 All Column Names:")
#for col in df.columns:
 #   print(f"'{col}'")

df.columns = df.columns.str.strip()
df.columns = df.columns.str.replace(' ', '_')
df.columns = df.columns.str.lower()

# ✅ 1. Top 10 Industries by Emission Factor (Without Margins)
if 'supply_chain_emission_factors_without_margins' in df.columns and 'industry_name' in df.columns:
    df_sorted_industry = df.sort_values(by='supply_chain_emission_factors_without_margins', ascending=False).head(10)

    plt.figure(figsize=(12,6))
    sns.barplot(x='supply_chain_emission_factors_without_margins',
                y='industry_name',
                data=df_sorted_industry,
                palette='viridis')
    plt.title("Top 10 Industries by Emission Factor (Without Margins)")
    plt.xlabel("Emission Factor (kg CO2e/unit)")
    plt.ylabel("Industry Name")
    plt.tight_layout()
    plt.show()
else:
    print("⚠️ Columns for industry emission plot not found.")

# ✅ 2. Top 10 Substances by Average Emission Factor
if 'supply_chain_emission_factors_without_margins' in df.columns:
    df['supply_chain_emission_factors_without_margins'] = pd.to_numeric(
        df['supply_chain_emission_factors_without_margins'], errors='coerce'
    )
    df = df.dropna(subset=['supply_chain_emission_factors_without_margins'])

if 'substance' in df.columns and df['substance'].notna().sum() > 0:
    df_avg_substance = df.groupby('substance')['supply_chain_emission_factors_without_margins'] \
                         .mean().sort_values(ascending=False).head(10)
    if not df_avg_substance.empty:
        plt.figure(figsize=(10,6))
        df_avg_substance.plot(kind='barh', color='teal')
        plt.title("Top 10 Substances by Average Emission Factor (Without Margins)")
        plt.xlabel("Average Emission Factor (kg CO2e/unit)")
        plt.ylabel("Substance")
        plt.tight_layout()
        plt.show()
    else:
        print("⚠️ No data available to plot average emission factors by substance.")
else:
    print("❌ 'substance' column missing or contains no valid data.")

# ✅ 3. Heatmap of Missing Values (only if any remain)
if df.isnull().values.any():
    plt.figure(figsize=(10, 4))
    sns.heatmap(df.isnull(), cbar=False, cmap='YlGnBu')
    plt.title("Heatmap of Missing Data After Cleaning")
    plt.tight_layout()
    plt.show()
else:
    print("✅ No missing values to visualize in heatmap.")e gas emission prediction ml
