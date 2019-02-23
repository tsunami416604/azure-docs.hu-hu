---
title: Az Azure Data Factory leképezés adatfolyam átalakítása kifejtése
description: Az Azure Data Factory leképezés adatfolyam átalakítása kifejtése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: f1eca7f76f45966ba43c15b3d8b92203c8199bfe
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732075"
---
# <a name="azure-data-factory-mapping-data-flow-unpivot-transformation"></a>Az Azure Data Factory leképezés adatfolyam átalakítása kifejtése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Unpivot használja az ADF-leképezés adatfolyam arra, hogy kapcsolja be egy normalizálás előtt adatkészlet több normalizált verzióba több rekord egyetlen oszlop ugyanazokkal az értékekkel, az adott rekord több oszlop értékeit kibontásával.

![Elemi értékekre bontása átalakítási](media/data-flow/unpivot1.png "elemi értékekre bontása 1 beállításai")

## <a name="ungroup-by"></a>Ungroup szerint

![Elemi értékekre bontása átalakítási](media/data-flow/unpivot5.png "elemi értékekre bontása beállítások 2")

Először állítsa be a pivot összesítés szerint csoportosítani kívánt oszlopokat. Egy vagy több oszlop esetében az csoportbontása beállítása a + bejelentkezés mellett az oszloplistán.

## <a name="unpivot-key"></a>Elemi értékekre bontása kulcs

![Elemi értékekre bontása átalakítási](media/data-flow/unpivot6.png "elemi értékekre bontása beállítások 3")

A Pivot kulcsa az oszlopot, amely az ADF sor lesz kimutatás az oszlopot. Alapértelmezés szerint minden egyes egyedi érték, a mező adatkészlet lesz forgáspont tartalmazó oszlop. Ugyanakkor igény szerint értékeket adhat meg a kimutatás az oszlopértékeket kívánt az adatkészletből.

## <a name="unpivoted-columns"></a>Oszlopok elemi értékekre bontva

![Elemi értékekre bontása átalakítási](media/data-flow//unpivot7.png "elemi értékekre bontása 4 beállításai")

Végül válassza ki az összesítést, akinél szeretné használni az elforgatott értékeket, és hogyan szeretne a az új kimeneti leképezése az átalakítást a megjelenítendő oszlopokat.

(Nem kötelező) Beállíthat egy elnevezési mintát egy előtagot, középső és utótagot hozzá kell minden egyes új oszlop neve, a sorok értékei.

Például olyannak "értékesítés" "Régió" egyszerűen adnák, új oszlop értékeit az egyes értékesítési értékét. Példa: "25", "50", "1000", ... Azonban ha egy "Eladások" előtag értékét, majd "Értékesítés" fog előtaggal ellátni az értékeket.

<img src="media/data-flow/unpivot3.png" width="400">

A "Normál" oszlop elrendezésének beállítása fog egy csoportba összes elforgatott oszlopot az összesített értékekre. "Oldalirányú" oszlopot a megállapodás beállítását fog alternatív oszlophoz és értékhez között.

![Elemi értékekre bontása átalakítási](media/data-flow//unpivot7.png "elemi értékekre bontása 5 beállításai")

A végső elemi értékekre bontva adatok eredmény set bemutatja a oszlopösszegzések most elemi értékekre bontva külön sorban értékekké.
