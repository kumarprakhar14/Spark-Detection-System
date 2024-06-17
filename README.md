# Spark Detection System in Irom Rolling Mill

## Overview

This script performs motion detection in a video file (or webcam feed) using OpenCV. It detects significant changes between consecutive frames and highlights areas of motion with bounding rectangles.

## Prerequisites

- OpenCV library
- NumPy library
- Google Colab (for `cv2_imshow`)

## Usage

1. **Setup**:
   - Ensure you have OpenCV and NumPy installed.
   - If running in Google Colab, make sure to use the provided `cv2_imshow` from `google.colab.patches`.

2. **Running the Script**:
   - Replace the video file path (`'/content/Spark_Detection.mp4'`) with your video file or use `0` for the webcam feed.
   - Execute the script to see the motion detection in action.

## Code Explanation

### Importing Libraries

```python
import cv2
import numpy as np
from google.colab.patches import cv2_imshow
```

- `cv2`: OpenCV library for video processing.
- `np`: NumPy library for numerical operations.
- `cv2_imshow`: Utility to display images in Google Colab.

### Main Function

```python
def main():
    cap = cv2.VideoCapture('/content/Spark_Detection.mp4')  # Or use 0 for webcam
```
- `cv2.VideoCapture`: Initializes video capture from a file or webcam.

### Initialize Variables for Motion Detection

```python
    ret, frame1 = cap.read()
    if not ret:
        print("Error reading video file")
        return

    gray1 = cv2.cvtColor(frame1, cv2.COLOR_BGR2GRAY)
    gray1 = cv2.GaussianBlur(gray1, (15, 15), 0)
```
- Reads the first frame and converts it to grayscale.
- Applies Gaussian blur to reduce noise.

### Processing Video Frames

```python
    while cap.isOpened():
        ret, frame2 = cap.read()
        if not ret:
            break

        gray2 = cv2.cvtColor(frame2, cv2.COLOR_BGR2GRAY)
        gray2 = cv2.GaussianBlur(gray2, (15, 15), 0)
```
- Continuously reads and processes each frame until the video ends or an error occurs.
- Converts each frame to grayscale and applies Gaussian blur.

### Compute Frame Difference

```python
        frame_diff = cv2.absdiff(gray1, gray2)
```
- Computes the absolute difference between the current frame and the previous frame.

### Thresholding

```python
        _, motion_mask = cv2.threshold(frame_diff, 25, 255, cv2.THRESH_BINARY)
```
- Applies a binary threshold to the difference image to highlight significant changes.

### Morphological Operations

```python
        kernel = np.ones((5, 5), np.uint8)
        motion_mask = cv2.morphologyEx(motion_mask, cv2.MORPH_CLOSE, kernel)
        motion_mask = cv2.morphologyEx(motion_mask, cv2.MORPH_OPEN, kernel)
```
- Enhances the motion mask using morphological operations (close and open) to remove noise.

### Contour Detection and Drawing Rectangles

```python
        contours, _ = cv2.findContours(motion_mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

        for contour in contours:
            if cv2.contourArea(contour) > 100:  # Adjust area threshold as needed
                x, y, w, h = cv2.boundingRect(contour)
                cv2.rectangle(frame2, (x, y), (x + w, y + h), (0, 0, 255), 2)
```
- Finds contours in the motion mask.
- Draws rectangles around the detected motion areas if they exceed a certain area threshold.

### Display Frame and Update Previous Frame

```python
        cv2_imshow(frame2)
        gray1 = gray2
```
- Displays the frame with detected motion.
- Updates the previous frame to the current frame for the next iteration.

### Exit Condition and Resource Cleanup

```python
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()
```
- Breaks the loop if 'q' is pressed.
- Releases video capture and destroys all OpenCV windows.

### Entry Point

```python
if __name__ == "__main__":
    main()
```
- Ensures the `main` function is called when the script is executed.

## Notes

- Adjust the threshold value in `cv2.threshold` and the contour area threshold in `cv2.contourArea` as needed for your specific use case.
- Morphological kernel size can also be adjusted to better suit your needs.

---

## Result Discussion

## References

This documentation should help users understand and utilize the script effectively.
