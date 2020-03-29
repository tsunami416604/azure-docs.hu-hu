---
title: Adatfolyam leképezése helyettesítő kulcs átalakítása
description: Az Azure Data Factory leképezési adatfolyamának helyettesítőkulcs-átalakítása a szekvenciális kulcsértékek létrehozásához
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930205"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Adatfolyam leképezése helyettesítő kulcs átalakítása



A helyettesítő kulcs átalakítása segítségével hozzáadhat egy nem üzleti tetszőleges kulcsértéket az adatfolyam-sorhalmazhoz. Ez akkor hasznos, ha dimenziótáblákat tervez egy csillagséma analitikus adatmodellben, ahol a dimenziótáblák minden tagjának rendelkeznie kell egy egyedi kulccsal, amely a Kimball DW módszertan része.

![Helyettesítő kulcs átalakítása](media/data-flow/surrogate.png "Helyettesítő kulcs átalakítása")

"Key Column" az a név, amit az új helyettesítő kulcs oszlopnak ad.

A "Kezdő érték" a növekményes érték kezdőpontja.

## <a name="increment-keys-from-existing-sources"></a>Meglévő forrásokból származó növekménykulcsok

Ha a sorozatot egy forrásban lévő értékből szeretné elindítani, akkor közvetlenül a helyettesítőkulcs átalakítása után használhatja a származtatott oszlop átalakítását, és hozzáadhatja a két értéket egymáshoz:

![SK hozzáadása Max](media/data-flow/sk006.png "Helyettesítő kulcs átalakítása Max hozzáadása")

Az előző max.

### <a name="database-sources"></a>Adatbázis-források

A "Lekérdezés" beállítással válassza a MAX() értéket a forrásból a Forrás transzformáció használatával:

![Helyettesítő kulcs lekérdezése](media/data-flow/sk002.png "Helyettesítő kulcs átalakítási lekérdezése")

### <a name="file-sources"></a>Fájlforrások

Ha az előző maximális érték egy fájlban van, használhatja a forrásátalakítást egy összesítő átalakítással együtt, és a MAX() kifejezés függvény segítségével lejuthat az előző maximális értékre:

![Helyettesítő kulcsfájl](media/data-flow/sk008.png "Helyettesítő kulcsfájl")

Mindkét esetben csatlakoznia kell a bejövő új adatokhoz az előző maximális értéket tartalmazó forrással együtt:

![Helyettesítő kulcs illesztése](media/data-flow/sk004.png "Helyettesítő kulcs illesztése")

## <a name="next-steps"></a>További lépések

Ezek a példák az [Illesztés](data-flow-join.md) és [a Származtatott oszlop](data-flow-derived-column.md) átalakításokat használják.
