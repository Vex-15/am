# Python Statistics & Machine Learning Lab Projects

---

# 1. Matrix Operations using NumPy

```python
import numpy as np

# Define matrices
A = np.array([[1, 2],
              [3, 4]])

B = np.array([[5, 6],
              [7, 8]])

# Matrix Addition
print("Matrix Addition:")
print(A + B)

# Matrix Multiplication
print("\nMatrix Multiplication:")
print(np.dot(A, B))

# Determinant
print("\nDeterminant of A:")
print(np.linalg.det(A))

# Inverse
print("\nInverse of A:")
print(np.linalg.inv(A))

# Eigenvalues and Eigenvectors
eigenvalues, eigenvectors = np.linalg.eig(A)

print("\nEigenvalues:")
print(eigenvalues)

print("\nEigenvectors:")
print(eigenvectors)
```

---

# 2. Principal Component Analysis (PCA)

```python
import numpy as np
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

# Sample dataset
X = np.array([[2.5, 2.4],
              [0.5, 0.7],
              [2.2, 2.9],
              [1.9, 2.2],
              [3.1, 3.0],
              [2.3, 2.7],
              [2, 1.6],
              [1, 1.1],
              [1.5, 1.6],
              [1.1, 0.9]])

# Standardize data
X_std = StandardScaler().fit_transform(X)

# Manual PCA
cov_matrix = np.cov(X_std.T)

eigenvalues, eigenvectors = np.linalg.eig(cov_matrix)

sorted_index = np.argsort(eigenvalues)[::-1]
sorted_eigenvectors = eigenvectors[:, sorted_index]

eigenvector_subset = sorted_eigenvectors[:, 0:2]

X_reduced = np.dot(X_std, eigenvector_subset)

print("Manual PCA Result:")
print(X_reduced)

# PCA using sklearn
pca = PCA(n_components=2)

X_pca = pca.fit_transform(X_std)

print("\nSklearn PCA Result:")
print(X_pca)
```

---

# 3. Naive Bayes Spam Classifier

```python
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.model_selection import train_test_split

# Sample dataset
emails = [
    "Win money now",
    "Claim your free prize",
    "Meeting at 10 am",
    "Project submission tomorrow",
    "Free lottery ticket",
    "Important office update"
]

labels = [1, 1, 0, 0, 1, 0]

# Convert text into numerical data
vectorizer = CountVectorizer()

X = vectorizer.fit_transform(emails)

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, labels, test_size=0.3, random_state=42
)

# Train model
model = MultinomialNB()

model.fit(X_train, y_train)

# Test
test_email = ["Free money prize"]

test_vector = vectorizer.transform(test_email)

prediction = model.predict(test_vector)

print("Spam" if prediction[0] == 1 else "Not Spam")
```

---

# 4. Coin Toss & Poisson Simulation

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import poisson

# Coin Toss Simulation
coin_tosses = np.random.choice(['H', 'T'], size=1000)

heads = np.count_nonzero(coin_tosses == 'H')
tails = np.count_nonzero(coin_tosses == 'T')

print("Heads:", heads)
print("Tails:", tails)

# Poisson Distribution Simulation
lam = 5

data = np.random.poisson(lam, 1000)

# Plot histogram
plt.hist(data, bins=15, density=True)

# Theoretical distribution
x = np.arange(0, 15)

plt.plot(x, poisson.pmf(x, lam))

plt.title("Poisson Distribution")
plt.xlabel("Events")
plt.ylabel("Probability")

plt.show()
```

---

# 5. Dice Roll & Binomial Simulation

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import binom

# Dice Roll Simulation
rolls = np.random.randint(1, 7, 1000)

for i in range(1, 7):
    print(f"{i} occurred {np.sum(rolls == i)} times")

# Binomial Distribution Simulation
n = 10
p = 0.5

binomial_data = np.random.binomial(n, p, 1000)

# Histogram
plt.hist(binomial_data, bins=10, density=True)

# Theoretical probabilities
x = np.arange(0, n + 1)

plt.plot(x, binom.pmf(x, n, p))

plt.title("Binomial Distribution")
plt.xlabel("Successes")
plt.ylabel("Probability")

plt.show()
```

---

# 6. Sampling Techniques using Python

