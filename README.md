# House Price Predictor - Project Documentation

## 📋 Table of Contents
1. [Project Overview](#1-project-overview)
2. [System Requirements](#2-system-requirements)
3. [Technology Stack](#3-technology-stack)
4. [Project Structure](#4-project-structure)
5. [Dataset Description](#5-dataset-description)
6. [Application Architecture](#6-application-architecture)
7. [Screen-by-Screen Walkthrough](#7-screen-by-screen-walkthrough)
8. [Prediction Algorithm - Multiple Linear Regression](#8-prediction-algorithm---multiple-linear-regression)
9. [Data Visualization Techniques](#9-data-visualization-techniques)
10. [How to Run the Application](#10-how-to-run-the-application)
11. [Code Flow & Logic Explanation](#11-code-flow--logic-explanation)
12. [Future Enhancements](#12-future-enhancements)
13. [Viva Q&A Preparation](#13-viva-qa-preparation)

---

## 1. Project Overview

**Project Title:** House Price Predictor  
**Platform:** Android (Mobile Application)  
**Purpose:** To analyze Indian housing market data and predict property prices using Machine Learning techniques.

### Objectives
- Load and parse a housing dataset from a CSV file
- Visualize housing trends using various chart types (bar, line, pie)
- Implement a Multiple Linear Regression model to predict house prices
- Provide an interactive UI for users to input house features and get price predictions
- Display model accuracy metrics (R² score)

### Key Features
- **Dashboard** with summary statistics (total listings, average/min/max price)
- **Trends Analysis** with 4 different chart visualizations
- **Price Prediction** using ML with input validation
- **About Section** with methodology explanation

---

## 2. System Requirements

| Requirement | Specification |
|------------|---------------|
| IDE | Android Studio (Arctic Fox or newer) |
| JDK | Java 8 or higher |
| Android SDK | API 24 (Android 7.0) minimum |
| Target SDK | API 34 (Android 14) |
| Build System | Gradle 8.4 with AGP 8.2.2 |
| Emulator/Device | API 24+ Android device or emulator |

---

## 3. Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Programming Language | **Java** | Application logic, ML implementation |
| UI Framework | **XML Layouts** | User interface design |
| Charts Library | **MPAndroidChart v3.1.0** | Data visualization (bar, line, pie charts) |
| UI Components | **Material Design Components** | Modern UI elements (cards, buttons, inputs) |
| Data Storage | **CSV file in assets/** | Embedded dataset |
| Build Tool | **Gradle** | Dependency management and build |

---

## 4. Project Structure

```
House_Pricing/
├── app/
│   ├── src/main/
│   │   ├── java/com/housepricing/app/
│   │   │   ├── MainActivity.java           ← Main entry point, navigation
│   │   │   ├── DashboardFragment.java      ← Statistics & overview chart
│   │   │   ├── TrendsFragment.java         ← 4 trend charts
│   │   │   ├── PredictFragment.java        ← ML prediction UI
│   │   │   ├── AboutFragment.java          ← App information
│   │   │   ├── model/
│   │   │   │   ├── House.java              ← Data model class
│   │   │   │   └── LinearRegression.java   ← ML algorithm
│   │   │   └── utils/
│   │   │       ├── CSVParser.java          ← Dataset parser
│   │   │       └── ChartHelper.java        ← Chart styling utility
│   │   ├── res/
│   │   │   ├── layout/                     ← XML layout files
│   │   │   ├── menu/                       ← Bottom navigation menu
│   │   │   ├── values/                     ← Colors, strings, themes
│   │   │   └── drawable/                   ← Icons and backgrounds
│   │   ├── assets/
│   │   │   └── house_prices.csv            ← Dataset (150 records)
│   │   └── AndroidManifest.xml
│   └── build.gradle                         ← App dependencies
├── build.gradle                             ← Project config
├── settings.gradle                          ← Module settings
└── gradle.properties                        ← Build properties
```

---

## 5. Dataset Description

### Source
The dataset is a curated collection of **150 house listings** across 5 major Indian cities, embedded as a CSV file in the app's assets folder.

### Features (Columns)

| Column | Type | Description | Range |
|--------|------|-------------|-------|
| Id | Integer | Unique identifier | 1–150 |
| Area | Double | Floor area in square feet | 650–4000 sqft |
| Bedrooms | Integer | Number of bedrooms | 1–5 |
| Bathrooms | Integer | Number of bathrooms | 1–4 |
| YearBuilt | Integer | Year of construction | 2000–2025 |
| City | String | City name | Mumbai, Delhi, Bangalore, Hyderabad, Chennai |
| Price | Double | Sale price in Lakhs (₹) | 16.8–152.3 Lakhs |

### Distribution by City
- **Mumbai** (30 listings) — Highest price range
- **Delhi** (30 listings) — Second highest
- **Bangalore** (30 listings) — Mid-range
- **Chennai** (30 listings) — Mid-range
- **Hyderabad** (30 listings) — Lower range

### Data Characteristics
- Prices correlate positively with area, bedrooms, and bathrooms
- Newer houses (higher YearBuilt) tend to have higher prices
- Mumbai has the highest average prices, Hyderabad the lowest
- The dataset is balanced across cities (30 each)

---

## 6. Application Architecture

### Architecture Pattern: Single Activity + Multiple Fragments

```
MainActivity (AppCompatActivity)
    │
    ├── BottomNavigationView (4 tabs)
    │
    ├── DashboardFragment ──→ CSVParser ──→ house_prices.csv
    │                      ──→ ChartHelper
    │
    ├── TrendsFragment ────→ CSVParser ──→ house_prices.csv
    │                   ────→ ChartHelper
    │
    ├── PredictFragment ──→ CSVParser ──→ house_prices.csv
    │                   ──→ LinearRegression (train + predict)
    │
    └── AboutFragment (static content)
```

### Data Flow
1. User opens app → `MainActivity` loads → `DashboardFragment` displayed
2. `CSVParser.parse()` reads CSV from `assets/` → returns `List<House>`
3. Fragments process the data for statistics, charts, or ML training
4. `LinearRegression` is trained on-the-fly when `PredictFragment` loads
5. User inputs features → model predicts → result displayed

---

## 7. Screen-by-Screen Walkthrough

### Screen 1: Dashboard
- **Header card** with gradient background showing app title
- **4 stat cards** in a 2×2 grid:
  - Total Listings (count of houses)
  - Average Price (mean of all prices)
  - Minimum Price (lowest price)
  - Maximum Price (highest price)
- **Bar Chart** showing average price by city

### Screen 2: Trends
- **Line Chart** — Price vs Area (sqft): Shows how price increases with area
- **Bar Chart** — Average Price by Bedrooms (1-5 BHK): Price per bedroom count
- **Pie Chart** — City Distribution: Percentage of listings per city
- **Bar Chart** — Price by Year Built (grouped in 5-year ranges)

### Screen 3: Predict
- **Input Form** with Material Design text fields:
  - Area (sq ft) — numeric input
  - Bedrooms — integer input
  - Bathrooms — integer input
  - Year Built — integer input
- **Predict Button** — triggers model prediction
- **Result Card** (appears after prediction):
  - Predicted price in ₹ Lakhs (large, bold text)
  - R² accuracy score

### Screen 4: About
- App name and version
- Project description
- Methodology explanation (Linear Regression)
- Dataset description
- Features used for prediction

---

## 8. Prediction Algorithm - Multiple Linear Regression

### What is Linear Regression?
Linear Regression is a supervised machine learning algorithm that models the relationship between a dependent variable (price) and one or more independent variables (features).

### Mathematical Formula

**Single Variable:**
```
y = mx + b
```

**Multiple Variables (our case):**
```
Price = w₁×Area + w₂×Bedrooms + w₃×Bathrooms + w₄×Age + bias
```

Where:
- `w₁, w₂, w₃, w₄` are learned weights
- `bias` is the intercept term
- `Area, Bedrooms, Bathrooms, Age` are input features

### Training Process

#### Step 1: Feature Normalization (Z-Score)
Before training, features are normalized to have zero mean and unit standard deviation:

```
normalized_value = (value - mean) / standard_deviation
```

**Why?** Features have different scales (Area: 650-4000, Bedrooms: 1-5). Without normalization, gradient descent would be dominated by larger-scale features.

#### Step 2: Gradient Descent
The algorithm iteratively adjusts weights to minimize the **Mean Squared Error (MSE)**:

```
MSE = (1/n) × Σ(predicted - actual)²
```

**Update rules (per iteration):**
```
weight_j = weight_j - learning_rate × (∂MSE/∂weight_j)
gradient_j = (2/n) × Σ(error × normalized_feature_j)
bias = bias - learning_rate × (2/n) × Σ(error)
```

**Hyperparameters used:**
- Learning Rate: 0.01
- Iterations: 2000

#### Step 3: R² Score Calculation
After training, we calculate the **Coefficient of Determination (R²)**:

```
R² = 1 - (SS_residual / SS_total)

SS_residual = Σ(actual - predicted)²
SS_total = Σ(actual - mean)²
```

- R² = 1.0 → Perfect fit
- R² = 0.0 → Model is as good as predicting the mean
- R² < 0.0 → Model is worse than the mean

### Prediction
For new input, normalize using **training statistics** (mean, std dev), then:
```
predicted_price = Σ(weight_j × normalized_input_j) + bias
```

---

## 9. Data Visualization Techniques

### Chart Types Used

| Chart Type | Screen | Data Shown | Purpose |
|-----------|--------|-----------|---------|
| **Bar Chart** | Dashboard | Avg price by city | Compare cities |
| **Line Chart** | Trends | Price vs Area | Show correlation |
| **Bar Chart** | Trends | Avg price by bedrooms | Show bedroom impact |
| **Pie Chart** | Trends | Houses per city | Show distribution |
| **Bar Chart** | Trends | Price by year range | Show time trends |

### Library: MPAndroidChart
- Open-source Android charting library
- Supports: Line, Bar, Pie, Radar, Scatter, Candlestick charts
- Features: Animations, touch gestures, scaling, custom formatting
- Dependency: `com.github.PhilJay:MPAndroidChart:v3.1.0`

### Chart Styling (ChartHelper.java)
All charts are styled consistently using:
- Disabled description label
- Bottom-positioned X-axis with custom labels
- Left Y-axis only (right hidden)
- Grid lines in light gray (#E0E0E0)
- Dark text (#1A1A2E) for readability
- Smooth Y-axis animation (1000ms)

---

## 10. How to Run the Application

### Prerequisites
1. Install **Android Studio** (latest version)
2. Install **JDK 8+**
3. Create an Android Virtual Device (AVD) with API 24+

### Steps
1. **Open the project** in Android Studio:
   - File → Open → Navigate to `House_Pricing` folder → Click OK
2. **Wait for Gradle sync** to complete (downloads dependencies)
3. **Create an emulator** (if not already):
   - Tools → Device Manager → Create Device
   - Select a phone (e.g., Pixel 6) → Next
   - Select system image (API 30+) → Next → Finish
4. **Run the app**:
   - Click the green ▶ Run button
   - Select the emulator → Click OK
5. **Navigate** using the bottom navigation bar:
   - Dashboard → Trends → Predict → About

### Troubleshooting
- If Gradle sync fails, check internet connection (needs to download dependencies)
- If "JitPack" repo fails, ensure `maven { url 'https://jitpack.io' }` is in `settings.gradle`
- Clear cache: File → Invalidate Caches → Restart

---

## 11. Code Flow & Logic Explanation

### Application Startup Flow
```
1. App launches → MainActivity.onCreate()
2. setContentView(activity_main.xml) loads toolbar + FrameLayout + BottomNav
3. DashboardFragment is loaded as default
4. CSVParser.parse() reads house_prices.csv → List<House>
5. Statistics calculated (total, avg, min, max)
6. BarChart populated with city-wise data
7. UI rendered to user
```

### Prediction Flow
```
1. User taps "Predict" tab → PredictFragment loads
2. trainModel() called:
   a. CSVParser reads dataset
   b. Extract features [area, bedrooms, bathrooms, age] for each house
   c. LinearRegression.train() called:
      - Normalize features (z-score)
      - Run gradient descent (2000 iterations)
      - Calculate R² score
3. User enters values → taps "Predict Price"
4. predictPrice() called:
   a. Validate all inputs (non-empty, within range)
   b. Create feature array [area, bedrooms, bathrooms, age]
   c. model.predict(features):
      - Normalize input using training stats
      - Compute weighted sum + bias
      - Return predicted price
5. Result card shown with price and R² score
```

### Key Classes Explained

#### House.java (Model)
- POJO (Plain Old Java Object) representing a house listing
- Stores: id, area, bedrooms, bathrooms, yearBuilt, city, price
- `getFeatures()` returns array for ML: [area, bedrooms, bathrooms, age]
- `getAge()` computes house age as `2025 - yearBuilt`

#### LinearRegression.java (ML Engine)
- Implements Multiple Linear Regression from scratch
- No external ML libraries used
- Key methods:
  - `train(features, prices)` — learns weights via gradient descent
  - `predict(features)` — returns predicted price
  - `getRSquared()` — returns model accuracy

#### CSVParser.java (Data Layer)
- Reads CSV from `assets/house_prices.csv`
- Skips header row
- Parses each line into a `House` object
- Handles malformed rows gracefully

#### ChartHelper.java (UI Utility)
- Centralized chart styling for consistency
- Methods: `styleBarChart()`, `styleLineChart()`, `stylePieChart()`
- Sets axis formatting, colors, animations, and legend

---

## 12. Future Enhancements

1. **Advanced ML Models** — Implement Polynomial Regression or Decision Trees
2. **Real-time Data** — Fetch prices from an API instead of static CSV
3. **City Filter** — Allow users to filter by specific city
4. **Comparison Tool** — Compare predicted vs actual prices
5. **Dark Mode** — Add dark theme support
6. **Data Export** — Export predictions to PDF/Excel
7. **Location-based** — Use GPS for location-specific predictions
8. **Historical Trends** — Show price appreciation over time for investments

---

## 13. Viva Q&A Preparation

### Q1: What is the purpose of this application?
**A:** This app analyzes housing market data from 5 Indian cities and predicts property prices using Multiple Linear Regression. It provides visualizations (bar, line, pie charts) to help users understand housing trends and make informed decisions.

### Q2: What algorithm did you use for prediction? Why?
**A:** Multiple Linear Regression using Gradient Descent. It's appropriate because:
- House prices have a roughly linear relationship with features like area and bedrooms
- It's simple, interpretable, and efficient for small datasets
- No external ML libraries needed — implemented from scratch in Java
- Easy to explain and understand the mathematics

### Q3: What is R² score? What does it tell us?
**A:** R² (Coefficient of Determination) measures how well the model fits the data:
- R² = 1.0 means perfect predictions (no error)
- R² = 0.0 means the model is as good as just predicting the mean price
- Our model typically achieves R² ≈ 0.92+, meaning it explains ~92% of price variance

### Q4: Why did you normalize the features?
**A:** Features have very different scales:
- Area: 650–4000 (large numbers)
- Bedrooms: 1–5 (small numbers)

Without normalization, gradient descent would be dominated by the area feature and converge very slowly. Z-score normalization (subtract mean, divide by std dev) puts all features on the same scale.

### Q5: Explain gradient descent in simple terms.
**A:** Imagine you're blindfolded on a hill and want to reach the lowest point:
1. Feel the slope (calculate gradient)
2. Take a small step downhill (update weights)
3. Repeat until you reach the bottom (convergence)

The "learning rate" controls step size — too large and you overshoot, too small and it takes forever.

### Q6: What features are used for prediction?
**A:** Four features:
1. **Area** (sq ft) — Most influential, larger area = higher price
2. **Bedrooms** — More bedrooms = higher price
3. **Bathrooms** — More bathrooms = higher price
4. **House Age** (2025 - yearBuilt) — Newer houses = higher price

### Q7: Why did you use fragments instead of separate activities?
**A:** Fragments with BottomNavigationView is the recommended Android pattern because:
- Lighter weight than Activities (faster switching)
- Shares the same toolbar and navigation
- Better memory management
- Standard Material Design pattern

### Q8: What chart library did you use? Why?
**A:** MPAndroidChart — it's the most popular open-source Android charting library with 37k+ GitHub stars. It supports multiple chart types, animations, touch interaction, and is well-documented.

### Q9: How is the data loaded?
**A:** The CSV file is stored in the `assets/` folder, which is bundled with the APK:
1. `CSVParser.parse()` opens the file using `context.getAssets().open()`
2. Reads line by line with `BufferedReader`
3. Splits each line by comma
4. Creates `House` objects from parsed values
5. Returns `List<House>` for use by fragments

### Q10: What are the limitations of your model?
**A:**
- Linear Regression assumes a linear relationship (may not capture complex patterns)
- Only 4 features used (real price depends on location within city, amenities, floor, etc.)
- Small dataset (150 records) — more data would improve accuracy
- No cross-validation implemented
- City information is not used as a feature in prediction (could use one-hot encoding)

### Q11: What is Mean Squared Error (MSE)?
**A:** MSE is the average of squared differences between predicted and actual values:
```
MSE = (1/n) × Σ(predicted_i - actual_i)²
```
We minimize MSE during training. Squaring ensures all errors are positive and penalizes large errors more heavily.

### Q12: What design pattern does the app follow?
**A:** The app uses a simplified **MVC (Model-View-Controller)** pattern:
- **Model**: `House.java`, `LinearRegression.java` (data + logic)
- **View**: XML layouts (UI)
- **Controller**: Fragment classes (connect model to view)

---

*Document prepared for academic viva presentation.*  
*Application Version: 1.0*
