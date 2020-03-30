---
title: Az Azure Media Services-események figyelése az Event Grid használatával a CLI használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan lehet feliratkozni az Event Grid az Azure Media Services-események figyelése érdekében.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71937286"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Media Services-események létrehozása és figyelése az Event Grid segítségével az Azure CLI használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ez a szolgáltatás [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) segítségével irányítja az eseményüzeneteket az előfizetőknek. A Media Services-események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. A Media Services-eseményeket azonosítani tudja, mert az eventType tulajdonság "Microsoft.Media" néven kezdődik. További információt a [Media Services eseménysémái című témakörben talál.](media-services-event-schemas.md)

Ebben a cikkben az Azure CLI használatával előfizethet az Azure Media Services-fiók eseményeire. Ezután eseményeket indít az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Ebben a cikkben az eseményeket egy webalkalmazásba küldi, amely összegyűjti és megjeleníti az üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.
- Telepítse és használja a CLI helyileg, ez a cikk megköveteli az Azure CLI 2.0-s vagy újabb verzióját. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI-parancs](https://aka.ms/ams-v3-cli-ref) működik az Azure Cloud Shellben. Javasoljuk, hogy a CLI-t helyileg használja.

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).

    Ügyeljen arra, hogy emlékezzen az erőforráscsoport nevéhez és a Media Services-fiók nevéhez használt értékekre.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Mielőtt előírna a Media Services-fiók eseményeire, hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) telepít, amely megjeleníti az eseményüzeneteket. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha átvált az "Azure Event Grid Viewer" webhelyre, akkor azt még nem láthatja.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Feliratkozás a Media Services eseményeire

Feliratkozik egy cikkre, amely ből megmondja az Event Gridnek, hogy mely eseményeket szeretné nyomon követni. A következő példa előfizet a létrehozott Media Services-fiókra, és továbbítja az URL-címet az eseményértesítés végpontjaként létrehozott webhelyről. 

Cserélje `<event_subscription_name>` le az esemény-előfizetés egyedi nevét. A `<resource_group_name>` `<ams_account_name>`és a, használja a Media Services-fiók létrehozásakor használt értékeket. A `<endpoint_URL>`esetén adja meg a webalkalmazás `api/updates` URL-címét, és adja hozzá a kezdőlap URL-címét. Az előfizetéskor a végpont megadásával az Event Grid kezeli az események végpontra történő továbbítását. 

1. Az erőforrás-azonosító beszereznie

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Példa:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Feliratkozás az eseményekre

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Példa:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Előfordulhat, hogy érvényesítési kézfogásfigyelmeztetést kap. Adj neki pár percet, és a kézfogás nak validálnia kell.

Most váltsuk el az eseményeket, hogy lássuk, hogyan terjeszti az eseményt az eseményrács a végpontra.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiók eseményeit egy kódolási feladat futtatásával indíthatja el. Ezt [a rövid útmutatót](stream-files-dotnet-quickstart.md) követve kódolhat egy fájlt, és elkezdheti az események küldését. 

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpontnak a `validationResponse` `validationCode`beállítására kell, hogy. További információt az [Event Grid biztonsága és hitelesítése](../../event-grid/security-authentication.md)című témakörben talál. Megtekintheti a webalkalmazás kódját, hogy hogyan érvényesíti az előfizetést.

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Ne frissítse a lapot, ha meg szeretné tekinteni az összes eseményt.

![Előfizetési esemény megtekintése](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás és streamelés](stream-files-tutorial-with-api.md)

