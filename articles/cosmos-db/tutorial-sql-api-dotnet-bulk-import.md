---
title: Adatok tömeges importálása az Azure Cosmos DB SQL API-fiókba a .Net SDK használatával
description: Megtudhatja, hogy miként importálhat vagy nagyíthat adatokat az Azure Cosmos DB-ba egy .NET konzolalkalmazás létrehozásával, amely optimalizálja az adatok importálásához szükséges kiépített átviteli -ot (RU/s)
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587566"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Adatok tömeges importálása az Azure Cosmos DB SQL API-fiókba a .NET SDK használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy .NET konzolalkalmazást, amely optimalizálja az Azure Cosmos DB-re történő adatok importálásához szükséges kiépített átviteli -t (RU/s). Ebben a cikkben egy minta adatforrásból származó adatokat fog olvasni, és importálja azokat egy Azure Cosmos-tárolóba.
Ez az oktatóanyag az Azure Cosmos DB .NET SDK [3.0-s verzióját](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) használja, amely a .

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Azure Cosmos-fiók létrehozása
> * A projekt konfigurálása
> * Csatlakozás Azure Cosmos-fiókhoz tömeges támogatással
> * Adatimportálás végrehajtása egyidejű létrehozási műveleteken keresztül

## <a name="prerequisites"></a>Előfeltételek

A cikkben található utasítások bekövetése előtt győződjön meg arról, hogy rendelkezik a következő erőforrásokkal:

* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). A futtatásával `dotnet --version`ellenőrizheti, hogy melyik verzió érhető el a környezetében.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása

[Hozzon létre egy Azure Cosmos DB SQL API-fiókot](create-cosmosdb-resources-portal.md) az Azure Portalon, vagy létrehozhatja a fiókot az [Azure Cosmos DB emulátor](local-emulator.md)használatával.

## <a name="step-2-set-up-your-net-project"></a>2. lépés: A .NET projekt beállítása

Nyissa meg a Windows parancssort vagy a Terminál ablakot a helyi számítógépről. A parancssorból vagy a terminálból a következő szakaszokban lévő összes parancsot futtatja. Futtassa a következő dotnet új parancsot, hogy hozzon létre egy új alkalmazást a tömeges *importálás-demó*névvel. A `--langVersion` paraméter beállítja a *LangVersion* tulajdonságot a létrehozott projektfájlban.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

A build várható kimenetének valahogy így kell kinéznie:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>3. lépés: Az Azure Cosmos DB-csomag hozzáadása

Amíg az alkalmazáskönyvtárban van, telepítse az Azure Cosmos DB ügyfélkönyvtárat a .NET Core-hoz a dotnet add package paranccsal.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>4. lépés: Az Azure Cosmos-fiók hitelesítő adatainak beszereznie

A mintaalkalmazás hitelesítésére az Azure Cosmos-fiók. A hitelesítéshez adja át az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatainak beszerezése az alábbi lépésekkel:

1.  Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1.  Keresse meg az Azure Cosmos-fiókot.
1.  Nyissa meg a **Kulcsok** ablaktáblát, és másolja a fiók **URI-** és **ELSŐDLEGES KULCSát.**

