---
title: "Visual Studio használatával Azure Functions kidolgozása |} Microsoft Docs"
description: "Megtudhatja, hogyan fejlesztéséhez és teszteléséhez az Azure Functions által az Azure Functions Tools for Visual Studio 2017 használatával."
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: glenga
ms.openlocfilehash: 1f0d50331aa3986e80542902fd528f98e1c91bc2
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="azure-functions-tools-for-visual-studio"></a>Az Azure Functions Tools for Visual Studio  

Az Azure funkciók Tools for Visual Studio 2017 bővítménye, amely lehetővé teszi a fejlesztés, tesztelése és C# funkciók telepítse az Azure Visual Studio. Ha ez az első tapasztalattal az Azure Functions, többet is megtudhat a [megismerkedhet az Azure Functions](functions-overview.md).

Az Azure Functions eszközök a következő előnyöket nyújtja: 

* Szerkesztés, elkészítéséhez és funkciók a helyi fejlesztési számítógépen futtassa. 
* Az Azure Functions projekt közzététele közvetlenül az Azure-bA. 
* WebJobs-attribútumok segítségével közvetlenül a C#-kódban helyett egy külön function.json a definíciók kötési fenntartása a függvénykötés deklarálható.
* Fejlesztésekor, és előre lefordított függvények C# telepítésekor. Előre lefordított függvények teljesítményt lehet biztosítani a jobban cold indítási mint C# parancsfájlalapú funkciók. 
* A funkciók a C# kód a Visual Studio fejlesztői előnyeit mindegyikével közben. 

Ez a témakör bemutatja, hogyan a Azure Functions Tools for Visual Studio 2017 segítségével a C# funkciók fejlesztése. Azt is megtudhatja, hogyan a projekt közzététele az Azure-ba, mint a .NET-szerelvény.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions eszközök tartalmazza az Azure fejlesztési munkaterhelését [Visual Studio 2017 verzió 15.4](https://www.visualstudio.com/vs/), vagy újabb verziója. Győződjön meg arról, a **Azure fejlesztési** a Visual Studio 2017 telepítési munkaterhelés:

![Az Azure-fejlesztési számítási feladatot is tartalmazó Visual Studio 2017 telepítése](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Hozzon létre, és funkciók telepítése, akkor is szüksége lesz:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [fiókok szabad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) érhetők el.

* Egy Azure Storage-fiókot. A storage-fiók létrehozásához lásd: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account).  
## <a name="create-an-azure-functions-project"></a>Az Azure Functions projekt létrehozása 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>A helyi fejlesztési projekt konfigurálása

Amikor létrehoz egy új projektet az Azure Functions sablonnal, kap egy üres C# projekt, amely a következő fájlokat tartalmazza:

* **Host.JSON**: lehetővé teszi a funkciók gazdagép konfigurálását. Ezeket a beállításokat is alkalmazza, ha fut a helyi és az Azure-ban is. További információkért lásd: [host.json hivatkozás](functions-host-json.md).
    
* **Local.Settings.JSON**: funkciók a helyi futtatás során használt beállításokat tárolja. Ezek a beállítások nem használhatók az Azure-ban, azokat a [Azure Functions Core eszközök](functions-run-local.md). Ez a fájl használatával adja meg a beállításokat, például más Azure-szolgáltatásokhoz való kapcsolódási karakterláncokat. Adja hozzá egy új kulccsal, hogy a **értékek** tömb minden egyes funkciók a projekt által igényelt kapcsolathoz. További információkért lásd: [helyi beállításfájl](functions-run-local.md#local-settings-file) az Azure Functions Core eszközök a témakörben.

A Functions futtatókörnyezete belső egy Azure Storage-fiókot használja. Összes indítás típusú HTTP- és webhookokkal, be kell állítani a **Values.AzureWebJobsStorage** kulcs egy érvényes Azure Storage-fiók kapcsolati karakterláncot.

[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

 A tárolási fiók kapcsolati karakterlánc beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer**, bontsa ki a **Tárfiók** > **a Tárfiók**, majd jelölje be **tulajdonságok**, és másolja a **elsődleges kapcsolódási karakterlánc** érték.   

2. A projektben nyissa meg a local.settings.json projektfájlt, és állítsa a **AzureWebJobsStorage** kulcs a kapcsolati karakterlánc módosításait másolta.

3. Az előző lépésben egyedi kulccsal hozzáadásához ismételje meg a **értékek** bármely más, a funkciók által igényelt kapcsolatok tömb.  

## <a name="create-a-function"></a>Függvény létrehozása

Az előre lefordított függvények a függvény által használt kötéseket határozzák meg a kódban attribútumok alkalmazása. A függvény a megadott felügyeleticsomag-sablonok létrehozásához használhatja az Azure Functions eszközök, ezek az attribútumok meg válnak érvényessé. 

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Add** (Hozzáadás)  > **New Item** (Új elem) lehetőséget. Válassza ki **Azure-függvény**, adjon meg egy **neve** az osztályt, majd kattintson a **Hozzáadás**.

2. Válassza ki az eseményindító, állítsa be a kötési tulajdonságok, majd kattintson **létrehozása**. A következő példa bemutatja a beállításokat, kiváltásakor a várólista-tároló létrehozása funkciót. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    Nevű kapcsolati karakterlánc kulcs **QueueStorage** van megadva, amely a local.settings.json fájlban van megadva. 
 
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
 
    Minden kötelező paraméter, a belépési pont metódus számára megadott alkalmazva van egy kötése-specifikus attribútum. Az attribútum a kötési információ paraméterekként vesz igénybe. Az előző példában az első paraméter tartozik egy **QueueTrigger** attribútuma, várólista indított függvény jelző. A várólista nevét és a kapcsolati karakterlánc Beállításnév paraméterként.  

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.  

A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez.  Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

A projekt fut tesztelheti a kódot, érdemes tesztelni telepített függvény. További információkért lásd: [a kódot az Azure Functions tesztelése kapcsolatos olyan stratégiák](functions-test-a-function.md). Hibakeresési módban fut, töréspontok az elvárt módon vannak elérte a Visual Studio. 

A várólista indított függvény tesztelése példáért lásd: a [indított várólista függvény gyors üzembe helyezési útmutató](functions-create-storage-queue-triggered-function.md#test-the-function).  

Az Azure Functions Core eszközök használatával kapcsolatos további tudnivalókért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

>[!NOTE]  
>Minden hozzáadott a local.settings.json beállítást is meg kell adni a függvény alkalmazásba az Azure-ban. Ezek a beállítások nem kerülnek be automatikusan. Kötelező beállítások adhat hozzá az függvény alkalmazásban az alábbi módszerek valamelyikével:
>
>* [Az Azure portál használatával](functions-how-to-use-azure-function-app-settings.md#settings).
>* [Használja a `--publish-local-settings` publish beállítást, az Azure Functions Core eszközök](functions-run-local.md#publish).
>* [Az Azure parancssori felület használatával](/cli/azure/functionapp/config/appsettings#set). 

## <a name="next-steps"></a>Következő lépések

További információ az Azure Functions eszközök című rész a gyakori kérdéseket a [Azure Functions Visual Studio 2017 eszközök](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogbejegyzést.

Az Azure Functions alapvető eszközökkel kapcsolatos további tudnivalókért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).  
A függvények .NET-osztálytárakként való fejlesztéséről további információért lásd [a .NET-osztálytárak és az Azure Functions használatát](functions-dotnet-class-library.md) ismertető cikket. Ez a témakör is példákat attribútumok használata a különféle az Azure Functions által támogatott kötések deklarálnia.    
