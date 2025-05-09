# **¿Qué es Mongoose?**

**Mongoose** es una biblioteca de Node.js que permite:

- Conectar fácilmente a **MongoDB**.
- Crear **esquemas** y **modelos** de datos.
- Validar y estructurar los datos antes de guardarlos.

## **Paso 1: Requisitos previos**

Antes de empezar, debemos asegurarnos de tener:

- Node.js y npm instalados.
- MongoDB en el PC o en la nube (como MongoDB Atlas).
- Express instalado en tu proyecto.

## **Paso 2: Instalar Mongoose**

En el proyecto Express, ejecuta en la terminal:
```
npm init -y
npm install express
npm install -g nodemon
npm install mongoose
```
## **Paso 3: Conexión a MongoDB con Mongoose**

Aquí un ejemplo básico completo (app.js o index.js):
```
const express = require('express');
const mongoose = require('mongoose');
const app = express();

// Middleware para leer JSON
app.use(express.json());

// 🔗 Conectar a MongoDB
mongoose.connect('mongodb://localhost:27017/academia', {})
.then(() => console.log('Conectado a MongoDB :)'))
.catch(err => console.error('Error al conectar a MongoDB :(', err));

// Esquema sin restricciones
const EstudianteSchema = new mongoose.Schema({}, { strict: false });
const Estudiante = mongoose.model('Estudiante', EstudianteSchema, 'estudiantes');

// Ruta para obtener todos los estudiantes
app.get('/estudiantes', async (req, res) => {
  try {
    const estudiantes = await Estudiante.find();
    res.json(estudiantes);
  } catch (err) {
    res.status(500).send('Error al obtener los estudiantes');
  }
});


// Iniciar servidor
app.listen(3000, () => {
  console.log('Servidor corriendo en http://localhost:3000');
});
```
A continuación, se desglosará el anterior código explicando cada una de sus partes:
```
const express = require('express');
```
**¿Qué hace?**

- Importa la librería Express (que ya se debe haber instalado con npm install express).
- Guarda en la constante express la funcionalidad del framework Express. Esto permitiría luego crear un servidor web fácilmente.
```
const mongoose = require('mongoose');
```
**¿Qué hace?**

- Importa la librería Mongoose (instalada con npm install mongoose).
- mongoose es una herramienta para conectar una app a una base de datos MongoDB y definir esquemas de datos. Como tal permite un "puente" entre la aplicación en Node.js y MongoDB.

### **¿Qué es un esquema de datos?**

Un esquema de datos (o data schema) es una estructura que define cómo deben ser los datos que se guardan en una base de datos.


Supongamos el siguiente bloque de código:
```
// Esquema sin restricciones_
const EstudianteSchema = new mongoose.Schema({}, { strict: false });
const Estudiante = mongoose.model('Estudiante', EstudianteSchema, 'estudiantes')
```


#### **Línea 1: const EstudianteSchema = new mongoose.Schema({}, { strict: false });**

**¿Qué está haciendo?**

Estás creando un **esquema vacío** con Mongoose, lo que significa:

1. {} ... No se define ningún campo fijo (no hay nombre, edad, etc.).
2. { strict: false } ... Le dices a Mongoose:  
    _“Acepta cualquier campo, aunque no esté en el esquema”_.

**¿Qué implica strict: false?**

- Permite insertar, leer y modificar cualquier campo arbitrario.
- MongoDB ya es flexible por sí solo, pero con strict: false, Mongoose también se vuelve flexible, y no impone restricciones.

Esto es útil cuando:

- Ya se tiene una colección desorganizada.
- Se Necesita capturar datos variados sin estructura fija (como logs o formularios dinámicos).
- Está haciendo pruebas iniciales sin esquema aún definido.

#### **Línea 2:const Estudiante = mongoose.model('Estudiante', EstudianteSchema, 'estudiantes');**

**¿Qué está haciendo?**

Se está creando un **modelo Mongoose** llamado Estudiante.

Ahora supongamos esto:

#### **Código:**
```
const EstudianteSchema = new mongoose.Schema({
  nombre: String,
  edad: Number,
  curso: String
});
```

**¿Qué hace?**

Se Está creando un esquema con Mongoose para la colección de estudiantes.

**¿Qué define este esquema?**

- **nombre: String** → El campo nombre debe ser una cadena de texto.
- **edad: Number** → El campo edad debe ser un número.
- **curso: String** → El campo curso también debe ser una cadena.

**Este esquema es estricto por defecto**, así que:

- Si intenta guardar un campo no definido (como notas, email, etc.), **Mongoose lo ignora** a menos que lo permitas explícitamente.
- Si el tipo no coincide (por ejemplo, edad como "veinte"), puede lanzar un error o intentar convertirlo.