```python
import numpy as np
import pandas as pd

np.random.seed(125)

civil = np.random.normal(6.8, 0.8, 1000)
mech = np.random.normal(7.1, 0.7, 2000)
entc = np.random.normal(7.5, 0.6, 3000)
comp = np.random.normal(8.0, 0.5, 4000)

# Combine all data
gpa = np.concatenate([civil, mech, entc, comp])

dept = (["CIVIL"] * 1000 +
        ["MECH"] * 2000 +
        ["ENTC"] * 3000 +
        ["COMP"] * 4000)

df = pd.DataFrame({"Dept": dept, "GPA": gpa})

# Random Sampling
random_sample = df.sample(1000)

print("Random Sample Mean:", random_sample["GPA"].mean())

# Stratified Sampling
civil_s = df[df["Dept"] == "CIVIL"].sample(100)
mech_s = df[df["Dept"] == "MECH"].sample(200)
entc_s = df[df["Dept"] == "ENTC"].sample(300)
comp_s = df[df["Dept"] == "COMP"].sample(400)

strat_sample = pd.concat([civil_s, mech_s, entc_s, comp_s])

print("Stratified Sample Mean:", strat_sample["GPA"].mean())
```

---

# 7. Hypothesis Testing (Z-Test for Mean)

```python
import numpy as np
from scipy.stats import norm

# Sample data
sample = np.array([52, 55, 51, 54, 53, 52, 50, 51])

n = len(sample)

mu0 = 50
sigma = 3
alpha = 0.05

x_bar = np.mean(sample)

Z = (x_bar - mu0) / (sigma / np.sqrt(n))

p_value = 2 * (1 - norm.cdf(abs(Z)))

print("Z statistic:", round(Z, 4))
print("p-value:", round(p_value, 6))
```

---

# 8. Hypothesis Testing (t-Test)

```python
import numpy as np
from scipy.stats import t

x_bar = 4.6
mu0 = 5
s = 1.2
n = 20
alpha = 0.05

t_stat = (x_bar - mu0) / (s / np.sqrt(n))

df = n - 1

p_value = t.cdf(t_stat, df)

print("t statistic:", round(t_stat, 4))
print("p-value:", round(p_value, 4))
```

---

# 9. Chi-Square Test of Independence

```python
import numpy as np
from scipy.stats import chi2_contingency

# Observed data
data = np.array([[30, 10],
                 [370, 590]])

chi2, p, dof, expected = chi2_contingency(data)

print("Chi-square:", chi2)
print("p-value:", p)
print("Degrees of freedom:", dof)

print("Expected frequencies:\n", expected)
```

---

# 10. Simple Linear Regression

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score

X = np.array([27, 20, 32, 25, 35, 22]).reshape(-1, 1)

Y = np.array([2600, 2100, 3050, 2550, 3390, 2250])

model = LinearRegression()

model.fit(X, Y)

Y_pred = model.predict(X)

plt.scatter(X, Y)
plt.plot(X, Y_pred)

plt.show()

print("Intercept:", model.intercept_)
print("Slope:", model.coef_[0])

r2 = r2_score(Y, Y_pred)

print("R² Score:", r2)
```

---

# 11. Directional Derivative using Python

```python
import numpy as np

# Function gradient
def gradient(x, y):

    df_dx = 2*x
    df_dy = 2*y

    return np.array([df_dx, df_dy])

# Directional derivative
def directional_derivative(x, y, direction):

    grad = gradient(x, y)

    u = direction / np.linalg.norm(direction)

    return np.dot(grad, u)

point = (1, 2)

direction = np.array([3, 4])

result = directional_derivative(point[0], point[1], direction)

print("Directional Derivative:", result)
```

---

# 12. Gradient Descent Algorithm

```python
import numpy as np
import matplotlib.pyplot as plt

# Define function
def f(x, y):
    return x**2 + y**2

# Gradient
def grad(x, y):

    df_dx = 2*x
    df_dy = 2*y

    return np.array([df_dx, df_dy])

# Gradient Descent
def gradient_descent(start, lr=0.1, steps=30):

    path = [start]

    current = np.array(start, dtype=float)

    for _ in range(steps):

        g = grad(current[0], current[1])

        current = current - lr * g

        path.append(current.copy())

    return np.array(path)

start = [4, 4]

path = gradient_descent(start)

x_vals = np.linspace(-5, 5, 100)
y_vals = np.linspace(-5, 5, 100)

X, Y = np.meshgrid(x_vals, y_vals)

Z = f(X, Y)

plt.figure()

plt.contour(X, Y, Z, levels=20)

plt.plot(path[:,0], path[:,1], marker='o')

plt.title("Gradient Descent")

plt.xlabel("x")
plt.ylabel("y")

plt.show()
```

# Hypothesis Testing (Z-Test for Proportion)

```python
import numpy as np
from scipy.stats import norm

# Sample data
n = 500                 # sample size
x = 320                 # satisfied customers

# Sample proportion
p_hat = x / n

# Hypothesized population proportion
p0 = 0.55

# Significance level
alpha = 0.05

# Z-test statistic
Z = (p_hat - p0) / np.sqrt((p0 * (1 - p0)) / n)

# Two-tailed p-value
p_value = 2 * (1 - norm.cdf(abs(Z)))

# Critical value
z_critical = norm.ppf(1 - alpha/2)

# Output
print("Sample Proportion:", round(p_hat, 4))
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

