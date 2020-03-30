---
title: Azure-függvények fejlesztése a Visual Studióval
description: Ismerje meg, hogyan fejleszthet és tesztelhet Azure Functions-funkciókat a Visual Studio 2019-hez.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277101"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Azure-függvények fejlesztése a Visual Studióval  

A Visual Studio lehetővé teszi a C# osztályú könyvtárfunkciók fejlesztését, tesztelését és üzembe helyezését az Azure-ban. Ha ez az élmény az első az Azure Functions szolgáltatással, további információ az [Azure Functions bevezető című](functions-overview.md)része.

A Visual Studio a következő előnyöket nyújtja a funkciók fejlesztése során: 

* A helyi fejlesztői számítógépen szerkesztheti, felépítheti és futtathatja a függvényeket. 
* Tegye közzé Az Azure Functions projektet közvetlenül az Azure-ban, és szükség szerint hozzon létre Azure-erőforrásokat. 
* C# attribútumok használatával közvetlenül a C# kódban deklarálja a függvénykötéseket.
* Előre lefordított C# függvények fejlesztése és üzembe helyezése. Az előre betartott függvények jobb hidegindítási teljesítményt biztosítanak, mint a C# parancsfájlalapú függvények. 
* A Funkciókat C# nyelven kódolhatja, miközben a Visual Studio fejlesztésének minden előnyét élvezheti. 

Ez a cikk részletesen ismerteti, hogyan használhatja a Visual Studio c# osztálykönyvtár-függvények fejlesztésére és közzétételére az Azure-ban. A cikk elolvasása előtt el kell végeznie a [Visual Studio Függvények rövid útmutatóját.](functions-create-your-first-function-visual-studio.md) 

Eltérő rendelkezés hiányában a visual Studio 2019-hez tartozik az eljárások és a példák. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions Tools a Visual Studio 2017-es részétől kezdődően a Visual Studio Azure-fejlesztési munkaterhelése része. Győződjön meg arról, hogy az **Azure fejlesztési** számítási feladatait is beilleszti a Visual Studio telepítésébe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A közzétételi folyamat során más erőforrások, például egy Azure Storage-fiók jönnek létre az előfizetésben.

