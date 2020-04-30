---
title: 'Azure Signaler szolgáltatás kiszolgáló nélküli rövid útmutatója – C #'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre csevegőszobát az Azure SignalR szolgáltatás és az Azure Functions használatával.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 75d9977546c2a085765310a5654897f739a271ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "65595392"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Gyors útmutató: csevegési helyiség létrehozása Azure Functions és a Signaler szolgáltatással C használatával\#

Az Azure SignalR szolgáltatás használatával egyszerűen adhat hozzá valós idejű funkciókat az alkalmazásához. Az Azure Functions egy kiszolgáló nélküli platform, amellyel infrastruktúra kezelése nélkül futtathat kódokat. Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan készíthet kiszolgáló nélküli, valós idejű csevegőalkalmazást a SignalR szolgáltatás és a Functions használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

Ezt az oktatóanyagot a parancssorban (macOS, Windows vagy Linux) is futtathatja a [Azure functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing), a [.net Core SDK](https://dotnet.microsoft.com/download)és a kedvenc Kódszerkesztő használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. Indítsa el a Visual studiót (vagy egy másik Kódszerkesztő), és nyissa meg a megoldást a klónozott adattár *src/chat/csharp* mappájában.

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

1. A Visual Studio Megoldáskezelőjében nevezze át a *local.settings.sample.json* fájlt a következőre: *local.settings.json*.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. Nyissa meg a **Functions.cs** alkalmazást. Ebben a függvényalkalmazásban két HTTP által indított függvény található:

    - **GetSignalRInfo** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaadásához.
    - **SendMessage** – A kéréstörzsben fogadja a csevegés üzenetét, és a *SignalR* kimeneti kötés használatával továbbítja azt az összes csatlakoztatott ügyfélalkalmazás számára.

1. Az Azure Function app helyi elindításához használja az alábbi lehetőségek egyikét.

    - **Visual Studio**: a *hibakeresés* menüben válassza a *hibakeresés elindítása* lehetőséget az alkalmazás futtatásához.

        ![Az alkalmazás hibakeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Parancssor**: futtassa a következő parancsot a függvény gazdagépének elindításához.

        ```bash
        func start
        ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást készített és futtatott a Visual Studióban. A következő lépésként tudjon meg többet az Azure-függvények Visual Studióval való fejlesztéséről és üzembe helyezéséről.

> [!div class="nextstepaction"]
> [Azure-függvények fejlesztése a Visual Studióval](../azure-functions/functions-develop-vs.md)