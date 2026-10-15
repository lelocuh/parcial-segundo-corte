# Citas veterinarias

Aplicación en Python para gestionar las citas de clínicas veterinarias: registro de clínicas, veterinarios, catálogo de servicios, propietarios y mascotas, y programación, cancelación y atención de citas con generación de factura.

Proyecto del Parcial 2 de Programación II, Universidad de Santander (UDES), docente Carlos Carrascal. Autor: Samuel Ivan Heredia Moya.

## Funcionalidades

1. Registrar una clínica.
2. Registrar los veterinarios y el catálogo de servicios de una clínica.
3. Registrar un propietario y sus mascotas (un propietario puede tener varias).
4. Programar una cita.
5. Cancelar una cita.
6. Atender una cita, agregar servicios adicionales y generar la factura.
7. Consultar las citas de una clínica en una fecha, con su estado.
8. Consultar el historial de citas de una mascota.

La aplicación se puede usar desde una interfaz web hecha con Gradio (modo por defecto) o desde un menú de consola.

## Reglas de negocio

1. Una cita puede estar en estado Programada, Cancelada o Atendida. Al crearse queda Programada.
2. Un veterinario no puede tener dos citas programadas en la misma fecha y hora.
3. Una mascota no puede tener dos citas programadas en la misma fecha y hora.
4. Solo se puede cancelar una cita Programada, antes de la fecha de la cita. Las citas canceladas no se eliminan: cambian de estado y quedan en el historial.
5. Solo se puede atender una cita Programada. Al atenderla pasa a Atendida y se genera su factura.
6. La factura tiene número único, fecha, datos del propietario y de la mascota, el detalle de servicios con su tarifa y el valor total, que es la suma de las tarifas.
7. No se pueden registrar dos clínicas con el mismo NIT, dos propietarios con la misma cédula en una misma clínica, ni dos servicios con el mismo código en una misma clínica.
8. Cuando una operación incumple una regla, la aplicación informa el motivo con un mensaje claro y no se detiene.

## Requisitos

Python 3.10 o superior (recomendado, por la versión de Gradio). El modo consola solo usa la biblioteca estándar. El modo web necesita Gradio, que se instala con el archivo de requisitos.

## Instalación y ejecución

Desde la carpeta del proyecto:

```
pip install -r requirements.txt
python main.py
```

El comando abre la interfaz web. La terminal muestra la dirección local (por defecto, http://127.0.0.1:7860). Ábrela en el navegador.

Para usar el menú de consola, que no necesita Gradio:

```
python main.py --consola
```

Los datos se guardan en memoria y se pierden al cerrar el programa.

## Uso de la interfaz web

La interfaz tiene una pestaña por funcionalidad: Ejemplo, Registros (clínica, veterinario, servicio, propietario y mascota), Datos, Programar, Cancelar, Atender y Consultas. En la pestaña Ejemplo, el botón "Cargar datos de ejemplo" registra dos clínicas con sus veterinarios y catálogos de servicios. Se pulsa una sola vez por ejecución.

La pestaña Cancelar trae la fecha actual rellena con el día de hoy. Se puede cambiar, porque la cancelación solo se permite antes de la fecha de la cita.

## Datos de ejemplo

| Clínica | NIT | Municipio | Veterinarios | Servicios |
|---|---|---|---|---|
| Clínica Veterinaria Huellitas | 901456789-1 | San Gil, Santander (DANE 68679) | Juliana Ortiz Ramírez, Mauricio Pinzón Gómez | CG, VAC, DES, PEL |
| Clínica Veterinaria Patitas Felices | 900987654-3 | Floridablanca, Santander (DANE 68276) | Sergio Arenas Villamizar, Diana Carolina Rey Moreno, Felipe Castellanos Díaz | CG, VAC, DES, ECO |

## Casos de prueba

Los cinco casos del enunciado, en el orden en que se pueden ejecutar:

| Caso | Situación | Resultado esperado |
|---|---|---|
| 1 | Cita atendida con servicio adicional | Cita CIT-001 de Rocky; al atenderla con vacunación se genera la factura FAC-001 por $ 105.000 |
| 2 | Veterinario ocupado | Se rechaza la cita de Copito a las 9:00 porque la veterinaria ya tiene CIT-001; a las 10:00 queda CIT-002 |
| 3 | Cita cancelada | CIT-003 de Luna queda Cancelada el 11 de noviembre de 2026 y no se genera factura |
| 4 | Cancelación no permitida | Se rechaza cancelar CIT-001 porque ya está Atendida |
| 5 | Consultas | Las citas de Huellitas del 10 de noviembre de 2026 muestran CIT-001 Atendida y CIT-002 Programada; el historial de Luna muestra CIT-003 Cancelada |

El caso 1 se completa después del caso 3, porque el caso 2 necesita que CIT-001 siga Programada.

## Arquitectura

La aplicación tiene tres capas, cada una en su paquete, más la clase Main que crea los objetos y los conecta.

| Capa | Paquete | Contenido |
|---|---|---|
| Presentación | presentacion | InterfazGradio y MenuConsola, que llaman a los mismos servicios |
| Lógica | logica | Entidades, reglas de negocio, servicios, excepciones y datos de ejemplo |
| Datos | datos | Repositorios en memoria, basados en diccionarios |

Las entidades son Municipio, Clinica, Veterinario, Servicio, Propietario, Mascota, Cita, Factura y LineaFactura, más la enumeración EstadoCita. Los atributos son privados y se exponen con @property. Los errores de reglas de negocio heredan de ReglaNegocioError (DuplicadoError, NoEncontradoError, ConflictoAgendaError, EstadoCitaError, FechaCancelacionError y ValidacionError), y la capa de presentación los captura para mostrar el motivo sin detener el programa.

## Estructura del proyecto

```
citas_veterinarias/
├── main.py                      clase Main (modo web o --consola)
├── requirements.txt
├── presentacion/
│   ├── interfaz_gradio.py       InterfazGradio
│   └── menu_consola.py          MenuConsola
├── logica/
│   ├── municipio.py, clinica.py, veterinario.py, servicio.py
│   ├── propietario.py, mascota.py
│   ├── estado_cita.py, cita.py
│   ├── factura.py, linea_factura.py
│   ├── servicio_clinicas.py, servicio_citas.py
│   ├── datos_ejemplo.py
│   └── excepciones.py
└── datos/
    ├── repositorio_clinicas.py
    ├── repositorio_citas.py
    └── repositorio_facturas.py
```
