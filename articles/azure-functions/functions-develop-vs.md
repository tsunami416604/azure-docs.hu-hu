---
title: Azure Functions fejlesztése a Visual Studióval
description: Megtudhatja, hogyan fejlesztheti és tesztelheti Azure Functionseket a Visual Studio 2019 Azure Functions Tools használatával.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484706"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Azure Functions fejlesztése a Visual Studióval  

A Visual Studio lehetővé teszi, hogy az Azure- C# hoz fejlesszen, tesztelje és telepítse az osztály-függvénytári funkciókat. Ha ez a felhasználói élmény Azure Functions, akkor a [Azure functions bevezetésével](functions-overview.md)többet is megtudhat.

A Visual Studio a következő előnyöket biztosítja a függvények fejlesztésekor: 

* Függvények szerkesztése, létrehozása és futtatása a helyi fejlesztői számítógépen. 
* Tegye közzé a Azure Functions projektet közvetlenül az Azure-ban, és szükség szerint hozzon létre Azure-erőforrásokat. 
* Attribútumok C# használatával deklarálhatja a függvény kötéseit közvetlenül a C# kódban.
* Előre lefordított C# függvények fejlesztése és üzembe helyezése. Az előzetesen teljesített függvények jobb, a C# parancsfájl-alapú függvényeknél hatékonyabban megkezdhető teljesítményt nyújtanak. 
* A függvények kódja a C# Visual Studio-fejlesztés összes előnyének kihasználásával. 

Ez a cikk részletesen ismerteti, hogyan használható a Visual Studio C# a Class Library-függvények fejlesztéséhez és az Azure-ban való közzétételéhez. A cikk elolvasása előtt végre kell hajtania a [Visual Studio functions](functions-create-your-first-function-visual-studio.md)rövid útmutatóját. 

Hacsak másként nincs jelezve, az eljárások és a példák a Visual Studio 2019-re vonatkoznak. 

## <a name="prerequisites"></a>Előfeltételek

A Visual Studióban a Visual Studio 2017-es verziótól kezdődően az Azure-fejlesztési számítási feladatok részét képező Azure Functions eszközök. Ügyeljen rá, hogy a Visual Studio-telepítésben szerepeljen az **Azure-fejlesztési** számítási feladat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A közzétételi folyamat során a szükséges egyéb erőforrások, például az Azure Storage-fiókok az előfizetésben jönnek létre.

