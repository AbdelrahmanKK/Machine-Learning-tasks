# Machine-Learning-tasks
This will contain mainly my machine learning tasks , assignments and projects


#include<stdint.h>
// GPIOA address
#define GPIOA_BASE  (uint32_t)0x40020400

//Get Moder register Address
#define MODER_OFFSET (uint32_t)0x00
#define MODER_ADD (*((uint32_t*)(GPIOA_BASE+MODER_OFFSET)))

//Get Output type register address
#define OYTPUTTYPE_OFFSET (uint32_t)0x04
#define OYTPUTTYPE_ADD (*((uint32_t*)(GPIOA_BASE+OYTPUTTYPE_OFFSET)))

//Get Output data register  address
#define OUTDATA_OFFSET (uint32_t)0x14
#define OUTDATA_ADD (*((uint32_t*)(GPIOA_BASE+OUTDATA_OFFSET)))

//Rcc Base Address
#define	RCC_BASE 0x40023800
// Get AHB1EN Register address
#define AHB1ENR_OFFSET 0x30
#define AHB1ENR_ADD (*((uint32_t*)(RCC_BASE+AHB1ENR_OFFSET)))
#define INPUT_DATA_OFFSET 0x10
#define INPUT_DATA_ADD  (*((uint32_t*)(GPIOA_BASE+INPUT_DATA_OFFSET)))
#define port_pull_up_pull_OFFSET 0x0C
#define port_pull_up_pull  (*((uint32_t*)(GPIOA_BASE+port_pull_up_pull_OFFSET)))


int debounce(){
	static uint16_t state=0;
	state =(state<<1)|((INPUT_DATA_ADD&(1<<3))>>3)|0xfe00;
	return (state ==0xff00);
}

int main(void)
{
	char leds=0;
	//Enable clock to GBIOA
	AHB1ENR_ADD |= (1 << 1);


	//PIN 3 INPUT MODE --> 00
	MODER_ADD &= ~(1 << 6);
	MODER_ADD &= ~(1 << 7);

	//setting pin 3 to pull up
	port_pull_up_pull &= ~(1 << 7);
	port_pull_up_pull|= (1 << 6);






	// Set Mode to output  of pin 4,5,6 GPIOA
	MODER_ADD |= (1 << 14);
	MODER_ADD &= ~(1 << 15);

	MODER_ADD |= (1 << 10);
	MODER_ADD &= ~(1 << 11);

	MODER_ADD |= (1 << 12);
	MODER_ADD &= ~(1 << 13);


	//setting push pull to output pins
	OYTPUTTYPE_ADD &=~(1<<7);
	OYTPUTTYPE_ADD &=~(1<<5);
	OYTPUTTYPE_ADD &=~(1<<6);





	// super loop
	while (1)
	{
		if(debounce()){

//		 OUTDATA_ADD^=(1 << 5);
//		 OUTDATA_ADD^=(1 << 6);
//
//		 OUTDATA_ADD^=(1 << 7);

			leds++;
			leds%=8;
			char temp=0;
			temp=((leds&(1<<0))>>0);
			if(temp) OUTDATA_ADD|= (1 << 5);
			else OUTDATA_ADD &=~(1<<5);

			temp=((leds&(1<<1))>>1);
			if(temp) OUTDATA_ADD|= (1 << 6);
			else OUTDATA_ADD &=~(1<<6);

			temp=((leds&(1<<2))>>2);
			if(temp) OUTDATA_ADD|= (1 << 7);
			else OUTDATA_ADD &=~(1<<7);


		}

	}

}


