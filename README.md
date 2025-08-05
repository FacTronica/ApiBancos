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
curl -k -d "accion=BANCOCHILE&desde=20250801&hasta=20250804&rut=11222333-5&clave=abc123&cuenta=11.111.111-6&token_api=abc123" -H "Content-Type: application/x-www-form-urlencoded" -X POST http://servidor9.sistemafactronica.cl:3426
````

## BANCO CHILE - DATOS DE ENTRADA

<br><b>desde</b> = Fecha inicio de la cartola en formato aaammdd
<br><b>hasta</b> = Fecha inicio de la cartola en formato aaammdd
<br><b>rut</b> = rut persona en formato 11222333-4 ( sin puntos y con guión )
<br><b>clave</b> = clave de la persona
<br><b>cuenta</b> = Rut de la cuenta empresa a consultar en formato 11.222.333-4 ( con puntos y guión )
<br><b>token_api</b> = Clave de acceso a la api ( se debe solicitar a factronicaerp@gmail.com )

## BANCO CHILE - DATOS DE SALIDA
````
{


"Movimientos":
[
{"Fecha":"","Tipo":"","Monto":"","Glosa":""},
{"FECHA":"20240220","TIPO":"C","MONTO":"50000","GLOSA":"App-traspaso A: Juan Perez"},
{"FECHA":"20240220","TIPO":"A","MONTO":"550000","GLOSA":"Traspaso De: Megamarket S.A.},
{"FECHA":"20240220","TIPO":"C","MONTO":"30000","GLOSA":"App-traspaso A: Lapiz Lopez"}
],


"Saldos":
[
{"SaldoDisponible":"1800000","SaldoContable":"1800000","Retenciones1":"200000","Retenciones2":"800000"}
],


"Consulta":
[
{"RutUsuario":"11222333-4","Cuenta":"22.333.444-5","FechaDesde":"20230901","FechaHasta":"20230914"}
]   


}
````

## Ejemoplo en Php
````
<?php
/**
 * Script: consumir_api_banco.php
 * Objetivo: Enviar una petición POST application/x-www-form-urlencoded a la API,
 *           recibir JSON, validarlo y extraer Movimientos, Saldos y Consulta.
 */

/* 1) URL del endpoint tal como en tu curl */
$endpoint = 'http://servidor9.sistemafactronica.cl:3426';

/* 2) Datos del formulario exactamente como en tu ejemplo curl (clave=valor) */
$postFields = array(
    'accion'    => 'BANCOCHILE',      // Acción solicitada por el backend
    'desde'     => '20250801',        // Fecha desde (YYYYMMDD)
    'hasta'     => '20250804',        // Fecha hasta (YYYYMMDD)
    'rut'       => '11222333-5',      // RUT del usuario
    'clave'     => 'abc123',          // Clave (según tu API)
    'cuenta'    => '11.111.111-6',    // Número de cuenta (formato según tu API)
    'token_api' => 'abc123'           // Token de autenticación del API
);

/* 3) Convertimos el arreglo a query string: accion=...&desde=...&... */
$postBody = http_build_query($postFields);

/* 4) Inicializamos cURL (cliente HTTP nativo de PHP) */
$ch = curl_init();

/* 5) Indicamos la URL del endpoint */
curl_setopt($ch, CURLOPT_URL, $endpoint);

/* 6) Indicamos que será una petición POST */
curl_setopt($ch, CURLOPT_POST, true);

/* 7) Cuerpo del POST: nuestra query string form-urlencoded */
curl_setopt($ch, CURLOPT_POSTFIELDS, $postBody);

/* 8) Cabeceras HTTP: tipo de contenido form-urlencoded (como en tu curl -H ...) */
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Content-Type: application/x-www-form-urlencoded'
));

/* 9) Recibir la respuesta como string (no imprimir directamente en salida) */
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

/* 10) Tiempo máximo para conectar (segundos) */
curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 10);

/* 11) Tiempo máximo total de la petición (segundos) */
curl_setopt($ch, CURLOPT_TIMEOUT, 30);

/* 12) Equivalente a curl -k (NO verificar SSL). Tu endpoint es http,
       pero si fuese https con cert no válido, esto evita que falle.
       En producción, lo recomendable es VERIFICAR SSL (true/true). */
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

/* 13) Ejecutamos la petición y capturamos el cuerpo de la respuesta */
$responseBody = curl_exec($ch);

/* 14) Obtenemos código HTTP de respuesta para depurar (200=OK, etc.) */
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);

/* 15) Si cURL falló a nivel de red/transferencia, capturamos el error */
if ($responseBody === false) {
    $curlError = curl_error($ch); // Mensaje de error de cURL
    curl_close($ch);              // Cerramos el handle
    // Mostramos un error claro y abortamos
    die('Error cURL: ' . $curlError);
}

