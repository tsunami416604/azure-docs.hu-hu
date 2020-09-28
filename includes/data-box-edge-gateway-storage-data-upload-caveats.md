---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401100"
---
A következő kikötések az Azure-ba való váltáskor érvényesek az adatvédelemre.

- Javasoljuk, hogy egynél több eszköz ne írjon ugyanahhoz a tárolóhoz.
- Ha van egy meglévő Azure-objektuma (például egy blob vagy egy fájl) a felhőben az éppen másolt objektum nevével, az eszköz felülírja a fájlt a felhőben.
- A megosztási mappák alatt létrehozott üres címtár-hierarchiát (fájlok nélkül) a blob-tárolók nem töltötték fel.
- Az Adatmásolás a Fájlkezelőben vagy a parancssoron keresztül a drag and drop paranccsal végezhető el. Ha a másolt fájlok összesített mérete meghaladja a 10 GB-ot, javasoljuk, hogy használjon tömeges másolási programot, például Robocopy vagy rsync. A tömeges másolási eszközök megismétlik a másolási műveletet az időszakos hibákhoz, és további rugalmasságot biztosítanak.
- Ha az Azure Storage-tárolóhoz társított megosztás olyan blobokat tölt fel, amelyek nem egyeznek a megosztáshoz a létrehozáskor meghatározott Blobok típusával, akkor az ilyen Blobok nem frissülnek. Létrehozhat például egy blokk blob-megosztást az eszközön. Társítsa a megosztást egy olyan meglévő Felhőbeli tárolóval, amelynek blobja van. Frissítse a megosztást a fájlok letöltéséhez. Módosítson néhány olyan frissített fájlt, amely már a felhőben blobként van tárolva. Megjelenik a feltöltési hibák.
- Miután létrehozott egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
- A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
- Ha az rsync használatával másol Adatmásolást, akkor `rsync -a` a beállítás nem támogatott.

