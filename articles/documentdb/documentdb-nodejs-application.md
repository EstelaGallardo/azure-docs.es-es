---
title: Aprendizaje de Node.js - Tutorial de Node.js de DocumentDB | Microsoft Docs
description: "Aprenda Node.js. El tutorial explora cómo usar DocumentDB de Microsoft Azure para almacenar datos desde una aplicación web Node.js Express hospedada en sitios web de Azure y cómo obtener acceso a dichos datos."
keywords: "Desarrollo de aplicaciones, tutorial de base de datos, información sobre node.js, tutorial de node.js, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: nodejs
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 12/16/2016
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 284bf43ceae0c42b88d6ea3fe8a2e68e7530e5fe
ms.lasthandoff: 03/08/2017


---
# <a name="_Toc395783175"></a>Compilación de una aplicación web Node.js mediante DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [.NET para MongoDB](documentdb-mongodb-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

En este tutorial de Node.js aprenderá a usar Azure DocumentDB para almacenar datos desde una aplicación Node.js Express hospedada en Azure Websites y obtener acceso a ellos. Compile una aplicación de administración de tareas basadas en web sencilla, una aplicación ToDo, que permite crear, recuperar y completar tareas. Las tareas se almacenarán como documentos JSON en Azure DocumentDB. Este tutorial le guiará a través de la creación e implementación de la aplicación y le explicará lo que sucede en cada fragmento de código.

![Captura de pantalla de la aplicación My Todo List creada en este tutorial de Node.js](./media/documentdb-nodejs-application/image1.png)

¿No tiene tiempo para completar el tutorial y solo desea obtener la solución completa? No es un problema, puede obtener la solución de ejemplo completa en [GitHub][GitHub]. Solo tiene que leer el archivo [Léame](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) para obtener instrucciones sobre cómo ejecutar la aplicación.

## <a name="_Toc395783176"></a>Requisitos previos
> [!TIP]
> En este tutorial de Node.js se presupone que tiene experiencia previa con el uso de Node.js y los sitios web de Azure.
> 
> 

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

   OR

   Una instalación local del [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md).
* [Node.js][Node.js] versión v0.10.29 o superior.
* [Express Generator](http://www.expressjs.com/starter/generator.html) (puede instalarlo mediante `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Paso 1: Creación de una cuenta de base de datos de DocumentDB
Comenzaremos por crear una cuenta de DocumentDB. Si ya tiene una cuenta o si usa el Emulador de DocumentDB en este tutorial, puede ir directamente al [Paso 2: Creación de una nueva aplicación de Node.js](#_Toc395783178).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Paso 2: Creación de una nueva aplicación Node.js
Ahora aprendamos a crear un proyecto Node.js básico de Hello World usando el marco [Express](http://expressjs.com/) .

1. Abra su terminal favorito como, por ejemplo, el símbolo del sistema de Node.js.
2. Navegue hasta el directorio en el que desea almacenar la nueva aplicación.
3. Use Express Generator para generar una nueva aplicación denominada **todo**.
   
        express todo
4. Abra el nuevo directorio **todo** e instale las dependencias.
   
        cd todo
        npm install
5. Ejecute la nueva aplicación.
   
        npm start
6. Para ver la nueva aplicación, vaya a [http://localhost:3000](http://localhost:3000) desde el explorador.
   
    ![Aprendizaje de Node.js - Captura de pantalla de la aplicación Hello World en una ventana del explorador](./media/documentdb-nodejs-application/image12.png)

    A continuación, para detener la aplicación, presione CTRL+C en la ventana de terminal y, a continuación, haga clic en **y** para finalizar el trabajo por lotes.

## <a name="_Toc395783179"></a>Paso 3: Instalación de módulos adicionales
El archivo **package.json** es uno de los archivos creados en la raíz del proyecto. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Node.js. Posteriormente, cuando implemente esta aplicación en un sitio web de Azure, este archivo se usará para determinar qué módulos se deben instalar en Azure para ofrecer respaldo a su aplicación. Todavía hay que instalar dos paquetes más para este tutorial.

1. De nuevo en el terminal, instale el módulo **async** mediante npm.
   
        npm install async --save
2. Instale el módulo **documentdb** mediante npm. En este módulo es donde ocurre toda la magia de DocumentDB.
   
        npm install documentdb --save
3. Una comprobación rápida del archivo **package.json** de la aplicación debe mostrar los módulos adicionales. Este archivo indicará a Azure qué paquetes debe descargar e instalar cuando ejecute la aplicación. El aspecto debe ser similar al siguiente.
   
        {
          "name": "todo",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "start": "node ./bin/www"
          },
          "dependencies": {
            "async": "^2.1.4",
            "body-parser": "~1.15.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.2.0",
            "documentdb": "^1.10.0",
            "express": "~4.14.0",
            "jade": "~1.11.0",
            "morgan": "~1.7.0",
            "serve-favicon": "~2.3.0"
          }
        }
   
    Esto indica a Node (y a Azure más tarde) que la aplicación depende de estos módulos adicionales.

## <a name="_Toc395783180"></a>Paso 4: Uso del servicio de DocumentDB en una aplicación Node
Se encarga de toda la configuración e instalación inicial; ahora volvamos a la razón por la que estamos aquí que es escribir código mediante Azure DocumentDB.

### <a name="create-the-model"></a>Crear el modelo
1. En el directorio del proyecto, cree un directorio denominado **models** en el mismo directorio que el archivo package.json.
2. En el directorio **models**, cree un archivo nuevo con el nombre **taskDao.js**. Este archivo contendrá el modelo para las tareas que crea nuestra aplicación.
3. En el mismo directorio **models**, cree otro nuevo archivo denominado **docdbUtils.js**. Este archivo contendrá código útil y reutilizable que se utilizará en toda nuestra aplicación. 
4. Copie el siguiente código en **docdbUtils.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
   
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
   
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
   
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };               
   
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
   
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
   
        module.exports = DocDBUtils;
   
   > [!TIP]
   > createCollection toma un parámetro de requestOptions opcional que puede utilizarse para especificar el tipo de la oferta para la colección. Si no se proporciona ningún valor requestOptions.offerType, la colección se creará con el tipo de oferta predeterminada.
   > 
   > Para obtener más información sobre los tipos de oferta de DocumentDB , consulte [Niveles de rendimiento de DocumentDB](documentdb-performance-levels.md) 
   > 
   > 
5. Guarde y cierre el archivo **docdbUtils.js** .
6. Al principio del archivo **taskDao.js**, agregue el código siguiente para hacer referencia a los elementos **DocumentDBClient** y **docdbUtils.js** creados anteriormente:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. A continuación, agregará código para definir y exportar el objeto Task. Esta función se encarga de inicializar el objeto Task y de configurar la base de datos y la colección de documentos que se utilizarán.
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. A continuación, agregue el siguiente código para definir métodos adicionales en el objeto Task, que permite las interacciones con los datos almacenados en DocumentDB.
   
        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
   
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
   
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
   
            find: function (querySpec, callback) {
                var self = this;
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results);
                    }
                });
            },
   
            addItem: function (item, callback) {
                var self = this;
   
                item.date = Date.now();
                item.completed = false;
   
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, doc);
                    }
                });
            },
   
            updateItem: function (itemId, callback) {
                var self = this;
   
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        doc.completed = true;
   
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
   
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
   
            getItem: function (itemId, callback) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };
