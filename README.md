#include <PubSubClient.h>
#include <UIPEthernet.h>
#include <utility/logging.h>
#include <SPI.h>

 //Define o endereço MAC que será utilizado
byte mac[] = {0xBe, 0xF0, 0x4F, 0x95, 0x3B, 0xFC};
//Variavel utilizada para enviar as mensagens utilizando o cliente MQTT
boolean mensagem;
int pino2= 2; // Cria uma variável para o Pino 2
bool estado_sensor; // Cria uma variável para armazenar o estado do sensor

//Iniciar o cliente Ethernet
EthernetClient client;

//Inicia o client MQTT
PubSubClient mqttClient(client);

void setup (){
  //Inicia o controlador Ethernet e solicita um IP para o servidor de DHCP
  Ethernet.begin(mac);

  //Iniciar o monitor Serial
  Serial.begin(9600);
  
   //Definir o IP e Porta TCP do Broker MQTT que vamos utilizar
mqttClient.setServer("54.210.160.207", 1883);

//Exibe no Monitor Serial as informações sobre o IP do Arduino
  Serial.print("O IP do Arduino e: ");
  Serial.println(Ethernet.localIP());

  //Exibe no Monitor Serial as informações sobre a Máscara de Rede do Arduino
  Serial.print("A Mascara de Rede do Arduino e:");
  Serial.println(Ethernet.subnetMask());

  //Exibe no Monitor Serial as informações sobre o Gateway do Arduino
  Serial.print("O Gateway do Arduino e: ");
  Serial.println(Ethernet.gatewayIP());
  //Aguarda 10 segundos

  pinMode (pino2, INPUT_PULLUP); // Coloca o Pino 2 como Entrada
  
  }
  
void loop() {
    
    //Define o nome cliente MQTT e efetua a conexão com o servidor.
    mqttClient.connect("LuizG");

   estado_sensor = digitalRead (pino2); // Efetua a leitura do Pino 2 e armazena o valor discriminado na variável

  if (estado_sensor == 0) {
    Serial.println ("O rack esta fechado");
    mensagem = mqttClient.publish("Rack", "O rack esta fechado");
  }
  else {
    Serial.println ("O rack está aberto");
      mensagem = mqttClient.publish("Rack", "O rack esta aberto");
    
  }
    Serial.println(mensagem);
    
    mqttClient.loop();
    
   }
