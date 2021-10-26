#include <stdio.h>
#include <stdlib.h>
#include "Lib.h"
int main()
{

    genera();
    estados_t estado=espera;
    personas_t config=inicio();
    int sensor_1=0,sensor_2=0;
    char *cant,*max;

    cant=&config.cantp;
    max=&config.maxp;

    //realizar con el archivo
    //config.cantp=7;
    //config.maxp=5;

    while(1)
    {
        switch(estado)
        {
            case espera: estado=f_espera(cant,max);
                            break;
            case ingreso: estado=f_ingreso(cant,max);
                            break;

            case egreso: estado=f_egreso(cant,max);
                            break;

            case cierre: estado=f_cierre(cant,max);
                            break;
        }
    }
    return 0;
}

