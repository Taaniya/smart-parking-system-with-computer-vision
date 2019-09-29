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
  	
	<img src="https://latex.codecogs.com/svg.latex?\Large&space;IOU=\frac{\text{Amount of pixels where 2 objects overlap}}{\text{Amount of pixels covered by both objects}}" />
	
	IOU=$\frac{\text{Amount of pixels where 2 objects overlap}}{\text{Amount of pixels covered by both objects}}$
		
    or 
        
	
    IOU = $\frac{\text{Intersection of boxes}}{\text{union of boxes}}$
    
    
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




### References 
* [Snagging Parking Spaces with Mask R-CNN and Python](https://medium.com/@ageitgey/snagging-parking-spaces-with-mask-r-cnn-and-python-955f2231c400)

