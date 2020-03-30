---
title: Funkciók csatlakoztatása az Azure Storage szolgáltatáshoz a Visual Studio használatával
description: Megtudhatja, hogyan adhat hozzá kimeneti kötést a C# osztálykönyvtár-függvények egy Azure Storage-várólistához való csatlakoztatásához a Visual Studio használatával.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74849207"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Funkciók csatlakoztatása az Azure Storage szolgáltatáshoz a Visual Studio használatával

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használhatja a Visual Studio segítségével az [előző rövid útmutató cikkben] létrehozott függvényt az Azure Storage-hoz. A függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure Queue storage-várólistában lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a Functions a kötött szolgáltatás eléréséhez használ. A könnyebb, használja a Storage-fiók, amely a függvényalkalmazással létrehozott. A fiókhoz való kapcsolat már a neve `AzureWebJobsStorage`s. alkalmazásbeállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk megkezdése előtt a következőket kell tennie: 

 - A [Visual Studio első részének teljes körű útmutatója](./functions-create-first-function-vs-code.md). 

- Jelentkezzen be Azure-előfizetésébe a Visual Studióból.

## <a name="download-the-function-app-settings"></a>A függvényalkalmazás beállításainak letöltése

Az [előző rövid útmutató cikkben](functions-create-first-function-vs-code.md)létrehozott egy függvényalkalmazást az Azure-ban a szükséges storage-fiókkal együtt. A fiók kapcsolati karakterlánca biztonságosan tárolódik az Azure-beli alkalmazásbeállításokban. Ebben a cikkben üzeneteket ír egy storage-várólistába ugyanabban a fiókban. Ha a funkció helyi futtatásakor szeretne csatlakozni a tárfiókhoz, le kell töltenie az alkalmazásbeállításokat a *local.settings.json* fájlba. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. 

1. A **Műveletek csoportban**válassza **az Azure App Service beállításainak szerkesztése lehetőséget.** 

    ![Az alkalmazás beállításainak szerkesztése](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Az **AzureWebJobsStorage csoportban**másolja a **távoli** karakterlánc értékét a **Helyi**lapra, majd kattintson az **OK gombra.** 

A tárolókötés, amely `AzureWebJobsStorage` a kapcsolat beállítását használja, most már csatlakozhat a várólista-tárolóhoz, ha helyileg fut.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel várólista-tároló kimeneti kötést használ, a projekt futtatása előtt telepítenie kell a Storage kötések bővítményt. A HTTP és az időzítő eseményindítók kivételével a kötések bővítménycsomagokként vannak megvalósítva. 

1. Az **Eszközök menüben** válassza a **NuGet Package Manager** > **Csomagkezelő konzolt**. 

1. A konzolon futtassa a következő [Install-Package](/nuget/tools/ps-ref-install-package) parancsot a Storage-bővítmények telepítéséhez:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Most hozzáadhatja a tárolási kimenetkötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés definiálása után a `name` kötés segítségével attribútumként érheti el a függvényaláírásban. Egy kimeneti kötés használatával nem kell használni az Azure Storage SDK-kódot a hitelesítéshez, a várólista-referencia beszerzése vagy adatok írása. A Functions futásidejű és a várólista kimeneti kötése ezeket a feladatokat elvégezheti.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Egy új `outqueue` nevű várólista jön létre a tárfiókban a Functions futásidejű, amikor a kimeneti kötés először használják. A Cloud Explorer segítségével ellenőrizze, hogy a várólista az új üzenettel együtt jött-e létre.

## <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A **Nézet** menü Visual Studio területén válassza a **Cloud Explorer**parancsot.

1. A **Cloud Explorerben**bontsa ki az Azure-előfizetést és **a tárfiókokat,** majd bontsa ki a függvény által használt tárfiókot. Ha nem emlékszik a tárfiók nevére, ellenőrizze a `AzureWebJobsStorage` kapcsolati karakterlánc beállítását a *local.settings.json* fájlban.  

1. Bontsa ki a **Várólisták csomópontot,** majd kattintson duplán a **várólistára,** hogy megtekinthesse a várólista tartalmát a Visual Studióban. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure*`name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

    ![Várólista-üzenet az Azure Storage Explorer ben](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a funkciót, küldjön egy másik kérelmet, és megjelenik egy új üzenet a várólistában.  

Most itt az ideje, hogy újra közzétegye a frissített függvényalkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újratelepítése és ellenőrzése

1. A **Solution Explorer**alkalmazásban kattintson a jobb gombbal a projektre, és válassza a **Közzététel**parancsot, majd a **Közzététel parancsot** a projekt Azure-ban való újbóli közzétételéhez.

1. A központi telepítés befejezése után ismét használhatja a böngészőt az újraüzembe helyezett függvény teszteléséhez. A hogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez.

1. Ismét [tekintse meg az üzenetet a tárolóvárólistában,](#examine-the-output-queue) és ellenőrizze, hogy a kimeneti kötés ismét új üzenetet hoz-e létre a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-aktivált függvényt, hogy adatokat írjon egy tárolási várólistába. A Funkciók fejlesztéséről az [Azure-funkciók fejlesztése a Visual Studio használatával (Fejleszteni: Azure Functions using Visual Studio ) (Az Azure-funkciók fejlesztése a Visual Studio használatával) témakörben](functions-develop-vs.md)olvashat bővebben.

Ezután engedélyeznie kell az Application Insights figyelését a függvényalkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[előző rövid útmutató cikk]: functions-create-your-first-function-visual-studio.md
