## Available Parking Spot detection

This repository contains the solution for determining available spots in parking lots from captured images

### Executive Summary

* Traffic arising from automobiles searching for vacant parking spaces is prominent with increasing vehicle 
size and confined parking spaces in urban cities and the problem of traffic congestion has been growing exponentially.
* Drivers also spend a signiﬁcant amount of time looking for parking, overpay for what they use, 
and pay a high amount of parking ﬁnes.
* Searching for a parking space is a routine (and often frustrating) activity for many people 
and this search burns about one million barrels of the world's oil every day and is also a cause of air pollution. 
As the global population continues to urbanize, without a well-planned, road and parking infrastructure this problem will worsen.
* Smart Parking systems typically obtain information about available parking spaces in a particular geographic area and
process in real-time to place vehicles at available parking spots. 
This solution uses Computer Vision and Image Processing to identify available parking spaces from parking lot camera images.


## Problem at hand
As a part of Smart Parking system, the problem is to identify available parking spots in the parking areas given bird’s eye 
view images of the parking lots captured by the cameras using Computer Vision and Image Processing. 

## Solution
* This solution treats the problem as an instance segmentation problem to identify cars from an image of a parking lot 
and infer empty parking spots in the image.
* This system uses deep learning model Mask R-CNN which is pre-trained on MS-COCO dataset and is used for identification 
of cars and subsequently, parking spots in the input images. 
* The first part of the problem is to identify all parking spaces in a parking lot from the camera images.
* The second part works on detecting empty parking spaces from a new image input to the system using locations 
of parking spaces identified in the previous phase.

![](/images/solution_methodology.PNG)


### Training data
 The instance segmentation model used in this solution is Mask R-CNN detector,pre-trained on MS-COCO dataset. 
 This dataset is designed for the detection and segmentation of objects occurring in their natural context and has more than 200K labelled images. This dataset has more than 80 object categories including capability to identify car in an image. 
 
 
Data used in this solution is [CNR Car parking dataset](http://www.cnrpark.it/) (CNR-EXT_FULL_IMAGE_1000x750) for solution demonstration.
This dataset is for visual occupancy of car parking lots and contains images captured in 3 different weather conditions : rainy, overcast & sunny. This variant of the dataset contains full frames (complete view of the parking lot) similar to the kind of input for the problem statement at hand.

Resolution – 1000x750 

### Parking spaces identification and car object identification with Mask R-CNN model - 
In this solution, Mask R-CNN model is used for identifying cars in the images. 
This model is designed for object instance segmentation and is an extension to Faster R-CNN model. 
For an image of a parking area, the model returns 4 things - 
* The bounding boxes of each object identified, as X,Y pixel coordinates.
* The class label for the object, one of 80 categories as supported by COCO dataset.
* Confidence score of object detection. Higher value denotes more certainty of object’s correct identification. 
* A bitmap mask telling which pixels within the bounding box belong to the object and which ones do not. This result is filtered to obtain this information for objects identified as ‘car’. 


### Flow of the system
* An image of completely occupied parking area is first input to this system. 
* The inference step from object instance identification returns bounding boxes of all the cars. 
     The location of each car is treated as a valid parking spot and bounding box of all of these spots are     
       maintained in the system. The coordinate locations of these  boxes are only needed to be stored for 
       the first time an image from a new parking lot is input to the system. These locations are used for 
       reference for subsequent images passed as input to determine occupancy of parking spots. 
* For any subsequent image, after the inference step, bounding box for cars in the image are obtained. 
     This time, the image will contain bounding boxes wherever car objects have been identified. 
* To determine empty parking spots, we use a measure IOU (Intersection over Union). 
  	
	<a href="https://www.codecogs.com/eqnedit.php?latex=IOU=\frac{\text{Amount&space;of&space;pixels&space;where&space;2&space;objects&space;overlap}}{\text{Amount&space;of&space;pixels&space;covered&space;by&space;both&space;objects}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?IOU=\frac{\text{Amount&space;of&space;pixels&space;where&space;2&space;objects&space;overlap}}{\text{Amount&space;of&space;pixels&space;covered&space;by&space;both&space;objects}}" title="IOU=\frac{\text{Amount of pixels where 2 objects overlap}}{\text{Amount of pixels covered by both objects}}" /></a>
	
    or 
    
    <a href="https://www.codecogs.com/eqnedit.php?latex=IOU=\frac{\text{Intersection&space;of&space;boxes}}{\text{union&space;of&space;boxes}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?IOU=\frac{\text{Intersection&space;of&space;boxes}}{\text{union&space;of&space;boxes}}" title="IOU=\frac{\text{Intersection of boxes}}{\text{union of boxes}}" /></a>
    
    
   This gives us a measure of overlap of a car’s bounding box with the parking spot’s bounding box. 
   A value greater than a threshold ( threshold here 0.5 ) denotes that the spot is occupied, otherwise vacant. 
   For any new image of a parking area with few spots vacant, the image after the instance detection stage    
   will only have bounding boxes at locations where cars were identified and hence, IOU value for those   
   locations will be > 0.5 (denoting occupied) and 0 (denoting empty) for the rest of the locations in the  
   reference coordinate locations. 
   
* To depict the output, all the bounding boxes and their centre locations from the reference     
     parking spot locations are overlayed on the current input image. 
    The spots where cars were identified are displayed in red colour bounding box and centre points 
    and green colour for the rest of the spots. 


### Approach

#### 1. To find empty/available parking space from the input images of parking area, we divide the problem statement into two parts-

* To obtain locations of parking spots in the images i.e which areas of the image correspond to parking  spots. These locations will be used for subsequent images in the 3rd step. 
* For new input images, identify car instances and their locations in the parking lot and determine the occupancy using the reference locations as described in previous sections.

 Advantages – 
This eliminates any dependency of the model on a human to provide hard coded locations of parking spots.
Enables the system to identify empty parking spots for new parking lots as well with minimal requirement of a single image of its completely occupied parking area without any compromise on its performance. 

Treating bounding boxes of parked cars as valid parking spot is more reliable and easier to detect than detecting parking meters or vehicle boundaries in the parking spaces as neither of them are always visible and may also be confused with noise by the model and hence detecting such objects do not promise reliable results every time for different areas. 


### References 
* [Snagging Parking Spaces with Mask R-CNN and Python](https://medium.com/@ageitgey/snagging-parking-spaces-with-mask-r-cnn-and-python-955f2231c400)

