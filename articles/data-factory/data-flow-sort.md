---
title: Azure Data Factory leképezési adatfolyam rendezésének átalakítása
description: Azure Data Factory leképezési adatrendezési átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 029ce3c509d3f4d241012d3786e60f0c6e95fdc2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387180"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory adatfolyamok rendezésének átalakítása



![Rendezési beállítások](media/data-flow/sort.png "Rendezés")

A rendezési transzformáció lehetővé teszi a bejövő sorok rendezését az aktuális adatfolyamon. A rendezési átalakításból származó kimenő sorok később a beállított sorrendi szabályok szerint lesznek végrehajtva. Kiválaszthatja az egyes oszlopokat, és sorba rendezheti azokat ASC vagy DEC értékre az egyes mezők melletti nyíl mutató használatával. Ha a rendezés alkalmazása előtt módosítania kell az oszlopot, kattintson a "számított oszlopok" elemre a kifejezés-szerkesztő elindításához. Ez lehetőséget biztosít arra, hogy kifejezést hozzon létre a rendezési művelethez ahelyett, hogy egyszerűen alkalmazza egy oszlopot a rendezéshez.

## <a name="case-insensitive"></a>Kis- és nagybetűk megkülönböztetése nélkül
A "kis-és nagybetűk megkülönböztetése" lehetőség bekapcsolható, ha figyelmen kívül hagyja az esetet a karakterlánc vagy a szövegmező rendezése során.

A "csak a partíción belüli rendezés" a Spark-adatparticionálást használja. A bejövő adatforgalomnak csak az egyes partíciókban való rendezésével az adatfolyamok a teljes adatfolyam rendezése helyett a particionált adatforgalmat is rendezhetik.

A rendezési transzformáció egyes rendezési feltételeit átrendezheti. Tehát ha egy oszlopot a rendezési sorrendnél magasabbra kell helyeznie, a sort az egérrel kell megadnia, és feljebb vagy lejjebb kell helyeznie a rendezési listán.

A rendezési effektusok rendezése

Az ADF-adatfolyamatot big data Spark-fürtökön hajtja végre több csomóponton és partíción elosztva. Fontos szem előtt tartani ezt az adatfolyamatot, ha a rendezési átalakítástól függ, hogy az adott sorrendben tárolja az adatait. Ha úgy dönt, hogy egy későbbi átalakítás során újraparticionálja az adatait, az Adatrendezés miatt elveszítheti a rendezést.

## <a name="next-steps"></a>Következő lépések

A rendezést követően érdemes lehet az [összesített átalakítást](data-flow-aggregate.md) használni
