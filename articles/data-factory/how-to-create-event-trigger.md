---
title: Eseményvezérelt eseményindítók létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy eseményindítót az Azure Data Factoryben, amely futtatja a folyamatot egy esemény bekövetkeztekor.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: douglasl
ms.openlocfilehash: fdbae70718ad62f96dda85e2d9574c7ec2d5ef23
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561093"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Hozzon létre egy eseményindítót, amely futtatja a folyamatot az adott esemény

Ez a cikk ismerteti az eseményalapú eseményindítókat, amelyek a Data Factory-folyamatok hozhat létre.

Eseményvezérelt architektúra (EDA), amely magában foglalja az éles, észlelési, fogyasztással és események reakciót gyakori adatok integrációs minta. Adatintegrációs forgatókönyvek gyakran igényelnek a Data Factory ügyfelek indítható folyamatok események alapján. A Data Factory mostantól integrálva van az [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), egy eseményt a folyamatok, amellyel indít el.

Egy 10 perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Az ebben a cikkben leírt integráció függ [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Győződjön meg arról, hogy az előfizetése regisztrálva van-e az Event Grid erőforrás-szolgáltatónál. További információ: [erőforrás-szolgáltatókat és típusaikat](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

### <a name="create-a-new-event-trigger"></a>Hozzon létre egy új eseményindító

Egy tipikus esemény egy fájl beérkezés vagy egy fájlt, az Azure Storage-fiókja törlését. Létrehozhat egy eseményindítót, amely ebben a Data Factory-folyamatot az eseményre.

> [!NOTE]
> Ez az integráció csak 2-es verzió tárfiókok (általános célú) támogatja.

![Új eseményindító létrehozása](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Az eseményindító konfigurálása

Az a **Blob elérési útja kezdődik** és **Blob elérési útja végződik** tulajdonságok, megadhatja a tárolókat és mappákat, amelynek a eseményeket fogadni kívánt blob nevének. Számos különböző minták használhatja mindkét **Blob elérési útja kezdődik** és **Blob elérési útja végződik** tulajdonságai, a cikk későbbi részében a példákban szemléltetett módon. Ezek a Tulajdonságok legalább egyikének megadása kötelező.

![Az eseményindító konfigurálása](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Válassza ki az esemény trigger típusa

Amint a tárolási hely érkezik a fájlt, és a megfelelő blob jön létre, ez az esemény aktivál, és a Data Factory-folyamatot futtat. Létrehozhat egy eseményindítót, amely egy blob létrehozása esemény, egy blob törlésének esemény vagy mindkét események, az adat-előállító folyamatok válaszol.

![Eseményindító kiválasztása típusú esemény](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Térkép eseményindító tulajdonságai a folyamat paramétereihez

Egy adott BLOB eseményindító akkor következik be, amikor az esemény kell-e be a tulajdonságokat a mappa elérési útját és nevét a BLOB `@triggerBody().folderPath` és `@triggerBody().fileName`. Ezek a tulajdonságok értékeit a folyamat használatához le kell képeznie a Tulajdonságok folyamat paramétereihez. A Tulajdonságok leképezése paraméterek, után az értékek keresztül az eseményindító által rögzített érheti el a `@pipeline().parameters.parameterName` kifejezés a folyamat során.

![Tulajdonságok hozzárendelése a folyamat paramétereihez](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Ha például az az előző képernyőképen látható. az eseményindító aktiválódik, amikor egy blob elérési útja a végződésű van konfigurálva `.csv` jön létre a Tárfiókot. Eredményként, ha a blob a `.csv` bővítmény bárhol jön létre a Storage-fiókban a `folderPath` és `fileName` tulajdonságok rögzítése az új blob helyét. Például `@triggerBody().folderPath` értéke például `/containername/foldername/nestedfoldername` és `@triggerBody().fileName` értéke például `filename.csv`. Ezek az értékek vannak leképezve a példában a folyamat paramétereihez `sourceFolder` és `sourceFile`. Használhatja őket, a folyamat során `@pipeline().parameters.sourceFolder` és `@pipeline().parameters.sourceFile` jelölik.

## <a name="json-schema"></a>JSON-sémája

Az alábbi táblázat áttekintést az eseményalapú eseményindítókat kapcsolódó séma elemei:

| **JSON-elem** | **Leírás** | **Típus** | **Megengedett értékek** | **Kötelező** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Hatókör** | Az Azure Resource Manager erőforrás-azonosító a Storage-fiók. | String | Az Azure Resource Manager-Azonosítót | Igen |
| **események** | Ez az eseményindító aktiválódik a kiváltó esemény típusa. | Tömb    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Igen, ezekre az értékekre tetszőleges kombinációját. |
| **blobPathBeginsWith** | A blob elérési útja a üzenetszám-trigger megadott mintának kell kezdődnie. Például `/records/blobs/december/` csak akkor következik be, az eseményindító lévő blobok esetében a `december` mappát a `records` tároló. | String   | | Meg kell adnia egy értéket legalább egy ezeket a tulajdonságokat: `blobPathBeginsWith` vagy `blobPathEndsWith`. |
| **blobPathEndsWith** | A blob elérési útja a üzenetszám-trigger megadott mintának kell végződnie. Ha például `december/boxes.csv` csak blobok nevű triggert `boxes` a egy `december` mappát. | String   | | Meg kell adnia egy értéket legalább egy ezeket a tulajdonságokat: `blobPathBeginsWith` vagy `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Eseményalapú eseményindítókat példái

Ez a szakasz példákat eseményalapú eseményindító-beállítások.

> [!IMPORTANT]
> Tartalmaznia kell a `/blobs/` szegmense, amely az elérési út, amikor Ön adja meg a tároló és a mappában, a tároló és a fájl, vagy a tárolót, mappát és fájlt a következő példákban látható módon.

| Tulajdonság | Példa | Leírás |
|---|---|---|
| **BLOB elérési útja kezdődik** | `/containername/` | Fogadja az eseményeket a blobok a tárolóban. |
| **BLOB elérési útja kezdődik** | `/containername/blobs/foldername/` | Fogadja az eseményeket bármely lévő blobok esetében a `containername` tároló és `foldername` mappát. |
| **BLOB elérési útja kezdődik** | `/containername/blobs/foldername/subfoldername/` | Egy almappát is hivatkozhat. |
| **BLOB elérési útja kezdődik** | `/containername/blobs/foldername/file.txt` | Fogadja az eseményeket egy blobhoz nevű `file.txt` a a `foldername` mappát a `containername` tároló. |
| **BLOB elérési útja végződik** | `file.txt` | Fogadja az eseményeket egy blobhoz nevű `file.txt` i bármilyen útvonalat. |
| **BLOB elérési útja végződik** | `/containername/blobs/file.txt` | Fogadja az eseményeket egy blobhoz nevű `file.txt` tárolóban `containername`. |
| **BLOB elérési útja végződik** | `foldername/file.txt` | Fogadja az eseményeket egy blobhoz nevű `file.txt` a `foldername` bármely tárolóban található mappa. |

## <a name="next-steps"></a>További lépések
Részletes információkat lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
