
### Global registration implementation in Open3D

Contents:
1. Introduction
2. Basics
	1. What are point clouds
	2. What is global registration
	3. What is the data used
3. Preprocessing
4. Models
	1. RANSAC
	2. FGR
5. Experiments
6. Results
7. Outcomes

# Introduction

When studying the topics of introductory signal processing classes, one of the topics that I found most interesting was Template Matching. In the very simplest case, it is taking some sub-image and locating it within the full image. At first it may seem fairly limited in it’s usefulness, but tasks like optical character recognition (OCR), locating anatomical structures in x-ray (or other kinds of) medical imaging, or quality control in a manufacturing process benefit greatly from the implementation of these tools. 
The actual tools range from simple, when the sub-image perfectly matches the object in the full image, to very complex, when the scale, rotation color etc. are different between the queried sub-image and its instance in the full image. 
My project takes the idea of what template matching is in 2D and applies it to 3D point cloud scenes. This area is also very well researched and many methods have been developed for the 3 dimensional case of this task. The application use case I imagined is tracking an object throughout a manufacturing process. In this case I assume we have the scan of a scene and a 3d model of the object we want to "query". In this case the query is no longer an image, but rather a 3D model and the full image is now a full point cloud scene. This method is commonly known as Registration.

# Basics

The first thing to do was to start with the basics. What is a point cloud? What is Global/Local Registration? What is the common pipeline of processing point cloud data? These were all questions that I have not previously encountered in the signal processing courses, so I decided that these should be included here as well.

### What is a point cloud

The suggestive name of this format should already help to imagine what might the representation look like. A point cloud (often shortened to pcd) is a 3D representation coming from a sensor that is able to encode the distance information of a given object. These sensors include lidar, sonar, ToF and others. In general, the sensors send some form of signal in a direction, wait for it to reflect and hit the sensor again. The time is then measured and combined with the direction, it positions a point in space. 
This is done for many points in many directions at once (depends on the actual sensor) and results in a “cloud” of points, positioned in space in relation to the sensor. 

### What is Global/Local Registration

I actually had trouble when looking for the name of the 3D version of template matching. The confusion was caused by the fact that a large portion of the focus of research in Computer Vision is object recognition, both in images and in point clouds. This task includes looking at the source image and labeling or locating objects based on text queries. In contrast, in the task of registration we have information about the shape properties of the target object we are searching the scene for. 

![[SIV Report 2025-03-29 18.41.07.excalidraw|center]]

Registration is further split into global and local. In local registration, we assume to have a good starting knowledge about the general location of the object we are looking for, therefore only need look in a smaller area. In global registration we do not have any knowledge about the position of the queried object, thus needing to perform the registration search globally. This report focuses on the global case.

![[SIV Report 2025-03-29 21.02.18.excalidraw|center]]

# Preprocessing steps

Once we have the scene and query point clouds, we don't want to start the registration immediately. There are 3 main pre-processing steps that will help the performance of the registration in a major way.

#### 1. Downsampling

Downsampling is the process of systematically reducing the number of points within the point cloud. There exist different methods and 2 of those are implemented in the open3d library. The first one is **uniform down sample** which takes the list of point clouds, and removes every $k^{th}$ point[2]. This operation is very fast, but it can be clearly seen that it lacks much motivation for choosing the points which should be removed in terms of their actual position in space. 
The method for downsampling I chose was the **voxel down sample**. This method is more "spatially" motivated, as it essentially splits the point cloud space into a voxel grid, if one voxel contains more than one points, it calculates the average point [3]. This method resembles resolution dimension in 2d images, with the difference that instead of pixels we use voxels (3d equivalent of pixels - **vo**lumetric **pixel**s)
![[SIV Report 2025-04-02 14.00.08.excalidraw|center]]

   >[!question] Why downsample?
   >Point clouds can be very dense data structures, with hundreds of thousands of points. The high complexity can quickly become unwieldy in subsequent computations. In our case, it could also be the case that the overly detailed point cloud would lead to a worse registration result [cite exact point cloud downsampling]
   
   
