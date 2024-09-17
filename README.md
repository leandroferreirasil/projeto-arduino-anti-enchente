# Alarme de Nível de Água com Arduino

## Descrição

Este projeto é um alarme de nível de água que utiliza um sensor de nível, um buzzer, uma bomba d'água e um LED vermelho para monitorar e controlar o nível de água em um reservatório. Quando o nível de água atinge um determinado limite, a bomba é acionada, e o buzzer emite um alarme sonoro intermitente para alertar sobre o alto nível de água.

## Componentes

- **Sensor de Nível de Água**: Detecta o nível da água.
- **Buzzer**: Emite um som contínuo quando o nível de água atinge o limite.
- **Bomba d'água**: Aciona para esvaziar o reservatório.
- **LED Vermelho**: Indica quando o nível de água está alto.

## Especificações

- **Microcontrolador**: Arduino (compatível com qualquer modelo que suporte o Timer2, como o Arduino Uno).
- **Timer2**: Utilizado para gerar interrupções periódicas e alternar o estado do buzzer.

## Funcionamento

1. **Inicialização**:
   - O `setup()` configura os pinos de entrada e saída e inicializa o Timer2.

2. **Loop Principal**:
   - O `loop()` chama a função `read_sensor()` continuamente para verificar o nível de água.

3. **Função `read_sensor()`**:
   - Se o nível de água atinge o limite, a bomba e o LED são ligados, e o buzzer emite um alarme intermitente.
   - Caso contrário, a bomba, o LED e o buzzer são desligados.

4. **Interrupção do Timer2**:
   - O Timer2 é configurado para gerar interrupções a cada segundo. Quando o Timer2 transita, o estado do buzzer é alternado para criar o som de alarme intermitente.

## Instalação e Uso

1. **Componentes Necessários**:
   - Arduino
   - Sensor de Nível de Água
   - Buzzer
   - Bomba d'água
   - LED Vermelho
   - Resistores e fiação apropriada

2. **Conectar os Componentes**:
   - Conecte o sensor de nível ao pino digital 5 do Arduino.
   - Conecte o buzzer ao pino digital 6.
   - Conecte a bomba d'água ao pino digital 7.
   - Conecte o LED vermelho ao pino digital 8.

3. **Carregar o Código**:
   - Abra o Arduino IDE.
   - Copie e cole o código fornecido no arquivo `main.ino`.
   - Selecione o tipo de placa e a porta correta.
   - Faça o upload do código para o seu Arduino.

## Código

O código-fonte está disponível no arquivo `main.ino`. O código configura o Timer2 para gerar interrupções periódicas e controla o buzzer, a bomba e o LED com base no nível de água detectado.

```cpp
/*
   Curso de Arduino e AVR 105
   
   WR Kits Channel

   Alarme de Nível de Água

   Autor: Eng. Wagner Rambo  Data: Fevereiro de 2017

   www.wrkits.com.br | facebook.com/wrkits | youtube.com/user/canalwrkits
*/

// =============================================================================================================
// --- Mapeamento de Hardware ---
#define SENSOR_PIN 5         // Sensor de nível de água
#define BUZZER_PIN 6         // Buzzer contínuo 5V
#define BOMBA_PIN 7          // Bomba d'água
#define LED_RED_PIN 8        // LED vermelho

// =============================================================================================================
// --- Protótipo das funções auxiliares ---
void read_sensor();            // Função para leitura do sensor e acionamento da bomba

// =============================================================================================================
// --- Declaração de variáveis globais ---
volatile short counterT2 = 0; // Contador auxiliar para o Timer2

// =============================================================================================================
// --- Interrupção ---
ISR(TIMER2_OVF_vect)
{
    counterT2++;               // Incrementa contador auxiliar do Timer2

    if (counterT2 >= 100) {    // Chegou a 100 contagens?
        counterT2 = 0;         // Reinicia o contador

        // Inverte estado do buzzer (soa alarme)
        digitalWrite(BUZZER_PIN, !digitalRead(BUZZER_PIN));
    }
}

// =============================================================================================================
// --- Configurações iniciais ---
void setup()
{
    // -- Direção dos I/Os --
    pinMode(BUZZER_PIN, OUTPUT);     // Saída para o buzzer
    pinMode(BOMBA_PIN, OUTPUT);      // Saída para a bomba d'água  
    pinMode(SENSOR_PIN, INPUT_PULLUP); // Entrada para o sensor com pull-up interno 
    pinMode(LED_RED_PIN, OUTPUT);    // Saída para LED vermelho

    // -- Inicialização das saídas --
    digitalWrite(BUZZER_PIN, LOW);   // Desliga buzzer
    digitalWrite(BOMBA_PIN, LOW);    // Desliga bomba
    digitalWrite(LED_RED_PIN, LOW);  // Desliga LED vermelho

    // -- Registradores de configuração do Timer2 --
    TCCR2A = 0x00;                // Timer operando em modo normal
    TCCR2B = (1 << CS22) | (1 << CS21) | (1 << CS20); // Prescaler 1:1024
    TCNT2  = 0x00;                // Inicializa o contador do Timer2
    TIMSK2 = (1 << TOIE2);        // Habilita interrupção do Timer2
}

// =============================================================================================================
// --- Loop Infinito ---
void loop()
{
    read_sensor();
}

// =============================================================================================================
// --- Funções Auxiliares ---
void read_sensor()              // Função para leitura do sensor e acionamento da bomba
{
    if (digitalRead(SENSOR_PIN)) { // Atingiu o nível de água limite?
        digitalWrite(BOMBA_PIN, HIGH);   // Liga bomba
        digitalWrite(LED_RED_PIN, HIGH); // Liga LED
        digitalWrite(BUZZER_PIN, HIGH);  // Liga buzzer
    } else {
        digitalWrite(BUZZER_PIN, LOW);   // Desliga buzzer
        digitalWrite(BOMBA_PIN, LOW);    // Desliga bomba
        digitalWrite(LED_RED_PIN, LOW);  // Desliga LED
    }
}
