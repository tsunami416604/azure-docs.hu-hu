---
title: CI/CD-folyamat beállítása az Azure Cosmos DB emulátor buildelési feladattal
description: Oktatóanyag a buildelési és kiadási munkafolyamatok Azure DevOpsban a Cosmos DB Emulator buildelési feladatával való beállításáról
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 521d5d8d587b39cf573dedc37ea9f6fd53646e66
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410955"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>CI-/CD-folyamat beállítása az Azure Cosmos DB Emulator buildelési feladatával az Azure DevOpsban

Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Az emulátor használatával helyben fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. 

Az Azure Cosmos DB Emulator Azure DevOpshoz készült buildelési feladata lehetővé teszi ugyanezt CI-környezetben. A buildelési feladat segítségével összeállítási és kiadási munkafolyamatai részeként futtathat teszteket az emulátorban. A feladat elindít egy Docker-tárolót, amelyben már fut az emulátor, és végpontot biztosít a builddefiníció további része számára. Tetszőleges számú emulátorpéldányt hozhat létre és indíthat el, amelyek mind külön tárolóban futnak. 

Ez a cikk bemutatja, hogyan állíthat be CI-folyamatot az Azure DevOpsban egy ASP.NET-alkalmazás számára, amely a Cosmos DB Emulator buildelési feladatával futtat teszteket. Hasonló megközelítést használhat egy CI-folyamat node.js vagy python-alkalmazáshoz történő beállításához. 

## <a name="install-the-emulator-build-task"></a>Az emulátor buildelési feladatának telepítése

A buildelési feladatot használat előtt telepítenie kell az Azure DevOps-szervezetben. Keresse meg az **Azure Cosmos DB Emulator** bővítményt a [Marketplace-en](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview), és kattintson a **Get it free** (Ingyenes beszerzés) elemre.

![Az Azure Cosmos DB Emulator buildelési feladatának megkeresése és telepítése az Azure DevOps Marketplace-ről](./media/tutorial-setup-ci-cd/addExtension_1.png)

Ezután válassza ki a szervezetet, amelyben telepíteni kívánja a bővítményt. 