/* 16) Cerramos el handle cURL (buena práctica) */
curl_close($ch);

/* 17) Validamos el código HTTP (por ejemplo, 200=éxito) */
if ($httpCode < 200 || $httpCode >= 300) {
    // Si el servidor respondió con error (4xx/5xx), lo informamos
    die('Error HTTP ' . $httpCode . '. Respuesta: ' . $responseBody);
}

/* 18) Intentamos decodificar el JSON a un arreglo asociativo (true) */
$data = json_decode($responseBody, true);

/* 19) Verificamos errores de JSON (por ejemplo, JSON mal formado) */
if (json_last_error() !== JSON_ERROR_NONE) {
    // Capturamos el mensaje de error de JSON
    $jsonError = json_last_error_msg();
    // Mostramos el error y opcionalmente el cuerpo recibido para depurar
    die('Error al decodificar JSON: ' . $jsonError . "\n" . 'Cuerpo recibido: ' . $responseBody);
}

/* 20) Extraemos secciones esperadas del JSON con valores por defecto */
$movimientos = isset($data['Movimientos']) && is_array($data['Movimientos']) ? $data['Movimientos'] : array();
$saldos      = isset($data['Saldos'])      && is_array($data['Saldos'])      ? $data['Saldos']      : array();
$consulta    = isset($data['Consulta'])    && is_array($data['Consulta'])    ? $data['Consulta']    : array();

/* 21) --- EJEMPLOS DE USO/IMPRESIÓN --- */

/* 21.1) Mostrar cantidad de elementos en cada sección */
echo "Movimientos: " . count($movimientos) . "\n";
echo "Saldos: "      . count($saldos)      . "\n";
echo "Consulta: "    . count($consulta)    . "\n\n";

/* 21.2) Recorrer y mostrar cada movimiento (si hay) */
if (!empty($movimientos)) {
    echo "==== Movimientos ====\n";
    foreach ($movimientos as $idx => $m) {
        // Tomamos valores con fallback a cadena vacía si no existen
        $fecha = isset($m['FECHA']) ? $m['FECHA'] : (isset($m['Fecha']) ? $m['Fecha'] : '');
        $tipo  = isset($m['TIPO'])  ? $m['TIPO']  : (isset($m['Tipo'])  ? $m['Tipo']  : '');
        $monto = isset($m['MONTO']) ? $m['MONTO'] : (isset($m['Monto']) ? $m['Monto'] : '');
        $glosa = isset($m['GLOSA']) ? $m['GLOSA'] : (isset($m['Glosa']) ? $m['Glosa'] : '');

        // Imprimimos formateado
        echo sprintf(
            "#%d  FECHA: %s | TIPO: %s | MONTO: %s | GLOSA: %s\n",
            $idx + 1,
            $fecha,
            $tipo,
            $monto,
            $glosa
        );
    }
    echo "\n";
} else {
    echo "No hay movimientos.\n\n";
}

/* 21.3) Mostrar saldos (si vienen) */
if (!empty($saldos)) {
    echo "==== Saldos ====\n";
    foreach ($saldos as $s) {
        $saldoDisp  = isset($s['SaldoDisponible']) ? $s['SaldoDisponible'] : '';
        $saldoCont  = isset($s['SaldoContable'])   ? $s['SaldoContable']   : '';
        $ret1       = isset($s['Retenciones1'])    ? $s['Retenciones1']    : '';
        $ret2       = isset($s['Retenciones2'])    ? $s['Retenciones2']    : '';
        echo "SaldoDisponible: $saldoDisp | SaldoContable: $saldoCont | Retenciones1: $ret1 | Retenciones2: $ret2\n";
    }
    echo "\n";
} else {
    echo "No hay saldos.\n\n";
}

/* 21.4) Mostrar parámetros de la consulta (si vienen) */
if (!empty($consulta)) {
    echo "==== Consulta ====\n";
    foreach ($consulta as $c) {
        $rutUsuario  = isset($c['RutUsuario']) ? $c['RutUsuario'] : '';
        $cuenta      = isset($c['Cuenta'])     ? $c['Cuenta']     : '';
        $fechaDesde  = isset($c['FechaDesde']) ? $c['FechaDesde'] : '';
        $fechaHasta  = isset($c['FechaHasta']) ? $c['FechaHasta'] : '';
        echo "RutUsuario: $rutUsuario | Cuenta: $cuenta | Desde: $fechaDesde | Hasta: $fechaHasta\n";
    }
    echo "\n";
} else {
    echo "No hay datos de consulta.\n\n";
}

/* 22) Si quieres usar los datos en tu lógica, ya están en:
       $movimientos (array), $saldos (array), $consulta (array) */
?>
````
 

