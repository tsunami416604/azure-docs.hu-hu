---
title: Azure Data Factory adatfolyam-csatlakozás átalakítása
description: Azure Data Factory adatfolyam-csatlakozás átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: da6c3c90ebbeffcf468aad3809da097976d8ef0d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387240"
---
# <a name="mapping-data-flow-join-transformation"></a>Az adatfolyam-csatlakozás átalakításának leképezése



A JOIN paranccsal egyesítheti az adatfolyamatban lévő két táblázat adatait. Kattintson az átalakításra, amely a bal oldali kapcsolat lesz, és vegyen fel egy JOIN transzformációt az eszközkészletből. Az illesztési átalakítón belül egy másik adatfolyamot kell kiválasztania az adatfolyamatból, hogy a megfelelő kapcsolat legyen.

![Csatlakozás az átalakításhoz](media/data-flow/join.png "Csatlakozás")

## <a name="join-types"></a>Illesztési típusok

Az illesztési típushoz az illesztési típus kiválasztása szükséges.

### <a name="inner-join"></a>Belső illesztés

A belső illesztés csak olyan sorokon halad át, amelyek megfelelnek mindkét tábla oszlopos feltételeinek.

### <a name="left-outer"></a>Bal oldali külső

A bal oldali adatfolyamból az illesztési feltételnek nem megfelelő összes sort átadja a rendszer, és a másik tábla kimeneti oszlopai NULL értékre vannak állítva, a belső illesztés által visszaadott sorok mellett.

### <a name="right-outer"></a>Jobb oldali külső

A jobb oldali adatfolyamból az illesztési feltételnek nem megfelelő összes sort átadja a rendszer, és a másik táblának megfelelő kimeneti oszlopok értéke NULL, a belső illesztés által visszaadott összes sor mellett.

### <a name="full-outer"></a>Teljes külső

A teljes külső előállítja a két oldal összes oszlopát és sorát NULL értékkel a másik táblában nem szereplő oszlopokhoz.

### <a name="cross-join"></a>Keresztbe illesztés

Egy kifejezéssel adja meg a két stream több termékét. Ezt használhatja egyéni csatlakozási feltételek létrehozásához.

## <a name="specify-join-conditions"></a>Csatlakozási feltételek meghatározása

A bal oldali illesztési feltétel az Illesztéstől balra csatlakoztatott adatfolyamból származik. A jobb oldali illesztési feltétel az a második adatfolyam, amely csatlakozik a csatlakozáshoz az alján, amely közvetlen összekötő egy másik streamnek vagy egy másik streamre mutató hivatkozásnak.

Legalább 1 (1. n) csatlakozási feltételt kell megadnia. Lehetnek közvetlenül hivatkozott mezők, amelyek a legördülő menüben vagy kifejezésekben vannak kiválasztva.

## <a name="join-performance-optimizations"></a>Csatlakozás a teljesítmény optimalizálásához

Az egyesítési Illesztéstől eltérően az olyan eszközökhöz, mint a SSIS, az ADF-adatfolyamban való csatlakozás nem kötelező egyesítő illesztési művelet. Ezért az illesztési kulcsokat nem kell elsőként rendezni. Az illesztési művelet a Spark-beli optimális illesztési műveleten alapul: szórás/Térkép – csatlakozás:

![Összekapcsolási átalakítás optimalizálása](media/data-flow/joinoptimize.png "Csatlakozás optimalizálása")

Ha az adatkészlet elfér a munkavégző csomópont memóriájában, optimalizálhatja az illesztési teljesítményt. Az illesztési művelet során az adatai particionálását is megadhatja olyan adatkészletek létrehozásához, amelyek jobban illeszkednek a memóriába egy munkavégzőn.

## <a name="self-join"></a>Önálló csatlakozás

A meglévő streamek aliasának kiválasztása lehetőséggel az átalakítás lehetőséget választva önillesztési feltételeket érhet el az ADF-adatfolyamban. Először hozzon létre egy "új ágat" egy adatfolyamból, majd adjon hozzá egy kijelölést a teljes eredeti adatfolyamhoz.

![Önálló csatlakozás](media/data-flow/selfjoin.png "Önálló csatlakozás")

A fenti ábrán az átalakító kijelölése felül van. A művelet során a rendszer az eredeti streamet aliasként "OrigSourceBatting". Az alább látható összekapcsolási átalakítóban láthatja, hogy ezt a Select alias streamet használjuk a jobb oldali csatlakozáshoz, ami lehetővé teszi, hogy ugyanarra a kulcsra hivatkozzon a belső illesztés bal & jobb oldalán.

## <a name="composite-and-custom-keys"></a>Összetett és egyéni kulcsok

Az illesztési transzformáción belül egyéni és összetett kulcsokat is létrehozhat menet közben. Sorok hozzáadása a további illesztési oszlopokhoz az egyes kapcsolati sorok melletti plusz jelre (+). Vagy egy új kulcs értékének kiszámítása a Kifejezésszerkesztő egy on-the-fly JOIN értékhez.

## <a name="next-steps"></a>Következő lépések

Az adategyesítést követően [új oszlopokat hozhat létre](data-flow-derived-column.md) , és [az adatait a célhely adattárában](data-flow-sink.md)is elvégezheti.
