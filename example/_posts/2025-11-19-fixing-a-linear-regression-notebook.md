---
layout: post
title: "Debugging a Broken Jupyter Notebook: A Linear Regression Tutorial Case Study"
date: 2025-11-19 14:30:00 -0500
categories: data-science python projects debugging jupyter
---

I recently found a great-looking Jupyter Notebook tutorial for Linear Regression. It's a comprehensive guide that covers everything from a simple scikit-learn fit to more advanced topics like horizontal residuals and total least squares regression.

There was just one problem: **it was completely broken.**

Running the notebook from top to bottom failed on the very first code cell, and as I fixed each error, a new one popped up. This post is a step-by-step log of our debugging journey, showing every error we found and how we fixed it to get the notebook working.

## The Final, Working Tutorial (PDF)

First, here is the final, working notebook, saved as a PDF. You can see the full tutorial and all the working code and visualizations.

---

<iframe 
  src="/finalwebsite/assets/img/linear-regression-final.pdf" 
  width="100%" 
  height="800px" 
  style="border:2px solid #ddd; border-radius: 5px; margin-top: 20px;">
 
  Your browser does not support iframes. 
  You can <a href="/finalwebsite/assets/img/linear-regression-final.pdf">download the PDF here</a>.
</iframe>

---

# Complete Debugging & Restoration Log
## From Broken Jupyter Notebook to Published Blog Post

### **Project Overview**

This document provides a comprehensive, in-depth analysis of the process used to fix a broken Jupyter Notebook on Linear Regression and subsequently publish it (and a "making-of" log) to a Jekyll-based GitHub Pages website. The process was divided into two distinct phases:

1.  **Phase 1: Python Notebook Restoration:** Debugging and updating the `.ipynb` file to be fully functional, error-free, and aligned with modern coding practices.
2.  **Phase 2: Website Deployment & Debugging:** Diagnosing and fixing `404` errors related to embedding the notebook as a PDF on the blog, which involved understanding the Jekyll build process.

---

## Part 1: Python Notebook Restoration (Cell-by-Cell)

This section details every code change made to the original Jupyter Notebook.

### Cell 2: Library Imports

* **The Problem(s):**
    1.  **`ModuleNotFoundError`:** The primary error that stopped the notebook. The script tried to `import sklearn`, but the library (scikit-learn) was not installed in the Python environment.
    2.  **Missing `seaborn`:** The original notebook had no way to load the `anscombe_i.csv` file. Our fix in Cell 4 required the `seaborn` library, so we needed to add the import statement here.

* **In-Depth Explanation:**
    The `ModuleNotFoundError` is an **environment-level error**, not a code error. It means the Python interpreter looked for the `sklearn` package in its known paths and couldn't find it. The fix was to run `pip install scikit-learn` in the terminal. We also preemptively installed `seaborn`, `statsmodels`, and `scipy`, as they were required by later cells.

* **Code Comparison:**

    **Before:**
    ```python
    import matplotlib.pyplot as plt
    import numpy as np
    import pandas as pd
    from math import log
    from sklearn import linear_model
    
    #comment below if not using ipython notebook
    %matplotlib inline
    ```

    **After:**
    ```python
    import matplotlib.pyplot as plt
    import numpy as np
    import pandas as pd
    from math import log
    from sklearn import linear_model
    import seaborn as sns # Added for loading the dataset
    
    #comment below if not using ipython notebook
    %matplotlib inline
    ```

---
### Cell 4: Data Loading

