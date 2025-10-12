---
layout: post
title: "Anscombe's Quartet: Why You Must Visualize Your Data"
description: >
  A final report and interactive analysis proving that statistical summaries alone are meaningless for understanding data distribution.
categories: [data-science, visualization, projects]
tags: [eda, python, anscombe, plotly]
---

Hey there! This post wraps up my work on Anscombe's Quartet, a classic data science example demonstrating why visualization is non-negotiable for Exploratory Data Analysis (EDA). 

## Final Report and Code

The complete statistical analysis, including residual plots, robust regression (RANSAC) comparison, and a detailed discussion, is available in the final PDF report.

<div style="text-align: center; margin: 30px 0;">
    <a href="{{ site.baseurl }}/anscombe_report.pdf" class="btn btn-primary btn-lg" role="button" download>
        Download Full Analysis Report (PDF)
    </a>
</div>

---

## Interactive Visualization (Above & Beyond)

Below is an **interactive Plotly visualization** that allows you to hover over the individual points for all four datasets, clearly showing their distinct patterns despite sharing the exact same mean, standard deviation, correlation, and OLS regression line.

<div style="margin: 0 auto; max-width: 900px; padding: 20px;">
    <iframe 
        src="{{ site.baseurl }}/anscombe_plotly.html" 
        width="100%" 
        height="600" 
        frameborder="0" 
        style="border: 1px solid #ccc; border-radius: 8px;">
    </iframe>
</div>

---

## Key Takeaway

The analysis proved that:
* **Datasets I, II, and III** show linear, curvilinear, and outlier-driven patterns, respectively.
* **Dataset IV** is dominated by a single, high-leverage point.
* **Robust Regression (RANSAC)** provided a much more accurate line for Dataset IV by ignoring the outlier, a finding completely missed by OLS and the statistical table.

*All code for this project is available in my [GitHub Repository Link Here].*
