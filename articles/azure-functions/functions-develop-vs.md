---
title: Fejlesztés az Azure Functions Visual Studio használatával |} A Microsoft Docs
description: Ismerje meg, hogyan fejlesztheti és tesztelheti az Azure Functions az Azure Functions Tools for Visual Studio 2017 használatával.
services: functions
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: glenga
ms.openlocfilehash: 39745991f7ab3b181f892bbaa59283d92737ecf3
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093873"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Fejlesztés az Azure Functions Visual Studio használatával  

Az Azure Functions Tools for Visual Studio 2017, amely lehetővé teszi fejlesztése, tesztelése és üzembe helyezése a C#-függvények az Azure-bA a Visual Studio-bővítmény. Ha ez az első és az Azure Functions, többet is megtudhat a [Bevezetés az Azure Functions](functions-overview.md).

Az Azure Functions Tools az alábbi előnyöket nyújtja: 

* Szerkesztés, elkészítéséhez és függvények a helyi fejlesztői számítógépen való futtatását. 
* Az Azure Functions-projekt közzététele közvetlenül az Azure-bA. 
* Webjobs-feladatok attribútumok használatával közvetlenül a C#-kód helyett egy külön function.json kötési meghatározások fenntartása a függvénykötésnek deklarálható.
* Fejlesztés és üzembe helyezése előre lefordított C#-függvények. Előre lefordított függvények egy jobban hidegindítási teljesítményt biztosítanak, mint a C# parancsfájlalapú funkciók. 
* A függvények C#-kód során a Visual Studio fejlesztési előnyeit a teljes. 

Ez a cikk bemutatja, hogyan fejleszthet a függvények C#-ban az Azure Functions Tools for Visual Studio 2017 használatával. Azt is megtudhatja, hogyan a projekt közzététele az Azure-ba, a .NET-szerelvény.

