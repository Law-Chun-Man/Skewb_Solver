# Skewb Solver (Monte Carlo)
## 4061 Project B report

## Content

1. [About the project](#1-about-the-project)  
2. [Getting Started](#2-getting-started)  
   i. [Installation](#2-i-installation)  
   ii. [Preparation](#2-ii-preparation)  
3. [Usage](#3-usage)  
4. [More Customisation](#4-more-customisation)  
5. [How this Programme Works](#5-how-this-programme-works)  
   i. [Understanding Mechanism of the Skewb](#5-i-understanding-mechanism-of-the-skewb)  
   ii. [Defining the State of the Skewb](#5-ii-defining-the-state-of-the-skewb)  
   iii. [Defining Turns](#5-iii-defining-turns)  
   iv. [God's Number](#5-iv-gods-number)  
   v. [Monte Carlo](#5-v-monte-carlo)  
   vi. [Colour Recognition](#5-vi-colour-recognition)  
   vii. [Inputting Colour with Python Script](#5-vii-inputting-colour-with-python-script)  
6. [More Statistics](#6-more-statistics)  
   i. [Finding Fewest Moves Solution for Carter](#6-i-finding-fewest-moves-solution-for-carter)  
   ii. [Average Iterations Needed to Find the Solutions and the Average Moves of the Solutions](#6-ii-average-iterations-needed-to-find-the-solutions-and-the-average-moves-of-the-solutions)  
   iii. [Giving Hints to the Programme](#6-iii-giving-hints-to-the-programme)
7. [Future Works](#7-future-works)
8. [Reference](#8-reference)  

---

## 1. About the Project

This programme allows user to capture all sides of the skewb, and solve it with the algorithm found by using Monte Carlo.

## 2. Getting Started

### 2. i. Installation

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

### 2. ii. Preparation
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

## 3. Usage
The main code is in **skewb_solver.py**. Run this python script and scan each face of the skewb. When you scan the skewb, you have to first find the white-green-red corner and place it at bottom right with white facing up. Press enter to capture a face, then follow the animation in the OpenCV preview window to rotate the cube to scan the next face. Should you have any difficulty scanning the cube, you could take a look at the [demo video](https://youtu.be/f4b-0wV-rUE?si=kuiRMmu7Bn71bkcC).


After scanning all faces, the python programme will return the skewb algorithm. The skewb algorithm follows the WCA standard notation. Please refer to quick guide to WCA skewb notation [video](https://youtu.be/V7M_IKuUREs?si=tjRuOQzYD4ZzFDXb).


Or, you could just click the link in the terminal under the skewb algorithm for the animation of showing the algorithm as shown in the [demo video](https://youtu.be/f4b-0wV-rUE?si=kuiRMmu7Bn71bkcC).

## 4. More Customisation

Let's say you are right handed, you might want an algorithm that contain less lefty moves. You could actually tune the ratio of the probability of the type of turns that come up in Monte Carlo simulation. 

* Tune the numbers in line 8 of **skewb_solution_finder.cpp**. The numbers in array mean the probability of R showing up to L to U to B respectively.
  ```cpp
  int ratio[4] = {1, 1, 1, 1};
  ```
* If you change the array to {3, 1, 1, 1}, R moves will come up 3 times more frequently than L, U and B moves.

## 5. How this Programme Works

In the **skewb_solution_finder.cpp**, I defined the state of the skewb using 3 arrays (will be explained later). When you compiled and run this script, you need to find the white-green-red corner (I will also explain why later), and place in front, with white on top as shown below, then input the colours one by one in the order shown below (note that if you use run **skewb_solver.py** and scan the cube, it will automatically input the colours for you).

* Input w for white, g for green, r for red, b for blue, o for orange, y for yellow.

|Front|Back|
|:---:|:---:|
|![order_front](README_picture/order_front.jpeg)|![order_back](README_picture/order_back.jpeg)|

Then there is a function that converts the colours to the 3 arrays that define the skewb's state.

### 5. i. Understanding Mechanism of the Skewb

|Centres|Attached corners|Floating corners|
|:---:|:---:|:---:|
|![centres](README_picture/centres.png)|![attached_corners](README_picture/attached_corners.png)|![floating_corners](README_picture/floating_corners.png)|

As shown in the figures, centre is the center piece with only one colour. Corner on the other hand contains 3 colours, and they are categorised into 2, attached corners, and floating corners. When you disassemble the cube, you will find that the attached corners are connected to the core piece, and their relative positions cannot change. Floating corners of the other hand, do not connect to the core, they can move around.

### 5. ii. Defining the State of the Skewb

Now that you understand the basic mechanism of a skewb, we can now try to understand how to define the state of a skewb.

In fact, there are only 8 different kinds of individual turns of a skewb needed to attain any state of the skewb. In other words, only 8 different kinds of turns are needed to solve a skewb of any state. You could refer to quick guide to WCA skewb notation [video](https://youtu.be/V7M_IKuUREs?si=tjRuOQzYD4ZzFDXb) to learn the 8 different moves used in this programme.

Coincidentally, if you put white-green-red corner in front with white facing up as shown in the [video](https://youtu.be/V7M_IKuUREs?si=tjRuOQzYD4ZzFDXb), all the 8 turns will not affect this white-green-red corner. We can conveniently use this corner as anchor as our "virtual cube" (which means the programme will be based on the white-green-red corner in front, with white facing top).

The first array is called centres[6], this defines the position of centres. The integers are mapped to different centres as shown below.

* 0 --> white centre
* 1 --> green centre
* 2 --> red centre
* 3 --> blue centre
* 4 --> orange centre
* 5 --> yellow centre

First entry of the array stores the position that white centre should be at when solved, second entry stores the position that green centre should be at when solved, and so on. When the centres are solved, the array should be {0, 1, 2, 3, 4, 5}.

The second array is called corners_p[7], this defines the permutation of the corners. The integers are mapped to different corners as shown below.

* 0 --> white-orange-blue corner
* 1 --> white-red-blue corner
* 2 --> white-green-orange corner
* 3 --> yellow-green-red corner
* 4 --> yellow-red-blue corner
* 5 --> yellow-blue-orange corner
* 6 --> yellow-green-orange corner

First entry of the array stores the position that white-orange-blue corner should be at when solved, second entry stores the position that white-red-blue corner should be at when solved, and so on. When the permutation of corners are solved, the array should be {0, 1, 2, 3, 4, 5, 6}.

The third array is called corners_o[7], this defines the orientation of the corners. Each corner must have either white or yellow colour, when the white or yellow colour faces up or down, the entry is 0, when it faces right-front or left-back, the input is 1, when it faces left-front or right-back, the input is 2. The first entry stores the orientation of white-orange-blue corner (0 corner), the second entry stores the orientation of white-red-blue corner (1 corner), and so on. When the orientation of corners are solved, the array should be {0, 0, 0, 0, 0, 0, 0}.

### 5. iii. Defining Turns

There are 8 different turns, namely R, R', L, L', U, U', B and B'. There are 8 functions, one for each turn. Each functions will apply necessary changes to the 3 arrays to do the turn to the "virtual cube".

### 5. iv. God's Number

God's number is the maximum number of turns needed to solve any state of a cube. For skewb, the God's number is 11.

### 5. v. Monte Carlo

This programme will then do random moves to the skewb and check whether it is solved after each moves. If solution is not found after doing 12 moves (one more move than God's number), it will search for another algorithm. The programme will avoid applying duplicated moves, such as R after R or R'. The Monte Carlo simulation will be based on the ratio set in line 8 of the code, and the probability of generating clockwise and counterclockwise turn are the same.

### 5. vi. Colour Recognition

The colour recognition is done by python opencv. For colours other than white, I allowed a range of hue and saturation shift from my defined colours.

```python
def get_limits(colour):
    #...
    lower_limit = hsvC[0][0][0] - 10, 100, 100
    upper_limit = hsvC[0][0][0] + 10, 255, 255
    #...
    return lower_limit, upper_limit
```

In the above function, the -10 and +10 refers to hue shift, while 100 and 255 refers to minimum and maximum saturation respectively.

For white colour, it's not that meaningful to define hue shift. Therefore, I hard coded the lower and upper limit of white colour.

```python
lower_white, upper_white = np.array([0, 0, 150], dtype=np.uint8), np.array([179, 55, 255], dtype=np.uint8)
```

### 5. vii. Inputting Colour With Python Script

```python
process = subprocess.Popen(['./a'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
output, error = process.communicate(input=input_string)
if error:
    print(error)
else:
    print(output)
```

At the end of **skewb_solver.py**, it will run the **./a** compiled from **skewb_solution_finder.cpp**, then it will input the colour of each side, capture and display the output from the C++ programme.

## 6. More Statistics

### 6. i. Finding Fewest Moves Solution for Carter

I added a for loop to the **skewb_solution_finder.cpp** to search for solution for 50 times, and return the solution with the fewest move count (saved to **fewest_move_finder.cpp**).

Then I tried to find the moves needed to solve the 5 scrambles of Carter Kucala's final round at Going Fast in Grandview 2024 using this programme. The reason I chose this average was because the last solve was the world record single, but he did not perform that well in other solves. The table below shows the time, scramble, number of moves found by my programme.

| Solve  | Result  |     Scramble (11 moves)      | Fewest number of moves  |
|:------:|:-------:|:----------------------------:|:-----------------------:|
|   1    |  2.49   |  R B L’ U’ L’ R B’ R’ B R B  |            9            |
|   2    |  1.77   | U L U’ B’ U’ B’ U’ L B U’ R’ |            9            |
|   3    |  3.32   |  U L B U’ B’ L’ B R’ B’ L U  |            7            |
|   4    |  4.65   |  R B’ U’ B U B L’ U B’ U R   |            8            |
|   5    |  0.75   |  B L R’ B’ L U B’ R U’ R U   |            8            |

For the last solve, he did find the 8 moves solution. However, he did not find the 7 moves solution for the third solve. This shows that the current method that the top solvers are using (NS algorithm) is unable to attain the fewest moves solution.

It's also worth noting that the third fastest solve was done by Simon Kellum in this same competition, solving that same scramble! This shows that luck really play a role in skewb solves.

### 6. ii. Average Iterations Needed to Find the Solutions and the Average Moves of the Solutions

I randomly generated 11 scrambles, with number of moves from 1 to 11, and tested how many iterations are needed to find the solutions for these scrambles using Monte Carlo, and the moves of the solutions, for 100 times. The averages are listed below.

| Scrambles length |         Scrambles         | Average moves | Average iterations |
|:----------------:|:-------------------------:|:-------------:|:------------------:|
|        1         |             U             |       1       |        7.72        |
|        2         |            B R            |       2       |        46.9        |
|        3         |           L U L           |       3       |       306.76       |
|        4         |         L' B' R U         |       4       |       1688.8       |
|        5         |        L U R' U' R        |     5.361     |      8162.51       |
|        6         |      R U' L' R L U'       |     6.583     |      45323.3       |
|        7         |      U L U' R B' R L      |      8.1      |       285151       |
|        8         |     R' L U B U B' L R     |     9.55      |       913639       |
|        9         | R' L' R' L' B' L' U B' U  |     9.32      |       709984       |
|        10        | L B' R U L U' L' R B' U'  |     9.16      |       444755       |
|        11        | U' B U L U L R' B' L B' L |     9.39      |       768838       |

To better visualise the data, I plotted the following 2 plots.

![average_moves](README_picture/average_moves.png)
![average_iterations](README_picture/average_iterations.png)

As observed from the plots, the number of iteration needed to find the solution and the number of moves of the solution found flatted out from scramble length 8 onwards. Also, the iterations needed significantly increased when the scramble length changed from 6 to 7 and 7 to 8. Therefore, it got me thinking, is it possible to train a neuro network that can predict first few steps of the solution and in return, decrease the iterations needed to find the solution?

### 6. iii. Giving Hints to the Programme

To address the above question, I first confirmed that if the number of iterations would really decrease when I give "hints" to the programme. I took the scramble with length 11 above (U' B U L U L R' B' L B' L) as an example. The first move of the solution is just the reverse of the last move of the scramble, which is L' (let's call this a hint). I try to give the programme hint of 1 moves to 5 moves, and run for 100 times, the result are listed below.

| Number of hints | Average moves | Average iterations |
|:---------------:|:-------------:|:------------------:|
|        1        |     8.67      |       514261       |
|        2        |     9.91      |       971047       |
|        3        |     9.43      |       706315       |
|        4        |     7.88      |       269720       |
|        5        |     6.44      |      44363.8       |

The first great reduce in iteration is caused by some randomness, as a state of a skewb can be solved by different algorithms. We should instead focus on the next continuous reduction in iteration, we can see that giving it hint of 4 to 5 moves will greatly reduce the number of iterations needed to find the solution. Therefore, if we could train a neuro network that can predict possible first 5 moves base on the state of the skewb, we can greatly reduce the computational time of Monte Carlo. Reduced by how much? You might ask. For my computer, the time needed to find the solution without hint is on average 630 ms, while 47 ms with hints.


## 7. Future Works
The code could be generalised for 3x3 cubes, or even other puzzels, this could be made into a programme that solves different Rubik's cubes.

Also, the colour recognition is not very robust. In low light conditions, or when the webcam quality is not good enough, the programme might fail to recognise the colour. The colour detection method in python could be improved.

Lastly, as mentioned in last part, we could train a neuro network to predict first few moves to reduce the number of moves that is needed to be simulated. The total computational time of 2 methods can be compared.

## 8. Reference

External library used:
* numpy
* Pillow
* OpenCV
* Matplotlib

Website used to animate algorithms: https://alpha.twizzle.net/edit/?puzzle=skewb  
Github page of the website: https://github.com/cubing/alg.cubing.net 
