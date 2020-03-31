---
title: Az Azure Functions és SignalR szolgáltatással csevegőszoba létrehozása a Java használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre csevegőszobát az Azure SignalR szolgáltatás és az Azure Functions használatával.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083199"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Rövid útmutató: A Java használatával csevegőszobát hozhat létre az Azure Functions és a SignalR szolgáltatás segítségével

Az Azure SignalR szolgáltatás lehetővé teszi, hogy egyszerűen valós idejű funkciókat adjon az alkalmazáshoz, és az Azure Functions egy kiszolgáló nélküli platform, amely lehetővé teszi a kód futtatását infrastruktúra kezelése nélkül. Ebben a rövid útmutatóban a Java segítségével hozhat létre egy kiszolgáló nélküli, valós idejű csevegőalkalmazást a SignalR szolgáltatás és funkciók használatával.

## <a name="prerequisites"></a>Előfeltételek

- Kódszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Az Azure Functions alapvető eszközei](https://github.com/Azure/azure-functions-core-tools#installing). Az Azure Function-alkalmazások helyi futtatásához használható.

   > [!NOTE]
   > A szükséges SignalR-szolgáltatás kötéseit Java-ban csak az Azure Function Core Tools 2.4.419-es (2.0.12332-es vagy újabb) verziója támogatja.

   > [!NOTE]
   > Bővítmények telepítéséhez az Azure Functions Core Tools a [.NET Core SDK-t](https://www.microsoft.com/net/download) igényli. A JavaScript-alapú Azure-függvényalkalmazások létrehozásához azonban nem szükséges a .NET ismerete.

- A [Java Developer Kit](https://www.azul.com/downloads/zulu/) 8-as verziója
- Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója

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

1. A kódszerkesztőben nyissa meg az *src/chat/java* mappát a klónozott tárházban.

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A funkciókat tartalmazó fő fájl *az src/chat/java/src/main/java/com/function/Functions.java:*

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **sendMessage** - Csevegési üzenetet fogad a kérelemtörzsben, és a *SignalR* kimeneti kötéssel közvetíti az üzenetet az összes csatlakoztatott ügyfélalkalmazásnak.

1. A terminálon győződjön meg arról, hogy az *src/chat/java* mappában van. A függvényalkalmazás létrehozása.

    ```bash
    mvn clean package
    ```

1. Futtassa a függvényalkalmazást helyileg.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást épített és futtatott a Maven használatával. Ezután ismerje meg, hogyan hozhat létre java Azure-függvényeket a semmiből.

> [!div class="nextstepaction"]
> [Az első funkció létrehozása Java és Maven segítségével](../azure-functions/functions-create-first-java-maven.md)