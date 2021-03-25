# **Finding Lane Lines on the Road** 

---

### Reflection

### 1. Pipeline

Pipeline was created as a combination of predefined methods carefully tuned to accomodate the input image set
```python
def detect_lines(image):
    shape = image.shape
    x_size = image.shape[1]
    y_size = image.shape[0]
    gray_img = grayscale(image)
    blur_img = gaussian_blur(gray_img, 3)
    edges_img = canny(blur_img, 50, 150)
    masked_img = region_of_interest(edges_img, np.array([[(10, y_size),(x_size/2, y_size/2 + 25),(x_size - 50, y_size),(x_size, y_size)]], dtype=np.int32))
    line_img = hough_lines(masked_img, 2, np.pi/180, 15, 30, 150)
    combined_img = weighted_img(line_img, image)
    return combined_img
```
Pipeline defines the area masking as the proportion of the image leaning to a lower part of the image.

For other parts of the pipeline the most default values from the course lessons were the most efficient.

### 2. Lines and their allocation

#### Line filtering
```python
def draw_lines(img, lines, color=[255, 0, 0], thickness=3):

    for line in lines:
        
        
        for x1,y1,x2,y2 in line:
                 
            # Detect the slope type positive or negative value
            slope = ((y2-y1)/(x2 - x1 + 1))
            
            # If slopes too vertical or horizontal, ignore them and don't include into the results
            if (slope > 0 and slope < 0.5) or (slope <= 0 and slope > -0.5) or (slope > 0 and slope > 0.95) or (slope <= 0 and slope < -0.85):
                continue
            

```
Detecting the slope allows to filter out the lines which are crossing the area not in the right direction and by that may occur as the totally wrong calculations for the algorithm, but basically we can allow lines between some thresholds depending on the type of the line

#### Line extensions

My selection was the algorithm based on geometry calculations to find the next best spot for the straight line trajectory

#### Output

Due to the heavy load my day-job I wasn't been able to dedicate enough time to come up with all possible best solutions to draw the perfect lines, and especially if those lines would need to go into the curve on the road. However one of the approach I took was the averaging the line coordinated per slope, drawing the line and extending that average line of good spots into the both directions.

Main problem are the road curves which will require different and more smart algorithm to draw the lines binding to actual whole length of the required trajectory



### 3. Possible improvements

A possible improvement would be to detect more lines, filter them more accurately and extend only lines which are closer to the car while leaving Hough lines be for the part of the road which is farther away from the car, that will allow lines to form better representation of road curves
