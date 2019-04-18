---
title: Az Azure SignalR Service kiszolgáló nélküli rövid útmutató – JavaScript
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre csevegőszobát az Azure SignalR szolgáltatás és az Azure Functions használatával.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 0ca73ed314b254f9f73833bb2c4311f03f62508f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264199"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Gyors útmutató: Csevegőszoba létrehozása az Azure Functions és a SignalR Service JavaScript használatával

Az Azure SignalR szolgáltatás használatával egyszerűen adhat hozzá valós idejű funkciókat az alkalmazásához. Az Azure Functions egy kiszolgáló nélküli platform, amellyel infrastruktúra kezelése nélkül futtathat kódokat. Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan készíthet kiszolgáló nélküli, valós idejű csevegőalkalmazást a SignalR szolgáltatás és a Functions használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

Ellenőrizze, hogy van-e telepítve valamilyen kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/).

Telepítse az [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) eszközkészletet az Azure-függvényalkalmazások helyi futtatásához.

A bővítmények telepítéséhez az Azure Functions Core Tools jelenleg igényli, hogy a [.NET Core SDK](https://www.microsoft.com/net/download) telepítve legyen. A JavaScript-alapú Azure-függvényalkalmazások létrehozásához azonban nem szükséges a .NET ismerete.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

    ![SignalR szolgáltatás létrehozása](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. A Kódszerkesztő, nyissa meg a *a javascript/src/Csevegés* a klónozott adattár-mappában.

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A JavaScript-függvények mappákba vannak rendezve. Minden mappában két fájl található: a *function.json* határozza meg a függvényben használt kötéseket, az *index.js* pedig a függvény törzse. Ebben a függvényalkalmazásban két HTTP által indított függvény található:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **messages** – A kéréstörzsben fogadja a csevegés üzenetét, és a *SignalR* kimeneti kötés használatával továbbítja azt az összes csatlakoztatott ügyfélalkalmazás számára.

1. A terminálban ellenőrizze, hogy az a *a javascript/src/Csevegés* mappát. Az Azure Functions Core Tools segítségével telepítse az alkalmazás futtatásához szükséges bővítményeket.

    ```bash
    func extensions install
    ```

1. Futtassa a függvényalkalmazást.

    ```bash
    func start
    ```

    ![SignalR szolgáltatás létrehozása](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott, és valós idejű kiszolgáló nélküli alkalmazás futtatta a VS Code-ban. A következőkben még többet tudhat meg az Azure Functions VS Code-ból történő üzembe helyezéséről.

> [!div class="nextstepaction"]
> [Az Azure Functions üzembe helyezése VS Code-dal](https://code.visualstudio.com/tutorials/functions-extension/getting-started)