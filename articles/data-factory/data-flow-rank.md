---
title: Rangsor átalakítása a leképezési adatfolyamban
description: A Azure Data Factory leképezési folyamatábrájának használata rangsor oszlop létrehozása
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: cb3a8c1c6df61a4a20ce596e3ff1118c2870033a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716517"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Rangsor átalakítása a leképezési adatfolyamban 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A rangsor-átalakítással a felhasználó által megadott rendezési feltételek alapján hozhatja meg a rendezett rangsort. 

## <a name="configuration"></a>Konfiguráció

![Rangsor beállításai](media/data-flow/rank-configuration.png "Rangsor beállításai")

**Kis-és nagybetűk megkülönböztetése:** Ha a rendezési oszlop karakterlánc típusú, akkor a rendszer a rangsorban számítja ki az esetet. 

**Sűrű:** Ha engedélyezve van, a rangsor oszlop sűrű rangsorolva lesz. Az egyes rangsorok száma egymást követő szám, a rangsorolási értékek pedig a döntetlen után nem lesznek kihagyva.

**Rangsor oszlopa:** A rangsor oszlopának neve. Az oszlop típusa hosszú lesz.

**Rendezési feltételek:** Kiválaszthatja, hogy mely oszlopok legyenek feldolgozva, és hogy milyen sorrendben történjen a rendezés. A sorrend meghatározza a rendezési prioritást.

A fenti konfiguráció a bejövő kosárlabda-adatsorokat veszi át, és létrehoz egy "pointsRanking" nevű rangsoroló oszlopot. A *PTS* oszlop legmagasabb értékével rendelkező sor *pointsRanking* értéke 1.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Példa

![Rangsor beállításai](media/data-flow/rank-configuration.png "Rangsor beállításai")

A fenti rangsor-konfigurációhoz tartozó adatfolyam-parancsfájl a következő kódrészletben található.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>További lépések

Sorok szűrése a rangsorolási értékek alapján a [szűrő-átalakítás](data-flow-filter.md)használatával.