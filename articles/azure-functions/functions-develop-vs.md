---
title: Visual Studio használatával Azure Functions kidolgozása |} Microsoft Docs
description: Megtudhatja, hogyan fejlesztéséhez és teszteléséhez az Azure Functions által az Azure Functions Tools for Visual Studio 2017 használatával.
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: glenga
ms.openlocfilehash: bc280e9b4ade8a4fb8107ec2dcc1c33f538472e1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081560"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Visual Studio használatával Azure Functions fejlesztése  

Az Azure funkciók Tools for Visual Studio 2017 bővítménye, amely lehetővé teszi a fejlesztés, tesztelése és C# funkciók telepítse az Azure Visual Studio. Ha a forgatókönyvben az első az Azure Functions, többet is megtudhat a [megismerkedhet az Azure Functions](functions-overview.md).

Az Azure Functions eszközök a következő előnyöket nyújtja: 

* Szerkesztés, elkészítéséhez és funkciók a helyi fejlesztési számítógépen futtassa. 
* Az Azure Functions projekt közzététele közvetlenül az Azure-bA. 
* WebJobs-attribútumok segítségével közvetlenül a C#-kódban helyett egy külön function.json a definíciók kötési fenntartása a függvénykötés deklarálható.
* Fejlesztésekor, és előre lefordított függvények C# telepítésekor. Előre lefordított függvények teljesítményt lehet biztosítani a jobban cold indítási mint C# parancsfájlalapú funkciók. 
* A funkciók a C# kód a Visual Studio fejlesztői előnyeit mindegyikével közben. 

Ez a cikk bemutatja, hogyan használja a Azure Functions Tools for Visual Studio 2017 a C# funkciók fejlesztése. Azt is megtudhatja, hogyan a projekt közzététele az Azure-ba, mint a .NET-szerelvény.

