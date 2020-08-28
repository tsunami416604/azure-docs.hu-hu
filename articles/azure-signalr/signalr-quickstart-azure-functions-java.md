---
title: Csevegési helyiség létrehozása a Azure Functions és a Signaler szolgáltatással a Java használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre csevegőszobát az Azure SignalR szolgáltatás és az Azure Functions használatával.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 544f200e749b1b125e8077ee65f20a06779fb13d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050573"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Rövid útmutató: csevegési helyiség létrehozása Azure Functions és a Signaler szolgáltatással a Java használatával

Az Azure Signaler szolgáltatással egyszerűen adhat hozzá valós idejű funkciókat az alkalmazáshoz, és Azure Functions egy kiszolgáló nélküli platform, amely lehetővé teszi, hogy az infrastruktúra kezelése nélkül futtassa a kódot. Ebben a rövid útmutatóban a Java segítségével kiszolgáló nélküli, valós idejű csevegési alkalmazást hozhat létre a Signal Service és a functions használatával.

## <a name="prerequisites"></a>Előfeltételek

- Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/)
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Az Azure Function apps helyi futtatására szolgál.

   > [!NOTE]
   > A szükséges szignáló szolgáltatásbeli kötések a javában csak az Azure Function Core Tools 2.4.419 (2.0.12332) vagy újabb verziójában támogatottak.

   > [!NOTE]
   > A bővítmények telepítéséhez Azure Functions Core Tools a [.net Core SDK](https://www.microsoft.com/net/download) telepítésére van szükség. A JavaScript-alapú Azure-függvényalkalmazások létrehozásához azonban nem szükséges a .NET ismerete.

- A [Java Developer Kit](https://www.azul.com/downloads/zulu/) 8-as verziója
- Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója

> [!NOTE]
> Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

    ![SignalR szolgáltatás létrehozása](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. A Kódszerkesztő alkalmazásban nyissa meg a *src/chat/Java* mappát a klónozott tárházban.

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A függvényeket tartalmazó fő fájl az *src/chat/Java/src/Main/Java/com/Function/functions. Java*:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **üzenetküldés** – csevegési üzenetet fogad a kérelem törzsében, és a *signaler* kimeneti kötés használatával közvetíti az üzenetet az összes csatlakoztatott ügyfélalkalmazás számára.

1. A terminálon győződjön meg arról, hogy a *src/chat/Java* mappában található. Hozza létre a Function alkalmazást.

    ```bash
    mvn clean package
    ```

1. Futtassa helyileg a Function alkalmazást.

    ```bash
    mvn azure-functions:run
    ```
[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást készített és futtatott a Maven használatával. Következő lépésként megismerheti, hogyan hozhat létre teljesen új Java-Azure Functions.

> [!div class="nextstepaction"]
> [Az első függvény létrehozása a Java és a Maven használatával](../azure-functions/functions-create-first-java-maven.md)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsjava)