Ha az Azure Cosmos DB-emulátort használja, szerezze be az [emulátor hitelesítő adatait ebből a cikkből.](local-emulator.md#authenticating-requests)

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>5. lépés: A CosmosClient objektum inicializálása tömeges végrehajtás támogatással

Nyissa meg `Program.cs` a létrehozott fájlt egy kódszerkesztőben. Hozzon létre egy új példányt a CosmosClient tömeges végrehajtás engedélyezve van, és használja az Azure Cosmos DB elleni műveletek végrehajtásához. 

Kezdjük az alapértelmezett `Main` módszer felülírásával és a globális változók meghatározásával. Ezek a globális változók tartalmazzák a végpontot és az engedélyezési kulcsokat, az adatbázis nevét, a létrehozandó tárolót és a tömegesen beszúrni kívánt elemek számát. Győződjön meg arról, hogy cserélje le a végpontURL és engedélyezési kulcs értékeit a környezetnek megfelelően. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

A `Main` metóduson belül adja hozzá a következő kódot a CosmosClient objektum inicializálásához:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Miután a tömeges végrehajtás engedélyezve van, a CosmosClient belsőleg csoportosítja az egyidejű műveleteket egyetlen szolgáltatáshívásokba. Így optimalizálja az átviteli kihasználtságot a szolgáltatáshívások partíciók közötti elosztásával, és végül az egyes eredmények hozzárendelésével az eredeti hívók.

Ezután létrehozhat egy tárolót az összes elemünk tárolásához.  Definiálja `/pk` a partíciókulcs, 50000 RU/s kiépített átviteli mivel, és egy egyéni indexelési szabályzat, amely kizárja az összes mezőt az írási átviteli hang optimalizálása érdekében. Adja hozzá a következő kódot a CosmosClient inicializálási utasítása után:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>6. lépés: Az egyidejű feladatok listájának feltöltése

A tömeges végrehajtás támogatásának kihasználásához hozzon létre egy listát az aszinkron feladatokról az adatforrás és a `Task.WhenAll` végrehajtani kívánt műveletek alapján, és használja őket egyidejűleg.
Kezdjük a "Hamis" adatok használatával az adatmodellünk elemeinek listájának létrehozásához. Egy valós alkalmazásban az elemek a kívánt adatforrásból származnak.

Először adja hozzá a Hamis csomagot a megoldáshoz a dotnet add package paranccsal.

   ```bash
   dotnet add package Bogus
   ```

Adja meg a menteni kívánt elemek definícióját. Meg kell határoznia `Item` az `Program.cs` osztályt a fájlon belül:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Ezután hozzon létre egy `Program` segítő függvényt az osztályon belül. Ez a segítő funkció a beszúráshoz definiált elemek számát kapja meg, és véletlenszerű adatokat hoz létre:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Olvassa el az elemeket, és szerializálja őket stream példányok az `System.Text.Json` osztály használatával. Az automatikusan generált adatok jellege miatt az adatokat adatfolyamként szerializálja. Az elempéldányt közvetlenül is használhatja, de streamekké konvertálásával kihasználhatja a stream API-k teljesítményét a CosmosClient-ben. Általában használhatja az adatokat közvetlenül, amíg ismeri a partíciókulcsot. 


Ha az adatokat adatfolyam-példányokká szeretné konvertálni, a `Main` metóduson belül a következő kódot adja hozzá közvetlenül a tároló létrehozása után:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Ezután használja az adatfolyamokat egyidejű feladatok létrehozásához, és feltölti a feladatlistát az elemek tárolóba való beszúrásához. A művelet végrehajtásához adja hozzá `Program` a következő kódot az osztályhoz:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Ezek az egyidejű pontműveletek együtt lesznek végrehajtva (amely ömlesztve történik) a bevezetési szakaszban leírtak szerint.

## <a name="step-7-run-the-sample"></a>7. lépés: A minta futtatása

A minta futtatásához egyszerűen a `dotnet` következő paranccsal teheti meg:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>A teljes minta beszerezni

Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a mintakódokat, a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer) beszerezhetőek.

A projekt klónozása után győződjön meg arról, hogy frissíti a kívánt hitelesítő adatokat [Program.cs.](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25)

A minta futtatható a tárház könyvtárára való váltással és a következő használatával: `dotnet`

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő lépéseket végezte el:

> [!div class="checklist"]
> * Azure Cosmos-fiók létrehozása
> * A projekt konfigurálása
> * Csatlakozás Azure Cosmos-fiókhoz tömeges támogatással
> * Adatimportálás végrehajtása egyidejű létrehozási műveleteken keresztül

Most folytathatja a következő oktatóanyagot:

> [!div class="nextstepaction"]
>[Az Azure Cosmos DB lekérdezése az SQL API használatával](tutorial-query-sql-api.md)