# ApiBancos
API Diseñada para que el programador pueda realizar una rápida Integración de Sistema Propio con el Banco.
El objetivo de esta api es descargar la cartola bancaria del rango de fecha seleccionada.
La comunicación entre el Servidor y Cliente se realiza a traves de Datos Json.

## Características:
-   🔲 **Compatibilidad:** Windows, Linux y Mac.

-   🖥 **Integración:** Datos Json.

-   🌐 **Soporte:** Soporte Técnico para Implementación


## Documentación

A continuación se detalla el procedimiento a seguir para realizar la integración.

-   [01.-Enviar Petición Json]
-   [02.-Esperar respuesta]
-   [03.-Recibir Respuesta Json] 

## Bancos Compatibles:

La Api actualmente es compatible con los siguientes bancos.

## PETICIÓN CURL CARTOLA BANCO CHILE EMPRESAS
curl -k -d "accion=BANCOCHILE&desde=20240201&hasta=20240220&rut=11222333-4&clave=clavedelbanco&cuenta=11.222.333-4&token_api=TOKEN-ACCESO-API" -H "Content-Type: application/x-www-form-urlencoded" -X POST http://45.236.128.126:3426



 

