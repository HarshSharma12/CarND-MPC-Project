# **CarND-MPC-Project - Reflection document**
---

### The Model

The model used is a kinematic model

**The state and atuators are modeled using the following equations**

state = {x, y, ψ, v}

actuators = {δ, α}

**The model update equations from t to t+1 are as follows:**

x<sub>​t+1</sub> = x<sub>t</sub> + v<sub>t</sub> cos(ψ<sub>t</sub>) Δt

y<sub>​t+1</sub> = y<sub>t</sub> + v<sub>t</sub> sin(ψ<sub>t</sub>) Δt

ψ<sub>​t+1</sub> = ψ<sub>t</sub> + v<sub>t</sub> δ<sub>t</sub> Δt / L<sub>f</sub>

v<sub>​t+1</sub> = v<sub>t</sub> + α<sub>t</sub> Δt


*Where: 
Lf - the distance between the center of mass of the vehicle and the front wheels.
δ (`delta` in the code) is steering actuation and α is the acceleration actuation (throttle).*

*The vehicle model can be found in the class FG_eval.*



### Timestep Length and Elapsed Duration (N & dt)


The Timestep length N and elapsed duration dt determine the prediction horizon. The MPC tries to fit a given trajectory by looking `N` points ahead spaced by `dt` seconds. 

I tried various combination of `N` and `dt` parameters to improve the performance. Table below represents few of my observations:

| N | dt | Comment |
| --- | --- | ------ |
| 20 | 0.05 | Off Track |
| 20 | 0.1 |  Oscillatory and off track |
| 10 | 0.2 | Off track |
| 10 | 0.1 | Smooth|

I finally setteled for `N = 10` abd `dt = 0.1`. For these values I was able to drive smoothly for speeds upto 42 mph.



### Polynomial Fitting and MPC Preprocessing

* The waypoint of car reference trajectory given in global/map coordinate system.
* These Waypoints are transformed to vehicle coordinate system by translation and rotation.
* Once the waypoints are in vehicle coordinate system, A third order polynomial is then fitted .



### Model Predictive Control with Latency

A latency of 100ms is added before sending the actuations to the simulator. If the latency problems are not handled, it may result in oscilations and drive the car off track. 

To take into account the latency, before feeding the equations to the solver, I predict the new state at the latency time. I calculate the new car's new_px and new_py and new_psi at lentency time and map them to new_vehicle coordinates. Then calucate cte and epsi and update them for latency. Finally, feed the updated values to the solver.


