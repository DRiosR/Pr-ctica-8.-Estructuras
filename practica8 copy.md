/*
   Nombre del archivo: practica8_1.c
   Autor: Daniel Rios Rodriguez
   Fecha de creación: 19 de Noviembre de 2023
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <limits.h>
#include <float.h>
#include <math.h>

#define CAPACIDAD_MAXIMA 1000
#define ARCHIVO_INVENTARIO "inventario.dat"
#define PORCENTAJE_MAXIMO 100
#define EPSILON 0.0001

typedef struct Producto
{
    char nombre[30];
    int cantidad;
    float precio;
} Producto;

void agregarElemento();
void retirarElemento();
void mostrarInventario();
void calcularValorTotal();

void realizarOperacionesMatematicas();
void ordenarInventario(int opcionOrden);
int compararProductosPorPrecio(const void *a, const void *b);
int compararProductosPorCantidad(const void *a, const void *b);
int compararProductosPorNombre(const void *a, const void *b);
int validarNumero(char mensaje[], int ri, int rf);

Producto inventario[CAPACIDAD_MAXIMA];

int cantidadElementos = 0;

int main()
{
    FILE *archivo = fopen(ARCHIVO_INVENTARIO, "rb");
    if (archivo != NULL)
    {
        fread(inventario, sizeof(Producto), CAPACIDAD_MAXIMA, archivo);
        fclose(archivo);

        for (int i = 0; i < CAPACIDAD_MAXIMA; i++)
        {
            if (inventario[i].cantidad > 0)
            {
                cantidadElementos++;
            }
        }
    }

    int opcion;
    int menu = 0;

    do
    {
        printf("\n--- Menú de Inventario ---\n");
        printf("1. Agregar elemento\n");
        printf("2. Retirar elemento\n");
        printf("3. Mostrar inventario\n");
        printf("4. Calcular el valor total del inventario\n");
        printf("5. Realizar operaciones matemáticas en precios/cantidades\n");
        printf("6. Ordenar elementos del inventario\n");
        printf("7. Guardar y salir del programa\n");

        opcion = validarNumero("Seleccione una opción: ", 1, 7);
        switch (opcion)
        {
        case 1:
            agregarElemento();
            break;
        case 2:
            retirarElemento();
            break;
        case 3:
            mostrarInventario();
            break;
        case 4:
            calcularValorTotal();
            break;
        case 5:
            realizarOperacionesMatematicas();
            break;
        case 6:
            printf("\n--- Opciones de Ordenamiento ---\n");
            printf("1. Por nombre\n");
            printf("2. Por cantidad\n");
            printf("3. Por precio\n");
            int opcionOrdenamiento = validarNumero("Seleccione una opción de ordenamiento: ", 1, 3);
            ordenarInventario(opcionOrdenamiento);
            break;
        case 7:
            archivo = fopen(ARCHIVO_INVENTARIO, "wb");
            if (archivo != NULL)
            {
                fwrite(inventario, sizeof(Producto), CAPACIDAD_MAXIMA, archivo);
                fclose(archivo);
            }

            printf("Guardando el inventario y saliendo del programa.\n");
            menu = 1;
            break;
        }
    } while (!menu);

    return 0;
}
/*
 Función: agregarElemento
 Descripción: Agrega un nuevo producto al inventario.
 Parámetros:
 - ninguno
 Valor de retorno: ninguno
*/
void agregarElemento()
{
    if (cantidadElementos < CAPACIDAD_MAXIMA)
    {
        Producto nuevoProducto;

        printf("Ingrese el nombre del producto: ");
        fgets(nuevoProducto.nombre, sizeof(nuevoProducto.nombre), stdin);
        nuevoProducto.nombre[strcspn(nuevoProducto.nombre, "\n")] = '\0';

        nuevoProducto.cantidad = validarNumero("Ingrese la cantidad a agregar: ", 1, 1000);
        nuevoProducto.precio = validarNumero("Ingrese el precio del producto: ", 0, 1000000);

        inventario[cantidadElementos] = nuevoProducto;
        cantidadElementos++;
        printf("Producto agregado al inventario.\n");
    }
    else
    {
        printf("El inventario está lleno. No se pueden agregar más productos.\n");
    }
}
/*
 Función: retirarElemento
 Descripción: Retira un producto existente del inventario según su nombre y cantidad.
 Parámetros:
 - ninguno
 Valor de retorno: ninguno
*/

