---
title: Azure Data Factory leképezési adatfolyam kiválasztása – átalakítás
description: Azure Data Factory leképezési adatfolyam kiválasztása – átalakítás
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314218"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory leképezési adatfolyam kiválasztása – átalakítás
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Használja ezt az átalakítást az oszlopok szelektivitásához (oszlopok számának csökkentése), alias-oszlopokhoz és adatfolyam-nevekhez, valamint az oszlopok átrendezéséhez.

## <a name="how-to-use-select-transformation"></a>Az átalakítás kiválasztása
Az átalakítás lehetőséget választva a teljes adatfolyamot vagy az adott adatfolyamban lévő oszlopokat, különböző neveket (aliasokat) rendelhet hozzá, majd az új neveket az adatfolyamatban később is hivatkozhat. Ez az átalakítás az önillesztési forgatókönyvek esetében hasznos. Ha önillesztést szeretne megvalósítani az ADF-adatforgalomban, egy streamet kell létrehoznia, amely az "új ág", majd közvetlenül ezt követően a "Select" átalakítás hozzáadásával történik. A stream ekkor egy új névvel fog rendelkezni, amellyel visszatérhet az eredeti streamhez, és létrehozhat egy önillesztést:

![Önálló csatlakozás](media/data-flow/selfjoin.png "Önálló csatlakozás")

A fenti ábrán az átalakító kijelölése felül van. Ez a "OrigSourceBatting" eredeti adatfolyamának aliasa. Az alatta lévő összekapcsolási átalakítóban láthatja, hogy a kiválasztott alias streamet használja a jobb oldali illesztéshez, ami lehetővé teszi, hogy ugyanarra a kulcsra hivatkozzon a belső illesztés bal & jobb oldalán.

A Select (kijelölés) lehetőséggel kiválaszthatja az adatfolyamatból az oszlopok kiválasztására szolgáló oszlopokat is. Ha például 6 oszlop van definiálva a fogadóban, de csak egy adott 3 értéket szeretne átalakítani, majd a fogadóba befolyni, akkor a Select átalakító használatával kiválaszthatja a csak a 3 értéket.

> [!NOTE]
> Csak meghatározott oszlopok kiválasztásához ki kell kapcsolnia az "összes kijelölése" lehetőséget.

![Átalakítás kiválasztása](media/data-flow/select001.png "Alias kiválasztása")

## <a name="options"></a>Beállítások
* A "Select" alapértelmezett beállítása az összes bejövő oszlop belefoglalása, és az eredeti nevek megtartása. A streamet aliasként adhatja meg a Select átalakító nevének beállításával.
* Az egyes oszlopok aliasához törölje az "összes kijelölése" lehetőséget, és használja a lenti oszlop-hozzárendelést.
* A duplikált elemek kihagyása lehetőséget választotta a bemeneti vagy kimeneti metaadatok ismétlődő oszlopainak eltávolítására.

![Ismétlődések] kihagyása (media/data-flow/select-skip-dup.png "Ismétlődések") kihagyása

## <a name="next-steps"></a>További lépések
* Miután a Select paranccsal átnevezi, átrendezi és alias oszlopokat, a fogadó [transzformáció](data-flow-sink.md) használatával az adatait egy adattárba helyezheti.
