---
title: Lekérdezési idő az Azure Database for PostgreSQL-kiszolgáló használatával bejelentési tábla tárolási stratégia optimalizálása
description: Ez a cikk ismerteti, hogyan optimalizálható a lekérdezéskor bejelentési table storage stratégiát egy Azure database for PostgreSQL-kiszolgálóhoz.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: dee8aaaef4b1998a7234a88d07ad5efbc79d050b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629130"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>BEJELENTÉSI table storage stratégiát lekérdezési idő optimalizálásához 
Ez a cikk ismerteti, hogyan optimalizálható a gyorsaság bejelentési table storage stratégiát.

## <a name="toast-table-storage-strategies"></a>BEJELENTÉSI table storage stratégiák
Nincsenek bejelentési képes oszlopok tömörítés és a sor végét ki storage között a különböző kombinációkkal jelölő lemezen tárolandó négy különböző stratégiákat. A stratégia megadható adattípusú szinten és az oszlopok szintjén.
- **Egyszerű** megakadályozza, hogy a tömörítés vagy a sor végét ki tárolási; továbbá, letilthatja az egybájtos fejlécek varlena esetében. **Egyszerű** az egyetlen lehetséges stratégia nem bejelentési-képes adattípusú oszlopokhoz.
- **Kiterjesztett** lehetővé teszi, hogy a tömörítés és a sor végét ki tárolási. **Kiterjesztett** legtöbb bejelentési képes adattípusok alapértelmezett. Tömörítés megkísérelt első, majd ki sor tárolására lesz, ha a sor még mindig túl nagy.
- **Külső** lehetővé teszi, hogy a sor végét ki tárolási, de nem tömörítést. Felhasználása **külső** fog tenni karakterláncrészletet műveletek széles szöveg és bytea oszlopok gyorsabb, a megnövelt tárhely, büntetés mivel ezeket a műveleteket csak a szükséges beolvasni vannak optimalizálva a out sor részeit értéket, amikor azt nincs tömörítve.
- **Fő** lehetővé teszi, hogy a tömörítés, de nem out sor tárolására. Beágyazott-out storage továbbra is történik ilyen oszlopokhoz, de csak végső megoldásként, ha nincs más módon nem lehet, hogy a sor elég kicsi a lapon elfér.

## <a name="using-toast-table-storage-strategies"></a>BEJELENTÉSI table storage stratégiákkal
Ha a lekérdezések adattípusok bejelentési képes elérni, érdemes **fő** helyett az alapértelmezett **Extended** a beállítással úgy csökkenthető a gyorsaság. **Fő** nem zárja ki a sor végét ki tárolási. Másrészről, ha a lekérdezések bejelentési képes adattípusok nem férnek hozzá, akkor előnyös lehet, hogy a **Extended** lehetőséget. Így a fő tábla sorait egy nagyobb része illeszkedik a megosztott pufferből gyorsítótárban útmutatás nyújtása a teljesítményt.

Ha egy munkaterhelés-séma használatával széles táblák és a magas karakterszám, fontolja meg PostgreSQL bejelentési táblákat. Egy példa ügyfél tábla kellett nagyobb, mint 350 oszlopait és több átfedés 255 karakter lehet. 467 másodperc, egy 89 százalékos teljesítményjavulást, a bejelentési stratégia az átalakítás után 4203 másodperc csökkentik idejüket a teljesítményteszt lekérdezés **fő**.

## <a name="next-steps"></a>További lépések
Tekintse át a számítási feladatok esetében a fenti jellemzők. 

Tekintse át a következő PostgreSQL-dokumentáció: [fejezet 68-as, fizikai adatbázistár](https://www.postgresql.org/docs/current/storage-toast.html) 