> [!IMPORTANT]
> Ne keverje a helyi fejlesztési ugyanaz a függvényalkalmazás a portál fejlesztési. Amikor közzétesz egy helyi projektből a függvényalkalmazáshoz, a telepítési folyamat felülírja a portálon fejlesztett funkciók.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions Tools tartalmazza az Azure-fejlesztési számítási [Visual Studio 2017 15.5 verzió](https://www.visualstudio.com/vs/), vagy egy újabb verziója. Győződjön meg arról, hogy a **Azure-fejlesztési** számítási feladatot a Visual Studio 2017 telepítése:

![Az Azure-fejlesztési számítási feladatot is tartalmazó Visual Studio 2017 telepítése](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Győződjön meg arról, hogy naprakész állapotban-e a Visual Studio és az, hogy használja a [legfrissebb](#check-your-tools-version) , az Azure Functions tools.

### <a name="other-requirements"></a>Egyéb követelmények

Hozzon létre és helyezhet üzembe functions, akkor is szüksége lesz:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) érhetők el.

* Egy Azure Storage-fiók. Storage-fiók létrehozásához lásd: [hozzon létre egy tárfiókot](../storage/common/storage-quickstart-create-account.md).

### <a name="check-your-tools-version"></a>Az eszközök verziójának ellenőrzéséhez

1. Az a **eszközök** menüben válassza a **bővítmények és frissítések**. Bontsa ki a **telepített** > **eszközök** válassza **Azure Functions és Webjobs-eszközök**.

    ![A Functions eszközök verziójának ellenőrzése](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Vegye figyelembe a telepített **verzió**. Ez a verzió a legújabb verzióra a felsorolt összehasonlíthatja [a kibocsátási megjegyzésekben](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Ha a verzió régebbi, frissítse az eszközök a Visual Studióban, a következő szakaszban látható módon.

### <a name="update-your-tools"></a>Az eszközök frissítése

1. Az a **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések** > **Visual Studio-piactér**, válassza a **Azure Functions és Webjobs-eszközök**  válassza **frissítés**.

    ![A Functions eszközök verziójának frissítése](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Után az eszközök a frissítés letöltését követően zárja be a Visual Studióban az eszközök frissítése a VSIX telepítővel eseményindítóra.

3. A telepítő válassza **OK** elindítani, majd **módosítás** az eszközök frissítése. 

4. A frissítés befejezése után válassza ki a **Bezárás** , és indítsa újra a Visual Studióban.

## <a name="create-an-azure-functions-project"></a>Az Azure Functions-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

A projekt sablont hoz létre egy C#-projektben, telepíti a `Microsoft.NET.Sdk.Functions` NuGet-csomagot, és beállítja a cílové rozhraní. Függvények 1.x célok a .NET-keretrendszer, és a 2.x célok .NET Standard működik. Az új projekt tartalmaz a következő fájlokat:

* **Host.JSON**: a funkciók gazdagép konfigurálását teszi lehetővé. Ezek a beállítások is alkalmazható, ha helyileg és az Azure-ban futó is. További információkért lásd: [host.json referencia](functions-host-json.md).

* **Local.Settings.JSON**: megőrzi a függvények helyi futtatás során használt beállításokat. Az Azure nem használja ezeket a beállításokat, azokat a [Azure Functions Core Tools](functions-run-local.md). Ez a fájl használatával adja meg a funkciók számára szükséges változókat alkalmazás beállításait. Egy új elem hozzáadása a **értékek** tömb minden egyes kapcsolathoz, a functions-kötéseket a projekt által igényelt. További információkért lásd: [helyi beállításfájl](functions-run-local.md#local-settings-file) az Azure Functions Core Tools cikkben.

További információkért lásd: [funkciók hordozhatóosztálytár-projektjének](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>A helyi fejlesztési projekt konfigurálása

A Functions futtatókörnyezete egy Azure Storage-fiókot használ. Összes vált a HTTP és webhookok eltérő típusú, be kell állítani a **Values.AzureWebJobsStorage** egy érvényes Azure-Tárfiók kapcsolati sztringje kulcs. A függvényalkalmazás is használhatja a [az Azure storage emulator](../storage/common/storage-use-emulator.md) számára a **AzureWebJobsStorage** a projekt által igényelt csatlakozási beállítást. Az emulator használatához állítsa **AzureWebJobsStorage** való `UseDevelopmentStorage=true`. Egy tényleges tárolási kapcsolathoz üzembe helyezés előtt módosítania kell ezt a beállítást.

A tárfiók kapcsolati sztringje beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer**, bontsa ki a **Tárfiók** > **a Storage-fiók**, majd **tulajdonságok**, és másolja a **elsődleges kapcsolati karakterlánc** értéket.

2. A projektben nyissa meg a local.settings.json fájlban, és az értékét állítsa be a **AzureWebJobsStorage** kulcs a kapcsolati karakterlánc másolta.

3. Ismételje meg az előző lépésben az egyedi kulcs hozzáadása a **értékek** bármilyen más kapcsolatot, a functions által igényelt az tömb.

## <a name="create-a-function"></a>Függvény létrehozása

Az előre lefordított függvények a függvény által használt kötéseket határozzák meg a kódban attribútumok alkalmazása. Az Azure Functions Tools használatával a függvények létrehozása a megadott sablonokból, ha ezek az attribútumok érvényesek az Ön számára. 

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Add** (Hozzáadás)  > **New Item** (Új elem) lehetőséget. Válassza ki **Azure-függvény**, adjon meg egy **neve** az osztályt, majd kattintson a **Hozzáadás**.

2. Válassza ki az eseményindító, állítsa be a kötés tulajdonságokat, és kattintson **létrehozás**. Az alábbi példa bemutatja a beállításokat, amikor létrehozása a Queue storage által aktivált függvény. 

    ![Üzenetsor által aktivált függvény létrehozása](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ez az eseményindító például kapcsolati karakterláncot használja nevű kulccsal **QueueStorage**. A kapcsolati karakterlánc beállítása definiálni kell a [local.settings.json fájljában](functions-run-local.md#local-settings-file).

3. Vizsgálja meg az újonnan hozzáadott osztály. Láthatja, hogy a statikus **futtatása** attribútummal módszernek a **FunctionName** attribútum. Ez az attribútum azt jelzi, hogy a metódus a belépési pont, a függvény.

    Az alábbi C# osztály például alapvető üzenetsor által aktivált függvény jelöli:

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
    A kötés-specifikus attribútum alkalmazza a rendszer minden egyes belépési pont metódushoz megadott kötési paraméter. Az attribútum a kötési információ paraméterekként vesz igénybe. Az előző példában az első paramétere rendelkezik egy **QueueTrigger** attribútum a alkalmazni, üzenetsor által aktivált függvény jelzi. A várólista nevét és kapcsolati karakterlánc beállítás neve adhatók be a paramétereket a **QueueTrigger** attribútum. További információkért lásd: [Azure Queue storage – Azure Functions kötések](functions-bindings-storage-queue.md#trigger---c-example).
    
A fenti eljárással további funkciók hozzáadása a függvényalkalmazás projektjét. A projektben egyes funkciót egy másik eseményindító rendelkezhet, de a következő függvénynek rendelkeznie kell a pontosan egy eseményindító. További információkért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Adhat hozzá kötéseket

Eseményindítók, a bemeneti és kimeneti kötései kerülnek a kötelező attribútumok funkciót. Adhat hozzá kötéseket az egy függvényt a következők szerint:

1. Ellenőrizze, hogy [konfigurálva a helyi fejlesztési projekt](#configure-the-project-for-local-development).

2. Adja hozzá a NuGet-bővítmény csomagot az adott kötéshez. További információkért lásd: [helyi C# használatával a Visual Studio fejlesztési](functions-triggers-bindings.md#local-csharp) az eseményindítók és kötések cikkben. A kötés NuGet csomag követelmény a kötési a áttekintésével foglalkozó cikkben találhatók. Például keresse meg a csomag követelményei az Event Hubs eseményindítót a [az Event Hubs kötés áttekintésével foglalkozó cikkben](functions-bindings-event-hubs.md).

3. Ha vannak olyan alkalmazásbeállításokat, amelyek a kötés van szüksége, adja hozzá őket a **értékek** gyűjteményt a [helyi beállítási fájl](functions-run-local.md#local-settings-file). Ezekkel az értékekkel, ha helyileg futtatja a függvényt. A függvényalkalmazáshoz az Azure-ban, a függvény futásakor a [Alkalmazásbeállítások függvény](#function-app-settings) szolgálnak.

4. A metódus aláírásához, adja hozzá a megfelelő kötéselem attribútum. A következő példában egy üzenetsor-üzenetet aktiválja a függvényt, és a kimeneti kötést egy új üzenetsor-üzenetet hoz létre ugyanannak a szövegnek az egy másik várólistához.

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
A Queue Storage-kapcsolat kérhető le a `AzureWebJobsStorage` beállítás. További információkért tekintse meg a áttekintésével foglalkozó cikkben meghatározott kötés. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

A projekt futtatása tesztelheti a kódot, tesztelni kellene üzembe helyezett függvényt. További információkért lásd: [stratégiát a kódot tesztelés az Azure Functions](functions-test-a-function.md). Hibakeresési módban futtatja, töréspontok az elvárt módon vannak találati a Visual Studióban. 

Egy üzenetsor által aktivált függvény tesztelése egy példa: a [üzenetsor által aktivált függvény a rövid útmutató](functions-create-storage-queue-triggered-function.md#test-the-function).  

Az Azure Functions Core Tools használatával kapcsolatos további tudnivalókért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Függvényalkalmazás beállításai

A local.settings.json a hozzáadott minden beállítást is meg kell adni a függvényalkalmazáshoz az Azure-ban. Ezek a beállítások nem lesznek feltöltve automatikusan, a projekt közzétételekor.

Töltse fel a szükséges beállításokat a függvényalkalmazáshoz az Azure-ban a legegyszerűbb módja az, hogy használja a **Alkalmazásbeállítások kezelése...**  hivatkozás, amely a projekt sikeres közzététel után jelenik meg. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Ez megjeleníti a **Alkalmazásbeállítások** párbeszédpanel a függvényalkalmazás, ahol hozzáadhat új Alkalmazásbeállítások vagy módosíthatja a meglévőket.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Alkalmazásbeállítások ezeket más módon is kezelhetők:

* [Az Azure portal használatával](functions-how-to-use-azure-function-app-settings.md#settings).
* [Használatával a `--publish-local-settings` publish beállítást, az az Azure Functions Core Tools](functions-run-local.md#publish).
* [Az Azure CLI-vel](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 

## <a name="next-steps"></a>További lépések

Gyakori kérdések című szakaszában talál további információt az Azure Functions Tools, a [Azure Functions Visual Studio 2017-es eszközei](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogbejegyzést.

Az Azure Functions Core Tools kapcsolatos további információkért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).

Is, mint a .NET-osztálytárak fejlesztésével kapcsolatos további tudnivalókért lásd: [Azure Functions C# – fejlesztői referencia](functions-dotnet-class-library.md). Ez a cikk példákat deklarálásához a kötéseket az Azure Functions támogatja a különböző típusú attribútumok használata is hivatkozik.    
