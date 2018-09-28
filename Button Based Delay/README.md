# Button Based Delay
  The purpose of this code is to blink two LEDs (Pins 1.0 and 1.6) at a frequency based on the time held by the user.
The initial frequency is set to 10HZ and is determined by the time Button 1.3 is held down for. To achieve this task, both the MSP430G2553 and MSP430FR2311 boards were used for this code.

# Functionality
This code utilizes two interrupts; a Timer A0 interrupt and a Port1 interrupt. When a partiular value in the CCR0 value is met, the internal Timer A will trigger an interrupt, causing the LED(s) to toggle on or off. Then, the clock overflows and returns to 0. During this state, the clock is currently in Up mode. However, when a button is pressed, the Port 1 interrupt is activated. Once this occurs, the clock switches to continous mode while the edge select is inversed. Recording the time held with the internal clock, once the button is released, that value is loaded from the TA0R register into the CCR0 register. Once this occurs, the edge select is reverted back to its orginal state and the clock is set back to Up mode. With the new CCR0 value loaded in, the frequency of the clock is now dependent on the time the button was held down. As indicated by the inital value CCR0 = 103, the base clock frequency is set to 10HZ.

# Initlization
  
    WDTCTL = WDTPW | WDTHOLD;   
   // Stop watchdog timer
   
    BCSCTL3 = LFXT1S_2; 
   //Enables crystal

    P1DIR |= 0X41;
    
   //Sets Pins 1.0 (GREEN LED) and PIN 1.6 (RED LED) as an output
   
    P1REN |= BIT3;
    
   //Sets Pin 1.3 (Button) to be pull up/down enabled
   
    P1OUT |= BIT3;
   //Sets Pin 1.3 (Button) to pull up resistor
   
    P1OUT |= 0X41; 
   //Sets Pin 1.0 (GREEN LED) and PIN 1.6 (RED LED) to be ON to begin
   
     P1IE |= BIT3; 
   //Enables interrupt on PIN 1.3 (Button)
   
    P1IES |= BIT3;
   //Enables high - to - low behavior on interrupt on Pin 1.3 (BUTTON)
   
     CCTL0 = CCIE; 
   //Enables interrupts

    CCR0 =  103;
   //Set Frequency to 5 Hz ((32768 Hz/8)/(2(2*5)) = 102.4 or 103
   
    TACTL = TASSEL_1 + MC_1 + ID_3;
   // ACLK selected, Up-Count Enabled, Divider = 8
  
  # Timer A Interrupt
  
  	P1OUT ^= 0X41; 
  //Toggles Pins 1.0 and 1.6 (GREEN and RED LEDs) on and off
  
  # Port 1 Interrupt
  
    if (P1IES & BIT3)
    //If P1IES AND BIT3 (Button) are 1, it will perform the following code.
  
    {
        TACTL = TACLR; 
        
        //Clears any prexisting value stored in Timer A's register
        
        TACTL = TASSEL_1 + MC_2 + ID_3;  
        //Changes clock mode to Continuous mode
        
        P1IES &= ~BIT3; 
        //Sets Port 1's edge select to 0 (Low - to - High)
    }
    else
    {
    CCR0 = TA0R; 
    //Sets value in CCR0 register to the value in Timer A's register
    
    TACTL = TASSEL_1 + MC_1 + ID_3;  
    //Reverts clock mode back to Up-Count mode
    
    P1IES |= BIT3; 
    //Sets Port 1's edge select to 1 (High - to - Low)
    
    }
    P1IFG &= ~BIT3; 
    //Resets interrupt flag initially triggered
    
 # Differences between MSP430G2553 and MSP430F5529LP
 Although mostly the same, below are the primary changes that can be observed:
 
 - All instances of BIT3 (Button) are changed to BIT1.
 - All instances of P1OUT is changed to P1OUT = 0X01;
 - All instances of P1DIR is changed to P1DIR = 0X01;
 - All instances of CCTL, CCR0, and TACCTL are changed to TA0CCTL, TA0CCR0, TA0CCTL.
    
 # Results
It can be observed that holding down the button for a set amount of time and releasing will cause that time value to be the new frequency of the timer. It should be noted that an overflow can cause the result to be different than what is expected.
  
