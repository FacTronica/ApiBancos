# ApiBancos

API Diseñada para que el programador pueda realizar una rápida Integración de Sistema Propio con el Banco.
El objetivo de esta api es descargar la cartola bancaria del rango de fecha seleccionada.
La comunicación entre el Servidor y Cliente se realiza a traves de Datos Json.

## CARACTERÍSTICAS:

**Compatibilidad:** Windows, Linux y Mac.

**Integración:** ApiRest Datos Json.

**Código Abierto:** Se entrega el código fuente.

## Documentación

A continuación se detalla el procedimiento a seguir para realizar la integración.

-   [01.-Enviar Petición Json]
-   [02.-Esperar respuesta]
-   [03.-Recibir Respuesta Json] 

## BANCOS COMPATIBLES:

La Api actualmente es compatible con los siguientes bancos.
-Banco Chile Empresas
-Banco Santander Empresas
-Banco Estado Personas
-Banco Scotiabank Personas

## BANCO CHILE - CONSULTAR CARTOLA
````
curl -k -d "accion=BANCOCHILE&desde=20240218&hasta=20240219&rut=11222333-4&clave=claveusuariobco&cuenta=11.222.333-4&token_api=API-KEY-USER" -H "Content-Type: application/x-www-form-urlencoded" -X POST http://152.174.83.224:3000
````

## BANCO CHILE - DATOS DE ENTRADA

desde = Fecha inicio de la cartola en formato aaammdd
hasta = Fecha inicio de la cartola en formato aaammdd
rut = rut persona en formato 11222333-4 ( sin puntos y con guión )
clave = clave de la persona
cuenta = Rut de la cuenta empresa a consultar en formato 11.222.333-4 ( con puntos y guión )
token_api = Clave de acceso a la api ( se debe solicitar a factronicaerp@gmail.com )

## BANCO CHILE - DATOS DE SALIDA
````

..
````



 

