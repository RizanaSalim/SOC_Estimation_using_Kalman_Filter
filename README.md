# SOC_Estimation_using_Kalman_Filter

 State-of-charge(SOC)

The state of charge of a battery describes the difference between a fully charged battery and the same battery in use. It is associated with the remaining quantity of electricity available in the cell.
It is defined as the ratio of the remaining charge in the battery, divided by the maximum charge that can be delivered by the battery. It is expressed as a percentage given below.

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/54576ce9-28ed-4077-959c-70ecadb21ba6)

This project aims at implementing a Simulink model that estimates SOC of a Lithium-ion battery using Kalman Filter approach.

# Model Details

The input parameters of the model include current and voltage that comes from battery data in HPPC(Hybrid PulsePower Characteristic) test.

Thevenin equivalent circuit model and extended kalman filter(EKF) are included in the simulation file "SOC_Estimation_EKF.slx"

FYR find the snapshot below:
![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/38d517d2-d14f-4f80-b74d-39d30a3423e9)

The estimated curve has distinct divergences in the current pulse areas and it converges to the true value in the constant current discharge areas.

The estimated SoC and update Up(voltage of RC element in Thevenin ECM) change synchronously due to the same state vector that they are in, that can be seen in the function block 'EKF'.

Kalman filter update of states including SoC and Up, is taken according to the difference between observed values and predicted values of UL(voltage on the load). The code format of this expression is as follows:
X_upd = X_pre + K*(UL_obs-UL_pre);


