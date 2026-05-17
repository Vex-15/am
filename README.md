## 📚 Projects Included

### 1. Matrix Operations using NumPy
Perform:
- Matrix Addition
- Matrix Multiplication
- Determinant
- Inverse
- Eigenvalues & Eigenvectors

- Code : 


### 2. Principal Component Analysis (PCA)
- Manual PCA implementation
- PCA using `sklearn`
- Dimensionality Reduction

### 3. Naive Bayes Spam Classifier
- Email Spam Detection
- Bayes’ Theorem based classification
- Text preprocessing

### 4. Coin Toss & Poisson Simulation
- Probability simulations
- Experimental vs Theoretical distribution comparison
- Poisson distribution analysis

### 5. Dice Roll & Binomial Simulation
- Dice roll probability simulation
- Binomial distribution experiments
- Statistical analysis

### 6. Sampling Techniques using Python
- Random Sampling
- Stratified Sampling
- Hospital waiting-time dataset analysis

- # Random and Stratified Sampling

## Problem Statement

A university wants to analyze student performance but cannot survey all 10,000 students.

### Tasks:
- Implement **Random Sampling** by selecting 1000 students at random and computing average GPA.
- Implement **Stratified Sampling** by dividing students into departments (`CIVIL`, `MECH`, `ENTC`, `COMP`) and sampling proportionally.
- Compare results of random vs stratified sampling.

---

# Data Creation / Collection

## Python Code

```python
import numpy as np
import pandas as pd

np.random.seed(125)

civil = np.random.normal(6.8, 0.8, 1000)
mech = np.random.normal(7.1, 0.7, 2000)
entc = np.random.normal(7.5, 0.6, 3000)
comp = np.random.normal(8.0, 0.5, 4000)

print(civil[:10])
```

---

## Output

```text
[6.24093045 6.80849846 6.04257085 7.06298399 7.05205166
 6.47774205 4.69335703 6.9615764  6.4741391  8.9495742 ]
```

---

## Combine All Data

```python
# Combine all data
gpa = np.concatenate([civil, mech, entc, comp])

dept = (["CIVIL"] * 1000 +
        ["MECH"] * 2000 +
        ["ENTC"] * 3000 +
        ["COMP"] * 4000)

df = pd.DataFrame({"Dept": dept, "GPA": gpa})

print("Population Mean:", df["GPA"].mean())
print("Population SD:", df["GPA"].std())
```

---

## Output

```text
Population Mean: 7.557477111577227
Population SD: 0.7252872965122296
```

---

# Simple Random Sampling

## Python Code

```python
random_sample = df.sample(1000)

print("\nRandom Sample Mean:", random_sample["GPA"].mean())

print("Random Sample SD:", random_sample["GPA"].std())
```

---

## Output

```text
Random Sample Mean: 7.520611654540375
Random Sample SD: 0.7255851250200396
```

---

# Stratified Sampling

## Python Code

```python
civil_s = df[df["Dept"] == "CIVIL"].sample(100)
mech_s = df[df["Dept"] == "MECH"].sample(200)
entc_s = df[df["Dept"] == "ENTC"].sample(300)
comp_s = df[df["Dept"] == "COMP"].sample(400)

strat_sample = pd.concat([civil_s, mech_s, entc_s, comp_s])

print("\nStratified Sample Mean:", strat_sample["GPA"].mean())

print("Stratified Sample SD:", strat_sample["GPA"].std())
```

---

## Output

```text
Stratified Sample Mean: 7.550397536707391
Stratified Sample SD: 0.71238670140451
```

---

# Conclusion

- **Random Sampling** selects samples completely at random from the population.
- **Stratified Sampling** ensures proportional representation from each department.
- The stratified sample mean is closer to the population mean, making it a more reliable sampling method for grouped data.


### 7. Hypothesis Testing (Z-Test for Mean)
- Statistical mean testing
- Hospital waiting-time comparison
- Z-Test implementation

- # Hypothesis Testing (Z-Test for Mean)

## Problem Statement
Test whether the sample mean differs significantly from the hypothesized population mean using a **Z-Test**.

---

## Python Code

