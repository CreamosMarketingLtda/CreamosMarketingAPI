# CreamosMarketingAPI

Documentacion del API de ilio, para integraciones con plataformas de terceros, con el API es posible crear y consultar polizas. Los detalles sobre como autenticarse y utilizar el API los pueden encontrar en Trabajando con el API.

### Trabajando con el API

El API contiene toda la funcionalidad que necesitas para crear y consultar una poliza.  
  1. Crear una póliza
  2. Consultar póliza
  3. Recibir callback con la poliza 

Una vez creada la poliza el asegurado recibira una copia en su correo y el asesor recibe la url con la poliza para descargar en PDF.
El API de ilio se puede consumir por medio de request HTTPS realizada a la url:

http://ilio.creamosmarketing.com:5555/v1/api-caribe

La documentación está escrita usando la URL de Pruebas, cuando quieras usar el servicio en Producción solo cambiala a la url correspondiente.
Lo primero que necesitas para usar el api es saber como autenticarte.


### Autentificación

Para utilizar el API necesitaras 2 cosas, el (id) o la API key generado por ilio al momento de crearles un usuario en la plataforma y una clave de acceso que llamaremos (pd). es necesaria para acceder a la api de ilio de forma general y obtener el token desde el metodo login. el token de acceso es necesario para realizar operaciones usando tu cuenta este tiene un tiempo de duracion de 24 horas.

Estas credenciales se deben incluir en los headers de tus request al api. Authorization.

por ejemplo, para consultar la poliza usando Javascript harias algo asi:

 

 ###### Un simple POST con el header de Authorization

``` 
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({"id":"ZGlnaXRhbEBnYXNjYXJpYmUuY29t","idConvenio":null,"pd":"Z2FzY2FyaWJlMjAyMQ=="});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("http://ilio.creamosmarketing.com:5555/login/", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```




#### ¿Como accedo a la API de Ilio?

Una vez tengas un usuario en la plataforma ilio se te generara el (id) y el Password (pd).
Una vez tengas el token, accedes al siguiente endpoint:

```
URL: http://ilio.creamosmarketing.com:5555/
METHOD: POST
Content-Type: application/json
```
El cuerpo de tu request debe ser un objeto de json convertido a texto (text/json), las propiedades que se deben enviar son:

| Parametro | Tipo | Descripción |
| -- | -- | -- |
| id | String |  API key generado por ilio (identid)
| idConvenio | String | 	null | 
| pd | String |  Contraseña

La respuesta que deberias obtener tendra estos parametros:

| Parametro | Tipo | Descripción |
| -- | -- | -- |
| userId | int | id del usuario |
| convenioId | String | id del convenio solo si tiene |
| status | String |	"ok" |
| errorMessage | String | 	null |
| token | String | Token de acceso con una validez de 24 horas |
| username | String | Nombre del usuario |
  
Por ejemplo para obtener tu token de acceso usando javascript:

```
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({"id":"c2FyYS5oYWFnQGFzcHNvbHMuY29t","idConvenio":null,"pd":"bWFxMjAwNg=="});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("D", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
 

//la respuesta en consola seria:
{
    "userId": 621,
    "convenioId": null,
    "status": "ok",
    "errorMessage": null,
    "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI2MjEiLCJpc3MiOiIxIiwiZXhwIjoxNjIzNDIwODc5fQ.6Du8LUbal4PHHXHcOtHzaeyAOZI9rSRQWM_B70jqe0xoYBACmEWwJrs40KsnawI95PtD5hI6lZzmc6lFzqjnJQ",
    "username": "ASESOR DIGITAL CARIBE"
}
```
El token recibido va a servirte para realizar peticiones por otras 24 horas, en ese momento deberás renovarlo, usando este mismo endpoint.

####  ¿Que viene ahora?

Antes de que puedas crear tu primera solicitud y obtener la póliza, debes consultar el colectivo Vigente.

### Obtener Colectivo Vigente

Este servicio puede ser llamado periodicamente, puede ser diario, semanal, con la frecuencia que prefieras.

El Endpoint para consultar el servicio de Colectivo Vigente:

``` 
URL: http://ilio.creamosmarketing.com:5555/v1/api-caribe/colectivo/vigente/
Method: GET
Content-Type: application/json

``` 


| Parametro | Descripción |
| -- | -- |
| moduloId | id del producto (seguro funerario="10", practiseguro="18", futuro protegido plus="21") |
| aseguradoraId | id de la aseguradora (Liberty Seguros="7", Seguros Alfa="11")|
| entiFactId | entidad de facturación (Gases del Caribe =”9”) |


El ejemplo para consumir el endpoint es:

http://ilio.creamosmarketing.com:5555/v1/api-caribe/colectivo/vigente/?colectivo=0000

