---
title: .NET-konzolos alkalmazás létrehozása Azure Cosmos DB SQL API-fiókban tárolt adatkezeléshez
description: Megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API C# -erőforrásokat egy konzolos alkalmazás használatával.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 36d172daed487372401691c7046215fb6c4a63ee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384947"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>.NET-konzolos alkalmazás létrehozása Azure Cosmos DB SQL API-fiókban tárolt adatkezeléshez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Üdvözöljük a Azure Cosmos DB SQL API első lépések oktatóanyagában. Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le. Ez az oktatóanyag a Azure Cosmos DB .NET SDK 3.0-s [verzióját](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) használja, amely a .net- [keretrendszerre](https://dotnet.microsoft.com/download) vagy a [.net Core](https://dotnet.microsoft.com/download)-ra is megcélozható.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Létrehozása és csatlakozás az Azure Cosmos-fiók
> * A projekt konfigurálása a Visual Studióban
> * Adatbázis és tároló létrehozása
> * Elemek hozzáadása a tárolóhoz
> * A tároló lekérdezése
> * Az elemen végzett szifilisz-műveletek
> * Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). A gyors utasításokért ugorjon a [teljes oktatóanyag-megoldás](#GetSolution) beszerzése szakaszra.

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1\. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha a Azure Cosmos DB emulátort használja, kövesse a [Azure Cosmos db emulatorban](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon előre a [Visual Studio-projekt beállításához](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>2. lépés: A Visual Studio-projekt beállítása
1. Nyissa meg a **Visual Studio 2017-et** a számítógépén.
1. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
1. Az **új projekt** párbeszédpanelen válassza  / a **Visual C#**  **Console app (.NET-keretrendszer)** elemet, nevezze el a projektet, majd kattintson **az OK**gombra.
    ![Képernyőfelvétel az új projekt ablakról](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > A .net Core Target esetében az **új projekt** párbeszédpanelen válassza a  /  **Visual C#**  **Console app (.net Core)** elemet, nevezze el a projektet, majd kattintson **az OK** gombra.

1. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.

    ![A projekt jobb gombos kattintással elérhető menüjének képernyőképe](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Az a **NuGet** lapra, majd **Tallózás**, és írja be **Microsoft.Azure.Cosmos** kifejezést a keresőmezőbe.
1. A találatok között keresse meg a **Microsoft.Azure.Cosmos** kattintson **telepítése**.
   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Képernyőkép a NuGet menüjéről Azure Cosmos DB ügyféloldali SDK megkereséséhez](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! A [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a id="Connect"></a>3. lépés: Kapcsolódás Azure Cosmos DB fiókhoz
1. Először cserélje le az C# alkalmazás elején található hivatkozásokat a **program.cs** fájlban a következő hivatkozásokkal:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Ezután adja hozzá ezeket az állandókat és változókat a nyilvános ``Program``osztályhoz.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    Vegye figyelembe, ha ismeri a .NET SDK korábbi verziójával, akkor is használható jelennek meg a feltételek "gyűjtemény" és "dokumentum." Azure Cosmos DB támogatja a több API-modell, mert a .NET SDK 3.0-s + verzióját használja-e az általános feltételek "container" és "cikk". A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. [Ismerje meg, további információk adatbázisok, tárolók és elemek.](databases-containers-items.md)

1. Kérje le a végponti URL-címet és az elsődleges kulcsot a [Azure Portal](https://portal.azure.com).

    Az Azure Portalon lépjen az Azure Cosmos DB-fiókra, majd kattintson a **Kulcsok** elemre.

    Másolja ki az URI-t a portálról, `<your endpoint URL>` és illessze ```Program.cs``` be a fájlba. Másolja az elsődleges kulcsot a portálról, és illessze `<your primary key>`be a következőbe:.

   ![Képernyőkép a Azure Cosmos DB kulcsok lekéréséről Azure Portal](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Ezután létrehozunk egy új példányt ```CosmosClient``` , és beállítunk néhány állványzatot a programunkhoz.

    A **Main** metódus alatt adjon hozzá egy új, **GetStartedDemoAsync**nevű aszinkron feladatot, amely új ```CosmosClient```példányt hoz létre. A **GetStartedDemoAsync** -t olyan belépési pontként fogjuk használni, amely Azure Cosmos db erőforrásokon működő metódusokat hív meg.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

1. Adja hozzá a következő kódot a **GetStartedDemoAsync** aszinkron feladat a **Main** metódusból való futtatásához. A **Fő** metódus észleli a kivételeket, és a konzolba írja azokat.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Az alkalmazás futtatásához nyomja le az **F5 billentyűt** . A konzol ablakának kimenete azt `End of demo, press any key to exit.` erősíti meg, hogy létrejött-e a Azure Cosmos db létesített kapcsolódás. Ezután bezárhatja a konzolablakot.

Gratulálunk! Sikeresen csatlakozott egy Azure Cosmos DB-fiókhoz. 

## <a name="step-4-create-a-database"></a>4\. lépés: Adatbázis létrehozása
Adatbázis hozható létre az ``CosmosClient`` osztály [**createdatabaseifnotexistasync metódusának**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) vagy [**ból**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) funkciójával. Az adatbázis a tárolók között particionált elemek logikai tárolója.

1. Másolja és illessze be a **ból** metódust a **GetStartedDemoAsync** metódus alá. A **ból** egy új adatbázist hoz létre, ``FamilyDatabase`` amely azonosítóval rendelkezik, ha még nem létezik, és a ``databaseId`` mezőben megadott azonosítóval rendelkezik. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Másolja és illessze be az alábbi kódot, ahová létrehozta a CosmosClient az imént hozzáadott **ból** metódus meghívásához.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Ekkor a kódnak így kell kinéznie, és ki kell töltenie a végpontot és az elsődleges kulcsot.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-adatbázist.  

## <a id="CreateColl"></a>5. lépés: Tároló létrehozása
> [!WARNING]
> Az **CreateContainerIfNotExistsAsync** metódus meghívása új tárolót hoz létre, amely a díjszabással kapcsolatos szempontokat is érinti. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

A tárolót a [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) vagy a [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) függvény használatával lehet létrehozni a **CosmosDatabase** osztályban. A tároló elemekből áll (JSON-dokumentumok, ha az SQL API) és a kapcsolódó kiszolgálóoldali alkalmazás-logikát a JavaScriptben, például tárolt eljárásokat, felhasználó által definiált függvényeket és eseményindítókat.

1. Másolja és illessze be a **CreateContainerAsync** metódust a **ból** metódus alá. A **CreateContainerAsync** létrehoz egy új, azonosítóval ``FamilyContainer`` rendelkező tárolót, ha még nem létezik, és a tulajdonság által ``containerId`` ``LastName`` particionált mező alapján megadott azonosítóval rendelkezik.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Másolja és illessze be az alábbi kódot, ahová létrehozta a CosmosClient az imént hozzáadott **CreateContainer** metódus meghívásához.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-tárolót.  

## <a id="CreateDoc"></a>6. lépés: Elemek hozzáadása a tárolóhoz
A **CosmosContainer** osztály [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) funkciójával hozhat létre egy elemeket. Az SQL API használatával az elemek dokumentumokként vannak kivetítve, amelyek felhasználói (tetszőleges) JSON-tartalmak. Most beszúrhat egy elemeket a Azure Cosmos DB tárolóba.

Először hozzon létre egy **család** osztályt, amely a minta Azure Cosmos db belül tárolt objektumokat jelképezi. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Megjegyzés: az elemek azonosító **tulajdonságának** szerializálva kell  lennie a JSON-azonosítóban.

1. Válassza a **CTRL + SHIFT + a** billentyűkombinációt az **új elem hozzáadása** párbeszédpanel megnyitásához. Vegyen fel egy új osztály **Family.cs** a projektbe.

    ![Képernyőkép új Family.cs osztály projekthez való hozzáadásáról](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Másolja és illessze be a **család**, **szülő**, **gyermek**, **kisállat**és **címek** osztályt a **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Váltson vissza a **program.cs** , és adja hozzá a **AddItemsToContainerAsync** metódust a **CreateContainerAsync** metódushoz.
A kód ellenőrzi, hogy az azonos AZONOSÍTÓJÚ elemek még nem léteznek-e a létrehozás előtt. Két elemet fogunk beszúrni, egyet az Andersen családhoz és a Wakefield családhoz.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Adjon hozzá egy hívást ``AddItemsToContainerAsync`` ``GetStartedDemoAsync`` a metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen létrehozott két Azure Cosmos DB elemet.  

## <a id="Query"></a>7. lépés: Azure Cosmos DB erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](sql-api-sql-query.md). Az alábbi mintakód bemutatja, hogyan futtathat lekérdezést az előző lépésben beszúrt elemekhez.

1. Másolja és illessze be a **QueryItemsAsync** metódust a **AddItemsToContainerAsync** metódus alá.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adjon hozzá egy hívást ``QueryItemsAsync`` ``GetStartedDemoAsync`` a metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen lekérdezett egy Azure Cosmos DB tárolón.

## <a id="ReplaceItem"></a>8. lépés: JSON-elemek cseréje
Most frissíteni fogjuk a Azure Cosmos DB egy elemét.

1. Másolja és illessze be a **ReplaceFamilyItemAsync** metódust a **QueryItemsAsync** metódus alá. Vegye figyelembe, hogy a ``IsRegistered`` család és az ``Grade`` egyik gyermek tulajdonságát módosítjuk.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adjon hozzá egy hívást ``ReplaceFamilyItemAsync`` ``GetStartedDemoAsync`` a metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen lecserélt egy Azure Cosmos DB-elemet.

## <a id="DeleteDocument"></a>9. lépés: Elem törlése
Most töröljük a Azure Cosmos DB egy elemét.

1. Másolja és illessze be a **DeleteFamilyItemAsync** metódust a **ReplaceFamilyItemAsync** metódus alá.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adjon hozzá egy hívást ``DeleteFamilyItemAsync`` ``GetStartedDemoAsync`` a metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-elemet.

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
Most törölni fogjuk az adatbázist. A létrehozott adatbázis törlésével törlődik az adatbázis és az összes gyermek erőforrás (tárolók, elemek, valamint az összes tárolt eljárás, felhasználó által definiált függvény és eseményindító). A **CosmosClient** -példányt is eldobjuk.

1. Másolja és illessze be a **DeleteDatabaseAndCleanupAsync** metódust a **DeleteFamilyItemAsync** metódus alá.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adjon hozzá egy hívást ``DeleteDatabaseAndCleanupAsync`` ``GetStartedDemoAsync`` a metódushoz.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Az alkalmazás futtatásához nyomja le az **F5 billentyűt** .

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-adatbázist.

## <a id="Run"></a>11. lépés: Futtassa együtt C# a konzol alkalmazást!
Az alkalmazás hibakeresési módban való létrehozásához és futtatásához válassza az F5 billentyűt a Visual Studióban.

A teljes alkalmazás kimenetét egy konzol ablakban kell megtekinteni. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gratulálunk! Elvégezte az oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a id="GetSolution"></a> Az oktatóanyagban szereplő teljes megoldás beszerzése
Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a mintakódokat, a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) beszerezhetőek. 

A GetStarted-megoldás létrehozásához a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [Azure Cosmos db-fiók][cosmos-db-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) megoldás.

Ha vissza szeretné állítani a Azure Cosmos DB .NET SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a **GetStarted** -megoldásra megoldáskezelő, majd kattintson a **NuGet-csomagok visszaállítása**elemre. Ezután az app. config fájlban frissítse a fájlban lecserélendő endpointuri és a PrimaryKey értékeket a [kapcsolódás Azure Cosmos db fiókhoz](#Connect)című témakörben leírtak szerint.

Ennyi az egész, hogy létrejöjjön, és Ön így van.

## <a name="next-steps"></a>További lépések
* Összetettebb ASP.NET MVC-oktatóanyagot szeretne? Lásd [: ASP.net MVC oktatóanyag: Webalkalmazás-fejlesztés Azure Cosmos DBokkal](sql-api-dotnet-application-preview.md).
* Méret- és teljesítménytesztelést szeretne végezni az Azure Cosmos DB használatával? Tekintse meg az [Azure Cosmos DB használatával történő teljesítmény- és mérettesztelést](performance-testing.md) ismertető cikket.
* Ismerje meg, hogyan [monitorozhatja az Azure Cosmos DB-kérelmeket, -használatot és -tárolást](monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
