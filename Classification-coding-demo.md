Classification coding demo
================
Michael Jacobs
2023-07-15

## Introduction

This coding demo is based on chapter 4.7 of James et al. ‘An
Introduction to Statistical Learning’. It works with `Smarket` - a
dataset of stock market data from the `ISLR2` package. You may need to
first install this package before loading the data.

This data set consists of percentage returns for the S&P 500 stock index
over 1, 250 days, from the beginning of 2001 until the end of 2005. For
each date, percentage returns for each of the five previous trading days
is recorded, (`Lag1` through `Lag5`). Also recorded is `Volume` (the
number of shares traded on the previous day, in billions), `Today` (the
percentage return on the date in question) and `Direction` (whether the
market was Up or Down on this date).

For this coding demo, **the goal is to predict `Direction` (a
qualitative response) using the other features.**

## Logistic regression

First, let’s try to predict `Direction` using a logistic regression,
with all lags and `Volume` as predictors. We do this using the `glm()`
function. Specifying `family = binomial` tells R to fit a logistic
regression rather than another type of generalised linear model.

``` r
# Fit a logistic regression

glm.fits <- glm(
  Direction ~ Lag1 + Lag2 + Lag3 + Lag4 + Lag5 + Volume ,
  data = Smarket , family = binomial
)

summary(glm.fits)
```

    ## 
    ## Call:
    ## glm(formula = Direction ~ Lag1 + Lag2 + Lag3 + Lag4 + Lag5 + 
    ##     Volume, family = binomial, data = Smarket)
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)
    ## (Intercept) -0.126000   0.240736  -0.523    0.601
    ## Lag1        -0.073074   0.050167  -1.457    0.145
    ## Lag2        -0.042301   0.050086  -0.845    0.398
    ## Lag3         0.011085   0.049939   0.222    0.824
    ## Lag4         0.009359   0.049974   0.187    0.851
    ## Lag5         0.010313   0.049511   0.208    0.835
    ## Volume       0.135441   0.158360   0.855    0.392
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 1731.2  on 1249  degrees of freedom
    ## Residual deviance: 1727.6  on 1243  degrees of freedom
    ## AIC: 1741.6
    ## 
    ## Number of Fisher Scoring iterations: 3

Careful how you interpret the output here.

- As in linear regression model outputs, the p-values tell us whether
  the coefficient is statistically significant (i.e. can we reject the
  null hypothesis that this coefficient is 0?)

- Unlike in a linear regression model, the coefficients themselves (in
  the `Estimate` column) do not tell us the marginal effect of the
  variable on the outcome. Instead, it tells us the increase in log odds
  of the outcome as the predictor increases by one unit. This is
  difficult to interpret meaningfully!

- We can convert the coefficients into ‘odds ratios’ (rather than change
  in ‘log odds’) by exponentiating them. i.e. $exp(\beta_1)$ is the
  ratio of the odds of the binary outcome occurring, given a 1-unit
  increase in variable 1, over the odds of the binary outcome occurring,
  given no change in variable 1.

``` r
# Exponentiate the coefficients to turn them into odds

exp(coef(glm.fits))
```

    ## (Intercept)        Lag1        Lag2        Lag3        Lag4        Lag5 
    ##   0.8816146   0.9295323   0.9585809   1.0111468   1.0094029   1.0103664 
    ##      Volume 
    ##   1.1450412

This is still quite difficult to interpret (and is a disadvantage of
logistic regression relative to linear regression). But note that
interpreting coefficients is only really important if we are interested
in *inference*, not *prediction*. Let’s move on to thinking about
*prediction*.

We can use the `predict()` function to predict the outcome of units
given we have (a) a pre-trained model (in this case `glm.fits`); and (b)
a dataset with values for all the predictors in that model.

``` r
# First, let's apply the predict() function to the training data itself

# Set type = "response" to get predicted probabilities out

pred_probs <- predict(glm.fits, type = "response")

pred_probs[1:10]
```

    ##         1         2         3         4         5         6         7         8 
    ## 0.5070841 0.4814679 0.4811388 0.5152224 0.5107812 0.5069565 0.4926509 0.5092292 
    ##         9        10 
    ## 0.5176135 0.4888378

Because we did not supply the `predict()` function with a `newdata`
argument, it used the training data of `glm.fits` itself in order to
make it’s predictions. In other words, the 10 values in the output above
represent the predict probability of the stock market moving upward for
the first 10 observations in the Smarket dataset.