9. Guarde y cierre el archivo **taskDao.js** . 

### <a name="create-the-controller"></a>Crear el controlador
1. En el directorio **routes** del nuevo proyecto, cree un nuevo archivo denominado **tasklist.js**. 
2. Agregue el siguiente código a **tasklist.js**. De este modo, se cargan los módulos DocumentDBClient y async, que utiliza **tasklist.js**. También se define la función **TaskList**, que pasa a una sesión del objeto **Task** que definimos anteriormente:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
   
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
   
        module.exports = TaskList;
3. Continúe agregando al archivo **tasklist.js** los métodos usados para **showTasks, addTask** y **completeTasks**:
   
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
   
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
   
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
   
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
   
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
   
                    res.redirect('/');
                });
            },
   
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
   
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };
4. Guarde y cierre el archivo **tasklist.js** .

### <a name="add-configjs"></a>Agregar config.js
1. En el directorio del proyecto, cree un nuevo archivo con el nombre **config.js**.
2. Agregue lo siguiente a **config.js**. Así se definen las opciones de configuración y los valores necesarios para nuestra aplicación.
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. En el archivo **config.js**, actualice los valores de HOST y AUTH_KEY con los valores encontrados en la hoja de claves de la cuenta de DocumentDB en [Microsoft Azure Portal](https://portal.azure.com).
4. Guarde y cierre el archivo **config.js** .

### <a name="modify-appjs"></a>Modificar app.js
1. En el directorio del proyecto, abra el archivo **app.js** . Este archivo se creó anteriormente, cuando se creó la aplicación web de Express.
2. Agregue el código siguiente al principio del archivo **app.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. Este código determina el archivo de configuración que se debe usar. Además, lee los valores de este archivo en algunas variables que usaremos pronto.
4. Sustituya las dos líneas siguientes en el archivo **app.js** :
   
        app.use('/', index);
        app.use('/users', users); 
   
      por el siguiente fragmento de código:
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. Estas líneas definen una nueva sesión de nuestro objeto **TaskDao**, con una nueva conexión a DocumentDB (usando los valores de lectura de **config.js**). Además, inicializan el objeto de la tarea y, a continuación, enlazan las acciones del formulario con los métodos de nuestro controlador de **TaskList**. 
6. Por último, guarde y cierre el archivo **app.js**. Estamos a punto de terminar.

## <a name="_Toc395783181"></a>Paso 5: Creación de una interfaz de usuario
Ahora dirijamos nuestra atención a la generación de la interfaz de usuario para que un usuario pueda realmente interactuar con nuestra aplicación. La aplicación Express que hemos creado utiliza **Jade** como motor de vistas. Para obtener más información sobre Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. El archivo **layout.jade** del directorio **views** se utiliza como plantilla global para otros archivos **.jade**. En este paso podrá modificarlo para utilizar [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de herramientas que facilita el diseño de un sitio web atractivo. 
2. Abra el archivo **layout.jade** que se encuentra en la carpeta **views** y reemplace el contenido con lo siguiente:
   
        doctype html
        html
           head
             title= title
             link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
             link(rel='stylesheet', href='/stylesheets/style.css')
           body
             nav.navbar.navbar-inverse.navbar-fixed-top
               div.navbar-header
                 a.navbar-brand(href='#') My Tasks
             block content
             script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
             script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')

    Esto indica al motor **Jade** que represente HTML de nuestra aplicación y que cree un **block** llamado **content** donde podemos especificar el diseño de nuestras páginas de contenido.
    Guarde y cierre este archivo **layout.jade** .

3. Ahora abra el archivo **index.jade** , la vista que utilizará nuestra aplicación, y reemplace el contenido del archivo por lo siguiente:
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             label Item Name:
             input(name="name", type="textbox")
             label Item Category:
             input(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   
    Esto amplía el diseño y proporciona contenido para el marcador de posición **content** que hemos visto anteriormente en el archivo **layout.jade**.
   
    En este diseño hemos creado dos formularios HTML. 
    El primer formulario contiene una tabla para nuestros datos y un botón que nos permite actualizar los elementos publicando en el método **/completetask** de nuestro controlador.
    El segundo formulario contiene dos campos de entrada y un botón que nos permite crear un nuevo elemento publicando en el método **/addtask** de nuestro controlador.
   
    Esto debería ser todo lo que necesitamos para que nuestra aplicación funcione.
4. Abra el archivo **style.css** ubicado en el directorio **public\stylesheets** y reemplace el código por lo siguiente:
   
        body {
          padding: 50px;
          font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
        }
        a {
          color: #00B7FF;
        }
        .well label {
          display: block;
        }
        .well input {
          margin-bottom: 5px;
        }
        .btn {
          margin-top: 5px;
          border: outset 1px #C8C8C8;
        }
   
    Guarde y cierre el archivo **style.css** .

## <a name="_Toc395783181"></a>Paso 6: Ejecución de la aplicación de forma local
1. Para probar la aplicación en el equipo local, ejecute `npm start` en terminal para iniciar la aplicación y, a continuación, actualice la página del explorador [http://localhost:3000](http://localhost:3000). La página se debería parecer a la de la imagen siguiente:
   
    ![Captura de pantalla de la aplicación MyTodo List en una ventana del explorador](./media/documentdb-nodejs-application/image18.png)

    > [!TIP]
    > Si recibe un error acerca de la sangría en el archivo layout.jade o el archivo index.jade, asegúrese de que las dos primeras líneas de los dos archivos están justificadas a la izquierda, sin espacios en blanco. Si hay espacios delante de las dos primeras líneas, quítelos, guarde ambos archivos y, a continuación, actualice la ventana del explorador. 

2. Utilice los campos Elemento, Nombre del elemento y Categoría para especificar una nueva tarea y, a continuación, haga clic en **Agregar elemento**. Esto creará un documento en DocumentDB con esas propiedades. 
3. La página debería actualizarse para mostrar el elemento recién creado en la lista ToDo.
   
    ![Captura de pantalla de la aplicación con un nuevo elemento en la lista de tareas pendientes](./media/documentdb-nodejs-application/image19.png)
4. Para completar una tarea, simplemente marque la casilla en la columna Complete y, a continuación, haga clic en **Actualizar tareas**. Con ello, se actualizará el documento que ya haya creado.

5. Para detener la aplicación, presione CTRL+C en la ventana de terminal y, a continuación, haga clic en **Y** para finalizar el trabajo por lotes.

## <a name="_Toc395783182"></a>Paso 7: Implementación del proyecto de desarrollo de aplicación en Sitios web de Azure
1. Si todavía no lo ha hecho, habilite un repositorio para el sitio web de Azure. En el tema [Implementación de Git local en el Servicio de aplicaciones de Azure](../app-service-web/app-service-deploy-local-git.md) puede encontrar instrucciones para hacerlo.
2. Agregue el sitio web de Azure como un git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Efectúe la implementación enviando los cambios al equipo remoto.
   
        git push azure master
4. En pocos segundos, git terminará de publicar su aplicación web y ejecutará un explorador donde podrá ver su útil trabajo ejecutándose en Azure.

    ¡Enhorabuena! Acaba de generar su primera aplicación web Express de Node.js mediante Azure DocumentDB y publicarlos en los sitios web de Azure.

    Si desea descargar o hacer referencia a la aplicación de referencia completa de este tutorial, la puede descargar desde [GitHub][GitHub].

## <a name="_Toc395637775"></a>Pasos siguientes

* ¿Desea realizar pruebas de escala y rendimiento con DocumentDB? Consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md)
* Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Explore la [documentación sobre DocumentDB](https://docs.microsoft.com/en-us/azure/documentdb/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[Github]: https://github.com/Azure-Samples/documentdb-node-todo-app


