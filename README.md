# Trabajo práctico - Informática 2
## Máquina de Estado 

![Contador de personas y aviso](https://i.ibb.co/SRkqNbX/20210820-170545.jpg)

## Memoria descriptiva

El sistema representa un control de entrada de un local en el que se permiten un cierto  número de personas. Se tomarán lecturas de los sensores de la puerta, uno detectando las entradas y otro las salidas. Operando con dichas lecturas se obtiene el número de personas. Se deberá permitir el acceso siempre y cuando se esté por debajo del máximo de personas. Caso contrario, se niega el acceso y se disparará algún tipo de señal o alarma.

## Primer código

    #include <stdio.h>
	#include <stdlib.h>

	typedef enum{
    espera = 0,
    ingreso = 1,
    egreso = 2,
    cierre = 3
	}estados_t;

	typedef struct{
    char cantp;
    char maxp;
	}personas_t;

	int main()
	{
    estados_t estado=espera;
    personas_t config;
    int sensor_1=0,sensor_2=0;

    //realizar con el archivo
    config.cantp=7;
    config.maxp=5;

    while(1)
    {
        switch(estado)
        {
            case espera:

                do
                {
                    printf("Estado: espera.\ncantidad de personas: %d\n",config.cantp);

                    printf("sensor 1: \n");
                    scanf("%d",&sensor_1);
                    fflush(stdin);

                    printf("sensor 2: \n");
                    scanf("%d",&sensor_2);
                    fflush(stdin);

                }while(sensor_1==0&&sensor_2==0);

                if(sensor_1!=0)
                    {
                        if(config.cantp<config.maxp)
                        {
                            estado=ingreso;
                            config.cantp++;
                        }
                        else estado=cierre;
                    }
                if(sensor_2!=0)
                {
                    estado=egreso;
                    config.cantp--;
                }

                break;

            case ingreso:


                do
                {
                    printf("Estado: ingreso.\npuerta abierta\n");
                    printf("sensor 1: \n");
                    scanf("%d",&sensor_1);
                    fflush(stdin);

                }while(sensor_1!=0);

                printf("puerta cerrada\n");

                estado=espera; //de este estado solo se puede volver a espera.

                break;

            case egreso:

                do
                {
                    printf("Estado: egreso.\npuerta abierta\n");
                    printf("sensor 2: \n");
                    scanf("%d",&sensor_2);
                    fflush(stdin);

                }while(sensor_2!=0);

                printf("puerta cerrada\n");

                if(sensor_1!=0&&config.cantp>=config.maxp)
                    estado=cierre;

                if(sensor_1==0||(sensor_1!=0&&config.cantp<config.maxp))
                    estado=espera;

                break;

            case cierre:

                do
                {
                    printf("maximo de personas alcanzado: %d\n",config.cantp);

                    printf("sensor 1: \n");
                    scanf("%d",&sensor_1);
                    fflush(stdin);

                    printf("sensor 2: \n");
                    scanf("%d",&sensor_2);
                    fflush(stdin);

                }while(sensor_1!=0&&config.cantp>=config.maxp);

                if(sensor_1==0)
                    estado=espera;



