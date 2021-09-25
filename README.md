# Guía Slash Command handler
Guía explicada sobre la creación de los slash command handler en discord.js

Los slash commands son una forma de interactuar con Discord, son bastantes fáciles de crear y de usar, pero por si las dudas, cree esta pequeña guía de como hacerlos en handler (es decir, un controlador de slash commands), con un plus agregando el event handler (un controlador de eventos) y en la última versión de discord.js a la creación de esta guía ([13.1.0](https://www.npmjs.com/package/discord.js/v/13.1.0))

Esta guía esta creada para aquellos que ya tienen conocimientos previos sobre el desarrollo de bots en discord.js, si tu no conoces dicho módulo, te recomiendo leer [la guía de discord.js](https://discordjs.guide/#before-you-begin)

# Controlador de slash commands

En un principio, este será el árbol que tendremos para crear el controlador de slash commands.
```bash
index.js
comandos/
  - ping.js
```
El controlador de slash commands se creara en el archivo raíz de tu proyecto.
```js
const Discord = require("discord.js") // requerimos el modulo discord.js, evidentemente se instala usando npm i discord.js
const client = new Discord.Client({intents: 32767})// defines el cliente con los intents necesarios
let fs = require("fs") //requerimos el modulo fs
client.slash = new Discord.Collection()//Creamos una Colección
let array = []//creamos un array vacío
  for (const file of fs.readdirSync("./comandos/")) {//creamos un bucle con la carpeta creada para los comandos

    const command = require(`./comandos/${file}`)//requieres el archivo dentro de la carpeta
    client.slash.set(command.name, command)//establecemos por objeto el nombre del comando y el archivo  
    const data = {//se crea un objeto recolectando todos los datos del comando
      name: command.name,
      description: command.description,
      options: command.options ? command.options : [],
      type: command.type ? command.type : 1
    }
    array.push(data)//los datos se agregan al array
  }
//Y para establecerlo hay 2 maneras, la primer es globalmente
  client.application.commands.set(array)
//y la segunda es en un servidor en concreto
  client.guilds.resolve("id-del-servidor").commands.set(array)

//...

client.login("token")//logeas el bot con su token
```
