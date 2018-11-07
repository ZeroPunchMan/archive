# MCU休眠的处理
所用MCU为STM32,其他MCU同理.
## 遇到的问题
应用场景逻辑如下:
```
1.主板休眠时需要处理看门狗,所以定期唤醒喂狗.
2.根据1,需要判断唤醒源,如果是喂狗,则继续休眠;如果是系统激活信号(如EXTI),则唤醒后开始工作.
```

目前的处理代码大致如下:
```
//RTC报警中断,用来喂狗
void RTCAlarm_IRQHandler(void)  												
{  
    if(RTC_GetITStatus(RTC_IT_ALR) != RESET)  
    {  
        EXTI_ClearITPendingBit(EXTI_Line17);  
        /* Set the RTC alarm flag */  
        g_bRTCAlarm = TRUE;  													//RTC报警标志
        ... //喂狗等操作
    }  
}

//外部中断,通知系统唤醒后开始工作
void EXTI1_IRQHandler(void)															
{
    if(EXTI_GetFlagStatus(EXTI_Line1) != RESET)
    {
        EXTI_ClearITPendingBit(EXTI_Line1);
        g_bRTCAlarm = FALSE;														//清RTC闹钟报警标志位
    }
}

void SLEEP_TO_ON(void)
{
    ...
ENTERSTOPMODE:   
    __WFI();  //休眠

    if(g_bRTCAlarm == TRUE)  // 是否是RTC闹钟中断唤醒
    {  
        g_bRTCAlarm = FALSE; 													//清RTC报警标志位
        goto ENTERSTOPMODE; 	 //继续休眠
    }  
    ...
}
```

__上面的代码通过g_bRTCAlarm来标志唤醒源,但如果在EXTI中断后,在if(g_bRTCAlarm)之前触发了RTC中断,就会导致g_bRTCAlarm为TRUE,从而错过此次唤醒.__

## 解决办法
只要用一个标志变量,在休眠之前清除,仅在收到激活信号(如EXTI1)时设置,再使用此变量判断即可.代码如下:
```
//RTC报警中断,用来喂狗
void RTCAlarm_IRQHandler(void)  												
{  
    if(RTC_GetITStatus(RTC_IT_ALR) != RESET)  
    {  
        EXTI_ClearITPendingBit(EXTI_Line17);  
        //这里不设置唤醒标志
        ... //喂狗等操作
    }  
}

//外部中断,通知系统唤醒后开始工作
void EXTI1_IRQHandler(void)															
{
    if(EXTI_GetFlagStatus(EXTI_Line1) != RESET)
    {
        EXTI_ClearITPendingBit(EXTI_Line1);
        g_bWakeUp = TRUE;			//设置唤醒标志
    }
}

void SLEEP_TO_ON(void)
{
    ...
    g_bWakeUp = FALSE;			//清唤醒标志
ENTERSTOPMODE:   
    __WFI();  //休眠

    if(g_bWakeUp == FALSE)  //没有唤醒信号
    {  
        goto ENTERSTOPMODE; 	 //继续休眠
    }

    ... //g_bWakeUp为TRUE,系统唤醒
}
```
这样休眠后,收到EXTI1时,就会设置g_bWakeUp,从而唤醒MCU.
