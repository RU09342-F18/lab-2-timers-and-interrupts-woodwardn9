# TIMER A Blink
  The purpose of this code is to control the speed of one or two LEDs by pressing a button on the devolpment board.
This is achieved by utilizing an Up-Count clock and CCRO register to set the frequency. To achieve this task, both the MSP430G2553 and MSP430FR2311 boards were used for this code.

# Functionality
This code utilizes two interrupts; a Timer A0 interrupt and a Port1 interrupt. When a partiular value in the CCR0 value is met, the internal Timer A will trigger an interrupt, causing the LED(s) to toggle on or off. Then, the clock overflows and returns to 0. When the correct button is pressed (1.1 for the FR2311 and 1.3 for the G2553), the Port 1 interrupt will occur. In this instance, the value in the CCR0 register is decremented by sets of 100.

Determining the Frequency: 

(ACLK/8) / (2 * (2 * CCRO))

ACLK = 32786 HZ

ID_3 = divide by 8

2 * 2 = account for period and double hits

CCR0 = Value in CCR0



# Initlization
	WDTCTL = WDTPW | WDTHOLD;	
// Stop watchdog timer

	BCSCTL3 = LFXT1S_2; 
//Enables 32Khz crystal

	P1DIR |= 0X41;
  //Sets Pins 1.0 (GREEN LED) and 1.6 (RED LED) to be outputs
  
	P1REN |= BIT3; //Sets Pin 1.3 (Button) to be pull up/down enabled
	
 	 P1OUT |= BIT3; 
  //Sets Pin 1.3 (Button) to pull up resistor
	
	  P1OUT |= 0X41; 
  //Sets Pins 1.0 (GREEN LED) and 1.6 (RED LED) to be ON to begin
	
  	P1IE |= BIT3; 
  //Enables interrupt on Pin 1.3 (Button)
	
 	 P1IES |= BIT3; 
  //Enables high - to - low behavior on interrupt (Button press)
	
 	 CCTL0 = CCIE;
  // CCR0 interrupt enabled
	
 	 CCR0 =  1024; 
  //Sets value in the CCR0 register. This value is defined by the equation referenced in the top of the README. In this case, the frequency was set to be 1HZ
	
  	TACTL = TASSEL_1 + MC_1 + ID_3;  
  // ACLK selected, Up-Count Enabled, Divider = 8
  
  # Timer A Interrupt
  
  	P1OUT ^= 0X41; 
  //Toggles Pins 1.0 and 1.6 (GREEN and RED LEDs) on and off
  
  # Port 1 Interrupt
  
  	CCR0 -= 100; 
//Subtracts the value in the CCR0 register by 100, effectively speeding up the frequency

   	P1IFG &= ~BIT3; 
//Resets interrupt flag initially triggered
    
 # Differences between MSP430G2553 and MSP430FR2311
 Although mostly the same, below are the primary changes that can be observed:
 
 - All instances of BIT3 (Button) are changed to BIT1
 - All instances of P1OUT is changed to P1OUT = 0X01;
 - All instances of P1DIR is changed to P1DIR = 0X01;
 -All instances of TIMER A and values relying on Timer A are changed to Timer B, such as:
 TB0CCTL0, TB0CCR0, TBCTL, and TIMER0_B0_VECTOR
 - P2OUT = 0X01 is set to as an output on second LED 
 -P2OUT ^= 0X01 is added to the Timer interrupt
    
 # Results
 Essentially, once the button is pressed, the value in CCR0 will decrement by 100, speeding up the frequency. Then, once it goes beyond 0, an overflow occurs.
  