How well does our model do at predicting the outcomes of the data it was
trained on? Let’s evaluate this using a confusion matrix

``` r
# First, lets create a predicted outcome variable
# If pred_prob > 0.5, assign value "Up", else assign value "Down"

pred_outcomes <- ifelse(pred_probs > 0.5, "Up", "Down")

# Now let's compare this to the true value of `Direction` (which is known in the training data)

true_outcomes <- Smarket$Direction

table(pred_outcomes, true_outcomes)
```

    ##              true_outcomes
    ## pred_outcomes Down  Up
    ##          Down  145 141
    ##          Up    457 507

Using this confusion matrix, we can see how many observations were
correctly and incorrectly classified in each class. From this, we can
calculate the proportion correctly (and incorrectly) classified.

``` r
# Proportion correctly classified

(145 + 507) / (145 + 507 + 141 + 457)
```

    ## [1] 0.5216

``` r
mean(pred_outcomes == true_outcomes)
```

    ## [1] 0.5216

So our model can correctly predict the direction of the movement in the
stock market *in our training set* in 52.16% of cases.

But we should never use our training set to evaluate model performance,
because there is a risk that we overfit our model to our training data,
in which case, the model would be better at predicting outcomes on the
training data than new data.

Solution: let’s split our data in a training and test set. We will use
the training set to train the model, and the test set to evaluate the
model.

``` r
# Split the data into a training set (data from before 2005) and a test set (data from after 2005)

training_data <- Smarket[Smarket$Year < 2005, ]
test_data <- Smarket[Smarket$Year >= 2005, ]

# Now lets fit the model, this time just using the training set

glm.fits <- glm(
  Direction ~ Lag1 + Lag2 + Lag3 + Lag4 + Lag5 + Volume,
  data = training_data , family = binomial
)

# And then predict the outcome for the test set
# To do this, we will use the newdata argument this time

pred_probs_test <- predict(glm.fits, newdata = test_data,
                     type = "response")

# And finally, let's evaluate the performance of the model on the test set

pred_outcomes_test <- ifelse(pred_probs_test > 0.5, "Up", "Down")

true_outcomes_test <- test_data$Direction

table(pred_outcomes_test, true_outcomes_test)
```

    ##                   true_outcomes_test
    ## pred_outcomes_test Down Up
    ##               Down   77 97
    ##               Up     34 44

``` r
# Calculate proportion of test set correctly classified

mean(pred_outcomes_test == true_outcomes_test)
```

    ## [1] 0.4801587

Proportion correctly classified in the test set is 48% - which is worse
than guessing :(

## Naive Bayes

Now let’s try using the Naive Bayes classifier. For this, we will need
the `e1071` library - again, you may need to install this using
`install.packages()`.

``` r
# install.packages("e1071")
library(e1071)
```

As with the logistic regression model, let’s train the model using data
from before 2005, and test it using data from after 2005. We do this
using the `naiveBayes()` function.

``` r
nb.fit <- naiveBayes(Direction ~ Lag1 + Lag2 + Lag3 + Lag4 + Lag5 + Volume,
                     data = Smarket)
```

When can then use the `predict()` function in exactly the same way as
for the logistic regression model (but this time, to get probabilities,
we specify `type = "raw"`).

``` r
# ?predict.naiveBayes

pred_probs_test_nb <- predict(nb.fit, newdata = test_data, type = "raw")

# And finally, let's evaluate the performance of the model on the test set

pred_outcomes_test_nb <- predict(nb.fit, newdata = test_data, type = "class")

true_outcomes_test <- test_data$Direction

table(pred_outcomes_test_nb, true_outcomes_test)
```

    ##                      true_outcomes_test
    ## pred_outcomes_test_nb Down  Up
    ##                  Down    2   0
    ##                  Up    109 141

``` r
# Calculate proportion of test set correctly classified

mean(pred_outcomes_test_nb == true_outcomes_test)
```

    ## [1] 0.5674603

The proportion correctly classified is now 56.7% - this looks like an
improvement! But careful - the model has classified all but 2
observations as “Up”. Of the 111 test set observations with a true value
of “Down” only 2 were correctly classified! We therefore need to
consider the performance of the model in each class, as well as the
average performance across classes.
