

### Feature based tracking

 - select a feature point to use for tracking of an object
 - The point should be salient = provides good information about the motion of the object
	 - An example woul be th corner point of an object
 -![[04 Tracking.pdf#page=23&rect=533,183,708,223|04 Tracking, p.23|200]]
  - what are goo features? example characteristics:
	  - Brightness
	  - Contrast
	  - Texture
	  - Edges
	  - **Corners**
	  - Points with high curvature
	
	calculate:
	![[04 Tracking.pdf#page=25&rect=292,288,494,398|04 Tracking, p.25|200]]
	Where Jx and Jy are derivatives evaluated in the window W
	- Good feature -> smallest eigenvalue of Z bigger than some threshold 
	- The eigenvalues should be larger than the noise in the image (should not get overpowered by noise)
	- We use the smallest eigenvalue to ensure the feature is detectable in movement in both x and y directions
	- (large eigenvalues imply the contrast around the point is large)
	
	#### How to track?
	 - need to make sure we track the same points throughout the frames
	 In The ideal case:
	![[04 Tracking.pdf#page=27&rect=78,72,683,348|04 Tracking, p.27|400]]
	
> [!Problem] Problem
> The function is unlikely to hold due to more complex transformations

Instead can use: 

![[04 Tracking.pdf#page=28&rect=80,137,771,262|04 Tracking, p.28]]
Epsilon = measure of dissimilarity
- if the dissimilarity grows too large, discard the feature point
- Works better in high frame-rate videos (due to the window)

#### Bayesian tracking
[!idea] Idea
> 
	
	
	



![[04 Tracking.pdf]]