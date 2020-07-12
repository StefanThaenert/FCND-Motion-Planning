# FCND - 3D Motion Planning
![Quad Image](./misc/enroute.png)


This project is a continuation of the Backyard Flyer project where you executed a simple square shaped flight path. 

## To run this project on your local machine, follow these instructions:
### Step 1: Download the Simulator
This is a new simulator environment!  

Download the Motion-Planning simulator for this project that's appropriate for your operating system from the [simulator releases respository](https://github.com/udacity/FCND-Simulator-Releases/releases).

### Step 2: Set up your Python Environment
If you haven't already, set up your Python environment and get all the relevant packages installed using Anaconda following instructions in [this repository](https://github.com/udacity/FCND-Term1-Starter-Kit)

### Step 3: Clone this Repository
```sh
git clone https://github.com/StefanThaenert/FCND-Motion-Planning.git
```
### Step 4: Test setup
The first task in this project is to test the [solution code](https://github.com/udacity/FCND-Motion-Planning/blob/master/backyard_flyer_solution.py) for the Backyard Flyer project in this new simulator. Verify that your Backyard Flyer solution code works as expected and your drone can perform the square flight path in the new simulator. To do this, start the simulator and run the [`backyard_flyer_solution.py`](https://github.com/udacity/FCND-Motion-Planning/blob/master/backyard_flyer_solution.py) script.

```sh
source activate fcnd # if you haven't already sourced your Python environment, do so now.
python backyard_flyer_solution.py
```
The quad should take off, fly a square pattern and land, just as in the previous project. If everything functions as expected then you are ready to take a deeper look on this project. 

Unfortunatly there is a bug at least in the windows version of the simulator. The starting point is within a building. Just leave the environement (ESC) and go back in and you are on a plain field. 

### Step 5: Inspect the relevant files
For this project two scripts are provided, `motion_planning.py` and `planning_utils.py`. Here you'll also find a file called `colliders.csv`, which contains the 2.5D map of the simulator environment. 

## Summary: Write it up!

### What's going on in  `motion_planning.py` and `planning_utils.py`

`motion_planning.py` is basically a modified version of `backyard_flyer.py` that leverages some extra functions in `planning_utils.py`. 

As you may see in the following diagrams the main difference between the backyard flyer project and the motion planning project is the planning phase.
![flow backyard Image](./misc/backyard_flyer_flow.png)
In the backyard flyer project a simple rectangle is defined within the code but within the motionplanning project your first load a 2.5D Map of your environment, create a grid where the drone is allowed to fly and search for a path from a predefined starting point to a goal.
![flow motion planning Image](./misc/motion_planning_flow.png)

You may run this by the following code.
 
```sh
conda activate fcnd # if you haven't already sourced your Python environment, do so now.
python motion_planning.py
```
#### Tasks within `motion_planning.py`
- read lat0, lon0 from colliders into floating point values
--> The required information is stored in the first line of colliders.csv ([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L123))
- set home position to (lon0, lat0, 0)
--> the required function is defined in drone.py([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L131))
-retrieve current global position
-->([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L134))
- convert to current local position using global_to_local()
--> function is defined in udacidrone.frame_utils ([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L137))
- convert start position to current position rather than map center
-->([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L149))
- adapt to set goal as latitude / longitude position and convert
-->([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L158))
- prune path to minimize number of waypoints
--> is solved as function within planning_utils.py ([Code](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/planning_utils.py#L158))

#### changes within `planning_utils.py`
- add valid actions north-west, north-east, south-west and south-east --> with this the drone is able to fly also in these 4 directions. 
- prune_path this function has been added togehter with the collinarity_check to earse not required waypoints on the path. Within this function it is checked whether the area spanned by three consecutive waypoints is smaller than a limit area (epsilon). 
-find_start_goal --> this function is required to find a path on a graph (equal distance between obstacales)

### What about A-Star-grid-vs-graph.ipynb
Everything described so far refers to python files that control the drone in the simulator. In A-Star-grid-vs-graph.ipynb the procedure for determining the path is shown. Not only the grid is used but also the graph. For a better understanding the single steps are shown in different images. The functions from motion_planning.py and planning_utilitys.py are reused.

### Adjust your deadbands
The size of the deadbands around your waypoints is defined with a fixed value of 1. This causes the drone to fly over the waypoints or tries to stop. To ensure a smooth transition to the next waypoint this limit can be increased to 10. However, it is better to introduce a speed dependency. This was done in [here](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L49). However, this measure leads to the fact that the last waypoint is not approached exactly. Therefore [here](https://github.com/StefanThaenert/FCND-Motion-Planning/blob/master/motion_planning.py#L53) an additional condition was introduced before the drone is allowed to land.

## Next steps

### Add heading commands to your waypoints
This is a recent update! Make sure you have the [latest version of the simulator](https://github.com/udacity/FCND-Simulator-Releases/releases). In the default setup, you're sending waypoints made up of NED position and heading with heading set to 0 in the default setup. Try passing a unique heading with each waypoint. If, for example, you want to send a heading to point to the next waypoint, it might look like this:

```python
# Define two waypoints with heading = 0 for both
wp1 = [n1, e1, a1, 0]
wp2 = [n2, e2, a2, 0]
# Set heading of wp2 based on relative position to wp1
wp2[3] = np.arctan2((wp2[1]-wp1[1]), (wp2[0]-wp1[0]))
```

This may not be completely intuitive, but this will yield a yaw angle that is positive counterclockwise about a z-axis (down) axis that points downward.
### Complex trajectory 
In this project, things are set up nicely to fly right-angled trajectories, where you ascend to a particular altitude, fly a path at that fixed altitude, then land vertically. However, you have the capability to send 3D waypoints and in principle you could fly any trajectory you like. Rather than simply setting a target altitude, try sending altitude with each waypoint and set your goal location on top of a building!
