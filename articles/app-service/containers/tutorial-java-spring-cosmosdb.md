---
title: Java-webalkalmazás összeállítása linuxon – az Azure App Service-ben
description: Hozhat létre, telepíthet és méretezhet a Spring Boot Java Web apps az Azure App Service Linux-és Azure Cosmos DB-hez.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 792346edf1d2b2326f7f5f5f53304ceca347508d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901117"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>Oktatóanyag: Spring és az Azure Cosmos DB Java webes alkalmazás készítése

Ez az oktatóanyag végigvezeti a folyamat létrehozását, konfigurálását, telepítését és a méretezési Java-webalkalmazások az Azure-ban. Amikor kész, hogy egy [Spring Boot](https://projects.spring.io/spring-boot/) adattárolásra alkalmazás [Azure Cosmos DB](/azure/cosmos-db) futó [Linuxon futó Azure App Service](/azure/app-service/containers).

![Az Azure App Service-ben futó Java-alkalmazás](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Cosmos DB-adatbázisban.
> * Egy mintaalkalmazás kapcsolódni az adatbázishoz, és a helyi teszteléshez
> * Mintaalkalmazás üzembe helyezése az Azure-bA
> * Stream-diagnosztikai naplók az App Service-ben
> * A mintaalkalmazás horizontális további példányok hozzáadása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure CLI](https://docs.microsoft.com/cli/azure/overview), a saját számítógépére telepített. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven 3](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Klónozza a minta Teendőkezelő alkalmazást, és a tárház előkészítése

Ebben az oktatóanyagban egy mintául szolgáló Teendőlista-alkalmazás egy webes felhasználói felület által támogatott Spring REST API-hívások [Spring adatokat az Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). Az alkalmazás kódja érhető el [a Githubon](https://github.com/Microsoft/spring-todo-app). Spring és a Cosmos DB Java-alkalmazások írására kapcsolatos további információkért tekintse meg a [Spring Boot Starter az Azure Cosmos DB SQL API – oktatóanyag](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) és a [Spring adatokat az Azure Cosmos DB gyors üzembe helyezési](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Futtassa az alábbi parancsokat a terminálon a minta tárház klónozásához, és állítsa be a minta app környezetet.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Kövesse az alábbi lépéseket egy Azure Cosmos DB-adatbázis létrehozása az előfizetésében. A Teendőlista-alkalmazás kapcsolódik ehhez az adatbázishoz, és tárolja az adatokat, amikor fut, függetlenül attól, ahol futtatja az alkalmazást az alkalmazás állapotának megőrzése.

1. Bejelentkezés az Azure CLI-vel, és igény szerint állítsa be az előfizetés, ha egynél több, a bejelentkezési hitelesítő adataival csatlakozik.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Hozzon létre egy Azure-erőforráscsoportot, figyelembe véve az erőforráscsoport neve.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Az Azure Cosmos DB létrehozása a `GlobalDocumentDB` típusa. A Cosmos DB neve csak kisbetűket kell használnia. Jegyezze fel a `documentEndpoint` mezőt, a parancs a válaszban.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Az alkalmazás csatlakozni az Azure Cosmos DB kulcs beszerzése. Tartsa a he `primaryMasterKey`, `documentEndpoint` közelben kell őket a következő lépésben.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>A Teendőkezelő alkalmazás tulajdonságainak konfigurálása

Nyisson meg egy terminált a számítógépen. Másolja a parancsfájl-mintafájlt a klónozott adattárra, így testre szabhatja az imént létrehozott Cosmos DB-adatbázis.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Szerkesztés `.scripts/set-env-variables.sh` a kedvenc szerkesztőjében, és az ellátási Azure Cosmos DB kapcsolati adatok. Az App Service Linux konfigurációs használja ugyanabban a régióban mint korábban (`your-resource-group-region`) és az erőforráscsoportot (`your-azure-group-name`) a Cosmos DB-adatbázis létrehozásakor használt. Válassza ki a WEBAPP_NAME, amely egyedi óta, nem ismétlődő bármely webalkalmazás nevének minden Azure környezetben.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Ezután futtassa a parancsfájlt:

```bash
source .scripts/set-env-variables.sh
```
   
Ezeket a környezeti változókat használják `application.properties` a Teendőlista-alkalmazás található. A mezők a Tulajdonságok fájlban Spring adatok egy alapértelmezett adattár-konfiguráció beállítása:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Ezután a mintaalkalmazás a `@Document` importált széljegyzetek `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` tárolt és Cosmos DB által kezelt entitástípusokra beállításához:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

A Maven használata a minta futtatásához.

```bash
mvn package spring-boot:run
```

A kimenet a következőhöz hasonlóan kell kinéznie.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Spring Teendőkezelő alkalmazást helyben ezen hivatkozás használatával, ha az alkalmazás elindult érheti el: [ http://localhost:8080/ ](http://localhost:8080/).

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Ha kivételek helyett a "Lépések TodoApplication" üzenetet látja, ellenőrizze, hogy a `bash` parancsfájlt az előző lépésben exportált a környezeti változók megfelelően és, hogy az értékek helyesek-e az Azure Cosmos DB-adatbázishoz létrehozott.

## <a name="configure-azure-deployment"></a>Az Azure-telepítés konfigurálása

Nyissa meg a `pom.xml` fájlt a `initial/spring-boot-todo` könyvtárat, és adja hozzá a következő [Azure App Service-ben készült maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) konfigurációs.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Helyezze üzembe a linuxon futó App Service-ben

Használja a `azure-webapp:deploy` Maven célja, hogy a Teendőkezelő alkalmazás üzembe helyezése az Azure App Service Linux rendszeren.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

A kimenet tartalmazza a telepített alkalmazások URL-CÍMÉT (ebben a példában `https://spring-todo-app.azurewebsites.net` ). Másolja az URL-címet a webböngésző, vagy futtassa a következő parancsot a terminálablakban az alkalmazás betöltése.

```bash
open https://spring-todo-app.azurewebsites.net
```

Az alkalmazás fut a távoli URL-címet a címsorba kell megjelennie:

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="view-logs-to-troubleshoot-the-app"></a>Naplók megtekintése az alkalmazás hibaelhárítása

Az üzembe helyezett Java-webalkalmazás Linuxon futó App Service-ben naplózásának engedélyezése:

```bash
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
  --web-server-logging filesystem
```

A webalkalmazás stream naplózza a terminálban:

```bash
az webapp log tail --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME}
```

Látni fogja a legutóbbi kimeneti sor és a Teendőkezelő alkalmazást az új kérések, azok fog streamelése a a konzolon. Lépjen ki a konzolon, használja a CONTROL + C billentyűkombinációt.

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers' needs non existent service `dev'
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======
```

## <a name="scale-out-the-todo-app"></a>Horizontális felskálázás a Teendőlista alkalmazás

Az alkalmazás horizontális felskálázása egy másik feldolgozó hozzáadásával:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más oktatóanyagokhoz (lásd a [következő lépéseket](#next)), az alábbi parancs a Cloud Shellben való futtatásával törölheti azokat: 
  
```bash
az group delete --name your-azure-group-name
```

<a name="next"></a>

## <a name="next-steps"></a>További lépések

[Azure Java-fejlesztőknek készült](/java/azure/)
[a Spring Boot](https://spring.io/projects/spring-boot), [adatokat a Cosmos DB a Spring](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [az Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) és [Linux App Service-ben ](/azure/app-service/containers/app-service-linux-intro).

További tudnivalók a Java-alkalmazások futtat a linuxon futó App Service-ben a fejlesztői útmutatóban.

> [!div class="nextstepaction"] 
> [Java, az App Service Linux fejlesztői útmutató](/azure/app-service/containers/app-service-linux-java)
