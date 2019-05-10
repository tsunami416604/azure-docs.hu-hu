---
title: Az Azure Data Factory leképezési adatok Flow létezik átalakítása
description: Az Exists átalakítási folyamatok hogyan ellenőrizheti a létező sorok használatával a data factory-társítási adatok
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235982"
---
# <a name="mapping-data-flow-exists-transformation"></a>Az adatfolyam-leképezés létezik átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Exists átalakítás átalakítást, amely a szolgáltatás leáll vagy az adatok áramlása érdekében lehetővé teszi a sorok szűrése egy sor. Létezik átalakítása hasonlít a ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS```. Létezik átalakítás után az eredményül kapott sorokat a streamből származó vagy tartalmazza az összes sor, ahol szerepel az 1. forrásból származó oszlop értékeit forrás 2 vagy 2 forrás nem létezik.

![Beállítások létezik](media/data-flow/exists.png "1 létezik")

A második adatforrás az Exists választhatja, hogy az adatfolyam összehasonlításával értékek Stream 1 elleni Stream 2.

1. forrásból származó és a forrás 2 értékük megtekintéséhez elleni Exists vagy nem létezik, válassza ki az oszlopot.

## <a name="multiple-exists-conditions"></a>Több létezik feltételek

Mellett minden egyes sorára az Exists oszlop feltételeit, megtalálhatja a + elérhető bejelentkezési, ha az egérmutatót elérni sor. Ez lehetővé teszi, hogy hozzá több sort a Exists feltételek. Minden további feltétel egy "És".

## <a name="custom-expression"></a>Egyéni kifejezés

![Egyéni beállítások létezik](media/data-flow/exists1.png "egyéni létezik")

"Egyéni kifejezés" hozzon létre egy szabad formátumú kifejezést, kattintson az létezik, vagy a feltétel nem létezik. A jelölőnégyzet bejelölésével engedélyezi, hogy írja be a saját kifejezés feltétele.

## <a name="next-steps"></a>További lépések

Hasonló átalakításokra [keresési](data-flow-lookup.md) és [csatlakozzon](data-flow-join.md).
