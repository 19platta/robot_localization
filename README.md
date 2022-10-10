# Robot Localization Project Report
Annabelle Platt and Kate Mackowiak

## Intructions for running the project:
Set up Gazebo: `ros2 launch neato2_gazebo neato_gauntlet_world.py`

Launch rviz: `rviz2` and open the saved configuration

To run the filter code, run `ros2 launch robot_localization test_pf.py map_yaml:=map_name.yaml`, where `map_name.yaml` is the path to the map to use. 

To drive the robot in Gazebo, run `ros2 run teleop_twist_keyboard teleop_twist_keyboard`

## Project Goal
Our goal was to estimate the location of the robot in a given map based on odometry and lidar scan data.  

## How did you solve the problem? (Note: this doesn’t have to be super-detailed, you should try to explain what you did at a high-level so that others in the class could reasonably understand what you did).
To solve this problem, we are using a particle filter. We first distribute a set of particles throughout the map, concentrated around where we think the robot might be. 

For each particle, we evaluate the probability that it is in the correct location, based on how closesly the lidar scan from the robot, if taken from that point, would align with what obstacles are visible in the map. Then we then redistribute to new locations any particles which have a probability of being the correct location below a threshold that we set. 

Once the particles are redistributed, we use the odometry data to move the particles to a new location based on where the robot has moved, and evaluate their probability using lidar again and redistributue unlikely particles. We continue to repeat these steps to cluster the particles in what is likely the correct location - as with each new step we have more information (because we have more prior data ala Bayes' Theorem) and can make a more accurate prediction of where the robot is located.

## Describe a design decision you had to make when working on your project and what you ultimately did (and why)? These design decisions could be particular choices for how you implemented some part of an algorithm or perhaps a decision regarding which of two external packages to use in your project.

One design decision that we made was in how we implemented updating particles based off the laser scan. We had two methods to determine the location of each laser data point for a given particle which we could have pursued -
option A - cycle through each point in the laser scan data and compute it's location based on the pose of the point.
option B - using linear algebra apply a transformation to the set of points to find their locations.
While option B would provide a cleaner solution, we implemented option A. This decision arose out of our time constraints while working on this project. Option A was faster to implement for us as it did not require us to navigate challenges of working with python linear algebra packages that we were unfamiliar with. The time to implement proved more important to us than the cleanliness of our code, something which we think is a relevant consideration when working under a deadline. 

## What if any challenges did you face along the way?

One challenge that we faced was figuring out how to implement updating each particles location based on the robots odometry. While we were able to figure out a high level process that we needed to implement, we struggled to determine the best way to translate that into code. Our initial thought was to use rotation matrices to convert points between frames (from odometry to base to map) and then translate the end result, however this method proved confusing to keep track of and seemed prone to potential errors. After consulting with Paul, we changed direction to using matrices which would convert a point between two frames. By finding matrices to convert between the frames we needed to move between, we were able to implement a clean solution to determine the particle's new location.

## What would you do to improve your project if you had more time?

This goes back to the design decision we made described above. If given more time, we would be able to implement cleaner solutions that utilize linear algebra to operate on a set of points rather that doing computation on each point individually. This impovement would allow our code to run faster and potentially allow us to debug errors more easily. 

## Did you learn any interesting lessons for future robotic programming projects? These could relate to working on robotics projects in teams, working on more open-ended (and longer term) problems, or any other relevant topic.

One learning we had was about how we can set up matrices to convert between frames more easily. This seems useful for future robotics projects as one key element of setting up a project is keeping track of which frame you are working in. 
Another learning is that working in different time zones and availabilities is pretty difficult (Kate sincerely apologizes to Annabelle) and it's quite nice to be able to work with your partner while you are both on campus. 