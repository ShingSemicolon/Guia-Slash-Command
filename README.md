# Guía Slash Command
Guía explicada sobre la creación de los slash command handler en discord.js

Los slash commands son una forma de interactuar con Discord, son bastantes fáciles de crear y de usar, pero por si las dudas, cree esta pequeña guía de como hacerlos en handler (es decir, un controlador) y en la última versión de discord.js a la creación de esta guía ([13.1.0](https://www.npmjs.com/package/discord.js/v/13.1.0))

Esta guía esta creada para aquellos que ya tienen conocimientos previos sobre el desarrollo de bots en discord.js, si tu no conoces dicho módulo, te recomiendo leer [la guía de discord.js](https://discordjs.guide/#before-you-begin)

# Controlador de slash commands

El controlador de slash commands se creara en el archivo raíz de tu proyecto, siguendo esta estructura.
```bash
index.js
comandos/
  - ping.js
```
```js
const Discord = require("discord.js") // requerimos el modulo discord.js, evidentemente se instala usando npm i discord.js
const client = new Discord.Client({intents: 32767})// defines el cliente con los intents necesarios
let fs = require("fs") //requerimos el modulo fs
let array = []//creamos un array vacío
  for (const file of fs.readdirSync("./comandos/")) {//creamos un bucle 

    const command = require(`../comandos/${file}`);
    client.slash.set(command.name, command);
    const data = {
      name: command.name,
      description: command.description,
      options: command.options ? command.options : [],
      type: command.type ? command.type : 1
    };
    array.push(data)
  }
  console.log(array)
  // client.application.commands.set(array)
  //  client.guilds.resolve("312846399731662850").commands.set(array)

```
