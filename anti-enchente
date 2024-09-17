/*
   Curso de Arduino e AVR 105
   
   WR Kits Channel


   Alarme de Nível de Água
   
    
   Autor: Eng. Wagner Rambo  Data: Fevereiro de 2017
   
   www.wrkits.com.br | facebook.com/wrkits | youtube.com/user/canalwrkits
   
*/


// =============================================================================================================
// --- Mapeamento de Hardware ---
#define     sensor     5               //Sensor de nível de água
#define     buzzer     6               //Buzzer contínuo 5V
#define     bomba      7               //Bomba d´agua
#define     led_red    8               // led red

 
// =============================================================================================================
// --- Protótipo das funções auxiliares ---
void read_sensor();                    //função para leitura do sensor e acionamento da bomba 


// =============================================================================================================
// --- Declaração de variáveis globais ---
short         counterT2 = 0x00;        //contador auxiliar para o Timer2


// =============================================================================================================
// --- Interrupção ---
ISR(TIMER2_OVF_vect)
{
    TCNT2 = 0x64;                      // Reinicializa o registrador do Timer2
    counterT2++;                       //incrementa contador auxiliar do Timer2

    if(counterT2 == 0x64)              //chegou em 100d?
    {                                  //sim...
      counterT2 = 0x00;                //reinicia

      //inverte estado do buzzer (soa alarme)
      digitalWrite(buzzer, !digitalRead(buzzer));
      
    } //end if counterT2
    
    
} //end ISR
    

// =============================================================================================================
// --- Configurações iniciais ---
void setup()
{ 
     
  // -- Direção dos I/Os --
  pinMode(buzzer, OUTPUT);             //saída para o buzzer
  pinMode(bomba,  OUTPUT);             //saída para a bomba dagua  
  pinMode(sensor, INPUT_PULLUP);       //entrada para o sensor com pull-up interno 
  pinMode(led_red, OUTPUT);            //liga led


  // -- Inicialização das saídas --
  digitalWrite(buzzer, LOW);           //desliga buzzer
  digitalWrite(bomba,  LOW);           //desliga bomba
  digitalWrite(led_red,  LOW);         //desliga led



  // -- Registradores de configuração do Timer2 --
  TCCR2A = 0x00;                       //Timer operando em modo normal
  TCCR2B = 0x07;                       //Prescaler 1:1024
  TCNT2  = 0x64;                       //10 ms overflow again
  TIMSK2 = 0x00;                       //Interrupção desabilitada inicialmente
  
   
} //end setup


// =============================================================================================================
// --- Loop Infinito ---
void loop()
{
    
    read_sensor();


} //end loop


// =============================================================================================================
// --- Funções Auxiliares ---
void read_sensor()                     //função para leitura do sensor e acionamento da bomba
{
   if(digitalRead(sensor))             //atingiu o nível de água limite?
   {                                   //sim...
      digitalWrite(bomba, HIGH);        //liga bomba
      digitalWrite(led_red, HIGH);      //liga led
      digitalWrite(buzzer, HIGH);        //liga buzzer
      TIMSK2 = 0x01;                   //habilita interrupção do timer2   
    
   } //end if sensor

   else                                //senão...
   {                    
      digitalWrite(buzzer, LOW);       //desliga buzzer             
      digitalWrite(bomba, LOW);       //desliga bomba
      digitalWrite(led_red, LOW);     //desliga led_red
      TIMSK2 = 0x00;                   //desabilita interrupção do timer2   
    
   } //end else
  
} //end read_sensor
