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


