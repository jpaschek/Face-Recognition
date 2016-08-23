
[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="880" alt="Visit QuantNet">](http://quantlet.de/index.php?p=info)

## [<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **FaceRecognition_FLDA** [<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/d3/ia)

```yaml

Name of Quantlet : FaceRecognition_FLDA

Published in : unpublished

Description : 'Classifies if a greyscale picture belongs to a particular person by using Fisher
Linear Discrimination Analysis as classification algorithm.'

Keywords : 'Fisher-LDA-projection, classification, Fisher, Fisher-Linear-Discriminant-Analysis,
FLDA, Face-Recognition'

Author : Xiaofei Xu, Janice Paschek

Submitted : Sunday, August 21 2016 by Janice Paschek

Datafile : newfaces1.txt

Input : 'Datafile consists of 400 pictures belonging to 10 persons. The dimension for the images
should be reduced before.'

```


### R Code:
```r
# required packages
library(class)
library(mclust)
library(MASS)
library(kernlab)

# read in the data after doing dimension reduction
faces1 = read.table("newfaces1.txt")

# split the data in test and trainings set (first nine pictures = train, last picture = test)
id = rep(1:40, each = 10)
faces.data.frame = data.frame(cbind(id = id, faces1))

testid = seq(10, 400, by = 10)
train.faces = faces.data.frame[-testid, ]
test.faces = faces.data.frame[testid, ]


# FLDA model
flda = lda(id ~ ., train.faces)
# prediction
pl = predict(flda, test.faces)$class

# accuracy measurement
table(pl)
diag(table(pl, id[testid]))
classError(pl, id[testid])
# calculation of running time
ptm = proc.time()
pl = predict(flda, test.faces)$class
proc.time() - ptm 

```
