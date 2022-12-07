# MODEL-PM
## EX 1 -- EMU8086
```
name "ADDITION"
org 100h
MOV AX,05H;
MOV BX,02H;
ADD AX,BX;
MOV CX,AX;
MOV AX,00H;
HLT;

name "SUBTRACTION"
org 100h
MOV AX,06H;
MOV BX,04H;
SUB AX,BX;
MOV CX,AX;
MOV AH,00H;
HLT;

name "MULTIPLICATION"
org 700h
MOV AL,02H;
MOV BL,03H;
MUL BL;
MOV CL,AL;
MOV AL,00H;
HLT;

name "DIVISION"
org 100h
MOV AL,20H;
MOV BL,10H;
DIV BL;
MOV CL,AL;
MOV AL,00H;
HLT;
```
## EX 2 -- LED
```
#include <lpc214x.h> 
void delay_ms(unsigned int count)
{
  unsigned int j=0,i=0;
  for(j=0;j<count;j++)
  {
    for(i=0;i<3000;i++);
  }
}
int main() 
{
    PINSEL2 = 0x000000;  
    IO1DIR = 0xffffffff; 
    while(1)
    {
       IO1SET = 0xffffffff;     
         delay_ms(1000);
       IO1CLR = 0xffffffff;   
         delay_ms(1000);
    }
}
```


