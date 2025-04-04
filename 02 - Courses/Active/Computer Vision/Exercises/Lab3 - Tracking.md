### Feature based tracking

Select a good "feature" that gives reliable information about the movement of the object

How to select? 
	Compute a matrix of derivatives along x, y, xy and yx directions (different for each point)
	Select a point with a high smallest eigenvalue

Typically good features: edges, corners etc.

### Meanshift