#### **Línea 2:**

const Estudiante = mongoose.model('Estudiante', EstudianteSchema, 'estudiantes');

**¿Qué hace?**

Estás creando un **modelo de Mongoose** llamado Estudiante.

| **Parte** | **Significado** |
| --- | --- |
| 'Estudiante' | Nombre del modelo (para usar en tu app JS). |
| EstudianteSchema | El esquema que define cómo deben ser los datos. |
| 'estudiantes' (opcional) | El nombre real de la colección en MongoDB. Si no lo pones, Mongoose usaría estudiantes automáticamente. |

**¿Qué puedes hacer con este modelo?**

Una vez definido, puedes usar el modelo Estudiante para interactuar con la colección estudiantes así:

- Estudiante.find() → buscar estudiantes.
- Estudiante.create({...}) → insertar uno nuevo.
- Estudiante.findByIdAndUpdate() → actualizar.
- Estudiante.deleteOne() → eliminar.

Todo eso estará **validado y limitado** al esquema que se definió.

#### **Codigo**
```
const app = express();
```
**¿Qué hace?**

- Crea una instancia de aplicación Express.
- Esta instancia (app) es la que se usa para:
  - Definir rutas (app.get(...), app.post(...)).
  - Conectar middlewares (app.use(...)).
  - Iniciar el servidor (app.listen(...)).
  - Configurar el servidor en general.
```
mongoose.connect('mongodb://localhost:27017/academia', {})

.then(() => console.log('Conectado a MongoDB :)'))

.catch(err => console.error('Error al conectar a MongoDB :(', err));
```

### **1.mongoose.connect(...)**

Este método conecta la aplicación Express (Node.js) a una base de datos MongoDB.
```
'mongodb://localhost:27017/academia'
```
- mongodb://… Es el protocolo que indica que se trata de una conexión a MongoDB.
- localhost … Indica que la base de datos está corriendo de manera local.
- 27017 … Es el puerto por defecto que usa MongoDB.
- /academia … Es el nombre de la base de datos a la que te quieres conectar.  
    Si no existe, MongoDB la creará automáticamente cuando insertes datos.

**{}**

- Aquí se podría incluir **opciones de configuración**, como:
- {
- useNewUrlParser: true,
- useUnifiedTopology: true
- }
- En versiones modernas de Mongoose, puedes dejarlo vacío {} y las opciones por defecto funcionarán bien.

### **2\. .then(() => console.log(...))**

- Si la conexión se realiza correctamente, entra al bloque .then(...).
- En este caso, muestra el mensaje:
- Conectado a MongoDB :)

### 3\. .catch(err => console.error(...))

- Si ocurre **un error de conexión**, por ejemplo:
  - MongoDB no está corriendo.
  - El puerto está mal.
  - La URL está mal escrita.
