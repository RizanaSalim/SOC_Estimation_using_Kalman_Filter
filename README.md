# SOC_Estimation_using_Kalman_Filter

 State-of-charge(SOC)

The state of charge of a battery describes the difference between a fully charged battery and the same battery in use. It is associated with the remaining quantity of electricity available in the cell.
It is defined as the ratio of the remaining charge in the battery, divided by the maximum charge that can be delivered by the battery. It is expressed as a percentage given below.

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/54576ce9-28ed-4077-959c-70ecadb21ba6)

This project aims at implementing a Simulink model that estimates SOC of a Lithium-ion battery using Kalman Filter approach.

# Simulink Model Details

The input parameters of the model include current and voltage that comes from battery data in HPPC(Hybrid PulsePower Characteristic) test.

Thevenin equivalent circuit model and extended kalman filter(EKF) are included in the simulation file "SOC_Estimation_EKF.slx"

FYR find the snapshot below:
![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/38d517d2-d14f-4f80-b74d-39d30a3423e9)


The estimated curve has distinct divergences in the current pulse areas and it converges to the true value in the constant current discharge areas.

The estimated SoC and update Up(voltage of RC element in Thevenin ECM) change synchronously due to the same state vector that they are in, that can be seen in the function block 'EKF'.

Kalman filter update of states including SoC and Up, is taken according to the difference between observed values and predicted values of UL(voltage on the load). The code format of this expression is as follows:

X_upd = X_pre + K*(UL_obs-UL_pre);

The output results of the simulation are shown below:

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/adeb3172-e8cd-4ba5-a319-760b852159a5)

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/6891791f-56c0-4bd7-a71b-5ce85b282e51)

# Improved EKF

After improvement, the I/O relationship between modules becomes more perspicuous, the corresponding Simulink file is named "SOC_Estimation_Improved_EKF.slx"
![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/e9993133-8179-41a6-96e4-4e51aa3e9518)
![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/cb5088b7-cd81-4a37-a342-08073375769c)
![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/67eeb6cb-22ae-448a-8635-9d0eb4d6062d)




 The input current to the battery is given in the below format:

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/f5bbc29c-42a9-4524-806f-0232e8d89e0b)

The output results are captured in the below snapshots:

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/4c6685ef-9e25-4f37-92e6-f87a79a4ee91)

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/0bf24a35-45f6-4477-852b-fd1925ee5c79)

# SOC estimation using EKF through MATLAB Scripts
MATLAB scripts simulate discharge process of lithium-ion battery under the BBDST working condition and constant current working condition with observation noise, and uses EKF/UKF method to estimate SoC of the battery.

function main(Work_modes, SoC_est_init)

The main function requires two arguments:

Work_modes: Choice of working condition with 1 representing HPPT working condition and 2 representing constant current.

SoC_est_init: The initial value of estimated SoC, it's set to 1 by default, if just one argument is passed.

Type in command window like main()ormain(1)ormain(1,1), the result curves will appear as follows.

The result is showcased below:

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/17896e37-c288-4e9d-80df-8cf252dd2006)


# Reference
# 1. Thevenin equivalent circuit model

Thevenin’s theorem

"Thevenin’s theorem states that any linear network having a number of voltage sources and resistances can be replaced by a simple equivalent circuit consisting of a single voltage source (VTH)  in series with a resistance (RTH), where VTH is the open-circuit voltage at the terminals of the load and RTH is the equivalent resistance measured across the terminals while independent sources are turned off."

Thevenin equivalent circuit model(ECM) is a first-order RC circuit. The discharge direction is taken as the positive direction of current, as shown in the figure below:

