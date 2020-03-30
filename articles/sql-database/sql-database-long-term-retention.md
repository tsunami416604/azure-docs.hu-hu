---
title: Akár 10 évig tárolhatja a biztonsági mentéseket
description: Ismerje meg, hogy az Azure SQL Database hogyan támogatja a teljes adatbázis-biztonsági mentések akár 10 évig tartó tárolását.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 15a2d58d2fc14c370c41d5454d62c74a5b66ad42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499983"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL Database-beli biztonsági másolatok tárolása akár 10 éven át

Számos alkalmazás rendelkezik szabályozási, megfelelőségi vagy egyéb üzleti célokkal, amelyek megkövetelik, hogy az Azure SQL Database automatikus biztonsági mentései által biztosított 7–35 napon túl is megőrizzék az adatbázis-biztonsági [mentéseket.](sql-database-automated-backups.md) A hosszú távú megőrzési (LTR) szolgáltatás használatával tárolhatja a megadott SQL-adatbázis teljes biztonsági mentését az Azure Blob storage olvasási hozzáférésű georedundáns tárolással akár 10 évig. Ezután visszaállíthatja a biztonsági másolatot új adatbázisként. Az Azure Storage redundanciájáról az [Azure Storage redundanciája](../storage/common/storage-redundancy.md)című témakörben talál további információt.

> [!NOTE]
> Az LTR engedélyezhető az egy- és összevont adatbázisokhoz. Még nem érhető el a felügyelt példányok példányaiban lévő például adatbázisokhoz. Az SQL Agent-feladatok segítségével [ütemezheti a csak másolási adatbázis biztonsági mentéseit](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) az LTR 35 napon túli alternatívájaként.
> 

## <a name="how-sql-database-long-term-retention-works"></a>A hosszú távú megőrzés működése az SQL Database-ben

A hosszú távú biztonsági mentés megőrzése (LTR) a teljes adatbázis-biztonsági mentéseket használja, amelyek [automatikusan létrejönnek](sql-database-automated-backups.md) a pont-idő visszaállítás (PITR) engedélyezéséhez. Ha egy LTR-házirend van konfigurálva, ezek a biztonsági mentések másolja a különböző blobok hosszú távú tárolásra. A másolat egy háttérfeladat, amely nincs hatással az adatbázis-munkaterhelés teljesítményére. Az egyes SQL-adatbázisok LTR-házirendje azt is megadhatja, hogy milyen gyakran jönnek létre az LTR-biztonsági mentések.

Az LTR engedélyezéséhez négy paraméter kombinációjával definiálhat házirendet: heti biztonsági mentésmegőrzés (W), havi biztonsági mentésmegőrzés (M), éves biztonsági mentésmegőrzés (Y) és az év hetében (WeekOfYear). Ha w értéket ad meg, a rendszer minden héten egy biztonsági másolatot másol a hosszú távú tárhelyre. Ha m értéket ad meg, a program minden hónap első biztonsági másolatát átmásolja a hosszú távú tárhelyre. Ha y értéket ad meg, a WeekOfYear által megadott héten egy biztonsági mentés kerül a hosszú távú tárolóba. Ha a megadott WeekOfYear a házirend konfigurálásakor már a múltban van, az első LTR biztonsági mentés a következő évben jön létre. Minden biztonsági mentés a hosszú távú tárolóban lesz az LTR biztonsági mentés létrehozásakor konfigurált házirend-paramétereknek megfelelően.

> [!NOTE]
> Az LTR-házirend bármilyen módosítása csak a jövőbeli biztonsági mentések esetén érvényes. Ha például a heti biztonsági mentés megőrzése (W), havi biztonsági mentés megőrzése (M) vagy az éves biztonsági mentés megőrzése (Y) módosul, az új megőrzési beállítás csak az új biztonsági mentések. A meglévő biztonsági mentések megőrzése nem módosul. Ha a szándék az, hogy törölje a régi LTR biztonsági mentéseket, mielőtt azok megőrzési időszaklejár, akkor manuálisan kell [törölnie a biztonsági másolatokat.](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)
> 

Példák az LTR-házirendre:

-  W=0, M=0, Y=5, Hétév=3

   Minden év harmadik teljes biztonsági mentését öt évig őrizik meg.
   
