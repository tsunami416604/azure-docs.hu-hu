---
title: Az adatfolyam rendezésének leképezése
description: Azure Data Factory leképezési adatrendezési átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930226"
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
