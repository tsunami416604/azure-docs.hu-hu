---
title: Adatforgalom leképezése az átalakításhoz
description: Azure Data Factory leképezési adatfolyamok kibontásának átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930140"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory unpivot átalakítás



A nem normalizált adatkészletek egy normalizált verzióban való kibontásához használja az ADF-leképezési adatfolyamatot úgy, hogy egy rekord több oszlopának értékeit több, egyetlen oszlopban szereplő értékkel rendelkező rekordba bővíti.

![Az átalakítás unpivot](media/data-flow/unpivot1.png "Kibontási beállítások 1")

## <a name="ungroup-by"></a>Csoportosítás bontása

![Az átalakítás unpivot](media/data-flow/unpivot5.png "Kibontási beállítások 2")

Először állítsa be azokat az oszlopokat, amelyeket a kimutatás összesítéséhez szeretne csoportosítani. Állítson be egy vagy több oszlopot a csoportosítás kibontásához az oszlopok listája melletti + jelre.

## <a name="unpivot-key"></a>Kulcs elforgatása

![Az átalakítás unpivot](media/data-flow/unpivot6.png "Kibontási beállítások 3")

A pivot kulcs az az oszlop, amelyet az ADF a sor és az oszlop között fog kimutatni. Alapértelmezés szerint a mezőhöz tartozó adatkészlet minden egyedi értéke egy oszlopra mutat. Azonban megadhatja az adatkészletből azokat az értékeket, amelyeket meg szeretne adni az oszlop értékeinek.

## <a name="unpivoted-columns"></a>Nem csuklós oszlopok

![Az átalakítás unpivot](media/data-flow//unpivot7.png "Kibontási beállítások 4")

Végül válassza ki a tagolt értékekhez használni kívánt összesítést, és azt, hogy az oszlopok hogyan jelenjenek meg a transzformáció új kimeneti vetületében.

Választható Megadhat egy elnevezési mintát egy előtaggal, egy középtel és egy utótaggal, amelyet a rendszer minden új oszlopnév számára hozzáadhat a sorok értékeiből.

Ha például a "Sales" (értékesítés) kifejezést a "régió" értékre szeretné kimutatni, egyszerűen csak új oszlop értékeket adhat meg az egyes értékesítési értékektől. Például: "25", "50", "1000",... Ha azonban a "Sales" előtag értékét állítja be, akkor az "értékesítés" érték az értékekre lesz feloldva.

<img src="media/data-flow/unpivot3.png" width="400">

Ha úgy állítja be az oszlop elrendezését a "NORMAL" értékre, hogy az összes elforgatott oszlopot összesítse. Ha az oszlopok elrendezése az "oldalirányú" értékre van állítva, akkor az oszlop és az érték között váltakozik.

![Az átalakítás unpivot](media/data-flow//unpivot7.png "Kibontási beállítások 5")

A végleges, nem felváltott adateredményhalmaz azt mutatja, hogy az oszlopok összesítései mostantól külön sorba vannak bontva.

## <a name="next-steps"></a>Következő lépések

Sorok oszlopokra való kimutatásához használja a [pivot transzformációt](data-flow-pivot.md) .