Ejemplo del request:

```
{
    "fechaDeVenta":"2021-08-01",
    "entidadFacturacion": 9,
    "aseguradora":7,
    "producto":10
      
   }

```

####   Response

En caso de que haya colectivo vigente a la fecha actual, el servicio le retornara la información y código de respuesta que vera a continuación.


``` 
 
let newSolicitud =[{
      {
    "load": {
        "id": 876,
        "codigoInterno": "7709",
        "descripcion": "Septiembre",
        "estado": 1,
        "meta": 200
    },
    "responseCode": 200,
    "message": "Solicitud procesada con ï¿½xito",
    "page": null,
    "pageSize": null,
    "totalRecords": null

De lo contrario le mostrara esta información con el respectivo código de error.
{
    "load": null,
    "responseCode": 4002,
    "message": "No existe colectivo vigente a la fecha",
    "page": null,
    "pageSize": null,
    "totalRecords": null
}

```

### Crear una poliza

El endpoint para crear es:

``` 
URL: http://ilio.creamosmarketing.com:5555/v1/api-caribe/polizas/insertar/
Method: POST
Content-Type: application/json
```
El cuerpo de tu request debe ser un objeto de json convertido a texto, las propiedades que se deben enviar y su contenido son:
 
| Parametro | Tipo | Descripción |
| -- | -- | -- |
| entidadFacturacion | Long | Valor estatico 9|
| contrato | Long | número de contrato creado con anticipación |
| localidad | Long |  requiere de servicio de localidades guardadas |
| documento| String | número del documento del asegurado |
| tipoDoc | String | tipo documento asegurado |
| nombre1 | String | primer nombre del asegurado |
| nombre2 | String | segundo  nombre del asegurado |
| apellido1 | String | primer apellido del asegurado |
| apellido2 | String | segundo apellido del asegurado |
| celular | String | celular del asegurado |
| telefono | String | telefono del asegurado |
| correo | String | correo electrónico del asegurado |
| genero | String | sexo del asegurado |
| direccion | String | direccion registrada del contrato |
| fechaDeNacimiento | String | formato yyyyy-mm-dd |
| fechaDeExpedicion | String | formato yyyyy-mm-dd |
| fechaDeVenta | String | formato yyyyy-mm-dd |
| aseguradora | Long | id de la entidad con la que se toma el seguro ( 7 = Liberty Seguros S.A., 11 = Seguros Alfa S.A. )|
| producto | Long | id del producto seleccionado|
| plan | Long | id del plan relacionado al producto|
| numPapeleria | String | este valor puede ir null |
| estado | Long | este valor puede ir null |
| polizaBeneficiario | List<PolizaBeneficiario> | listado de beneficiarios de la póliza |
| porcentaje| polizaBeneficiario/Double| % de indemnizacion |
| parentesco | polizaBeneficiario | Se debe consumir un servicio para obtener parentesco |
| nombre | polizaBeneficiario/String | nombre del beneficiario de la póliza |
 
 Ejemplo de objeto

#####  Posibles respuestas
| Código HTTP | Causa | 
| -- | -- |
| 200 | Solicitud creada | 
| 500 | Faltan campos o algun campo invalido |  
v

``` 
{
   "timestamp":1623435974405,
   "status":500,
   "error":"Internal Server Error",
   "exception":"java.lang.RuntimeException",
   "message":"El contrato (162) ya supero el numero de productos de SEGURO FUNERARIO permitidos a un contrato; \n",
   "path":"/v1/api-caribe/polizas/"
   
``` 


Si todo es correcto recibiras una respuesta con estado 201(creado) y un objeto json con estos parametros:

| Parametro | Tipo | Descripción |
| -- | -- | -- |
| num_poliza | int | id de la poliza generada por ilio |
| estado_poliza | int | estado de la solicitud 10: DIGITADA WEB. 33: EN PROCESO DE AUDITORIA. 34: SOLICITUD RECHAZADA. 3: pagado. 104: ERROR DE CREACION ......
| mensaje | String | descripcion del estado de la poliza |
| url_poliza | String | Código numerico de identificacion de la poliza. Con este se accede a la vista web en https://ilio.creamosmarketing.com:5555/solicitud/{url_poliza} |
| platform | String | plataforma por donde se tramito el seguro |

 

Por ejemplo para crear una solicitud usando Javascript y procesar la respuesta:

