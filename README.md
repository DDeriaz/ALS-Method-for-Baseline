---
title: "ALS Method for baseline"
author: "Denis Deriaz"
date: "2024-06-24"
output:
  md_document:
    variant: markdown_github
    preserve_yaml: true
    pandoc_args: ["--webtex"]
---

## Introduction

The Asymmetric Least Squares (ALS) method is a technique used to correct
baseline drifts in various types of data by iteratively fitting a smooth
baseline that minimizes the weighted sum of squared residuals, allowing
for effective separation of the true signal from background noise.This
method is commonly used to correct baseline drifts in spectral data,
chromatographic data, time-series data, sensor data, and data from
diffractograms.

## Mathematical Formulation

The ALS method works as follows:

1.  **Initial Estimation:**

    -   Start with an initial estimate of the baseline, typically a
        smooth function like a straight line.

2.  **Weighted Residual Minimization:**

    -   The goal is to minimize the sum of squared residuals between the
        observed data ![y](https://latex.codecogs.com/png.latex?y "y")
        and the baseline
        ![z](https://latex.codecogs.com/png.latex?z "z"), with different
        weights applied to positive and negative residuals.

3.  **Mathematical Formulation:**

    -   For observed data points
        ![(x_i, y_i)](https://latex.codecogs.com/png.latex?%28x_i%2C%20y_i%29 "(x_i, y_i)"),
        the baseline ![z](https://latex.codecogs.com/png.latex?z "z") is
        estimated by solving the following optimization problem:

    ![\min\_{z} \sum\_{i=1}^{n} w_i (y_i - z_i)^2 + \lambda \sum\_{i=1}^{n} (D^2 z)\_i^2](https://latex.codecogs.com/png.latex?%5Cmin_%7Bz%7D%20%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20w_i%20%28y_i%20-%20z_i%29%5E2%20%2B%20%5Clambda%20%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20%28D%5E2%20z%29_i%5E2 "\min_{z} \sum_{i=1}^{n} w_i (y_i - z_i)^2 + \lambda \sum_{i=1}^{n} (D^2 z)_i^2")

    -   Here, ![w_i](https://latex.codecogs.com/png.latex?w_i "w_i") are
        the weights,
        ![\lambda](https://latex.codecogs.com/png.latex?%5Clambda "\lambda")
        is a smoothing parameter, and
        ![D](https://latex.codecogs.com/png.latex?D "D") is a difference
        operator to ensure smoothness of the baseline
        ![z](https://latex.codecogs.com/png.latex?z "z").

4.  **Weight Update:**

    -   The weights
        ![w_i](https://latex.codecogs.com/png.latex?w_i "w_i") are
        updated asymmetrically based on the residuals
        ![r_i = y_i - z_i](https://latex.codecogs.com/png.latex?r_i%20%3D%20y_i%20-%20z_i "r_i = y_i - z_i"):

    ![w_i = \begin{cases} 
    p & \text{if } y_i \> z_i \\
    1-p & \text{if } y_i \leq z_i
    \end{cases}](https://latex.codecogs.com/png.latex?w_i%20%3D%20%5Cbegin%7Bcases%7D%20%0Ap%20%26%20%5Ctext%7Bif%20%7D%20y_i%20%3E%20z_i%20%5C%5C%0A1-p%20%26%20%5Ctext%7Bif%20%7D%20y_i%20%5Cleq%20z_i%0A%5Cend%7Bcases%7D "w_i = \begin{cases} 
    p & \text{if } y_i > z_i \\
    1-p & \text{if } y_i \leq z_i
    \end{cases}")

    -   Here, ![p](https://latex.codecogs.com/png.latex?p "p") is a
        parameter between 0 and 1 (commonly set around 0.5).

5.  **Iteration:**

    -   Repeat the minimization and weight update steps until
        convergence, meaning the changes in
        ![z](https://latex.codecogs.com/png.latex?z "z") are below a
        certain threshold or a maximum number of iterations is reached.

### Detailed Steps:

1.  **Initialization:**

    -   Start with an initial baseline
        ![z = z_0](https://latex.codecogs.com/png.latex?z%20%3D%20z_0 "z = z_0").

2.  **Optimization:**

    -   Solve the weighted least squares problem with the current
        weights:

    ![z^{(k+1)} = \arg\min\_{z} \sum\_{i=1}^{n} w_i^{(k)} (y_i - z_i)^2 + \lambda \sum\_{i=1}^{n} (D^2 z)\_i^2](https://latex.codecogs.com/png.latex?z%5E%7B%28k%2B1%29%7D%20%3D%20%5Carg%5Cmin_%7Bz%7D%20%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20w_i%5E%7B%28k%29%7D%20%28y_i%20-%20z_i%29%5E2%20%2B%20%5Clambda%20%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20%28D%5E2%20z%29_i%5E2 "z^{(k+1)} = \arg\min_{z} \sum_{i=1}^{n} w_i^{(k)} (y_i - z_i)^2 + \lambda \sum_{i=1}^{n} (D^2 z)_i^2")

3.  **Weight Update:**

    -   Update weights based on the residuals:

    ![w_i^{(k+1)} = \begin{cases} 
    p & \text{if } y_i \> z_i^{(k+1)} \\
    1-p & \text{if } y_i \leq z_i^{(k+1)}
    \end{cases}](https://latex.codecogs.com/png.latex?w_i%5E%7B%28k%2B1%29%7D%20%3D%20%5Cbegin%7Bcases%7D%20%0Ap%20%26%20%5Ctext%7Bif%20%7D%20y_i%20%3E%20z_i%5E%7B%28k%2B1%29%7D%20%5C%5C%0A1-p%20%26%20%5Ctext%7Bif%20%7D%20y_i%20%5Cleq%20z_i%5E%7B%28k%2B1%29%7D%0A%5Cend%7Bcases%7D "w_i^{(k+1)} = \begin{cases} 
    p & \text{if } y_i > z_i^{(k+1)} \\
    1-p & \text{if } y_i \leq z_i^{(k+1)}
    \end{cases}")

4.  **Convergence Check:**

    -   Check if the change in
        ![z](https://latex.codecogs.com/png.latex?z "z") is below a
        predetermined threshold. If not, return to the optimization
        step.

The iterative process ensures that the baseline
![z](https://latex.codecogs.com/png.latex?z "z") converges to a smooth
curve that effectively separates the true signal from the background
noise, particularly in data with significant baseline variations.

## Exemple of Application in R

### SAXS data

We will try to fit the baseline using the SAXS dataset, which consists
of data from a diffractogram of bovine lipids. The data represents the
intensity of the signal as a function of the reciprocal spacing.

``` r
data<-read.table("./Data/SAXS.txt")
```

The data can be visualized.

``` r
plot(data,type="l")
```

![](ALS-Method-for-Baseline_files/figure-markdown_github/unnamed-chunk-2-1.png)

As depicted in the plot, the baseline increases in intensity as the RS
increases. One would like to estimate the baseline using the ALS method
and to subtract it to isolate the signal.

### The **baseline** library

The **baseline** library can be uploaded.Be aware that the data has to
be converted to an horizontal vector. Choose the
![\lambda](https://latex.codecogs.com/png.latex?%5Clambda "\lambda") and
![p](https://latex.codecogs.com/png.latex?p "p") values adequately.

``` r
library(baseline)
```

    ## 
    ## Attachement du package : 'baseline'

    ## L'objet suivant est masqué depuis 'package:stats':
    ## 
    ##     getCall

``` r
b<-getBaseline(baseline(t(as.vector(data$Intensity)),method = "als",lambda=4.5,p=0.05))

data$b<-t(b)

plot(Intensity~RS,data=data,type="l")
points(b~RS,data=data,type="l", col="blue", lwd=2)
```

![](ALS-Method-for-Baseline_files/figure-markdown_github/unnamed-chunk-3-1.png)

We can then plot the result.

``` r
data$Intensity_corrected<-data$Intensity-data$b

plot(Intensity_corrected~RS,data=data,type="l")
```

![](ALS-Method-for-Baseline_files/figure-markdown_github/unnamed-chunk-4-1.png)
