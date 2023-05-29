## Funcionamiento de la Práctica 
    Se enciende 9 led siendo las salidas            
    modificando los GPIO mostrando un valor 
    binario.
    Para la entradas se utilizaran dos push button:
    Botón A: Al presionar se debe incrementar el 
    valor de la variable en la unidad 
    Botón B: Al presionar se debe decrementarse el 
    valor en la variable de la unidad 
    Los dos botones: Se hace un reset del contador.

## Instalación de software 
 	El compilador cruzado gcc-arm-none-eabi.
    stlink- tools paquete contiene las utilizadas 
    que permiten grabar un  microcontrolador de 
    STM32
	libusb-1.0-0-dev. Este paquete contiene los 
    controladores que
    permiten detectar la conexión con el ST-Link 
    V2.
    Instalación del software STM32CubeProg

## Alias:
    alias arm-gcc=arm-none-eabi-gcc
    alias arm-as=arm-none-eabi-as
    alias arm-objdump=arm-none-eabi-objdump
    alias arm-objcopy=arm-none-eabi-objcopy

## Archivo main.s

Se crea el marco de main que tendrá counter,    buttonA, button B, r7 y ls, se ensambla reloj del puerto A, para utilizar las 9 entradas se configuró los pin 0-7 en GPIOA_CRL para los registros altos se configuró  GPIOA_CRH 1 pin más de salida en la dirección de memoria se indica un 4, para las entradas se configuró 2 pines PA9 Y PA10 para modificarlos como salidas se modifica en la dirección con un 8. 
Se inicializan las variables 
Se la lógica de los botones se codificó en alto nivel para pasar al ensamblador. 

    # if (buttonA && buttonB)
        #     counter = 0;
    # else if (buttonA && !buttonB)
        #     counter++;
    # else if (!buttonA && buttonB)
        #     counter--;
    ## MARCO 
    
## read_button.s 
    Logica del codigo en alto nivel 
    
    #define DELAY 50
    #define SAMPLES 10
    #define POSITIVE_READINGS 4

        int digital_read(int port, int pin);
        void delay(int ms);

        int read_button(int port, int pin)
        {
            int bit = digital_read(port, pin);
            if (!bit)
                return 0;
            int counter = 0;
            for (int i = 0; i < SAMPLES; i++)
            {
                delay(5);
                bit = digital_read(port, pin);
                if (!bit)
                    counter = 0;
                else{
                    counter++;
                    if (counter >= POSITIVE_READINGS)
                        return 1;
                }
            }
            return 0;
        }

## output.s
    Para emitir el valor se modifico poniendo una mascara para nuestro puerto diginal de los 9 led, agregamos un registro con la mascara 0x3FF para poder prender los 9 leds.