## EX 3 -- PUSH BUTTON
```
#include<LPC214x.h>  
#define led (1<<2)   
#define sw (1<<10)   
int main(void)
{
   unsigned int x;
   IO0DIR|= (~sw);  
   IO0DIR|= led;  
   while(1)
      {
	     x=IOPIN0 &sw; 
		 if(x==sw)
			 
         {
             IOCLR0|=led;
		 }
         else 
         {
            IOSET0|=led; 
		 }
	 }		 
 }

```
![image](https://user-images.githubusercontent.com/93427443/206143753-5bf6c764-3dcf-422c-bbb7-3cafd71f2b5c.png)

## EX 4 -- LCD
```
#include <lpc214x.h>
#include <stdint.h>
#include <stdlib.h>
#include <stdio.h>

void delay_ms(uint16_t j) 
{
	uint16_t x,i;
	for(i=0;i<j;i++)
	{
		for(x=0;x<6000;x++); 
	}
}

void LCD_CMD(char command)
{
	IO0PIN = ((IO0PIN & 0xFFFF00FF)|(command<<8));
	IO0SET = 0x00000040; 
	IO0CLR = 0x00000030; 
	delay_ms(2);
	IO0CLR= 0x00000040; 
	delay_ms(5);
}

void LCD_INIT(void)
{
	IO0DIR = 0x0000FFF0; 
	delay_ms(20);
	LCD_CMD(0x38);
	LCD_CMD(0x0C);
	LCD_CMD(0x06);
	LCD_CMD(0x01);
	LCD_CMD(0x80);
}

void LCD_STRING (char* msg)
{
	uint8_t i=0;
	while(msg[i]!=0)
	{
		IO0PIN = ((IO0PIN & 0xFFFF00FF)|(msg[i]<<8));
		IO0SET = 0x00000050; 
		IO0CLR = 0x00000020; 
		delay_ms(2);
		IO0CLR = 0x00000040; 
		delay_ms(5);
		i++;
	}
}

void LCD_CHAR (char msg)
{
	IO0PIN = ((IO0PIN & 0xFFFF00FF)|(msg<<8));
	IO0SET = 0x00000050; 
	IO0CLR = 0x00000020; 
	delay_ms(2);
	IO0CLR = 0x00000040;
	delay_ms(5);
}

int main(void)
{

	LCD_INIT();
	LCD_STRING("welcome to AI&DS");
	LCD_CMD(0xC0);
	LCD_STRING("212221230106");

	return 0;
}

```
![image](https://user-images.githubusercontent.com/93427443/206143988-97673b91-d291-4931-82cb-87944052a33b.png)

## EX 5 -- KEYPAD
```
#include <lpc21xx.h> 
#define RS (1<<0)
#define RW (1<<1)
#define E (1<<2)

void LCD_command(unsigned char command);
void  delay_ms(unsigned char time);
void LCD_data(unsigned char data);
void LCD_init() ;

int main(void)
{
 //PINSEL1 = 0x00000000;  
 //PINSEL2 = 0X00000000;  
 IODIR1= 0x00780000; 
 IODIR0= 0x00FF0007;  
 LCD_init(); 
 LCD_command(0x01); 
 while(1)
   {
      IOCLR1|=(1<<19);          
      IOSET1|=(1<<20)|(1<<21)|(1<<22);
      if(!(IOPIN1&(1<<16)))            
       {
        while(!(IOPIN1&(1<<16)));
        LCD_data('1');                          
       }
      if(!(IOPIN1&(1<<17)))
       {
         while(!(IOPIN1&(1<<17)));
          LCD_data('2'); 
       }
      if(!(IOPIN1&(1<<18)))
       {
         while(!(IOPIN1&(1<<18)));
          LCD_data('3'); 
       }
      IOCLR1|=(1<<20);
      IOSET1|=(1<<21)|(1<<22)|(1<<19);
      if(!(IOPIN1&(1<<16)))
{
        while(!(IOPIN1&(1<<16)));
         LCD_data('4'); 
      }
      if(!(IOPIN1&(1<<17)))
{
        while(!(IOPIN1&(1<<17)));
         LCD_data('5'); 
     }
      if(!(IOPIN1&(1<<18)))
{
        while(!(IOPIN1&(1<<18)));
         LCD_data('6'); 
     }
      IOCLR1|=(1<<21);
      IOSET1|=(1<<22)|(1<<20)|(1<<19);
      if(!(IOPIN1&(1<<16)))
{
        while(!(IOPIN1&(1<<16)));
         LCD_data('7'); 
     }
      if(!(IOPIN1&(1<<17)))
{
       while(!(IOPIN1&(1<<17)));
        LCD_data('8'); 
    }
      if(!(IOPIN1&(1<<18)))
{
        while(!(IOPIN1&(1<<18)));
         LCD_data('9'); 
}
      IOCLR1|=(1<<22);
      IOSET1|=(1<<19)|(1<<20)|(1<<21);
      if(!(IOPIN1&(1<<16)))
{
        while(!(IOPIN1&(1<<16)));
         LCD_data('*'); 
}
      if(!(IOPIN1&(1<<17)))
{
        while(!(IOPIN1&(1<<17)));
         LCD_data('0'); 
}
      if(!(IOPIN1&(1<<18)))
{
        while(!(IOPIN1&(1<<18)));
         LCD_data('#'); 
} 
   }
}
void  delay_ms(unsigned char time)    
{  
 unsigned int  i, j;
 for (j=0; j<time; j++)
 {
  for(i=0; i<8002; i++)
  {
  }
}
}
void LCD_command(unsigned char command)
{
 IOCLR0 = 0xFF<<16; 
 IOCLR0=RS;     
 IOCLR0=RW;    
 IOSET0=command<<16; 
 IOSET0=E; 
 delay_ms(10) ;   
 IOCLR0=E; 
}
void LCD_data(unsigned char data)
{
 IOCLR0 = 0xFF<<16; 
 IOSET0=RS;     
 IOCLR0=RW;    
 IOSET0= data<<16;  
 IOSET0=E; 
 delay_ms(10) ;   
 IOCLR0=E;  
 }

void LCD_init()
{
 LCD_command(0x38); 
 delay_ms(10) ;  
 LCD_command(0x0c); 
 delay_ms(10) ;  
    LCD_command(0x0e);  
    delay_ms(10) ;   
 LCD_command(0x01);  
 delay_ms(10) ;   
 LCD_command(0x80); 
  delay_ms(10) ;
 
}
```
![image](https://user-images.githubusercontent.com/93427443/206144175-f291a018-8f2e-4d26-a5b0-be3a4953e720.png)

## EX-6 -- ADC
```
#include <lpc214x.h>
#include "LCD.h"
#include "ADC.h"
unsigned int val;

int main()
{
    IO1DIR=0xffffffff;
    IO0DIR=0x00000000;
    PINSEL0=0x0300;
    VPBDIV=0x02;
    lcd_init();
    show("ADC Value : ");
    while(1) {
        cmd(0x8b);
        val=adc(0,6);
        dat((val/1000)+48);
        dat(((val/100)%10)+48);
        dat(((val/10)%10)+48);
        dat((val%10)+48);

    }
}
```
![image](https://user-images.githubusercontent.com/93427443/206144329-2635547d-e0a6-4693-b4dd-488e2dc02fbe.png)
![image](https://user-images.githubusercontent.com/93427443/206144452-012a1978-57cd-4eb0-983b-5758c35e74b2.png)
![image](https://user-images.githubusercontent.com/93427443/206144514-7bc61f67-b073-4d99-aa94-7161636403ab.png)

## EX-7 -- TEMPERATURE SENSOR
```
#include<lpc214x.h>
#include "LCD.h"
#include "ADC.h"

unsigned int val;

int main()
{
    IO1DIR=0xffffffff;
    IO0DIR=0x00000000;
    PINSEL0=0x0300;
    VPBDIV=0x02;
    lcd_init();
    show("ADC Value : ");
    while(1) {
        cmd(0x8b);
        val=adc(0,6);
        dat((val/1000)+48);
        dat(((val/100)%10)+48);
        dat(((val/10)%10)+48);
        dat((val%10)+48);
    }
}
```
```
Calculation of sensitivity 
% of sensitivity is   
S=  (T2-T1)/(A2-A1)*100
```
![image](https://user-images.githubusercontent.com/93427443/206144609-372fc9d9-1b8e-472e-b441-88f5829adedd.png)
![image](https://user-images.githubusercontent.com/93427443/206144657-36f2c69a-8be7-4fb5-bea5-1d63d612c2fc.png)
![image](https://user-images.githubusercontent.com/93427443/206144914-0a9696bc-53a6-41da-b0f8-6021ae8b9d0b.png)

## EX-8 -- 7 SEGMENT
```
#include <LPC214x.H>
unsigned char dig[]= {0x88,0xeb,0x4c,0x49,0x2b,0x19,0x18,0xcb,0x8,0x9,0xa,0x38,0x9c,0x68,0x1c,0x1e};

void delay(unsigned int count)
{
	int j=0,i=0;
	for(j=0;j<count;j++)
	{
		for(i=0;i<120;i++);
		
	}
	
}

int main(void)
{
	unsigned char count=0;
	unsigned int i=0;
	IO0DIR |= (1 << 11);
	IO0SET |= (1 << 11);
	IO0DIR |= 0x007F8000;
	
	while(1)
	{
		count++;
		if(count == 16)count=0;
		for(i=0;i<800;i++)
		{
			IO0CLR = 0x007f8000;
			IO0SET = (dig[count] << 15);
			 delay(200);
			
		}
		
	}}
```
![image](https://user-images.githubusercontent.com/93427443/206145063-97f9ff23-e5f3-4cc5-96ec-07e57380ce8f.png)

## EX-9 -- UART
```
#include <LPC213x.H>              
char a;
void uart0_init(){
  PINSEL0 = 0x00000005;           
  U0LCR = 0x83;              
  U0DLL = 97;                   
  U0LCR = 0x03;                                             
}
void uart0_putc(char c){
 while(!(U0LSR & 0x20)); 
 U0THR = c; // Send character
}
int uart0_getc (void)  {                     
  while (!(U0LSR & 0x01));
  return (U0RBR);
}
int main (void)  {                
  uart0_init();      
  while (1) {                          
  a=uart0_getc();
   uart0_putc(a);
  }                               
}
```
![image](https://user-images.githubusercontent.com/93427443/206145178-2ac24c31-6bf3-4a3d-90da-b9372854b3bc.png)
