# Face-Recognition
#converts colorpictures into graysclae pictures

#necessary packages
install.packages("jpeg")
install.packages("RColorBrewer")
library(jpeg)
library(RColorBrewer)

#path to the images which should be converted
x<-readJPEG("C:/Users.../01.jpg")
dim(x)
res<-dim(x)[1:2]
plot(1,1,xlim=c(1,res[1]),ylim=c(1,res[2]),asp=1,type='n',xaxs='i',yaxs='i',xaxt='n',yaxt='n',xlab='',ylab='',bty='n')
rasterImage(x,1,1,res[1],res[2])

#creating the pixel matrix
y<-x[,,1]*0.3+x[,,2]*0.59+x[,,3]*0.11
showMatrix <- function(x, ...) image(t(x[nrow(x):1,]), xaxt = 'none', yaxt = 'none', col = rev(colorRampPalette(brewer.pal(7, 'Greys'))(100)), ...)
showMatrix(y)

#name the picture
writeJPEG(y,"1g.jpg",quality=1)