``` 
 
let newSolicitud =[{
        {
 "entidadFacturacion":9,
 "contrato":66519994,
 "localidad":10,
 "documento":1080024550,
 "tipoDoc":"CC",
 "nombre1":"Ejemplo Inserta",
 "nombre2":"Ejemplo Inserta",
 "apellido1":"Ejemplo Inserta",
 "apellido2":"Ejemplo Inserta",
 "celular":"3126378524",
 "telefono":"856526",
 "correo":"EjemploInsertarPoliza@Gmail.com",
 "genero":"M",
 "direccion":"Kr 8 · 7-69",
 "fechaDeNacimiento":"1996-05-08",
 "fechaDeExpedicion":"2014-05-24",
 "fechaDeVenta":"2021-08-01",
 "aseguradora":7,
 "producto":10,
 "plan":252, 
 "asesor":5,// el id del asesor que le retorno en el primer EndPoint
 "colectivo":59,// requiere de un servicio de consulta de colectivo
 "operCom":48, // el id del asesor que le retorno en el primer EndPoint
 "tarifa":18810,// requiere de un servicio de consulta de tarifa
 "polizaBeneficiario":[{
 "porcentaje":100,
 "parentesco":{
     "id":3,
     "nombre":"HIJO(A)"},
 "nombre":"nombres beneficiario"
 }]

}

let  Response =[{
 {
    "load": "Ok",
    "responseCode": 200,
    "message": "Solicitud procesada con ï¿½xito",
    "page": null,
    "pageSize": null,
    "totalRecords": null
}


    if(response.status.toString() == "200"){ 
            let res = await response.json();
            console.log(res);
    }

    //el output de la consola seria:
    {
        "numPoliza" : 2272889,
        "estadoPoliza": 10, // estado interno deacuerdo al modelo de negocio
        "mensaje": "DIGITADA WEB", 
        "url_poliza": "ct9yd3g3",
        "asesor": "ASESOR DIGITAL GASES DEL CARIBE "
    }
        
```
Nota: Código 200 quiere decir que se insertó correctamente de lo contrario mostrará 4000, quiere decir que ocurrió algún problema y no se logró el cargue de la póliza.
  
 Algunos otros errores son:
  
Error 4005 = La poliza a insertar cuenta con un numero de papeleria que ya se encuentra registrada en otra poliza actualmente.

Error 4006 = La poliza a insertar excede el numero de Modulos/Productos permitidos en un contrato especifico

Error 4007 = La poliza a insertar excede el numero de Modulos/productos totales permitidos en un contrato





### Consultar poliza por contrato y cedula de asegurado

Para obtener los detalles de una solicitud se usa este endpoint:

``` 
URL: http://ilio.creamosmarketing.com:5555/v1/api-caribe/poliza/contrato/
Method: GET
Content-Type: application/json
```
  
{num_poliza} es la url alfa númerica que recibes al crear la poliza, solo puedes consultar la poliza que te pertenescan, y tu request debe estar autenticada con el header de Authorization, como se menciona en el Apartado de Autentificación.

 Ejemplo  http:// ilio.creamosmarketing.com:5555/v1/api-caribe/poliza/contrato/idEntidadFact=9&idAseguradora=7&idModulo=10&numContrato=17129224&docAsegurado=1100332016



| Parametro | Tipo | Descripción |
| -- | -- | -- |
| producto |Long |  id del producto (seguro funerario="10", practiseguro="18", futuro protegido plus="21") |
| aseguradora | Long | id de la entidad con la que se toma el seguro ( 7 = Liberty Seguros S.A., 11 = Seguros Alfa S.A. ) |
| entidadFacturacion  | Long | Valor estatico (Gases del Caribe = "9") |
| numContrato | Long | numero del contrato valido|
| docAsegurado | Long | numero de identificación del asegurado|

 
Posibles estados de una poliza

El campo state que recibes en este endpoint describe el estado de la poliza, y puede tener los siguientes valores:

| Estado | Valor del campo state |
| -- | -- | 
| ERROR DE CREACION | 104 | 
| CANCELADA | 14 | 
| DIGITADA WEB | 7 | 
| AUDITADA WEB | 89 | 
| ALTA TRANSACCIONAL | 11 |
| AUDITADA | 10 | 
| DIGITADA APP | 9 | 
 
 Ejemplo de request:
  
  
  ``` 
  {
    
    "entidadFacturacion": 9,
    "aseguradora":7,
    "producto":10,
    "numContrato": "420808" 
    "docAsegurado": "1100332016"
    
    
   }

  
  ```
 
 Ejemplo de response:
 
 ```
  
  {
    "load": [
        "poliza": 1518,
    ],
    "responseCode": 200,
    "message": "Solicitud procesada con ï¿½xito",
    "page": null,
    "pageSize": null,
    "totalRecords": null
}

 
```

  
  ### Consultar poliza por Id

  Se puede consultar pólizas solo usando el id correspondiente.  Para obtener los detalles de una solicitud se usa este endpoint:

