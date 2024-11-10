# Skewb Solver (Monte Carlo)

## About the Project

This programme allows user to capture all sides of the skewb, and solve it with the algorithm found by using Monte Carlo.

## Getting Started

### Installation

To be able to run the python programme, you need to have numpy, Pillow and OpenCV installed.

* numpy
  ```cmd
  pip install numpy
  ```
  
* Pillow
  ```cmd
  pip install pillow
  ```
* OpenCV
  ```cmd
  pip install opencv-python
  ```

### Preparation
Please put all the files and folders in the same directory.

The colour input is done by python opencv, and the skewb algorithm finder is done in C++ using Monte Carlo. Therefore, you need to first compile the C++ programme.
* Compile the C++ script
  ```cmd
  g++ skewb_solution_finder.cpp -o a
  ```

Then, after you scanned the cube using the python programme, it will run the compiled file.

This programme requires you to connect a webcam to the computer, 

* Change the webcam number in the code in line 8 of skewb_solver.py if necessary.
  ```python
  cap = cv2.VideoCapture(0)
  ```

* If you are using a linux system, change the code as following.
  ```python
  cap = cv2.VideoCapture(0, cv2.CAP_V4L2)
  ```

## Usage
The main code is in **skewb_solver.py**. Run this python script and scan each face of the skewb. When you scan the skewb, you have to first find the white-green-red corner and place it at bottom right with white facing up. **Press enter** to capture a face, then follow the animation in the OpenCV preview window to rotate the cube to scan the next face. Should you have any difficulty scanning the cube, you could take a look at the [demo video](https://law-chun-man.github.io/Skewb_Solver/demo.mp4).

<div class="video-container">
    <video width="640" height="360" controls allowfullscreen>
        <source src="demo.mp4" type="video/mp4">
        Your browser does not support the video tag.
    </video>
</div>

After scanning all faces, the python programme will return the skewb algorithm. The skewb algorithm follows the WCA standard notation. Please refer to quick guide to WCA skewb notation [video](https://law-chun-man.github.io/Skewb_Solver/notation.mp4). 

<div class="video-container">
    <video width="640" height="360" controls allowfullscreen>
        <source src="notation.mp4" type="video/mp4">
        Your browser does not support the video tag.
    </video>
</div>

Or, you could just click the link in the terminal under the skewb algorithm for the animation of showing the algorithm as shown in the [demo video](https://law-chun-man.github.io/Skewb_Solver/demo.mp4).

## More Customisation

Let's say you are right handed, you might want an algorithm that contain less lefty moves. You could actually tune the ratio of the probability of the type of turns that come up in Monte Carlo simulation. 

* Tune the numbers in line 8 of **skewb_solution_finder.cpp**. The numbers in array mean the probability of R showing up to L to U to B respectively.
  ```cpp
  int ratio[4] = {1, 1, 1, 1};
  ```
* If you change the array to {3, 1, 1, 1}, R moves will come up 3 times more frequently than L, U and B moves.
