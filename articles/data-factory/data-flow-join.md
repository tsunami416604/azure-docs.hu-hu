---
title: Az Azure Data Factory folyamat illesztési átalakítását
description: Az Azure Data Factory folyamat illesztési átalakítását
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 517afe21fbf9241e2b2423525e9caee12a5603f6
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271809"
---
# <a name="azure-data-factory-data-flow-join-transformation"></a>Az Azure Data Factory folyamat illesztési átalakítását

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Csatlakozás használatával két tábla az adatfolyam származó adatokat kombinálni. Kattintson a bal oldali kapcsolatban, és adja hozzá az illesztési átalakítás eszközkészletben az átalakítást. Az illesztési átalakítás belül kiválaszthatja a megfelelő kapcsolat a adatok folyamatból egy másik adatfolyam.

![Csatlakozzon az átalakítási](media/data-flow/join.png "csatlakozás")

## <a name="join-types"></a>Csatlakozás típusa

Válassza a csatlakozás típusa szükség az illesztési átalakítás

### <a name="inner-join"></a>Belső illesztés

Csak a mindkét tábla oszlop feltételeknek megfelelő sorokat továbbítja a belső illesztés

### <a name="left-outer"></a>Bal oldali külső

A bal oldali stream nem felel meg az illesztési feltétel az összes sor továbbítja a rendszer, és a kimeneti oszlopokat a táblázatból mellett a belső illesztéssel által visszaadott összes sor NULL értékre vannak beállítva.

### <a name="right-outer"></a>Jobb oldali külső

A megfelelő stream nem felel meg az illesztési feltétel az összes sor továbbítja a rendszer, és a kimeneti oszlopok, amelyek megfelelnek a másik tábla NULL, a belső illesztéssel által visszaadott összes sor mellett.

### <a name="full-outer"></a>A teljes külső

Teljes külső összes oszlopot hoz létre, és mindkét fél oszlopoknál, amelyek NULL értékkel sorait nem jelenik meg a másik táblában

### <a name="cross-join"></a>Keresztillesztés

A két kifejezés-adatfolyamok keresztszorzatát specifikus

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