#### 2. Estimating normals

The following step is estimating the normals. As mentioned above, point clouds do not have explicit information about the surface of the scanned object. We can still extract some information about surfaces by estimating the normal to a given point. The normal can clearly not be computed given only a single point, so instead we select a point and use the neighborhood of points to estimate the normal. The neighborhood is typically calculated using  a simple radius or various forms of K-nearest neighbors. [4]

![[SIV Report 2025-04-05 10.37.23.excalidraw| center]]

#### 3. Extracting features

Extracting the features is one of the most important steps, as it is these features that will be used to find matches. The state of the art features that are being used are the **Fast Point Feature Histograms** (**FPFH**). The FPFH is a faster version of the PFH algorithm, which uses local information about the 3 different local geometric properties, related to the angles between points, normals, relative position etc. The 3 groups of values are split into 11 bins each to create 3 different histograms. These histogram values are what is used to map each point into the 33-dimensional feature space ($histograms*bins=3*11=33$) [5]. The exact composition of the properties and the FPFH algorithm itself is outside of both the scope and focus of this paper, but more information can be found in references.
The main takeaway is that mapping into the feature space is done with the goal of being able to compare points between point clouds in a space, where closer points should have more similar local geometric properties.

![[SIV Report 2025-04-06 20.48.09.excalidraw|center]]

# RANSAC Global Registration

**RANSAC**, or **Ran**dom **Sa**mple **C**onsensus, is a general method for learning data with outliers. As the "Random" suggests, it is a non-deterministic method. The general idea is to take a random sample among the data, fit based solely on this sample and examine the quality of this fit based on the entire data. All points which are deemed as "well fitted" are marked as inliers. This process is repeated and the fit which has the highest number of inliers is selected. [7]
In the point cloud case, we choose a random sample of points, look up their closest neighbors in the FPFH space (where similar points should exist nearby). The algorithm then takes these pairs of sampled points and their closest FPFH neighbors, and tries to find a transformation which would minimize their distance in the XYZ space. Afterwards, all points are considered and to check if their nearest neighbor in the XYZ space is also near in the FPFH space. If they are, we mark them as inliers and count them. This process of randomly sampling and evaluation is repeated many times, with some clever pruning tricks to focus only on promising matches. The output of the algorithm is the transformation which lead to the highest number of inliers. [6] 

![[SIV Report 2025-04-05 11.12.57.excalidraw|center]]

So why is this method particularly useful in point cloud registration? It is essentially looking for a fit that matches for the highest number of points. It is very likely that the 2 point clouds are not exactly equal, nor have the same number of points. Since we are only matching based on a subsample and allow for outliers in the result, the RANSAC algorithm is a robust way to locate point clouds with similar shape. 

>[!pros] Pros of RANSAC Global Registration
>- Robust
>- Able to register partially overlapping point clouds
>- Works globally

>[!cons] Cons of RANSAC global registration
>- Requires many iterations
>- Slow

# Fast Global Registration

**Fast Global Registration**, or FGR, is a method within the open3d library. It tries to improve on the RANSAC method by eliminating the need for many iterations of subsampling. As a result the algorithm is much faster, enabling on-line application. In simplified terms it does so by defining a single objective function to be optimized over the 2 point clouds. It also looks for corresponding points within the 33 dimensional FPFH space. The objective function is optimized using the graduated non-convexity strategy. This allows FGR to ignore the possible outliers between clouds in the early steps while still achieving very close match in the end.
Some of the shortcomings of this method are that it is crucial to select the correct parameters (specifically the voxel size for downsampling) for it to work properly. Overall FGR can be **much** faster and also more precise than RANSAC, but there are some drawbacks in terms of reliability, especially in less overlapping point clouds. Since it only does one iteration of the maximization, it has some risk of getting into a local minimum. More information about the actual workings can be found in the original paper. [10]

(add fgr figure)

>[!pros] Pros of FGR
>- Much faster than RANSAC
>- With good parameters can give better result than even local alignment

>[!cons] cons of FGR
>- Parameters need to be picked well
>- Less robust


# Experiments

