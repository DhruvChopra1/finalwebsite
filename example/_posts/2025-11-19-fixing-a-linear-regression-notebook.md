Markdown

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

### **How to Display The PDF**

The `iframe` code below will display your PDF. Just make sure the file `Linear Regression.pdf` is uploaded to the `/finalwebsite/example/_posts/` directory on your web server.

```html
<iframe 
  src="/finalwebsite/example/_posts/Linear Regression.pdf" 
  width="100%" 
  height="800px" 
  style="border:2px solid #ddd; border-radius: 5px; margin-top: 20px;">
  
  Your browser does not support iframes. 
  You can <a href="/finalwebsite/example/_posts/Linear Regression.pdf">download the PDF here</a>.
</iframe>
A quick tip: In the future, I'd recommend renaming the file to something like linear-regression.pdf (without the space). Filenames with spaces can sometimes cause broken links on the web. But for now, the path you provided is in the code!

The Debugging Log: Errors and Fixes
Here is a complete breakdown of everything that was broken and how we fixed it, from top to bottom.

1. The Environment Crash
The Problem: The notebook failed immediately on the first code cell.

The Error: ModuleNotFoundError: No module named 'sklearn'

The Fix: This was an environment problem. The notebook's Python environment was missing the required libraries. The tutorial also depends on statsmodels, seaborn, and scipy, which would have caused errors later.

Solution: Install all missing libraries from the terminal.

Bash

pip install scikit-learn pandas numpy matplotlib statsmodels scipy seaborn
2. The Missing Data File
The Problem: The notebook tried to load a local file that didn't exist.

The Error: FileNotFoundError: [Errno 2] No such file or directory: 'anscombe_i.csv'

The Fix: The filename anscombe_i strongly implies this is Anscombe's Quartet, dataset I. This famous dataset is built into the seaborn library. We replaced the broken pd.read_csv call with a call to seaborn.

Solution:

Python

# Load the entire Anscombe dataset from seaborn
anscombe = sns.load_dataset('anscombe')

# Filter for just dataset "I"
anscombe_i = anscombe[anscombe['dataset'] == 'I'].copy()
3. The reshape Error
The Problem: The code failed when trying to prepare the data for scikit-learn.

The Error: AttributeError: 'Series' object has no attribute 'reshape'

The Fix: The code anscombe_i.x.reshape(...) was trying to call a NumPy function on a pandas Series object. You must first access the underlying NumPy array using the .values attribute.

Solution:

Python

# Old, broken code
# X = anscombe_i.x.reshape((len(anscombe_i.x), 1))

# Fixed code
X = anscombe_i.x.values.reshape(-1, 1)
y = anscombe_i.y.values.reshape(-1, 1)
4. The jointplot TypeError
The Problem: This was a tricky one! The seaborn plot failed even after we fixed the data.

The Error: TypeError: jointplot() got multiple values for argument 'data'

The Fix: The original code (sns.jointplot("x", "y", data=anscombe_i, ...) ) was using an old, ambiguous syntax. Newer versions of seaborn received "x" as the data argument (by position) and also received data=anscombe_i (by keyword), causing a conflict.

Solution: Use the modern, explicit syntax by naming the x, y, and data arguments.

Python

# Old, broken code
# g = sns.jointplot("x", "y", data=ansiscombe_i, ...)

# Fixed code
g = sns.jointplot(data=anscombe_i, x="x", y="y", ...)
5. Outdated and Deprecated Functions
The Problem: The notebook was clearly written a few years ago and used several function parameters that are now outdated.

The Errors: AttributeError or DeprecationWarning on:

plt.hist(..., normed=1)

sns.jointplot(..., size=7)

The Fix: We replaced these with their modern equivalents.

Solution:

normed=1 was replaced with density=True.

size=7 was replaced with height=7.

6. Bad Imports and pylab
The Problem: The original code used functions like scatter(...), plot(...), and polyfit(...) without the plt. or np. prefixes. This implies a from pylab import * was intended, which is bad practice. This also caused a NameError on P.normpdf in the residual plot cell.

The Error: NameError: name 'P' is not defined (or name 'normpdf' is not defined)

The Fix: We went through the notebook and added the correct plt. and np. prefixes everywhere. For the missing normpdf function, we added the correct import from scipy.stats.

Solution:

Python

from scipy.stats import norm

# ...

# Old, broken code
# y_pdf = P.normpdf( bins, error_mean, error_sigma)

# Fixed code
y_pdf = norm.pdf(bins, error_mean, error_sigma)
7. Slow, Inefficient Loops
The Problem: To show the residual lines, the code used for loops to draw every single line one by one. This is very slow and inefficient in Python.

The Fix: NumPy and Matplotlib are optimized for "vectorized" operations. We replaced the slow loops with a single, fast function call.

Solution:

The for loop drawing vertical lines was replaced with plt.vlines(...).

The for loop drawing horizontal lines was replaced with plt.hlines(...).

Conclusion
And that's it! After fixing all these environment, data, and code-rot issues, the entire notebook runs perfectly from top to bottom. It's a great reminder that "old" code and tutorials are still incredibly valuable, but they often need a little maintenance to bring them up to speed with modern libraries.

Hopefully, this debugging log helps you if you run into similar issues!
