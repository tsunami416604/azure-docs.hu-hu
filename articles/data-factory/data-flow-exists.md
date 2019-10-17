---
title: Azure Data Factory leképezési adatfolyam létezik átalakítás
description: Meglévő sorok keresése a adatfeldolgozó-leképezési adatfolyamatok használatával a létező átalakítással
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387842"
---
# <a name="mapping-data-flow-exists-transformation"></a>A leképezési adatfolyam létezik átalakítás



A létező átalakítás egy sor-szűrési átalakítás, amely leállítja vagy lehetővé teszi az adatsorok áthaladását az adatain. A létező átalakító a következőhöz hasonló: ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS```. A létező átalakítás után az adatfolyamból létrejövő sorok az összes olyan sort tartalmazzák, amelyekben az 1. forrás oszlop értékei szerepelnek a 2. forrásban, vagy a 2. forrásban nem léteznek.

![Létező beállítások](media/data-flow/exists.png "létezik 1")

Válassza ki a második forrást, hogy az adatfolyam össze tudja hasonlítani az 1. streamből származó értékeket a 2. streamben.

Válassza ki az 1. forrás és a 2. forrás oszlopát, amelynek értékeit ellenőrizni kívánja, hogy létezik-e vagy sem.

## <a name="multiple-exists-conditions"></a>Több meglévő feltétel

Az oszlop feltételeiben szereplő minden egyes sor mellett megtalálhatja a + jelet, ha az egérmutatót a REACH sor fölé viszi. Ez lehetővé teszi több sor hozzáadását a meglévő feltételekhez. Minden további feltétel egy "és".

## <a name="custom-expression"></a>Egyéni kifejezés

![Meglévő egyéni beállítások](media/data-flow/exists1.png "létezik egyéni")

Az "egyéni kifejezés" lehetőségre kattintva Ehelyett létrehozhat egy szabad formátumú kifejezést a létező vagy nem létező feltételnek megfelelően. Ha bejelöli ezt a jelölőnégyzetet, a rendszer feltételként beírhatja a saját kifejezését.

## <a name="next-steps"></a>Következő lépések

Hasonló átalakítások [Keresés](data-flow-lookup.md) és [Csatlakozás](data-flow-join.md).
