[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/KzqfxGd5)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=22940016&assignment_repo_type=AssignmentRepo)
# Lab02 - Caracterización de osciladores (externo vs. interno)


## 1. Integrantes
.
.

## 2. Documentación

# 📑 Informe de Laboratorio: Configuración de Osciladores en PIC18F45K22

## 📝 Resumen

Este proyecto documenta la configuración y análisis de los sistemas de reloj en el microcontrolador **PIC18F45K22**. Se implementó un firmware en lenguaje C utilizando el compilador **XC8** que permite conmutar entre tres modos de operación: **Oscilador Interno (INTOSC)**, **Cristal de Cuarzo Externo (HS)** y **Circuito RC**. 

El objetivo principal fue validar la precisión de cada fuente de reloj mediante la generación de una señal cuadrada de **500 Hz** en el pin **RC0**. A través de mediciones con osciloscopio, se evaluó la estabilidad de la frecuencia, el error porcentual respecto al valor teórico y el impacto de la **deriva térmica** en los osciladores basados en circuitos RC (internos y externos).

---

## 📚 Marco Teórico

### 1. El Sistema de Reloj en Microcontroladores
El reloj (*clock*) es la señal periódica que sincroniza todas las operaciones internas de la CPU y los periféricos. En la arquitectura PIC18, el ciclo de instrucción ($T_{cy}$) equivale a cuatro periodos del oscilador principal ($4 / F_{osc}$), lo que significa que la velocidad de ejecución depende directamente de la estabilidad de la fuente de reloj elegida.

### 2. Fuentes de Oscilación en el PIC18F45K22

#### A. Oscilador de Cristal (Modo HS - High Speed)
Utiliza un cristal de cuarzo externo conectado a los pines `OSC1` y `OSC2`. 
* **Funcionamiento:** Se basa en el efecto piezoeléctrico para mantener una frecuencia altamente estable.
* **Precisión:** Es la opción ideal para comunicaciones seriales (UART, CAN) y temporización crítica debido a su bajísimo margen de error (ppm).
* **Componentes:** Requiere condensadores de carga (en este caso **15pF - 21pF**) para garantizar el desfase necesario y el arranque de la oscilación.

#### B. Oscilador Interno (Modo INTOSC)
El bloque **HFINTOSC** consiste en un oscilador RC integrado y calibrado de fábrica.
* **Ventajas:** Reduce el costo y el espacio en el PCB al no requerir componentes externos.
* **Desventajas:** Es sensible a variaciones de voltaje y temperatura, presentando el fenómeno de **deriva térmica**.
* **Configuración:** Se controla mediante el registro `OSCCON`, permitiendo seleccionar frecuencias desde 31 kHz hasta 16 MHz.

#### C. Oscilador RC Externo
Utiliza una red de resistencia y capacitancia externa. La frecuencia de oscilación está dada aproximadamente por:
$$f \approx \frac{1}{R \times C}$$
Es el modo menos preciso y se utiliza únicamente en aplicaciones donde el tiempo no es un factor crítico.

