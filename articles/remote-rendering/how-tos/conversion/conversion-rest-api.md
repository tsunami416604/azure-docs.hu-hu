---
title: Az eszközkonverziós REST API
description: Ez a témakör azt ismerteti, hogy miként konvertálható egy eszköz a REST API-n keresztül
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887044"
---
# <a name="use-the-model-conversion-rest-api"></a>A modellátalakító REST API használata

A [modellkonverziós](model-conversion.md) szolgáltatás egy [REST API-n](https://en.wikipedia.org/wiki/Representational_state_transfer)keresztül történik. Ez a cikk ismerteti a konverziós szolgáltatás API-részleteit.

## <a name="regions"></a>Régiók

Tekintse meg az [elérhető régiók listáját](../../reference/regions.md) az alap URL-címek a kérelmek et küldeni.

## <a name="common-headers"></a>Gyakori fejlécek

### <a name="common-request-headers"></a>Gyakori kérelemfejlécek

Ezeket a fejléceket minden kérelemhez meg kell adni:

- Az **engedélyezési** fejlécnek "Tulajdonos [*TOKEN*]" értékkel kell rendelkeznie, ahol a [*TOKEN*] [szolgáltatás-hozzáférési jogkivonat](../tokens.md).

### <a name="common-response-headers"></a>Gyakori válaszfejlécek

Minden válasz a következő fejléceket tartalmazza:

- Az **MS-CV** fejléc egy egyedi karakterláncot tartalmaz, amely a szolgáltatáson belüli hívás nyomon követésére használható.

## <a name="endpoints"></a>Végpontok

A konverziós szolgáltatás három REST API-végpontot biztosít:

- indítsa el a modellkonverziót az Azure remote rendering fiókjához kapcsolódó tárfiók használatával. 
- indítsa el a modellátalakítást a megadott *megosztott hozzáférési aláírások (SAS)* használatával.
- lekérdezés az átalakítás állapotáról

### <a name="start-conversion-using-a-linked-storage-account"></a>Átalakítás indítása csatolt tárfiókhasználatával
Az Azure távoli leadási fióknak hozzá kell férnie a megadott tárfiókhoz a [tárfiókok összekapcsolása](../create-an-account.md#link-storage-accounts)lépéseinek követésével.

| Végpont | Módszer |
|-----------|:-----------|
| /v1/számlák/**fiókazonosító**/konverziók/létrehozás | POST |

A folyamatban lévő átalakítás json-dokumentumba csomagolt azonosítóját adja eredményül. A mező neve "conversionId".

#### <a name="request-body"></a>A kérés törzse


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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Átalakítás megkezdése a megadott megosztott hozzáférésű aláírások használatával
Ha az ARR-fiók nincs összekapcsolva a tárfiókkal, ez a REST-felület lehetővé teszi a hozzáférést *a megosztott hozzáférésű aláírások (SAS)* használatával.

| Végpont | Módszer |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

A folyamatban lévő átalakítás json-dokumentumba csomagolt azonosítóját adja eredményül. A mező neve "conversionId".

#### <a name="request-body"></a>A kérés törzse

A kérelem törzse megegyezik a fenti REST-hívás létrehozása kor, de a bemeneti és kimeneti bemeneti és *kimeneti bemeneti és kimeneti adatok megosztott hozzáférésű aláírások (SAS) jogkivonatokat tartalmaznak.* Ezek a jogkivonatok hozzáférést biztosítanak a tárfiókhoz a bemenet olvasásához és a konverziós eredmény írásához.

> [!NOTE]
> Ezek a SAS URI-jogkivonatok a lekérdezési karakterláncok, és nem a teljes URI. 


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

### <a name="poll-conversion-status"></a>Szavazás konverziós állapota
A fenti REST-hívások egyikével indított folyamatos átalakítás állapota a következő felületen kérdezhető le:


| Végpont | Módszer |
|-----------|:-----------|
| /v1/számlák/**számlaazonosító**/konverziók/**konverzióAzonosító** | GET |

Olyan JSON-dokumentumot ad eredményül, amelynek "állapot" mezője a következő értékeket tartalmazhatja:

- "Futás"
- "Siker"
- "Hiba"

Ha az állapot "Hiba", akkor egy további "hiba" mező lesz, amely egy "üzenet" almezőt tartalmaz, amely hibainformációkat tartalmaz. További naplók lesznek feltöltve a kimeneti tárolóba.

## <a name="next-steps"></a>További lépések

- [Modellek átalakítása az Azure Blob Storage használatával](blob-storage.md)
- [Modell átalakítása](model-conversion.md)
