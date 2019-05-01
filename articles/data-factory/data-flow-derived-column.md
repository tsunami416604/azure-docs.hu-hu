---
title: Az Azure Data Factory, az adatfolyam-leképezés származtatott oszlop átalakítása
description: Útmutató az Azure Data Factory leképezési Flow származtatott oszlop adatátalakítás nagy mennyiségű adat átalakítására
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917582"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Az adatfolyam-leképezés származtatott oszlop átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A származtatott oszlop átalakítása használja, az adatfolyam létrehozni az új oszlopok vagy a meglévő mezők módosítása.

![oszlop célosztályából](media/data-flow/dc1.png "származtatott oszlop")

Több származtatott oszlopot művelet egyetlen származtatott oszlopot átalakítás végrehajthatja. Kattintson az "Oszlop hozzáadása" átalakítja a 1-nél több oszlopra az átalakítást egyetlen lépésben.

Az oszlop mezőben válassza ki egy létező oszlop felülírása az új származtatott érték, vagy kattintson a "Hozzon létre új oszlop" egy az újonnan származtatott értéket tartalmazó új oszlop létrehozása.

A kifejezés szövegmező megnyílik a Kifejezésszerkesztő, ahol hozhat létre a kifejezés a származtatott oszlopok kifejezés függvények használatával.

## <a name="column-patterns"></a>Oszlopminták

Ha az oszlopneveket a forrásból származó változót, előfordulhat, hogy szeretné hozhat létre a származtatott oszlop belül átalakítások segítségével oszlop minták nevű oszlopok használata helyett. Tekintse meg a [séma eltéréseket](concepts-data-flow-schema-drift.md) további részleteivel.

![oszlop minta](media/data-flow/columnpattern.png "oszlop minták")

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [adat-előállító kifejezés nyelve átalakítások](https://aka.ms/dataflowexpressions) és a [Kifejezésszerkesztő](concepts-data-flow-expression-builder.md)
