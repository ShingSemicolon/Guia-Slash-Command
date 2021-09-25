# Guía Slash Command handler
Guía explicada sobre la creación de los slash command handler en discord.js

Los slash commands son una forma de interactuar con Discord, son bastantes fáciles de crear y de usar, pero por si las dudas, cree esta pequeña guía de como hacerlos en handler (es decir, un controlador de slash commands), con un plus agregando el event handler (un controlador de eventos) y en la última versión de discord.js a la creación de esta guía ([13.1.0](https://www.npmjs.com/package/discord.js/v/13.1.0))


### Antes de nada... sobre copiar y pegar...
No me importa que copies y pegues todo lo escrito en este repositorio, siempre cuando sepas lo que estás haciendo, por favor, si no tiene ninguna idea respecto a esto, no lo copie y pegue, lo más probable es que se te genere un error debido a ello.

Esta guía está creada para aquellos que ya tienen conocimientos previos sobre el desarrollo de bots en discord.js, si tu no conoces dicho módulo, te recomiendo leer [la guía de discord.js](https://discordjs.guide/#before-you-begin)

Si usted cree que esta guía contiene errores, hagamelo saber en Discord, Shing_XD_0602#5907


## Árbol de los archivos
Este será el árbol que tendremos para crear los controladores.
```bash
index.js
comandos/
  - ping.js
eventos/
  - interactionCreate.js
```
## Controlador de slash commands

El controlador de slash commands se creará en el archivo raíz de tu proyecto, recuerda crear la carpeta de "comandos" y un archivo .js dentro de esa carpeta.
```js
const Discord = require("discord.js") // requerimos el módulo discord.js, evidentemente se instala usando npm i discord.js
const client = new Discord.Client({intents: 32767})// defines el cliente con los intents necesarios
let fs = require("fs") //requerimos el módulo fs
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
## Controlador de eventos
Ahora crearemos el controlador de eventos, tambíen lo haremos en el archivo raíz, recuerda que debes de crear la carpeta de "eventos" y crear el archivo interactionCreate.js
```js
//...

  for (const file of fs.readdirSync("./eventos/")) {//creamos un bucle con la carpeta creada para los eventos
        const event = require(`../eventos/${file}`)//requieres el archivo dentro de la carpeta
        let eventName = file.split(".")[0]//obtenemos el nombre del evento sin el .js
        client.on(eventName, event.bind(null, client)); //y ejecutamos el evento
    }

}
client.login("token")//logeas el bot con su token
```

## Creación del evento: interactionCreate
interactionCreate es el evento que se encarga de detectar todas las interacciones, incluidos, los slash commands, así pues, nos es útil para poder ejecutar los slash commands, recuerda que este código se hará en el archivo eventos/interactionCreate.js

```js
module.exports = async (client, interaction) =>{
  try {//creamos un try... catch por si ocurre algún error inesperado en este evento.
  if(interaction.isCommand()){//de todas las interacciones, solo los slash commands podrán realizar el código dentro de la condición
    const command = interaction.commandName.toLowerCase()//obtenemos el nombre del slash command ejecutado y lo ponemos en minúsculas
  const cmd = client.slash.get(command)//obtenemos el comando de la colección
 
  cmd.execute(Discord, client, interaction)//y lo ejecutamos
  }
  }catch(err){
   
console.log(err)//la consola devuelve el error
  }
  
}
```

## Creación del comando: ping
Este sencillo comando se usa bastante para asegurar que todo funciona bien devolviendo un mensaje, con este ejemplo, te harás una idea de como poner el nombre, las descripciones e incluso las opciones a los slash commands, recuerda que este código se hará en el archivo comandos/ping.js

```js

module.exports = {
  name: "ping2",
  description: "Pong!",
  options: [
    {
      name: "texto",
      type: "STRING",
      description: "Coloca un texto",
      required: false,
    },
  ],
  async execute(Discord, client, interaction) {
    //como ves en el objeto, exportamos el nombre del comando, y la descripción, que se usa en el controlador de slash commands para poder publicarlos, también se agregan las opciones, un equivalente a los argumentos que usan muchos bots, y puedes elegir el tipo, poner la descripcion... si es necesario... te recomiendo leer las docs de discord.js si quieres saber mas acerca de eso: https://discord.js.org/#/docs/main/stable/class/ApplicationCommand

    interaction.reply("Pong")//así se enviará un mensaje con la interacción pero ahora vamos a usar la opción del texto para que lo tengas más claro de cómo funciona

    if (interaction.options.getString("texto")) {//así se obtiene el texto o lo que fuese dependiendo del tipo, con las interacciones
      interaction.reply(`Pong!\nTambién escribiste: \`${interaction.options.getString("texto")}\``) // enviamos el mensaje con el texto
    } else {//si no introdujo ningún texto (recordemos que la opción no es obligatoria por el required: false)
      intraction.reply("Pong!")
    }
  }
}
```



