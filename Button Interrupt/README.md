# Button Interrupt
  The purpose of this code is to flash an LED on the press of a button and maintain the LED until the button is pressed again.
This is achieved by utilizing the Port 1 Vector interrupt and taking advantage of the edge select feature on the development boards.
This code is designed to work on the MSP430G2553 and the MSP430F5529LP.

# Functionality
This code utilizes a Port 1 Interrupt in order to trigger the LED to blink. This event is caused when the respective button is pressed down (1.3 on the G2553 and 1.1 on the F5529LP). After pressing the button, the LED should remain on and can be turned off by pressing the same button again, triggering a flag on a high to low edge.

NOTE: A odd debounce was noticed in the F5229LP board for this section of the lab.

# Initilization

    WDTCTL = WDTPW | WDTHOLD;	
// Stop watchdog timer

    P1DIR |= 0X41;
//Sets Pin 1.0 (GREEN LED) and Pin 1.6 (RED LED) to be outputs

    P1OUT |= BIT3;
//Sets Pin 1.3 (Button) to enable pull up resistor

    P1REN |= BIT3; 
//Enables Pull up/down resistor

    P1OUT &= ~(0X41);
//Sets Pin 1.0 (GREEN LED) and 1.6 (RED LED) to 0 initially

    P1IE |= BIT3; 
//Interrupt is enabled on Pin 1.3 (Button)

    P1IES |= BIT3;
//Initially sets interrupt edge to go from high to low on PIN 1.3 (Button)

# Port 1 Interrupt

    P1OUT ^= 0X41; 
// Pin 1.0 (GREEN LED) and Pin 1.6 (RED LED) is toggled on/off

    P1IFG &= ~BIT3; 
//Flag clear on Pin 1.3 (Button)

# Differences between MSP430G2553 and MSP430F5529LP
Although mostly the same, below are the primary changes that can be observed:
- All instances of BIT3 become BIT1
- All instances of P1DIR = 0x41 become 0x01
- ALl instances of P1OUT = 0x41 become 0x01

# Results
Based on the code above and the full code in the readme's, each board is successfully able to trigger an LED toggle on a button press using interrupts.

