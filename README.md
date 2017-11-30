# Controle_Umidade_de_Solo_Arduino
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
