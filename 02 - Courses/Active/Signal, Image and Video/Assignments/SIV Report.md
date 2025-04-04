
### Global registration implementation in Open3D

Contents:
1. Introduction
2. Basics
	1. What are point clouds
	2. What is global registration
	3. What is the data used
3. Models
	1. RANSAC
	2. FGR (I don't think so)
	3. +ICP (maybe just mention)
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

# Preprocessing steps
#### 1. Downsampling

Downsampling is the process of systematically reducing the number of points within the point cloud. There exist different methods and 2 of those are implemented in the open3d library. The first one is **uniform down sample** which takes the list of point clouds, and removes every $k^{th}$ point[cite o3d]. This operation is very fast, but it can be clearly seen that it lacks much motivation for choosing the points which should be removed in terms of their actual position in space. 
The method for downsampling I chose was the **voxel down sample**. This method is more "spatially" motivated, as it essentially splits the point cloud space into a voxel grid, if one voxel contains more than one points, it calculates the average point [cite 03d voxel down sample]. This method resembles resolution dimension in 2d images, with the difference that instead of pixels we use voxels (3d equivalent of pixels - **vo**lumetric **pixel**s)
![[SIV Report 2025-04-02 14.00.08.excalidraw]]
(add downsample diagram)
	
   >[!question] Why downsample?
   >Point clouds can be very dense data structures, with hundreds of thousands of points. The high complexity can quickly become unwieldy in subsequent computations. In our case, it could also be the case that the overly detailed point cloud would lead to a worse registration result [cite exact point cloud downsampling]
   
   
#### 2. Estimating normals

The following step is estimating the normals. As mentioned above, point clouds do not have explicit information about the surface of the scanned object. We can still extract some information about surfaces by estimating the normal to a given point. The normal can clearly not be computed given only a single point, so instead we select a point and use the neighborhood of points to estimate the normal. The neighborhood is typically calculated using  a simple radius or various forms of K-nearest neighbors. [cite o3d estimate normals]

(add diagram for normal estimation)

#### 3. Extracting features

Extracting the features is one of the most important steps, as it is these features that will be used to find matches. The state of the art features that are being used are the Fast Point Feature Histograms (fpfh). The fpfh is a faster version of the pfh algorithm, which uses local information about the 3 different local geometric properties, related to the angles between points, normals, relative position etc. The 3 groups of values are split into 11 bins each to create 3 different histograms. These histogram values are what is used to map each point into the 33-dimensional feature space ($histograms*bins=3*11=33$) [cite IEEE fpfh paper]. The exact composition of the properties and the fpfh algorithm itself is outside of both the scope and focus of this paper, but more information can be found in references.
The main takeaway is that mapping into the feature space is done with the goal of being able to compare points between point clouds in a space, where closer points should have more similar local geometric properties.

(insert diagram showing that points with similar geometric properties are close to each other in fpfh feature space)

# RANSAC Global Registration

RANSAC, or **Ran**dom **Sa**mple **C**onsensus, is a general method for learning data with outliers. As the "Random" suggests, it is a non-deterministic method. The general idea is to take a random sample among the data, fit based solely on this sample and examine the quality of this fit based on the entire data. All points which are deemed as "well fitted" are marked as inliers. This process is repeated and the fit which has the highest number of inliers is selected. [cite wikipedia]
In the point cloud case, we choose a random sample of points, look up their closest neighbors in the fpfh space (where similar points should exist nearby). The algorithm then takes these pairs of sampled points and their closest fpfh neighbors, and tries to find a transformation which would minimize their distance in the xyz space. Afterwards, all points are considered and to check if their nearest neighbor in the xyz space is also near in the fpfh space. If they are, we mark them as inliers and count them. This process of randomly sampling and evaluation is repeated many times, with some clever pruning tricks to focus only on promising matches. The output of the algorithm is the transformation which lead to the highest number of inliers. [cite  o3d] 
So why is this method particularly useful in point cloud registration? The method is essentially looking for a fit that matches for the highest number of points. It is very likely that the 2 point clouds are not exactly equal, nor have the same number of points. Since we are only matching based on a subsample and allow for outliers in the result, the RANSAC algorithm is a robust way to locate point clouds with similar shape. 

# Experiments


### Bibliography
[1]

K. Koide, S. Oishi, M. Yokozuka, and A. Banno, “Exact Point Cloud Downsampling for Fast and Accurate Global Trajectory Optimization,” Dec. 25, 2023, _arXiv_: arXiv:2307.02948. doi: [10.48550/arXiv.2307.02948](https://doi.org/10.48550/arXiv.2307.02948).