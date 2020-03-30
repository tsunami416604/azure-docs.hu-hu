---
title: 'Rövid útmutató: Keresési index létrehozása Java-ban REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a Java-gyorshasználatában megtudhatja, hogyan hozhat létre indexet, tölthet be adatokat, és futtathat lekérdezéseket az Azure Cognitive Search REST API-k használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: 0b07e934625b09e7f6249dc00865465147f6f0ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77624017"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java-using-rest-apis"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexet Java-ban REST API-k használatával
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portál](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Hozzon létre egy Java konzolalkalmazást, amely létrehoz, betölt és lekérdezi a keresési indexet [az IntelliJ](https://www.jetbrains.com/idea/), [a Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)és az [Azure Cognitive Search REST API](/rest/api/searchservice/)használatával. Ez a cikk lépésenkénti útmutatást nyújt az alkalmazás létrehozásához. Másik lehetőségként [letöltheti és futtathatja a teljes alkalmazást.](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő szoftvereket és szolgáltatásokat használtuk a rövid útmutató létrehozásához és teszteléséhez:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Kulcs és URL beszerezése

A szolgáltatás hívásai minden kéréshez url-végpontot és hozzáférési kulcsot igényelnek. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

   Hozzon létre egy lekérdezési kulcsot is. Ajánlott csak olvasási hozzáféréssel rendelkező lekérdezési kérelmeket kiadni.

![A szolgáltatás névének, a rendszergazdai és lekérdezési kulcsoknak a beszereznie](media/search-get-started-nodejs/service-name-and-keys.png)

A szolgáltatásnak küldött minden kérelemhez api-kulcs szükséges. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Kezdje az IntelliJ IDEA megnyitásával és egy új projekt létrehozásával.

### <a name="create-the-project"></a>A projekt létrehozása

1. Nyissa meg az IntelliJ IDEA programot, és válassza **az Új projekt létrehozása**lehetőséget.
1. Válassza **a Maven**lehetőséget.
1. A **Project SDK** listájában válassza ki a Java 11 SDK-t.

    ![Maven projekt létrehozása](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. A **GroupId** és artifactId mezőbe írja be a **következőt:** `AzureSearchQuickstart`.
1. Fogadja el a fennmaradó alapértelmezéseket a projekt megnyitásához.

### <a name="specify-maven-dependencies"></a>Maven-függőségek megadása

1. Válassza a **Fájlbeállítások** > **lehetőséget.**
1. A **Beállítások** ablakban válassza a Build, Execution, Deployment**Build Tools** > Maven Importing **(Build, Végrehajtás, Telepítési** > build tools**Maven** > **Importing**.
1. Jelölje be a **Maven-projektek automatikus importálása** jelölőnégyzetet, majd az ablak bezárásához kattintson az **OK** gombra. A Maven beépülő modulok és egyéb függőségek automatikusan szinkronizálódnak, amikor a következő lépésben frissíti a pom.xml fájlt.

    ![Maven importálási lehetőségek az IntelliJ beállításaiban](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Nyissa meg a pom.xml fájlt, és cserélje le a tartalmát a következő Maven konfigurációs részletekre. Ezek közé tartoznak az [Exec Maven Plugin-re](https://www.mojohaus.org/exec-maven-plugin/) és a [JSON interfész API-ra](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2) való hivatkozások

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>A projektstruktúra beállítása

1. Válassza a > **Fájlprojekt-struktúra lehetőséget.** **File**
1. Válassza a Modulok lehetőséget, és **bontsa** `src`  >   `main` ki a forrásfát a mappa tartalmának eléréséhez.
1. `src` `java` `app` A mappában adja `service` hozzá és mappákat.  >   `main`  >  Ehhez jelölje ki `java` a mappát, nyomja le az Alt + Insert billentyűkombinációt, majd írja be a mappa nevét.
1. `src` `resources` `app` A mappában adja `service` hozzá és mappákat.  >   `main`  >

    Ha elkészült, a projektfa az alábbi képen látható.

    ![Projektkönyvtár-struktúra](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Kattintson az **OK** gombra az ablak bezárásához.

### <a name="add-azure-cognitive-search-service-information"></a>Az Azure Cognitive Search szolgáltatás adatainak hozzáadása

1. A **Project** ablakban bontsa ki `src`  >   `main`  > `resources`  >  `app` a forrásfát `config.properties` a mappa eléréséhez, és adjon hozzá egy fájlt. Ehhez jelölje ki `app` a mappát, nyomja le az Alt + Insert billentyűkombinációt, válassza a **Fájl**lehetőséget, majd írja be a fájl nevét.

1. Másolja a következő beállításokat az `<YOUR-SEARCH-SERVICE-NAME>` `<YOUR-ADMIN-KEY>`új `<YOUR-QUERY-KEY>` fájlba, és cserélje le a , és a szolgáltatás nevét és kulcsait. Ha a szolgáltatás `https://mydemo.search.windows.net`végpontja, a szolgáltatás neve "mydemo" lesz.

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>A fő módszer hozzáadása

1. A `src`  >   `main`  >  `java` mappában  >  vegyen fel egy osztályt. `App` `app` Ehhez jelölje ki `app` a mappát, nyomja le az Alt + Insert billentyűkombinációt, válassza a **Java-osztály**lehetőséget, majd írja be az osztály nevét.
1. Nyissa `App` meg az osztályt, és cserélje le a tartalmat a következő kódra. Ez a `main` kód tartalmazza a metódust. 

    A nem kommentált kód beolvassa a keresési szolgáltatás paramétereit, és azokat használja a keresési szolgáltatás ügyfél példányának létrehozásához. A keresési szolgáltatás ügyfélkódja a következő szakaszban jelenik meg.

    Az ebben az osztályban található megjegyzéskód megjegyzését a rövid útmutató egy későbbi szakaszában nem kommentáljuk.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>A HTTP-műveletek hozzáadása

1. A `src`  >   `main`  >  `java` mappában  >  vegyen fel egy osztályt.`SearchServiceClient` `service` Ehhez jelölje ki `service` a mappát, nyomja le az Alt + Insert billentyűkombinációt, válassza a **Java-osztály**lehetőséget, majd írja be az osztály nevét.
1. Nyissa `SearchServiceClient` meg az osztályt, és cserélje le a tartalmát a következő kódra. Ez a kód biztosítja az Azure Cognitive Search REST API használatához szükséges HTTP-műveleteket. Az index létrehozásának, a dokumentumok feltöltésének és az index lekérdezésének további módszerei egy későbbi szakaszban jelennek meg.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>A projekt felépítése

1. Ellenőrizze, hogy a projekt rendelkezik-e a következő struktúrával.

    ![Projektkönyvtár-struktúra](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Nyissa meg a **Maven** eszközablakot, és `verify exec:java` 
 ![hajtsa végre ezt a maven célt: Maven cél végrehajtása: ellenőrizze exec:java](media/search-get-started-java/java-quickstart-execute-maven-goal.png)

A feldolgozás befejezésekor keressen egy BUILD SUCCESS üzenetet, amelyet egy nulla (0) kilépési kód követ.

## <a name="1---create-index"></a>1 - Index létrehozása

A hotelindex-definíció egyszerű mezőket és egy összetett mezőt tartalmaz. Egyszerű mező például a "HotelName" vagy a "Description". A "Cím" mező összetett mező, mivel almezői vannak, például "Utcacím" és "Város". Ebben a rövid útmutatóban az indexdefiníció json használatával van megadva.

1. A **Project** ablakban bontsa ki `src`  >   `main`  > `resources`  >  `service` a forrásfát `index.json` a mappa eléréséhez, és adjon hozzá egy fájlt. Ehhez jelölje ki `app` a mappát, nyomja le az Alt + Insert billentyűkombinációt, válassza a **Fájl**lehetőséget, majd írja be a fájl nevét.

1. Nyissa `index.json` meg a fájlt, és szúrja be a következő indexdefiníciót.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Az index neve "hotel-quickstart" lesz. Az indexmezők attribútumai határozzák meg, hogy az indexelt adatok hogyan kereshetők egy alkalmazásban. Az `IsSearchable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amelyet a teljes szöveges keresésnek tartalmaznia kell. Az attribútumokról a [Mezők gyűjteménye és mezőattribútumai (Mezők gyűjtése és mezőattribútumai)](search-what-is-an-index.md#fields-collection)témakörben olvashat bővebben.
    
    Az `Description` index mezője a `analyzer` választható tulajdonságot használja az alapértelmezett Lucene nyelvi elemző felülbírálásához. A `Description_fr` mező a francia Lucene-elemzőt `fr.lucene` használja, mert francia szöveget tárol. Az `Description` az opcionális Microsoft nyelvi analizátoren.lucene. További információ az elemzőkről: [Analyzeers for text processing in Azure Cognitive Search](search-analyzers.md).

1. Adja hozzá a `SearchServiceClient` következő kódot az osztályhoz. Ezek a módszerek az Azure Cognitive Search REST-szolgáltatás URL-címeit hozzák létre és törlik, amelyek meghatározzák, hogy létezik-e index. A metódusok a HTTP-kérelmet is elküldik.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Az osztályban ne `App` fűzze ki a következő kódot. Ez a kód törli a "hotels-quickstart" indexet, ha létezik, és létrehoz egy új indexet az "index.json" fájlindex definíciója alapján. 

    Az indexlétrehozási kérelem után egy másodperces szünet kerül beillesztésre. Ez a szünet biztosítja, hogy az index a dokumentumok feltöltése előtt létrejön.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Nyissa meg a **Maven** eszközablakot, és hajtsa végre ezt a maven célt:`verify exec:java`

    A kód futása közben keressen egy "Index létrehozása" üzenetet, amelyet egy 201-es válaszkód követ. Ez a válaszkód megerősíti, hogy az index et létrehozták. A futtatásnak BUILD SUCCESS üzenettel és nulla (0) kilépési kóddal kell végződnie.
    
## <a name="2---load-documents"></a>2 - Dokumentumok betöltése

1. A **Project** ablakban bontsa ki `src`  >   `main`  > `resources`  >  `service` a forrásfát `hotels.json` a mappa eléréséhez, és adjon hozzá egy fájlt. Ehhez jelölje ki `app` a mappát, nyomja le az Alt + Insert billentyűkombinációt, válassza a **Fájl**lehetőséget, majd írja be a fájl nevét.
1. Helyezze be a következő szállodai dokumentumokat az aktába.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Szúrja be a `SearchServiceClient` következő kódot az osztályba. Ez a kód létrehozza a REST szolgáltatás URL-címét, hogy feltöltse a szállodai dokumentumokat az indexbe, majd a HTTP POST-kérelmet.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Az osztályban ne `App` fűzze ki a következő kódot. Ez a kód feltölti a dokumentumokat a "hotels.json" az index.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    A feltöltési kérelem után a rendszer két másodperces szünetelteti a dokumentum betöltési folyamatát, mielőtt lekérdezné az indexet.

1. Nyissa meg a **Maven** eszközablakot, és hajtsa végre ezt a maven célt:`verify exec:java`

    Mivel az előző lépésben létrehozott egy "hotel-quickstart" indexet, a kód most törli, és újra létrehozza a szállodai dokumentumok betöltése előtt.

    A kód futása közben keressen egy "Dokumentumok feltöltése" üzenetet, amelyet egy 200-as válaszkód követ. Ez a válaszkód megerősíti, hogy a dokumentumok feltöltve voltak az indexbe. A futtatásnak BUILD SUCCESS üzenettel és nulla (0) kilépési kóddal kell végződnie.

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy betöltötte a szállodai dokumentumokat, keresési lekérdezéseket hozhat létre a szállodai adatok eléréséhez.

1. Adja hozzá a `SearchServiceClient` következő kódot az osztályhoz. Ez a kód az Azure Cognitive Search REST szolgáltatás URL-címeit építi fel az indexelt adatok kereséséhez, és kinyomtatja a keresési eredményeket.

    Az `SearchOptions` osztály `createSearchOptions` és a módszer lehetővé teszi, hogy adja meg a rendelkezésre álló Azure Cognitive Search REST API-lekérdezési beállítások egy részét. A REST API lekérdezési beállításairól további információt a [Search Documents (Azure Cognitive Search REST API) című témakörben](/rest/api/searchservice/search-documents)talál.

    A `SearchPlus` metódus létrehozza a keresési lekérdezés URL-címét, létrehozza a keresési kérelmet, majd kinyomtatja az eredményeket a konzolra. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. Az `App` osztályban ne fűzze ki a megjegyzést a következő kódnak. Ez a kód öt különböző lekérdezést állít be, beleértve a visszaadandó keresési szöveget, lekérdezési paramétereket és adatmezőket. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    A [lekérdezésben kétféleképpen lehet egyező kifejezéseket adni:](search-query-overview.md#types-of-queries)a teljes szöveges keresést és a szűrőket. A teljes szöveges keresési lekérdezés egy vagy `IsSearchable` több kifejezést keres az index mezőiben. A szűrő egy logikai kifejezés, `IsFilterable` amely et az index mezői rekednek ki. Használhatja a teljes szöveges keresés és szűrők együtt vagy külön-külön.

1. Nyissa meg a **Maven** eszközablakot, és hajtsa végre ezt a maven célt:`verify exec:java`

    Keresse meg az egyes lekérdezések összegzését és eredményeit. A futtatásnak build SUCCESS üzenettel és nulla (0) kilépési kóddal kell befejeződnie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a Java-gyorsútmutatóban számos feladatot végzett egy index létrehozásához, dokumentumokba való betöltéséhez és lekérdezések futtatásához. Ha nem ismeri az alapfogalmakat, javasoljuk a következő cikket, amely felsorolja az indexelő műveletek REST.

> [!div class="nextstepaction"]
> [Indexelő műveletek](/rest/api/searchservice/indexer-operations)
