
#include <stdio.h>
#include <pthread.h>
#include "function.h"

#define MAXRECT 1000000
#define MAXTHREAD 10
#define BASE 0.0
#define LIMIT 1.0

double length = LIMIT - BASE;
double numRectxThread = (double)(MAXRECT/MAXTHREAD);//numero de rectangulos por hilo
double base = 0;
double sumTotal = 0;
double partialSums[MAXTHREAD];


void* calcular(void *arg) {
    	long id = (long) arg;
    	double lowlimit = id*base*numRectxThread;

    	double suma = 0;

		int i;
    	for (i = 0; i < numRectxThread; i++) {
            	suma += (double)function(lowlimit + i*base) * base;//suma cada area resultante de evaluar cada limite inferior en la funcion exponencial y multiplicarlo por la base de rectangulo
    	}
    	partialSums[id] = suma;
    	printf("hilo: %li\t suma parcial: %lf\n", id, suma);//imprime la suma parcial de cada hilo
    	return 0;
}

int main(int argc, char** argv) {
    	pthread_t threads[MAXTHREAD];
    	long taskids[MAXTHREAD];

    	base = length/MAXRECT;
    	printf("base rectangulo: %lf rectangulos por hilo: %lf\n",base, numRectxThread);
		int i;
    	for (i = 0; i < MAXTHREAD; i++) {
            	taskids[i] = i;
            	partialSums[i] = 0;
            	pthread_create(&threads[i], NULL, calcular, (void*)taskids[i]);
    	}
    	for (i = 0; i < MAXTHREAD; i++) {
            	pthread_join(threads[i], NULL);
            	sumTotal += partialSums[i];
    	}
    	printf("Suma total %lf\n",sumTotal);
    	pthread_exit(NULL);
}
