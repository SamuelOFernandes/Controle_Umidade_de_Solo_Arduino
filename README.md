# Controle_Umidade_de_Solo_Arduino
Objetivo
O projeto consiste de através de um ARDUINO controlar e monitorar a umidade do solo, criando um sistema automático que faça a irrigação de acordo com  as configurações do sensor.
___________________________________________________________________________________
Funcionamento: No ARDUINO UNO R3 será executada uma rotina responsável por
processar as informações coletadas de um sensor de umidade, que estará sob a terra
e dentro de um vaso, caso seja detectado um valor de umidade abaixo do nível
programado, será acionada via relé, por 3 segundos, uma bomba d’água para irrigação
da terra, após 10 segundos dessa operação, que é o tempo de espera para que a terra
absorva a água, caso não seja atingido o valor de umidade ideal, a bomba será
acionada novamente.

Esse processo será verificado continuamente e executado até que a terra alcance a
condição e umidade programada. Durante todo o processo será possível verificar em
um display as informações de umidade, temperatura e acionamento da bomba de
irrigação.
________________________________________________________________________________________
Código do Projeto com detalhes e comentários :

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

#include <Wire.h>                 
#include <LiquidCrystal_I2C.h>  // Bibliotéca LCD


LiquidCrystal_I2C lcd(0x3f,2,1,0,4,5,6,7,3, POSITIVE); // Inicializa o display no endereco 0x3f
 

//=======================================================//OBSERVAÇÔES DE HARDWARE//=======================================================

//=// Valor sensor ( Máx: 1024"SECO")( Min: 370 "Supermer molhado") OBS: é preciso analisar/calibrar sempre quando for trocado o sensor//==
//=// A placa de relés funciona em nivel lógico baixo (Ligado = LOW || Desligado = HIGH)===============================================//==

//=========================================================================================================================================

const int sensorSW = 4   ;           // Sinal digital de detecção de umidade
const int sensor   = A1  ;           // Pino analógico em que está conectado o sensor
const int Smax     = 474   ;         // valor máximo do sensor de calibração para percentual
const int Smin     = 1024;           // valor mínimo do sensor de calibração para percentual
const int relay    = 2   ;           // Pino em que está conectado o Relay           


//===========================================================// AMOSTRAGEM //==============================================================

int       V              ;           // Armazenamento do valor analógico do sensor de umidade
int       porcen         ;           // Recebe o calculo percentual de umidade
int       porcen2        ;
int       ACbomb = 0     ;           // Variavel de chamada de bomba
String    bomb           ;           // Variavel de status da bomba
int       Recontador     ;           // Contador de quantas vezes foi regada a planta


//===================================================// CONFIGURAÇÃO DE IRRIGAÇÃO //=======================================================

unsigned long trega   = 3000 ;       // Tempo de rega em segundos                                     //AJUSTAR SEMPRE
unsigned long tespera = 10000;       // Tempo espera em segundos                                      //AJUSTAR SEMPRE
const int     regraR  = 631  ;       // Resistência (Em kohms) a partir da qual começa a regar        //AJUSTAR SEMPRE
//=========================================================================================================================================

void setup() {
  
  Serial.begin (9600)          ;     // Inicia comunicação serial
  pinMode      (relay,OUTPUT)  ;     // Configurar relay como saída
  digitalWrite (relay,HIGH)    ;     // Desliga bomba
  pinMode      (sensorSW,INPUT);     // Se (1 = umidade OK) se (0 = Falta Umidade)
  pinMode      (sensor, INPUT) ;     // Configura sensor como entrada
  lcd.begin    (16,2)          ;     // Capacidade do LCD (16 caracteres por linhas/ duas linhas)
  
}

void loop() {
  V      =  analogRead(sensor)                     ;       // Armazenamento da leitura sensor
  porcen =  100-((( V - Smin)*100)/(Smax - Smin))  ;       // Cálculo do percentual de umidade de acordo com dados analógicos analisados.

//================================================= Rotina de Monitoramento Serial=========================================================  
           
  Serial.print("Leitura do Sensor: ");  Serial.print(analogRead(sensor)); Serial.print("\t Saída digital sensor: "); Serial.print(digitalRead(sensorSW));Serial.print("\tUmidade "); Serial.print(porcen); Serial.print("%\t"); Serial.print(bomb);
  Serial.print("\t"); Serial.print(digitalRead(relay));
  Serial.println("\n")               ;    
  
//=============================================== Rotina de Amostragem e Decisão (Umidade % e Bomba on/off)================================
  lcd.setBacklight(HIGH);            // Aciona LED de luz de fundo do display LCD
  

  if(V>regraR){
    lcd.clear()               ;      // Limpa a mensagem de tela do LCD
    lcd.setCursor(0,0)        ;      // Posicionamento do cursor para receber escrita
    lcd.print("Bomba: ")      ;      // Escrita
    bomb = "ON"               ;      // Define String de Status da bomba
    lcd.setCursor(6,0)        ;      // Posicionamento do cursor para receber escrita
    lcd.print(bomb)           ;      // Escreve status da bomba
    lcd.setCursor(0,1)        ;      // Posicionamento do cursor para receber escrita
    lcd.print(V)              ;      // Escreve valor analógico de leitura do sensor
    lcd.setCursor(5,1)        ;      // Posicionamento do cursor para receber escrita
    lcd.print(" Umidade")     ;      // Escrita                ;
    delay(500)                ;      // tempo de amostagem do texto
    digitalWrite(relay,LOW)   ;      // Liga bomba
    delay(trega)              ;      // Tempod e operação
    digitalWrite(relay,HIGH)  ;      // Desliga bomba
    delay(tespera)            ;      // Tempo de espera
    Recontador = Recontador +1;
  }
  
    if(V<regraR){
    lcd.clear()               ;       // Limpa a mensagem de tela do LCD
    lcd.setCursor(0,0)        ;       // Posicionamento do cursor para receber escrita
    lcd.print("Bomba: ")      ;       // Escrita
    bomb = "OFF"              ;       // Define String de Status da bomba
    lcd.setCursor(6,0)        ;       // Posicionamento do cursor para receber escrita
    lcd.print(bomb)           ;       // Escreve status da bomba
    lcd.setCursor(0,1)        ;       // Posicionamento do cursor para receber escrita
    lcd.print(V)              ;       // Escreve valor analógico de leitura do sensor
    lcd.setCursor(5,1)        ;       // Posicionamento do cursor para receber escrita
    lcd.print(" Umidade")     ;       // Escrita 
    delay(500)                ;       // tempo de amostagem do texto
  }

}


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>










