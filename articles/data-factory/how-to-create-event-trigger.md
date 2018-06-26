---
title: Eseményalapú eseményindítók létrehozása az Azure Data Factory |} Microsoft Docs
description: Megtudhatja, hogyan lehet indítót létrehozni az Azure Data Factory fut egy folyamat adott esemény bekövetkezésekor.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2018
ms.author: douglasl
ms.openlocfilehash: 2bcb0d4e6af00b56d083690439be45379ce4d175
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752809"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Egy adott esemény bekövetkezésekor egy folyamatot futtató eseményindító létrehozása

Ez a cikk ismerteti a eseményalapú eseményindítókat, amelyek az adat-előállító adatcsatornák hozhatja létre.

Eseményvezérelt architektúra (EDA) olyan közös adatok integrációs mintát, amely magában foglalja a termelési, észlelési, felhasználása és szembeni események. Adatok integrációs feladatokhoz gyakran megkövetelik a Data Factory ügyfelek való folyamatok események alapján.

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

### <a name="create-a-new-event-trigger"></a>Hozzon létre egy új eseményindító

Egy tipikus esemény egy fájl megérkezése, vagy mert törlésre kerültek a fájlt az Azure Storage-fiókban. Létrehozhat egy eseményindítót, hogy válaszol-e ez az esemény a Data Factory-folyamathoz.

> [!NOTE]
> Ez az integráció csak 2-es tárfiókok (általános célú) támogatja.

![Hozzon létre új eseményindító](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Válassza ki az esemény indítási típusa

Amint a tároló helyéhez érkezik a fájlt, és a megfelelő blob jön létre, ez az esemény váltja ki, és futtatja a Data Factory-folyamathoz. Létrehozhat egy eseményindítót, amely egy blob létrehozása esemény, a blob törlése esemény vagy mindkét események, a Data Factory kimenetátirányítási válaszol.

![Esemény válassza eseményindító-típusa](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Az eseményindító konfigurálása

Az a **Blob elérési út kezdődik** és **Blob elérési út végződik** tulajdonságok, megadhatja a tárolók, mappák, és képes eseményeket fogadni szeretné a blob nevének. Mindkét is használhatja a minták számos **Blob elérési út kezdődik** és **Blob elérési út végződik** tulajdonságai, a példákban a cikk későbbi részében látható módon. Ezek a tulajdonságok közül legalább egy megadása kötelező.

![Az eseményindító konfigurálása](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON-séma

Az alábbi táblázat áttekintést a eseményvezérelt kapcsolódó séma elemei:

| **JSON-elem** | **Leírás** | **Típus** | **Megengedett értékek** | **Szükséges** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Hatókör** | Az Azure Resource Manager erőforrás-azonosítója a tárfiók. | Sztring | Az Azure erőforrás-kezelő azonosítója | Igen |
| **Események** | Ehhez az eseményindítóhoz az érvényesítést okozó események típusa. | Tömb    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Igen, tetszőleges kombinációját. |
| **blobPathBeginsWith** | A blob elérési út a mintában az érvényesítést eseményindító kell kezdődnie. Például "/ rekordok/blobok/december /" fog csak érvényesítést a blobok a december mappában található a rekordok tároló eseményindító. | Sztring   | | Ezek a tulajdonságok közül legalább egy meg kell adni: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | A blob elérési út végén szerepelnie kell a mintában az érvényesítést eseményindító. Például "december/boxes.csv" csak érvényesítést az eseményindító a blobok neve mezőbe egy december mappában. | Sztring   | | Ezek a tulajdonságok közül legalább egy meg kell adni: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Eseményalapú eseményindítók példák

Ez a szakasz példákat eseményalapú eseményindító beállításokat.

-   **BLOB elérési út kezdődik**("/ containername /" –) kap események bármely BLOB a tárolóban.
-   **BLOB elérési út kezdődik**("/ containername/Mappanév") – a containername tárolóhoz és mappanév mappa bármely BLOB események fogadása.
-   **BLOB elérési út kezdődik**("/ containername/foldername/file.txt") – a containername tárolóban Mappanév mappában file.txt nevű blob eseményeket fogad.
-   **A BLOB elérési útja végződik**("file.txt") – egy BLOB Receives események nevű file.txt bármely elérési úton.
-   **BLOB elérési út végződik**("/ containername/file.txt") – a tároló containername file.txt nevű blob eseményeket fogad.
-   **A BLOB elérési útja végződik**("foldername/file.txt") – egy BLOB Receives események nevű file.txt Mappanév mappában található a tárolóban.

## <a name="next-steps"></a>További lépések
Eseményindítók kapcsolatos részletes információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