- Entonces entra al bloque .catch(...) y muestra:
  - Error al conectar a MongoDB :( &lt;detalle del error&gt;

#### **Explicación de Código**
```
const EstudianteSchema = new mongoose.Schema({}, { strict: false });
const Estudiante = mongoose.model('Estudiante', EstudianteSchema, 'estudiantes');
```
#### **Línea 1: const EstudianteSchema = new mongoose.Schema({}, { strict: false });**

**¿Qué está pasando aquí?**

- mongoose.Schema(...) crea un esquema de datos.
- El primer argumento {} es un objeto vacío, lo que significa:  
    “No defino ninguna estructura específica”.
- El segundo argumento { strict: false } indica que:
  - Mongoose no va a restringir los campos permitidos.
  - Se puede insertar documentos con cualquier estructura y campos variados.
  - Es útil cuando:
    - No conoces de antemano la estructura.
    - Tienes documentos con tamaños o formatos muy diferentes.
    - Estás trabajando con una colección ya existente y no quieres imponer reglas.

**¿Qué pasa si no se pone strict: false?**

- Por defecto, Mongoose **ignora cualquier campo que no esté definido** en el esquema.

#### **Línea 2: const Estudiante = mongoose.model('Estudiante', EstudianteSchema, 'estudiantes');**

**¿Qué hace?**

- Crea un modelo a partir del esquema definido.
- Este modelo permite que se interactue con la colección real en MongoDB.

|     |     |
| --- | --- |
|     |     |

#### **Código explicado**
```
// Ruta para obtener todos los estudiantes
app.get('/estudiantes', async (req, res) => {
try {
const estudiantes = await Estudiante.find();
res.json(estudiantes);
} catch (err) {
res.status(500).send('Error al obtener los estudiantes');
}
});
```

#### **1\. app.get('/estudiantes', async (req, res) => { ... })**

- Define una **ruta HTTP GET** en Express.
- Cuando un cliente (como un navegador o Postman) hace una solicitud a <http://localhost:3000/estudiantes>, se ejecuta esta función.
- La función es async porque usaremos await para esperar la respuesta de MongoDB.

#### **2\. const estudiantes = await Estudiante.find();**

- Usamos el **modelo Estudiante** (conectado a la colección estudiantes en MongoDB).
- .find() es un método de Mongoose que recupera **todos los documentos** de la colección.
- Como es una operación asíncrona (puede tardar), usamos await para esperar su resultado.

#### **3\. res.json(estudiantes);**

- Envía los datos obtenidos al cliente **en formato JSON**.
- Por ejemplo, si la colección tiene tres estudiantes, se enviará un arreglo con los tres objetos.

#### **4\. catch (err) { ... }**

- Si ocurre un error (por ejemplo, si la base de datos no responde), se captura con catch.

#### **5\. res.status(500).send('Error al obtener los estudiantes');**

- Envía una respuesta con **código 500 (error interno del servidor)** y un mensaje de error.
- Esto evita que la aplicación se caiga si algo falla.

## Codigo
```
// GET /buscar?nombre=ana
app.get('/buscar', async (req, res) => {
const nombreBuscado = req.query.nombre;
try {
const resultados = await Estudiante.find({
nombre: { $regex: nombreBuscado, $options: 'i' }
});
res.json(resultados);
} catch (err) {
res.status(500).send('Error al buscar estudiantes');
}
});
```
#### **1.app.get('/buscar', async (req, res) => { ... })**

- Define una **ruta GET** en Express.
- Se activa cuando alguien visita /buscar con un parámetro de consulta, por ejemplo:
- /buscar?nombre=ana

#### **2.const nombreBuscado = req.query.nombre;**

- Extrae el valor del parámetro nombre de la **URL**.
- Si la URL es /buscar?nombre=ana, entonces:
- nombreBuscado === 'ana'

#### **3.await Estudiante.find({ nombre: { $regex: nombreBuscado, $options: 'i' } });**

- Usa Mongoose para buscar en la colección estudiantes.
- $regex permite hacer una búsqueda tipo LIKE '%ana%'.
- $options: 'i' hace que la búsqueda ignore mayúsculas/minúsculas.

Ejemplos de coincidencia para 'ana':

- "Ana"
- "diana"
- "Hernández"
- "ANA"

#### **4.res.json(resultados);**

- Si la búsqueda fue exitosa, responde con los documentos que coinciden en formato **JSON**.

#### **5.catch (err) { ... }**

- Captura errores si algo falla (por ejemplo, si MongoDB no responde).

#### **6.res.status(500).send('Error al buscar estudiantes');**

- Responde con un error 500 y un mensaje de error si ocurre un problema.

#### **7\. Prueba en Postman o navegador**

- URL: <http://localhost:3000/buscar?nombre=ana>
- Resultado: Lista de estudiantes cuyo nombre contiene "ana".

## **Código:**
```
// Ruta POST para insertar un documento sin restricciones
app.post('/guardarEstudiante', async (req, res) => {
try {
const estudiante = new Estudiante(req.body); // Acepta cualquier estructura
const guardado = await estudiante.save();
res.status(201).json(guardado);
} catch (err) {
res.status(400).send(' Error al guardar el estudiante :(');
}
});
```

#### **1.app.post('/guardarEstudiante', async (req, res) => { ... })**

- Define una **ruta POST** en Express.
- Se activa cuando un cliente (Postman, frontend, etc.) envía una **petición POST a /guardarEstudiante** con un cuerpo (body) en formato JSON.
- Usa async porque la operación con la base de datos es asincrónica.

#### **2.const estudiante = new Estudiante(req.body);**

- Crea una nueva instancia del modelo Estudiante con los datos enviados en la solicitud.
- req.body contiene el JSON enviado, por ejemplo:
```
{

"nombre": "Daniela",

"edad": 24,

"programa": "Ingeniería de Sistemas"

}
```

- **Como el esquema es flexible (strict: false)**, se puede enviar **cualquier campo**, incluso si nunca fue definido en un esquema tradicional.

#### **3.const guardado = await estudiante.save();**

- Guarda el documento en la base de datos MongoDB.
- await espera que la operación termine antes de seguir.

#### **4.res.status(201).json(guardado);**

- Si todo salió bien, responde con:
  - **Código 201** (creado exitosamente).
  - El documento guardado, incluyendo el \_id generado por MongoDB.

#### **5.catch (err) { ... }**

- Captura errores como:
  - Fallas de conexión.
  - Datos mal formateados.
  - Otros errores de Mongoose.

#### **6.res.status(400).send('Error al guardar el estudiante :(');**

- Si hay error, responde con:
  - **Código 400** (error del cliente).
  - Un mensaje amigable de error.

#### **7.Ejemplo de uso en Postman**

- Método: POST
- URL: <http://localhost:3000/guardarEstudiante>
- Body (JSON):
```
{
"nombre": "Sofía",
"edad": 20,
"email": "<sofia@email.com>",
"materias": \["Bases de Datos", "Algoritmos"\]
}
```
#### **Código completo:**

// PUT /estudiantes/:id
```
app.put('/estudiantes/:id', async (req, res) => {
const id = req.params.id; // Se obtiene el ID desde la URL
const nuevosDatos = req.body; // Datos nuevos desde el cuerpo de la solicitud
try {
const actualizado = await Estudiante.findByIdAndUpdate(
id,
{ $set: nuevosDatos },
{ new: true } // Devuelve el documento ya actualizado
);

if (!actualizado) {

return res.status(404).send('Estudiante no encontrado');

}

res.json(actualizado);

} catch (err) {

res.status(400).send('Error al actualizar el estudiante');

}

});
```
#### **1.app.put('/estudiantes/:id', async (req, res) => { ... })**

- Define una **ruta PUT** en Express.
- :id es un **parámetro dinámico** de la URL, por ejemplo:
- /estudiantes/661fbe19aa021cf4952a725a

#### **2.const id = req.params.id;**

- Extrae el parámetro id desde la URL.
- Este valor corresponde al **\_id del estudiante** en MongoDB.

#### **3.const nuevosDatos = req.body;**

- Obtiene los nuevos datos que se desean actualizar, enviados en el **cuerpo del request** en formato JSON.
- Por ejemplo:
- {
- "edad": 25,
- "correo": "nuevo@email.com"
- }

#### **4.await Estudiante.findByIdAndUpdate(...)**

Este es el corazón de la operación:
```
await Estudiante.findByIdAndUpdate(

id,

{ $set: nuevosDatos },

{ new: true }

);
```

- id: el _id del documento que quieres actualizar.
- $set: nuevosDatos: MongoDB actualiza solo los campos enviados.
- { new: true }: hace que se devuelva el documento ya actualizado (si no se pone, devuelve el antiguo).

#### **5.if (!actualizado) { return res.status(404)... }**

- Si el ID no corresponde a ningún documento, se envía una respuesta con **código 404** (no encontrado).

#### **6.res.json(actualizado);**

- Si todo fue bien, se envía de vuelta el **documento ya actualizado** como respuesta en formato JSON.

#### **7.catch (err) { ... }**

- Captura errores (por ejemplo, si el ID es inválido o hay un problema con MongoDB).
- Se responde con código **400** (solicitud mal hecha) y un mensaje de error.

**Código:**
```
// DELETE /deleteEstudiante/:id
app.delete('/deleteEstudiante/:id', async (req, res) => {
const id = req.params.id;
try {
const eliminado = await Estudiante.findByIdAndDelete(id);
if (!eliminado) {
return res.status(404).send('Estudiante no encontrado');
}
res.send(\`Estudiante con ID ${id} eliminado exitosamente\`);
} catch (err) {
res.status(400).send('Error al eliminar el estudiante');
}
});
```

#### **1.app.delete('/deleteEstudiante/:id', async (req, res) => { ... })**

- Crea una ruta DELETE que escucha peticiones como:
- DELETE <http://localhost:3000/deleteEstudiante/661fbcd123abcde98765fgh>
- :id es un parámetro dinámico que representa el \_id del estudiante a eliminar.
- La función es async porque se usa await para consultar la base de datos.

#### **2.const id = req.params.id;**

- Extrae el valor del ID desde la URL usando req.params.

#### **3.const eliminado = await Estudiante.findByIdAndDelete(id);**

- Usa Mongoose para buscar un documento con ese \_id en la colección estudiantes y eliminarlo.
- Si lo encuentra, lo elimina y devuelve el documento eliminado.
- Si no lo encuentra, devuelve null.

#### **4.if (!eliminado) { return res.status(404)... }**

- Si no se encontró ningún documento con ese ID, responde con:
  - Código 404 → "No encontrado".
  - Un mensaje claro: 'Estudiante no encontrado'.

#### **5.res.send(...)**

- Si la eliminación fue exitosa, se devuelve un mensaje confirmando que el estudiante fue eliminado, incluyendo su ID.

#### **6.catch (err) { ... }**

- Captura cualquier error que ocurra (por ejemplo, si el ID no tiene formato válido).
- Devuelve código 400 → "Bad request" y un mensaje de error.
