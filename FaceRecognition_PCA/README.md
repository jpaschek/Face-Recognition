
[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="880" alt="Visit QuantNet">](http://quantlet.de/index.php?p=info)

## [<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **FaceRecognition_PCA** [<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/d3/ia)

```yaml

Name of Quantlet : FaceRecognition_PCA

Published in : unpublished

Description : 'Use Principle Component Analysis to project the data into subspace to reduce the
dimension greatly and get the eigenfaces which are corresponding to the eigenvalues of original
dataset.'

Keywords : 'PCA, dimension-reduction, principal-components, principal-component-analysis,
face-recognition'

Author : Xiaofei Xu, Janice Paschek

Submitted : Sunday, August 21 2016 by Janice Paschek

Datafile : faces.txt

Input : 'Datafile consists of 400 pictures belonging to 10 persons, this is the pixel matrix of the
facedata.'

```


### R Code:
```r
# read in the data
faces = read.table("newfaces1.txt")
id <- rep(1:40, each = 10)
faces.data.frame <- data.frame(cbind(id = id, faces))

# data splitting
id <- rep(1:40, each = 10)
testid <- seq(10, 400, by = 10)

train.faces <- faces.data.frame[-testid, ]
test.faces <- faces.data.frame[testid, ]

# since train.faces is 360x4094 matrix, it is not efficient to directly compute the eignvalues and eignvecters
# for a size of 4096*4096 matrix but fortunately,
xc <- scale(train.faces[, -1], scale = FALSE)
A <- t(xc)/sqrt(360 - 1)  # 4096x360
# thus , the covariance matrix is A%*%t(A)
A.egn <- eigen(t(A) %*% A)  # 360x360
# thus, the covariance matrix is A%*%t(A)
pc <- A %*% A.egn$vectors
pc <- apply(pc, 2, function(i) i/sqrt(sum(i * i)))  # normalize the pc
n <- 80
sum(A.egn$value[1:n])/sum(A.egn$value)
# 92.08%
pcs <- pc[, 1:n]
yt <- xc %*% pcs  # pc scores for training data

ft <- data.frame(cbind(id[-testid], yt))  #training set
# test group
xv <- as.matrix(test.faces[, -1])
xvs <- scale(xv, scale = FALSE)
yv <- xvs %*% pcs  # pc scores for testing data
fv <- data.frame(cbind(id[testid], yv))  #test set   

```