> [!NOTE]
> Egy Azure DevOps-szervezet bővítményének telepítéséhez fióktulajdonosnak vagy projektgyűjtemény-rendszergazdának kell lennie. Ha nem rendelkezik engedélyekkel, de a fiók tagja, ehelyett kérheti a bővítményt. [További információ.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![Válasszon olyan Azure DevOps-szervezetet, amelyben bővítményt szeretne telepíteni](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Builddefiníció létrehozása

Most, hogy a bővítmény települt, jelentkezzen be az Azure DevOps-fiókjába, és keresse meg a projektet a projektek irányítópultján. Hozzáadhat egy új [buildfolyamatot](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) a projekthez, vagy módosíthat egy meglévőt is. Ha már rendelkezik buildfolyamattal, továbbléphet [az Emulator buildelési feladatának hozzáadása egy builddefinícióhoz](#addEmulatorBuildTaskToBuildDefinition) részre.

1. Új builddefiníció létrehozásához lépjen az Azure DevOps **Builds** (Buildek) lapjára. Válassza a **+Új lehetőséget.** \> **Új buildfolyamat**

   ![Új buildfolyamat létrehozása](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Válassza ki a kívánt értékeket a következőkhöz: **Source** (Forrás), **Team project** (Csapatprojekt), **Repository** (Adattár) és **Default branch for manual and scheduled builds** (Manuális és ütemezett buildelések alapértelmezett ága). Miután megadta a kívánt beállításokat, kattintson a **Continue** (Folytatás) elemre.

   ![Válassza ki a csapatprojektet, az adattárat és az ágat a buildfolyamathoz.](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Végül válassza ki a buildfolyamathoz használni kívánt sablont. Ebben az oktatóanyagban az **ASP.NET** sablont választjuk. Most már rendelkezik egy buildfolyamat, amely beállítható az Azure Cosmos DB emulátor build feladat használatára. 

> [!NOTE]
> A ci-hez kiválasztandó ügynökkészlethez telepítve kell lennie a Docker for Windows rendszernek, kivéve, ha a telepítés manuálisan történik egy korábbi feladatban a ci részeként. A [Microsoft által üzemeltetett ügynökökről](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) szóló cikkben az ügynökkészletek egy részét is megtekintheti; javasoljuk, hogy `Hosted VS2017`kezdje .

Az Azure Cosmos DB-emulátor jelenleg nem támogatja a házigazdája VS2019 ügynökkészletet. Azonban az emulátor már rendelkezik a VS2019 telepítve, és használja az emulátor elindításával a következő PowerShell-parancsmagok. Ha a VS2019 használata során bármilyen problémába ütközik, forduljon segítségért az [Azure DevOps-csapatához:](https://developercommunity.visualstudio.com/spaces/21/index.html)

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Feladat hozzáadása buildfolyamathoz

1. Mielőtt hozzáadhatna egy feladatot a buildfolyamathoz, hozzá kell adnia egy ügynökfeladatot. Keresse meg a buildfolyamatot, kattintson a három pont (**...**) elemre, és válassza az **Add an agent job** (Ügynökfeladat hozzáadása) lehetőséget.

1. Ezután **+** válassza ki az ügynöki feladat melletti szimbólumot az emulátor build feladat hozzáadásához. Keressen a **cosmos** kifejezésre a keresőmezőben, válassza ki az **Azure Cosmos DB Emulator** lehetőséget, és adja hozzá az ügynökfeladathoz. A buildelési feladat elindít egy tárolót, amelyben már fut a Cosmos DB Emulator egy példánya. Az Azure Cosmos DB Emulator-feladatot minden olyan feladat elé kell ütemezni, amelyhez az emulátor futása szükséges.

   ![Az emulátor buildelési feladatának hozzáadása a builddefinícióhoz](./media/tutorial-setup-ci-cd/addExtension_3.png)

Ebben az oktatóanyagban a feladatot a legelején adjuk hozzá, hogy biztosítsuk az emulátor elérhetőségét a tesztjeink futtatása előtt.

## <a name="configure-tests-to-use-the-emulator"></a>Tesztek konfigurálása az emulátor használatához

Most konfigurálni fogjuk a tesztjeinket az emulátor használatához. Az emulátor buildelési feladata exportál egy környezeti változót (CosmosDbEmulator.Endpoint), amelyre a buildfolyamat összes további feladata küldhet kéréseket. 

Ebben az oktatóanyagban a [Visual Studio tesztelési feladatával](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) futtatunk olyan egységteszteket, amelyek **.runsettings** fájllal lettek konfigurálva. Az egységtesztek beállításáról a [dokumentációban](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017) talál további információt. A dokumentumban használt teljes Todo alkalmazáskód-minta elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

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

Ha egy CI/CD-folyamatot állít be egy olyan alkalmazáshoz, amely az Azure Cosmos DB API-ját használja a MongoDB-hoz, a kapcsolati karakterlánc alapértelmezés szerint tartalmazza a 10255-ös portszámot. Ez a port azonban jelenleg nincs megnyitva, alternatívaként az 10250-es portot kell használnia a kapcsolat létrehozásához. Az Azure Cosmos DB MongoDB-kapcsolati karakterláncapi-ja ugyanaz marad, kivéve, hogy a támogatott portszám 10250 az 10255 helyett.

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

Lépjen az Execution Options (Végrehajtási beállítások) lapra a Visual Studio-tesztfeladatban. A **Settings file** (Beállításfájl) lehetőségnél adja meg, hogy a tesztek konfigurálása a **.runsettings** fájllal történjen. Az **Override test run parameters** (A tesztfuttatás paramétereinek felülbírálása) beállításnál adja hozzá az `-endpoint $(CosmosDbEmulator.Endpoint)` értéket. Ezzel úgy konfigurálja a tesztelési feladatot, hogy az emulátor buildelési feladatának végpontjára hivatkozzon a **.runsettings** fájlban definiált végpont helyett.  

![A végpont változójának felülbírálása az emulátor buildelési feladatának végpontjával](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>A build futtatása

Most a **Save and queue** (Mentés és üzenetsorba helyezés) elemre kattintva mentse és helyezze az üzenetsorba a buildet. 

![A build mentése és futtatása](./media/tutorial-setup-ci-cd/runBuild_1.png)

Figyelje meg, hogy amint a build elindul, a Cosmos DB emulátor elkezdi a telepített emulátort tartalmazó Docker-rendszerkép lekérését. 

![A build mentése és futtatása](./media/tutorial-setup-ci-cd/runBuild_4.png)

Miután a build létrejött, láthatja, hogy a buildelési feladatból a Cosmos DB emulátorban futó tesztek sikeresen zárultak.

![A build mentése és futtatása](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="set-up-using-yaml"></a>Beállítás a YAML használatával

Ha a CI/CD-folyamatot YAML-feladattal állítja be, a YAML-feladatot a következő kód szerint határozhatja meg:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="next-steps"></a>További lépések

További információ az emulátor helyi fejlesztéshez és teszteléshez való használatáról: [Az Azure Cosmos DB Emulator használata helyi fejlesztéshez és teszteléshez](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Emulátor TLS/SSL-tanúsítványok exportálása: [Az Azure Cosmos DB emulátor-tanúsítványok exportálása Java, Python és Node.js használatával](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
