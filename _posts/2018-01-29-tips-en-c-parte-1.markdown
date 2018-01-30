---
layout: post
title: Tips en C - Parte 1
author: arandi
published: true
tags: c programming software pointers
category: spanish
---

C es un lenguaje de programación que es considerado como el lenguaje básico a aprender si estudias algún grado en computación. Fue el lenguaje que aprendí en el primer curso de programación de la Universidad, y hoy estoy retomando estos temas en mi maestría.

Es por eso que decidí escribir acerca de C, y de cosas que he encontrado de él que no me fueron enseñadas en cuando tomé el curso de programación hace años. Espero encuentres útil la siguiente información que comparto.

---

## Apuntadores

La definción más sencilla de mencionar de una apuntador es:

> Un apuntador es una variable que posee un tipo de dato, él cuál *apunta* a una dirección de memoria.

Decir que *apunta* a una dirección de memoria es que el valor que un apuntador guarda es literalmente el número de la dirección de memoria. La memoria esta direccionada como si fuera un arreglo, desde el 0 hasta, por ejemplo, tus 8GB de memoria RAM.

Tengamos como ejemplo este programa en C.

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, const char *argv[])
{
	int numero = 20;
	int * a_numero = &numero;

	printf("El numero es %d\n", numero);
	printf("El apuntador tiene como memoria %d\n", a_numero);
	printf("El valor del apuntador es %d\n", *a_numero);

	return 0;
}
```

En este programa vemos la definición de un apuntador, esta es `int * a_numero = &numero`, que significa: definir un apuntador de enteros llamado `a_numero`. El valor que se le da al apuntador es `&numero`, poner el `&` antes del nombre de la variable pide el apuntador (o dirección de memoria) de la variable `numero`.

El resultado del código sería lo siguiente

```
El numero es 20
El apuntador tiene como memoria 1598883260
El valor del apuntador es 20
```

Observa el segundo resultado de imprimir el apuntador. Este es el número de memoria en donde se encuentra alojado la variable `numero`. El apuntador `a_numero` puede obtener su valor al usar el `*` al inicio de su nombre, es decir: `*a_numero` obtiene el valor de aquella posición de memoria del que `a_numero` apunta.

### El apuntador es una referencia

Si, el apuntador `a_numero` es una referencia a `numero`, asi que cualquier operacion que hagamos sobre `*a_numero` será sobre el valor de `numero`. La reasignación, por ejemplo, de `*a_numero` hará que el valor de `numero` también cambie.

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, const char *argv[])
{
	int numero = 20;
	int * a_numero = &numero;

	printf("El numero es %d\n", numero);
	printf("El apuntador tiene como memoria %d\n", a_numero);
	printf("El valor del apuntador es %d\n", *a_numero);

	*a_numero = 40;

	printf("El numero es %d\n", numero);
	printf("El valor del apuntador es %d\n", *a_numero);

	return 0;
}
```

En el ejemplo de código anterior, observa que hacemos una reasignación `*a_numero = 40;`.

El resultado de ejecutar el programa es:

```
El numero es 20
El apuntador tiene como memoria 1598883260
El valor del apuntador es 20
El numero es 40
El valor del apuntador es 40
```

Entonces al modificar el valor de `*a_numero` modificó de igual forma a `numero`. Esto es lo que se conoce como *referencia* en programación. Tener una referencia es tener un apuntador a un valor que ya se encuentra en la memoria y que los cambios que apliquemos sobre ella tomarán efecto como si trabajaramos con la variable real.

---

Espero que encuentres útil ésta información. En la siguiente publicación de esta serie hablaré sobre los `struct` en C y cómo hacer nuestros propios tipos de datos complejos para dar a nuestros programas un mejor manejo de la memoria.

