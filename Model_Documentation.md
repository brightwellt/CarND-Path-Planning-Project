# Vehicle Detection Project
# Model Description and Writeup

## Outline

The goal of this project is to design a path planner that is able to create smooth, safe paths for the car to follow along a 3 lane highway with traffic.
A successful path planner will be able to 
- keep inside its lane 
- avoid hitting other cars
- pass slower moving traffic.

This is to be done using localization, sensor fusion, and map data.

## Code overview
Starter code was provided by Udacity. The intent is to have a path planning executable which sends path data to the simulator,
uiding my car through the traffic.
A walkthrough video provided further guidance, suggesting how to get the car moving and navigating between lanes.

The simulator provides my code with
- my car's current localization data
- the previous path data submitted to the simulator
- a set of sensor fusion data, listing all other cars on my side of the road.

### State Machine code
The main.cpp code contains a very simple finite state machine, which determines which lane I should be in and how fast I should go.

It works as follows:
- Examine the sensor fusion data. 
- Work in Frenet coordinates.
- If a car is in my lane, then check to see if it is within my lane, just ahead of me, and if I am likely to hit it.
- If so, note that the car is "too close" and track the speed.

- Repeat these checks for the lanes to my left and right. Assuming I have a lane there that is. 
- Look more specifically for passing gaps - just behind as well as in front of me.
- I potentially want to move out if it is safe, so track the speed and whether the lane is occupied.

- If I am "too close" in my lane, then move into an adjacent lane if they are not occupied.
- If the lanes adjacent to me are occupied, then set a slower target speed than the car in front of me, so that I can fall back and find a gap.
(In effect this is a crude "prepare to change lanes" state.)

The next section of code does the actual path planning.

### Path Planning code
- Continue using any remaining points from the previous path. Otherwise we might get jerk.
- In Frenet coordinates, add points some 30m ahead of the starting reference.
- Use a 3rd party spline library, found here
http://kluge.in-chemnitz.de/opensource/spline/

to make a set of spline points so that we arrive smoothly at our reference velocity and target speed. 
Note that we'll be slowing down here if our lane is blocked and our state machine told us to slow down.
The points are then converted bacck into x, y values and sent to the simulator.


## Performance
On tests, this path planner is capable of smoothly driving the car around the highway course for the distance outlined in the rubric.
There are no collisions. It can change lanes when it needs to. It does not have excessive acceleration or jerk. It will drive slowly
if obstructed by traffic.


## Limitations
The state machine employed here is very basic. It does not look further into the future than the next set of path planning. It does
not look at all of the lanes all of the time. 
This means that it is potentially vulnerable to being caught in an "r" type pocket in the right hand lane, where the left hand lane is empty,
but the car cannot fall back far enough to move across to the empty lane.
The d motion of other cars is not considered. If another car changes lanes (rare in the simulator; less so in real life) at the same
time as we do, then a collision could theoretically occur.


## Future Improvements
Separate the state machine logic into its own set of classes. Write cost functions for each state transition and evaluate those.
So if all 3 lanes are occupied, we can determine which would be the best lane to be in - as in the one having the least cost - 
and move into that lane. Include emergency states, so that we can evade if another car abruptly changes lane or speed.
Consider using a PID or MPC.