``` 
URL: http://ilio.creamosmarketing.com:5555/v1/api-caribe/poliza/consultaById/
Method: GET
Content-Type: application/json
```
 
 Ejemplo  http: //ilio.creamosmarketing.com:5555/v1/api-caribe/poliza/consultaById/?idPoliza=2

  
  Ejemplo de request:
  
  ```
  {
    
    "entidadFacturacion": 9,
    "idPoliza": 2
   
   }

  
  
  ```
  
  
 Ejemplo de response:
 
```
    {{
    "load": {
        "fechaventa": "15-2-2019",
        "producto": "SEGURO PAZ Y SALVO",
        "aseguradora": "Liberty Seguros S.A.",
        "idPoliza": 745,
        "urlPoliza": "http://ilio.creamosmarketing.com/files/download/?path=/home/admincmk/archivos/polizas/745_1102.png",
        "idEstado": 11,
        "descripcionEstado": "ALTA TRANSACCIONAL"
    },
    "responseCode": 200,
    "message": "Solicitud procesada con ï¿½xito",
    "page": null,
    "pageSize": null,
    "totalRecords": null
}

  }
  
```
  ### Agendamiento

Para obtener los detalles de una solicitud se usa este endpoint:

``` 
URL: http://ilio.creamosmarketing.com:5555/v1/api-caribe/polizas/Agendamiento
Method: GET
Content-Type: application/json
```
  
Se recibe la información de contacto del usuario, para ser almacenada en Ilio y proceder con el agedamiento de llamada por parte de un asesor comercial
  


| Parametro | Tipo | Descripción |
| -- | -- | -- |
| id | Long | id del servicio de agendamiento, debe ser 1 el valor |
| producto |Long |  id del producto (seguro funerario="10", practiseguro="18", futuro protegido plus="21") |
| aseguradora | Long | id de la entidad con la que se toma el seguro ( 7 = Liberty Seguros S.A., 11 = Seguros Alfa S.A. ) |
| entidadFacturacion  | Long | Valor estatico (Gases del Caribe = "9") |
| numContrato | Long | numero del contrato valido|
| documento | Long | numero de identificación del asegurado|
| tipoDoc | String | tipo documento asegurado |
| nombre1 | String | primer nombre del asegurado |
| nombre2 | String | segundo  nombre del asegurado |
| apellido1 | String | primer apellido del asegurado |
| apellido2 | String | segundo apellido del asegurado |
| celular | String | celular del asegurado |
| fechaDeVenta | String | formato yyyy-mm-dd |
  
  
  Ejemplo de request:
  
  ```
  
  {
    "load": {
        
        "id":1,
        "producto":111,
        "aseguradora":777,
        "entidadFacturacion":"999",
        "documento":1080024550,
        "tipoDoc":"CC",
        "nombre1":"Ejemplo Inserta111111",
        "nombre2":"Ejemplo Inserta222222",
        "apellido1":"Ejemplo Inserta3333333",
        "apellido2":"Ejemplo Inserta444444",
        "celular":"3126378524",
        "fechaDeVenta": "2021-10-23",
  
  }

  }
  
  
  ```
  
  Ejemplo de Response:
  
  ```
  
  let  Response =[{
 {
    "load": "Ok",
    "responseCode": 200,
    "message": "Solicitud procesada con ï¿½xito",
    "page": null,
    "pageSize": null,
    "totalRecords": null
}
  ```
  Mapeo de Errores 
  
  ```
  
  {
  
  Error 4010 = No existe o no se encontro ningun body o parametros enviados al end point

Error 4011 = el campo 'nombre1' es obligatorio y se encuentra vacío o nulo

Error 4012 = el campo 'nombre1' debe tener solo caracteres y una longitud de minimo 4 caracteres

Error 4013 = el campo 'apellido1' es obligatorio y se encuentra vacio o nulo

Error 4014 =  el campo 'apellido1' debe tener solo caracteres y una longitud de minimo 4 caracteres

Error 4015 = el campo 'documento' es obligatorio y se encuentra vacío o nulo

Error 4016 = el campo 'documento' debe tener solo caracteres numéricos y una longitud de mínimo 8 caracteres

Error 4017 = el campo 'celular' es obligatorio y se encuentra vacío o nulo

Error 4018 = el campo 'celular' debe tener solo caracteres numéricos y una longitud de mínimo 7 caracteres

Error 4019 = los campos 'producto' , 'aseguradora' , 'entidadFacturacion' y 'contrato' no pueden ser nulos o vacios

Error 4020 =  los campos 'producto' , 'aseguradora' , 'entidadFacturacion' y 'contrato' deben tener solo caracteres numéricos y una longitud de minimo 1









  
  }
  
  
  
  
