---
title: "Azure Blob Storage-események átirányítása egyéni webes végpontra (előzetes verzió) | Microsoft Docs"
description: "Az Azure Event Griddel előfizethet Blob Storage-eseményekre."
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/19/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 50a6126f065b1b4d851f53b5cb3096c130314450
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Azure Blob Storage-események átirányítása egyéni webes végpontra (előzetes verzió)

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben előfizetünk a Blob Storage-eseményekre az Azure CLI-vel, majd elindítjuk az eseményt az eredmény megtekintéséhez. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. A cikkben bemutatott példa egyszerűsítése érdekében az eseményeket egy olyan URL-címnek küldjük el, amely pusztán üzenetek gyűjtésével foglalkozik. Az URL-cím nevű nyílt forráskódú, külső eszköz használatával létrehozhat [RequestBin](https://requestb.in/).

> [!NOTE]
> **RequestBin** nyílt forráskódú eszköz, amely nem célja a magas teljesítmény-használatról. Az eszköz használata ebben az esetben kizárólag bemutató célt szolgál. Ha egyszerre több eseményt továbbít, lehetséges, hogy az eszközben nem fog megjelenni az összes esemény.

A cikkben leírt lépések elvégzése után látni fogja, hogy az eseményadatokat egy végpontnak küldte el a rendszer.

![Eseményadatok](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e a legújabb Azure CLI (2.0.24 vagy újabb). A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Ha nem a Cloud Shellt használja, először be kell jelentkeznie a(z) `az login` használatával.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. 

A következő példában létrehozunk egy `<resource_group_name>` nevű erőforráscsoportot a *westcentralus* helyen.  A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>Blob Storage-fiók létrehozása

Az Azure Storage használatához tárfiókra van szüksége.  A Blob Storage-események jelenleg csak Blob Storage-fiókokban érhetők el.

A Blob Storage-fiók egy speciális tárfiók a strukturálatlan adatok blobként (objektumként) való tárolására az Azure Storage-ban. A Blob Storage-fiókok hasonlóak a meglévő általános célú tárfiókjaihoz, és a jelenlegi rendszereivel megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtanak, beleértve a 100%-os API-konzisztenciát a blokkblobokhoz és a hozzáfűző blobokhoz. A csak blokkok és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát.

> [!NOTE]
> Esemény rács jelenleg előzetes, és csak a tárfiókok esetén érhető el a **westcentralus** és **westus2** régiók.

A `<storage_account_name>` elemet cserélje le az erőforráscsoport egyedi nevére, a `<resource_group_name>` elemet pedig a korábban létrehozott erőforráscsoportra.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Mielőtt eseményekre iratkozna fel a Blob Storage-fiókból, hozzuk létre az eseményüzenetek végpontját. Az eseményre reagáló kód írása helyett egy olyan végpontot hozunk létre, amely gyűjti az üzeneteket, hogy meg tudja őket tekinteni. RequestBin egy nyílt forráskódú, külső eszköz, amely lehetővé teszi, hogy hozzon létre egy végpontot, és a hozzá küldött kérelmekben megtekintése. Nyissa meg a [RequestBin](https://requestb.in/) eszközt, és kattintson a **Create a RequestBin** (Kéréstár létrehozása) elemre.  Másolja ki a tár URL-címét, mert szüksége lesz rá, amikor feliratkozik a témakörre.

## <a name="subscribe-to-your-blob-storage-account"></a>Feliratkozás a Blob Storage-fiókra

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. Az alábbi példa feliratkozik a létrehozott Blob Storage-fiókra, és az eseményértesítés végpontjaként adja át a RequestBinből átemelt URL-címet. Az `<event_subscription_name>` elemet az esemény-feliratkozás egyedi nevére cserélje le, az `<URL_from_RequestBin>` elemet pedig az előző szakaszból származó értékre. Ha megadja a végpontot a feliratkozáskor, az Event Grid az adott végpontra irányítja az eseményeket. A `<resource_group_name>` és `<storage_account_name>` elemnél a korábban létrehozott értékeket adja meg. 

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <URL_from_RequestBin>
```

## <a name="trigger-an-event-from-blob-storage"></a>Esemény kiváltása a Blob Storage-ból

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először hozzuk nevének konfigurálása és a storage-fiók kulcsát, majd azt létrehozni egy tárolót, majd hozhat létre, és -fájl feltöltése. Ismét a korábban létrehozott `<storage_account_name>` és `<resource_group_name>` értéket használja.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Lépjen a RequestBin eszközben korábban létrehozott URL-címre, vagy kattintson a megnyitott RequestBin-böngésző frissítés elemére. Megjelenik az imént elküldött esemény. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy folytatja a tárfiók és az esemény előfizetésének használatát, akkor ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

A `<resource_group_name>` elemet cserélje le a fent létrehozott erőforráscsoportra.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthatnak segítséget a Blob Storage-események és az Event Grid:

- [Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)
- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