> [!NOTE]
> A Visual Studio 2017-ben az Azure fejlesztési számítási feladatok külön bővítményként telepíti az Azure Functions Tools-t. A Visual Studio 2017 frissítésekén ellenőrizze azt is, hogy az Azure Functions eszközök [legújabb verzióját](#check-your-tools-version) használja-e. Az alábbi szakaszok bemutatják, hogyan ellenőrizheti és (szükség esetén) frissítheti az Azure Functions Tools bővítményt a Visual Studio 2017-ben. 
>
> A Visual Studio 2019 használatakor hagyja ki ezeket a szakaszokat.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Az eszközverzió ellenőrzése a Visual Studio 2017-ben

1. Az **Eszközök** menüben válassza a **Bővítmények és frissítések parancsot.** **Bontsa** > ki a Telepített**eszközök** csomópontot, és válassza az Azure Functions and Web Jobs **Tools (Webes feladatok) lehetőséget.**

    ![A Functions eszközök verziójának ellenőrzése](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Megjegyzés: a telepített **verzió**. Ezt a verziót összehasonlíthatja a [kiadási megjegyzésekben](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)felsorolt legújabb verzióval. 

1. Ha a verzió régebbi, frissítse az eszközöket a Visual Studióban a következő szakaszban látható módon.

### <a name="update-your-tools-in-visual-studio-2017"></a>Eszközök frissítése a Visual Studio 2017-ben

1. A Bővítmények és frissítések párbeszédpanelen **bontsa** ki a **Frissítések** > **a Visual Studio piactérre,** válassza az Azure Functions és a Web Jobs Tools **elemet,** és válassza a **Frissítés**lehetőséget.

    ![A Functions eszközök verziójának frissítése](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Az eszközök frissítésének letöltése után zárja be a Visual Studio alkalmazást, és indítsa el az eszközfrissítést a VSIX telepítővel.

1. A telepítőben válassza az **OK** gombot az indításhoz, majd a **Módosítás lehetőséget** az eszközök frissítéséhez. 

1. A frissítés befejezése után válassza a **Bezárás** és a Visual Studio újraindítása lehetőséget.

> [!NOTE]  
> A Visual Studio 2019-es és újabb verzióiban az Azure Functions eszközbővítmény a Visual Studio részeként frissül.  

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

A projektsablon létrehoz egy C# `Microsoft.NET.Sdk.Functions` projektet, telepíti a NuGet csomagot, és beállítja a célkeretrendszert. Az új projekt a következő fájlokat:

* **host.json**: Lehetővé teszi a Functions állomás konfigurálását. Ezek a beállítások helyi és azure-beli futtatáskor is érvényesek. További információ: [host.json reference](functions-host-json.md).

* **local.settings.json**: A függvények helyi futtatásakor használt beállítások karbantartása. Ezek a beállítások nem használatosak az Azure-ban való futtatáskor. További információt a [Helyi beállítások fájl című témakörben](#local-settings-file)talál.

    >[!IMPORTANT]
    >Mivel a local.settings.json fájl titkos kulcsokat tartalmazhat, ki kell zárnia azt a projektforrás-vezérlőből. A fájl **másolása kimenetre könyvtárba** beállításnak mindig másolásnak kell **lennie, ha újabb**. 

További információt a [Functions osztálytárprojekt című témakörben](functions-dotnet-class-library.md#functions-class-library-project)talál.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

A local.settings.json beállításai nem töltődnek fel automatikusan a projekt közzétételekor. Győződjön meg arról, hogy ezek a beállítások is léteznek a függvényalkalmazásban az Azure-ban, fel kell töltenie őket a projekt közzététele után. További információ: [Funkcióalkalmazás-beállítások](#function-app-settings).

A **ConnectionStrings értékei** soha nem kerülnek közzétételre.

A függvényalkalmazás beállításai értékek is olvasható a kódban, mint a környezeti változók. További információt a Környezeti változók című [témakörben talál.](functions-dotnet-class-library.md#environment-variables)

## <a name="configure-the-project-for-local-development"></a>A projekt konfigurálása helyi fejlesztéshez

A Functions futásidejű egy Azure Storage-fiókot használ belsőleg. A HTTP-n és webhookokon kívüli összes eseményindító-típushoz be kell állítania a **Values.AzureWebJobsStorage** kulcsot egy érvényes Azure Storage-fiók kapcsolati karakterláncra. A függvényalkalmazás is használhatja az [Azure storage-emulátor](../storage/common/storage-use-emulator.md) az **AzureWebJobsStorage** kapcsolat beállítás, amely a projekt által megkövetelt. Az emulátor használatához állítsa az **AzureWebJobsStorage** értékét a értékére. `UseDevelopmentStorage=true` Módosítsa ezt a beállítást egy tényleges tárfiók-kapcsolati karakterláncra a telepítés előtt.

A tárfiók kapcsolati karakterláncának beállítása:

1. A Visual Studio nyissa meg a **Cloud Explorer**alkalmazást, **bontsa** > ki a Tárfiók**a tárfiók csomópontot,** majd a **Tulajdonságok** lapon másolja az **Elsődleges kapcsolati karakterlánc** értékét.

2. A projektben nyissa meg a local.settings.json fájlt, és állítsa be az **AzureWebJobsStorage** kulcs értékét a másolt kapcsolati karakterlánchoz.

3. Ismételje meg az előző lépést, ha egyedi kulcsokat szeretne hozzáadni a **Függvények** által igényelt egyéb kapcsolatok értékek tömbjéhez. 

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

A C# osztálykönyvtár-függvényekben a függvény által használt kötések a kódban lévő attribútumok alkalmazásával vannak definiálva. Amikor létrehozza a függvény eseményindítók a megadott sablonok, az eseményindító attribútumok vonatkoznak az Ön számára. 

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Add** (Hozzáadás)  > **New Item** (Új elem) lehetőséget. Válassza az **Azure Függvény lehetőséget,** írja be az osztály **nevét,** majd kattintson a **Hozzáadás gombra.**

2. Válassza ki az eseményindítót, állítsa be a kötési tulajdonságokat, majd kattintson a **Létrehozás gombra.** A következő példa bemutatja a beállításokat, amikor létrehoz egy várólista-tároló aktivált függvényt. 

    ![Várólista által aktivált függvény létrehozása](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ez az eseményindító példa egy **QueueStorage**nevű kulccsal rendelkező kapcsolati karakterláncot használ. Ezt a kapcsolati karakterlánc-beállítást a [local.settings.json fájlban](functions-run-local.md#local-settings-file)kell definiálni.

3. Vizsgálja meg az újonnan hozzáadott osztályt. Megjelenik egy statikus **Futtatás** metódus, amely a **FunctionName** attribútummal van hozzárendelve. Ez az attribútum azt jelzi, hogy a metódus a függvény belépési pontja.

    A következő C# osztály például egy alapvárólista-tároló aktivált függvényt jelöl:

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

    A program a belépési pont módszeréhez megadott minden egyes kötési paraméterre kötésspecifikus attribútumot alkalmaz. Az attribútum a kötési információt paraméterként veszi fel. Az előző példában az első paraméterhez **egy QueueTrigger** attribútum van alkalmazva, amely a várólista aktivált függvényét jelzi. A várólista név és a kapcsolati karakterlánc beállításának neve paraméterként kerül átadásra az **QueueTrigger** attribútumnak. További információ: [Azure Queue storage bindings for Azure Functions.](functions-bindings-storage-queue-trigger.md)

A fenti eljárással további függvényeket adhat hozzá a függvényalkalmazás-projekthez. A projekt minden függvényének lehet egy másik eseményindítója, de egy függvénynek pontosan egy eseményindítóval kell rendelkeznie. További információ: [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Kötések hozzáadása

Az eseményindítókhoz is a bemeneti és kimeneti kötések kötési attribútumként kerülnek a függvénybe. Kötések hozzáadása egy függvényhez az alábbiak szerint:

1. Győződjön meg arról, hogy [a projektet helyi fejlesztésre konfigurálta.](#configure-the-project-for-local-development)

2. Adja hozzá a megfelelő NuGet bővítménycsomagot az adott kötéshez. További információ: [Helyi C# fejlesztés a Visual Studio használatával](./functions-bindings-register.md#local-csharp) az eseményindítók és a kötések cikkben. A kötés-specifikus NuGet csomag követelmények találhatók a referencia cikkben a kötés. Például keresse meg az Event Hubs eseményindító csomagkövetelményeit az [Event Hubs kötési referenciacikkben.](functions-bindings-event-hubs.md)

3. Ha vannak olyan alkalmazásbeállítások, amelyeket a kötésnek szüksége van, adja hozzá őket a helyi beállításfájl **Értékek** [gyűjteményéhez.](functions-run-local.md#local-settings-file) Ezek az értékek akkor használatosak, amikor a függvény helyileg fut. Amikor a függvény fut a függvényalkalmazásban az Azure-ban, a [függvény alkalmazás beállításait](#function-app-settings) használja.

4. Adja hozzá a megfelelő kötési attribútumot a metódus aláírásához. A következő példában egy várólista-üzenet váltja ki a funkciót, és a kimeneti kötés létrehoz egy új várólista-üzenetet ugyanazzal a szöveggel egy másik várólistában.

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
   A várólista-tárolóval való `AzureWebJobsStorage` kapcsolat a beállításból származik. További információt az adott kötésreferencia-cikkében talál. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

A projekt futásával tesztelheti a kódot, ahogy az üzembe helyezett függvényt tesztelné. További információ: [Strategies for testing your code in Azure Functions](functions-test-a-function.md). Hibakeresési módban való futtatáskor a program a várt módon lenyomja a töréspontokat a Visual Studióban. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Ha többet szeretne tudni az Azure Functions alapvető eszközeinek használatáról, olvassa el az [Azure-függvények helyi kódolása és tesztelése című témakört.](functions-run-local.md)

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Visual Studio-ból történő közzétételsorán a következő két telepítési módszer egyikét használja:

* [Webtelepítés:](functions-deployment-technologies.md#web-deploy-msdeploy)windowsos alkalmazásokat csomagol és telepít bármely IIS-kiszolgálóra.
* [Zip-telepítés a Run-From-Package engedélyezve:](functions-deployment-technologies.md#zip-deploy)ajánlott az Azure Functions központi telepítések.

Az alábbi lépésekkel teheti közzé a projektet egy függvényalkalmazásban az Azure-ban.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>A függvényalkalmazás beállításai

A local.settings.json ban hozzáadott beállításokat is hozzá kell adni a függvényalkalmazáshoz az Azure-ban. Ezek a beállítások nem töltődnek fel automatikusan a projekt közzétételekor.

A legegyszerűbb módja annak, hogy feltölti a szükséges beállításokat a függvényalkalmazásaz Azure-ban, hogy használja az **alkalmazásbeállítások kezelése ...** linket, amely megjelenik, miután sikeresen közzétette a projektet.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Ez megjeleníti a függvényalkalmazás **alkalmazásbeállításai** párbeszédpanelt, ahol új alkalmazásbeállításokat adhat hozzá, vagy módosíthatja a meglévőket.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**A helyi** beállítási értéket jelöl a local.settings.json fájlban, és a **Remote** az Azure-beli függvényalkalmazás aktuális beállítása.  Új alkalmazásbeállítás létrehozásához válassza a **Beállítás hozzáadása** lehetőséget. A **Beszúrás a Helyi hivatkozásból érték** kelendő a **Távoli** mezőbe. A függőben lévő módosítások a helyi beállításfájlba és a függvényalkalmazásba kerülnek, ha az **OK**lehetőséget választja.

> [!NOTE]
> Alapértelmezés szerint a local.settings.json fájl nincs bevéve a forrásvezérlőbe. Ez azt jelenti, hogy ha egy helyi Functions projektet forrásvezérlőből klónoz, a projektnem rendelkezik local.settings.json fájllal. Ebben az esetben manuálisan kell létrehoznia a local.settings.json fájlt a projekt gyökérében, hogy az **Alkalmazásbeállítások** párbeszédpanel a várt módon működjön. 

Az alkalmazásbeállításokat az alábbi módokon is kezelheti:

* [Az Azure Portal használata](functions-how-to-use-azure-function-app-settings.md#settings).
* [A `--publish-local-settings` közzétételi lehetőség használata az Azure Functions Core Tools eszközben.](functions-run-local.md#publish)
* [Az Azure CLI használatával.](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)

## <a name="monitoring-functions"></a>Felügyeleti funkciók

A függvények végrehajtásának figyelésének ajánlott módja a függvényalkalmazás integrálása az Azure Application Insightsalkalmazással. Amikor létrehoz egy függvényalkalmazást az Azure Portalon, ez az integráció alapértelmezés szerint elvégezhető. Azonban amikor létrehozza a függvényalkalmazást a Visual Studio közzététele során, a függvényalkalmazásba való integráció az Azure-ban nem történik meg.

Az Application Insights engedélyezése a függvényalkalmazáshoz:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További információ: [Monitor Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Functions alapvető eszközeiről, olvassa el az [Azure-függvények helyi kódolása és tesztelése című témakört.](functions-run-local.md)

Ha többet szeretne tudni a .NET osztálytárak ként való függvények fejlesztéséről, olvassa el az [Azure Functions C# fejlesztői útmutató című témakört.](functions-dotnet-class-library.md) Ez a cikk is hivatkozik példákat, hogyan használatával attribútumok deklarálni a különböző típusú kötések által támogatott Azure Functions.    
