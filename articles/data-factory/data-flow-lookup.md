---
title: Az Azure Data Factory-folyamat keresési adatátalakítás leképezése
description: Az Azure Data Factory-folyamat keresési adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788003"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Az Azure Data Factory-folyamat keresési adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Keresés használatával más forrásból származó referenciaadatokkal hozzáadása az adatfolyam. A keresési átalakító egy meghatározott forrásból, amely a referencia-táblára mutat, és megfelel-e a kulcsmezők van szükség.

![Keresés átalakítása](media/data-flow/lookup1.png "keresése")

Válassza ki a bejövő adatfolyam mezőket és a mezőket a referencia-forrás között egyezés kívánt kulcsmezők. Kell először hozott létre egy új forrás a vásznon adatfolyam tervező használatára, a jobb oldalon a kereséshez.

Egyezés, ha az eredményül kapott sorokat és oszlopokat a referencia-forrásból hozzáadódik az adatfolyam. Kiválaszthatja, hogy a lényeges, az adatfolyam végén található a fogadó szerepeltetni kívánt mezőket.

## <a name="optimizations"></a>Optimalizálási lehetőségek

A Data Factory áttekintése adatfolyamok hajtsa végre a kiterjesztett Spark-környezetek. Ha az adatkészletet feldolgozó csomópont memóriaterületet elfér, azt is optimalizálhatja a keresési teljesítményt.

![Csatlakozás szórási](media/data-flow/broadcast.png "szórási illesztés")

### <a name="broadcast-join"></a>Szórási illesztés

Válassza ki a bal oldalon, és/vagy a jobb oldalon szórási kéréséhez ADF a teljes adatkészlet leküldése a keresési kapcsolat mindkét oldalán a memóriába való csatlakozás.

### <a name="data-partitioning"></a>Adatparticionálás

Megadhatja azt is, válassza a "Beállítása particionálás" Optimalizálás lapján, a Keresés átalakítása jobban illeszkednek a memóriába worker kiszolgálónként adatkészletek létrehozása az adatok particionálása.

## <a name="next-steps"></a>További lépések

[Csatlakozás](data-flow-join.md) és [Exists](data-flow-exists.md) átalakítások hasonló feladatokat az ADF-leképezés adatáramlás. Tekintse meg, ezek az átalakítások tovább.
