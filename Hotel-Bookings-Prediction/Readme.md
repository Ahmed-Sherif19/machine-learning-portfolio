# Hotel Booking Data Preprocessing

Data preprocessing pipeline for hotel booking cancellation prediction.

## Requirements
```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

## Usage
```python
python preprocessing_pipeline.py
```

## Pipeline Phases

### Phase 1: EDA & Data Quality
```python
# Summary statistics and info
df.describe()
df.info()

# Missing values analysis with heatmap
sns.heatmap(df.isnull())

# Outlier detection using IQR method and boxplots
```

### Phase 2: Data Cleaning
```python
# Remove data leakage (CRITICAL FIRST STEP)
df.drop(['reservation_status', 'reservation_status_date'], axis=1)

# Handle missing values
df['company'].fillna('None')      # 94.3% missing
df['agent'].fillna(0)             # 13.7% missing  
df['country'].fillna(mode)        # 0.4% missing
df['children'].fillna(median)     # 0.003% missing

# Remove duplicates (31,994 removed)
df.drop_duplicates()

# Cap outliers
df.loc[df['adr'] > 1000, 'adr'] = 1000
df.loc[df['lead_time'] > 365, 'lead_time'] = 365
```

### Phase 3: Feature Engineering
```python
# Create new features
df['total_guests'] = df['adults'] + df['children'] + df['babies']
df['total_nights'] = df['stays_in_weekend_nights'] + df['stays_in_week_nights']
df['is_family'] = ((df['children'] > 0) | (df['babies'] > 0)).astype(int)

# Encode categorical variables
# One-hot encoding for low cardinality (≤10 unique values)
pd.get_dummies(df[low_cardinality_cols])

# Country: Group infrequent into 'Other' (177 → 16 categories)
# Train-test split: 80/20, stratified, random_state=42
```

## Results
| Metric | Value |
|--------|-------|
| Duplicates Removed | 31,994 (26.8%) |
| Missing Values | 0 (100% resolved) |
| Outliers Handled | ADR: 2.9%, Lead Time: 2.7% |
| Family Bookings | 10.4% identified |
| Cancellation Rate | 27.3% (train/test consistent) |
| Final Features | 99 (from 30 original) |

## Data Quality
- ✅ Zero missing values
- ✅ No data leakage
- ✅ Outliers capped
- ✅ Balanced train/test split
- ✅ All categorical variables encoded