```python
import numpy as np
from scipy.stats import norm

# Sample data
sample = np.array([52, 55, 51, 54, 53, 52, 50, 51])
n = len(sample)

# Given values
mu0 = 50          # hypothesized mean
sigma = 3         # population std dev (known)
alpha = 0.05

# Sample mean
x_bar = np.mean(sample)

# Step 1: Compute test statistic
Z = (x_bar - mu0) / (sigma / np.sqrt(n))   # Z statistic

# Step 2: Compute p-value (two-tailed)
p_value = 2 * (1 - norm.cdf(abs(Z)))

# Step 3: Critical value
z_critical = norm.ppf(1 - alpha/2)

# Output
print("Z statistic:", round(Z, 4))
print("p-value:", round(p_value, 6))
print("Critical value:", round(z_critical, 2))

# Decision using critical value
if abs(Z) > z_critical:
    print("Reject H0")
else:
    print("Fail to reject H0")

# Decision using p-value
if p_value < alpha:
    print("Reject H0")
else:
    print("Fail to reject H0")
```

---

## Output

```text
Z statistic: 2.1213
p-value: 0.033895
Critical value: 1.96
Reject H0
Reject H0
```

---


### 8. Hypothesis Testing (t-Test)
- Student performance analysis
- Push-up improvement verification
- Independent & paired t-test concepts

# Hypothesis Testing (t-Test)

## Problem Statement
Perform a **left-tailed t-test** to determine whether the sample mean is significantly less than the hypothesized population mean.

---

## Python Code

```python
import numpy as np
from scipy.stats import t

# Given values
x_bar = 4.6
mu0 = 5
s = 1.2
n = 20
alpha = 0.05

# Test statistic
t_stat = (x_bar - mu0) / (s / np.sqrt(n))

# Degrees of freedom
df = n - 1

# p-value (left-tailed)
p_value = t.cdf(t_stat, df)

# Critical value
t_critical = t.ppf(alpha, df)

# Output
print("t statistic:", round(t_stat, 4))
print("p-value:", round(p_value, 4))
print("critical value:", round(t_critical, 4))

# Decision using critical value
if t_stat < t_critical:
    print("Reject H0")
else:
    print("Fail to reject H0")

# Decision using p-value
if p_value < alpha:
    print("Reject H0")
else:
    print("Fail to reject H0")
```

---

## Output

```text
t statistic: -1.4907
p-value: 0.0762
critical value: -1.7291
Fail to reject H0
Fail to reject H0
```

---

## Conclusion

Since the p-value is greater than 0.05 and the t statistic does not fall in the rejection region, we fail to reject the null hypothesis (**H₀**).  
This indicates that there is not enough evidence to conclude that the sample mean is significantly less than the hypothesized population mean.


### 9. Hypothesis Testing (Z-Test for Proportion)
- Customer satisfaction analysis
- Mobile network service dataset
- Proportion testing

### 10. Chi-Square Test of Independence
- Independence testing
- Department vs learning mode analysis
- Contingency tables

- # Chi-Square Test of Independence

## Hypothesis

- **H₀:** Gender and product preference are independent.
- **H₁:** Gender and product preference are not independent.

Perform a **Chi-Square Test of Independence** on the contingency table at a 5% level of significance.

---

# Method 1: Using `scipy.stats.chi2_contingency`

## Python Code

```python
import numpy as np
from scipy.stats import chi2_contingency

# Observed data
data = np.array([[30, 10],
                 [370, 590]])

# Chi-square test
chi2, p, dof, expected = chi2_contingency(data)

# Output
print("Chi-square:", chi2)
print("p-value:", p)
print("Degrees of freedom:", dof)
print("Expected frequencies:\n", expected)

# Decision
alpha = 0.05

if p < alpha:
    print("Reject H0")
else:
    print("Fail to reject H0")
```

---

## Output

```text
Chi-square: 19.775390625
p-value: 8.709690096995101e-06
Degrees of freedom: 1

Expected frequencies:
[[ 16.  24.]
 [384. 576.]]

Reject H0
```

---

# Method 2: Manual Chi-Square Calculation

## Python Code

