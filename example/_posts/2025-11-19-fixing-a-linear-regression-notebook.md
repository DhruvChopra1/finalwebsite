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

```html
<iframe 
  src="/finalwebsite/assets/img/linear-regression-final.pdf" 
  width="100%" 
  height="800px" 
  style="border:2px solid #ddd; border-radius: 5px; margin-top: 20px;">
  
  Your browser does not support iframes. 
  You can <a href="/finalwebsite/assets/img/linear-regression-final.pdf">download the PDF here</a>.
</iframe>
