<h1>RTOS</h1>

<h2>O que é RTOS?</h2>

<p>RTOS significa "Real-Time Operating System" (Sistema Operacional de Tempo Real). É um tipo de sistema operacional projetado para suportar aplicações em tempo real, onde as tarefas precisam ser executadas dentro de prazos estritos e previsíveis.

<h2>O que é a multitarefa no RTOS?</h2>

<p>Multitarefa no RTOS refere-se à capacidade do sistema operacional de executar várias tarefas concorrentemente. No contexto do RTOS, as tarefas são pequenos programas ou threads que executam funções específicas. O RTOS permite que essas tarefas sejam executadas de forma intercalada ou simultânea, compartilhando os recursos do sistema de forma eficiente. O RTOS é responsável por atribuir prioridades às tarefas e determinar quando cada tarefa deve ser executada.

<h2>O que é a sincronização de tarefas no RTOS?</h2>

<p>Sincronização de tarefas é o processo de coordenar a execução de tarefas em um ambiente multitarefa. Quando várias tarefas compartilham recursos, como memória, dispositivos de E/S ou variáveis compartilhadas, é necessário sincronizar suas operações para evitar condições de corrida, inconsistências ou erros. A sincronização de tarefas envolve o uso de mecanismos como semáforos, mutexes e filas para controlar o acesso e garantir a consistência dos dados.

<h2>Reação em tempo real:</h2>

<p>Utilizando a variável volátil e global 'temperature', podemos exibir no LCD o valor calculado da temperatura e também, caso os valor seja igual a 26.00, acender o LED verde. Todo esse processo das funções (ou tarefas) ocorre simultaneamente, de acordo com a prioridade de cada tarefa. 

<h2>Vantagens do uso do RTOS:</h2>

<ol>
<li>Determinismo: O RTOS fornece garantias temporais, permitindo que as tarefas sejam executadas dentro de prazos previsíveis. Isso é fundamental para aplicações em tempo real, onde a resposta rápida é essencial.</li>

<li>Multitarefa: O RTOS permite a execução de várias tarefas concorrentemente, compartilhando eficientemente os recursos do sistema.</li>

<li>Escalabilidade: Os RTOS são projetados para suportar sistemas embarcados de diferentes tamanhos e complexidades. Eles podem ser configurados para atender às necessidades específicas de uma aplicação, permitindo um uso eficiente de recursos.</li>

<li>Sincronização e comunicação: Os RTOS fornecem mecanismos de sincronização e comunicação entre tarefas, facilitando a coordenação e o compartilhamento de informações.</li>

<li>Preemptividade: Os RTOS preemptivos permitem que tarefas de alta prioridade interrompam tarefas de baixa prioridade, garantindo a execução de tarefas críticas em tempo hábil.</li>

<li>Confiabilidade: Os RTOS são projetados para serem robustos e confiáveis, permitindo a detecção e recuperação de erros.</li>
</ol>

<h2>Código e Circuito:</h2>

    #include <Arduino.h>
    #include <Arduino_FreeRTOS.h>
    #include <LiquidCrystal.h>

    LiquidCrystal lcd(12, 11, 10, 9, 8, 7);

    // Protótipos para as Tarefas:
    void TaskReadTemperature(void *pvParameters);
    void TaskLed1(void *pvParameters);
    void TaskLedTemp(void *pvParameters);

    // Variável para armazenar o valor da temperatura:
    volatile float temperature = 0.0;

    #define LED_1 6 // LED azul.
    #define LED_2 5 // LED verde. 

    void setup()
    {
    Serial.begin(9600);
    lcd.begin(16, 2);

    pinMode(LED_1, OUTPUT);
    pinMode(LED_2, OUTPUT);

    // Tarefas:
    xTaskCreate(
        TaskReadTemperature,
        "ReadTemperature",
        128,
        NULL,
        3,
        NULL);

    xTaskCreate(
        TaskLed1,
        "Led1",
        128,
        NULL,
        3,
        NULL);

    xTaskCreate(
        TaskLedTemp,
        "LedTemp",
        128,
        NULL,
        3,
        NULL);
    }

    void loop()
    {
    // Nada aqui!
    }

    void TaskReadTemperature(void *pvParameters)
    {

    (void)pvParameters;
    float sensorValue = 0.0;
    for (;;)
    {
        // Aqui você normalmente leria o valor do sensor de temperatura, mas por simplicidade, vamos apenas simular um valor para a temperatura, variando de -10 a 40.

        sensorValue = -10.0 + (rand() % 51); // Gera um número aleatório entre -10 e 40.
        temperature = sensorValue;
        vTaskDelay(2000 / portTICK_PERIOD_MS); // Aguarda por 2 segundos.
        lcd.setCursor(0, 0);
        lcd.print("Temp: ");
        lcd.print(temperature);
        lcd.print(" C");
    }
    }
  
    void TaskLed1(void *pvParameters)
    {

    (void)pvParameters;
    for (;;)
    {
        digitalWrite(LED_1, HIGH);
        vTaskDelay(500 / portTICK_PERIOD_MS);
        digitalWrite(LED_1, LOW);
        vTaskDelay(500 / portTICK_PERIOD_MS);
    }
    }

    void TaskLedTemp(void *pvParameters)
    {
    (void)pvParameters;
    for (;;)
    {
        if (temperature == 26.00)
        {
            vTaskDelay(2000 / portTICK_PERIOD_MS);
            digitalWrite(LED_2, HIGH);
        }
    }
    }

![image](https://github.com/MatheusChiapetti/RTOS/assets/110207330/2736a044-d050-47e1-8e7d-340928c914cd)
