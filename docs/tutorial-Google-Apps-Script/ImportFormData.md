---
sidebar_position: 2
---

# Importar data de Formularios a un Google Sheet

De un formulario con estos datos:

## Direccion hacia los formulario que se usaron para el test

1. [Formulario1](https://docs.google.com/forms/d/e/1FAIpQLSdk5KYwhiNbKzUx1yOtJIaQllwcn3pBAfw8Ae5jYRUNWf0FIQ/viewform?usp=sf_link)

2. [Formulario2](https://docs.google.com/forms/d/e/1FAIpQLSfAG3zq24tjPX8nd_1KrsnKUrbJMxF1A4DWs16Q-hS3fePyRg/viewform?usp=sf_link)

3. [GoogleSheetPrincipal](https://docs.google.com/spreadsheets/d/1M9xQ_a6ofPiQHOEAnDNsedpq-U6q9lRccDJZiVYUsAg/edit#gid=567319210) (Se debe solicitar acceso)

| Formulario1  | Formulario2  | GoogleSheet (Se debe solicitar acceso)  |
|---|---|---|
| <a href="https://docs.google.com/forms/d/e/1FAIpQLSdk5KYwhiNbKzUx1yOtJIaQllwcn3pBAfw8Ae5jYRUNWf0FIQ/viewform" target="_blank"> <img src="/img/googleAppScriptsProyects/Form1.png" width="250" height="200"/></a> |  <a href="https://docs.google.com/forms/d/e/1FAIpQLSfAG3zq24tjPX8nd_1KrsnKUrbJMxF1A4DWs16Q-hS3fePyRg/viewform" target="_blank"> <img src="/img/googleAppScriptsProyects/Form2.png" width="250" height="200"/></a> | <a href="https://docs.google.com/spreadsheets/d/1M9xQ_a6ofPiQHOEAnDNsedpq-U6q9lRccDJZiVYUsAg/edit#gid=567319210" target="_blank"> <img src="/img/googleAppScriptsProyects/Sheet.png" width="250" height="200"/></a>  |


## Codigo de los Scripts en los Formularios

#### Formulario1:

```javascript
function onFormSubmit(e) {
  var hojaMaestra = SpreadsheetApp.openById('1M9xQ_a6ofPiQHOEAnDNsedpq-U6q9lRccDJZiVYUsAg') // ID del google sheet donde se enlazara el primer formulario.
  
  const hoja= hojaMaestra.getSheetByName('FormPrincipal'); //Haciendo referencia a la hoja dentro del 'Google Sheet' anexado.

  const responseForm1 = e.response; //La respuesta que se va a enviar en vivo cuando se envie un formulario por medio de un trigger.
  const respuestas = responseForm1.getItemResponses(); //Obtenemos las repuestas del formulario de forma individual.

  //Respuestas del formulario
  var marcaTemporal = responseForm1.getTimestamp();
  var correoElectronico = responseForm1.getRespondentEmail();
  var nombreCompleto = respuestas[0].getResponse();
  var anioNac = respuestas[1].getResponse();
  var genero = respuestas[2].getResponse();
  //NOTA: Se extraen informacion general del formulario como son la 'marca temporal' y el 'correo electronico'

  //Agregar la data
  hoja.appendRow([marcaTemporal, correoElectronico, nombreCompleto, anioNac, genero]) //Añadir a la hoja de calculo los datos estraidos del formulario
}
```

#### Formulario2:

```javascript
function onFormSubmit2(e) {
  const colCorreo = 1; //Para especificar en que columna esta el correo que es en 1 ya que [MarcaTemporal, Correo Electronico, ...]
  var hojaMaestra = SpreadsheetApp.openById('1M9xQ_a6ofPiQHOEAnDNsedpq-U6q9lRccDJZiVYUsAg'); // Se accede al Google Sheet donde se anexara con su ID.
  const hoja = hojaMaestra.getSheetByName('FormPrincipal'); //Haciendo referencia a la hoja dentro del 'Google Sheet' anexado.

  const responseForm1 = e.response; //La respuesta que se va a enviar en vivo cuando se envie un formulario por medio de un trigger.
  const respuestas = responseForm1.getItemResponses(); //Obtenemos las repuestas del formulario de forma individual.

  // Respuestas del formulario
  var marcaTemporal = responseForm1.getTimestamp();
  var correoElectronico = responseForm1.getRespondentEmail();
  var distritoResidencia = respuestas[0].getResponse();
  var numTelef = respuestas[1].getResponse();
  var codPostal = respuestas[2].getResponse();
  //NOTA: Se extraen informacion general del formulario como son la 'marca temporal' y el 'correo electronico'

  //Si no se encontró el correo, agregar una nueva fila
  var fila = -1; //Indicador
  var data = hoja.getDataRange().getValues(); //Obtenemos el rango de toda la data de la tabla para obtener los valores de cada fila.
  var numfilas = data.length; //Obtenuendo los valores de cada fila en formato array buscamos la cantidad de filas dentro de la tabla
  
  // Hacemos un bucle para verificar que exista una fila con el mismo correo electronico, en caso se encuentra, se actualizara esa fila.
  for (var i = 1; i < numfilas; i++) { 
    if (data[i][colCorreo] == correoElectronico) {
      fila = i + 1;
      break;
    }
  } 

  //Hacemos un condicional en caso se haya mantenido el valor de fila = -1 cuando se haya enviado una respuesta quiere decir que no se encuentra una fila con el mismo correo electronico, en todo caso se agrega una nueva fila con la marca temporal y dicho correo electronico.
  if (fila == -1) {
    hoja.appendRow([marcaTemporal, correoElectronico]);
    fila = hoja.getLastRow(); //Para que se agrege en la ultima fila de la tabla
  }
  /**
   * Establecemos los valores de las respuestas del formulario:
   * getRange(filaInicial,columaInicial,numeroDeFilas,numeroDeColumnas)
   **/
  hoja.getRange(fila, 6, 1, 3).setValues([[distritoResidencia, numTelef, codPostal]]);
}
```
## Configuracion del Trigger

En la pestaña **activadores** en la esquina derecha dira activadores o trigger si lo tienes en inglés y puedes crear un activador para decirle en que momento se va a ejecutar el script.

Tanto para el **Formulario1** como el **Formulario2** utilizan la misma configuracion para el trigger.

<img src="/img/googleAppScriptsProyects/image-1.png" alt="logo drive" style={{ width: '1200px', height: 'auto' }} />