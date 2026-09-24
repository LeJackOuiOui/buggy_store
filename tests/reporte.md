# Informe de Evaluación

## Resumen de la evaluación

| # | Fallo evaluado | Identificación | Solución | Tests | Subtotal |
|---|---|---:|---:|---:|---:|
| 1 | Error de tipografía (`ventas_totaIes`) | 1/1 | 2/2 | 3/3 | **6/6** |
| 2 | Matemáticas de descuento (`SENA2026`) | 1/1 | 2/2 | 3/3 | **6/6** |
| 3 | Argumento mutable por defecto | 1/1 | 2/2 | 3/3 | **6/6** |
| 4 | Stock negativo / insuficiente | 1/1 | 2/2 | 3/3 | **6/6** |
| 5 | Mutación durante la iteración | 1/1 | 2/2 | 3/3 | **6/6** |
| 6 | Producto inexistente y transaccionalidad | 1/1 | 1/2 | 3/3 | **5/6** |
| **Total** | | **6/6** | **11/12** | **18/18** | **35/36** |

---

## 1. Error de tipografía (`ventas_totaIes`)

### Identificación — 1/1 punto

Explicaron detalladamente el `AttributeError` generado por la confusión entre la `"I"` mayúscula y la `"l"` minúscula en el nombre del atributo.

La identificación permite comprender claramente por qué el programa no encontraba el atributo esperado.

### Solución — 2/2 puntos

Corrigieron correctamente la referencia al atributo:

```python id="6wq8y1"
self.ventas_totales
```

La modificación soluciona el problema de nomenclatura y permite acceder correctamente al acumulador de ventas.

### Tests — 3/3 puntos

Diseñaron pruebas utilizando `pytest` y un fixture para verificar diferentes aspectos del comportamiento:

- Inicialización de las ventas.
- Cálculo exitoso.
- Acumulación de ventas.

La utilización del fixture permite reutilizar la configuración necesaria entre las diferentes pruebas.

### Subtotal: **6/6 puntos**

---

## 2. Matemáticas de Descuento (Cupón `SENA2026`)

### Identificación — 1/1 punto

Documentaron correctamente el error de lógica producido al multiplicar el total por `1.20`, operación que incrementaba el valor en un 20 % en lugar de aplicar un descuento.

### Solución — 2/2 puntos

Ajustaron correctamente el multiplicador a:

```python id="f4n8qs"
0.80
```

Esto representa correctamente la aplicación de un descuento del 20 % sobre el valor original.

### Tests — 3/3 puntos

Escribieron pruebas específicas utilizando `pytest` para verificar el cálculo del descuento tanto con cupón como sin cupón.

Esto permite comprobar que la lógica del precio se comporte correctamente en ambos escenarios.

### Subtotal: **6/6 puntos**

---

## 3. Argumento mutable por defecto (`inventario_inicial={}`)

### Identificación — 1/1 punto

Explicaron claramente el problema ocasionado por utilizar `{}` como valor predeterminado en la firma del constructor.

Identificaron correctamente que el diccionario puede ser creado una sola vez y posteriormente compartido entre diferentes instancias, provocando modificaciones no deseadas entre objetos.

### Solución — 2/2 puntos

Cambiarion correctamente el valor predeterminado a `None` y asignaron un diccionario nuevo cuando no se proporciona un inventario inicial.

Esta implementación garantiza que cada instancia disponga de su propio inventario independiente.

### Tests — 3/3 puntos

Crearon `TestTiendaOnline`, comprobando que `tienda2` no reciba los productos agregados previamente a `tienda1`.

La prueba valida directamente la independencia entre las instancias.

### Subtotal: **6/6 puntos**

---

## 4. Stock Negativo / Insuficiente

### Identificación — 1/1 punto

Identificaron correctamente la falta de control sobre las operaciones de resta de existencias y el riesgo de permitir que el inventario termine con cantidades negativas.

### Solución — 2/2 puntos

Implementaron una validación preventiva mediante `ValueError` antes de realizar la resta del stock.

Esto impide que una compra superior a las existencias disponibles modifique incorrectamente el inventario.

### Tests — 3/3 puntos

Implementaron `TestProcesarPedido` con pruebas para:

- Compras válidas.
- Compras que exceden las existencias disponibles.
- Captura de la excepción mediante `pytest`.
- Verificación de que el inventario no sea modificado cuando la operación es inválida.

Las pruebas cubren tanto el comportamiento exitoso como el escenario de error.

### Subtotal: **6/6 puntos**

---

## 5. Mutación durante la iteración (`RuntimeError`)

### Identificación — 1/1 punto

Explicaron correctamente la causa teórica del `RuntimeError` generado al modificar el tamaño de un diccionario mientras se recorre mediante un ciclo `for`.

### Solución — 2/2 puntos

Utilizaron:

```python id="3g8h2v"
list(self.inventario.keys())
```

para crear una lista estática de las claves antes de comenzar la iteración.

Esto permite eliminar elementos del diccionario sin modificar la estructura que está siendo recorrida.

### Tests — 3/3 puntos

Implementaron `TestLimpiarAgotados`, comprobando que los productos agotados sean eliminados y que los productos que todavía cuentan con existencias permanezcan intactos.

La prueba cubre correctamente el comportamiento esperado del método.

### Subtotal: **6/6 puntos**

---

## 6. Producto inexistente (`KeyError` y transaccionalidad)

### Identificación — 1/1 punto

Identificaron correctamente el fallo producido al intentar acceder directamente a una clave que no existe dentro del diccionario de inventario.

### Solución — 1/2 puntos

Implementaron una validación mediante:

```python id="7m1q4x"
if id_prod not in self.inventario:
    raise ValueError(...)
```

Esto permite controlar correctamente el caso de un producto inexistente y evita que se produzca el `KeyError` original.

Sin embargo, la validación se encuentra dentro del ciclo que procesa los productos del pedido.

Esto genera un problema de transaccionalidad: si el carrito contiene primero un producto válido y posteriormente uno inexistente, el producto válido puede modificar el inventario antes de que se genere el `ValueError`.

Por lo tanto, la solución cumple con el control básico de la excepción, pero no garantiza que el pedido se procese de manera atómica.

Para obtener el puntaje completo, se debería realizar primero una validación de todos los productos del carrito y comenzar las modificaciones únicamente cuando todas las validaciones hayan sido exitosas.

### Tests — 3/3 puntos

Escribieron pruebas funcionales utilizando `pytest.raises(ValueError)` para verificar que se genere la excepción correspondiente.

Además, comprobaron el mensaje asociado al error, permitiendo validar no solamente el tipo de excepción sino también la información proporcionada al usuario.

### Subtotal: **5/6 puntos**

---

# Resultado de la evaluación

| Componente | Puntaje obtenido | Puntaje máximo |
|---|---:|---:|
| Identificación de errores | **6** | 6 |
| Implementación de soluciones | **11** | 12 |
| Tests | **18** | 18 |
| **Total** | **35** | **36** |

## Calificación final

**35/36 puntos — 97,22 %**