### 3. Registro de Control OSCCON y Fusibles
La selección de la fuente de reloj se define en dos niveles:
1.  **Configuration Bits (#pragma config FOSC):** Determinan la fuente principal al momento del encendido (Power-up).
2.  **Registro OSCCON:** Permite al software cambiar la fuente de reloj o la frecuencia del oscilador interno durante la ejecución mediante los bits `IRCF` (frecuencia) y `SCS` (selección de fuente).

### 4. Análisis de Error y Estabilidad
Para cuantificar la calidad del oscilador, se utiliza el cálculo del error porcentual comparando la frecuencia medida en el osciloscopio ($f_{med}$) contra la frecuencia esperada en el código ($f_{teo} = 500$ Hz):

$$Error (\%) = \left( \frac{f_{med} - f_{teo}}{f_{teo}} \right) \times 100$$

La **deriva térmica** se observa cuando, al aplicar calor al encapsulado, la frecuencia medida se desplaza debido a la alteración de las propiedades físicas de los materiales que componen el oscilador RC.

### 2.1 Descripción del laboratorio

* Herramientas y Materiales

Para el desarrollo de esta práctica se emplearon los siguientes elementos tecnológicos y componentes electrónicos:

* **Microcontrolador:** Microchip **PIC18F45K22** (Arquitectura de 8 bits).
* **Componentes de Salida:** * Diodos LED (indicadores visuales).
    * Resistencias de precisión de **330Ω** y **1kΩ**.
* **Sistema de Oscilación Externa:**
    * Cristal de cuarzo de **16 MHz**.
    * Condensadores cerámicos de **21pF** y **15pF** (Lazo de estabilidad).
* **Hardware de Programación y Alimentación:**
    * Programador/Depurador **PICkit 3 / PICkit 4**.
    * Fuente de alimentación de **5V** (Suministrada vía ICSP desde el programador).
* **Software de Desarrollo:**
    * **MPLAB X IDE** (Entorno de desarrollo).
    * Compilador **XC8** (Lenguaje C).
* **Instrumentación y Prototipado:**
    * **Osciloscopio digital** (Análisis de señales y frecuencia).
    * Computador con SO compatible.
    * Protoboard y jumpers de conexión.

---

* Descripción del Laboratorio

La práctica consistió en la configuración, programación y caracterización del sistema de reloj del microcontrolador **PIC18F45K22**. El enfoque principal fue la validación de tres fuentes de temporización distintas: **Oscilador Interno (INTOSC)**, **Oscilador Externo (Cristal de Cuarzo)** y la configuración mediante **circuito RC**.

### Metodología de Validación
1.  **Configuración de Registros:** Se manipularon los bits de configuración (*Configuration Bits*) y el registro `OSCCON` para conmutar entre las fuentes de reloj disponibles.
2.  **Generación de Señal de Referencia:** Se implementó una rutina de conmutación (Toggle) en el pin **RC0** para generar una señal cuadrada de referencia.
3.  **Medición y Análisis:** Utilizando un **osciloscopio**, se capturó la señal de salida para verificar:
    * Integridad de la forma de onda.
    * Periodo y frecuencia real de operación.
    * Estabilidad bajo manipulación externa.

### Análisis Comparativo
Se realizó una comparativa técnica entre los modos **Cristal vs. INTOSC vs. RC**, bajo los siguientes criterios:

| Criterio | Descripción |
| :--- | :--- |
| **Precisión Absoluta** | Margen de error respecto a la frecuencia teórica programada ($500$ Hz). |
| **Estabilidad de Señal** | Presencia de *jitter* o variaciones en el ancho de pulso. |
| **Deriva Térmica** | Sensibilidad de la frecuencia ante cambios de temperatura ambiente o contacto físico. |
| **Costo/Espacio** | Evaluación de la necesidad de componentes externos frente al uso del oscilador interno. |




### 2.2 Explicación del código implementado

#include <xc.h>
#include <stdint.h>

// ========================== CONFIGURACIÓN GENERAL ========================
// Configuraciones que no dependen del modo de oscilador
#pragma config WDTEN = OFF      
#pragma config LVP = OFF        
#pragma config PBADEN = OFF     
#pragma config CP0 = OFF, CP1 = OFF, CP2 = OFF, CP3 = OFF  
#pragma config BOREN = OFF      
#pragma config FCMEN = OFF      
#pragma config IESO = OFF       

// ========================== MODO DE OSCILADOR ==========================
// 1 = INTOSC interno
// 2 = Cristal externo HS
// 3 = RC externo
#define MODE 2 

#if MODE == 1
    #pragma config FOSC = INTIO67   // Oscilador interno
    #define USE_PLL 1
#elif MODE == 2
    #pragma config FOSC = HSHP     // Cristal HS
    #define USE_PLL 1
#elif MODE == 3
    #pragma config FOSC = RC       // RC externo
    #define USE_PLL 0
#else
    #error "Modo de oscilador inválido"
#endif


// ========================== FRECUENCIA DEL OSCILADOR =====================
#if MODE == 1 || MODE == 2
    #if USE_PLL  
        #define _XTAL_FREQ 16000000UL // 16 MHz * 4
    #else
        #define _XTAL_FREQ 16000000UL
    #endif
#else
    #define _XTAL_FREQ 2400000UL // Ajustar según resistencia + condensador
#endif

// ========================== FUNCIONES ==========================
void delay_ms(uint16_t ms) {
    while(ms--) {
        __delay_ms(1);
    }
}

void init_pins(void) {
    // RC0 salida blinker
    TRISCbits.TRISC0 = 0;
    LATCbits.LATC0 = 0;

    // RA6 salida CLKO solo si modo lo permite
    if(MODE == 1 || (MODE == 2 && USE_PLL)) {
        TRISAbits.TRISA6 = 0;
        LATAbits.LATA6 = 0;
    }
}

void init_oscillator(void) {
#if USE_PLL 
    OSCCONbits.IRCF= 0b111;  // habilita PLL
    OSCCONbits.SCS= 0b10;  //seleccionamos el oscilador interno */
#endif
}

// ========================== PROGRAMA PRINCIPAL ==========================
void main(void) {
    init_pins();
    init_oscillator();

    while(1) {
        // RC0 toggle ? 500 Hz
        LATCbits.LATC0 = 1;
        delay_ms(1);
        LATCbits.LATC0 = 0;
        delay_ms(1);
    }
}

### 2.3 Análisis y comparación

#### Tabla 1: Medición en frío 

| Modo de oscilador | Freq. teórica Fosc | RA6 medible (CLKO)? | Freq. medida RA6 (Hz) | Freq. teórica RC0 (Hz)| Freq. medida RC0 (Hz) | Error RC0 (%) |  
|------------------|------------------|---------------------|---------------|---------------------|---------------|---------------|
| INTOSC (interno) | 16,000,000       | Sí                 |          31.4           |                500                 |    31           |       93        | |
| HS (cristal externo 16 MHz) | 16,000,000 | No |     NA      |               500                 |    495.8           |          0.8%     |
| RC externo       | ~16,000,000*     | No                                    |       N/A        | 500                 |             501.6  |     0.32%          | |

#### Tabla 2: Medición con calor

| Modo de oscilador | Freq. teórica Fosc | RA6 medible (CLKO)? | Freq. medida RA6 (Hz) | Freq. teórica RC0 (Hz)| Freq. medida RC0 (Hz) | Error RC0 (%) |  
|------------------|------------------|---------------------|---------------|---------------------|---------------|---------------|
| INTOSC (interno) | 16,000,000       | Sí                 |           No hay señal         |                500                 |      30.67         |     93.86%          | |
| HS (cristal externo 16 MHz) | 16,000,000 | No |     NA      |               500                 |        501.63       |     0.32%          |
| RC externo       | ~16,000,000*     | No                                    |       N/A        | 500                 |       497.02        |       146.5%        | |

#### Tabla 3: Deriva

| Modo de oscilador |RC0 deriva (Hz) |
|------------------|--------------------|
| INTOSC (interno) |         468.6           |                
| HS (cristal externo 16 MHz) |       3.9         |                |
| RC externo       |        1.6         |                


<!-- Agregar tablas para valores usando PLL -->

<!-- Complemente con análisis de lo registrado en tablas -->

## 2.4 Diagramas
<img width="1080" height="1080" alt="image" src="https://github.com/user-attachments/assets/3f6ea1e7-3738-4ed0-b058-26b04f0aeb46" />


## 2.5 Formas de onda
<img width="1378" height="869" alt="ossssss" src="https://github.com/user-attachments/assets/0564d6ed-0c73-4a16-bf7e-a3ee18c10f1a" />


### INTOSC (interno) 
<img width="1280" height="960" alt="image" src="https://github.com/user-attachments/assets/b26aa9f6-4e55-477a-8fbf-a698bcc9da06" />



### HS
<img width="1280" height="960" alt="image" src="https://github.com/user-attachments/assets/6ce944a7-69dd-4a58-925d-4131f643de03" />



## RC
<img width="1536" height="1024" alt="osii" src="https://github.com/user-attachments/assets/832e4437-a831-4482-8eff-f2bd25699619" />


## 3. Evidencias de implementación
<img width="1200" height="1600" alt="image" src="https://github.com/user-attachments/assets/bde5f416-7d41-427c-be4f-217f415b1c56" />


## 4. Preguntas

* ¿En qué modo se obtuvo la medición más cercana a la frecuencia teórica?

* ¿Fue posible evidenciar el fenómeno de deriva? ¿Qué factores podrían explicar la variación de frecuencia al calentar el PIC?

* ¿Cuál es más preciso en cuanto a frecuencia teórica vs. medida?


* Explique cómo usar RC0 para estimar la frecuencia del oscilador cuando RA6 no está disponible.

* Si se quisiera duplicar la frecuencia del PIC usando PLL, ¿en qué modos se podría aplicar?

* Enliste ventajas y desventajas de cada modo.

## 5. Referencias
