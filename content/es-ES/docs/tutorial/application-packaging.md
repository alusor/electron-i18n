# Empaquetado de la aplicación

Para mitigar los [problemas](https://github.com/joyent/node/issues/6960) relacionados con los nombres de ruta largas en Windows, acelere ligeramente y `exija` que su código fuente se inspeccione rápidamente, puede optar por empaquetar su aplicación en un archivo [asar](https://github.com/electron/asar) con pocos cambios en su código fuente.

## Generando Archivo `asar`

Un archivo [asar](https://github.com/electron/asar) es un formato simple similar a un alquitrán que concatena archivos en un solo archivo. Electron puede leer archivos arbitrarios sin desempaquetar todo el archivo. 

Pasos para empaquetar su aplicación en un archivo `asar< 0>:</p>

<h3>1. Instalar la utilidad de Asar</h3>

<pre><code class="sh">$ npm install -g asar
`</pre> 

### 2. Empaquetar con `Paquete de asar`

```sh
$ asar pack your-app app.asar
```

## Usando archivos `asar`

En Electron hay dos conjuntos de API: API de nodo proporcionadas por Node.js y API Web proporcionadas por Chromium. Ambas API admiten la lectura de archivos de `asar`.

### Nodo de API

Con parches especiales en Electron, las API de nodo como `fs.readFile` y ` requieren` tratar archivos `asar` como directorios virtuales, y los archivos en él como normales en el sistema de archivos.

Por ejemplo, supongamos que tenemos un archivo `ejemplo.asar` en `/ruta/a</ 0>:</p>

<pre><code class="sh">$ asar list /path/to/example.asar
/app.js
/file.txt
/dir/module.js
/static/index.html
/static/main.css
/static/jquery.min.js
`</pre> 

Lea una ficha en el archivo `asar`:

```javascript
const fs = require('fs')
fs.readFileSync('/path/to/example.asar/file.txt')
```

Lista de todos las fichas debajo de la raíz del archivo:

```javascript
const fs = require('fs')
fs.readdirSync('/path/to/example.asar')
```

Use un módulo del archivo:

```javascript
require('/path/to/example.asar/dir/module.js')
```

You can also display a web page in an `asar` archive with `BrowserWindow`:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('file:///path/to/example.asar/static/index.html')
```

### Web API

En una página web, las fichas en un archivo pueden solicitarse con el protocolo `file:`. Al igual que la API del nodo, los archivos `asar` se tratan como directorios.

Por ejemplo, para obtener un fichero con `$,obtenga`:

```html
<script>
let $ = require('./jquery.min.js')
$.get('file:///path/to/example.asar/file.txt', (data) => {
  console.log(data)
})
</script>
```

### Tratamiento de un archivo `asar` como una ficha normal

Para algunos casos, como verificar la suma de comprobación del archivo `asar`, necesitamos leer el contenido de un archivo `asar` como una ficha. Para este propósito usted puede usar el módulo incorporado `original-fs` el cual provee APIs originales `fs` sin el soporte de `asar`:

```javascript
const originalFs = require('original-fs')
originalFs.readFileSync('/path/to/example.asar')
```

También puede configurar `process.noAsar` a `Verdad` para inhabilitar el soporte de `asar` en el módulo `fs`:

```javascript
const fs = require('fs')
process.noAsar = true
fs.readFileSync('/path/to/example.asar')
```

## Limitaciones de la API de nodo

Aunque tratamos de hacer que los archivos `asar` en la API de nodo funcionaran como directorios tanto como sea posible, todavía hay limitaciones debido a la naturaleza de bajo nivel de la API de nodo.

### Archivos de solo lectura

Los archivos no pueden ser modificados así que las APIs de nodos que puedan modificar archivos no podrán trabajar con archivos de `asar`.

### Directorio de Trabajo No Puede Ser Configurado en Directorios en Archivos

Aunque los archivos `asar` son tratados como directorios, no hay tal en el sistema de archivos, así que nunca puede configurar un directorio de trabajo como directorio en los archivos `asar`. Al igual que con las opciones `cwd` de algunas APIs también causarán errores.

### Desempaque adicional en algunas APIs

La mayoría de las APIs `fs` pueden leer un archivo u obtener información de un archivo desde un archivo `asar` sin desempaquetar, pero para algunas APIs que dependen de pasar el archivo real a requerimientos subyacentes del sistema. Electron sacará el archivo requerido a un archivo temporal y pasar la ruta de este a la APIs que lo hará trabajar. Esto añadirá unos pequeños gastos adicionales para aquellas APIs.

Las APIs que requieren de un desempaquetado extra son:

* `child_process.execFile`
* `child_process.execFileSync`
* `fs.open`
* `fs.openSync`
* `process.dlopen` - Used by `require` on native modules

### Información estadística falsa de `fs.stat`

Los objetos `Estadística` devuelto por `fs.stat` y sus archivos amigos en archivos `asar` son generados al azar, debido a que esos archivos no existen en el archivo de sistema. Así que no debe confiar en el objeto `estadística` a excepción de que vaya a obtener el tamaño del archivo o verificar el tipo de este.

### Ejecución binaria dentro del archivo `asar`

Hay APIs de nodos que pueden ejecutar binarios como `child_process.exec`, `child_process.spawn` y `child_process.execFile`, pero solo `execFile` es soportado para ejecutar archivos binario `asar` de adentro.

Esto es debido a que `exec` y `spawn` acepta `command` en vez de `file` como entrada, y `command` son ejecutados por debajo de la cáscara. No hay manera fiable de determinar si un comando usa un archivo en un archivo asar, y aún si lo hiciésemos, no podemos estar seguros si no podemos reemplazar el camino en comando sin efectos secundarios.

## Añadiendo archivos desempaquetados en un archivo `asar`

Como e indicó anteriormente, algunas APIs de node desempaquetarán un archivo en el sistema cuando sea llamado, aparte de los problemas de desempeño, pudiese fácilmente llevar a una falsa alerta en los antivirus.

Para trabajar alrededor de esto, usted puede desempaquetar algunos archivos creando otros usando la opción `--unpack`, un ejemplo de excluir bibliotecas compartidas de módulos nativos es:

```sh
$ asar pack app app.asar --unpack *.node
```

Después de correr el comando, aparte de `app.asar`, hay también una carpeta `app.asar.unpacked` generada que contiene los archivos desempaquetados, debe copiarlos juntos con `app.asar` cuando se entregue a lo usuarios.