> [!IMPORTANT]
> A függvény ugyanazt az alkalmazást a portál fejlesztési helyi fejlesztési ne keverje. Ha közzéteszi a helyi projektből függvény alkalmazásokhoz, a telepítési folyamat felülírja a függvényeket, a portál kifejlesztő.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions eszközök tartalmazza az Azure fejlesztési munkaterhelését [Visual Studio 2017 verzió 15.5](https://www.visualstudio.com/vs/), vagy újabb verziója. Győződjön meg arról, a **Azure fejlesztési** a Visual Studio 2017 telepítési munkaterhelés:

![Az Azure-fejlesztési számítási feladatot is tartalmazó Visual Studio 2017 telepítése](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Győződjön meg arról, hogy a Visual Studio naprakész, és hogy használja a [legújabb verziója](#check-your-tools-version) az Azure Functions eszközök.

### <a name="other-requirements"></a>Egyéb követelmények

Hozzon létre, és funkciók telepítése, akkor is szüksége lesz:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [fiókok szabad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) érhetők el.

* Egy Azure Storage-fiók. A storage-fiók létrehozásához lásd: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account).

### <a name="check-your-tools-version"></a>Az eszközök verziójának ellenőrzése

1. Az a **eszközök** menüben válasszon **bővítmények és frissítések**. Bontsa ki a **telepített** > **eszközök** válassza **Azure Functions és webes feladatok eszközök**.

    ![A funkciók eszközök verziójának ellenőrzése](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Vegye figyelembe a telepített **verzió**. Összehasonlíthatja a felsorolt a legújabb verziót [a kibocsátási megjegyzésekben](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. A verzió telepítve, ha frissíteni a Visual Studio eszközök a következő szakaszban látható.

### <a name="update-your-tools"></a>Az eszköz frissítése

1. Az a **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések** > **Visual Studio piactér**, válassza a **Azure Functions és webes feladatok eszközök**  válassza **frissítés**.

    ![A funkciók eszközök verziója](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Után az eszközök a frissítés letöltését követően zárja be a Visual Studio elindítani az eszközök frissítése a VSIX installer használatával.

3. A telepítő válassza **OK** elindításához, majd **módosítás** frissítése az eszközöket. 

4. A frissítés befejezése után válassza ki a **Bezárás** , és indítsa újra a Visual Studio.

## <a name="create-an-azure-functions-project"></a>Az Azure Functions projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

A projekt sablont hoz létre egy C#-projektet, telepíti a `Microsoft.NET.Sdk.Functions` NuGet-csomagot, és beállítja a megcélzott keretrendszer. 1.x célokat a .NET-keretrendszer működik, és a 2.x célok .NET-szabvány funkciókkal. Az új projekt rendelkezik a következő fájlokat:

* **Host.JSON**: lehetővé teszi a funkciók gazdagép konfigurálását. Ezeket a beállításokat is alkalmazza, ha fut a helyi és az Azure-ban is. További információkért lásd: [host.json hivatkozás](functions-host-json.md).

* **Local.Settings.JSON**: funkciók a helyi futtatás során használt beállításokat tárolja. Ezek a beállítások nem használhatók az Azure-ban, azokat a [Azure Functions Core eszközök](functions-run-local.md). A funkciók által igényelt változók app beállításához használja ezt a fájlt. Egy új elem hozzáadása a **értékek** tömb minden egyes kapcsolathoz szükséges a projekt funkciók kötéseket. További információkért lásd: [helyi beállításfájl](functions-run-local.md#local-settings-file) az Azure Functions Core eszközök cikkben.

További információkért lásd: [Functions hordozhatóosztálytár-projektjének](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>A helyi fejlesztési projekt konfigurálása

A Functions futtatókörnyezete belső egy Azure Storage-fiókot használja. Összes indítás típusú HTTP- és webhookokkal, be kell állítani a **Values.AzureWebJobsStorage** kulcs egy érvényes Azure Storage-fiók kapcsolati karakterláncot. A függvény alkalmazást is használhatja a [az Azure storage emulator](../storage/common/storage-use-emulator.md) a a **AzureWebJobsStorage** a projekt kapcsolódási beállítást igényel. Az emulátor, állítsa az értékét **AzureWebJobsStorage** való `UseDevelopmentStorage=true`. Ez a beállítás egy tényleges tárolási kapcsolathoz központi telepítés előtt módosítania kell.

A tárolási fiók kapcsolati karakterlánc beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer**, bontsa ki a **Tárfiók** > **a Tárfiók**, majd jelölje be **tulajdonságok**, és másolja a **elsődleges kapcsolódási karakterlánc** érték.

2. A projektben nyissa meg a local.settings.json fájlt, és állítsa a **AzureWebJobsStorage** kulcs a kapcsolati karakterlánc módosításait másolta.

3. Az előző lépésben egyedi kulccsal hozzáadásához ismételje meg a **értékek** bármely más, a funkciók által igényelt kapcsolatok tömb.

## <a name="create-a-function"></a>Függvény létrehozása

Az előre lefordított függvények a függvény által használt kötéseket határozzák meg a kódban attribútumok alkalmazása. A függvény a megadott felügyeleticsomag-sablonok létrehozásához használhatja az Azure Functions eszközök, ezek az attribútumok meg válnak érvényessé. 

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Add** (Hozzáadás)  > **New Item** (Új elem) lehetőséget. Válassza ki **Azure-függvény**, adjon meg egy **neve** az osztályt, majd kattintson a **Hozzáadás**.

2. Válassza ki az eseményindító, állítsa be a kötési tulajdonságok, majd kattintson **létrehozása**. A következő példa bemutatja a beállításokat, kiváltásakor a várólista-tároló létrehozása funkciót. 

    ![Várólista indított függvény létrehozása](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    A trigger példában egy kapcsolati karakterláncot nevű kulccsal **QueueStorage**. A kapcsolati karakterlánc beállítása definiálni kell a [local.settings.json fájl](functions-run-local.md#local-settings-file).

3. Vizsgálja meg az újonnan hozzáadott osztály. Megjelenik a statikus **futtatása** van módszerhez a **függvénynév** attribútum. Ez az attribútum azt jelzi, hogy a metódus a belépési pont, a függvény.

    A következő C# osztály például egy alapszintű várólista indított tárolási függvény jelöli:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ````
    Minden kötelező paraméter, a belépési pont metódus számára megadott alkalmazva van egy kötése-specifikus attribútum. Az attribútum a kötési információ paraméterekként vesz igénybe. Az előző példában az első paraméter tartozik egy **QueueTrigger** attribútuma, várólista indított függvény jelző. A várólista nevét és a kapcsolati karakterlánc Beállításnév paraméterként való a **QueueTrigger** attribútum.
    
A fenti eljárással további funkciók hozzáadása a függvény app projekthez. A projekt mindkét funkciót rendelkezhet egy másik eseményindítót, de a következő függvénynek rendelkeznie kell pontosan egy eseményindító. További információkért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Kötések hozzáadása

Bemeneti és kimeneti kötések, eseményindítók, mint a függvény kötési attribútumok kerülnek. Kötéseket adjon hozzá egy olyan függvényt az alábbiak szerint:

1. Győződjön meg arról, hogy [konfigurálva a projekt helyi fejlesztési](#configure-the-project-for-local-development).

2. Adja hozzá a NuGet-bővítmény csomag a megadott kötés. További információkért lásd: [helyi C# fejlesztési Visual Studio használatával](functions-triggers-bindings.md#local-csharp) az eseményindítók és kötések cikkben. A kötés NuGet csomag követelmény is vonatkozik a kötés a áttekintésével foglalkozó cikkben találhatók. Például a csomag követelményei az Event Hubs eseményindítót található a [Event Hubs kötés áttekintésével foglalkozó cikkben](functions-bindings-event-hubs.md).

3. Ha a kötés igénylő alkalmazás-beállítások, hozzáadhatja őket a a **értékek** gyűjtemény a [helyi beállítási fájl](functions-run-local.md#local-settings-file). Ezeket az értékeket használják, amikor helyileg futtat a függvény. A függvény futásakor az Azure, a függvény alkalmazásban a [Alkalmazásbeállítások működéséhez](#function-app-settings) szolgálnak.

4. Adja hozzá a megfelelő kötési attribútumot a metódus aláírásában. A következő példában egy üzenetsor elindítja a függvény, és a kimeneti kötést hoz létre egy új sor üzenetet ugyanannak a szövegnek az egy másik várólistához.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            TraceWriter log)
        {
            log.Info($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
A kapcsolat a Queue storage származik a `AzureWebJobsStorage` beállítást. További információkért lásd: a áttekintésével foglalkozó cikkben a meghatározott kötés. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

A projekt fut tesztelheti a kódot, érdemes tesztelni telepített függvény. További információkért lásd: [a kódot az Azure Functions tesztelése kapcsolatos olyan stratégiák](functions-test-a-function.md). Hibakeresési módban fut, töréspontok az elvárt módon vannak elérte a Visual Studio. 

A várólista indított függvény tesztelése példáért lásd: a [indított várólista függvény gyors üzembe helyezési útmutató](functions-create-storage-queue-triggered-function.md#test-the-function).  

Az Azure Functions Core eszközök használatával kapcsolatos további tudnivalókért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Függvényalkalmazás beállításai

Minden hozzáadott a local.settings.json beállítást is meg kell adni a függvény alkalmazásba az Azure-ban. Ezek a beállítások nem feltöltése automatikusan a projekt közzétételekor.

Töltse fel a szükséges beállításokat a függvény-alkalmazásokhoz az Azure-ban legegyszerűbb módja a használja a **Alkalmazásbeállítások kezelése...**  sikeresen közzéteszi a projekt megjelenő hivatkozás. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Ez megjeleníti a **Alkalmazásbeállítások** párbeszédpanelen, a függvény alkalmazásra, ahol új alkalmazás beállításokat adhat meg, vagy módosíthatja a meglévőket.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Alkalmazásbeállítások ezeket más módon is kezelheti:

* [Az Azure portál használatával](functions-how-to-use-azure-function-app-settings.md#settings).
* [Használja a `--publish-local-settings` publish beállítást, az Azure Functions Core eszközök](functions-run-local.md#publish).
* [Az Azure parancssori felület használatával](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). 

## <a name="next-steps"></a>További lépések

További információ az Azure Functions eszközök című rész a gyakori kérdéseket a [Azure Functions Visual Studio 2017 eszközök](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogbejegyzést.

Az Azure Functions alapvető eszközökkel kapcsolatos további tudnivalókért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).

Alkalmazás feladatok fejlesztésével kapcsolatos további tudnivalókért lásd: [Azure Functions C# fejlesztői leírás](functions-dotnet-class-library.md). Ez a cikk is példák attribútumok segítségével deklarálhatja kötések Azure Functions által támogatott különböző típusú hivatkozásokat tartalmaz.    
