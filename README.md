# Controle_Umidade_de_Solo_Arduino

Integrantes do Grupo :

Paulo Germano Ramos Villegas - RA: 20896906
Carlos Eduardo Custódio do Carmo - RA : 20980204
Edeval Damiati Neto - RA : 20948505 
Samuel Oliveira Fernandes dos Santos - RA : 20408096 
Gustavo Scabuzzi - RA : 20948506 


Objetivo
O projeto consiste de através de um ARDUINO controlar e monitorar a umidade do solo, criando um 
sistema automático que faça a irrigação de acordo com  as configurações do sensor.
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

Código do Projto de Controle de Umidade de Solo

Abaixo segue o fluxograma da construção do código:

![alt text](https://github.com/SamuelOFernandes/Controle_Umidade_de_Solo_Arduino/blob/master/Fluxograma%20Codigo%20Ardu%C3%ADno%20Controle_Umidade_%20Solo.PNG)


Pode ser conferida a orientação da programação :

![alt text](https://github.com/SamuelOFernandes/Controle_Umidade_de_Solo_Arduino/blob/master/orienta%C3%A7%C3%A3o.PNG)


Código do Projeto com detalhes e comentários :

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

#include <Wire.h>                 
#include <LiquidCrystal_I2C.h>  // Bibliotéca LCD


LiquidCrystal_I2C lcd(0x3f,2,1,0,4,5,6,7,3, POSITIVE); // Inicializa o display no endereco 0x3f
 

//=======================================================//OBSERVAÇÔES DE HARDWARE//=======================================================

//=// Valor sensor ( Máx: 1024"SECO")( Min: 370 "Super molhado") OBS: é preciso analisar/calibrar sempre quando for trocado o sensor//==
//=// A placa de relés funciona em nível lógico baixo (Ligado = LOW || Desligado = HIGH)===============================================//==

//=========================================================================================================================================

const int sensorSW = 4   ;           // Sinal digital de detecção de umidade
const int sensor   = A1  ;           // Pino analógico em que está conectado o sensor
const int Smax     = 370 ;         // valor máximo do sensor de calibração para percentual
const int Smin     = 1024;           // valor mínimo do sensor de calibração para percentual
const int relay    = 2   ;           // Pino em que está conectado o Relay           


//===========================================================// AMOSTRAGEM //==============================================================

int       V              ;           // Armazenamento do valor analógico do sensor de umidade
int       porcen         ;           // Recebe o calculo percentual de umidade
String    bomb           ;           // Variavel de status da bomba
int       Recontador     ;           // Contador de quantas vezes foi regada a planta


//===================================================// CONFIGURAÇÃO DE IRRIGAÇÃO //=======================================================

unsigned long trega   = 3000 ;       // Tempo de rega em segundos                                     //AJUSTAR SEMPRE
unsigned long tespera = 10000;       // Tempo espera em segundos                                      //AJUSTAR SEMPRE
const int     regraR  = 45  ;        // Resistência (Em kohms) a partir da qual começa a regar        //AJUSTAR SEMPRE
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
  V      =  analogRead(sensor) ;     // Armazenamento da leitura sensor
  porcen =  (Smin-V)/6.54      ;     // Cálculo do percentual de umidade de acordo com dados analógicos analisados.

//================================================= Rotina de Monitoramento Serial=========================================================  
           
  Serial.print("Leitura do Sensor: ");  Serial.print(analogRead(sensor)); Serial.print("\t Saida digital sensor: "); Serial.print(digitalRead(sensorSW));Serial.print("\tUmidade "); Serial.print(porcen); Serial.print("%\t"); Serial.print(bomb);
  Serial.print("\t"); Serial.print(digitalRead(relay));
  Serial.println("\n")               ;    
  
//=============================================== Rotina de Amostragem e Decisão (Umidade % e Bomba on/off)================================
  lcd.setBacklight(HIGH);            // Aciona LED de luz de fundo do display LCD
  

  if(porcen<regraR){
    lcd.clear()               ;      // Limpa a mensagem de tela do LCD
    lcd.setCursor(0,0)        ;      // Posicionamento do cursor para receber escrita
    lcd.print("Bomba: ")      ;      // Escrita
    bomb = " ON"              ;      // Define String de Status da bomba
    lcd.setCursor(6,0)        ;      // Posicionamento do cursor para receber escrita
    lcd.print(bomb)           ;      // Escreve status da bomba
    lcd.setCursor(0,1)        ;      // Posicionamento do cursor para receber escrita
    lcd.print(porcen)         ;      // Escreve valor analógico de leitura do sensor
    lcd.setCursor(3,1)        ;      // Posicionamento do cursor para receber escrita
    lcd.print("% Umidade")    ;      // Escrita                ;
    delay(500)                ;      // tempo de amostagem do texto
    digitalWrite(relay,LOW)   ;      // Liga bomba
    delay(trega)              ;      // Tempod e operação
    digitalWrite(relay,HIGH)  ;      // Desliga bomba
    delay(tespera)            ;      // Tempo de espera
    Recontador = Recontador +1;
  }
  
    if(porcen>regraR){
    lcd.clear()               ;       // Limpa a mensagem de tela do LCD
    lcd.setCursor(0,0)        ;       // Posicionamento do cursor para receber escrita
    lcd.print("Bomba: ")      ;       // Escrita
    bomb = " OFF"             ;       // Define String de Status da bomba
    lcd.setCursor(6,0)        ;       // Posicionamento do cursor para receber escrita
    lcd.print(bomb)           ;       // Escreve status da bomba
    lcd.setCursor(0,1)        ;       // Posicionamento do cursor para receber escrita
    lcd.print(porcen)         ;       // Escreve valor analógico de leitura do sensor
    lcd.setCursor(3,1)        ;       // Posicionamento do cursor para receber escrita
    lcd.print("% Umidade")    ;       // Escrita 
    delay(500)                ;       // tempo de amostagem do texto
  }

}

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>




Layout do projeto


![alt text](https://github.com/SamuelOFernandes/Controle_Umidade_de_Solo_Arduino/blob/master/layout.PNG)
 



	Componentes Utilizados

⦁	 Placa Uno R3
⦁	 Cabo usb  arduíno
⦁	Relé 8 canais 5v
⦁	Jumpers macho/macho e Jumpers macho/fêmea
⦁	 Display lcd 16x2 12c backlight azul 
⦁	Módulo serial i2c para display lcd 
⦁	Protoboard 830 pontos 
⦁	Módulo sensor de umidade de solo



Descrição Técnica dos Itens Utilizados


> Placa Uno R3
 
Especificações: 

- Microcontrolador: ATmega328  
- Tensão de Operação: 5V
- Tensão de Entrada: 7-12V 
- Portas Digitais: 14 (6 podem ser usadas como PWM) 
- Portas Analógicas: 6
- Corrente Pinos I/O: 40mA
- Corrente Pinos 3,3V: 50mA
- Memória Flash: 32KB (0,5KB usado no bootloader) 
- SRAM: 2KB
- EEPROM: 1KB
- Velocidade do Clock: 16MHz 

                     
> Cabo USB Arduíno


Especificações: 

- Cabo USB 2.0
-  Conectores:  A Macho X B Macho


>Relé 5v 8 canais 


Especificações:


– Modelo: SRD-05VDC-SL-C 

– Tensão de operação: 5VDC

– Permite controlar cargas de 220V AC

– Corrente típica de operação: 15~20mA

– LED indicador de status

– Pinagem: Normal Aberto, Normal Fechado e Comum

– Tensão de saída: (30 VDC a 10A) ou (250VAC a 10A)

– Furos de 3mm para fixação nas extremidades da placa

– Tempo de resposta: 5~10ms
– Dimensões: 135 x 52 x 20mm

>Jumpers Macho/Macho e Jumpers Macho/Fêmea                                                                

Especificações:

–  Conector Macho x Macho

– Conector Macho x Fêmea

– Fios de 24 AWG


>Display LCD 16x2 12c Backlight Azul

 
Especificações:
- Cor backlight: Azul
- Cor escrita: Branca 
- Adaptador display I2C integrado
- Potenciômetro para ajuste do contraste
- Tensão de operação: 5V 
- Linhas: 2 
- Colunas: 16
- Interface: I2C 
- Dimensões: 80 x 36 x 12mm 
- Área visível: 64,5 x 16mm  
Pinos: 
- SDA 
- SCL 
- Vcc 
- GND 


>Módulo serial i2c para display LCD 

Especificações:
- Endereço I2C: 0x20-0x27 (Padrão 0x20 mas pode ser modificado) 
- Compatível com Display LCD 16x2 e LCD 20x4 
- Tensão de operação: 5V
- Dimensões: 55 x 23 x 14mm 
- Peso: 5g 









>Protoboard 830 pontos


Especificações:
- Furos: 830 
- Faixa de Temperatura: -20 a 80°C 
- Para terminais e condutores de 0,3 a 0,8 mm (20 a 29 AWG)
- Resistência de Isolamento: 100MΩ min. 
- Tensão Máxima: 500v AC por minuto 
- Dimensões: 165mm x 57mm x 10mm 




> Módulo sensor de umidade de solo


Especificações: 
- Tensão de Operação: 3,3-5V 
- Sensibilidade ajustável via potenciômetro
- Saída Digital e Analógica
- Led indicador para tensão (vermelho)
- Led indicador para saída digital (verde) 
- Comparador LM393
- Dimensões PCB: 3x1,5 cm
- Dimensões Sonda: 6x2 cm
 

Pinagem: 
- VCC: 3,3-5v 
- GND: GND 
- D0: Saída Digital 
- A0: Saída analógica