void retirarElemento()
{
    if (cantidadElementos > 0)
    {
        char nombreBuscado[30];
        int cantidad;

        printf("Ingrese el nombre del producto a retirar: ");
        fgets(nombreBuscado, sizeof(nombreBuscado), stdin);
        nombreBuscado[strcspn(nombreBuscado, "\n")] = '\0';

        int indiceProducto = -1;
        for (int i = 0; i < cantidadElementos; i++)
        {
            if (strcmp(inventario[i].nombre, nombreBuscado) == 0)
            {
                indiceProducto = i;
                break;
            }
        }

        if (indiceProducto != -1)
        {
            cantidad = validarNumero("Ingrese la cantidad a retirar: ", 1, inventario[indiceProducto].cantidad);

            inventario[indiceProducto].cantidad -= cantidad;
            printf("Producto retirado del inventario.\n");

            if (inventario[indiceProducto].cantidad == 0)
            {
                for (int i = indiceProducto; i < cantidadElementos - 1; i++)
                {
                    inventario[i] = inventario[i + 1];
                }
                cantidadElementos--;
            }
        }
        else
        {
            printf("Producto no encontrado en el inventario.\n");
        }
    }
    else
    {
        printf("El inventario está vacío. No se pueden retirar productos.\n");
    }
}
/*
 Función: mostrarInventario
 Descripción: Muestra en consola el contenido actual del inventario.
 Parámetros:
 - ninguno
 Valor de retorno: ninguno
*/
void mostrarInventario()
{
    printf("\n--- Inventario ---\n");

    for (int i = 0; i < cantidadElementos; i++)
    {
        printf("Producto %d:\n", i + 1);
        printf("   Nombre: %s\n", inventario[i].nombre);
        printf("   Cantidad: %d\n", inventario[i].cantidad);
        printf("   Precio: %.2f\n", inventario[i].precio);
    }
}
/*
 Función: calcularValorTotal
 Descripción: Calcula el valor total del inventario y lo muestra en consola.
 Parámetros:
 - ninguno
 Valor de retorno: ninguno
*/
void calcularValorTotal()
{
    float valorTotal = 0;

    for (int i = 0; i < cantidadElementos; i++)
    {
        valorTotal += inventario[i].cantidad * inventario[i].precio;
    }

    printf("\nEl valor total del inventario es: %.2f\n", valorTotal);
}
/*
 Función: realizarOperacionesMatematicas
 Descripción: Realiza operaciones matemáticas en los precios y cantidades de los productos.
 Parámetros:
 - ninguno
 Valor de retorno: ninguno
*/
void ordenarInventario(int opcionOrden)
{
    switch (opcionOrden)
    {
    case 1:
        qsort(inventario, cantidadElementos, sizeof(Producto), compararProductosPorNombre);
        printf("Inventario ordenado por nombre.\n");
        break;
    case 2:
        qsort(inventario, cantidadElementos, sizeof(Producto), compararProductosPorCantidad);
        printf("Inventario ordenado por cantidad.\n");
        break;
    case 3:
        qsort(inventario, cantidadElementos, sizeof(Producto), compararProductosPorPrecio);
        printf("Inventario ordenado por precio.\n");
        break;
    }
}
/*
 Función: compararProductosPorNombre
 Descripción: Compara dos productos según su nombre.
 Parámetros:
 - a: Puntero al primer producto a comparar.
 - b: Puntero al segundo producto a comparar.
 Valor de retorno: Un entero que indica la relación entre los nombres de los productos.
*/
int compararProductosPorNombre(const void *a, const void *b)
{
    return strcmp(((Producto *)a)->nombre, ((Producto *)b)->nombre);
}
/*
 Función: compararProductosPorCantidad
 Descripción: Compara dos productos según su cantidad.
 Parámetros:
 - a: Puntero al primer producto a comparar.
 - b: Puntero al segundo producto a comparar.
 Valor de retorno: Un entero que indica la relación entre las cantidades de los productos.
*/
int compararProductosPorCantidad(const void *a, const void *b)
{
    return ((Producto *)a)->cantidad - ((Producto *)b)->cantidad;
}
/*
 Función: compararProductosPorPrecio
 Descripción: Compara dos productos según su precio.
 Parámetros:
 - a: Puntero al primer producto a comparar.
 - b: Puntero al segundo producto a comparar.
 Valor de retorno: Un entero que indica la relación entre los precios de los productos.
*/
int compararProductosPorPrecio(const void *a, const void *b)
{
    float diff = ((Producto *)a)->precio - ((Producto *)b)->precio;
    return (fabs(diff) < EPSILON) ? 0 : (diff > 0 ? 1 : -1);
}
/*
 Función: realizarOperacionesMatematicas
 Descripción: Realiza operaciones matemáticas en los precios y cantidades de los productos.
 Parámetros:
 - ninguno
 Valor de retorno: ninguno
*/
void realizarOperacionesMatematicas()
{
    int opcionOperacion;
    printf("\n--- Opciones de Operaciones Matemáticas ---\n");
    printf("1. Aumentar precios\n");
    printf("2. Reducir precios\n");
    printf("3. Aumentar cantidades\n");
    printf("4. Reducir cantidades\n");
    opcionOperacion = validarNumero("Seleccione una opción de operación matemática: ", 1, 4);

    float porcentaje;
    switch (opcionOperacion)
    {
    case 1:
        porcentaje = validarNumero("Ingrese el porcentaje de aumento de precios: ", 0, PORCENTAJE_MAXIMO);
        for (int i = 0; i < cantidadElementos; i++)
        {
            inventario[i].precio *= (1 + porcentaje / 100);
        }
        printf("Precios aumentados en %.2f%%.\n", porcentaje);
        break;
    case 2:
        porcentaje = validarNumero("Ingrese el porcentaje de reducción de precios: ", 0, PORCENTAJE_MAXIMO);
        for (int i = 0; i < cantidadElementos; i++)
        {
            inventario[i].precio *= (1 - porcentaje / 100);
        }
        printf("Precios reducidos en %.2f%%.\n", porcentaje);
        break;
    case 3:
        porcentaje = validarNumero("Ingrese el porcentaje de aumento de cantidades: ", 0, PORCENTAJE_MAXIMO);
        for (int i = 0; i < cantidadElementos; i++)
        {
            inventario[i].cantidad *= (1 + porcentaje / 100);
        }
        printf("Cantidades aumentadas en %.2f%%.\n", porcentaje);
        break;
    case 4:
        porcentaje = validarNumero("Ingrese el porcentaje de reducción de cantidades: ", 0, PORCENTAJE_MAXIMO);
        for (int i = 0; i < cantidadElementos; i++)
        {
            inventario[i].cantidad *= (1 - porcentaje / 100);
        }
        printf("Cantidades reducidas en %.2f%%.\n", porcentaje);
        break;
    }
}

/*
 Función: validarNumero
 Descripción: Valida un número ingresado por el usuario dentro de un rango específico.
 Parámetros:
 - mensaje: Mensaje a mostrar solicitando al usuario que ingrese un número.
 - ri: Límite inferior del rango permitido.
 - rf: Límite superior del rango permitido.
 Valor de retorno: El número validado dentro del rango.
*/
int validarNumero(char mensaje[], int ri, int rf)
{
    int numero;
    do
    {
        printf("%s", mensaje);
        scanf("%d", &numero);
        if (numero < ri || numero > rf)
        {
            printf("Error: Ingrese un número entre %d y %d.\n", ri, rf);
        }
    } while (numero < ri || numero > rf);

    return numero;
}
