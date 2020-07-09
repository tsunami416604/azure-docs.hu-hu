---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179574"
---
A következő kikötések az Azure-ba való váltáskor érvényesek az adatvédelemre.

- Javasoljuk, hogy egynél több eszköz ne írjon ugyanahhoz a tárolóhoz.
- Ha van egy meglévő Azure-objektuma (például egy blob vagy egy fájl) a felhőben az éppen másolt objektum nevével, az eszköz felülírja a fájlt a felhőben.
- A megosztási mappák alatt létrehozott üres címtár-hierarchiát (fájlok nélkül) a blob-tárolók nem töltötték fel.
- Az Adatmásolás a Fájlkezelőben vagy a parancssoron keresztül a drag and drop paranccsal végezhető el. Ha a másolt fájlok összesített mérete meghaladja a 10 GB-ot, javasoljuk, hogy használjon tömeges másolási programot, például Robocopy vagy rsync. A tömeges másolási eszközök megismétlik a másolási műveletet az időszakos hibákhoz, és további rugalmasságot biztosítanak.
- Ha az Azure Storage-tárolóhoz társított megosztás olyan blobokat tölt fel, amelyek nem egyeznek a megosztáshoz a létrehozáskor meghatározott Blobok típusával, akkor az ilyen Blobok nem frissülnek. Létrehozhat például egy blokk blob-megosztást az eszközön. Társítsa a megosztást egy olyan meglévő Felhőbeli tárolóval, amelynek blobja van. Frissítse a megosztást a fájlok letöltéséhez. Módosítson néhány olyan frissített fájlt, amely már a felhőben blobként van tárolva. Megjelenik a feltöltési hibák.