I tested the models using 2 different experiments. The task in each of the experiments was to align the target and source point clouds. The first experiment focuses on aligning 2 identical point clouds with high resolution and a relatively good initial alignment. The second experiment performs registration in a scene with multiple objects, where the target and source point clouds have very different distribution of points (simulates scans coming from different sensor types).

## Experiment 1 - complex point clouds, no outliers

The point cloud for this experiment is taken from the open3d library. It is a relatively high resolution scan of a small scene. In this case the match is done on the identical point cloud, which has been randomly rotated, but not translated. This experiment **highlights the ability of the model to align two scans which are very similar, but slightly disturbed**.

(add some visualization of the point cloud)

## Experiment 2 - simple point clouds, with outliers

The dataset for this experiment was created using the open-source 3d modelling software Blender [8] (specifically version 3.3) and a plugin which allows to simulate various scanner types within Blender called BlAInder range scanner, developed by Stefan Reitmann, Lorenzo Neumann and Bernhard Jung [9]. The experiment runs a script which places 3 simple distinct objects at random locations on a 5x5 grid, and then exports the scanned scene. The objects in the scanned scenes are incomplete, as the scanner point of view does not enable to scan all sides of the objects. The point cloud which is used as the query for the registration, is generated using a 3d object model and the sample_points_poisson_disk method of open3d. This experiment is meant to **simulate a use case, where we want to register a real world scanned object based on its computer model**.
This experiment contains inherent outliers in the form of additional objects. Furthermore, the different method of acquisition of the point clouds means that the points are distributed very differently across the objects. This could simulate registration of objects which were scanned by a different scanner in a environment with multiple objects. It should highlight the robustness of the models.

(add visualization of how different the point clouds are)

## Evaluation

As for evaluating the results of the experiment, 2 metrics will be used. First is the **Fitness**, which is the internal metric used by the o3d models to determine how good a fit was found. It is a measure of the calculated portion of points which are determined as inliers. 
The other metric is the **Root Mean Square Error** or **RMSE**. This is a fairly typical metric used in many tasks. In the point cloud case it works by for each point in cloud A it finds the closest point in cloud B and calculates their distance. All of these distances are then summed and normalized by the number of points. Lastly we take the square root to give approximate "actual" distance. 
The last metric of interest is **time**. The actual computation time of each of the different models is very relevant to their applications. It should be said however that measuring computation time is not always very accurate, still it gives an important insight. 

# Results

To evaluate the models, I compared their performance between the 2 experiments on the 3 metrics mentioned above.

## Fitness

The fitness is not always the most insightful metric. It conveys the belief of the model as to how good a fit it has found. Comparing them between different samples is not always useful, since having any amount of outliers will cause an inherent decrease in the fitness measure. 
In the no outlier case, both of the models have achieved a fitness of 1, meaning that both models found what they perceive as a perfect fit. 
In the experiment with outliers, the RANSAC registration was again able to saturate around the fitness level of $0.41$(which is likely the ratio of correct inliers to correct outliers). The FGR mean was close enough at $0.40$, however it is visibly less stable.

![[Pasted image 20250409100856.png]]

![[Pasted image 20250409100909.png]]

## RMSE

The RMSE is in some way a "more strict" measure, as it uses the transformation learned on the downscaled model, but uses it to evaluate the distance between the full resolution clouds. Between the 2 experiments the outcomes were quite different. 
In the no-outliers FGR outperformed RANSAC in all examples (note that for RMSE lower is better).
As for the outliers case, the opposite outcome happened. RANSAC was able to outperform FGR in all but few cases, by similar relative margin. 

![[Pasted image 20250409101017.png]]
![[Pasted image 20250409100935.png]]

## Time

As for the time required of both of these algorithms, FGR clearly outperforms RANSAC in the vast majority of cases, both in experiment 1 and 2. FGR was especially fast in the second experiment, which contains much simpler point clouds. Interestingly enough, the more complex first experiment actually made the RANSAC algorithm faster. 

![[Pasted image 20250409101034.png]]
![[Pasted image 20250409101045.png]]

# Conclusion

