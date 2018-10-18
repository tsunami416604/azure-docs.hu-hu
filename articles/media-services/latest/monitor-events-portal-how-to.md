---
title: Az Event GRID használatával a portálról az Azure Media Services-események monitorozása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet előfizetni az Event Gridbe annak érdekében, hogy az Azure Media Services-események figyelésére.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 4e6527bf115f327635a0b0fe187094dafb320598
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381039"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Hozzon létre, és figyelheti a Media Services-események az Event GRID használatával az Azure portal használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben az Azure Portalon előfizetni események az Azure Media Services-fiók használata. Ezután fogja aktiválni eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Ebben a cikkben egy webalkalmazást, amely összegyűjti és megjeleníti az üzenetek eseményeket küldeni azt.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

## <a name="prerequisites"></a>Előfeltételek 

* Aktív Azure-előfizetéssel rendelkezik.
* Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](create-account-cli-quickstart.md) leírt módon.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Feliratkozás a Media Services-fiók eseményeire, előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben üzembe helyezése egy [előre elkészített webalkalmazás](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely az esemény üzeneteket jelenít meg. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha úgy vált, az "Azure Event Grid megjelenítő" helyhez, láthatja, az események nem még nem.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Fizessen elő a Media Services-események

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. A portálon, válassza ki a Media Services-fiókját, és válassza ki **események**.
1. Ha eseményeket szeretne küldeni a megjelenítő alkalmazásba, használjon egy webhookot a végponthoz. 

   ![Webhook kiválasztása](./media/monitor-events-portal/select-web-hook.png)

1. Az esemény-előfizetés van előre kitöltött értékeket a Media Services-fiókját. 
1. Válassza ki a "Webhook" az a **típusú végpont**.
1. Ebben a témakörben hagyjuk az **fizessen elő az összes eseménytípusra** be van jelölve. Azonban jelölését, és az adott eseménytípusok szűrése. 
1. Kattintson a **válasszon végpontot** hivatkozásra.

    A webhook végponthoz adja meg a webalkalmazás URL-címét, és adja hozzá az `api/updates` elemet a kezdőlap URL-címéhez. 

1. Nyomja meg **kijelölés megerősítéséhez**.
1. Nyomja meg a **Create** (Létrehozás) gombot.
1. Nevezze el az előfizetését.

   ![Naplók kiválasztása](./media/monitor-events-portal/create-subscription.png)

1. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. 

    Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpont rendelkezik beállítása `validationResponse` való `validationCode`. További információkért lásd: [Event Grid biztonsági és hitelesítési](../../event-grid/security-authentication.md). Megtekintheti a webalkalmazás kódját hogyan azt ellenőrzi, hogy az előfizetés megtekintéséhez.

Most aktiváljunk események megtekintéséhez, hogyan osztja el a Event Grid a végpontnak az üzenetet.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiók eseményeire a PowerShell-kódolási feladat futtatásával is indíthat. Követheti [ebben a rövid útmutatóban](stream-files-dotnet-quickstart.md) kódolja a fájlt, és indítsa el az események. Ha az összes esemény előfizetett, egy a következőhöz hasonló képernyő jelenik meg:

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Nem frissíti az oldalt, ha meg szeretné jeleníteni az összes eseményt.

![Előfizetési esemény megtekintése](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>További lépések

[Feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
