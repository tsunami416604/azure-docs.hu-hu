---
title: A lekérdezési idő optimalizálása a TOAST táblatárolási stratégia használatával az Azure Database for PostgreSQL - Single Server rendszerben
description: Ez a cikk ismerteti, hogyan optimalizálhatja a lekérdezési idő a TOAST tábla tárolási stratégia egy Azure Database for PostgreSQL - Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65066975"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>A lekérdezési idő optimalizálása a TOAST tábla tárolási stratégiájával 
Ez a cikk azt ismerteti, hogyan optimalizálhatja a lekérdezési időket a túlméretes attribútumtárolási módszerrel (TOAST) táblatárolási stratégiával.

## <a name="toast-table-storage-strategies"></a>TOAST asztal tárolási stratégiák
Négy különböző stratégiák tárolására használt oszlopok lemezen, amely képes használni TOAST. A tömörítés és a soron kívüli tárolás különböző kombinációit képviselik. A stratégia az adattípus szintjén és az oszlop szintjén állítható be.
- **Az Egyszerű** megakadályozza a tömörítést vagy a soron kívüli tárolást. Letiltja az egybájtos fejlécek használatát a varlena típusokhoz. Az egyszerű az egyetlen lehetséges stratégia olyan adattípusok oszlopai számára, amelyek nem tudják használni a TOAST-ot.
- **A kiterjesztett** lehetővé teszi mind a tömörítést, mind a soron kívüli tárolást. A KITERJESZTETT az alapértelmezett a legtöbb olyan adattípusnál, amely használhatja a TOAST-ot. Először a tömörítést kísérli meg. A rendszer kísérlettel kísérli meg a soron kívüli tárolást, ha a sor még mindig túl nagy.
- **A Külső** lehetővé teszi a soron kívüli tárolást, de a tömörítést nem. A Külső használata gyorsabbá teszi a karakterlánc-alatti műveleteket a széles szöveg- és byteaoszlopokon. Ez a sebesség a megnövekedett tárhely büntetésével jár. Ezek a műveletek úgy vannak optimalizálva, hogy csak a soron kívüli érték szükséges részeit érjék le, ha nincstömörítve.
- **A Fő** lehetővé teszi a tömörítést, de nem a soron kívüli tárolást. Az ilyen oszlopok esetében továbbra is soron kívüli tárolás történik, de csak végső megoldásként. Ez akkor fordul elő, ha nincs más módja annak, hogy a sor elég kicsi ahhoz, hogy elférjen egy oldalon.

## <a name="use-toast-table-storage-strategies"></a>Toast asztaltárolási stratégiák használata
Ha a lekérdezések olyan adattípusokhoz férnek hozzá, amelyek a TOAST-t használhatják, fontolja meg a Fő stratégia használatát az alapértelmezett Kiterjesztett beállítás helyett a lekérdezési idők csökkentéséhez. A fő nem zárja ki a vezetéken kívüli tárolást. Ha a lekérdezések nem férnek hozzá a TOAST használatára jogosult adattípusokhoz, hasznos lehet a Kiterjesztett beállítás megtartása. A főtábla sorainak nagyobb része elfér a megosztott puffergyorsítótárban, ami segíti a teljesítményt.

Ha olyan számítási feladattal rendelkezik, amely széles táblákkal és magas karakterszámmal rendelkező sémát használ, fontolja meg a PostgreSQL TOAST táblák használatát. Egy példa vevőtáblája 350-nél nagyobb oszlopot tartalmazott, több oszlop255 karaktert ölelt fel. Miután átlett alakítva a TOAST tábla Fő stratégiájára, a benchmark lekérdezési idő 4203 másodpercről 467 másodpercre csökkent. Ez 89 százalékos javulás.

## <a name="next-steps"></a>További lépések
Tekintse át a számítási feladatok at a korábbi jellemzők. 

Tekintse át a PostgreSQL alábbi dokumentációját: 
- [68. fejezet, Adatbázis fizikai tárolása](https://www.postgresql.org/docs/current/storage-toast.html) 