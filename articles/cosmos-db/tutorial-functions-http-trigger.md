---
title: "Hozzon létre egy HTTP-eseményindítóval Azure Cosmos DB bemeneti kötést |} Microsoft Docs"
description: "Útmutató az Azure Functions használatával lekérdezés Azure Cosmos DB HTTP-eseményindítókkal."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: mvc
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 86a660309fd3fd80f10f706ff460af2309c12174
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Az Azure Functions HTTP-eseményindítóval hozzon létre egy Azure Cosmos DB bemeneti kötése

Azure Cosmos-adatbázis, amely séma nélküli, és a kiszolgáló nélküli globálisan elosztott, több modellre adatbázis. Az Azure függvény egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi igény kódot. A két Azure szolgáltatás párosítani és rendelkezik az alap, amelyre egy kiszolgáló nélküli architektúra, amely lehetővé teszi, hogy nagyszerű alkalmazások létrehozására összpontosítson, és nem kell foglalkoznia üzembe helyezési és a számítás kiszolgáló karbantartásáért és -adatbázist vissza kell.

Ez az oktatóanyag épít, a létrehozott kód a [Graph API gyors üzembe helyezés, a .NET-hez](create-graph-dotnet.md). Ez az oktatóanyag hozzáadja egy Azure-függvény, amely tartalmaz egy [HTTP-eseményindítóval](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-bindings-http-webhook.md#http-trigger). A HTTP-eseményindítóval használ egy Azure Cosmos DB [kötés bemeneti](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-triggers-bindings.md) adatok lekérése a graph-adatbázist, a gyors üzembe helyezés létrehozott. Ez különösen HTTP eseményindító lekérdezések Azure Cosmos DB az adatokat, de bemeneti kötéseket Azure Cosmos DB segítségével függetlenül a függvény csak az adatok a bemeneti értékek beolvasása.

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * Egy Azure-függvény projekt létrehozása 
> * Hozzon létre egy HTTP-eseményindítóval
> * Az Azure függvény közzététele
> * Az Azure-függvény kapcsolódni az Azure Cosmos DB adatbázishoz

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017 15.3-as verziója](https://www.visualstudio.com/vs/preview/), amely tartalmazza az **Azure-fejlesztési** számítási feladatot is.

    ![Az Azure-fejlesztési számítási feladatot is tartalmazó Visual Studio 2017 telepítése](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- A Visual Studio 2017 15.3 verzió frissítése vagy telepítése után manuálisan kell frissíteni a Visual Studio 2017 eszközök az Azure Functions. Az eszközök frissítheti a **eszközök** menüt **bővítmények és frissítések...**   >  **Frissítések** > **Visual Studio piactér** > **az Azure Functions és webes feladatok eszközök**  >  **Frissítés**.

- Fejezze be a [létre olyan .NET-alkalmazás, a Graph API-jával](tutorial-develop-graph-dotnet.md) oktatóanyag, vagy a kód például a get a [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) GitHub-tárház és a projekt felépítéséhez.
 
## <a name="build-a-function-in-visual-studio"></a>A Visual Studio függvény létrehozása

1. Hozzáadás egy **Azure Functions** a megoldásához kattintson a jobb gombbal a megoldás csomópontján projekt **Megoldáskezelőben**, a válasszon **Hozzáadás**  >   **Új projekt**. Válasszon **Azure Functions** a párbeszédpanelen mezőbe, és adjon neki nevet **PeopleDataFunctions**.

   ![Az Azure-függvény projekt hozzáadása a megoldáshoz](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. Miután az Azure Functions-projekt létrehozása, van néhány NuGet kapcsolódó frissíti, és telepíti a végrehajtásához. 

    a. Győződjön meg arról, hogy a legújabb funkciók SDK, a NuGet-kezelő használatával frissítse a **Microsoft.NET.Sdk.Functions** csomag. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**. Az a **telepített** lapra, válassza ki a Microsoft.NET.Sdk.Functions, majd kattintson **frissítés**.

   ![NuGet-csomagok frissítése](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. Az a **Tallózás** lapra, adja meg **azure.graphs** található a **Microsoft.Azure.Graphs** csomagot, majd kattintson a **telepítése**. Ez a csomag tartalmazza a Graph API .NET ügyféloldali SDK-JÁT.

   ![A Graph API telepítése](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. Az a **Tallózás** lapra, adja meg **mono.csharp** található a **Mono.CSharp** csomagot, majd kattintson a **telepítése**.

   ![Mono.CSharp telepítése](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. A Solution Explorer most telepíti, akkor a csomagokat kell tartalmaznia, ahogy az itt látható. 
   
   A következő igazolnia kell írnia egy kódrészletet, ezért fel kell venni egy új **Azure-függvény** elem a projekthez. 

    a. A **Megoldáskezelő** felületén kattintson jobb egérgombbal a projekt csomópontra, majd válassza a **Hozzáadás** > **Új elem** lehetőséget.   
    b. A a **új elem hozzáadása** párbeszédablakban válassza **Visual C# elemek**, jelölje be **Azure-függvény**, típus **keresési** , a nevet a projektnek, majd Kattintson a **Hozzáadás**.  
 
   ![Hozzon létre egy új függvényt nevű keresési](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Az Azure-függvény válaszol a HTTP-kérelmekre, így a Http-eseményindító sablon itt megfelelő.
   
   Az a **új Azure-függvény** mezőben válassza **Http-eseményindítóval**. Azt szeretnénk, ha az Azure-függvény nyitva kell lennie "kiterjedő," túl, így hivatott a **hozzáférési jogosultsággal** való **névtelen**, amely lehetővé teszi, hogy mindenki. Kattintson az **OK** gombra.

   ![Névtelen set hozzáférési jogok](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. Miután Search.cs hozzáadása az Azure-függvény projekthez, másolja ezeket **használatával** keresztül utasítások a meglévő használati utasításokat:

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. Ezután cserélje le az Azure-függvény osztály kódot az alábbi kódot. A kód a Graph API használatával, vagy minden, a felhasználók számára, vagy az adott személy által azonosított Azure Cosmos DB adatbázis keres a `name` lekérdezési karakterlánc.

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   A kód tulajdonképpen a kapcsolat logikák beállítani, mint az eredeti konzolalkalmazást, amely az adatbázis egy egyszerű lekérdezést beolvasni a megfelelő rekordok a rendezés.

## <a name="debug-the-azure-function-locally"></a>Helyileg Azure függvény hibakeresése

Most, hogy a kód befejeződött, az Azure-függvény helyi hibakeresési eszközök és emulator használatával helyben, ha kipróbálja a kódra.

1. A kód lefut megfelelően, mielőtt konfigurálnia kell azt a helyi végrehajtásához az Azure Cosmos adatbázis-kapcsolati információkat. A local.settings.json fájl segítségével konfigurálhatja a helyi végrehajtásához Azure-függvény sokkal ugyanolyan módon állítsa be az eredeti konzol alkalmazását végrehajtó szeretné használni az App.config fájlt.

    Ehhez az szükséges, az alábbi kódsorokat hozzáadása local.settings.json, és másolja a végpont és a következő ábrán látható módon a GraphGetStarted projekt App.Config fájlból AuthKey.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![A végpont és az engedélyezés kulcsát állítsa a local.settings.json fájlban](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Az új funkciók alkalmazás kezdőprojektként módosítható. A **Megoldáskezelőben**, kattintson a jobb gombbal **PeopleDataFunctions**, és válassza ki **beállítás kezdőprojektként**.

3. A **Megoldáskezelőben**, kattintson a jobb gombbal **függőségek** a a **PeopleDataFunctions** projektre, és kattintson a **hivatkozás hozzáadása**. A listában jelölje ki a System.Configuration, és kattintson **OK**.

3. Most tegyük a az alkalmazás futtatásához. Nyomja le az F5 billentyűt a helyi func.exe üzemeltetett és használatra készen álljon az Azure-függvény kóddal hibakeresési eszköz indítása.

   Kezdeti kimenetét a func.exe végén észlelünk, localhost:7071 tárolt Azure-függvény. Ez akkor hasznos, ha kipróbálja az ügyfél.

   ![Az ügyfél tesztelése](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. Az Azure-függvény teszteléséhez [Visual Studio Code](http://code.visualstudio.com/) Huachao Mao kiterjesztésű [többi ügyfél](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). REST-ügyfél a helyi vagy távoli HTTP kérelem funkció egy egyetlen kattintson a jobb gombbal a kínál. 

    Ehhez az szükséges, hozzon létre egy új fájlt teszt-függvény-locally.http, és adja hozzá a következő kódot:

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Most kattintson a jobb gombbal a kód első sorában, majd válasszon **kérés küldése** a következő ábrán látható módon.

   ![A Visual Studio code többi kérés küldése](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   A helyileg futó Azure-függvény fejlécek, JSON törzs tartalmat, a nyers HTTP-válasz jelenik meg minden.

   ![REST-válasz](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Most már a második kódsort, majd válassza ki és **kérés küldése**. Adja hozzá a `name` lekérdezési karakterlánc-paraméter ismert, hogy az adatbázis, a azt az Azure-függvény adja vissza az eredményeket szűrheti.

   ![Az Azure-függvény eredmények szűréséhez](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

Miután az Azure-függvény van hitelesítve, és úgy tűnik, hogy megfelelően működik, az utolsó lépés, hogy közzéteszi az Azure App Service és a felhőben való futtatásra konfigurálja.

## <a name="publish-the-azure-function"></a>Az Azure függvény közzététele

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, majd válassza ki **közzététel**.

   ![Az új projekt közzététele](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Elkészültünk közzététele a tesztek a nyilvánosan elérhető forgatókönyvek a felhőben. Az a **közzététel** lapon jelölje be **Azure függvény App**, jelölje be **hozzon létre új** egy Azure-függvény létrehozása az Azure-előfizetése, majd kattintson **közzététele** .

   ![Hozzon létre egy új Azure-függvény alkalmazást](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. Az a **közzététel** párbeszédpanelen tegye a következőket:
   
    a. A **alkalmazásnév**, a függvény adjon egy egyedi nevet.

    b. A **előfizetés**, válassza ki az Azure-előfizetés használatára.
   
    c. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, és ugyanazt a nevet használja, az alkalmazás nevére.
   
    d. A **App Service-csomag**, kattintson a **új** létrehozni egy új fogyasztás alapján App Service-csomag, mert azt a kiszolgáló nélküli Azure-függvény használati használatalapú számlázási módszert használni kíván. Az alapértelmezett beállításokat használja a **konfigurálása App Service-csomag** lapon, majd **OK**.
   
    e. A **Tárfiók**, is **új** használata az Azure-függvény, abban az esetben, ha a blobokat, táblák és várólisták indul el, egyéb funkciók végrehajtása legalább egyszer kell egy új Tárfiók létrehozásához. Az alapértelmezett beállításokat használja a **Tárfiók** lapon, majd **OK**.

    f. Kattintson a **létrehozása** gombra a párbeszédpanelen az erőforrások létrehozásához az Azure-előfizetése. A Visual Studio tölti le a közzétételi profilt (egyszerű XML-fájl), amelyet az Azure-függvény kód a következő közzétételekor használ.

   ![A Storage-fiók létrehozása](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    A Visual Studio akkor használhatja, ha módosítja a függvény, és tegye közzé újból kell közzététel lapot jeleníti meg. Nincs teendője, hogy oldalon most.

4. Miután az Azure-függvény közzé van téve, akkor lépjen a [Azure-portálon](https://portal.azure.com/) az Azure-függvény lapját. Itt láthatja az Azure-függvény mutató hivatkozás **Alkalmazásbeállítások**. Az élő Azure-függvény Azure Cosmos-adatbázis adatbázis-kapcsolata konfigurálása az személy adatait a hivatkozás megnyitásához.

   ![Tekintse át az alkalmazásbeállítások](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Csak úgy, ahogy korábban a Konzolalkalmazás App.config fájlban, és az Azure-függvény alkalmazás local.settings.json fájlban, szüksége lesz a végpont és AuthKey a közzétett függvény Azure Cosmos DB adatbázishoz adhat hozzá. Ezzel a módszerrel sosem ellenőrizze a konfigurációs kódot is, amely tartalmazza a kulcsok - konfigurálja őket a portálon, és győződjön meg a verziókövetési rendszerrel való nem tárolás. Az egyes értékek hozzáadásához kattintson a **új beállítás hozzáadása** gombra, adja hozzá **végpont** és az érték az App.config fájlban, majd kattintson a **új beállítás hozzáadása** újra, és adja hozzá **AuthKey**  és az egyéni érték. Miután hozzáadott, és menti a értékek, a beállításokat a következőhöz hasonló kell kinéznie.

   ![Az Endpoint és AuthKey konfigurálása](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. Az Azure-függvény megfelelően van konfigurálva az Azure-előfizetéssel, ha újra segítségével a Visual Studio Code többi ügyfél bővítmény lekérdezni a nyilvánosan elérhető Azure Function URL-cím. E két sornyi kód hozzáadása a test-függvény-locally.http, és futtassa a minden egyes sorban, a függvény vizsgálandó. Cserélje le az URL-címben a függvény nevét a függvény nevét.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    Az Azure Cosmos Adatbázisból lekért adatok válaszol, a függvény.

    ![A többi ügyfél használatával az Azure-függvény lekérdezése](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Az Azure-függvény projekt létrehozása 
> * Létrehozott egy HTTP-eseményindítóval
> * Az Azure függvény közzétett
> * A függvény az Azure Cosmos DB adatbázishoz kapcsolódó

Most már folytathatja a fogalmakat részt Cosmos DB további információt.

> [!div class="nextstepaction"]
> [Globális terjesztés](distribute-data-globally.md) 

Ez a cikk alapján a blog [Brady Gaster Schemaless & kiszolgáló nélküli](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless) blog adatsorozat. Látogasson el az adatsorozat további közlemények a blogbejegyzésben.
