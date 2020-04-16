---
title: Helyettesítő kulcs átalakítása az adatfolyam leképezésében
description: Az Azure Data Factory leképezési adatfolyamának helyettesítőkulcs-átalakítása a szekvenciális kulcsértékek létrehozásához
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: 6f0310f13ab23b5b3d4fba649d0faf0da4dad5b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413141"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Helyettesítő kulcs átalakítása az adatfolyam leképezésében 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A helyettesítő kulcs átalakításával minden adatsorhoz hozzáadhat egy növekménykulcs-értéket. Ez akkor hasznos, ha dimenziótáblákat tervez csillagséma analitikus adatmodellben. A csillagsémában a dimenziótáblák minden tagjának szüksége van egy egyedi kulcsra, amely nem üzleti kulcs.

## <a name="configuration"></a>Konfiguráció

![Helyettesítő kulcs átalakítása](media/data-flow/surrogate.png "Helyettesítő kulcs átalakítása")

**Kulcsoszlop:** A létrehozott helyettesítő kulcs oszlop neve.

**Kezdő érték:** A létrehozandó legalacsonyabb kulcsérték.

## <a name="increment-keys-from-existing-sources"></a>Meglévő forrásokból származó növekménykulcsok

Ha a sorozatot egy forrásban létező értékből szeretné elindítani, a helyettesítő kulcs átalakítását követő származtatott oszlopátalakítással adja hozzá a két értéket:

![SK hozzáadása Max](media/data-flow/sk006.png "Helyettesítő kulcs átalakítása Max hozzáadása")

### <a name="increment-from-existing-maximum-value"></a>Növekmény a meglévő maximális értéktől

A kulcsérték az előző max értékkel történő magzatához két olyan módszer van, amelyet a forrásadatok helye alapján használhat.

#### <a name="database-sources"></a>Adatbázis-források

Sql-lekérdezési beállítással válassza ki a MAX() elemet a forrásból. Például,`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Helyettesítő kulcs lekérdezése](media/data-flow/sk002.png "Helyettesítő kulcs átalakítási lekérdezése")

#### <a name="file-sources"></a>Fájlforrások

Ha az előző maximális érték egy `max()` fájlban van, az összesített átalakítás függvényével kapja meg az előző maximális értéket:

![Helyettesítő kulcsfájl](media/data-flow/sk008.png "Helyettesítő kulcsfájl")

Mindkét esetben csatlakoznia kell a bejövő új adatokhoz az előző maximális értéket tartalmazó forrással együtt.

![Helyettesítő kulcs illesztése](media/data-flow/sk004.png "Helyettesítő kulcs illesztése")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Példa

![Helyettesítő kulcs átalakítása](media/data-flow/surrogate.png "Helyettesítő kulcs átalakítása")

A fenti helyettesítő kulcs konfigurációjának adatfolyam-parancsfájlja az alábbi kódrészletben található.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>További lépések

Ezek a példák az [Illesztés](data-flow-join.md) és [a Származtatott oszlop](data-flow-derived-column.md) átalakításokat használják.
