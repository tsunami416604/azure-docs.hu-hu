---
title: Az Azure SQL Database biztonsági mentéseinek Store akár 10 évig |} A Microsoft Docs
description: Ismerje meg, hogyan Azure SQL Database támogatja akár 10 évig tárolását teljes adatbázis biztonsági másolatait.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 0f764ebbad53185f46c7166011e05493ed261d6a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696644"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Az Azure SQL Database biztonsági mentéseinek Store akár 10 évig

Számos alkalmazás rendelkezik szabályozási, megfelelőségi vagy egyéb üzleti célra használja, amely esetében megtarthatja az adatbázisok biztonsági mentése az Azure SQL Database által biztosított 7 – 35 napon túl [automatikus biztonsági mentések](sql-database-automated-backups.md). A hosszú távú megőrzés (LTR) szolgáltatással tárolhatja a megadott SQL adatbázis teljes biztonsági mentés a [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) a blob storage akár 10 évig. A biztonsági másolatokat új adatbázisként állíthatja helyre.

> [!NOTE]
> Egyetlen vagy készletezett adatbázisok LTR engedélyezhető. Még nem érhető el például adatbázisok, a felügyelt példány van. Használhatja az SQL Agent-feladatok ütemezése [csak másolatot adatbázis biztonsági másolatait](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) alternatívájaként LTR 35 napos időszak letelte után.
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL-adatbázis hosszú távú megőrzés működése

Hosszú távú megőrzésének (LTR) használja az adatbázis teljes biztonsági mentéseket, amelyek [automatikusan létrehozott](sql-database-automated-backups.md) időpontban visszaállításának (PITR) engedélyezéséhez. Az LTR-szabályzat van konfigurálva, ha ezek a biztonsági mentések hosszú távú tárolás különböző blobok lesz másolva. A másolási művelet, amely az adatbázis-munkaterhelés nem teljesítményét befolyásolja háttérfeladat. Az LTR biztonsági mentések megmaradnak az LTR-szabályzat által egy ideig. Minden egyes SQL database LTR házirendje is megadhatja, hogy milyen gyakran jönnek létre az LTR biztonsági mentések. A rugalmasság a szabályzat négy paraméter együttes használatával adhatja meg: heti biztonsági másolatok megőrzése (W), havi biztonsági másolatok megőrzése (M), éves biztonsági másolatok megőrzése (Y), és hét év (WeekOfYear). Ha megad egy biztonsági mentés hetente W lesznek másolva a hosszú távú tároláshoz. M ad meg, ha egy biztonsági mentés során minden hónap első hetében másolandó a hosszú távú tároláshoz. Y ad meg, ha egy biztonsági mentés WeekOfYear által megadott hétre esik-e a hosszú távú tárolásra másolhatók. Minden egyes biztonsági másolat ezeket a paramétereket által megadott időszakban marad a hosszú távú tárolására. A későbbi biztonsági mentések minden olyan változás az LTR-szabályzat vonatkozik. Például ha a megadott WeekOfYear a múltban, ha a szabályzat van konfigurálva, az első LTR biztonsági mentés létrejön következő évben. 

Példák az LTR-szabályzat:

-  W=0, M=0, Y=5, WeekOfYear=3

   A harmadik teljes biztonsági mentés minden évben folyamatosan öt év során.
   
- W=0, M=3, Y=0

   Az első teljes biztonsági mentés minden hónapban a három hónapig megmarad.

- W=12, M=0, Y=0

   A heti teljes biztonsági 12 hetes marad.

- W=6, M=12, Y=10, WeekOfYear=16

   A heti teljes biztonsági hat hét alatt marad. Minden hónap első teljes biztonsági, kivéve, amely folyamatosan 12 hónapig. Kivételével a teljes készült biztonsági másolatok 16 év hete, amely folyamatosan 10 évig. 

Az alábbi táblázat azt mutatja be, a kiadása ütemben történik, és a lejárati a hosszú távú biztonsági mentését a következő szabályzatot:

W = 12 hetes (84 nap), M = 12 hónap (365 napos), Y = 10 év (3650 nap), WeekOfYear = 15 (hét után április 15.)

   ![az ltr-példa](./media/sql-database-long-term-retention/ltr-example.png)



Ha a fenti szabályzatot módosítja, és a set W = 0 (nincs heti biztonsági mentések), a kiadása ütemben történik a biztonsági másolatok változik jelenik meg a fenti táblázat kiemelt dátuma szerint. A szükséges, hogy ezeket a biztonsági másolatokat tároló összeg csökkentené a ennek megfelelően. 

> [!NOTE]
> Azure SQL Database az egyes LTR biztonsági mentés ütemezése határozza meg. Nem manuálisan LTR biztonsági másolatot készíteni, és a biztonsági mentés létrehozása szabályozásához.
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Georeplikáció és a hosszú távú adatmegőrzés

Aktív georeplikációs és feladatátvételi csoportok használ az üzleti folytonossági megoldásként, ha a végleges feladatátvétel előkészítése, és a ugyanazt az LTR-házirend konfigurálása a geo-secondary adatbázisra. Az LTR tárolási költsége nem nő, a biztonsági mentések nem jönnek létre, a másodlagos példány hozható létre. Csak akkor, ha a másodlagos elsődleges válik a biztonsági mentések jön létre. Az LTR biztonsági mentések nem megszakított generációja biztosítja azt, amikor a feladatátvételi akkor aktiválódik, és az elsődleges áthelyezi a másodlagos régióba. 

> [!NOTE]
> Ha az eredeti elsődleges adatbázis, amely a feladatátvétel miatt a kimaradás utáni állítja helyre, egy új másodlagos lesz belőle. Ezért a biztonsági mentés létrehozása nem folytatódik, és a meglévő LTR-szabályzat nem lépnek érvénybe, amíg az elsődleges újra lesz. 

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

Az Azure portal vagy a PowerShell használatával hosszú távú megőrzésének konfigurálása kapcsolatban lásd: [hosszú távú megőrzésének kezelése az Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Adatbázisban LTR biztonsági másolatból

Adatbázis visszaállítása az LTR-storage-ból, kiválaszthatja az időbélyegző alapján meghatározott biztonsági másolat. Az adatbázis az eredeti adatbázissal azonos előfizetéshez tartozó meglévő kiszolgáló visszaállítható. Megtudhatja, hogyan állíthatja vissza az adatbázist egy biztonsági mentésből balról jobbra, használja az Azure portal vagy a PowerShell-lel, tekintse meg a [hosszú távú megőrzésének kezelése az Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>További lépések

Adatbázisok biztonsági mentése a véletlen sérülés vagy törlés adatok védelme, mert azok bármely üzletmenet-folytonossági és vészhelyreállítási stratégia nagyon fontos részét. Az SQL Database üzletmenet-folytonossági megoldásait kapcsolatos további információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
