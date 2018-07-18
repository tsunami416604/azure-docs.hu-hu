---
title: Az Azure SQL Database biztonsági mentéseinek Store akár 10 évig |} A Microsoft Docs
description: Ismerje meg, hogyan Azure SQL Database támogatja akár 10 évig tárolását teljes adatbázis biztonsági másolatait.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 8edf66d8ee61b2d0896ed8249ea286b0f3de7de5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39093138"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Az Azure SQL Database biztonsági mentéseinek Store akár 10 évig

Számos alkalmazás rendelkezik szabályozási, megfelelőségi vagy egyéb üzleti célra használja, amely esetében megtarthatja az adatbázisok biztonsági mentése az Azure SQL Database által biztosított 7 – 35 napon túl [automatikus biztonsági mentések](sql-database-automated-backups.md). A hosszú távú megőrzés (LTR) szolgáltatással tárolhatja a megadott SQL adatbázis teljes biztonsági mentés a [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) a blob storage akár 10 évig. Biztonsági másolat új adatbázisként visszaállíthatja.

## <a name="how-sql-database-long-term-retention-works"></a>SQL-adatbázis hosszú távú megőrzés működése

Hosszú távú adatmegőrzés kihasználja a [az automatikus SQL Database biztonsági mentéseinek](sql-database-automated-backups.md) létrehozott időpontban visszaállításhoz (PITR) vesz igénybe. Minden egyes SQL-adatbázis hosszú távú adatmegőrzési konfigurálhatja, és adja meg, hogy milyen gyakran kell másolnia a biztonsági másolatok a hosszú távú tárolásra. A rugalmasság a szabályzat négy paraméter együttes használatával adhatja meg: heti biztonsági másolatok megőrzése (W), havi biztonsági másolatok megőrzése (M), éves biztonsági másolatok megőrzése (Y), és hét év (WeekOfYear). Ha megad egy biztonsági mentés hetente W lesznek másolva a hosszú távú tároláshoz. M ad meg, ha egy biztonsági mentés során minden hónap első hetében másolandó a hosszú távú tároláshoz. Y ad meg, ha egy biztonsági mentés WeekOfYear által megadott hétre esik-e a hosszú távú tárolásra másolhatók. Minden egyes biztonsági másolat ezeket a paramétereket által megadott időszakban marad a hosszú távú tárolására. 

Példák:

-  W = 0, WeekOfYear M = 0, Y = 5, = 3

   A 3. teljes biztonsági mentés minden évben 5 éves marad.
- W=0, M=3, Y=0

   Az első teljes biztonsági mentés minden hónapban, 3 hónapig megmarad.

- W=12, M=0, Y=0

   A heti teljes biztonsági 12 hetes marad.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   A heti teljes biztonsági 6 hétig fog tartani. Minden hónap első teljes biztonsági, kivéve, amely folyamatosan 12 hónapig. Kivételével a teljes készült biztonsági másolatok 16 év hete, amely folyamatosan 10 évig. 

Az alábbi táblázat azt mutatja be, a kiadása ütemben történik, és a lejárati a hosszú távú biztonsági mentését a következő szabályzatot:

W = 12 hetes (84 nap), M = 12 hónap (365 napos), Y = 10 év (3650 nap), WeekOfYear = 15 (hét után április 15.)

   ![az ltr-példa](./media/sql-database-long-term-retention/ltr-example.png)


 
Ha elveszne a fenti házirend módosítása és a set W = 0 (nincs heti biztonsági mentések), a kiadása ütemben történik a biztonsági másolatok akkor változik, ahogy látható a fenti táblázat kiemelt dátuma szerint. A szükséges, hogy ezeket a biztonsági másolatokat tároló összeg csökkentené a ennek megfelelően. 

> [!NOTE]
1. Az LTR-példányt, a másolási folyamat nem befolyásolja teljesítmény a meglévő adatbázis hozzák létre az Azure storage szolgáltatás.
2. A későbbi biztonsági mentések a szabályzat vonatkozik. Például Ha a megadott WeekOfYear a múltban, ha a szabályzat van konfigurálva, az első LTR biztonsági mentés létrejön következő évben. 
3. Adatbázis visszaállítása az LTR-storage-ból, kiválaszthatja az időbélyegző alapján meghatározott biztonsági másolat.   Az adatbázis az eredeti adatbázissal azonos előfizetéshez tartozó meglévő kiszolgáló visszaállítható. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Georeplikáció és a hosszú távú adatmegőrzés

Ha az aktív georeplikáció útján vagy a feladatátvételi csoport használja, az üzleti folytonossági megoldást kell végleges feladatátvétel előkészítése és a ugyanazt az LTR-házirend konfigurálása a geo-secondary adatbázisra. Nem ez megnöveli az LTR tárolási költségeket, amikor biztonsági mentések nem jönnek létre, a másodlagos példány hozható létre. Csak akkor, ha a másodlagos elsődleges válik a biztonsági mentések jön létre. Ezzel a módszerrel az LTR biztonsági mentések generációja nem megszakad a feladatátvétel elindításakor garantálja és az elsődleges áthelyezi a másodlagos régióba. 

> [!NOTE]
Az eredeti elsődleges adatbázist helyreállítja a feladatátvétel okozó meghibásodás után, amikor egy új másodlagos lesz belőle. Ezért a biztonsági mentés létrehozása nem folytatódik, és a meglévő LTR-szabályzat nem lépnek érvénybe, amíg az elsődleges újra lesz. 
> 

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

Megtudhatja, hogyan konfigurálhatja a hosszú távú megőrzése az Azure portal használatával, vagy a PowerShell-lel, tekintse meg a [hosszú távú megőrzés konfigurálásához](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>További lépések

Adatbázisok biztonsági mentése a véletlen sérülés vagy törlés adatok védelme, mert azok bármely üzletmenet-folytonossági és vészhelyreállítási stratégia nagyon fontos részét. Az SQL Database üzletmenet-folytonossági megoldásait kapcsolatos további információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
