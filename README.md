# STM32-
定时器

#include "sys.h"
#include "delay.h"
#include "usart.h"
#include "led.h"
#include "time.h"



//主函数
int main(void)
{
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	//设置系统分组2
	delay_init(168);		  //延时
	LED_Init();//led初始化

	TIM3_Int_Init(5000-1,8400-1);



	while(1)
		{
			LED0=!LED0;
			delay_ms(200);//延时200ms
		}



}

time.c函数

#include "time.h"
#include "led.h"




void TIM3_Int_Init(u16 arr,u16 psc)
{
	TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;
	NVIC_InitTypeDef  NVIC_InitStructure;
	
	
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3,ENABLE);//使能TIM3时钟
	
	
	
	TIM_TimeBaseInitStructure.TIM_ClockDivision=TIM_CKD_DIV1;
	TIM_TimeBaseInitStructure.TIM_CounterMode=TIM_CounterMode_Up;//向上计数
	TIM_TimeBaseInitStructure.TIM_Period=arr;自动重装载
	TIM_TimeBaseInitStructure.TIM_Prescaler=psc;//预分频
	//TIM_TimeBaseInitStructure.TIM_RepetitionCounter=
	TIM_TimeBaseInit(TIM3,&TIM_TimeBaseInitStructure);
	
	TIM_ITConfig(TIM3,TIM_IT_Update,ENABLE);//初始TIM3

	
	
	
	
//中断使能
	NVIC_InitStructure.NVIC_IRQChannel=TIM3_IRQn;
	NVIC_InitStructure.NVIC_IRQChannelCmd=ENABLE;
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority=0x01;
	NVIC_InitStructure.NVIC_IRQChannelSubPriority=0x03;
	NVIC_Init(&NVIC_InitStructure);

	TIM_Cmd(TIM3,ENABLE);//使能定时器3


}


//中断服务函数
void TIM3_IRQHandler (void)
{
	if(TIM_GetITStatus(TIM3,TIM_IT_Update)==SET)
	{
		LED1=!LED1;
	}
	TIM_ClearITPendingBit(TIM3,TIM_IT_Update);
}


//time.h函数

#ifndef _TIME_H
#define _TIME_H

#include "sys.h"




void TIM3_Int_Init(u16 arr,u16 psc);
void TIM3_IRQHandler (void);


#endif


