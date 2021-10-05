# CreamosMarketingAPI

Documentacion del API de ilio, para integraciones con plataformas de terceros, con el API es posible crear y consultar polizas. Los detalles sobre como autenticarse y utilizar el API los pueden encontrar en Trabajando con el API.

### Trabajando con el API

El API contiene toda la funcionalidad que necesitas para crear y consultar una poliza.  
  1. Crear una póliza
  2. Consultar póliza
  3. Recibir callback con la poliza 

Una vez creada la poliza el asegurado recibira una copia en su correo y el asesor recibe la url con la poliza para descargar en PDF.
El API de ilio se puede consumir por medio de request HTTPS realizada a la url:

http://ilio.creamosmarketing.com:5555/

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

| Parametro | Descripción |
| -- | -- |
| moduloId | Aquí iría el id del producto (seguro funerario="10", practiseguro, futuro protegido plus="21") |
| aseguradoraId | Aquí el id de la aseguradora (Liberty Seguros="7", Seguros Alfa="11")|
| entiFactId | Aquí entidad de facturación (Gases del Caribe =”9”) |

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
URL: http://ilio.creamosmarketing.com:5555/secure/polizas/api-caribe/
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
   "path":"/secure/polizas/"
   
``` 


Si todo es correcto recibiras una respuesta con estado 201(creado) y un objeto json con estos parametros:

| Parametro | Tipo | Descripción |
| -- | -- | -- |
| num_poliza | int | id de la poliza generada por ilio |
| estado_poliza | int | estado de la solicitud 10: DIGITADA WEB. 33: EN PROCESO DE AUDITORIA. 34: SOLICITUD RECHAZADA. 3: pagado. 104: ERROR DE CREACION ......
| mensaje | String | descripcion del estado de la poliza |
| url_poliza | String | Código alfanumerico de identificacion de la poliza. Con este se accede a la vista web en https://stage.creamosmarketing.com/solicitud/{url_poliza} |
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
        num_poliza : 2272889,
        estado_poliza: 10, // estado interno deacuerdo al modelo de negocio
        mensaje: "DIGITADA WEB", 
        url_poliza: "ct9yd3g3",
        platform: "ASESOR DIGITAL GASES DEL CARIBE "
    }
        
```
Nota: Código 200 quiere decir que se insertó correctamente de lo contrario mostrará 4000, quiere decir que ocurrió algún problema y no se logró el cargue de la póliza.



### Consultar poliza

Para obtener los detalles de una solicitud se usa este endpoint:

``` 
URL: http://ilio.creamosmarketing.com:5555/secure/polizas/{num_poliza}
Method: GET
Content-Type: application/json
```

{num_poliza} es la url alfa númerica que recibes al crear la poliza, solo puedes consultar la poliza que te pertenescan, y tu request debe estar autenticada con el header de Authorization, como se menciona en el Apartado de Autentificación.


este endpoint devuelve un objeto de json con estos parametros en su body:

| Parametro | Tipo | Descripción |
| -- | -- | -- |
| num_poliza | int | el id del operador comercial dentro de ilio |
| id_asesor_comercial | int | id del asesor comercial asignado al operador |
| id_entidad_recaudo | int | entidad recaudadora |
| numero_contrato | int | numero del contrato valido|
 
 Posibles estados de una polisa

El campo state que recibes en este endpoint describe el estado de la poliza, y puede tener los siguientes valores:

