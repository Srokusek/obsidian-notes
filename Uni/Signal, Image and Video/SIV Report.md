
### Global registration implementation in Open3D

Contents:
1. Introduction
2. Basics
	1. What are point clouds
	2. What is global registration
	3. What is the data used
3. Models
	1. RANSAC
	2. FGR
	3. +ICP
4. Performance
5. Learned outcomes

# Introduction

When studying the topics of introductory computer vision classes, one of the topics that I found most interesting was Template Matching. In the very simplest case, it is taking some sub-image and locating it within the full image. At first it may seem fairly limited in it’s usefulness, but tasks like optical character recognition (OCR), locating anatomical structures in x-ray (or other kinds of) medical imaging, or quality control in a manufacturing process benefit greatly from the implementation of these tools. 

The actual algorithms tools range from simple, when the sub-image perfectly matches the object in the full image, to very complex, when the scale, rotation color etc. are different between the queried sub-image and its instance in the full image. 

My project takes the idea of what Template Matching is in 2D and applies it to 3D point cloud scenes. This area is also very well researched and many methods have been developed for the 3 dimensional case of this task. The application use case I imagined is quality control in a manufacturing process (for example a furniture manufacturer). To ensure the fabrication runs smoothly, an implementation of an automated quality control would be very useful. In my case I focused on quality control based on the shape of the product. This might be useful when the material used can vary in color, but generally have the same shape. In this case the query is no longer an image, but rather a 3D model (which should in almost all cases be available to the manufacturer) and the full image is now a full point cloud scene. This method is commonly known as Registration.

# Basics

The first thing to do was to start with the basics. What is a point cloud? What is Global/Local Registration? What is the common pipeline of processing point cloud data? These were all questions that I have not previously encountered in the computer vision courses, so I decided that these should be included here as well.

### What is a point cloud

The suggestive name of this format should already help to imagine what might the representation look like. A point cloud (often shortened to pcd) is a 3D representation coming from a sensor that is able to encode the distance information of a given object. These sensors include lidar, sonar, ToF and others. In general, the sensors send some form of signal in a direction, wait for it to reflect and hit the sensor again. The time is then measured and combined with the direction, it positions a point in space. 
This is done for many points in many directions at once (depends on the actual sensor) and results in a “cloud” of points, positioned in space in relation to the sensor. 

### What is Global/Local Registration

I actually had trouble when looking for the name of the 3D version of Template Matching. The confusion was caused by the fact that a large portion of the focus of research in Computer Vision is object recognition, both in images and in point clouds. This task includes looking at the source image and labeling or locating objects based on text queries. In contrast, in the task of Registration we have information about the shape properties of the target object we are searching the scene for. 

![[SIV Report 2025-03-29 18.41.07.excalidraw]]


![[SIV Report 2025-03-29 21.02.18.excalidraw]]