![image](https://github.com/RizanaSalim/SOC_Estimation_using_Kalman_Filter/assets/84447324/527e4589-05b6-44e5-b739-0fef8a515064)


The voltage on the polarization capacitor is denoted as Up. Then according to KVL and KCL we get the following equations.

UL(t) = UOC - Up(t) - Ro * I(t)              ······(1)

I(t) = Up(t) / Rp - Cp * (dUp(t)/dt)         ······(2)

The solution of the differential equation (2) is as follows.

Up(t) = C * exp(-t/tao) + I(t) * Rp          ······(3)

tao = Rp * Cp                                ······(4)

Here C is an arbitrary constant. The zero input response of the circuit model corresponds to the idle condition of the battery while zero state response corresponds to the working condition. The discretized form of Up in different states can be unified as follows.

Up(k+1) = Up(k) * exp(-Δt/tao) + 
          Rp * I(k) * (1 - exp(-Δt/tao))     ······(5)
          
Here Δt denotes the sample interval. The parameters in the Thevenin ECM, including UOC, Ro, Rp and Cp, are deemed to be related to the SoC of the battery. The relationships are usually identified through the so call Hybrid Pulse Power Characterization(HPPC) test.

# 2. Extended Kalman Filter

The basis of Kalman filter is the process of fusing the information from the prediction and the observation, under the hypothesis that the process error and the observation error are both stochastic noises, obeying Gaussian distributions. The EKF mainly consist of three steps: prediction, linearization and update.


_______________________________________________________
|                                                     |
|    |----------|     |-------------|     |------|    |
---->|prediction|---->|linearization|---->|update|-----
     |----------|     |-------------|     |------|

     
The prediction needs the knowledge of the state transition, expressed as equation (5) and (6), and observation estimate, expressed as equation (7) and (8).

SoC(k) = SoC(k-1) - eta/Qn * I(k-1)          ······(6)

UL(k) = UOC(k-1) - I(k-1) * Ro - Up(k-1)     ······(7)

UOC(k) = f(SoC(k-1))                         ······(8)

Here eta and Qn denote the coulombic efficiency and the rated capacity of the battery respectively. Let X be the state vector [SoC, Up]', A be the state transformation matrix [1, 0; 0, exp(-Δt/tao)] and B be the input control matrix [-eta/Qn, 0; 0, Rp*(1-exp(-Δt/tao))]. Then equation (5) and (6) can be expressed as follows.

X(k) = A * X(k-1) + B * I(k-1)               ······(9)

The Kalman filter makes use of the process noise and observation noise for the state estimation. Then there's an important step of predicting the covariance of process error, expressed as P.

P(k) = A(k-1) * P(k-1) * A'(k-1) + Q        ······(10)

Here Q is a diagonal matrix containing the variances of the process noises. For the state X=[SoC, Up]', Q is a 2x2 diagonal matrix [Qs, 0; 0, Qu], in which Qs is the variances of process noises for SoC and Qu is that for Up.

Note that UOC is a nonlinear function of SoC, which makes the UL=g(X, I) is nonlinear. The linearization step is Taylor expanding g(X, I) around X(k) with first order approximation.

UL = g(X(k), I(k)) + əg/əX(k) * (X - X(k))  ······(11)

C(k) = əg/əX(k)
     = [əg/əSoC(k) əg/əUp(k)]
     = [ə(UoC-Ro*I(k))/əSoC(k) -1]          ······(12)
     
Hence UL = C(k) * X + (g(X(k), I(k)) - C(k)*X(k)).

C(k) is a constant matrix and (g(X(k), I(k)) - C(k)*X(k)) is a constant too. The linearization is completed.

Based on the result of linearization, the update step include updating the state vector and the covariance of process error by using the variance of observation noise R and observation value UL_ob.

K(k) = P(k) * C'(k) * 
       (C(k) * P(k) * C'(k) + R)^(-1)       ······(13)
       
X(k) = X(k) + K(k) * (UL_ob - UL(k))        ······(14)

P(k) = P(k) - K(k) * C(k) * P(k)            ······(15)


An Intuitive Derivation of the Kalman filter process is given in this source:

https://courses.engr.illinois.edu/ece420/sp2017/UnderstandingKalmanFilter.pdf.

Detailed explanation of UKF is available below:

https://www.cs.ubc.ca/~murphyk/Papers/Julier_Uhlmann_mar04.pdf











