---
title: Azure Blob storage-események átirányítása egy egyéni webes végpontjának |} Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: f0764ebc423cfb5323f2b634ce5a5ecbe075135c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-azure-cli"></a>A Blob storage-események átirányítása egy egyéni webkiszolgáló-végpont Azure parancssori felülettel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben előfizetünk a Blob Storage-eseményekre az Azure CLI-vel, majd elindítjuk az eseményt az eredmény megtekintéséhez. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. A cikkben bemutatott példa egyszerűsítése érdekében az eseményeket egy olyan URL-címnek küldjük el, amely pusztán üzenetek gyűjtésével foglalkozik. Az URL-címet a [Hookbin](https://hookbin.com/) külső eszközével fogjuk létrehozni.

> [!NOTE]
> **Hookbin** nem célja a magas teljesítmény-használatról. Az eszköz jelen használata kizárólag bemutató célt szolgál. Ha egyszerre több eseményt továbbít, lehetséges, hogy az eszközben nem fog megjelenni az összes esemény.

A cikkben leírt lépések elvégzése után látni fogja, hogy az eseményadatokat egy végpontnak küldte el a rendszer.

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI legújabb verzióját (2.0.24 vagy újabb) kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Ha nem a Cloud Shellt használja, először be kell jelentkeznie a(z) `az login` használatával.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. 

A következő példában létrehozunk egy `<resource_group_name>` nevű erőforráscsoportot a *westcentralus* helyen.  A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-storage-account"></a>Create a storage account

A Blob storage-események használatához szüksége vagy egy [Blob storage-fiók](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) vagy egy [általános célú v2 tárfiókkal](../common/storage-account-options.md#general-purpose-v2). **Általános célú v2 (GPv2)** minden funkció támogatása az összes tárolószolgáltatásokra, köztük a blobokat, fájlok, üzenetsorok és táblák storage-fiókok vannak. A **Blob storage-fiók** egy speciális tárfiók a strukturálatlan adatok blobként (objektumokként) az Azure Storage való tárolására. BLOB storage-fiókok hasonló általános célú tárfiókok, és minden nagy tartósságot, rendelkezésre állási, méretezhetőség és teljesítmény szolgáltatás használata ma beleértve a 100 %-os API-konzisztenciát a blokkblobokhoz, és a hozzáfűző blobokhoz. A csak blokkok és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát. 

> [!NOTE]
> Rendelkezésre állás a Storage-események kötődik esemény rács [rendelkezésre állási](../../event-grid/overview.md) és más régiókban is elérhető lesz a esemény rács hasonlóan.

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

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Az eseményre reagáló kód írása helyett egy olyan végpontot hozzunk létre, amely gyűjti az üzeneteket, hogy meg tudja őket tekinteni. A Hookbin egy külső eszköz, amely lehetővé teszi egy végpont létrehozását és a neki küldött kérések megtekintését. Nyissa meg a [Hookbin](https://hookbin.com/) eszközt, és kattintson a **Create New Endpoint** (Új végpont létrehozása) elemre.  Másolja ki a tár URL-címét, mert szüksége lesz rá, amikor feliratkozik a témakörre.

## <a name="subscribe-to-your-storage-account"></a>A storage-fiók előfizetés

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. A következő példa a tárfiókot hozott létre, és az URL-cím továbbítja a Hookbin és a végpontnak a eseményértesítés számítógépcsoportra fizetett elő. Az `<event_subscription_name>` elemet az esemény-feliratkozás egyedi nevére cserélje le, az `<endpoint_URL>` elemet pedig az előző szakaszból származó értékre. Ha megadja a végpontot a feliratkozáskor, az Event Grid az adott végpontra irányítja az eseményeket. A `<resource_group_name>` és `<storage_account_name>` elemnél a korábban létrehozott értékeket adja meg.  

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="trigger-an-event-from-blob-storage"></a>Esemény kiváltása a Blob Storage-ból

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először hozzuk nevének konfigurálása és a storage-fiók kulcsát ezután azt létrehozni egy tárolót, majd hozzon létre és -fájl feltöltése. Ismét a korábban létrehozott `<storage_account_name>` és `<resource_group_name>` értéket használja.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Lépjen a végpont korábban létrehozott URL-címére, vagy kattintson a megnyitott böngésző frissítés gombjára. Megjelenik az imént elküldött esemény. 

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
