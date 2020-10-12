---
title: Az eszköz konverziós REST API
description: Ismerteti, hogyan lehet átalakítani egy eszközt a REST API használatával
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 5c638b434ceb31b57689b11971f48eb322b94726
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87985614"
---
# <a name="use-the-model-conversion-rest-api"></a>A modellátalakító REST API használata

A [modell-átalakítási](model-conversion.md) szolgáltatást egy [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)vezérli. Ez a cikk a konverziós szolgáltatás API részleteit ismerteti.

## <a name="regions"></a>Régiók

Tekintse meg az [elérhető régiók listáját](../../reference/regions.md) az alapurl-címek számára, hogy elküldje a kérelmeket.

## <a name="common-headers"></a>Gyakori fejlécek

### <a name="common-request-headers"></a>Gyakori kérelmek fejlécei

Az összes kérelemhez meg kell adni ezeket a fejléceket:

- Az **engedélyezési** fejlécben szerepelnie kell a "tulajdonos [*token*]" értéknek, ahol a [*token*] egy [szolgáltatás-hozzáférési jogkivonat](../tokens.md).

### <a name="common-response-headers"></a>Gyakori válaszok fejlécei

Az összes válasz tartalmazza ezeket a fejléceket:

- Az **MS-CV** fejléc egy egyedi karakterláncot tartalmaz, amely a szolgáltatáson belüli hívás nyomkövetésére használható.

## <a name="endpoints"></a>Végpontok

Az átalakítási szolgáltatás három REST API végpontot biztosít a következőhöz:

- Indítsa el a modell átalakítását az Azure távoli renderelési fiókjával társított Storage-fiók használatával. 
- a modell átalakításának elindítása a megadott *megosztott hozzáférési aláírások (SAS)* használatával.
- az átalakítás állapotának lekérdezése

### <a name="start-conversion-using-a-linked-storage-account"></a>Konverzió indítása társított Storage-fiók használatával
Az Azure-beli távoli renderelési fióknak hozzáféréssel kell rendelkeznie a megadott Storage-fiókhoz a Storage- [fiókok összekapcsolásának](../create-an-account.md#link-storage-accounts)lépésein követve.

| Végpont | Módszer |
|-----------|:-----------|
| /v1/accounts/**accountID**/Conversions/Create | POST |

Egy JSON-dokumentumba csomagolt, folyamatban lévő konverzió AZONOSÍTÓját adja vissza. A mező neve "conversionId".

#### <a name="request-body"></a>A kérés törzse

> [!NOTE]
> `input.folderPath`Az Azure-ban történő átalakítás elvégzéséhez a rendszer lekéri az összes lehetőséget. Ha `input.folderPath` nincs megadva, a rendszer lekéri a tároló teljes tartalmát. Az összes lekérdezett blobnak és mappának [érvényes Windows-fájlnevekkel](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions)kell rendelkeznie.

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Konverzió indítása a megadott megosztott hozzáférési aláírásokkal
Ha az ARR-fiók nincs csatolva a Storage-fiókhoz, ez a REST-felület lehetővé teszi, hogy *megosztott hozzáférési aláírásokkal (SAS)* biztosítson hozzáférést.

| Végpont | Módszer |
|-----------|:-----------|
| /v1/accounts/**accountID**/Conversions/createWithSharedAccessSignature | POST |

Egy JSON-dokumentumba csomagolt, folyamatban lévő konverzió AZONOSÍTÓját adja vissza. A mező neve: `conversionId` .

#### <a name="request-body"></a>A kérés törzse

A kérés törzse megegyezik a fenti REST-hívás létrehozásakor, de a bemenet és a kimenet *közös hozzáférésű aláírási (SAS) jogkivonatokat*tartalmaz. Ezek a tokenek hozzáférést biztosítanak a Storage-fiókhoz a bemenet olvasásához és az átalakítás eredményének megírásához.

> [!NOTE]
> Ezek az SAS URI-tokenek a lekérdezési karakterláncok, nem pedig a teljes URI-azonosítók. 

> [!NOTE]
> `input.folderPath`Az Azure-ban történő átalakítás elvégzéséhez a rendszer lekéri az összes lehetőséget. Ha `input.folderPath` nincs megadva, a rendszer lekéri a tároló teljes tartalmát. Az összes lekérdezett blobnak és mappának [érvényes Windows-fájlnevekkel](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions)kell rendelkeznie.

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Lekérdezés konverziójának állapota
A fenti REST-hívások egyikével megkezdett folyamatos konverzió állapota a következő felületen kérdezhető le:


| Végpont | Módszer |
|-----------|:-----------|
| /v1/accounts/**accountID**/Conversions/**conversionId** | GET |

Egy "status" mezőt tartalmazó JSON-dokumentumot ad vissza, amely a következő értékekkel rendelkezhet:

- Létrehozott
- Fut
- Sikeres
- Hiba

Ha az állapot "hiba", akkor egy további "hiba" mező jelenik meg, amely tartalmaz egy "üzenet" almezőt, amely tartalmazza a hiba adatait. A kimeneti tárolóba további naplók lesznek feltöltve.

## <a name="next-steps"></a>Következő lépések

- [Modellek átalakítása az Azure Blob Storage használatával](blob-storage.md)
- [Modell átalakítása](model-conversion.md)
