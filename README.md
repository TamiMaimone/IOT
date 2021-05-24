# Medição da umidade e irrigação do solo
## Michel Victor Gurfinkiel, Tamiris Salzano Maimone, Willian Costa
#### Nosso projeto foi criado com a intensão de resolver o problema de excesso de umidade no solo das plantas, funcionando da seguinte forma. Um resistor de umidade do solo deve ser inserido no solo da planta para verificar a umidade  e caso estiver abaixo ou igual a 60% ativa a mini bomba submersa que irá irrigar o solo, além disso nosso projeto possui o módulo ESP8266 NodeMcu para a realização da conexão com o wi-fi e o uso do protocolo MQTT ligado ao broker thingspeak que recebe os dados do resistor de umidade.

### Software desenvolvido e documentação do codigo
```cpp
long prevMillisThingSpeak = 0;
int intervalThingSpeak = 1500;
//MQTT
#include <ESP8266WiFi.h>  
const char ssid[] = "rede.iot";
const char password[] = "***********";
WiFiClient client;
#include <ThingSpeak.h> 
const long CHANNEL = 1387329;
const char *WRITE_API = "GGK42YQS35J2DWG8";
//UMIDADE DA PLANTA
const int pinoSensor = A0; //PINO UTILIZADO PELO SENSOR
int valorLido; //VARIÁVEL QUE ARMAZENA O PERCENTUAL DE UMIDADE DO SOLO
int analogSoloSeco = 1023; //VALOR MEDIDO COM O SOLO SECO (VOCÊ PODE FAZER TESTES E AJUSTAR ESTE VALOR)
int analogSoloMolhado = 450; //VALOR MEDIDO COM O SOLO MOLHADO (VOCÊ PODE FAZER TESTES E AJUSTAR ESTE VALOR)
int percSoloSeco = 0; //MENOR PERCENTUAL DO SOLO SECO (0%)
int percSoloMolhado = 100; //MAIOR PERCENTUAL DO SOLO MOLHADO (100%)
void setup()
{
  Serial.begin(9600);
  Serial.println();
  Serial.println("Envia os dados do sensor para o ThingSpeak usando o módulo ESP8266");
  Serial.println();
  pinMode(D1,OUTPUT);
  WiFi.mode(WIFI_STA);//Modo Station
  ThingSpeak.begin(client); // Inicializar o ThingSpeak
}
void loop()
{
  //planta
 valorLido = constrain(analogRead(pinoSensor),analogSoloMolhado,analogSoloSeco); //MANTÉM valorLido DENTRO DO INTERVALO (ENTRE analogSoloMolhado E analogSoloSeco)
 valorLido = map(valorLido,analogSoloMolhado,analogSoloSeco,percSoloMolhado,percSoloSeco); //EXECUTA A FUNÇÃO "map" DE ACORDO COM OS PARÂMETROS PASSADOS
 Serial.print("Umidade do solo: "); //IMPRIME O TEXTO NO MONITOR SERIAL
 Serial.print(valorLido); //IMPRIME NO MONITOR SERIAL O PERCENTUAL DE UMIDADE DO SOLO
 Serial.println("%"); //IMPRIME O CARACTERE NO MONITOR SERIAL
 delay(1000);  //INTERVALO DE 1 SEGUNDO
 //BOMBA 
  if(valorLido <=60)
 { // Regar planta
 analogWrite(D1,LOW);   
 }
 else{ // Não regar
 analogWrite(D1,HIGH);
  }
 //Conecta ou Reconecta WiFi
 if (WiFi.status() != WL_CONNECTED){
  Serial.print("Atenção para conectar o SSID: ");
  Serial.print(ssid);
  while (WiFi.status() != WL_CONNECTED){
  WiFi.begin(ssid, password);
  Serial.print(".");
  delay(5000);   
  }
  Serial.println( "\nConectdo");
 }
  Serial.println("Umidade"); 
  Serial.println(valorLido);
  Serial.println( "%");  
  delay(1000);
  if(millis() - prevMillisThingSpeak > intervalThingSpeak){
    //configura os campos com valores
    ThingSpeak.setField(1,valorLido);=
    //Escreve no canal do ThingSpeak
    int x = ThingSpeak.writeFields(CHANNEL, WRITE_API);
    if(x==200){
      Serial.println("Update realizado com sucesso");
    }
    else{
      Serial.println("Problema no canal - erro HTTP" + String(x));
    }
    prevMillisThingSpeak = millis();
  }
  }
```
### Descrição do Hardware utilizado:
- Módulo ESP8266 NODEMCU;
- jumpers (macho-macho e macho-femea);
- Protoboard branco de 830 pontos;
- Resistor de umidade solo;
- Modulo Relê 5V;
- Arduino Uno R3;
- Cabo USB (para conexão do nodemcu com o notebook);
- Cabo USB para conexão do Arduino com o powerbank;
- Powerbank(para fornecer a energia para o Arduino);
- Mini bomba submersa 5V;
- Mangueira de PVC (1 metro);
- Planta;
- Pote com agua;

### Descrição do Software utilizado
- Arduino IDE;
- ThingSpeak;

### Protocolos e módulos de comunicação
- Protocolo usado: MQTT;
- Módulo de comunicação: ESP8266 NODEMCU;

### Links
- MAIMONE, Tamiris. Youtube. Medidor de umidade e irrigação do solo, (2021). Disponível em: https://www.youtube.com/watch?v=fd_Dk314_LI. Acesso em: 22 maio 2021.
- Thingspeak. Mediçaõ da umidade do solo, (2021). Disponível em: https://thingspeak.com/channels/1387329. Acesso em: 22 maio 2021.
