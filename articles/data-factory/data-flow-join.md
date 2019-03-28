---
title: Az Azure Data Factory folyamat illesztési átalakítását
description: Az Azure Data Factory folyamat illesztési átalakítását
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 18f713198ef9aa45cb72a6718c0f7b086c019258
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540040"
---
# <a name="mapping-data-flow-join-transformation"></a>Adatátalakítás folyamat illesztési leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Csatlakozás használatával két tábla az adatfolyam származó adatokat kombinálni. Kattintson a bal oldali kapcsolatban, és adja hozzá az illesztési átalakítás eszközkészletben az átalakítást. Az illesztési átalakítás belül kiválaszthatja a megfelelő kapcsolat a adatok folyamatból egy másik adatfolyam.

![Csatlakozzon az átalakítási](media/data-flow/join.png "csatlakozás")

## <a name="join-types"></a>Csatlakozás típusa

Válassza a csatlakozás típusa szükség az illesztési átalakítást.

### <a name="inner-join"></a>Belső illesztés

Belső illesztés csak a mindkét tábla oszlop feltételeknek megfelelő sorokat fog továbbítani.

### <a name="left-outer"></a>Bal oldali külső

A bal oldali stream nem felel meg az illesztési feltétel az összes sor továbbítja a rendszer, és a kimeneti oszlopokat a táblázatból mellett a belső illesztéssel által visszaadott összes sor NULL értékre vannak beállítva.

### <a name="right-outer"></a>Jobb oldali külső

A megfelelő stream nem felel meg az illesztési feltétel az összes sor továbbítja a rendszer, és a kimeneti oszlopok, amelyek megfelelnek a másik tábla NULL, a belső illesztéssel által visszaadott összes sor mellett.

### <a name="full-outer"></a>A teljes külső

Teljes külső összes oszlopot hoz létre, és mindkét fél oszlopoknál, amelyek NULL értékkel sorait nem jelenik meg a másik táblában.

### <a name="cross-join"></a>Keresztillesztés

Adja meg a két adatfolyam keresztszorzatát kifejezése. Ezzel egyéni illesztési feltételek létrehozása.

## <a name="specify-join-conditions"></a>Adja meg az illesztési feltételek

A Left Join feltétel nem kapcsolódik az illesztés bal oldalán streamből. A Right Join feltétel nem csatlakozik a Join alul, amely lehet egy közvetlen összekötőt egy másik adatfolyamba vagy egy másik stream egy hivatkozást a második streamből.

Ön legalább 1 (1..n) illesztési feltételek megadása kötelező. Ezek lehetnek vagy hivatkozott közvetlenül, a legördülő menüből, vagy kifejezések kiválasztott mezők.

## <a name="join-performance-optimizations"></a>Csatlakozzon a teljesítmény optimalizálása

Egyesítési való csatlakozás SSIS hasonló eszközben, ellentétben az ADF adatfolyam Join nem kötelező egyesítési az illesztési művelet. Az illesztési kulcsok, ezért nem kell először lehet rendezni. A csatlakoztatási műveletet a Spark használatával Databricks Spark optimális join művelet alapján történik: Szórási / térkép oldali illesztési:

![Csatlakozzon az átalakítási optimalizálása](media/data-flow/joinoptimize.png "csatlakozzon optimalizálása")

Ha az adatkészlet illeszkednek a Databricks munkavégző csomópont a memóriába, azt is optimalizálhatja a Join teljesítményt. Azt is megadhatja, hogy a Join művelet jobban illeszkednek a memóriába worker kiszolgálónként adatkészletek létrehozása az adatok particionálása.

## <a name="self-join"></a>Önillesztés

Az ADF adatfolyam önillesztést feltételek a válassza egy meglévő adatfolyam alias átalakítással használatával érheti el. Először is hozzon létre egy "új ágat" streamből, majd hozzáadásához jelölje ki az alias a teljes eredeti adatfolyam.

![Önillesztés](media/data-flow/selfjoin.png "Önillesztés")

A fenti ábrán a Select transformaci tetején. Az összes műveletet az aliasképző "OrigSourceBatting" az eredeti adatfolyam. A kijelölt illesztési átalakítás alatta látható, hogy vesszük a Select alias stream a jobb oldali illesztési lehetővé teszi számunkra, hogy ugyanazzal a kulccsal az bal és jobb oldalán a belső illesztéssel hivatkozhat.

## <a name="composite-and-custom-keys"></a>Összetett és egyéni kulcsok

Egyéni és összetett kulcsokat hozhat létre menet közben belső illesztés átalakítását. A további illesztési oszlopok az egyes kapcsolati sor melletti a plusz jelre (+) sorokat adhat hozzá. Vagy egy új kulcs értéket a működés közbeni illesztési számnak a Kifejezésszerkesztő számítási.

## <a name="next-steps"></a>További lépések

Csatlakozás adatok, után használhatja majd [hozhat létre új oszlopokat](data-flow-derived-column.md) és [az adatok gyűjtése a célként megadott adattárba](data-flow-sink.md).
