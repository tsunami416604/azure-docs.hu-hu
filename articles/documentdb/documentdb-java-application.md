---
title: "Oktatóanyag Java alapú alkalmazásfejlesztéshez DocumentDB használatával | Microsoft Docs"
description: "Ez a Java-webalkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Java-alkalmazás adatait az Azure DocumentDB szolgáltatás segítségével."
keywords: "Alkalmazásfejlesztés, adatbázis-oktatóanyag, java-alkalmazás, java-webalkalmazás oktatóanyag, documentdb, azure, Microsoft Azure"
services: documentdb
documentationcenter: java
author: dennyglee
manager: jhubbard
editor: mimig
ms.assetid: 0867a4a2-4bf5-4898-a1f4-44e3868f8725
ms.service: documentdb
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/16/2016
ms.author: denlee
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d8abb90b19fd3b79324209a2418c414e3b6bb301


---
# <a name="build-a-java-web-application-using-documentdb"></a>Java-webalkalmazás létrehozása a DocumentDB használatával
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Ez a Java-webalkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Java-alkalmazás adatait a [Microsoft Azure DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB) szolgáltatás segítségével. A témakörben érintett témák köre:

* Alapszintű JSP-alkalmazás létrehozása az Eclipse-ben.
* Az Azure DocumentDB szolgáltatás használata a [DocumentDB Java SDK-val](https://github.com/Azure/azure-documentdb-java).

Ez a Java-alkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan hozhat létre egy webalapú feladatkezelő alkalmazást, amellyel feladatokat hozhat létre, kérhet le, valamint „kész” jelöléssel láthatja el azokat, ahogyan azt az alábbi illusztráció is mutatja. A rendszer a teendőlistában szereplő összes feladatot JSON-dokumentumokként tárolja az Azure DocumentDB-ben.

![Saját teendőlista Java-alkalmazása](./media/documentdb-java-application/image1.png)

> [!TIP]
> Ez az alkalmazásfejlesztési oktatóanyag feltételezi, hogy rendelkezik korábbi tapasztalattal a Java használatát illetően. Ha még nem ismeri a Javát vagy az [előfeltételt jelentő eszközöket](#Prerequisites), ajánlott letölteni a teljes [teendők](https://github.com/Azure-Samples/documentdb-java-todo-app) projektet a GitHubról, majd lefordítani azt a [jelen cikk végén található utasítások](#GetProject) segítségével. A projekt lefordítása után a cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében.  
> 
> 

## <a name="a-idprerequisitesaprerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>A jelen Java-webalkalmazásokra vonatkozó oktatóanyag előfeltételei
Az alkalmazásfejlesztési oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További részletekért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/)

    VAGY

    Az [Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) egy helyi telepítése.
* [Java fejlesztői készlet (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Eclipse IDE for Java EE Developers.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
* [Engedélyezett Java-futtatókörnyezettel (pl. Tomcat vagy Jetty) rendelkező Azure-webhely.](../app-service-web/web-sites-java-get-started.md)

Ha először telepíti ezeket az eszközöket, a coreservlets.com webhelyen megtalálhatja a telepítési folyamat útmutatóját (angol nyelven) az alábbi cikk Quick Start (Gyors üzembe helyezés) szakaszában: [Tutorial: Installing TomCat7 and Using it with Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) (Oktatóanyag: A Tomcat7 telepítése és használata az Eclipse-szel).

## <a name="a-idcreatedbastep-1-create-a-documentdb-database-account"></a><a id="CreateDB"></a>1. lépés: DocumentDB-adatbázisfiók létrehozása
Először hozzon létre egy DocumentDB-fiókot. Ha már rendelkezik fiókkal vagy az oktatóanyagban a DocumentDB Emulatort használja, továbbléphet a [2. lépés: Új Java JSP-alkalmazás létrehozása](#CreateJSP) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="a-idcreatejspastep-2-create-the-java-jsp-application"></a><a id="CreateJSP"></a>2. lépés: Új Java JSP-alkalmazás létrehozása
JSP-alkalmazás létrehozása:

1. Először is hozzon létre egy Java-projektet. Indítsa el az Eclipse-t, kattintson a **File** (Fájl), **New** (Új), majd a **Dynamic Web Projekt** (Dinamikus webes projekt) lehetőségre. Ha nem jelenik meg a **Dynamic Web Projekt** (Dinamikus webes projekt) lehetőség az elérhető projektek között, tegye a következőt: kattintson a **File** (Fájl), **New** (Új), **Project** (Projekt) lehetőségre, bontsa ki a **Web** elemet, majd kattintson a **Dynamic Web Projekt** (Dinamikus webes projekt) elemre, és végül a **Tovább** gombra.
   
    ![JSP Java-alkalmazások fejlesztése](./media/documentdb-java-application/image10.png)
2. Adja meg a projekt nevét a **Project name** (Projekt neve) mezőben, majd a **Target Runtime** (Tervezett futásidő) legördülő menüben válasszon ki egy értéket (pl. Apache Tomcat v7.0) (nem kötelező), és kattintson a **Finish** (Befejezés) gombra. A tervezett futásidő megadása lehetővé teszi, hogy helyileg, az Eclipse-ben is futtathassa projektjét.
3. Az Eclipse Project Explorer (Projektböngésző) nézetében bontsa ki a projektet. Kattintson a jobb gombbal a **WebContent** (Webes tartalom), majd a **New** (Új) elemre, és végül a **JSP File** (JSP-fájl) elemre.
4. A **New JSP File** (Új JSP-fájl) párbeszédablakban nevezze el a fájlt az alábbi módon: **index.jsp**. A szülőmappa neve maradjon **WebContent**, ahogy azt az alábbi ábra is mutatja, majd kattintson a **Next** (Tovább) lehetőségre.
   
    ![Új JSP-fájl létrehozása – Java-alkalmazásokra vonatkozó oktatóanyag](./media/documentdb-java-application/image11.png)
5. A **Select JSP Template** (JSP-sablon kiválasztása) párbeszédablakban, a jelen oktatóanyag céljából válassza a **New JSP File (html)** (Új JSP-fájl (html)) lehetőséget, majd kattintson a **Finish** (Befejezés) lehetőségre.
6. Miután megnyílt az index.jsp fájl az Eclipse-ben, adja hozzá a **Hello World!** szöveget a már meglévő <body> elemhez. A frissített <body>-tartalomnak az alábbi kódhoz kell hasonlítania:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Mentse az index.jsp fájlt.
8. Ha megadta a tervezett futásidőt a 2. lépésben, most rákattinthat a **Project** (Projekt), majd a **Run** (Futtatás) elemre a JSP-alkalmazás helyileg történő futtatásához.
   
    ![Hello World – Java-alkalmazásokra vonatkozó oktatóanyag](./media/documentdb-java-application/image12.png)

## <a name="a-idinstallsdkastep-3-install-the-documentdb-java-sdk"></a><a id="InstallSDK"></a>3. lépés: A DocumentDB Java SDK telepítése
A DocumentDB Java SDK, valamint annak függőségei a legegyszerűbben az [Apache Maven](http://maven.apache.org/) használatával kérhetők le.

Ehhez át kell konvertálnia a projektet Maven-projektté az alábbi lépések végrehajtásával:

1. Kattintson a jobb gombbal a projektre a Project Explorer (Projektböngésző) nézetben, kattintson a **Configure** (Konfigurálás), majd a **Convert to Maven Project** (Konvertálás Maven-projekté) lehetőségre.
2. A **Create new POM** (Új POM létrehozása) ablakban fogadja el az alapértelmezett beállításokat, majd kattintson a **Finish** (Befejezés) lehetőségre.
3. A **Project Explorer** (Projektböngésző) nézetben nyissa meg a pom.xml fájlt.
4. A **Dependencies** (Függőségek) lap **Dependencies** (Függőségek) paneljén kattintson az **Add** (Hozzáadás) lehetőségre.
5. A **Select Dependency** (Függőség kiválasztása) ablakban tegye a következőket:
   
   * A **GroupId** (Csoportazonosító) mezőben adja meg a következőt: com.microsoft.azure.
   * Az **Azure Id** (Azure-azonosító) mezőben adja meg a következőt: azure-documentdb.
   * A **Version** (Verzió) mezőben adja meg a következőt: 1.5.1.
     
     ![A DocumentDB Java Application SDK telepítése](./media/documentdb-java-application/image13.png)
     
     Vagy adja hozzá a függőség XML-fájljának GroupId és ArtifactId szakaszát közvetlenül a pom.xml fájlhoz egy szövegszerkesztő segítségével:
     
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-documentdb</artifactId>
            <version>1.9.1</version>
        </dependency>
6. Az **Ok** gombra való kattintás után a Maven feltelepíti a DocumentDB Java SDK-t.
7. Mentse a pom.xml fájlt.

## <a name="a-iduseserviceastep-4-using-the-documentdb-service-in-a-java-application"></a><a id="UseService"></a>4. lépés: A DocumentDB szolgáltatás használata Java-alkalmazásokban
1. Először is határozza meg a TodoItem (Tennivaló) objektumot:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    Ebben a projektben a [Project Lombok](http://projectlombok.org/) nevű projekt használatával hoztuk létre a konstruktort, a beolvasókat, a beállítókat és a felépítőt. Ezt a kódot kézzel is megírhatja, vagy létrehozhatja azt az IDE használatával.
2. A DocumentDB szolgáltatás elindításához példányosítania kell egy új **DocumentClient** ügyfelet. Általában érdemes újrafelhasználni a **DocumentClient** ügyfelet ahelyett, hogy minden későbbi kérés esetén új ügyfelet hozna létre. Az ügyfél újrafelhasználásához burkolja be azt egy **DocumentClientFactory** használatával. Az [1. lépésben](#CreateDB) a vágólapra mentett URI és PRIMARY KEY (ELSŐDLEGES KULCS) értékeket szintén itt kell beilleszteni. Cserélje ki a [YOUR\_ENDPOINT\_HERE] részt az URI-re, a [YOUR\_KEY\_HERE] részt pedig az elsődleges kulcsra.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Ezután hozzon létre egy DAO-t a teendők a DocumentDB szolgáltatásba történő kivonatolásához.
   
    Ha menteni szeretné a teendőket egy gyűjteménybe, az ügyfélnek tudnia kell, melyik adatbázisban és gyűjteményben kívánja azt megőrizni (ahogy erre az önhivatkozások is hivatkoznak). Általában érdemes gyorsítótárazni az adatbázist és a gyűjteményt, amennyiben ez lehetséges, így elkerülhető az adatbázissal való fölösleges üzenetváltás.
   
    Az alábbi kód bemutatja, hogyan kérheti le az adatbázist és a gyűjteményt, ha azok léteznek, és hogyan hozhat létre újat, ha azok még nem léteznek:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The DocumentDB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. A következő lépésben be kell írnia egy kódrészletet a teendők a gyűjteményben való megőrzéséhez. A jelen példában a [Gson](https://code.google.com/p/google-gson/) segítségével szerializáltuk és deszerializáltuk a teendő POJO objektumait JSON-dokumentumokká. A [Jackson](http://jackson.codehaus.org/), illetve saját egyéni szerializálói szintén remek megoldást jelentenek a POJO-k szerializálására.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Csakúgy, mint a DocumentDB-adatbázisok és -gyűjtemények esetén, a dokumentumok szintén önhivatkozásokkal is hivatkoznak magukra. A következő segédfüggvény lehetővé teszi a dokumentumok az önhivatkozástól eltérő attribútum (pl. „id”) alapján történő lekérését:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Az 5. lépésben leírt segédmetódus segítségével lekérheti egy teendő JSON-dokumentumát annak azonosítója alapján, majd deszerializálhatja azt egy POJO-vá.
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. A DocumentClient ügyfél segítségével szintén lekérheti a teendők gyűjteményét vagy listáját a DocumentDB SQL használatával:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. A DocumentClient ügyfél segítségével számos módon frissítheti a dokumentumokat. A teendőlista alkalmazásában azt szeretnénk, ha beállíthatnánk, hogy elvégeztük-e az egyes teendőket. Ez a dokumentum „complete” (befejezve) attribútumának frissítésével érhető el:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Végül azt szeretnénk, ha törölhetnénk az egyes teendőket a listából. Ehhez a korábban megírt segédmetódus használatával kérje le az önhivatkozást, majd adja ki a törlés parancsot az ügyfélnek:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // DocumentDB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a name="a-idwireastep-5-wiring-the-rest-of-the-of-java-application-development-project-together"></a><a id="Wire"></a>5. lépés: A Java-alkalmazásfejlesztési projekt fennmaradó részeinek összefűzése
Most, hogy elért a könnyedebb részek végére, már csak fel kell építenie egy gyors felhasználói felületet, és hozzá kell fűznie azt a DAO objektumhoz.

1. Először is hozzon létre egy vezérlőt a DAO meghívásához:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    Egy összetettebb alkalmazásban a vezérlő a DAO mellett bonyolult üzleti logikát is tartalmazhat.
2. Ezután hozzon létre egy servletet, amely továbbítja a HTTP-kéréseket a vezérlőhöz:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Szükség lesz egy webes felhasználói felületre, amelyet megjeleníthet a felhasználó számára. Írja át a korábban létrehozott index.jsp fájlt az alábbi módon:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure DocumentDB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. Majd végezetül írjon egy ügyféloldali Javascript-kódot, amely megteremti a kapcsolatot a webes felhasználói felület és a servlet között:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Nagyszerű! Most már csak le kell tesztelni az alkalmazást. Futtassa az alkalmazást helyileg, és adjon hozzá néhány teendőt. Ehhez adja meg az elemek nevét és kategóriáját, majd kattintson az **Add Task** (Feladat hozzáadása) elemre.
6. Ha megjelent az elem, a jelölőnégyzet bejelölésével, majd az **Update Tasks** (Feladatok frissítése) gombra való kattintással állíthatja be, hogy elvégezte-e már azt.

## <a name="a-iddeployastep-6-deploy-your-java-application-to-azure-websites"></a><a id="Deploy"></a>6. lépés: A Java-alkalmazás üzembe helyezése az Azure Websites-ban
Az Azure Websites rendkívül megkönnyíti a Java-alkalmazások telepítését. Nincs más dolga, mint exportálni az alkalmazást WAR-fájlként, majd feltölteni azt egy forráskezelő rendszer (pl. GIT) vagy FTP segítségével.

1. Az alkalmazás WAR-fájlként történő exportálásához kattintson a jobb gombbal a projektre a **Project Explorer** (Projektböngésző) nézetben, majd kattintson az **Export** (Exportálás), és végül a **WAR File** (WAR-fájl) lehetőségre.
2. A **WAR Export** (WAR-fájl exportálása) ablakban tegye a következőket:
   
   * A Web project (Webes projekt) mezőben adja meg a következőt: azure-documentdb-java-sample.
   * A Destination (Cél) mezőben válassza ki, hova szeretné menteni a WAR-fájlt.
   * Kattintson a **Finish** (Befejezés) gombra.
3. Most, hogy megvan a WAR-fájl, egyszerűen csak töltse fel azt az Azure Websites **webapps** könyvtárába. Fájlok feltöltésével kapcsolatos további információk: [Adding an application to your Java website on Azure](../app-service-web/web-sites-java-add-app.md) (Alkalmazás hozzáadása az Azure Java-webhelyéhez).
   
    Miután feltöltötte a WAR-fájlt a webapps könyvtárba, a futtatókörnyezet észleli majd annak hozzáadását, és automatikusan betölti azt.
4. A kész termék megtekintéséhez keresse fel a http://SAJÁT\_WEBHELY\_NEVE.azurewebsites.net/azure-documentdb-java-sample/ oldalt, és kezdje el a feladatok hozzáadását.

## <a name="a-idgetprojectaget-the-project-from-github"></a><a id="GetProject"></a>A projekt beszerzése a GitHubról
A jelen oktatóanyag minden példáját megtalálhatja a GitHubról elérhető [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) (teendők) projektben. A teendők projekt Eclipse-be történő importálásához győződjön meg arról, hogy rendelkezik az [Előfeltételek](#Prerequisites) szakaszban ismertetett szoftverekkel és erőforrásokkal, majd tegye a következőket:

1. Telepítse a [Project Lombok](http://projectlombok.org/) nevű projektet. A projekt konstruktorainak, beolvasóinak, beállítóinak létrehozása a Lombok használatával történik. A lombok.jar fájl letöltése után kattintson rá duplán annak telepítéséhez, vagy telepítse azt a parancssorból.
2. Ha az Eclipse meg van nyitva, zárja be, és indítsa el újra a Lombok betöltéséhez.
3. Az Eclipse **File** (Fájl) menüjében kattintson az **Import** (Importálás) elemre.
4. Az **Import** (Importálás) ablakban kattintson a **Git**, majd a **Projects from Git** (Git-projektek), és végül a **Next** (Tovább) lehetőségre.
5. A **Select Repository Source** (Tárház forrásának kiválasztása) képernyőn kattintson a **Clone URI** (URI klónozása) lehetőségre.
6. A **Source Git Repository** (Forrás Git-tárház) képernyő **URI** mezőjében adja meg a következőt: https://github.com/Azure-Samples/documentdb-java-todo-app.git, majd kattintson a **Next** (Tovább) gombra.
7. A **Branch Selection** (Ág kiválasztása) képernyőn válassza a **master** (fő) lehetőséget, majd kattintson a **Next** (Tovább) gombra.
8. A **Local Destination** (Helyi cél) képernyőn kattintson a **Browse** (Tallózás) lehetőségre, válassza ki a mappát, ahova a tárházat másolni szeretné, majd kattintson a **Next** (Tovább) gombra.
9. A **Select a wizard to use for importing projects** (Varázsló kiválasztása a projektek importálásához) képernyőn győződjön meg arról, hogy az **Import existing projects** (Létező projektek importálása) lehetőség van kiválasztva, majd kattintson a **Next** (Tovább) gombra.
10. Az **Import Projects** (Projektek importálása) képernyőn törölje a **DocumentDB** projekt jelölését, majd kattintson a **Finish** (Befejezés) gombra. A DocumentDB-projekt tartalmazza a DocumentDB Java SDK-t, amely ezúttal függőségként lesz hozzáadva.
11. A **Project Explorer** (Projektböngésző) nézetben lépjen a következő helyre: azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java. Itt cserélje ki a HOST és MASTER_KEY értékeket a DocumentDB-fiókjának URI és PRIMARY KEY értékeivel, majd mentse a fájlt. További információ: [1. lépés DocumentDB-adatbázisfiók létrehozása](#CreateDB).
12. A **Project Explorer** (Projektböngésző) nézetben kattintson a jobb gombbal az **azure-documentdb-java-sample** elemre, majd kattintson a **Build Path** (Fordítás elérési útja), és végül a **Configure Build Path** (Fordítás elérési útjának konfigurálása) lehetőségre.
13. A **Java Build Path** (Java-verzió elérési útja) képernyő jobb oldalsó paneljén válassza ki a **Libraries** (Könyvtárak) lapot, majd kattintson az **Add External JARs** (Külső JAR-fájlok hozzáadása) lehetőségre. Navigáljon a lombok.jar fájl helyére, kattintson az **Open** (Megnyitás), majd az **OK** gombra.
14. A 12. lépésben leírtak segítségével nyissa meg ismét a **Properties** (Tulajdonságok) ablakot, majd a bal oldali ablaktáblán kattintson a **Targeted Runtimes** (Tervezett futásidők) elemre.
15. A **Targeted Runtimes** (Tervezett futásidők) képernyőn kattintson a **New** (Új) elemre, válassza ki az **Apache Tomcat v7.0** lehetőséget, majd kattintson az **OK** gombra.
16. A 12. lépésben leírtak segítségével nyissa meg ismét a **Properties** (Tulajdonságok) ablakot, majd a bal oldali ablaktáblán kattintson a **Project Facets** (A projekt aspektusai) elemre.
17. A **Project Facets** (A projekt aspektusai) képernyőn válassza a **Dynamic Web Module** (Dinamikus webmodul), majd a **Java** lehetőséget, és kattintson az **OK** gombra.
18. A **Servers** (Kiszolgálók) lapon, a képernyő alján, kattintson a jobb gombbal a **Tomcat v7.0 Server at localhost** (Tomcat 7.0-s verziójú kiszolgáló itt: localhost), majd az **Add and Remove** (Hozzáadás és eltávolítás) lehetőségre.
19. Az **Add and Remove** (Hozzáadás és eltávolítás) ablakban helyezze át az **azure-documentdb-java-sample** kiszolgálót a **Configured** (Konfigurált) mezőbe, majd kattintson a **Finish** (Befejezés) gombra.
20. A **Servers** (Kiszolgálók) lapon, a képernyő alján, kattintson a jobb gombbal a **Tomcat v7.0 Server at localhost** (Tomcat 7.0-s verziójú kiszolgáló itt: localhost), majd a **Restart** (Újraindítás) lehetőségre.
21. Egy böngészőből keresse fel a http://localhost:8080/azure-documentdb-java-sample/ címet, és kezdje el hozzáadni a feladatait a listához. Ügyeljen arra, hogy ha módosította a portok alapértelmezett értékét, akkor a 8080 értéket módosítsa a választott értékre.
22. Projekt telepítése egy Azure-webhelyre: [6. lépés : Az alkalmazás telepítése az Azure Websitesra](#Deploy).

[1]: media/documentdb-java-application/keys.png



<!--HONumber=Dec16_HO1-->


