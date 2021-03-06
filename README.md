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

                if(sensor_2!=0)
                {
                    estado=egreso;
                    config.cantp--;
                }

                if(config.cantp<config.maxp)
                {
                    estado=ingreso;
                    config.cantp++;
                }


                break;
        }
    }
    return 0;
    }

## Main final

    #include <stdio.h>
	#include <stdlib.h>
	#include "Lib.h"

	int main()
	{
    personas_t config=inicio();

    char *cant,*max; //punteros para pasaje por referencia.

    estados_t estado=espera;
    estados_t (*fsm[])(char*,char*){f_espera,f_ingreso,f_egreso,f_cierre}; //puntero a las 4 funciones de estado.

    cant=&config.cantp;
    max=&config.maxp;

    while(1) estado=(*fsm[estado])(cant,max);

    return 0;
}

## Librerías y archivos de configuración
### lib.h

    #ifndef MY_LIB
	#define MY_LIB
	#include <stdio.h>
	#include <stdlib.h>
	#include "def.h"
	#include "mystr.h"

	personas_t inicio(void)
	{
    FILE *pf;
    personas_t bf;
    char str[100];
    char *ps; //puntero a la cadena;


    if((pf=fopen("config.txt","rt"))==NULL)
        {
            printf("no se puede abrir el archivo");
            return bf;
        }

    fread(&str,sizeof(str),1,pf); //guardo el contenido del txt en una cadena.

    ps=str;

    bf.cantp = strtoint(ps); //obtengo un valor entero de la cadena

    while(*ps != 'm')ps++;

    bf.maxp = strtoint(ps); //obtengo un valor entero de la cadena

    printf("cantidad de personas:  %d\n",bf.cantp);
    printf("maximo de personas:  %d\n",bf.maxp);

    fclose(pf);

    return bf;
	}

	estados_t f_espera(char *cant,char *m)
	{
    estados_t est;

        printf("Estado: espera.\ncantidad de personas: %d\n",*cant);

        printf("sensor 1: \n");
        scanf("%d",&sensor_1);
        fflush(stdin);

        printf("sensor 2: \n");
        scanf("%d",&sensor_2);
        fflush(stdin);

    if(sensor_1==0&&sensor_2==0) est=espera;

    if(sensor_1!=0)
    {
        if(*cant<*m)
        {
            est=ingreso;
        }
        else est=cierre;

        return est;
    }
    if(sensor_2!=0)
    {
        est=egreso;

    }

    return est;
	}
	estados_t f_ingreso(char *cant,char *m)
	{
    estados_t est;

    printf("Estado: ingreso.\npuerta abierta\n");
    printf("sensor 1: \n");
    scanf("%d",&sensor_1);
    fflush(stdin);

    if(sensor_1!=0)
    {
        est=ingreso;
        return est;
    }

    printf("puerta cerrada\n");

    *cant=*cant+1;

    est=espera; //de este estado solo se puede volver a espera.

    return est;

	}
	estados_t f_egreso(char *cant,char *m)
	{
    estados_t est;

    printf("Estado: egreso.\npuerta abierta\n");
    printf("sensor 2: \n");
    scanf("%d",&sensor_2);
    fflush(stdin);

    if(sensor_2!=0)
    {
        est=egreso;
        return est;
    }

    printf("puerta cerrada\n");
    *cant=*cant-1;

    if(sensor_1!=0&&*cant>=*m)
        est=cierre;

    if(sensor_1==0||(sensor_1!=0&&*cant<*m))
        est=espera;

    return est;
	}
	estados_t f_cierre(char *x,char *y)
	{
    estados_t est;

    printf("maximo de personas alcanzado: %d\n",*y);

    printf("sensor 1: \n");
    scanf("%d",&sensor_1);
    fflush(stdin);

    printf("sensor 2: \n");
    scanf("%d",&sensor_2);
    fflush(stdin);

    if(sensor_1!=0&&*x>=*y&&sensor_2==0)
    {
        est=cierre;
        return est;
    }

    if(sensor_1==0)
    {
        est=espera;
    }
    if(sensor_2!=0)
    {
        est=egreso;
    }
    if(*x<*y)
    {
        est=ingreso;
    }
    return est;
	}

	#endif
### def.h

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

	int sensor_1=0,sensor_2=0; //simulo las entradas de los sensores
### mystr.h
Esta librería contiene una función para poder obtener los parámetros de configuración del archivo txt.

    int strtoint (char *ps)
	{
	    int x;
	    char straux[4]="xxxx";
	    char i=0; //contador


    while(*ps != '\n' && *ps != '\0')
    {

        if(*ps > 47 && *ps < 58)//en la cadena auxiliar solo guardo los caracteres numéricos.
        {
            straux[i]=*ps;
            i++;
        }
        ps++;

    }

    x = atoi(straux); //obtengo un valor entero de la cadena auxiliar

    return x;
}

### config.txt

    cantidad inicial de personas: 0

	maximo de personas: 100


