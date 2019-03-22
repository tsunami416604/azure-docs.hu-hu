---
title: Az Event GRID használatával CLI-vel az Azure Media Services-események monitorozása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet előfizetni az Event Gridbe annak érdekében, hogy az Azure Media Services-események figyelésére.
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
ms.openlocfilehash: f6243bbc21466361aed7cbb7193f3a7b7c7e539f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885016"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Hozzon létre, és az Event GRID használatával az Azure CLI-vel a Media Services-események figyelése

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ez a szolgáltatás használ [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára. Media Services-események összes kell reagálni az igények változásaira az adatokban adatokat tartalmazzák. A Media Services esemény is azonosítani, mert az esemény típusa tulajdonság "Microsoft.Media." karakterlánccal kezdődik. További információkért lásd: [Media Services Eseménysémák](media-services-event-schemas.md).

Ebben a cikkben az Azure CLI előfizetni események az Azure Media Services-fiók használata. Ezután fogja aktiválni eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Ez a cikk az eseményeket küldeni egy webalkalmazást, amely összegyűjti és megjeleníti az üzenetek.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

    Ellenőrizze, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Feliratkozás a Media Services-fiók eseményeire, előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben üzembe helyezése egy [előre elkészített webalkalmazás](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely az esemény üzeneteket jelenít meg. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha úgy vált, az "Azure Event Grid megjelenítő" helyhez, láthatja, az események nem még nem.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Fizessen elő a Media Services-események

Event Grid megállapítani, hogy mely eseményeket kívánja nyomon követni egy cikk fizet. Az alábbi példa feliratkozik a létrehozott, és az URL-címet a webhelyen létrehozott az eseményértesítés végpontjaként adja át a Media Services-fiók. 

Cserélje le `<event_subscription_name>` az esemény-feliratkozás egyedi nevére. A `<resource_group_name>` és `<ams_account_name>`, használja a Media Services-fiók létrehozásakor használt értékeket. Az a `<endpoint_URL>`, adja meg a webalkalmazás URL-CÍMÉT, és adja hozzá `api/updates` a kezdőlap URL-címre. Adja meg a végpont, amikor feliratkozik, az Event Grid kezeli irányítja az eseményeket, hogy a végpont. 

1. Az erőforrás-azonosító beszerzése

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Példa:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Fizessen elő az események

    ```azurecli
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Példa:

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Érvényesítési kézfogás figyelmeztetést kaphat. Adjon meg néhány percet, és a kézfogás ellenőrizni kell.

Most aktiváljunk események megtekintéséhez, hogyan osztja el a Event Grid a végpontnak az üzenetet.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiók eseményeire a PowerShell-kódolási feladat futtatásával is indíthat. Követheti [ebben a rövid útmutatóban](stream-files-dotnet-quickstart.md) kódolja a fájlt, és indítsa el az események. 

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpont rendelkezik beállítása `validationResponse` való `validationCode`. További információkért lásd: [Event Grid biztonsági és hitelesítési](../../event-grid/security-authentication.md). Megtekintheti a webalkalmazás kódját hogyan azt ellenőrzi, hogy az előfizetés megtekintéséhez.

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Nem frissíti az oldalt, ha meg szeretné jeleníteni az összes eseményt.

![Előfizetési esemény megtekintése](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>További lépések

[Feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)

