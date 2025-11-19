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

## Our Debugging Journey: A Step-by-Step Log

Fixing the notebook was only half the battle! We also had to figure out how to get it to display on this website. Here is a simple, step-by-step process of what went wrong and how we fixed it.

### Phase 1: Fixing the Python Notebook

First, we had to get the `.ipynb` file to actually run without errors.

* **Step 1: Installing Missing Libraries**
    * **Problem:** The notebook crashed immediately with a `ModuleNotFoundError: No module named 'sklearn'`.
    * **Fix:** We installed scikit-learn and other libraries (`statsmodels`, `seaborn`, `scipy`) that the notebook depended on using `pip`.

* **Step 2: Loading the Missing Data**
    * **Problem:** The code tried to load a local file named `anscombe_i.csv`, which we didn't have, causing a `FileNotFoundError`.
    * **Fix:** We recognized this as Anscombe's Quartet, dataset I. Instead of hunting for the file, we loaded this famous dataset directly from the `seaborn` library.

* **Step 3: Updating Outdated Code**
    * **Problem:** The code was old and used functions that have changed. This caused an `AttributeError` on `.reshape()` and a `TypeError` on `sns.jointplot()`.
    * **Fix:** We updated the code to modern standards. We used `.values.reshape()` to access the NumPy array and used the explicit `sns.jointplot(data=..., x=..., y=...)` syntax.
