---
title: Transzformáció rendezése az adatfolyam leképezésében
description: Azure-adat-előképezési adatok rendezésének átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 381c6573dff1b3f1638af9090a535d9a1e59b2b5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413170"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transzformáció rendezése az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A rendezési transzformáció lehetővé teszi az aktuális adatfolyam bejövő sorainak rendezését. Kiválaszthatja az egyes oszlopokat, és növekvő vagy csökkenő sorrendbe rendezheti őket.

> [!NOTE]
> Az adatfolyamok leképezése szikrafürtökön történik, amelyek több csomópont és partíció között osztják el az adatokat. Ha úgy dönt, hogy egy későbbi átalakítás során újraparticionálja az adatokat, az adatok átrendezése miatt elveszhet a rendezés.

## <a name="configuration"></a>Konfiguráció

![Beállítások rendezése](media/data-flow/sort.png "Rendezés")

**A kis- és nagybetűk megkülönböztetése:** Azt jelzi, hogy figyelmen kívül hagyja-e a kis- és nagybetűket a karakterlánc- vagy szövegmezők rendezésekor

**Rendezés csak partíciókon belül:** Az adatfolyamok spark on futnak, minden adatfolyam partíciókra van osztva. Ez a beállítás csak a bejövő partíciókon belül rendezi az adatokat, nem pedig a teljes adatfolyamot. 

**Rendezési feltételek:** Válassza ki, hogy mely oszlopok szerint rendez, és milyen sorrendben történik a rendezés. A sorrend határozza meg a rendezési prioritást. Adja meg, hogy a null értékek megjelenjenek-e az adatfolyam elején vagy végén.

### <a name="computed-columns"></a>Számított oszlopok

Ha a rendezés alkalmazása előtt módosítani vagy kibontani egy oszlopértéket, vigye az egérmutatót az oszlop fölé, és válassza a "számított oszlop" lehetőséget. Ezzel megnyitja a kifejezésszerkesztőt, hogy oszlopérték helyett kifejezést hozzon létre a rendezési művelethez.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Példa

![Beállítások rendezése](media/data-flow/sort.png "Rendezés")

A fenti rendezési konfiguráció adatfolyam-parancsfájlja az alábbi kódrészletben található.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>További lépések

A rendezés után érdemes lehet az [Összesítő transzformációt](data-flow-aggregate.md)
