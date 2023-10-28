# Projeto de Monitoramento de IoT :thermometer: :cloud:

Este projeto de código aberto é voltado para monitoramento de temperatura e umidade de um dispositivo IoT usando um sensor DHT11. Ele se comunica com a plataforma Arduino IoT Cloud para enviar e receber dados.

## Bibliotecas Necessárias :books:

Para este projeto, as seguintes bibliotecas são necessárias:

- `thingProperties.h`: Uma biblioteca personalizada que contém as definições das propriedades do dispositivo IoT.
- `DHT.h`: Uma biblioteca para trabalhar com o sensor DHT11.

## Configuração do Sensor :wrench:

Antes de começar, é importante configurar o sensor DHT:

- `DHTTYPE` é definido como DHT11, especificando o tipo de sensor usado (DHT11).
- `DHTPIN` é definido como 12, especificando o pino ao qual o sensor DHT está conectado.
- Um objeto DHT é criado usando essas configurações.

## Configuração Inicial no `setup()` :computer:

No bloco `setup()`, o seguinte é feito:

- Inicia a comunicação serial com uma taxa de 9600 baud.
- Define o pino 13 como saída, que é comumente usado para controlar o relé.
- Inicializa o sensor DHT.
- Chama a função `initProperties()` (que provavelmente está definida na biblioteca `thingProperties.h`) para inicializar as propriedades do dispositivo.
- Inicia a comunicação com o Arduino IoT Cloud.

## Loop Principal no `loop()` :repeat:

No loop principal, as seguintes ações são executadas repetidamente:

- Chama a função `ArduinoCloud.update()`, que atualiza a conexão com a nuvem, permitindo que o dispositivo envie e receba dados.
- Chama a função `leituraSensor()` para ler os dados do sensor.

## Função `onReleChange()` :electric_plug:

Esta função é chamada quando o estado do relé (variável `rele`) é alterado:

- Se o relé estiver ligado (valor 1), o pino 13 é definido como HIGH (ligado); caso contrário, é definido como LOW (desligado).

## Função `leituraSensor()` :thermometer: :droplet:

Esta função realiza a leitura da temperatura e umidade do sensor DHT:

- Lê a temperatura e a umidade do sensor DHT.
- Atualiza as variáveis `temperatura` e `umidade` com os valores lidos.
- Imprime a temperatura no monitor serial.
- Imprime o valor da variável `rele` no monitor serial.
- Adiciona um atraso de 1 segundo (1000 milissegundos) antes de ler novamente o sensor.

## Código do Projeto :page_with_curl:

Aqui está o código completo do projeto:

```cpp
#include "thingProperties.h"
#include "DHT.h"

#define DHTTYPE DHT11
#define DHTPIN 12
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  pinMode(13, OUTPUT);
  dht.begin();
  initProperties();
  ArduinoCloud.begin(ArduinoIoTPreferredConnection);
  setDebugMessageLevel(2);
  ArduinoCloud.printDebugInfo();
}

void loop() {
  ArduinoCloud.update();
  leituraSensor();
}

void onReleChange() {
  if (rele == 1) {
    digitalWrite(13, HIGH);
  } else {
    digitalWrite(13, LOW);
  }
}

void leituraSensor() {
  float t = dht.readTemperature();
  float h = dht.readHumidity();
  temperatura = t;
  umidade = h;
  delay(1000);
}
