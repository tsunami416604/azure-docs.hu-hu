---
title: Azure Data Factory leképezési adatfolyam létezik átalakítás
description: Meglévő sorok keresése a adatfeldolgozó-leképezési adatfolyamatok használatával a létező átalakítással
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029384"
---
# <a name="mapping-data-flow-exists-transformation"></a>A leképezési adatfolyam létezik átalakítás



A létező átalakítás egy sor-szűrési átalakítás, amely leállítja vagy lehetővé teszi az adatsorok áthaladását az adatain. A létező átalakító a következőhöz hasonló: ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS```. A létező átalakítás után az adatfolyamból létrejövő sorok az összes olyan sort tartalmazzák, amelyekben az 1. forrás oszlop értékei szerepelnek a 2. forrásban, vagy a 2. forrásban nem léteznek.

Létező ![Beállítások]léteznek(media/data-flow/exists.png "1")

Válassza ki a második forrást, hogy az adatfolyam össze tudja hasonlítani az 1. streamből származó értékeket a 2. streamben.

Válassza ki az 1. forrás és a 2. forrás oszlopát, amelynek értékeit ellenőrizni kívánja, hogy létezik-e vagy sem.

## <a name="multiple-exists-conditions"></a>Több meglévő feltétel

Az oszlop feltételeiben szereplő minden egyes sor mellett megtalálhatja a + jelet, ha az egérmutatót a REACH sor fölé viszi. Ez lehetővé teszi több sor hozzáadását a meglévő feltételekhez. Minden további feltétel egy "és".

## <a name="custom-expression"></a>Egyéni kifejezés

Meglévő ![Egyéni beállítások](media/data-flow/exists1.png "léteznek egyéniek")

Az "egyéni kifejezés" lehetőségre kattintva Ehelyett létrehozhat egy szabad formátumú kifejezést a létező vagy nem létező feltételnek megfelelően. Ha bejelöli ezt a jelölőnégyzetet, a rendszer feltételként beírhatja a saját kifejezését.

## <a name="next-steps"></a>További lépések

Hasonló átalakítások [Keresés](data-flow-lookup.md) és [Csatlakozás](data-flow-join.md).
