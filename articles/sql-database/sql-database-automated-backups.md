---
title: "Azure SQL adatbázis automatikus, georedundáns biztonsági mentések |} Microsoft Docs"
description: "SQL-adatbázis automatikusan létrehoz egy helyi adatbázis biztonsági másolatának néhány percenként és Azure írásvédett georedundáns tárolást használ georedundancia."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: 25fa8d03a206452c17e5d4ad38211203aeb5dd65
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="learn-about-automatic-sql-database-backups"></a>További tudnivalók az automatikus SQL-adatbázis biztonsági mentése

SQL-adatbázis automatikusan létrehozza és használ az Azure-írásvédett georedundáns tárolás (RA-GRS) földrajzi redundancia. Ezek a biztonsági másolatok jönnek létre automatikusan és nem kell külön fizetni. Nem kell tennie semmit, így ezek fordulhat elő. Adatbázis biztonsági mentése az üzleti folytonossági és vészhelyreállítási helyreállítási stratégia fontos részét képezik, mivel ezek az adatok védelméhez véletlen sérülése vagy törlése. Ha meg szeretné tartani a biztonsági mentések a saját tároló konfigurálhatja egy hosszú távú biztonsági mentés megőrzési házirend. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Mi az az SQL-adatbázis biztonsági másolatának?

