---
title: Adatkezelés az Azure Cosmos DB SQL API-fiók egy .NET-Konzolalkalmazás létrehozása
description: Ismerje meg, hogyan hozhat létre Azure Cosmos DB SQL API-erőforrások egy C# Konzolalkalmazás.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: kirankk
ms.openlocfilehash: 6fd7efe38aeb1f1094d240cf1675d432f3766229
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985719"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Adatkezelés az Azure Cosmos DB SQL API-fiók egy .NET-Konzolalkalmazás létrehozása

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Üdvözli az Azure Cosmos DB SQL API get az első lépések oktatóanyag. Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le. Ebben az oktatóanyagban [3.0-s vagy újabb](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) , az Azure Cosmos DB .NET SDK, amely lehet megcélozni [.NET-keretrendszer](https://dotnet.microsoft.com/download) vagy [.NET Core](https://dotnet.microsoft.com/download).

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Létrehozása és csatlakozás az Azure Cosmos-fiók
> * A projekt konfigurálása a Visual Studióban
> * Egy adatbázis és a egy tároló létrehozása
> * Elemek hozzáadása a tárolóhoz
> * A tároló lekérdezése
> * Az elem CRUD-műveletek
> * Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Ugrás a [a teljes oktatóanyag megoldás beszerzése szakaszra](#GetSolution) gyors utasításokért.

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1\. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha vannak az Azure Cosmos DB Emulatort használja, kövesse [Azure Cosmos DB Emulator](local-emulator.md) az emulátor telepítéséhez, és folytassa a [beállítása a Visual Studio-projektek](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>2. lépés: A Visual Studio-projekt beállítása
1. Nyissa meg a **Visual Studio 2017-et** a számítógépén.
1. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
1. Az a **új projekt** párbeszédablakban válassza **Visual C#**   /  **Console App (.NET Framework)** , nevezze el a projektet, és kattintson **OK** .
    ![Képernyőfelvétel az új projekt ablakról](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > .NET core célként, az a **új projekt** párbeszédablakban válassza **Visual C#**   /  **Console App (.NET Core)** , nevezze el a projektet, és kattintson a  **oké**

1. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.

    ![A projekt jobb gombos kattintással elérhető menüjének képernyőképe](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Az a **NuGet** lapra, majd **Tallózás**, és írja be **Microsoft.Azure.Cosmos** kifejezést a keresőmezőbe.
1. A találatok között keresse meg a **Microsoft.Azure.Cosmos** kattintson **telepítése**.
   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Képernyőfelvétel a NuGet menüről az Azure Cosmos DB ügyféloldali SDK megkereséséhez menü](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! A [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a id="Connect"></a>3. lépés: Csatlakozás az Azure Cosmos DB-fiók
1. Első lépésként cserélje le a hivatkozások elején a C# az alkalmazás a **Program.cs** fájl az alábbi hivatkozásokat:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Ezután adja hozzá az e állandókat és változókat a nyilvános osztályba ``Program``.

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

1. Végponti URL-cím és az elsődleges kulcs beszerzéséért az [az Azure portal](https://portal.azure.com).

    Az Azure Portalon lépjen az Azure Cosmos DB-fiókra, majd kattintson a **Kulcsok** elemre.

    Másolja az URI-t a portálról, és illessze be azt `<your endpoint URL>` a a ```Program.cs``` fájlt. Másolja az elsődleges kulcsot a portálról, és illessze be azt `<your primary key>`.

   ![Képernyőkép: az Azure Portalról az Azure Cosmos DB-kulcsok beolvasása](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Ezután létrehozunk egy új példányát ```CosmosClient``` , és állítsa be a program bizonyos szerkezetkialakító.

    Alább a **fő** módot, adjon hozzá egy elnevezésű új aszinkron feladatot **GetStartedDemoAsync**, amely létrehozza nekünk az új ```CosmosClient```. Ezzel **GetStartedDemoAsync** módszereket hív meg, hogy a belépési pontként működik az Azure Cosmos DB erőforrásokat.

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

1. Adja hozzá a következő kódot futtatni a **GetStartedDemoAsync** aszinkron feladat a **fő** metódust. A **Fő** metódus észleli a kivételeket, és a konzolba írja azokat.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Válassza ki **F5** az alkalmazás futtatásához. A konzolablak kimenete a következő üzenet jelenik meg `End of demo, press any key to exit.` megjelenítésével erősíti meg az Azure Cosmos DB a kapcsolat létrejöttét. Ezután bezárhatja a konzolablakot.

Gratulálunk! Sikeresen csatlakoztatta az Azure Cosmos DB-fiókot. 

## <a name="step-4-create-a-database"></a>4\. lépés: Adatbázis létrehozása
Egy adatbázis használatával hozható létre a [ **Createdatabaseasync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) vagy [ **Documentclient** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) funkcióját a ``CosmosClient`` osztály. Az adatbázis a tárolók között particionált elemek logikai tárolója.

1. Másolja és illessze be a **Documentclient** az alábbi metódust a **GetStartedDemoAsync** metódust. **Documentclient** létrehoz egy új adatbázist azonosítójú ``FamilyDatabase`` Ha ezt még nem létezik, a megadott azonosítójú a ``databaseId`` mező. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Másolja és illessze be az alábbi kódot, ahol a CosmosClient meghívásához példányosítani, a **Documentclient** metódus az előzőekben adott hozzá.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Ezen a ponton a kód hasonlóan kell kinéznie, ez a végpont és az elsődleges kulcs kitölti a rendszer.

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

Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-adatbázist.  

## <a id="CreateColl"></a>5. lépés: Tároló létrehozása
> [!WARNING]
> A metódus meghívása **CreateContainerIfNotExistsAsync** létrehoz egy új tárolót, amely következményeiről. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Egy tároló használatával hozható létre a [ **CreateContainerIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) vagy [ **CreateContainerAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) működni a **CosmosDatabase** osztály. Egy tároló elemek áll (JSON-dokumentumok, ha az SQL API-t) és a kapcsolódó kiszolgálóoldali alkalmazáslogika javascriptben, például a tárolt eljárások, felhasználó által definiált függvények és eseményindítók.

1. Másolja és illessze be a **CreateContainerAsync** az alábbi metódust a **Documentclient** metódust. **CreateContainerAsync** létrehoz egy új tárolót azonosító ``FamilyContainer`` Ha ezt még nem létezik, a megadott azonosítójú a ``containerId`` mező dokumentumtárolási ``LastName`` tulajdonság.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Másolja és illessze be az alábbi kódot, ahol a CosmosClient meghívásához példányosítani, a **CreateContainer** metódus az előzőekben adott hozzá.

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

   Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-tárolót.  

## <a id="CreateDoc"></a>6. lépés: Elemek hozzáadása a tárolóhoz
Egy elem használatával hozható létre a [ **CreateItemAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) funkcióját a **CosmosContainer** osztály. Az SQL API használatával az elemek dokumentumokként vannak kivetítve, amelyek felhasználói (tetszőleges) JSON-tartalmak. Most már beszúrhat egy elemet az Azure Cosmos DB-tárolóba.

Először is hozzunk létre egy **termékcsalád** osztály, amely az Azure Cosmos DB-ben Ez a minta tárolt objektumokat képviseli. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Megjegyzés elem rendelkeznie kell egy **azonosítója** tulajdonság kézjegyként **azonosító** JSON-fájlban.

1. Válassza ki **Ctrl + Shift + A** megnyitásához a **új elem hozzáadása** párbeszédpanel. Adjon hozzá egy új osztályt **Family.cs** a projekthez.

    ![Képernyőkép a projektbe a Finderből új Family.cs osztály hozzáadása](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Másolja és illessze be a **termékcsalád**, **szülő**, **gyermek**, **Kisállat**, és **cím** be osztály**Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Lépjen vissza a **Program.cs** , és adja hozzá a **AddItemsToContainerAsync** módszer alapján az **CreateContainerAsync** metódust.
A kód ellenőrzi, hogy egy elemet ugyanazzal az azonosítóval már nem létezik előtt hozza létre. Szúrja be a Microsoft két elemet, egyet-egyet az Andersen családhoz pedig a Wakefield családhoz.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Adja hozzá egy hívást ``AddItemsToContainerAsync`` a a ``GetStartedDemoAsync`` metódust.

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

Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott két Azure Cosmos DB-elemekben.  

## <a id="Query"></a>7. lépés: Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](sql-api-sql-query.md). Az alábbi mintakód bemutatja, hogyan futtathat lekérdezéseket a cikkeket, hogy az előző lépésben beszúrt ellen.

1. Másolja és illessze be a **QueryItemsAsync** az alábbi metódust a **AddItemsToContainerAsync** metódust.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adja hozzá egy hívást ``QueryItemsAsync`` a a ``GetStartedDemoAsync`` metódust.

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

Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeres lekérdezést végzett egy Azure Cosmos DB-tárolók ellen.

## <a id="ReplaceItem"></a>8. lépés: Cserélje le egy JSON-elem
Most frissíteni fogjuk az Azure Cosmos DB egy elemet.

1. Másolja és illessze be a **ReplaceFamilyItemAsync** az alábbi metódust a **QueryItemsAsync** metódust. Vegye figyelembe, hogy módosítja a ``IsRegistered`` tulajdonság a családhoz és a ``Grade`` egy gyermekei.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adja hozzá egy hívást ``ReplaceFamilyItemAsync`` a a ``GetStartedDemoAsync`` metódust.

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

   Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeresen lecserélt egy Azure Cosmos DB-elemet.

## <a id="DeleteDocument"></a>9. lépés: Elem törlése
Most töröljük a az Azure Cosmos DB egy elemet.

1. Másolja és illessze be a **DeleteFamilyItemAsync** az alábbi metódust a **ReplaceFamilyItemAsync** metódust.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adja hozzá egy hívást ``DeleteFamilyItemAsync`` a a ``GetStartedDemoAsync`` metódust.

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

Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-elemet.

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
Most töröljük az adatbázisban. A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (tárolók és elemek bármely tárolt eljárásokat, felhasználói függvényeket és eseményindítókat). Mi lesz is tud megszabadulni a **CosmosClient** példány.

1. Másolja és illessze be a **DeleteDatabaseAndCleanupAsync** az alábbi metódust a **DeleteFamilyItemAsync** metódust.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adja hozzá egy hívást ``DeleteDatabaseAndCleanupAsync`` a a ``GetStartedDemoAsync`` metódust.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Válassza ki **F5** az alkalmazás futtatásához.

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-adatbázist.

## <a id="Run"></a>11. lépés: Futtassa a C# Konzolalkalmazás minden egy helyen!
Válassza ki az F5 össze és futtathatja az alkalmazást hibakeresési módban, a Visual studióban.

A kimenet egy konzolablakban a teljes alkalmazás kell megjelennie. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

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
* Egy [Azure Cosmos DB-fiók][cosmos-db-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) megoldás.

A hivatkozásokat az Azure Cosmos DB .NET SDK a Visual Studio-adatbázis visszaállításához kattintson a jobb gombbal a **GetStarted** megoldásra a Megoldáskezelőben, és kattintson **NuGet-csomagok visszaállítása**. Ezután az App.config fájlban frissítse az EndPointUri és PrimaryKey értékeket leírtak szerint [csatlakozhat az Azure Cosmos DB-fiók](#Connect).

Ennyi az egész, build azt, és máris jó úton jár!

## <a name="next-steps"></a>További lépések
* Összetettebb ASP.NET MVC-oktatóanyagot szeretne? Lásd: [ASP.NET MVC oktatóprogram: Webalkalmazások fejlesztése az Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Méret- és teljesítménytesztelést szeretne végezni az Azure Cosmos DB használatával? Tekintse meg az [Azure Cosmos DB használatával történő teljesítmény- és mérettesztelést](performance-testing.md) ismertető cikket.
* Ismerje meg, hogyan [monitorozhatja az Azure Cosmos DB-kérelmeket, -használatot és -tárolást](monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
