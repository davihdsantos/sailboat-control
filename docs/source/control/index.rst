
.. _control:

=========================
Control Strategies
=========================


The robots of this simulator have a build in movement control that we use to test some scenarios. The main objetive of this control strategy is to take the vehicle from point A to point B, point A beeing the curent position and point B beeing the desired or target position. The curent control strategy implemented is one of the simplest control strategies, called heading control.

This heading control strategy consists of doing consecutives correction of the robots heading by chosing appropriated actuators position/velocity. The idea of this control is as follows: if at each step the robot is pointing to the target point and if theres some forward velocity (in the robots frame) then eventually the vehicle will reach the desired position. Thus, what the controller does is recieve the information necessary to ajust the robots heading, namelly the curent and desired position, then calculates the proper position of the actuator to reduce the error generated by subtracting the curent and desired heading. Another part of the control must be resposible for activating the thrusters power (or at least the actuator responsable for propulsion) accordingly. The behavior generated by only using this raw erro to ajust the heading is pretty slow. To reduce this problem and set a desired behavior we use a PI strategy on the error.

The control strategy describe above is currently running in all platforms. We chose this strategy due to its simplicity that makes it quick and easy to implement. The main difference among the implemented control strategies on the platforms is how the actuation is done. For example, in the airboat the robots heading is changed according to the orientation of the fan. The rudder boat and sailboat use the rudder to change its heading. The differential boat set different thurster power on the left and right thruster to achieve heading change. The details of each control are shown below.

All of the controllers bellow use the same strategy to find the heading to be followed (preprossessing). First the it gets information about odometry (curent position) the the target position and it uses these information to find the heading to be followed. Then finds the error between the curent heading and the desired heading and uses the sign and absolute value of this error (with the PI gain) to find the apropriate action to correct it and bring the platform to the desired heading. For each platform:

**Heading control of the airboat**: Uses the heading error to find suitable fan orientation. Publishes the fan angle to /airboat/jointSetpoint.

**Forward propulsion control of the airboat**: We implemented this behavior as a set of conditions. If the distance to target is greater than 10 meters the propulsion (fan power) is 100%. If the distance is between 2 and 10 meters then the propusion is 50%. If the distance is less than 2 meters then the propusion is set to 0%.

**Heading control of the boat rudder and sailboat**: These platforms use the same rudder control, wich consists of using the heading error to find suitable rudder angles. The rudder angle is published in /boat_rudder/jointSetpoint for the boat rudder and /sailboat/jointSetpoint for the saiboat.

**Forward propulsion control of the Boat Rudder**: Similar to the Airboat.

**Forward propulsion control of the Sailboat**: Ajusts the sail position according to the wind direction.Open and closes the sail according to the wind direction on the sailboats frame: head to wind (in irons) -> close sail (0 degrees). rear to wind (running) -> open sail (90 degrees). wind betweens these positions -> sail position chosen linearly between 0 and 90 degrees.

**Heading control of the Differential Boat**: Uses the heading error to find suitable velocities for both thrusters (left and right). The diferential boat rotates acording to the power of both thrusters. So, for example, to rotate clockwise (on its axis) a diferential boat must use oposite power values on each thruster, for example, 10% on the right and -10% on the left thurster. So the controller check the error sign, chose a suitable direction of rotation and, acording to the error absolute value, chose the thuster velocity.  For example, a positive error means that the platform must rotate clockwise to reach the desired heading and a negative error means the platform must rotate counter clockwise (these relations depends of the defined frames). 

**Forward propulsion control of the Differential Boat**: Uses a set of condition to decide if the boat should go forward. Use a similar set of condition describe on the Airboat forward control. To get forward velocity in the Diferential Boat it must set the same power in both thrusters.

+-------------------+-------------------------------------------------------------------+
| Heading Control   | File                                                              |
+===================+===================================================================+
| Airboat           | usv_sim_lsa/src/usv_base_ctrl/scripts/airboat_control_heading.py  |
+-------------------+-------------------------------------------------------------------+
| Differential Boat | usv_sim_lsa/src/usv_base_ctrl/scripts/diff_control_heading.py     |
+-------------------+-------------------------------------------------------------------+
| Rudder Boat       | usv_sim_lsa/src/usv_base_ctrl/scripts/rudder_control_heading.py   |
+-------------------+-------------------------------------------------------------------+
| Sailboat          | usv_sim_lsa/src/usv_base_ctrl/scripts/sailboat_control_heading.py |
+-------------------+-------------------------------------------------------------------+

The videos below show the control strategies described above woking at different levels of disturbance (water curent and wind).


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="https://www.youtube.com/embed/eFy0dBdKnTg" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 95%; height: 95%;"></iframe>
    </div>

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="https://www.youtube.com/embed/Fx0n8Vdzoj8" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 95%; height: 95%;"></iframe>
    </div>

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="https://www.youtube.com/embed/1V33dut5HRg" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 95%; height: 95%;"></iframe>
    </div>


