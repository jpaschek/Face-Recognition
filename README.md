# Face-Recognition

library(RColorBrewer)
showMatrix<-function(x,...)
image(t(x[nrow(x):1,]),xaxt='none',yaxt='none',
col=rev(colorRampPalette(brewer.pal(7,'Greys'))(100)),...)

faces<-read.table("E:/faces.txt")
x<-as.matrix(faces)

###############################################################
PCA
###############################################################
xc<-apply(x,2,function(i) i-mean(i)) 
A<-t(xc)/sqrt(400-1)   #4096x400
#thus, the covariance matrix is A%*%t(A)

A.egn<-eigen(t(A)%*%A)   #400x400
pc<-A%*%A.egn$vectors
pc<-apply(pc,2,function(i) i/sqrt(sum(i*i)))  #normalize the pc
n<-80
sum(A.egn$value[1:n])/sum(A.egn$value)

#we use the first 80 pcs

pcs<-pc[,1:n] 
#pc scores
y<-xc%*%pcs
mu<-apply(x,2,mean)

#reconstruct the image
xr<-y%*%t(pcs)+matrix(mu,nrow=400,ncol=4096,byrow=TRUE)
par(mfrow = c(4,5), mar = c(1, 0, 0, 0), bty = 'n')
for(i in 1:10){
showMatrix(matrix(xr[10*i,],64,64))
showMatrix(matrix(x[10*i,],64,64))
}


