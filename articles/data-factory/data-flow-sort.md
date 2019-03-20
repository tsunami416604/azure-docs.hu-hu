---
title: Az Azure Data Factory-folyamat rendezési adatátalakítás leképezése
description: Az Azure Data Factory rendezési átalakítását leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: d0482d1081c16dc89e7371c4c33de9b2bb4e4c2e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898754"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Az Azure Data Factory folyamat rendezési adatátalakítások

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Rendezés beállítások](media/data-flow/sort.png "rendezés")

A rendezési átalakítás lehetővé teszi a bejövő az aktuális adatfolyam a sorok rendezése. A rendezés átalakítási kimenő sorai ezt követően követi a rendelési Ön által beállított szabályoknak. Az egyes oszlopok kiválasztása és rendezheti is őket ASC vagy DEC., a mezők melletti nyílra jelző használatával. Ha szeretné módosítani az oszlop a rendezés alkalmazása előtt, kattintson a "Számított oszlopokat" a kifejezés-szerkesztő elindításához. Mindezzel biztosíthatja, hogy értékelhessék hozhat létre egy kifejezést a rendezési művelet helyett egyszerűen alkalmazása egy oszlopot a rendezéshez.

## <a name="case-insensitive"></a>Kis- és nagybetűk megkülönböztetése nélkül
Bekapcsolása "Különbözteti meg a" Ha figyelmen kívül hagyja az esetben, ha a karakterlánc- vagy szöveges mezők rendezéséhez.

"Rendezés csak a partíciók belül" kihasználja a Spark, az adatparticionálás. Minden egyes partíción belül csak a bejövő adatok rendezésével adatfolyamok rendezheti particionált adatok teljes adatfolyam rendezés helyett.

A rendezés átalakításában szerepel a rendezési feltételek mindegyike alakítható. Ezért ha át kell helyeznie egy oszlopot a rendezési sorrend a magasabb az egérrel a sorhoz befogadhatja és helyezze magasabb vagy alacsonyabb a rendezési listán.

A particionálás rendezési gyakorolt hatás

ADF-adatfolyam végrehajtása a big Data típusú adatok a Spark-fürtök több csomópontokat és partíciókat elosztva adatokkal. Fontos szem előtt tartani ezt, ha az adatfolyam mikroszolgáltatásokra, ha Ön a rendezési átalakítási adatok tartani, hogy ugyanabban a sorrendben vannak függően. Ha particionálja az adatokat a későbbi átalakításában, a rendezést, hogy az adatok reshuffling miatt elveszhetnek.

## <a name="next-steps"></a>További lépések

Rendezés után előfordulhat, hogy használni kívánt a [összesített átalakítása](data-flow-aggregate.md)
