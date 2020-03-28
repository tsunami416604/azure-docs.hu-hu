---
title: 'Bemutató: Linux Java alkalmazás a MongoDB-vel'
description: Megtudhatja, hogyan juthat be egy adatközpontú Linux Java-alkalmazáshoz az Azure App Service szolgáltatásban, az Azure-ban futó MongoDB-val (Cosmos DB) való csatlakozással.
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e5dcb39430158db1ee9a18524d0214335a2bbbba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045380"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Oktatóanyag: Java spring boot webalkalmazás létrehozása Linuxon és Azure Cosmos DB-n futó Azure App Service szolgáltatással

Ez az oktatóanyag végigvezeti a Java webalkalmazások létrehozásának, konfigurálásának, üzembe helyezésének és méretezésének folyamatán az Azure-ban. Ha elkészült, egy [spring boot](https://projects.spring.io/spring-boot/) alkalmazás tárolhatja az adatokat az [Azure Cosmos DB](/azure/cosmos-db) linuxos Azure App Service [rendszeren.](/azure/app-service/containers)

![Az Azure Cosmos DB-ben tárolt adatok tavaszi rendszerindítási alkalmazása](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Cosmos DB adatbázist.
> * Mintaalkalmazás csatlakoztatása az adatbázishoz és helyi tesztelése
> * A mintaalkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók streamelése az App Service-ből
> * További példányok hozzáadása a mintaalkalmazás horizontális felskálázására

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Azure CLI](https://docs.microsoft.com/cli/azure/overview), telepítve a saját számítógépén. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Klónozza a minta TODO alkalmazást, és készítse elő a tárhét

Ez az oktatóanyag egy minta TODO-listaalkalmazást használ egy webes felhasználói felülettel, amely a [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb)által támogatott tavaszi REST API-t hívja meg. Az alkalmazás kódja elérhető [a GitHubon.](https://github.com/Microsoft/spring-todo-app) Ha többet szeretne megtudni a Java-alkalmazások tavaszi és Cosmos DB használatával történő írásáról, tekintse meg a [Spring Boot Starter az Azure Cosmos DB SQL API oktatóanyagát](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) és a Spring Data Azure [Cosmos DB gyorsindítást.](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)


Futtassa a következő parancsokat a terminálon a mintatár klónozásához és a mintaalkalmazás-környezet beállításához.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Az alábbi lépésekkel hozzon létre egy Azure Cosmos DB-adatbázist az előfizetésében. A TODO-lista alkalmazás csatlakozik ehhez az adatbázishoz, és tárolja az adatokat futás közben, az alkalmazás állapotának megőrzése érdekében, függetlenül attól, hogy hol futtatja az alkalmazást.

1. Jelentkezzen be az Azure CLI-be, és szükség esetén állítsa be az előfizetést, ha egynél több van csatlakoztatva a bejelentkezési hitelesítő adataihoz.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Hozzon létre egy Azure-erőforráscsoportot, amely az erőforráscsoport nevét is feljegyzi.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Hozzon létre azure `GlobalDocumentDB` Cosmos DB a fajta. A Cosmos DB neve csak kisbetűket használhat. Megjegyzés a `documentEndpoint` mezőben a parancs válaszában.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Az Azure Cosmos DB-kulcs beszerezése az alkalmazáshoz való csatlakozáshoz. Tartsa `primaryMasterKey`a `documentEndpoint` közelében, ahogy szüksége lesz rájuk a következő lépésben.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>A TODO alkalmazás tulajdonságainak konfigurálása

Nyisson meg egy terminált a számítógépen. Másolja a mintaparancsfájlt a klónozott tárházban, így testreszabhatja az imént létrehozott Cosmos DB-adatbázisához.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Szerkesztheti `.scripts/set-env-variables.sh` kedvenc szerkesztőjét, és az Azure Cosmos DB-kapcsolatadatait adja meg. Az App Service Linux-konfiguráció, használja ugyanazt a régiót, mint korábban (`your-resource-group-region`) és erőforráscsoport ( )`your-azure-group-name`a Cosmos DB adatbázis létrehozásakor használt. Válasszon egy WEBAPP_NAME, amely egyedi, mivel nem tudja duplikálni a webalkalmazás nevét az Azure-telepítésben.

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
   
Ezek a környezeti változók a TODO-listaalkalmazásban használatosak. `application.properties` A tulajdonságfájl mezői a tavaszi adatok alapértelmezett tárház-konfigurációját állították be:

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

Ezután a mintaalkalmazás az `@Document` `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` importált jegyzetet használja a Cosmos DB által tárolandó és kezelendő entitástípus beállításához:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

A minta futtatásához használja a Maven-t.

```bash
mvn package spring-boot:run
```

A kimenetnek a következőkre kell hasonlítania.

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

Az alkalmazás indítása után a Spring TODO App [http://localhost:8080/](http://localhost:8080/)helyileg érhető el: .

 ![Access Spring TODO alkalmazás helyileg](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Ha az "Elkezdett TodoApplication" üzenet helyett kivételeket `bash` lát, ellenőrizze, hogy az előző lépésben lévő parancsfájl megfelelően exportálta-e a környezeti változókat, és hogy az értékek megfelelőek-e a létrehozott Azure Cosmos DB adatbázishoz.

## <a name="configure-azure-deployment"></a>Az Azure-telepítés konfigurálása

Nyissa `pom.xml` meg a `initial/spring-boot-todo` fájlt a címtárban, és adja hozzá a következő [Azure Web App Plugin a Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) konfiguráció.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

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

## <a name="deploy-to-app-service-on-linux"></a>Üzembe helyezés az App Service-ben Linux on

A `azure-webapp:deploy` Maven-cél használatával üzembe helyezheti a TODO-alkalmazást az Azure App Service Linuxon.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.9.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

A kimenet tartalmazza az üzembe helyezett alkalmazás URL-címét (ebben a `https://spring-todo-app.azurewebsites.net` példában). Ezt az URL-címet átmásolhatja a webböngészőbe, vagy futtathatja a következő parancsot a Terminál ablakban az alkalmazás betöltéséhez.

```bash
open https://spring-todo-app.azurewebsites.net
```

A címsorban a távoli URL-címmel futó alkalmazásnak is meg kell jelennie:

 ![Távoli URL-címmel futó tavaszi rendszerindítási alkalmazás](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>A TODO alkalmazás kiszélesedése

Az alkalmazás horizontális felskálázása egy másik dolgozó hozzáadásával:

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

[Az Azure for Java Developers](/java/azure/)
[tavaszi rendszerindítása](https://spring.io/projects/spring-boot), [spring data for Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), Azure [Cosmos DB](/azure/cosmos-db/sql-api-introduction) és App Service [Linux](app-service-linux-intro.md).

További információ a Java-alkalmazások Linuxon futó alkalmazásszolgáltatásról a fejlesztői útmutatóban.

> [!div class="nextstepaction"] 
> [Java a Linuxos App Service-ben – fejlesztői útmutató](configure-language-java.md)
