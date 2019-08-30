---
title: Java-Webalkalmazás létrehozása Linux rendszeren – Azure App Service
description: Spring boot Java-webalkalmazások létrehozása, üzembe helyezése és méretezése Azure App Service Linux és Azure Cosmos DB rendszeren.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18, seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 27b30bae80e2959698d71279efbfa2531498d8de
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171208"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Oktatóanyag: Java Spring boot-Webalkalmazás létrehozása Azure App Service Linux és Azure Cosmos DB rendszeren

Ez az oktatóanyag végigvezeti a Java-webalkalmazások Azure-ban való létrehozásának, konfigurálásának, üzembe helyezésének és méretezésének folyamatán. Ha elkészült, egy [Spring boot](https://projects.spring.io/spring-boot/) -alkalmazással fog rendelkezni, amely az [Azure app Service Linux](/azure/app-service/containers)rendszeren futó [Azure Cosmos DBban](/azure/cosmos-db) tárolja az adattárolást.

![Az Azure App Service-ben futó Java-alkalmazás](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Cosmos DB adatbázist.
> * Egy minta alkalmazás összekötése az adatbázissal és a helyi tesztelés
> * A minta alkalmazás üzembe helyezése az Azure-ban
> * Stream diagnosztikai naplók a App Serviceból
> * További példányok hozzáadása a minta alkalmazás felskálázásához

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Saját számítógépre telepített [Azure CLI](https://docs.microsoft.com/cli/azure/overview). 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven 3](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>A minta TODO-alkalmazás klónozása és a tárház előkészítése

Ez az oktatóanyag egy webes felhasználói felülettel rendelkező minta TODO-lista alkalmazást használ, amely a Spring [adatAzure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb)által támogatott Spring Rest APIt hív meg. Az alkalmazás kódja elérhető [a githubon](https://github.com/Microsoft/spring-todo-app). Ha többet szeretne megtudni a Java-alkalmazások Spring és Cosmos DB használatával történő írásához, tekintse meg a [Spring boot Starter és a Azure Cosmos db SQL API oktatóanyag](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) és a [Spring adatai Azure Cosmos db gyors üzembe helyezés](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)című témakört.


Futtassa a következő parancsokat a terminálon a minta-tárház klónozásához és a minta alkalmazás környezetének beállításához.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Az alábbi lépéseket követve létrehozhat egy Azure Cosmos DB adatbázist az előfizetésében. A TODO List alkalmazás csatlakozni fog ehhez az adatbázishoz, és a futtatásakor tárolja az adattárolási állapotot, függetlenül az alkalmazás futtatásának helyétől.

1. Jelentkezzen be az Azure CLI-be, és szükség esetén állítsa be az előfizetést, ha több van csatlakoztatva a bejelentkezési hitelesítő adataihoz.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Hozzon létre egy Azure-erőforráscsoportot, és adja meg az erőforráscsoport nevét.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Hozzon létre Azure Cosmos db `GlobalDocumentDB` a típussal. Cosmos DB nevének csak kisbetűket kell használnia. Jegyezze fel a parancs válaszában szereplő mezőt.`documentEndpoint`

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Azure Cosmos DB kulcsának beszerzése az alkalmazáshoz való kapcsolódáshoz. A következő `primaryMasterKey`lépésben `documentEndpoint` meg kell őriznie a közeli lépéseket.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>A TODO alkalmazás tulajdonságainak konfigurálása

Nyisson meg egy terminált a számítógépén. Másolja a minta parancsfájlt a klónozott tárházba, hogy testreszabja az imént létrehozott Cosmos DB-adatbázishoz.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Szerkessze `.scripts/set-env-variables.sh` kedvenc szerkesztőjét, és adja meg Azure Cosmos db a kapcsolatok adatait. A app Service Linux-konfiguráció esetében használja ugyanazt a régiót, mint`your-resource-group-region`az előző () és`your-azure-group-name`az erőforráscsoport (), amelyet a rendszer a Cosmos db-adatbázis létrehozásakor használ. Olyan WEBAPP_NAME válasszon, amely egyedi, mert nem tudja duplikálni bármely webalkalmazás nevét bármely Azure-telepítésben.

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
   
Ezeket a környezeti változókat a `application.properties` Todo List alkalmazásban lehet használni. A Properties fájlban lévő mezők a Spring-adat alapértelmezett tárház-konfigurációját állítják be:

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

Ezután a minta alkalmazás a `@Document` -ból `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` importált megjegyzéssel állítja be a Cosmos db által tárolt és felügyelt entitás típusát:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

A minta futtatásához használja a Mavent.

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

Az alkalmazás elindítása után az alábbi hivatkozással érheti el helyileg a Spring TODO alkalmazást: [http://localhost:8080/](http://localhost:8080/).

 ![Az Azure App Service-ben futó Java-alkalmazás](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Ha a "Started TodoApplication" üzenet helyett kivételek jelennek meg, ellenőrizze, `bash` hogy az előző lépésben szereplő parancsfájl megfelelően exportálta-e a környezeti változókat, és hogy helyesek-e a létrehozott Azure Cosmos db adatbázishoz tartozó értékek.

## <a name="configure-azure-deployment"></a>Az Azure-telepítés konfigurálása

Nyissa meg a `initial/spring-boot-todo` fájltacímtárban,ésadjahozzáakövetkezőMavenbeépülőmodultAzureappServicekonfigurációhoz.`pom.xml` [](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

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

## <a name="deploy-to-app-service-on-linux"></a>Üzembe helyezés a Linuxon App Service

A `azure-webapp:deploy` Maven céljával telepítse a ToDo alkalmazást a Linuxon Azure app Service.

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

A kimenet tartalmazza a telepített alkalmazás URL-címét (ebben a példában `https://spring-todo-app.azurewebsites.net` ). Másolja az URL-címet a webböngészőjébe, vagy futtassa a következő parancsot a terminál ablakban az alkalmazás betöltéséhez.

```bash
open https://spring-todo-app.azurewebsites.net
```

A címsorban a távoli URL-címmel futó alkalmazást kell látnia:

 ![Az Azure App Service-ben futó Java-alkalmazás](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>A TODO-alkalmazás felskálázása

Az alkalmazás felskálázása egy másik feldolgozó hozzáadásával:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más oktatóanyagokhoz (lásd a [következő lépéseket](#next)), az alábbi parancs a Cloud Shellben való futtatásával törölheti azokat: 
  
```bash
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>További lépések

[Azure Java](/java/azure/)
-fejlesztőknek[tavaszi rendszerindítás](https://spring.io/projects/spring-boot), Cosmos db, [Azure Cosmos db](/azure/cosmos-db/sql-api-introduction) és [app Service Linux](app-service-linux-intro.md)rendszerhez készült [Spring-alapú adatfeldolgozás](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable).

További információ a Java-alkalmazások App Service Linux rendszeren való futtatásáról a Fejlesztői útmutatóban.

> [!div class="nextstepaction"] 
> [Java App Service Linux fejlesztői útmutató](configure-language-java.md)