The experiments do well at highlighting the individual benefits and shortcomings of each of the models. FGR being the faster and in low-outlier case more accurate, while RANSAC being very robust, at the cost of computation time and lower accuracy. In the case with high amounts of outliers the robustness of RANSAC comes out and it is able to outperform FGR in terms of accuracy, but not time. 
It should be said that in my experimentation FGR was very sensitive to any changes in parameters (for example voxel size in downsampling) and when the parameters were set wrong, the optimization fell in a local minimum and the registration was very poor.

Overall in this process I learned the general pipeline for processing point clouds for registration, along with the various functions of open3d. There are definitely more advanced models to be evaluated, but both RANSAC and FGR were important points in global registration and served as a good introduction into the field. 


# References

[1]

K. Koide, S. Oishi, M. Yokozuka, and A. Banno, “Exact Point Cloud Downsampling for Fast and Accurate Global Trajectory Optimization,” Dec. 25, 2023, _arXiv_: arXiv:2307.02948. doi: [10.48550/arXiv.2307.02948](https://doi.org/10.48550/arXiv.2307.02948).

[2]

“open3d.geometry — Open3D 0.6.0 documentation.” Accessed: Apr. 01, 2025. [Online]. Available: [https://www.open3d.org/docs/0.6.0/python_api/open3d.geometry.html](https://www.open3d.org/docs/0.6.0/python_api/open3d.geometry.html)

[3]

“Point cloud — Open3D 0.8.0 documentation.” Accessed: Apr. 01, 2025. [Online]. Available: [https://www.open3d.org/docs/0.8.0/tutorial/Basic/pointcloud.html](https://www.open3d.org/docs/0.8.0/tutorial/Basic/pointcloud.html)

[4]

“open3d.geometry.estimate_normals — Open3D 0.6.0 documentation.” Accessed: Apr. 01, 2025. [Online]. Available: [https://www.open3d.org/docs/0.6.0/python_api/open3d.geometry.estimate_normals.html](https://www.open3d.org/docs/0.6.0/python_api/open3d.geometry.estimate_normals.html)

[5]

R. B. Rusu, N. Blodow, and M. Beetz, “Fast Point Feature Histograms (FPFH) for 3D registration,” in _2009 IEEE International Conference on Robotics and Automation_, May 2009, pp. 3212–3217. doi: [10.1109/ROBOT.2009.5152473](https://doi.org/10.1109/ROBOT.2009.5152473).

[6]

“Global registration - Open3D 0.19.0 documentation.” Accessed: Apr. 02, 2025. [Online]. Available: [https://www.open3d.org/docs/release/tutorial/pipelines/global_registration.html](https://www.open3d.org/docs/release/tutorial/pipelines/global_registration.html)

[7]

“Random sample consensus,” _Wikipedia_. Nov. 22, 2024. Accessed: Apr. 04, 2025. [Online]. Available: [https://en.wikipedia.org/w/index.php?title=Random_sample_consensus&oldid=1258988090](https://en.wikipedia.org/w/index.php?title=Random_sample_consensus&oldid=1258988090)

[8]

B. Foundation, “blender.org - Home of the Blender project - Free and Open 3D Creation Software,” blender.org. Accessed: Apr. 05, 2025. [Online]. Available: [https://www.blender.org/](https://www.blender.org/)

[9]

ln-12, _ln-12/blainder-range-scanner_. (Mar. 31, 2025). Python. Accessed: Apr. 05, 2025. [Online]. Available: [https://github.com/ln-12/blainder-range-scanner](https://github.com/ln-12/blainder-range-scanner)

[10]

Q.-Y. Zhou, J. Park, and V. Koltun, “Fast Global Registration,” in _Computer Vision – ECCV 2016_, vol. 9906, B. Leibe, J. Matas, N. Sebe, and M. Welling, Eds., in Lecture Notes in Computer Science, vol. 9906. , Cham: Springer International Publishing, 2016, pp. 766–782. doi: [10.1007/978-3-319-46475-6_47](https://doi.org/10.1007/978-3-319-46475-6_47).