| Estado | Valor del campo state |
| -- | -- | 
| ERROR DE CREACION | 104 | 
| CANCELADA | 14 | 
 
 
 
 para solicitar los detalles de una solicitud la respuesta:
 
 ``` 
 {
  "id": 32,
  "fechaVenta": "11/02/2019",
  "numPapeleria": "10000032",
  "observacionAuditoria": null,
  "numpoliza": null,
  "fechaTarifa": null,
  "fechaDigitacion": "11/02/2019 15:15:02",
  "plan": {
    "id": 11,
    "descripcion": "PLAN TRADICIONAL",
    "topeAsegurados": 2,
    "topeBeneficiarios": 3,
    "topeValorAsegurado": "5 SMLV",
    "codigoInterno": "203",
    "tmpClientId": 1,
    "estado": "A",
    "benefNominados": 0,
    "asegNominados": 1,
    "restUbicacion": 1
  },
  "convenio": null,
  "modulo": {
    "id": 10,
    "nombre": "SEGURO FUNERARIO",
    "ultimaActualizacion": 1602707848000,
    "estado": "A",
    "reporte": {
      "id": 2,
      "nombre": "CertificadoIndividualFunerario",
      "descripcion": "Seguro Funerario"
    },
    "validarFechaNace": 0,
    "repiteContrato": 0,
    "validaDocumentoBene": 0,
    "validaNumCuotas": 0,
    "validaEstadoCivil": 0
  },
  "colectivoVigente": {
    "id": 83,
    "codigoInterno": "2502",
    "descripcion": "Febrero",
    "estado": 1,
    "meta": 0
  },
  "asegurador": {
    "id": 7,
    "nit": {
      "id": 590,
      "tipoDoc": "NT",
      "documento": "860.039.988-0",
      "nombre1": null,
      "nombre2": null,
      "apellido1": null,
      "apellido2": null,
      "nombre": "Liberty Seguros S.A.",
      "telefono": "3559209",
      "telefono2": null,
      "telefono3": null,
      "telefono4": null,
      "telefono5": null,
      "telefono6": null,
      "celular": "3003559209",
      "email": "gaserasGNO@libertycolombia.com",
      "sexo": null,
      "direccion": null,
      "pais": null,
      "departamento": null,
      "municipio": null,
      "localidad": null,
      "estado": 1,
      "fechaNacimiento": null,
      "fechaExpedicion": null,
      "usuario": {
        "id": 81,
        "nombre": "liberty",
        "email": "gaserasGNO@libertycolombia.com",
        "contrasena": "$2a$10$MN62Ie8wmnJXRxwsTIvMC.Bw1eJCpftcxx.B1cTmdIGXyZzFLNHYq",
        "estado": 1,
        "ipConexion": null
      },
      "verEnPoliza": "N",
      "estadoCivil": null,
      "estudio": null,
      "ocupacion": null,
      "cantTrabajo": null,
      "hijos": null,
      "conviven": null,
      "paga": null,
      "trabajan": null,
      "csmmlv": null,
      "nomPaga": null,
      "celpaga": null,
      "propVivienda": null,
      "nombreContac": null,
      "celContact": null
    },
    "codAgenciaVenta": "279",
    "numProductoPorContrato": 2
  },
  "entidadFact": {
    "id": 11,
    "nit": {
      "id": 596,
      "tipoDoc": "NT",
      "documento": "8001676435",
      "nombre1": "MARIA",
      "nombre2": "TRINIDAD",
      "apellido1": "OSORIO",
      "apellido2": null,
      "nombre": "GASES DE OCCIDENTE",
      "telefono": "3380523",
      "telefono2": null,
      "telefono3": null,
      "telefono4": null,
      "telefono5": null,
      "telefono6": null,
      "celular": "3178244270",
      "email": "florg@gdo.com.co",
      "sexo": "F",
      "direccion": "CL 51 KR 40B - 15 PISO 01",
      "pais": null,
      "departamento": {
        "id": 58,
        "nombre": "Valle del Cauca",
        "dptoDane": "76000",
        "dptoSmartFlex": "5"
      },
      "municipio": {
        "id": 858,
        "nombre": "CALI"
      },
      "localidad": {
        "id": 10159,
        "nombre": "SANTIAGO DE CALI",
        "homologaciones": null
      },
      "estado": 1,
      "fechaNacimiento": "01/05/1952",
      "fechaExpedicion": "03/08/1973",
      "usuario": null,
      "verEnPoliza": "N",
      "estadoCivil": null,
      "estudio": null,
      "ocupacion": null,
      "cantTrabajo": null,
      "hijos": null,
      "conviven": null,
      "paga": null,
      "trabajan": null,
      "csmmlv": null,
      "nomPaga": null,
      "celpaga": null,
      "propVivienda": null,
      "nombreContac": null,
      "celContact": null
    }
  },
  "contrato": {
    "id": 1827884,
    "numeroContrato": 1407349,
    "estrato": "2",
    "direccion": "CL 57D KR 34E - 49 MZ 10 CASA 10 PISO 01 UNI_RES BOSQUES DEL EDEN",
    "departamento": {
      "id": 58,
      "nombre": "Valle del Cauca",
      "dptoDane": "76000",
      "dptoSmartFlex": "5"
    },
    "municipio": {
      "id": 884,
      "nombre": "PALMIRA"
    },
    "localidad": {
      "id": 10347,
      "nombre": "PALMIRA",
      "homologaciones": null
    },
    "titular": "ROQUELIA OREJUELA TEJADA",
    "titularNit": null,
    "infoVentaExterna": null
  },
  "asesor": {
    "id": 9,
    "nit": {
      "id": 617,
      "tipoDoc": "CC",
      "documento": "1067933118",
      "nombre1": "JESUS",
      "nombre2": "DANIEL",
      "apellido1": "DUQUE",
      "apellido2": "BARRERA",
      "nombre": "JESUS DANIEL DUQUE BARRERA",
      "telefono": "(319) 324-6949",
      "telefono2": null,
      "telefono3": null,
      "telefono4": null,
      "telefono5": null,
      "telefono6": null,
      "celular": "3193246949",
      "email": "",
      "sexo": "M",
      "direccion": "KR 91 CL 28 - 70",
      "pais": null,
      "departamento": {
        "id": 58,
        "nombre": "Valle del Cauca",
        "dptoDane": "76000",
        "dptoSmartFlex": "5"
      },
      "municipio": {
        "id": 858,
        "nombre": "CALI"
      },
      "localidad": {
        "id": 10159,
        "nombre": "SANTIAGO DE CALI",
        "homologaciones": null
      },
      "estado": 1,
      "fechaNacimiento": "12/09/1995",
      "fechaExpedicion": "13/08/1995",
      "usuario": {
        "id": 112,
        "nombre": "JESUS DUQUE",
        "email": "asesor2@creamosmarketing.com",
        "contrasena": "$2a$10$cnVzkxT0CObG4MdUc.fBXOazIDhDVuYA9W13wKEIFVnf4kHDVaB9m",
        "estado": 1,
        "ipConexion": null
      },
      "verEnPoliza": "S",
      "estadoCivil": null,
      "estudio": null,
      "ocupacion": null,
      "cantTrabajo": null,
      "hijos": null,
      "conviven": null,
      "paga": null,
      "trabajan": null,
      "csmmlv": null,
      "nomPaga": null,
      "celpaga": null,
      "propVivienda": null,
      "nombreContac": null,
      "celContact": null
    }
  },
  "operCom": {
    "id": 48,
    "nit": {
      "id": 602,
      "tipoDoc": "NT",
      "documento": "9003722931",
      "nombre1": null,
      "nombre2": null,
      "apellido1": null,
      "apellido2": null,
      "nombre": "CREAMOS MARKETING",
      "telefono": "3851891",
      "telefono2": null,
      "telefono3": null,
      "telefono4": null,
      "telefono5": null,
      "telefono6": null,
      "celular": "3176654031",
      "email": "info@creamosmarketing.com",
      "sexo": null,
      "direccion": null,
      "pais": {
        "id": 1,
        "nombre": "COLOMBIA"
      },
      "departamento": null,
      "municipio": null,
      "localidad": null,
      "estado": 1,
      "fechaNacimiento": null,
      "fechaExpedicion": null,
      "usuario": {
        "id": 92,
        "nombre": "cmk",
        "email": "info@creamosmarketing.com",
        "contrasena": "$2a$10$i.0Q2AtNuGxgtwpMHW1USeHWKuAxf5Xr5.AF4s0iekxSwxa9lnQM2",
        "estado": 1,
        "ipConexion": null
      },
      "verEnPoliza": "N",
      "estadoCivil": null,
      "estudio": null,
      "ocupacion": null,
      "cantTrabajo": null,
      "hijos": null,
      "conviven": null,
      "paga": null,
      "trabajan": null,
      "csmmlv": null,
      "nomPaga": null,
      "celpaga": null,
      "propVivienda": null,
      "nombreContac": null,
      "celContact": null
    }
  },
  "tmpClientId": null,
  "ultimaActualizacion": 1550527733000,
  "asegurados": [
    {
      "id": 33,
      "condiciones": null,
      "principal": null,
      "asegurado": {
        "id": 7511,
        "tipoDoc": "CC",
        "documento": "29664440",
        "nombre1": "ROQUELIA",
        "nombre2": "",
        "apellido1": "OREJUELA",
        "apellido2": "TEJADA",
        "nombre": "ROQUELIA  OREJUELA TEJADA",
        "telefono": "3152404683",
        "telefono2": null,
        "telefono3": null,
        "telefono4": null,
        "telefono5": null,
        "telefono6": null,
        "celular": "3152404683",
        "email": null,
        "sexo": "F",
        "direccion": "CL 57D KR 34E - 49 MZ 10 CASA 10 PISO 01 UNI_RES BOSQUES DEL EDEN",
        "pais": null,
        "departamento": {
          "id": 58,
          "nombre": "Valle del Cauca",
          "dptoDane": "76000",
          "dptoSmartFlex": "5"
        },
        "municipio": {
          "id": 884,
          "nombre": "PALMIRA"
        },
        "localidad": {
          "id": 10347,
          "nombre": "PALMIRA",
          "homologaciones": null
        },
        "estado": null,
        "fechaNacimiento": "04/05/1989",
        "fechaExpedicion": "29/04/1969",
        "usuario": null,
        "verEnPoliza": "S",
        "estadoCivil": null,
        "estudio": null,
        "ocupacion": null,
        "cantTrabajo": null,
        "hijos": null,
        "conviven": null,
        "paga": null,
        "trabajan": null,
        "csmmlv": null,
        "nomPaga": null,
        "celpaga": null,
        "propVivienda": null,
        "nombreContac": null,
        "celContact": null
      },
      "beneficiarios": []
    }
  ],
  "tarifa": 18810,
  "estado": {
    "id": 11,
    "codigo": "13",
    "descripcion": "ALTA TRANSACCIONAL",
    "nombre": "ALTA TRANSACCIONAL",
    "indiceIconoApk": 3
  },
  "urlImgPoliza": null,
  "planAnterior": null,
  "fotosPoliza": [
    {
      "id": 36,
      "url": "/home/admincmk/archivos/polizas/32_2502.png",
      "idImagenSubida": null
    }
  ],
  "desdeBase": null,
  "campaniaDetalleId": null,
  "solicitudPolizaId": null,
  "motivoAuditoria": null,
  "emailEnviado": null,
  "puntos": 51,
  "auditor": null,
  "infoVentaExterna": null,
  "digitador": null,
  "usuarioVendedorCalle": null,
  "razon": null,
  "index": 0
}
 
```