SQL-adatbázis SQL Server technológiát használja, hozzon létre a [teljes](https://msdn.microsoft.com/library/ms186289.aspx), [különbözeti](https://msdn.microsoft.com/library/ms175526.aspx), és [tranzakciónapló](https://msdn.microsoft.com/library/ms191429.aspx) biztonsági mentéseket. A tranzakciónapló biztonsági mentései a teljesítményszintet és adatbázis-tevékenység gyakorisággal általában 5-10 percenként fordulhat elő. Tranzakciónapló biztonsági mentései, teljes és különbözeti biztonsági másolat, lehetővé teszi egy meghatározott-időpontban ugyanarra a kiszolgálóra, amelyen az adatbázis egy adatbázis visszaállítása. Ha egy adatbázis visszaállításához a szolgáltatás kimenő biztonsági mentések visszaállítandó teljes, különbségi és tranzakciós naplófájlokat számadatok.


A biztonsági mentések használhatók:

* Állítsa vissza egy adatbázist egy-időpontban a megőrzési időn belül. Ez a művelet egy új adatbázist fog létrehozni az eredeti adatbázist ugyanarra a kiszolgálóra.
* A törölt adatbázisok visszaállítása az idő törölve lett vagy a megőrzési időn belül. A törölt adatbázis visszaállítása ugyanarra a kiszolgálóra, amelyen létrehozták az eredeti adatbázist csak végezhető el.
* Állítsa vissza egy adatbázist egy másik földrajzi régióban. Ez lehetővé teszi, hogy ha a kiszolgáló és az adatbázis nem tud hozzáférni a földrajzi katasztrófa utáni helyreállítás. Minden meglévő kiszolgálóról a bárhol a világon hozna létre egy új adatbázist. 
* Adatbázis visszaállítása egy adott biztonsági másolatból az Azure Recovery Services-tároló tárolja. Ez lehetővé teszi, hogy egy régi verziója megfelelőségi kérelem teljesítéséhez vagy egy régi verziója, az alkalmazás futtatásához az adatbázis visszaállításához. Lásd: [hosszú távú megőrzési](sql-database-long-term-retention.md).
* A helyreállítás végrehajtásához tekintse meg [adatbázis visszaállítása biztonsági másolatból](sql-database-recovery-using-backups.md).

> [!NOTE]
> Az Azure storage kifejezés *replikációs* fájlok másolása egyik helyről egy másikra hivatkozik. SQL *adatbázis-replikáció* több másodlagos adatbázishoz egy elsődleges adatbázis szinkronizálva tartása hivatkozik. 
> 

## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Hány biztonsági másolatokat tároló megtalálható ingyenesen?
SQL-adatbázis biztonsági másolatok tárolásának további költségek nélkül biztosít a maximális kiosztott adatbázistár 200 %-kal. Például ha egy szabványos DB példány kiosztott DB méretű 250 GB-os, 500 GB van biztonsági másolat tárolási használatáért nem kell külön fizetni. 

## <a name="how-often-do-backups-happen"></a>Milyen gyakran történik a biztonsági mentések?
Adatbázis teljes biztonsági mentések fordulhat elő, hetente, adatbázis különbözeti biztonsági mentések általánosan fordulhat elő, minden néhány óra, és a tranzakciós napló biztonsági mentések általában 5 – 10 percenként kerül sor. Az első teljes biztonsági mentés van ütemezve, azonnal adatbázis létrehozása után. Általában 30 percen belül befejeződik, de azt is tovább tarthat jelentős méretű az adatbázis esetén. Például a kezdeti biztonsági másolatot több időt vesz igénybe a visszaállított adatbázis vagy egy adatbázis-másolat. Az első teljes biztonsági mentés óta az összes további biztonsági másolatot automatikusan ütemezett, és csendes felügyelni a háttérben. Az összes adatbázis biztonsági másolatait pontos időzítése határozza meg az SQL Database szolgáltatás a teljes munkaterhelés egyenlegének. 

A biztonsági mentési tároló földrajzi-replikáció az Azure Storage replikációs ütemezés alapján.

## <a name="how-long-do-you-keep-my-backups"></a>Mennyi ideig megtartja a biztonsági másolatok?
Minden egyes SQL-adatbázis biztonsági mentése van a megőrzési időtartam, amely azon alapul a [szolgáltatásréteg](sql-database-service-tiers.md) az adatbázis. Egy adatbázis megőrzési időtartama a:


* Alapszintű service érvényben lévő korlát miatt 7 nap.
* Standard szintű szolgáltatási rétegben 35 napon.
* Prémium szolgáltatásszintet 35 napon.

Ha használni az adatbázist a Standard vagy Premium szolgáltatásszintek a Basic, a biztonsági másolatok hét napja. Már nem érhetők el minden létező biztonsági másolatai, régebbi, mint hét nap. 

Történő frissítés után az adatbázis alapszintű rétegben Standard vagy prémium, SQL-adatbázis meglévő biztonsági másolatok tartja, amíg azokat régi 35 napon. Új biztonsági másolatok 35 napon előforduló tartja.

Ha töröl egy adatbázist, SQL-adatbázis a biztonsági mentések tartja azt szeretné, online adatbázis azonos módon. Tegyük fel, hogy törli a hét napos megőrzési idővel rendelkező alapszintű adatbázis. Olyan biztonsági mentésből négy napos három további napig kerül.

> [!IMPORTANT]
> Ha törli az Azure SQL-kiszolgálót futtató SQL-adatbázisok, a kiszolgálóhoz tartozó összes adatbázis is törlődnek, és nem állítható helyre. A Törölt kiszolgáló nem tudja visszaállítani.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Hogyan terjeszthető ki a biztonsági mentés megőrzési időszakot?
Ha az alkalmazás által igényelt, a biztonsági mentések érhetők el a hosszabb ideig kiterjesztheti a beépített megőrzési időszak úgy konfigurálja a hosszú távú biztonsági mentés megőrzési házirend az egyes adatbázisok (balról jobbra házirend). Ez lehetővé teszi, hogy terjessze ki a beépített informatikai megőrzési időtartam legfeljebb 10 év a 35 napon. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

Miután hozzáadta a balról jobbra házirend Azure-portálon vagy API-val egy adatbázist, a heti teljes adatbázis biztonsági mentései automatikusan kerülnek a saját Azure Backup szolgáltatás tárolóba. Ha az adatbázis TDE van titkosítva a rendszer automatikusan titkosítja a biztonsági mentések aktívan.  A Services-tárolónak automatikusan törli a lejárt biztonsági mentések időbélyegzőik és a balról jobbra házirend alapján.  Így nem kell a biztonsági mentés ütemezése kezeléséhez, vagy a karbantartás, régi fájlok foglalkoznia. A visszaállítási API támogatja a tárolóban található biztonsági másolataihoz, mindaddig, amíg a tárolóban van az SQL-adatbázis ugyanahhoz az előfizetéshez. Az Azure portálon vagy a PowerShell használatával is a biztonsági mentéseket hozzáférhetnek.

> [!TIP]
> Útmutató, váltásról [és az Azure SQL Database hosszú távú biztonsági mentés megőrzési visszaállítás konfigurálása](sql-database-long-term-backup-retention-configure.md)
>

## <a name="are-backups-encrypted"></a>Biztonsági mentés titkosítása?

Ha a TDE engedélyezve van az Azure SQL-adatbázis, a biztonsági mentések is titkosítást kapnak. Minden új Azure SQL-adatbázisok TDE alapértelmezés szerint engedélyezve vannak konfigurálva. A TDE további információkért lásd: [átlátható adattitkosítást az Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="next-steps"></a>Következő lépések

- Adatbázis biztonsági mentése az üzleti folytonossági és vészhelyreállítási helyreállítási stratégia fontos részét képezik, mivel ezek az adatok védelméhez véletlen sérülése vagy törlése. Az egyéb Azure SQL Database üzleti folytonossági megoldásokkal kapcsolatos további tudnivalókért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
- A pont visszaállítása az Azure portál használatával időpontra, lásd: [ponttá adatbázis visszaállítása az Azure portál használatával időpontra](sql-database-recovery-using-backups.md).
- Visszaállítása egy alkalommal PowerShell használatával, lásd: [adatbázis visszaállítása ponthoz, amikor PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurálásához, kezeléséhez, és állítsa vissza a hosszú távú megőrzési automatikus biztonsági mentés egy Azure Recovery Services-tárolóban az Azure portál használatával, lásd: [kezelése az Azure portál használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).
- Automatikus konfigurálásához, kezeléséhez, és állítsa vissza a hosszú távú megőrzési a az Azure Recovery Services-tároló PowerShell használatával a biztonsági másolatok című [kezelése a PowerShell használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).
