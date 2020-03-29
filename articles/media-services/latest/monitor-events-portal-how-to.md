---
title: Az Azure Media Services-események figyelése az Event Grid del portállal
description: Ez a cikk bemutatja, hogyan lehet feliratkozni az Event Grid az Azure Media Services-események figyelése érdekében.
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
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509223"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Media Services-események létrehozása és monitorozása az Event Griddel az Azure Portalon

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ez a szolgáltatás [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) segítségével irányítja az eseményüzeneteket az előfizetőknek. A Media Services-események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. A Media Services-eseményeket azonosítani tudja, mert az eventType tulajdonság "Microsoft.Media" néven kezdődik. További információt a [Media Services eseménysémái című témakörben talál.](media-services-event-schemas.md)

Ebben a cikkben az Azure Portal használatával előfizethet az Azure Media Services-fiók eseményeire. Ezután eseményeket indít az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikkben eseményeket küldünk egy webalkalmazásnak, amely összegyűjti és megjeleníti az üzeneteket.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

## <a name="prerequisites"></a>Előfeltételek 

* Aktív Azure-előfizetéssel rendelkezik.
* Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](create-account-cli-quickstart.md) leírt módon.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Mielőtt előírna a Media Services-fiók eseményeire, hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) telepít, amely megjeleníti az eseményüzeneteket. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha átvált az "Azure Event Grid Viewer" webhelyre, akkor azt még nem láthatja.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Feliratkozás a Media Services eseményeire

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. A portálon válassza ki a Media Services-fiókot, és válassza **az Események**lehetőséget.
1. Ha eseményeket szeretne küldeni a megjelenítő alkalmazásba, használjon egy webhookot a végponthoz. 

   ![Webhook kiválasztása](./media/monitor-events-portal/select-web-hook.png)

1. Az esemény-előfizetés előre ki van töltve a Media Services-fiók értékeivel. 
1. Válassza a "Web Hook" lehetőséget a **végponttípushoz.**
1. Ebben a témakörben hagyjuk a **Feliratkozás az összes eseménytípust** ellenőrizni. Azonban törölje a jelet, és szűrje az adott eseménytípusokat. 
1. Kattintson a **Végpont kiválasztása** hivatkozásra.

    A webhook végponthoz adja meg a webalkalmazás URL-címét, és adja hozzá az `api/updates` elemet a kezdőlap URL-címéhez. 

1. Nyomja **meg a Kijelölés megerősítése gombot.**
1. Nyomja meg a **Create** (Létrehozás) gombot.
1. Nevezze el az előfizetését.

   ![Naplók kiválasztása](./media/monitor-events-portal/create-subscription.png)

1. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. 

    Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpontnak a `validationResponse` `validationCode`beállítására kell, hogy. További információt az [Event Grid biztonsága és hitelesítése](../../event-grid/security-authentication.md)című témakörben talál. Megtekintheti a webalkalmazás kódját, hogy hogyan érvényesíti az előfizetést.

Most váltsuk el az eseményeket, hogy lássuk, hogyan terjeszti az eseményt az eseményrács a végpontra.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiók eseményeit egy kódolási feladat futtatásával indíthatja el. Ezt [a rövid útmutatót](stream-files-dotnet-quickstart.md) követve kódolhat egy fájlt, és elkezdheti az események küldését. Ha minden eseményre feliratkozott, a következőhöz hasonló képernyő jelenik meg:

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Ne frissítse a lapot, ha meg szeretné tekinteni az összes eseményt.

![Előfizetési esemény megtekintése](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás és streamelés](stream-files-tutorial-with-api.md)
