---
title: Módosított és törölt Blobok
titleSuffix: Azure Cognitive Search
description: Miután egy kezdeti keresési index létrehozta az Azure Blob Storage-ból való importálást, a későbbi indexelés csak azokat a blobokat tudja felvenni, amelyek módosítva vagy törölve lettek. Ez a cikk a részleteket ismerteti.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 210a4a352f5130bc67d70f47bdf6305c5a992282
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403779"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Blobok módosítási és törlési észlelésének beállítása az Azure-ban Cognitive Search indexelés

A kezdeti keresési index létrehozása után érdemes lehet úgy konfigurálni a további indexelő feladatokat, hogy csak a kezdeti Futtatás óta létrehozott vagy törölt dokumentumokat szeretnék kiválasztani. Az Azure Blob Storage-ból származó keresési tartalmak esetén a változások észlelése automatikusan történik, ha az indexelést az ütemterv használatával végzi. Alapértelmezés szerint a szolgáltatás csak a módosított blobokat indexeli, ahogy azt a blob `LastModified` időbélyege határozza meg. A keresési indexek által támogatott egyéb adatforrásokkal ellentétben a Blobok mindig rendelkeznek időbélyeg-val, így nem kell manuálisan beállítani a változás-észlelési szabályzatot.

Bár a változás észlelése egy adott, a törlés észlelése nem. Ha a törölt dokumentumokat szeretné felderíteni, ügyeljen arra, hogy a "Soft Delete" módszert használja. Ha törli a blobokat, a megfelelő dokumentumokat nem távolítja el a rendszer a keresési indexből.

A Soft delete módszert kétféleképpen lehet megvalósítani. Mindkettőről alább olvashat.

## <a name="native-blob-soft-delete-preview"></a>Natív blob – Soft Delete (előzetes verzió)

> [!IMPORTANT]
> A natív Blobok Soft delete támogatása előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2020-06-30-es verziójának előzetes verziója](./search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál vagy .NET SDK-támogatás.

> [!NOTE]
> Ha a natív blob Soft delete szabályzatot használja, az index dokumentumaihoz tartozó dokumentum kulcsainak blob tulajdonságnak vagy blob-metaadatoknak kell lennie.

Ebben a módszerben az Azure Blob Storage által kínált [natív blob-törlési](../storage/blobs/soft-delete-blob-overview.md) funkciót fogja használni. Ha a natív blobos törlés engedélyezve van a Storage-fiókban, az adatforrás natív törlési szabályzattal rendelkezik, és az indexelő olyan blobot talál, amely a törölt állapotba került, az indexelő eltávolítja a dokumentumot az indexből. A Blobok natív törlési szabályzata nem támogatott a Blobok Azure Data Lake Storage Gen2ból való indexelése során.

Ehhez a következő lépések szükségesek:

1. [Az Azure Blob Storage natív törlésének](../storage/blobs/soft-delete-blob-overview.md)engedélyezése. Azt javasoljuk, hogy az adatmegőrzési szabályzatot olyan értékre állítsa be, amely sokkal nagyobb, mint az indexelő intervallumának ütemezett értéke. Így ha probléma merül fel az indexelő futtatásakor, vagy ha nagy számú dokumentumot szeretne indexelni, akkor elég sok idő van arra, hogy az indexelő feldolgozza a puha törölt blobokat. Az Azure Cognitive Search-indexek csak akkor törölnek egy dokumentumot az indexből, ha a blobot feldolgozza, miközben a rendszer helyreállított állapotban van.

1. Natív blobos törlési észlelési házirend konfigurálása az adatforráson. Erre mutat példát az alábbi ábra. Mivel ez a funkció előzetes verzióban érhető el, az előzetes verziójú REST API kell használnia.

1. Az indexelő futtatása vagy az indexelő beállítása ütemezett futtatásra. Amikor az indexelő futtatja és feldolgozza a blobot, a rendszer eltávolítja a dokumentumot az indexből.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Törölt Blobok újraindexelése (natív törlési szabályzatok használatával)

Ha töröl egy blobot az Azure Blob Storage-ból, ha a Storage-fiókban engedélyezve van a natív törlés, akkor a blob egy helyreállítható törölt állapotba kerül, és lehetővé teszi a blob törlését a megőrzési időn belül. Ha az indexelő feldolgozása után visszavon egy törlést, akkor az indexelő nem mindig indexeli a visszaállított blobot. Ennek az az oka, hogy az indexelő meghatározza, hogy mely Blobok legyenek indexelve a blob `LastModified` időbélyege alapján. Ha egy nem törölt blob törölve lett, az `LastModified` időbélyege nem frissül, így ha az indexelő már feldolgozta a blobokat a legutóbbi `LastModified` időbélyegekkel, akkor az nem tudja újraindexelni a nem törölt blobot. 

Annak ellenőrzéséhez, hogy egy törölt blob újraindexelve lett-e, frissítenie kell a blob `LastModified` időbélyegét. Ezt úgy teheti meg, ha átmenti a blob metaadatait. Nem kell módosítania a metaadatokat, de a metaadatok újramentése a blob `LastModified` időbélyegét fogja frissíteni, hogy az indexelő tudja, hogy újra kell indexelni ezt a blobot.

## <a name="soft-delete-using-custom-metadata"></a>Soft delete egyéni metaadatok használatával

Ebben a metódusban egy blob metaadatait fogja használni, amely jelzi, ha egy dokumentumot el kell távolítani a keresési indexből. Ennek a módszernek két külön műveletre van szüksége, és törölni kell a keresési dokumentumot az indexből, majd az Azure Storage-ban a blob törlését.

Ehhez a következő lépések szükségesek:

1. Adjon hozzá egy egyéni metaadat-érték párokat a blobhoz, hogy jelezze az Azure Cognitive Search, hogy logikailag törölve van.

1. Állítsa be az adatforrásra vonatkozó, a törlési oszlop észlelésére szolgáló házirendet. Erre mutat példát az alábbi ábra.

1. Miután az indexelő feldolgozta a blobot, és törölte a dokumentumot az indexből, törölheti a blobot az Azure Blob Storage-ban.

Az alábbi szabályzat például egy olyan blobot tekint, amelyet törölni kell, ha a metaadatok tulajdonsága a következő `IsDeleted` értékkel rendelkezik `true` :

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Törölt Blobok újraindexelése (egyéni metaadatok használatával)

Miután egy indexelő feldolgozza a törölt blobot, és eltávolítja a megfelelő keresési dokumentumot az indexből, nem fogja újra felkeresni a blobot, ha később visszaállítja, ha a blob `LastModified` timestamp régebbi, mint az utolsó indexelő futtatása.

Ha szeretné átindexelni a dokumentumot, módosítsa a `"softDeleteMarkerValue" : "false"` blobot, és futtassa újra az indexelő.

## <a name="see-also"></a>Lásd még

* [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
* [BLOB-indexelő konfigurálása](search-howto-indexing-azure-blob-storage.md)
* [A blob indexelésének áttekintése](search-blob-storage-integration.md)