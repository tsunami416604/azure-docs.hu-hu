---
title: Azure Data Factory leképezési adatfolyam keresési transzformációja
description: Azure Data Factory leképezési adatfolyam keresési transzformációja
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029339"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory leképezési adatfolyam keresési transzformációja



A lookup használatával adjon meg egy másik forrásból származó referenciát az adatfolyamathoz. A keresési átalakításhoz olyan definiált forrásra van szükség, amely a hivatkozási táblára mutat, és megfelel a kulcs mezőinek.

![Keresési transzformáció](media/data-flow/lookup1.png "keresése")

Válassza ki azokat a legfontosabb mezőket, amelyeknek meg kell egyeznie a bejövő adatfolyam mezői és a hivatkozási forrás mezői között. Először létre kell hoznia egy új forrást az adatfolyam-tervezési vászonon, hogy az a keresés jobb oldalán legyen.

A egyezések megtalálása esetén a hivatkozási forrásból származó sorok és oszlopok lesznek hozzáadva az adatfolyamhoz. Kiválaszthatja, hogy mely érdekes mezőket kívánja felvenni a fogadóba az adatfolyam végén.

## <a name="match--no-match"></a>Egyezés/nincs egyezés

A keresési átalakítás után a következő átalakításokkal vizsgálhatja meg az egyes egyeztetési sorok eredményét a `isMatch()` Expression függvény használatával, hogy további döntéseket lehessen tenni a logikában attól függően, hogy a keresés egy sor egyezését eredményezte-e, vagy sem.

## <a name="optimizations"></a>Optimalizálás

Data Factory az adatfolyamatok kibővített Spark-környezetekben futnak. Ha az adatkészlet elfér a munkavégző csomópont memóriájában, optimalizálhatja a keresési teljesítményt.

![Szórásos csatlakozás](media/data-flow/broadcast.png "szórásos csatlakoztatása")

### <a name="broadcast-join"></a>Szórásos csatlakozás

Válassza ki a bal oldali és/vagy jobb oldali szórásos illesztést, hogy az ADF kérése után a teljes adatkészletet a keresési kapcsolat egyik oldaláról a memóriába küldje.

### <a name="data-partitioning"></a>Adatparticionálás

Az adatparticionálást úgy is megadhatja, ha a keresési átalakítás optimalizálása lapján a "particionálás beállítása" lehetőséget választja, és olyan adatkészleteket hoz létre, amelyek jobban illeszkednek a memóriába egy munkavégző számára.

## <a name="next-steps"></a>További lépések

A [Csatlakozás](data-flow-join.md) és a [meglévő](data-flow-exists.md) átalakítások hasonló feladatokat hajtanak végre az ADF-leképezési adatforgalomban. Tekintse meg a következő átalakításokat.
