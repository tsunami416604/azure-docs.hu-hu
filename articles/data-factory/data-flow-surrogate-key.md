---
title: Azure Data Factory leképezési adatforgalom helyettes kulcsának átalakítása
description: A Azure Data Factory leképezési adatforgalmának helyettes kulcs-átalakításának használata szekvenciális kulcsok értékének létrehozásához
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387168"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Adatfolyamatok helyettesítő kulcsának átalakításának leképezése



A helyettesítő kulcs átalakításával bővítheti a nem üzleti tetszőleges kulcs értékét az adatfolyam-sorhalmazban. Ez akkor hasznos, ha dimenziós táblákat tervez egy olyan Star Schema analitikai adatmodellben, amelyben a dimenziós táblák egyes tagjainak egy nem üzleti kulcshoz tartozó egyedi kulccsal kell rendelkezniük, amely a Kimball DW módszertan részét képezi.

![Helyettes kulcs átalakítása](media/data-flow/surrogate.png "Helyettes kulcs átalakítása")

A "kulcs oszlop" az a név, amelyet az új helyettesítő kulcs oszlopához fog adni.

A "kezdő érték" a növekményes érték kezdőpontja.

## <a name="increment-keys-from-existing-sources"></a>Kulcsok növelése meglévő forrásokból

Ha a sorozatot egy olyan értékről szeretné elindítani, amely egy forrásban található, akkor a származtatott oszlop-átalakítást közvetlenül a helyettesítő kulcs átalakítását követően, a két érték együttes hozzáadásával is elvégezheti:

![SK maximális hozzáadása](media/data-flow/sk006.png "Helyettes kulcs átalakításának hozzáadása max.")

Ha a kulcs értékét az előző max. módszerrel szeretné kiosztani, két módszert használhat:

### <a name="database-sources"></a>Adatbázis-források

A "lekérdezés" lehetőséggel válassza ki a MAX () elemet a forrás-átalakítás használatával:

![Helyettes kulcs lekérdezése](media/data-flow/sk002.png "Helyettes kulcs transzformációs lekérdezése")

### <a name="file-sources"></a>Fájlok forrásai

Ha az előző maximális érték egy fájlban van, használhatja a forrás-átalakítást egy összesített átalakítással együtt, és a MAX () Expression függvény használatával szerezheti be az előző maximális értéket:

![Helyettesítő kulcs fájlja](media/data-flow/sk008.png "Helyettesítő kulcs fájlja")

Mindkét esetben a korábbi maximális értéket tartalmazó forrással együtt kell csatlakoztatnia a bejövő új adatait:

![Helyettes kulcs illesztése](media/data-flow/sk004.png "Helyettes kulcs illesztése")

## <a name="next-steps"></a>Következő lépések

Ezek a példák a [JOIN](data-flow-join.md) és a [származtatott oszlop](data-flow-derived-column.md) átalakításokat használják.
