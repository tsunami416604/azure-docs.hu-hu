---
title: Helyettes kulcs átalakítása a leképezési adatfolyamban
description: A Azure Data Factory leképezési adatforgalmának helyettes kulcs-átalakításának használata szekvenciális kulcsok értékének létrehozásához
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606298"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Helyettes kulcs átalakítása a leképezési adatfolyamban 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A helyettesítő kulcs átalakításával adja hozzá a növekményes kulcs értékét az egyes adatsorokhoz. Ez akkor hasznos, ha dimenziós táblákat tervez kialakítani egy Star Schema analitikai adatmodellben. A Star-sémákban a dimenziós táblázatok egyes tagjainak olyan egyedi kulcsra van szükségük, amely nem üzleti kulcs.

## <a name="configuration"></a>Konfiguráció

![Helyettes kulcs átalakítása](media/data-flow/surrogate.png "Helyettes kulcs átalakítása")

**Kulcs oszlopa:** A generált helyettesítő kulcs oszlopának neve.

**Kezdő érték:** A legkisebb kulcs értéke, amelyet a rendszer generál.

## <a name="increment-keys-from-existing-sources"></a>Kulcsok növelése meglévő forrásokból

Ha a sorozatot egy olyan értékről szeretné elindítani, amely egy adott forrásban létezik, a helyettesítő kulcs átalakítását követően hozzon létre egy származtatott oszlop-átalakítást a két érték együttes hozzáadásához:

![SK maximális hozzáadása](media/data-flow/sk006.png "Helyettes kulcs átalakításának hozzáadása max.")

### <a name="increment-from-existing-maximum-value"></a>Növekmény a meglévő maximális értéktől

Ha a kulcs értékét az előző max. módszerrel szeretné kiosztani, két olyan módszert használhat, amelyet a forrásadatok alapján lehet használni.

#### <a name="database-sources"></a>Adatbázis-források

SQL-lekérdezési lehetőséggel válassza ki a MAX () elemet a forrásból. Például: `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Helyettes kulcs lekérdezése](media/data-flow/sk002.png "Helyettes kulcs transzformációs lekérdezése")

#### <a name="file-sources"></a>Fájlok forrásai

Ha az előző maximális érték egy fájlban található, az `max()` összesített transzformációban az előző maximális érték beszerzéséhez használja a függvényt:

![Helyettesítő kulcs fájlja](media/data-flow/sk008.png "Helyettesítő kulcs fájlja")

Mindkét esetben csatlakoztatnia kell a bejövő új adatait az előző maximális értéket tartalmazó forrással együtt.

![Helyettes kulcs illesztése](media/data-flow/sk004.png "Helyettes kulcs illesztése")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Példa

![Helyettes kulcs átalakítása](media/data-flow/surrogate.png "Helyettes kulcs átalakítása")

A fenti helyettesítő kulcs konfigurációjának adatfolyam-parancsfájlja az alábbi kódrészletben található.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>További lépések

Ezek a példák a [JOIN](data-flow-join.md) és a [származtatott oszlop](data-flow-derived-column.md) átalakításokat használják.
