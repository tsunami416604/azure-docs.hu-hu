---
title: Azure SQL-adatbázis biztonsági másolatait tárolni legfeljebb tíz éve |} Microsoft Docs
description: Ismerje meg, hogyan Azure SQL Database támogatja legfeljebb tíz éve tárolni teljes adatbázis biztonsági másolatait.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: b2f3c454ba84c7b892096cc42dcbe2706ab6159f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648292"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL-adatbázis biztonsági másolatait tárolni legfeljebb 10 év

Számos alkalmazás rendelkezik szabályozó, megfelelőségi vagy egyéb üzleti célokat szolgál, amelyek megkövetelik a tartsa meg az adatbázis biztonsági mentése az Azure SQL Database által biztosított 7-35 napon túl [automatikus biztonsági mentésekhez](sql-database-automated-backups.md). A hosszú távú megőrzési (LTR) szolgáltatás segítségével tárolhatja a megadott SQL adatbázis teljes biztonsági mentés a [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) legfeljebb tíz éve blob-tároló. Biztonsági másolat új adatbázisként visszaállíthatja.

> [!IMPORTANT]
> Hosszú távú megőrzési jelenleg előzetes verzió. A korábbi előzetes verziója Ez a szolgáltatás részeként az Azure helyreállítási szolgáltatások tárolóban tárolt meglévő biztonsági másolatok SQL Azure Storage települnek át.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>SQL-adatbázis hosszú távú megőrzési működése

Hosszú távú biztonsági másolatok megőrzésének kihasználja a [automatikus SQL-adatbázis biztonsági másolatait](sql-database-automated-backups.md) létrehozott időpontban helyreállítás (PITR) vesz igénybe. Hosszú távú megőrzési házirend konfigurálásában az minden SQL-adatbázist, és adja meg, hogy milyen gyakran kell másolnia a biztonsági mentések a hosszú távú tárolásra. Ahhoz, hogy a rugalmasság négy paraméterek kombinációjával házirend meghatározhatja: heti biztonsági másolatok megőrzésének (W) havi biztonsági másolatok megőrzésének (M), az éves biztonsági másolatok megőrzésének (Y) és a hét év (WeekOfYear). Ha megad W, egy biztonsági mentés hetente lesznek másolva a hosszú távú tároláshoz. M ad meg, ha egy biztonsági mentés minden hónap első hetében a hosszú távú tárolásra kerülnek. Y ad meg, ha a WeekOfYear által megadott hét során egy biztonsági másolat a hosszú távú tárolásra kerülnek. Minden egyes biztonsági másolat a hosszú távú megőrzés ezek a paraméterek által megadott időtartam tárolni fogja. 

Példák:

-  W = 0, WeekOfYear M = 0, I = 5, = 3

   A 3. teljes biztonsági mentés minden évben 5 évig tárolni fogja.

- W=0, M=3, Y=0

   Az első teljes biztonsági mentés minden hónap 3 hónapig megmarad.

- W=12, M=0, Y=0

   A heti teljes biztonsági 12 hétre tárolni fogja.

- W = 6, M = 12, I = 10, WeekOfYear = 16

   A heti teljes biztonsági 6 hétig tárolni fogja. Első a teljes biztonsági mentés minden hónap, kivéve, amelyek folyamatosan 12 hónapig. A teljes készült biztonsági másolatok év 16 hét, kivéve amelyek folyamatosan tíz éve. 

A következő táblázat bemutatja a ütemben történik, és a lejárati a hosszú távú biztonsági mentését a következő házirendet:

W = 12 hetes (84 nap), M = 12 hónapon keresztül (365 nap), I = 10 évben (3650 nap), WeekOfYear = 15 (a hét után április 15)

   ![balról jobbra – példa](./media/sql-database-long-term-retention/ltr-example.png)


 
Ha el a fenti házirend módosítása és a biztonsági másolatok W = 0 (nincs heti biztonsági mentései), a ütemben történik készletét megváltozna látható a fenti táblázatban a kijelölt dátumok szerint. Ezek a biztonsági mentések megtartása szükséges tárterület ennek megfelelően csökkenne. 

> [!NOTE]
1. A balról jobbra másolja az Azure storage szolgáltatás jönnek létre, így a másolást nem befolyásolta a teljesítményt a létező adatbázis felett.
2. A házirend a jövőbeli biztonsági mentések vonatkozik. Például Ha a megadott WeekOfYear a múltban, ha a házirend van beállítva, az első balról jobbra biztonsági mentés készül rendszer következő év. 
3. Adatbázis visszaállítása a balról jobbra tárolóeszközről, jelölje ki a Timestamp típusú alapján meghatározott biztonsági másolatot.   Az adatbázis az eredeti adatbázis tárolóként ugyanazt az előfizetést a meglévő kiszolgálói állítható vissza. 
> 

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

Hosszú távú megőrzési az Azure portál használatával, vagy a PowerShell konfigurálásáról további tudnivalókért lásd: [konfigurálása hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>További lépések

Adatbázis biztonsági másolatait véletlen sérülése vagy a törlés adatok védelme, mert nagyon fontos részét képezik az üzletmenet folytonosságának és a vész-helyreállítási stratégiát fontosságúak. A más SQL-adatbázis üzleti folytonosságot biztosító megoldásokkal kapcsolatos további tudnivalókért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
