# NTC Thermistor interfacing with STM32 Microcontroller

### INTRODUCTION

The NTC thermistor(thermal-Resistor) referes to the use of Negative Temperature Coefficient material for temperature sensing. It means that when temperature T *increases* the resistance R *decreases*.
While NTC thermistor finds it's application in temperature sensing, PTC thermistors(T increases, R increases) is used in fuses.
For thermistors, R-T curve is plotted which basically tells us the resistance values of thermistor at different temperatures. Here's an example:
<img width="500" height="357" alt="image" src="https://github.com/user-attachments/assets/b003c902-6914-4cbb-a967-64b670c8feb3" />

*reference:https://blazeprobes.com/all-about-temperature-sensors-thermistors-thermocouples-and-rtds/*

The formula that describes the describes the behaviour of the NTC thermistor is given by:
  $\frac{1}{T} = \frac{1}{T_0} + \frac{\ln\left(\frac{R}{R_0}\right)}{B}$

This is also called *Steinhart-Hart Equation*.

## CIRCUIT
3.3V ── R_100k ──┬── ADC (Vout)

                     | 
                 
                    NTC
                
                     |
                 
                    GND

 A voltage divider is used to get the output with a resistor of 100K ohm in series with Thermistor.
 ADC channel 0 is configured for the input.

### INTERFACING AND VARIABLES USED

Refer the *main.c* file for the code. Location: core --> src --> main.c .

I am using ADC(polling mode) since it and an analog sensor.
1. The variable *voutpin* stores the digital output of the analog input signal from thermistor, in the expression:

 **const uint16_t voutpin = HAL_ADC_GetValue(&hadc);**

2. Using a reference voltage of 3.3V the output voltage is determined using the formula:

**vout = voutpin * (3.3 / 4095);**       

(4095 as 12-Bit ADC max value, 0-4095 range).

3. Now we calculate the thermistor resistance using voltage divider fomrula:

**R_NTC = (vout * R_100k) / (vsupply - vout);**

4. Once we have the thermistor resistance we can use it in the Steinhart-Hart equation to get the temperature in kelvin(Temp_K)

**Temp_K = (T0 * B_param) / (T0 * log(R_NTC / R_100k) + B_param);**

Temperature in degree celcius is given by:

**Temp_C = Temp_K - 273.15;**

[Note: The value of Beta parameter is given in the datasheet of the given sensor or specified in specifications page.
Beta (β) is a constant (in Kelvin units) that characterizes the slope of the resistance–temperature curve of an NTC thermistor.]

In the code, I have used UART to transmit the temperature values to the Tera-term terminal PuTTy.

References for better understanding:

https://www.youtube.com/watch?v=nXmkkyw8v5A

https://www.youtube.com/watch?v=gxZhyAM2Gfk