- W=0, M=3, Y=0

   Minden hónap első teljes biztonsági mentése három hónapig tart.

- W=12, M=0, Y=0

   Minden heti teljes biztonsági mentés 12 hétig marad.

- W=6, M=12, Y=10, Hétév=16

   Minden heti teljes biztonsági mentés tartjuk hat hétig. Kivéve minden hónap első teljes biztonsági mentését, amit 12 hónapig őrizünk meg. Kivéve a teljes mentést az év 16. 

Az alábbi táblázat a hosszú távú biztonsági mentések ütemét és lejáratát mutatja be a következő szabályzathoz:

W=12 hét (84 nap), M=12 hónap (365 nap), Y=10 év (3650 nap), WeekOfYear=15 (hét április 15.)

   ![Ltr példa](./media/sql-database-long-term-retention/ltr-example.png)



Ha módosítja a fenti házirendet, és a W=0-t állítja be (nincs heti biztonsági másolat), a biztonsági másolatok üteme a fenti táblázatban látható módon változik a kiemelt dátumok szerint. A biztonsági mentések megtartásához szükséges tárterület ennek megfelelően csökkenne. 

> [!IMPORTANT]
> Az egyes LTR-biztonsági mentések időzítését az Azure SQL Database szabályozza. Az LTR-biztonsági mentés manuálisan nem hozható létre, és nem szabályozhatja a biztonsági másolat létrehozásának időzítését. Az LTR-házirend konfigurálása után akár 7 napig is eltarthat, amíg az első LTR biztonsági mentés megjelenik a rendelkezésre álló biztonsági mentések listájában.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Georeplikáció és hosszú távú biztonsági mentés megőrzése

Ha aktív georeplikációs vagy feladatátvételi csoportokat használ üzletmenet-folytonossági megoldásként, elő kell készítenie a végleges feladatátvételre, és ugyanazt az LTR-házirendet kell konfigurálnia a geoszektion adatbázison. Az LTR tárolási költsége nem fog növekedni, mivel a biztonsági mentések nem a másodlagos jelentésekből jönnek létre. Csak akkor, ha a másodlagos lesz az elsődleges a biztonsági mentések jön létre. Biztosítja az LTR biztonsági mentések megszakítás nélküli létrehozását, amikor a feladatátvétel aktiválódik, és az elsődleges áthelyezi a másodlagos régióba. 

> [!NOTE]
> Amikor az eredeti elsődleges adatbázis helyreáll egy kimaradás, amely a feladatátvételt okozta, ez lesz az új másodlagos. Ezért a biztonsági mentés létrehozása nem folytatódik, és a meglévő LTR-házirend nem lép érvénybe, amíg nem válik újra az elsődlegesvé. 

## <a name="configure-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének konfigurálása

A hosszú távú adatmegőrzés azure portalon vagy a PowerShellen keresztül történő konfigurálásáról az [Azure SQL Database hosszú távú biztonsági mentésének kezelése című](sql-database-long-term-backup-retention-configure.md)témakörben olvashat.

## <a name="restore-database-from-ltr-backup"></a>Adatbázis visszaállítása ltr biztonsági másolatból

Ha vissza szeretne állítani egy adatbázist az LTR-tárolóból, az időbélyeg alapján kiválaszthat egy adott biztonsági másolatot. Az adatbázis visszaállítható bármely meglévő kiszolgálóra az eredeti adatbázissal azonos előfizetéssel. Ha meg szeretné tudni, hogyan állíthatja vissza az adatbázist egy LTR biztonsági másolatból az Azure Portalon vagy a PowerShellen keresztül, olvassa [el az Azure SQL Database hosszú távú biztonsági mentésének kezelése](sql-database-long-term-backup-retention-configure.md)című témakört.

## <a name="next-steps"></a>További lépések

Mivel az adatbázis biztonsági mentései védik az adatokat a véletlen sérülés vagy törlés ellen, minden üzletmenet-folytonossági és vész-helyreállítási stratégia lényeges részét képezik. Az SQL Database üzletmenet-folytonossági megoldásairól az [Üzletmenet folytonosságának áttekintése című témakörben](sql-database-business-continuity.md)olvashat.
