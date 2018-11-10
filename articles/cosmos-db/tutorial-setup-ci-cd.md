---
title: CI-/CD-folyamat beállítása az Azure Cosmos DB Emulator buildelési feladatával
description: Oktatóanyag a buildelési és kiadási munkafolyamatok Azure DevOpsban a Cosmos DB Emulator buildelési feladatával való beállításáról
services: cosmos-db
keywords: Azure Cosmos DB Emulator
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: dech
ms.openlocfilehash: 4f4cc18bb8423a20358476142488c94361d6b72d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419214"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>CI-/CD-folyamat beállítása az Azure Cosmos DB Emulator buildelési feladatával az Azure DevOpsban

Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Az emulátor használatával helyben fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. 

Az Azure Cosmos DB Emulator Azure DevOpshoz készült buildelési feladata lehetővé teszi ugyanezt CI-környezetben. A buildelési feladat segítségével összeállítási és kiadási munkafolyamatai részeként futtathat teszteket az emulátorban. A feladat elindít egy Docker-tárolót, amelyben már fut az emulátor, és végpontot biztosít a builddefiníció további része számára. Tetszőleges számú emulátorpéldányt hozhat létre és indíthat el, amelyek mind külön tárolóban futnak. 

Ez a cikk bemutatja, hogyan állíthat be CI-folyamatot az Azure DevOpsban egy ASP.NET-alkalmazás számára, amely a Cosmos DB Emulator buildelési feladatával futtat teszteket. 

## <a name="install-the-emulator-build-task"></a>Az emulátor buildelési feladatának telepítése

A buildelési feladatot használat előtt telepítenie kell az Azure DevOps-szervezetben. Keresse meg az **Azure Cosmos DB Emulator** bővítményt a [Marketplace-en](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview), és kattintson a **Get it free** (Ingyenes beszerzés) elemre.

![Az Azure Cosmos DB Emulator buildelési feladatának megkeresése és telepítése az Azure DevOps Marketplace-ről](./media/tutorial-setup-ci-cd/addExtension_1.png)

Ezután válassza ki a szervezetet, amelyben telepíteni kívánja a bővítményt. 

> [!NOTE]
> Egy bővítmény Azure DevOps-szervezetben való telepítéséhez fióktulajdonosnak vagy a projektgyűjtemény adminisztrátorának kell lennie. Ha nem rendelkezik engedélyekkel, de a fiók tagja, ehelyett kérheti a bővítményt. [Részletek](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions)

![Azon Azure DevOps-szervezet kiválasztása, amelyben telepíteni kívánja a bővítményt](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Builddefiníció létrehozása

Miután telepítette, hozzá kell adnia a bővítményt egy [builddefinícióhoz](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). Használhat egy meglévő builddefiníciót, vagy létrehozhat egy újat. Ha már rendelkezik builddefinícióval, folytassa [az Emulator buildelési feladatának hozzáadása egy builddefinicióhoz](#addEmulatorBuildTaskToBuildDefinition) résszel.

Új builddefiníció létrehozásához lépjen Azure DevOps **Build and Release** (Buildelés és kiadás) lapjára. Válassza a **+New** (+Új) lehetőséget.

![Új builddefiníció létrehozása](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Válassza ki a kívánt csapatprojektet, adattárat és ágat a buildek engedélyezéséhez. 

![Válassza ki a csapatprojektet, az adattárat és az ágat a builddefiníció számára ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

Végül válassza ki a builddefinícióhoz használni kívánt sablont. Ebben az oktatóanyagban az **ASP.NET** sablont választjuk. 

![Válassza ki a builddefinícióhoz használni kívánt sablont ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Most már van builddefiníciónk, amelyet beállíthatunk az Azure Cosmos DB emulátor buildelési feladatának használatához, és amely az alábbihoz hasonlóan fest. 

![ASP.NET builddefiníció-sablon](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Feladat hozzáadása egy builddefinícióhoz

Az emulátor buildelési feladatának hozzáadásához keressen a **cosmos** kifejezésre a keresőmezőben, és válassza az **Add** (Hozzáadás) lehetőséget. A buildelési feladat elindít egy tárolót, amelyben már fut a Cosmos DB Emulator egy példánya, ezért minden olyan feladat elé kell ütemezni, amelyhez az emulátor futása szükséges.

![Az emulátor buildelési feladatának hozzáadása a builddefinícióhoz](./media/tutorial-setup-ci-cd/addExtension_3.png) Ebben az oktatóanyagban a feladatot az 1. fázis kezdetéhez adjuk hozzá, hogy biztosítsuk az emulátor elérhetőségét a tesztjeink futtatása előtt.
A kész builddefiníció most így néz ki: 

![ASP.NET builddefiníció-sablon](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Tesztek konfigurálása az emulátor használatához
Most konfigurálni fogjuk a tesztjeinket az emulátor használatához. Az emulátor buildelési feladata exportál egy környezeti változót (CosmosDbEmulator.Endpoint), amelyre a buildfolyamat összes további feladata küldhet kéréseket. 

Ebben az oktatóanyagban a [Visual Studio tesztelési feladatával](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) futtatunk olyan egységteszteket, amelyek **.runsettings** fájllal lettek konfigurálva. Az egységtesztek beállításáról a [dokumentációban](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017) talál további információt.

Az alábbiakban mutatunk egy példát a **.runsettings** fájlra, amely definiálja egy alkalmazás egységtesztjeibe továbbítandó paramétereket. Figyelje meg, hogy a használt `authKey` változó az emulátor [jól ismert kulcsa](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests). Az `authKey` az emulátor buildelési feladatának várt kulcsa, és definiálva kell lennie a **.runsettings** fájlban.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Ezekre a paraméterekre (`TestRunParameters`) az alkalmazás tesztelési projektjei egy `TestContext` tulajdonsággal hivatkoznak. Íme egy példa a Cosmos DB-ben futó tesztre.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Lépjen az Execution Options (Végrehajtási beállítások) lapra a Visual Studio-tesztfeladatban. A **Settings file** (Beállításfájl) lehetőségnél adja meg, hogy a tesztek konfigurálása a **.runsettings** fájllal történjen. Az **Override test run parameters** (A tesztfuttatás paramétereinek felülbírálása) beállításnál adja hozzá az ` -endpoint $(CosmosDbEmulator.Endpoint)` értéket. Ezzel úgy konfigurálja a tesztelési feladatot, hogy az emulátor buildelési feladatának végpontjára hivatkozzon a **.runsettings** fájlban definiált végpont helyett.  

![A végpont változójának felülbírálása az emulátor buildelési feladatának végpontjával](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>A build futtatása
Most mentse és helyezze várakozási sorba a buildet. 

![A build mentése és futtatása](./media/tutorial-setup-ci-cd/runBuild_1.png)

Figyelje meg, hogy amint a build elindul, a Cosmos DB emulátor elkezdi a telepített emulátort tartalmazó Docker-rendszerkép lekérését. 

![A build mentése és futtatása](./media/tutorial-setup-ci-cd/runBuild_4.png)

Miután a build létrejött, láthatja, hogy a buildelési feladatból a Cosmos DB emulátorban futó tesztek sikeresen zárultak.

![A build mentése és futtatása](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>További lépések

További információ az emulátor helyi fejlesztéshez és teszteléshez való használatáról: [Az Azure Cosmos DB Emulator használata helyi fejlesztéshez és teszteléshez](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Az emulátor SSL-tanúsítványainak exportálásáról a [Java-, Python- és Node.js-környezethez használható Azure Cosmos DB Emulator-tanúsítványok exportálása](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates) című cikkben olvashat.