* **The Problem(s):**
    1.  **`FileNotFoundError`:** The code `pd.read_csv('anscombe_i.csv')` tried to load a local file. This makes the notebook "non-portable"—it only works on a computer that has this specific file in the exact same folder.
    2.  **Indexing Issues:** Even if the file was found, the original dataset (Anscombe's Quartet) has a non-standard index. Later cells that used `for` loops would have failed.

* **In-Depth Explanation:**
    The fix was to replace the fragile local file load with a robust, public data source. The `seaborn` library comes with the Anscombe dataset built-in.
    1.  We use `sns.load_dataset('anscombe')` to load the *full* quartet.
    2.  We then filter this DataFrame for *only* dataset "I" using `anscombe[anscombe['dataset'] == 'I']`.
    3.  Crucially, we add `.copy()` to avoid a `SettingWithCopyWarning` from pandas.
    4.  Finally, we call `.reset_index(drop=True)` to create a clean, 0-based index (0, 1, 2, ...), which makes the data much easier and more predictable to work with.

* **Code Comparison:**

    **Before:**
    ```python
    #read csv
    anscombe_i = pd.read_csv('anscombe_i.csv')
    anscombe_i
    ```

    **After:**
    ```python
    # FIX: Load the dataset from seaborn instead of a missing CSV file
    anscombe = sns.load_dataset('anscombe')
    
    # Filter for dataset "I"
    anscombe_i = anscombe[anscombe['dataset'] == 'I'].copy()
    
    # Reset the index to be 0-based, which the rest of the notebook expects
    anscombe_i.reset_index(drop=True, inplace=True)
    
    anscombe_i
    ```

---
### Cell 8: First Regression (Scikit-learn)

* **The Problem(s):**
    1.  **`AttributeError: 'Series' object has no attribute 'reshape'`:** This is a very common error. The code `anscombe_i.x` is a pandas **Series**, but `.reshape()` is a **NumPy** method.
    2.  **Incorrect Plot Labels:** A simple human error where the `xlabel` and `ylabel` were swapped.

* **In-Depth Explanation:**
    1.  To fix the `AttributeError`, you must first get the underlying NumPy array from the pandas Series. You do this by accessing its `.values` attribute.
    2.  Once you have the NumPy array (`anscombe_i.x.values`), you can *then* call `.reshape()`.
    3.  We use `.reshape(-1, 1)` because scikit-learn's `fit` method expects the `X` (features) data to be a **2D matrix**. The `-1` tells NumPy to "automatically calculate the number of rows," and the `1` tells it to "create 1 column." This converts the 1D array into a 2D matrix suitable for `sklearn`.

* **Code Comparison:**

    **Before:**
    ```python
    # ...
    X = anscombe_i.x.reshape((len(anscombe_i.x), 1))
    y = anscombe_i.y.reshape((len(anscombe_i.y), 1))
    # ...
    plt.ylabel("X")
    plt.xlabel("y")
    ```

    **After:**
    ```python
    # FIX: Use .values.reshape(-1, 1) to convert pandas Series to 2D numpy array
    X = anscombe_i.x.values.reshape(-1, 1)
    y = anscombe_i.y.values.reshape(-1, 1)
    # ...
    # FIX: The x and y labels were swapped in the original code
    plt.ylabel("Y")
    plt.xlabel("X")
    ```
---
### Cell 10: Plotting Vertical Residuals

* **The Problem(s):**
    1.  **Bad Practice (`from pylab import *`):** This "star import" dumps hundreds of functions from NumPy and Matplotlib into the global namespace. This is confusing, can cause function name collisions, and makes code hard to debug.
    2.  **Inefficiency (Slow `for` loop):** The code used a Python `for` loop to draw every single residual line. For a large dataset, this would be incredibly slow.
    3.  **Data Type Error:** The code `polyfit(anscombe_i.x, y, 1)` would have failed because `y` was the 2D matrix from Cell 8, not the 1D data `polyfit` expects.

* **In-Depth Explanation:**
    The main fix here was to replace the slow, iterative `for` loop with a single **vectorized** command.
    * Python `for` loops are slow. NumPy and Matplotlib are written in C and are highly optimized to perform operations on *entire arrays at once*.
    * The `plt.vlines()` (vertical lines) function is a vectorized command that draws all the lines in one, super-fast C-level operation, replacing the entire loop.
    * We also fixed the `polyfit` call to use the original 1D pandas Series `anscombe_i.y`, which it can handle correctly.

* **Code Comparison:**

    **Before:**
    ```python
    from pylab import *
    
    k,d = polyfit(anscombe_i.x,y,1) # This would fail
    yfit = k*anscombe_i.x+d
    
    # ... plotting ...
    #plot line from point to regression line
    for ii in range(len(X)):
        plot([anscombe_i.x[ii], anscombe_i.x[ii]], [yfit[ii], y[ii]], 'k')
    
    xlabel('X')
    ylabel('Y')
    ```

    **After:**
    ```python
    # FIX: Use np.polyfit and the original 1D data
    k,d = np.polyfit(anscombe_i.x, anscombe_i.y, 1)
    yfit = k*anscombe_i.x + d
    
    # ... plotting ...
    # FIX: Replaced the slow loop with efficient plt.vlines()
    plt.vlines(anscombe_i.x, ymin=yfit, ymax=anscombe_i.y, colors='k', linestyles='--')
     
    plt.xlabel('X')
    plt.ylabel('Y')
    ```
---
### Cell 12: Residual Distribution Plot

* **The Problem(s):**
    1.  **`NameError`:** The code `import pylab as P` and then `P.normpdf(...)` would fail. The `normpdf` (Normal Distribution PDF) function is not part of Matplotlib/Pylab; it's in the `scipy.stats` library.
    2.  **Deprecated Argument:** The code `plt.hist(..., normed=1)` uses an old argument. "Deprecated" means it's being phased out and will be removed in a future version.

* **In-Depth Explanation:**
    This is a perfect example of why star imports are bad. The original author was confused about which library contained which function.
    1.  We fixed the `NameError` by adding the correct import: `from scipy.stats import norm`. We then called the function correctly with `norm.pdf(...)`.
    2.  We "future-proofed" the code by replacing the deprecated `normed=1` argument with its modern equivalent, `density=True`. They do the exact same thing (normalize the histogram so the area equals 1), but `density=True` is the correct one to use now.

* **Code Comparison:**

    **Before:**
    ```python
    import pylab as P
    # ...
    n, bins, patches = plt.hist(residual_error, 10, normed=1, facecolor='blue', alpha=0.75)
    y_pdf = P.normpdf( bins, error_mean, error_sigma) # This would cause a NameError
    l = P.plot(bins, y_pdf, 'k--', linewidth=1.5)
    ```

    **After:**
    ```python
    from scipy.stats import norm # Added correct import
    # ...
    # FIX: Use 'density=True' instead of deprecated 'normed=1'
    n, bins, patches = plt.hist(residual_error, 10, density=True, facecolor='blue', alpha=0.75)
    
    # FIX: Use 'norm.pdf' from scipy.stats, not 'P.normpdf'
    y_pdf = norm.pdf(bins, error_mean, error_sigma)
    l = plt.plot(bins, y_pdf, 'k--', linewidth=1.5)
    ```
---
### Cell 18: Seaborn Plot

* **The Problem(s):**
    1.  **`TypeError: jointplot() got multiple values for argument 'data'`:** A subtle but breaking error. The old syntax `sns.jointplot("x", "y", data=df)` is ambiguous. Newer `seaborn` versions see `"x"` as the *first positional argument*, which is `data`, and *also* see the *keyword argument* `data=anscombe_i`, causing a conflict.
    2.  **Deprecated Argument:** `size=7` is another old argument that has been replaced.

* **In-Depth Explanation:**
    The fix is to use the modern, explicit **keyword-argument syntax** for `seaborn`. Instead of `sns.jointplot("x", "y", data=df)`, the correct way is `sns.jointplot(data=df, x="x", y="y")`. This is unambiguous and works on all versions. We also replaced the deprecated `size` argument with `height`.

* **Code Comparison:**

    **Before:**
    ```python
    g = sns.jointplot("x", "y", data=anscombe_i, kind="reg",
                      xlim=(0, 20), ylim=(0, 12), color="r", size=7)
    ```

    **After:**
    ```python
    # FIX: Use 'height=7' instead of deprecated 'size=7'
    # FIX: Use keyword arguments (data=, x=, y=) for modern seaborn compatibility
    g = sns.jointplot(data=anscombe_i, x="x", y="y", kind="reg",
                       xlim=(0, 20), ylim=(0, 12), color="r", height=7)
    ```
---
### Cell 20: Horizontal Residuals Plot

* **The Problem(s):**
    1.  **Inefficiency (Slow `for` loop):** Same as Cell 10, this code used a slow Python loop to draw lines.
    2.  **Data Type Error:** Same as Cell 10, this code used the 2D `y` array in its calculations, which would have failed.

* **In-Depth Explanation:**
    The fix was identical to Cell 10. We replaced the entire `for` loop with the single, vectorized `plt.hlines()` (horizontal lines) function. We also fixed the `polyfit` and `xfit` calculations to use the 1D `anscombe_i.y` Series.

* **Code Comparison:**

    **Before:**
    ```python
    # ...
    k,d = polyfit(anscombe_i.y,anscombe_i.x,1) # Would fail
    xfit = k*y+d # Would fail
    # ...
    for ii in range(len(y)):
        plot([xfit[ii], anscombe_i.x[ii]], [y[ii], y[ii]], 'k')
    ```

    **After:**
    ```python
    # This time, we fit x as a function of y
    k,d = np.polyfit(anscombe_i.y, anscombe_i.x, 1)
    xfit = k*anscombe_i.y + d
    # ...
    # FIX: Replaced loop with efficient plt.hlines()
    plt.hlines(anscombe_i.y, xmin=xfit, xmax=anscombe_i.x, colors='k', linestyles='--')
    ```

---
### Cell 22 & 24: Final Plots

* **The Problem(s):**
    1.  **Bad Practice (`pylab`):** These cells used functions like `scatter()` and `plot()` without the `plt.` prefix, relying on the fragile "star import" from Cell 10.

* **In-Depth Explanation:**
    The fix was simple: add the standard `plt.` prefix to all plotting functions (e.g., `plt.scatter(...)`, `plt.plot(...)`). This makes the code self-contained, explicit, and easy to read.

* **Code Comparison:**

    **Before:**
    ```python
    scatter(anscombe_i.x,anscombe_i.y, color = 'black')
    plot(anscombe_i.x, y_ortho_fit, 'r')
    ```

    **After:**
    ```python
    plt.scatter(anscombe_i.x, anscombe_i.y, color = 'black')
    plt.plot(anscombe_i.x, y_ortho_fit, 'r')
    ```
---
