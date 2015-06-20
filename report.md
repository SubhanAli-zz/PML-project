# Practical Machine Learning Project
Subhan Ali  
Friday, June 19, 2015  
##Data Loading
First, I went ahead and loaded the data into R. Since the data was already partitioned between the training and tests sets, I did not need to use R in order to further subset the data.


```r
library(rpart)
library(ggplot2)
library(caret)
```

```
## Loading required package: lattice
```

```r
library(lattice)

testing <- read.csv("pml-testing.csv")
training <- read.csv("pml-training.csv")
attach(training)
```

##Model Fitting and Selection
Ultimately, I decided to choose a boosting model with trees. I did also consider and attempt to use a random forest model, but when I used the random forest model and predicted the values on the test set, there was only 50% accuracy so using a more accurate method was necessary. I also specified which variables were to be used in the model creation since many of the variables were completely missing and including them in the model would yield erroneous results.
 

```r
boosting <- train(classe ~ num_window + roll_belt + pitch_belt + yaw_belt + 
                      total_accel_belt + gyros_belt_x + gyros_belt_y + gyros_belt_z + 
                      accel_belt_x + accel_belt_y + accel_belt_z + magnet_belt_x + 
                      magnet_belt_y + magnet_belt_z + roll_arm + pitch_arm + yaw_arm + 
                      total_accel_arm + gyros_arm_x + gyros_arm_y + gyros_arm_z + accel_arm_x + 
                      accel_arm_y + accel_arm_z + magnet_arm_x + magnet_arm_y + magnet_arm_z + 
                      roll_dumbbell + pitch_dumbbell + yaw_dumbbell, 
                  method = "gbm", 
                  data = training, 
                  verbose = F)
```

```
## Loading required package: gbm
```

```
## Warning: package 'gbm' was built under R version 3.2.1
```

```
## Loading required package: survival
## 
## Attaching package: 'survival'
## 
## The following object is masked from 'package:caret':
## 
##     cluster
## 
## Loading required package: splines
## Loading required package: parallel
## Loaded gbm 2.1.1
## Loading required package: plyr
```

```r
boosting
```

```
## Stochastic Gradient Boosting 
## 
## 19622 samples
##   159 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Bootstrapped (25 reps) 
## 
## Summary of sample sizes: 19622, 19622, 19622, 19622, 19622, 19622, ... 
## 
## Resampling results across tuning parameters:
## 
##   interaction.depth  n.trees  Accuracy   Kappa      Accuracy SD
##   1                   50      0.6868280  0.6000783  0.008627533
##   1                  100      0.7639891  0.7003776  0.006685263
##   1                  150      0.8230584  0.7757418  0.004640756
##   2                   50      0.8636493  0.8270806  0.007764236
##   2                  100      0.9406899  0.9249259  0.002451006
##   2                  150      0.9669538  0.9581717  0.003039476
##   3                   50      0.9223653  0.9016885  0.004176202
##   3                  100      0.9731035  0.9659636  0.002631791
##   3                  150      0.9891863  0.9863175  0.001657174
##   Kappa SD   
##   0.011428650
##   0.008487593
##   0.005815852
##   0.009858750
##   0.003087847
##   0.003844390
##   0.005252369
##   0.003327226
##   0.002098627
## 
## Tuning parameter 'shrinkage' was held constant at a value of 0.1
## 
## Tuning parameter 'n.minobsinnode' was held constant at a value of 10
## Accuracy was used to select the optimal model using  the largest value.
## The final values used for the model were n.trees = 150,
##  interaction.depth = 3, shrinkage = 0.1 and n.minobsinnode = 10.
```

Estimated error is 98.9% and the estimated out of sample error is therefore 1.1%.

##Prediction using the Model

```r
answers <- predict(boosting, newdata = testing)
answers
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```
From the above, I was able to use the model in order to predict on the test set. 


```r
plot(boosting)
```

![](report_files/figure-html/unnamed-chunk-4-1.png) 
