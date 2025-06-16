# RedBus-Demand-Forecasting

![Project Pipeline](https://github.com/dsmohiit/TimeSeries-Bus-Demand-Model/blob/main/redbus_logo.jpg)


## TimeSeries-Bus-Demand-Model

## **Author:** Mohit Soni

## **Final Model:** redbus_decode_RF_leakproof_v1.ipynb

## Final Validation Score

- Metric: Root Mean Squared Error (RMSE)

- Score: 605.59 seats

**Note:** This score was achieved using a strict, time-based validation split (80% past data for training, 20% future data for validation) to provide an honest and realistic measure of the model's performance on unseen future data.

## 1. Project Overview

The goal of this project was to predict the final total number of seats booked for various bus routes, with the prediction being made exactly 15 days before the journey date. The challenge lies in accurately forecasting demand using historical booking and search data while accounting for complex factors like holidays, seasonality, and day-of-week effects.

## 2. Solution Approach Summary

This solution employs a **Random Forest Regressor** model built upon a robust feature engineering and validation framework. The development process was iterative and focused on a core principle: preventing data leakage to build an honest and reliable forecasting model.

The approach evolved from initial simple models to a final sophisticated strategy:

- **Initial Exploration:** Began with basic feature engineering and standard validation techniques.

- **Identifying a Critical Flaw:** Discovered that a simple random train-test split was giving misleadingly optimistic scores due to "data leakage" (using future information to predict the past).

- **The Methodological Shift:** Implemented a strict time-based validation split. This became the cornerstone of the project, ensuring the model was trained only on past data to predict the future, mimicking the real-world challenge.

- **Leak-Proof Feature Engineering:** The most critical feature, a historical average of demand per route, was re-engineered using an "expanding window" approach. This ensures that the historical average for any given journey is calculated using only data available before that journey took place, making the feature 100% time-aware and "leak-proof".

- **Final Model:** The final solution combines this robust feature set with a tuned Random Forest model to generate the predictions.

## 3. Key Methodologies & Concepts
**A. Data Preprocessing**

- **15-Day Snapshot:** The transactions.csv file was filtered to extract the latest cumulative seat and search counts for each journey exactly at (or just before) the 15-day-to-departure mark.

- **Data Merging:** This snapshot of information was then merged with the train.csv and test_8gqdJqH.csv files to form the base for feature engineering.

- **Date Handling:** All date columns were converted to a consistent datetime format (YYYY-MM-DD), avoiding errors related to regional settings.

**B. Feature Engineering**

The model's performance relies on a set of carefully crafted features:

- **Date-Based Features:** dayofweek, month, year, weekofyear, and a binary is_weekend flag to capture calendar effects.

- **External Holiday Data:** A custom list of major holidays was included to create is_holiday and is_holiday_near features, capturing demand spikes around special events.

- **Route-Based Features:** A unique route identifier was created by combining srcid and destid.

is_inter_region and is_inter_tier flags were created to differentiate between long-distance/metro-to-town journeys and local ones.

- **Interaction Feature:** A search_to_book_ratio was calculated to measure the conversion rate of user interest into actual bookings.

The "Secret Sauce" - Leak-Proof Expanding Average:

The most impactful feature, expanding_route_avg, was created. For each journey in the time-sorted dataset, this feature calculates the average final_seatcount of all previous journeys on that same route.

This was implemented using groupby().cumsum() and groupby().cumcount().shift(1) to ensure no future data was included in the calculation for any given row.

**C. Model Selection**

A RandomForestRegressor from Scikit-learn was chosen. It is a powerful ensemble model that excels at capturing complex, non-linear interactions in tabular data.

Key parameters (n_estimators=300, max_depth=15, min_samples_leaf=5) were selected based on iterative testing to balance model complexity and prevent overfitting.

**D. Validation Strategy**

A Time-Based Split was used. The complete training dataset was sorted by date, with the oldest 80% used for the training set (X_train, y_train) and the most recent 20% used for the validation set (X_val, y_val). This is the most robust method for time-series forecasting problems as it realistically simulates predicting the future.

## 4. File Structure
```
├── redbus_decode_RF_leakproof_v1.ipynb   
├── train.csv                             
├── test_8gqdJqH.csv                      
├── transactions.csv                      
├── submission.csv                        
└── README.md
```                  

## 5. How to Run the Code

**Prerequisites:** Make sure you have the following Python libraries installed:

```bash
pip install pandas numpy scikit-learn
```

**A. Data Placement:** Place train.csv, test_8gqdJqH.csv, and transactions.csv in the same directory as the redbus_decode_RF_leakproof_v1.ipynb notebook.

**B. Execution:** Open the notebook in a Jupyter environment (like Jupyter Lab or Google Colab) and run the cells sequentially from top to bottom.

**C. Output:** The script will train the model, evaluate it on the validation set, and finally generate a submission.csv file in the correct format.
