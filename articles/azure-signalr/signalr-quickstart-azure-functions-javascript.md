---
title: Az Azure Functions és signalr szolgáltatással rendelkező csevegőszoba létrehozása javascripttel
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre csevegőszobát az Azure SignalR szolgáltatás és az Azure Functions használatával.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.openlocfilehash: 2726d5da2613be4ae2065246543d206cf814f353
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083196"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Rövid útmutató: A JavaScript használatával csevegőszobát hozhat létre az Azure Functions és signalr szolgáltatás segítségével

Az Azure SignalR szolgáltatás lehetővé teszi, hogy egyszerűen valós idejű funkciókat adjon az alkalmazáshoz, és az Azure Functions egy kiszolgáló nélküli platform, amely lehetővé teszi a kód futtatását infrastruktúra kezelése nélkül. Ebben a rövid útmutatóban a JavaScript segítségével kiszolgáló nélküli, valós idejű csevegőalkalmazást hozhat létre a SignalR szolgáltatás és a Funkciók használatával.

## <a name="prerequisites"></a>Előfeltételek

- Kódszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), 2-es vagy újabb verzió. Az Azure Function-alkalmazások helyi futtatásához használható.
- [Node.js](https://nodejs.org/en/download/), 10.x verzió

   > [!NOTE]
   > A példák nak együtt kell működniük a Node.js más verzióival, további információért tekintse meg az [Azure Functions futásidejű verzióinak dokumentációját.](../azure-functions/functions-versions.md#languages)

> [!NOTE]
> Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

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

1. A kódszerkesztőben nyissa meg az *src/chat/javascript* mappát a klónozott tárházban.

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A JavaScript-függvények mappákba vannak rendezve. Minden mappában két fájl található: a *function.json* határozza meg a függvényben használt kötéseket, az *index.js* pedig a függvény törzse. Ebben a függvényalkalmazásban két HTTP által indított függvény található:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **messages** – A kéréstörzsben fogadja a csevegés üzenetét, és a *SignalR* kimeneti kötés használatával továbbítja azt az összes csatlakoztatott ügyfélalkalmazás számára.

1. A terminálon győződjön meg arról, hogy az *src/chat/javascript* mappában van. Futtassa a függvényalkalmazást.

    ```bash
    func start
    ```

    ![SignalR szolgáltatás létrehozása](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást épített és futtatott a VS Code-ban. A következőkben még többet tudhat meg az Azure Functions VS Code-ból történő üzembe helyezéséről.

> [!div class="nextstepaction"]
> [Az Azure Functions üzembe helyezése VS Code-dal](/azure/javascript/tutorial-vscode-serverless-node-01)