> [!NOTE]
> A Visual Studio 2017-es verziójában az Azure-fejlesztési munkaterhelés külön bővítményként telepíti a Azure Functions eszközöket. A Visual Studio 2017 frissítésekor ellenőrizze, hogy a Azure Functions eszközök [legújabb verzióját](#check-your-tools-version) használja-e. A következő részben bemutatjuk, hogyan lehet megtekinteni és (ha szükséges) frissíteni a Azure Functions Tools bővítményt a Visual Studio 2017-ben. 
>
> A Visual Studio 2019 használata esetén ugorja át ezeket a szakaszokat.

### <a name="check-your-tools-version"></a>Tekintse meg az eszközök verzióját a Visual Studio 2017-ben

1. A **Tools (eszközök** ) menüben válassza a **bővítmények és frissítések**lehetőséget. Bontsa ki a **telepített** > **eszközök** elemet, és válassza **a Azure functions és a webes feladatok eszközöket**.

    ![A functions Tools verziójának ellenőrzése](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Jegyezze fel a telepített **verziót**. Ezt a verziót a [kibocsátási megjegyzésekben](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)felsorolt legújabb verzióval is összehasonlíthatja. 

1. Ha a verzió régebbi, frissítse az eszközök a Visual Studióban, a következő szakaszban látható módon.

### <a name="update-your-tools-in-visual-studio-2017"></a>Eszközök frissítése a Visual Studio 2017-ban

1. A **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések** > a **Visual Studio Marketplace**elemet, majd válassza a **Azure functions és a webjobs-eszközök** lehetőséget, és válassza a **frissítés**lehetőséget.

    ![A functions Tools verziójának frissítése](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Után az eszközök a frissítés letöltését követően zárja be a Visual Studióban az eszközök frissítése a VSIX telepítővel eseményindítóra.

1. A telepítőben kattintson az **OK** gombra a kezdéshez, majd **módosítsa** az eszközök frissítéséhez. 

1. A frissítés befejezése után kattintson a **Bezárás gombra** , és indítsa újra a Visual studiót.

> [!NOTE]  
> A Visual Studio 2019-es és újabb verzióiban a Azure Functions Tools bővítmény a Visual Studio részeként frissül.  

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

A Project sablon létrehoz egy C# projektet, telepíti a `Microsoft.NET.Sdk.Functions` NuGet csomagot, és beállítja a célként megadott keretrendszert. Az új projekt a következő fájlokat tartalmazhatja:

* **Host. JSON**: lehetővé teszi a functions gazdagép konfigurálását. Ezek a beállítások mind a helyi, mind az Azure-beli futtatáskor érvényesek. További információ: [Host. JSON-dokumentáció](functions-host-json.md).

* **Local. Settings. JSON**: a függvények helyi futtatásakor használt beállításokat tartja karban. Ezek a beállítások nem használhatók az Azure-ban való futtatáskor. További információ: [Local Settings fájl](#local-settings-file).

    >[!IMPORTANT]
    >Mivel a local. Settings. JSON fájl tartalmazhat titkos kódokat, ki kell zárnia azt a projekt forrásának vezérlőjéből. **Ha újabb**értékre van állítva, akkor a fájl **másolásának kimeneti könyvtárra** vonatkozó beállítását mindig másolja. 

További információ: [functions Class Library Project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

A local. Settings. JSON beállításai nem kerülnek automatikusan feltöltésre a projekt közzétételekor. Ahhoz, hogy ezek a beállítások az Azure-beli Function alkalmazásban is elérhetők legyenek, a projekt közzététele után fel kell töltenie őket. További információ: [Function app Settings](#function-app-settings).

A **ConnectionStrings** lévő értékek soha nem lesznek közzétéve.

A Function app Settings értékeit környezeti változókként is beolvashatja a kódban. További információ: [környezeti változók](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>A projekt konfigurálása helyi fejlesztéshez

A functions futtatókörnyezet belsőleg használ egy Azure Storage-fiókot. A HTTP és webhookok kivételével az összes trigger típusnál a **Values. AzureWebJobsStorage** kulcsot egy érvényes Azure Storage-fiók kapcsolati karakterláncához kell beállítania. A Function alkalmazás az [Azure Storage emulatort](../storage/common/storage-use-emulator.md) is használhatja a projekthez szükséges **AzureWebJobsStorage** -kapcsolatok beállításához. Az emulátor használatához állítsa `UseDevelopmentStorage=true`értékre a **AzureWebJobsStorage** értéket. Az üzembe helyezés előtt módosítsa a beállítást egy tényleges Storage-fiók kapcsolódási karakterláncára.

A Storage-fiók kapcsolódási karakterláncának beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer alkalmazást**, bontsa ki a **storage** -fiók > **a Storage-fiókját**, majd a **Tulajdonságok** lapon másolja az **elsődleges kapcsolatok karakterláncának** értékét.

2. A projektben nyissa meg a local. Settings. JSON fájlt, és állítsa be a **AzureWebJobsStorage** kulcs értékét a másolt kapcsolódási karakterláncra.

3. Az előző lépés megismétlésével egyedi kulcsokat adhat hozzá az **értékek** tömbhöz a függvények által igényelt egyéb kapcsolatokhoz. 

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

Az C# osztály-függvénytár függvények a függvény által használt kötéseket a kódban található attribútumok alkalmazásával határozzák meg. Amikor létrehozza a függvény eseményindítóit a megadott sablonokból, az eseményindító attribútumai lesznek alkalmazva. 

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Add** (Hozzáadás)  > **New Item** (Új elem) lehetőséget. Válassza az **Azure-függvény**lehetőséget, írja be az osztály **nevét** , majd kattintson a **Hozzáadás**gombra.

2. Válassza ki az triggert, állítsa be a kötési tulajdonságokat, majd kattintson a **Létrehozás**gombra. A következő példában a várólista-tároló által aktivált függvény létrehozásakor a beállítások láthatók. 

    ![Üzenetsor által aktivált függvény létrehozása](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ez az trigger-példa egy **QueueStorage**nevű kulccsal rendelkező kapcsolatok karakterláncot használ. Ezt a kapcsolattípus-beállítást a [Local. Settings. JSON fájlban](functions-run-local.md#local-settings-file)kell megadni.

3. Vizsgálja meg az újonnan hozzáadott osztályt. Egy statikus **futtatási** metódus jelenik meg, amely a **függvénynév** attribútummal van társítva. Ez az attribútum azt jelzi, hogy a metódus a függvény belépési pontja.

    A következő C# osztály például egy alapszintű üzenetsor-tároló által aktivált függvényt képvisel:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    A rendszer a belépési pont metódusához megadott kötési paraméterekre alkalmazza a kötés-specifikus attribútumot. Az attribútum paraméterként a kötési adatokat veszi fel. Az előző példában az első paraméter egy **QueueTrigger** attribútummal rendelkezik, amely jelzi az üzenetsor által aktivált függvényt. A várólista neve és a kapcsolódási karakterlánc beállításának neve paraméterként a **QueueTrigger** attribútumnak lesz átadva. További információ: [Azure üzenetsor-tárolási kötések Azure Functionshoz](functions-bindings-storage-queue-trigger.md).

A fenti eljárás segítségével további függvényeket adhat hozzá a Function app-projekthez. A projekt egyes függvényei eltérő triggerrel rendelkezhetnek, de a függvénynek pontosan egy triggerrel kell rendelkeznie. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Kötések hozzáadása

Ahogy az eseményindítók esetében, a bemeneti és kimeneti kötések kötési attribútumokként lesznek hozzáadva a függvényhez. Adja hozzá a kötéseket egy függvényhez a következő módon:

1. Győződjön meg arról, hogy [konfigurálta a projektet a helyi fejlesztéshez](#configure-the-project-for-local-development).

2. Adja hozzá a megfelelő NuGet-kiterjesztési csomagot az adott kötéshez. További információ: [helyi C# fejlesztés a Visual Studióval](./functions-bindings-register.md#local-csharp) az eseményindítók és kötések című cikkben. A kötési NuGet-csomagra vonatkozó követelmények a kötés dokumentációjában találhatók. Megkeresheti például a Event Hubs trigger csomagra vonatkozó követelményeit a [Event Hubs kötési útmutató cikkben](functions-bindings-event-hubs.md).

3. Ha vannak olyan Alkalmazásbeállítások, amelyekre a kötés szükséges, adja hozzá őket az **értékek** gyűjteményhez a [helyi beállítási fájlban](functions-run-local.md#local-settings-file). Ezeket az értékeket akkor használja a rendszer, amikor a függvény helyileg fut. Ha a függvény az Azure-beli Function alkalmazásban fut, a rendszer a [Function app beállításait](#function-app-settings) használja.

4. Adja hozzá a megfelelő kötési attribútumot a metódus aláírásához. A következő példában egy üzenetsor-üzenet elindítja a függvényt, és a kimeneti kötés egy új üzenetsor-üzenetet hoz létre, amely egy másik várólistában található azonos szöveggel rendelkezik.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   A várólista-tárolóhoz való kapcsolódás a `AzureWebJobsStorage`-beállításból származik. További információkért tekintse meg az adott kötésre vonatkozó hivatkozási cikket. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

A-t futtató projekt segítségével tesztelheti a kódot úgy, ahogy az üzembe helyezett függvény tesztelését végzi. További információ: stratégiák a [kód teszteléséhez Azure functions](functions-test-a-function.md). Hibakeresési módban való futtatáskor a rendszer a várt módon megkeresi a töréspontokat a Visual Studióban. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Ha többet szeretne megtudni a Azure Functions Core Tools használatáról, tekintse meg a [code and test Azure functions helyileg](functions-run-local.md)című témakört.

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Visual studióból való közzétételkor a rendszer a következő két telepítési módszer egyikét használja:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): csomagok és Windows-alkalmazások telepítése bármely IIS-kiszolgálóra.
* A [zip üzembe helyezése a-csomaggal engedélyezve: Azure functions üzemelő](functions-deployment-technologies.md#zip-deploy)példányokhoz ajánlott.

A következő lépésekkel teheti közzé a projektjét egy Azure-beli Function alkalmazásban.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>A függvényalkalmazás beállításai

A local. Settings. JSON fájlban hozzáadott beállításokat is fel kell venni az Azure-beli Function alkalmazásba. Ezeket a beállításokat a projekt közzétételekor nem kell automatikusan feltölteni.

A szükséges beállításoknak az Azure-beli Function alkalmazásba való feltöltésének legegyszerűbb módja az **Alkalmazásbeállítások kezelése...** hivatkozás, amely a projekt sikeres közzétételekor jelenik meg.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Ez megjeleníti a Function **alkalmazás Alkalmazásbeállítások** párbeszédpanelét, ahol új Alkalmazásbeállítások hozzáadására vagy a meglévők módosítására van lehetőség.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

A **Local** érték a local. Settings. JSON fájlban, a **távoli** pedig az Azure-beli Function alkalmazás jelenlegi beállítását jelöli.  Új Alkalmazásbeállítás létrehozásához válassza a **beállítás hozzáadása** lehetőséget. A beállítás értékének a **helyi** hivatkozásból történő másolásához használja a Setting értéket a **távoli** mezőre. A függőben lévő módosítások a helyi beállítások fájlba és a Function alkalmazásba íródnak, amikor az **OK gombra**kattint.

> [!NOTE]
> Alapértelmezés szerint a local. Settings. JSON fájl nincs bejelölve a verziókövetés számára. Ez azt jelenti, hogy amikor egy helyi functions-projektet klónozott a forrás vezérlőelemből, a projekthez nem tartozik helyi. Settings. JSON fájl. Ebben az esetben manuálisan kell létrehoznia a local. Settings. JSON fájlt a projekt gyökerében, hogy az **alkalmazás beállításai** párbeszédpanel a várt módon működjön. 

Az alkalmazás beállításait az alábbi módokon is kezelheti:

* [A Azure Portal használata](functions-how-to-use-azure-function-app-settings.md#settings).
* A [Azure Functions Core Tools `--publish-local-settings` közzétételi lehetőségének használatával](functions-run-local.md#publish).
* [Az Azure CLI használatával](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Figyelési függvények

A függvények végrehajtásának ajánlott figyelése a Function alkalmazás Azure Application Insights-nal való integrálásával. Ha a Azure Portalban hoz létre egy Function alkalmazást, a rendszer alapértelmezés szerint ezt az integrációt végzi. Ha azonban a Visual Studio Publishing szolgáltatásban hozza létre a Function alkalmazást, az Azure-beli Function alkalmazásban való integráció nem történik meg.

Application Insights engedélyezése a Function alkalmazáshoz:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További információért lásd: [Azure functions figyelése](functions-monitoring.md).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Functions Core Toolsről, tekintse meg a [code and test Azure functions helyileg](functions-run-local.md)című témakört.

Ha többet szeretne megtudni a függvények .NET-es kódtáraként való fejlesztéséről, tekintse meg a [Azure functions C# fejlesztői referenciát](functions-dotnet-class-library.md). A cikk azt is bemutatja, hogyan használhatók az attribútumok a Azure Functions által támogatott különböző típusú kötések deklarálása céljából.    
