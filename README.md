# FlyingCarND_BackyardFlyer

First drone flight path using Python code and Udacity Drone API(https://udacity.github.io/udacidrone/docs/drone-api.html)

Python v3.6.3

Packages used:

* [`matplotlib`](https://matplotlib.org/)
* [`jupyter`](http://jupyter.org/)
* [`udacidrone`](https://github.com/udacity/udacidrone)
* [`visdom`](https://github.com/facebookresearch/visdom/)



Drone flies in a simple 7X7 square path before coming back to its home location.

## Step 1: Download the Simulator
If you haven't already, download the version of the simulator that's appropriate for your operating system [from this repository](https://github.com/udacity/FCND-Simulator-Releases/releases).

## Step 2: Set up your Python Environment
If you haven't already, set up your Python environment and get all the relevant packages installed using Anaconda following instructions in [this repository](https://github.com/udacity/FCND-Term1-Starter-Kit)

## Step 3: Clone this Repository
```sh
git clone https://github.com/udacity/FCND-Backyard-Flyer
```
## Drone API

To communicate with the simulator (and a real drone), you will be using the [UdaciDrone API](https://udacity.github.io/udacidrone/).  This API handles all the communication between Python and the drone simulator.  A key element of the API is the `Drone` superclass that contains the commands to be passed to the simulator and allows you to register callbacks/listeners on changes to the drone's attributes.  The goal of this project is to design a subclass from the Drone class implementing a state machine to autonomously fly a box. A subclass is started for you in `backyard_flyer.py`

### Drone Attributes

The `Drone` class contains the following attributes that you may find useful for this project:

 - `self.armed`: boolean for the drone's armed state
 - `self.guided`: boolean for the drone's guided state (if the script has control or not)
 - `self.local_position`: a vector of the current position in NED coordinates
 - `self.local_velocity`: a vector of the current velocity in NED coordinates

For a detailed list of all of the attributes of the `Drone` class [check out the UdaciDrone API documentation](https://udacity.github.io/udacidrone/).

### Outgoing Commands

The UdaciDrone API's `Drone` class also contains function to be able to send commands to the drone.  Here is a list of commands that you may find useful during the project:

 - `connect()`: Starts receiving messages from the drone. Blocks the code until the first message is received
 - `start()`: Start receiving messages from the drone. If the connection is not threaded, this will block the code.
 - `arm()`: Arms the motors of the quad, the rotors will spin slowly. The drone cannot takeoff until armed first
 - `disarm()`: Disarms the motors of the quad. The quadcopter cannot be disarmed in the air
 - `take_control()`: Set the command mode of the quad to guided
 - `release_control()`: Set the command mode of the quad to manual
 - `cmd_position(north, east, down, heading)`: Command the drone to travel to the local position (north, east, down). Also commands the quad to maintain a specified heading
 - `takeoff(target_altitude)`: Takeoff from the current location to the specified global altitude
 - `land()`: Land in the current position
 - `stop()`: Terminate the connection with the drone and close the telemetry log

These can be called directly from other methods within the drone class.

### Message Logging

The telemetry data is automatically logged in "Logs\TLog.txt" or "Logs\TLog-manual.txt" for logs created when running `python drone.py`. Each row contains a comma seperated representation of each message. The first row is the incoming message type. The second row is the time. The rest of the rows contains all the message properties. The types of messages relevant to this project are:

* `MsgID.STATE`: time (ms), armed (bool), guided (bool)
* `MsgID.GLOBAL_POSITION`: time (ms), longitude (deg), latitude (deg), altitude (meter)
* `MsgID.GLOBAL_HOME`: time (ms), longitude (deg), latitude (deg), altitude (meter)
* `MsgID.LOCAL_POSITION`: time (ms), north (meter), east (meter), down (meter)
* `MsgID.LOCAL_VELOCITY`: time (ms), north (meter), east (meter), down (meter) 

The six states predefined for the state machine:
* MANUAL: the drone is being controlled by the user
* ARMING: the drone is in guided mode and being armed
* TAKEOFF: the drone is taking off from the ground
* WAYPOINT: the drone is flying to a specified target position
* LANDING: the drone is landing on the ground
* DISARMING: the drone is disarming

### Reference Frames

Two different reference frames are used. Global positions are defined [longitude, latitude, altitude (pos up)]. Local reference frames are defined [North, East, Down (pos down)] and is relative to a nearby global home provided. Both reference frames are defined in a proper right-handed reference frame . The global reference frame is what is provided by the Drone's GPS, but degrees are difficult to work with on a small scale. Conversion to a local frame allows for easy calculation of m level distances. Two convenience function are provided to convert between the two frames. These functions are wrappers on `utm` library functions.
