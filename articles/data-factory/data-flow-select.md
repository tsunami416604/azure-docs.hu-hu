---
title: Az Azure Data Factory-folyamat válassza átalakítását leképezése
description: Az Azure Data Factory-folyamat válassza átalakítását leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729338"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Az Azure Data Factory-folyamat válassza átalakítását leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az átalakítás használni (az oszlopok számának csökkentése) oszlop szelektivitás vagy alias oszlopokra és adatfolyam-neveket.

A Select átalakítás lehetővé teszi, hogy egy teljes stream alias, vagy a Stream oszlopok rendelje hozzá a különböző neveket (aliasok), és ezután hivatkozhat az új nevére később az adatok folyamatban. Az átalakítási önillesztést forgatókönyvek esetén hasznos. Önillesztés megvalósításához az ADF adatfolyam módja egy streamet, az "Új ág", majd ezt követően azonnal hozzáadni ág "Select" átalakító igénybe. A stream egy új nevet, amely segítségével csatlakozzon az eredeti adatfolyam önillesztés tartozik:

![Önillesztés](media/data-flow/selfjoin.png "Önillesztés")

A fenti ábrán a Select transformaci tetején. Ez az alias "OrigSourceBatting" az eredeti adatfolyam. Az alatta higlighted illesztési átalakítás láthatja, hogy vesszük a Select alias stream a jobb oldali illesztési lehetővé teszi számunkra, hogy ugyanazzal a kulccsal az bal és jobb oldalán a belső illesztéssel hivatkozhat.

Válassza ki is használható, úgy megszüntetéséhez jelölje ki az adatfolyama oszlopokat. Például ha a fogadó definiált 6 oszlopok rendelkezik, de csak szeretné egy adott 3 átalakításához, és a fogadó majd flow válasszon, kiválaszthatja csak a megadott 3 a select átalakító használatával.

> [!NOTE]
> "Összes" csak az adott oszlopot válasszon ki kell váltania

Beállítások

Az alapértelmezett beállítás a "Kiválasztás", hogy az összes bejövő oszlop tartalmazza, és tartsa az eredeti nevére. Így alias a streamet a Select átalakító név beállítása.

Alias egyes oszlopain törölje "Az összes kijelölése" és az oszlopleképezés használja a lap alján.

![Válassza ki az átalakítási](media/data-flow/select001.png "Alias kiválasztása")