```python
import numpy as np
from scipy.stats import chi2

# Observed data
observed = np.array([[30, 10],
                     [370, 590]])

# Step 1: Row totals, Column totals, Grand total
row_totals = observed.sum(axis=1)
col_totals = observed.sum(axis=0)
grand_total = observed.sum()

print(observed)
print(row_totals)
print(col_totals)
print(grand_total)

# Step 2: Expected frequencies
expected = np.outer(row_totals, col_totals) / grand_total

# Step 3: Chi-square statistic
chi_square = np.sum((observed - expected)**2 / expected)

# Step 4: Degrees of freedom
r, c = observed.shape
df = (r - 1) * (c - 1)

# Step 5: p-value
p_value = 1 - chi2.cdf(chi_square, df)

# Output
print("Observed:\n", observed)
print("\nExpected:\n", expected)

print("\nChi-square statistic:", round(chi_square, 4))
print("Degrees of freedom:", df)
print("p-value:", round(p_value, 6))

# Decision
alpha = 0.05

if p_value < alpha:
    print("\nReject H0 (Variables are dependent)")
else:
    print("\nFail to reject H0 (Variables are independent)")
```

---

## Output

```text
[[ 30  10]
 [370 590]]

[ 40 960]
[400 600]
1000

Observed:
[[ 30  10]
 [370 590]]

Expected:
[[ 16.  24.]
 [384. 576.]]

Chi-square statistic: 21.2674
Degrees of freedom: 1
p-value: 0.000004

Reject H0 (Variables are dependent)
```

---

# Conclusion

Since the p-value is less than 0.05, we reject the null hypothesis (**H₀**).

This indicates that **gender and product preference are not independent**, meaning there is a significant association between the two variables.


### 11. Simple Linear Regression (Food Delivery Revenue)
- Revenue prediction
- Relationship between orders and income
- Regression analysis
# Simple Linear Regression (Temperature vs Sales)

## Problem Statement

A company wants to analyze the relationship between **temperature** and **sales** using **Simple Linear Regression**.

### Objectives:
- Visualize the relationship using a scatter plot
- Build a regression model
- Compute regression coefficients manually and using sklearn
- Predict sales for a given temperature
- Evaluate model performance using **R² Score**

---

# Using Inbuilt Model (`sklearn`)

## Import Libraries

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score
```

---

## Given Data

```python
X = np.array([27, 20, 32, 25, 35, 22]).reshape(-1, 1)
Y = np.array([2600, 2100, 3050, 2550, 3390, 2250])

X = pd.DataFrame(X)

print(X)
```

---

## Output

```text
    0
0  27
1  20
2  32
3  25
4  35
5  22
```

---

# Scatter Plot

## Python Code

```python
plt.scatter(X, Y)

plt.xlabel("Temperature (°C)")
plt.ylabel("Sales (₹)")
plt.title("Scatter Plot: Temperature vs Sales")

plt.show()
```

---

# Manual Calculation of Regression Coefficients

## Python Code

```python
import numpy as np
import matplotlib.pyplot as plt

X = np.array([27, 20, 32, 25, 35, 22])
Y = np.array([2600, 2100, 3050, 2550, 3390, 2250])

n = len(X)

X_sqre = X**2
X_Y = X*Y

X_sum = np.sum(X)
Y_sum = np.sum(Y)

X_sqre_sum = np.sum(X_sqre)
X_Y_sum = np.sum(X_Y)

slope_beta1 = (n*X_Y_sum - X_sum*Y_sum) / (n*X_sqre_sum - X_sum**2)

intercept_beta0 = (Y_sum - slope_beta1 * X_sum) / n

print("Slope (beta1):", slope_beta1)
print("Intercept (beta0):", intercept_beta0)
```

---

## Output

```text
Slope (beta1): 83.77622377622377
Intercept (beta0): 408.6713286713287
```

---

# Regression Line Plot

## Python Code

```python
Y_pred = intercept_beta0 + slope_beta1 * X

plt.scatter(X, Y, label="Actual Data")

plt.plot(X, Y_pred, label="Regression Line")

plt.xlabel("Temperature (°C)")
plt.ylabel("Sales (₹)")
plt.title("Regression Line with Scatter Plot")

plt.legend()

plt.show()
```

---

# Model Fitting using `LinearRegression`

## Python Code

```python
model = LinearRegression()

model.fit(X, Y)
```

---

# Regression Coefficients

## Python Code

```python
beta_0 = model.intercept_
beta_1 = model.coef_[0]

print("Intercept (a):", beta_0)
print("Slope (b):", beta_1)
```

---

## Output

```text
Intercept (a): 408.6713286713296
Slope (b): 83.7762237762238
```

---

# Scatter Plot and Regression Line

## Python Code

```python
# Predicted values
Y_pred = model.predict(X)

