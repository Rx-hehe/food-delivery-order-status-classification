
# Order Status Prediction with Different `top_k` Values

## Overview
This project uses the same food delivery dataset to test how changing the `top_k` value in `Item_Name_reduced` affects model performance.

The target variable is:

- `Order_Status`

The comparison was done for:

- `top_k = 3`
- `top_k = 5`
- `top_k = 6`

For each value, the following were compared:

- Accuracy
- Top feature importances

---

## Dataset
The dataset contains 25,000 rows and 23 columns related to food delivery orders, including:

- order details
- item name
- customer / restaurant / driver locations
- traffic level
- payment method
- order status

### Initial checks
- Shape: `(25000, 23)`
- Missing values: `0`
- Duplicate rows: `0`

### Target distribution
- Delivered: `10197`
- Cancelled: `9812`
- In Transit: `4991`

---

## Preprocessing and Feature Engineering

### Dropped columns
The following columns were dropped because they were either not needed or could leak information:

- `Delivery_Time`
- `Delivery_Duration_Minutes`

Later, additional columns were excluded from modeling:

- `Order_ID`
- `User_ID`
- `Restaurant_ID`
- `Driver_ID`
- `Total_Price`
- `haversine_rest_to_cust_km`
- `Order_Time`
- `Item_Name`

### Created features
From `Order_Time`, the following features were created:

- `order_hour`
- `order_dayofweek`
- `is_weekend`
- `is_peak_hour`

A Haversine distance feature was also calculated:

- `haversine_rest_to_cust_km`

A price category feature was created from `Total_Price`:

- `price_tier`

---

## Item Name Reduction
The categorical feature `Item_Name` originally had 9 unique values.

A reduced version called `Item_Name_reduced` was created by keeping only the top `k` most frequent items and replacing the rest with `"Other"`.

The tested values were:

- `top_k = 3`
- `top_k = 5`
- `top_k = 6`

---

## Model
A **Random Forest Classifier** was used inside a pipeline with preprocessing:

- `OneHotEncoder(handle_unknown="ignore")` for categorical columns
- numerical columns passed through directly

### Train-test split
- 80% training
- 20% testing
- `random_state = 42`
- stratified by target

### Random Forest settings
- `n_estimators = 300`
- `random_state = 42`
- `n_jobs = -1`

---

## Results

### Accuracy comparison

| top_k | Accuracy |
|------:|---------:|
| 3     | 0.4016   |
| 5     | 0.3990   |
| 6     | 0.3988   |

### Best result
The best accuracy was achieved when:

- **`top_k = 3`**

However, the differences between the three values were very small.

---

## Top Feature Importances

### top_k = 3
Top important features included:

1. `Delivery_Distance_km`
2. `Driver_Lon`
3. `Driver_Lat`
4. `Customer_Lon`
5. `Customer_Lat`
6. `Restaurant_Lat`
7. `Restaurant_Lon`
8. `order_hour`
9. `order_dayofweek`
10. `Quantity`

### top_k = 5
Top important features included:

1. `Delivery_Distance_km`
2. `Driver_Lat`
3. `Customer_Lon`
4. `Driver_Lon`
5. `Restaurant_Lon`
6. `Restaurant_Lat`
7. `Customer_Lat`
8. `order_hour`
9. `order_dayofweek`
10. `Quantity`

### top_k = 6
Top important features included:

1. `Delivery_Distance_km`
2. `Driver_Lat`
3. `Driver_Lon`
4. `Customer_Lon`
5. `Restaurant_Lat`
6. `Restaurant_Lon`
7. `Customer_Lat`
8. `order_hour`
9. `order_dayofweek`
10. `Quantity`

---

## Discussion
The results show that changing `top_k` in `Item_Name_reduced` did **not** significantly improve model accuracy.

Main observations:

- `top_k = 3` performed slightly better than `top_k = 5` and `top_k = 6`
- The most important features were mostly the same across all experiments
- Geographic and distance-related features had the strongest influence on prediction
- The reduced item grouping had only a small impact on performance

This suggests that `Item_Name_reduced` was not one of the strongest drivers of prediction compared to delivery distance, location coordinates, and time-based features.

---

## Conclusion
Using different `top_k` values for `Item_Name_reduced` caused only minor changes in model performance.

- Best accuracy: `0.4016` with `top_k = 3`
- Feature importance rankings were very similar across all three tests
- Delivery distance and location features consistently dominated the model

Overall, changing `top_k` had only a limited effect on predicting `Order_Status`.

---

## Files in this repository
- `Lab5.ipynb` — code for preprocessing, modeling, and comparison
- `README.md` — project explanation and results
- dataset file

---

## Author
Fatimah Al-Maqhawi
