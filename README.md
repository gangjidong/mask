// MASK 

#include<string.h>

#include <TimerOne.h>

unsigned long duration;

unsigned long starttime;

unsigned long endtime;

unsigned long sampletime_ms = 3000;

unsigned long lowpulseoccupancy = 0;

float ratio = 0;

float concentration = 0; 

unsigned char FL=0, second=0;

void setup()
{ Serial.begin(9600);		//블루투스 연결
  
  pinMode(8,INPUT);		//먼지센서 연결
  starttime = millis(); 
  
  pinMode(9,INPUT);  		//스타트 버튼  
  digitalWrite(9,1);	
  
  pinMode(10,OUTPUT);  		//FAN 제어 

digitalWrite(10,LOW);	   
  
  pinMode(11,OUTPUT);  		//MOTOR 제어 

digitalWrite(11,LOW);	
  
  pinMode(12,OUTPUT);  		//살균 LED 제어 

digitalWrite(12,LOW);	 
  
  Timer1.initialize(1000000); 	  //1초 셋팅
 Timer1.attachInterrupt(timerIsr);//attach the service routine here        
}
void timerIsr()			 //타이머 인터 럽트
{   second++;			// increment second
   if(second >19) 		//20초 경과시 정지
    {
     second=0;
     digitalWrite(10,0);	//팬 정지
     digitalWrite(11,0);	//진동모터 정지
     digitalWrite(12,0);	//살균램프 정지 
    }
   
}
void loop()
{   	 
// 먼지 센서 메이커 		

duration = pulseIn(pin, LOW);		//센서 초기화

lowpulseoccupancy += duration;	//센서 초기화

endtime = millis();			//센서 초기화

if ((endtime-starttime) > sampletime_ms) //측정이 시작되면

{ //먼지값을 계산하는 공식(센서 메이커 제공)   

ratio = (lowpulseoccupancy-endtime+starttime + sampletime_ms)/(sampletime_ms*10.0);  // Integer percentage 0=>100

concentration = 1.1*pow(ratio,3)-3.8*pow(ratio,2)+520*ratio+0.62; // using spec sheet curve

Serial.print("    DUST :");

Serial.print(concentration);  	//먼지 값을 폰에 전송

Serial.println(" mg");



lowpulseoccupancy = 0;		//센서 초기화

starttime = millis();		//센서 초기화


} 
   

if(!(digitalRead(9))) 	//버튼 

{digitalWrite(10,1);		//FAN 온

digitalWrite(11,1);		//MOTOR 온

digitalWrite(12,1);		//LED 살균등 온

} 

}