plt.scatter(X, Y, label="Actual Data")

plt.plot(X, Y_pred, label="Regression Line")

plt.xlabel("Temperature (°C)")
plt.ylabel("Sales (₹)")
plt.title("Regression Line with Scatter Plot")

plt.legend()

plt.show()
```

---

# Prediction of Sales

## Python Code

```python
temp = np.array([[30]])

predicted_sales = model.predict(temp)

print("Predicted Sales at 30°C:", predicted_sales[0])
```

---

## Output

```text
Predicted Sales at 30°C: 2921.958041958042
```

---

# Model Evaluation using R² Score

## Python Code

```python
r2_score_value = r2_score(Y, Y_pred)

print("R-squared Score:", r2_score_value)
```

---

## Output

```text
R-squared Score: 0.9903403053107028
```

---

# Conclusion

- The regression model shows a strong positive relationship between temperature and sales.
- As temperature increases, sales also increase.
- The R² score is very close to 1, indicating that the model fits the data extremely well.
- The model can be used to predict future sales based on temperature values.

### 12. Simple Linear Regression (Rainfall vs Water Level)
- Reservoir water level prediction
- Rainfall data analysis
- Linear regression model

### 13. Directional Derivative using Python
- Gradient computation
- Directional derivative calculation
- Pollution concentration analysis

- 
```python
import numpy as np
import matplotlib.pyplot as plt
```

---

# Define Function

## Python Code

```python
# Define function
def f(x, y):
    return x**2 + y**2
```

The function used is:

:contentReference[oaicite:0]{index=0}

---

# Gradient Function

## Python Code

```python
# Gradient
def grad(x, y):

    df_dx = 2*x
    df_dy = 2*y

    return np.array([df_dx, df_dy])
```

Gradient of the function:

:contentReference[oaicite:1]{index=1}

---

# Gradient Descent Algorithm

## Python Code

```python
# Gradient Descent
def gradient_descent(start, lr=0.1, steps=30):

    path = [start]

    current = np.array(start, dtype=float)

    for _ in range(steps):

        g = grad(current[0], current[1])

        current = current - lr * g

        path.append(current.copy())

    return np.array(path)
```

---

# Run Gradient Descent

## Python Code

```python
# Run GD
start = [4, 4]

path = gradient_descent(start)
```

---

# Create Grid for Contour Plot

## Python Code

```python
# Create grid for contour plot
x_vals = np.linspace(-5, 5, 100)
y_vals = np.linspace(-5, 5, 100)

X, Y = np.meshgrid(x_vals, y_vals)

Z = f(X, Y)
```

---

# Visualization

## Python Code

```python
# Plot
plt.figure()

plt.contour(X, Y, Z, levels=20)

plt.plot(path[:,0], path[:,1], marker='o')

plt.title("Gradient Descent on f(x,y) = x^2 + y^2")

plt.xlabel("x")
plt.ylabel("y")

plt.show()
```


### 14. Gradient Descent Algorithm
- Optimization using Gradient Descent
- Loss function minimization
- Iterative learning process

## Python Code

```python
import numpy as np
```

---

# Function Gradient

## Python Code

```python
# Function gradient
def gradient(x, y):

    df_dx = 2*x
    df_dy = 2*y

    return np.array([df_dx, df_dy])
```

Gradient of the function:

:contentReference[oaicite:0]{index=0}

---

# Directional Derivative Function

## Python Code

```python
# Directional derivative
def directional_derivative(x, y, direction):

    grad = gradient(x, y)

    # Normalize direction vector
    u = direction / np.linalg.norm(direction)

    return np.dot(grad, u)
```

Directional derivative formula:

:contentReference[oaicite:1]{index=1}

---

# Example Calculation

## Python Code

```python
# Example
point = (1, 2)

direction = np.array([3, 4])

result = directional_derivative(point[0], point[1], direction)

print("Directional Derivative:", result)
```

---

## Output

```text
Directional Derivative: 4.4
```

---

# Evaluating Function at a Given Point

## Python Code

```python
import sympy as sp

# Define symbols
x, y = sp.symbols('x y')

# Define function
f = x**2 + 3*x*y + y**2

# Evaluate function
result = f.subs({x: 1, y: 1})

print(result)
```
