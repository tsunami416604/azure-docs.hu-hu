---
title: Az Azure Data Factory-folyamat adatkészletek adatok leképezése
description: Az Azure Data Factory leképezési adatfolyam sepecific adatkészlet kompatibilitási rendelkezik
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ad6cfdad519ab3901c58979970ea07439b3106e9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726923"
---
# <a name="mapping-data-flow-datasets"></a>Data Flow adatkészletek leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Adatkészlet lehet egy Data Factory-szerkezet, amely a folyamat használata az adatok felépítésének meghatározásához. Az adatfolyam sor- és szolgáltatói szükséges az adatok egy finom részletesebben adatkészlet-definícióban. A control flow folyamatokban használt Adatkészletekre nem szükséges adatok megismerése azonos mélységét.

Az adatfolyam adatkészletek forrásaként és fogadó átalakítások használhatók. Ezek az alapvető adatsémák meghatározására szolgálnak. Ha a séma az adatok nem rendelkeznek, a forrás és fogadó séma eltéréseket állíthatja a. Az adathalmaz definiált sémával kell a kapcsolódó adattípusok, adatformátumok a célnyelven, helye és a kapcsolódó Linked Service csatlakozási adataival. A metaadatok az adatkészletekből származó "Leképezés" forrásaként jelenik az adatforrás-átalakítás. A séma az adatkészletben a leképezésről az adatforrás-átalakítás jelöli az adatok meghatározott nevét és típusát adatreprezentációt folyamat során a fizikai adattípus és alakzat jelöli.

## <a name="dataset-types"></a>Adatkészlet-típusok

Jelenleg a data flow-ban található négy adatkészlet típusa:

* Azure SQL DB
* Azure SQL DW
* Parquet (az ADLS- és Blob)
* Tagolt szöveges (az ADLS- és Blob)

Data flow adatkészletek külön a *adatforrástípust* származó a *társított szolgáltatási kapcsolati típus*. Általában a Data Factory áttekintése válassza ki a kapcsolat típusát (Blob, ADLS stb.), és megadhatja az adatkészletben lévő fájl típusa. Belül adatfolyam a másik társított szolgáltatás kapcsolattípusok társítható forrástípusok válasszon ki.

![Átalakítási beállítások a forrás](media/data-flow/dataset1.png "források")

## <a name="data-flow-compatible-datasets"></a>Data Flow kompatibilis adatkészletek

Amikor egy új adatkészletet hoz létre, van egy "Data Flow kompatibilis" jobb felső sarokban lévő távolságát a panel feliratú jelölőnégyzetet. A gombra kattintva a rendszer kiszűri csak azokat az adatkészleteket, amelyek együtt adatáramlás. 

## <a name="import-schemas"></a>Sémák importálása

A séma az adatfolyam adatkészletek importálásakor egy séma importálása gomb jelenik meg. A gombra kattintva megjelennek a két lehetőség közül választhat: A forrásból importálja, vagy importáljon egy helyi fájlból. A legtöbb esetben közvetlenül a forrásból fogja importálja a sémát. Azonban ha egy meglévő sémafájl (Parquet-fájlok vagy CSV-fejlécek), mutatva, hogy helyi fájlt és a Data Factory határozza meg a séma séma fájl alapján.

## <a name="delimited-text-dataset"></a>Karakterrel elválasztott szöveg adatkészlet

Elválasztójellel tagolt szöveges adatkészlet állítja a elválasztó kezeléséhez vagy egyetlen elválasztó karakterek ("\t"számára TSV,"," fürt megosztott kötetei szolgáltatás, ' |} "...) vagy több karakter határoló keresése. A fejléc sorra váltógombot állítsa be, és keresse meg azokat a forrás átalakítással automatikusan észlelje az adattípusokat.

## <a name="next-steps"></a>További lépések

Első lépésként [létrehozása egy új adatfolyam](data-flow-create.md) , és adja hozzá egy adatforrás-átalakítás. Ezután konfigurálja az adatkészlet ahhoz az adatforráshoz.

Használja a [másolási tevékenység](copy-activity-overview.md) ahhoz, hogy az adatok minden ADF az adatforrásra, és adatfolyam eléri az ADLS- vagy Blob előkészítése.

