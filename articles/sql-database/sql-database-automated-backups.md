---
title: Azure SQL adatbázis automatikus, georedundáns biztonsági mentések |} Microsoft Docs
description: SQL-adatbázis automatikusan létrehoz egy helyi adatbázis biztonsági másolatának néhány percenként és Azure írásvédett georedundáns tárolást használ georedundancia.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="learn-about-automatic-sql-database-backups"></a>További tudnivalók az automatikus SQL-adatbázis biztonsági mentése

SQL-adatbázis automatikusan létrehozza és használ az Azure-írásvédett georedundáns tárolás (RA-GRS) földrajzi redundancia. Ezek a biztonsági másolatok jönnek létre automatikusan és nem kell külön fizetni. Nem kell tennie semmit, így ezek fordulhat elő. Adatbázis biztonsági mentése az üzleti folytonossági és vészhelyreállítási helyreállítási stratégia fontos részét képezik, mivel ezek az adatok védelméhez véletlen sérülése vagy törlése. Ha meg szeretné tartani a biztonsági mentések a saját tároló konfigurálhatja egy hosszú távú biztonsági mentés megőrzési házirend. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Mi az az SQL-adatbázis biztonsági másolatának?

SQL-adatbázis SQL Server technológiát használja, hozzon létre a [teljes](https://msdn.microsoft.com/library/ms186289.aspx), [különbözeti](https://msdn.microsoft.com/library/ms175526.aspx), és [tranzakciónapló](https://msdn.microsoft.com/library/ms191429.aspx) időpontban alkalmazásában biztonsági másolatok visszaállítása (PITR). A tranzakciónapló biztonsági mentései a teljesítményszintet és adatbázis-tevékenység gyakorisággal általában 5-10 percenként fordulhat elő. Tranzakciónapló biztonsági mentései, teljes és különbözeti biztonsági másolat, lehetővé teszi egy meghatározott-időpontban ugyanarra a kiszolgálóra, amelyen az adatbázis egy adatbázis visszaállítása. Ha egy adatbázis visszaállításához a szolgáltatás kimenő biztonsági mentések visszaállítandó teljes, különbségi és tranzakciós naplófájlokat számadatok.


A biztonsági mentések használhatók:

* Állítsa vissza egy adatbázist egy-időpontban a megőrzési időn belül. Ez a művelet egy új adatbázist fog létrehozni az eredeti adatbázist ugyanarra a kiszolgálóra.
* A törölt adatbázisok visszaállítása az idő törölve lett vagy a megőrzési időn belül. A törölt adatbázis visszaállítása ugyanarra a kiszolgálóra, amelyen létrehozták az eredeti adatbázist csak végezhető el.
* Állítsa vissza egy adatbázist egy másik földrajzi régióban. Ez lehetővé teszi, hogy ha a kiszolgáló és az adatbázis nem tud hozzáférni a földrajzi katasztrófa utáni helyreállítás. Minden meglévő kiszolgálóról a bárhol a világon hozna létre egy új adatbázist. 
* Ha az adatbázis nincs konfigurálva a hosszú távú adatmegőrzési állítsa vissza egy adatbázist egy hosszú távú biztonsági másolat. Ez lehetővé teszi, hogy egy régi verziója megfelelőségi kérelem teljesítéséhez vagy egy régi verziója, az alkalmazás futtatásához az adatbázis visszaállításához. Lásd: [hosszú távú megőrzési](sql-database-long-term-retention.md).
* A helyreállítás végrehajtásához tekintse meg [adatbázis visszaállítása biztonsági másolatból](sql-database-recovery-using-backups.md).

> [!NOTE]
> Az Azure storage kifejezés *replikációs* fájlok másolása egyik helyről egy másikra hivatkozik. SQL *adatbázis-replikáció* több másodlagos adatbázishoz egy elsődleges adatbázis szinkronizálva tartása hivatkozik. 
> 

## <a name="how-often-do-backups-happen"></a>Milyen gyakran történik a biztonsági mentések?
Adatbázis teljes biztonsági mentések fordulhat elő, hetente, adatbázis különbözeti biztonsági mentések általánosan fordulhat elő, minden néhány óra, és a tranzakciós napló biztonsági mentések általában 5 – 10 percenként kerül sor. Az első teljes biztonsági mentés van ütemezve, azonnal adatbázis létrehozása után. Általában 30 percen belül befejeződik, de azt is tovább tarthat jelentős méretű az adatbázis esetén. Például a kezdeti biztonsági másolatot több időt vesz igénybe a visszaállított adatbázis vagy egy adatbázis-másolat. Az első teljes biztonsági mentés óta az összes további biztonsági másolatot automatikusan ütemezett, és csendes felügyelni a háttérben. Az összes adatbázis biztonsági másolatait pontos időzítése határozza meg az SQL Database szolgáltatás a teljes munkaterhelés egyenlegének. 

A biztonsági mentési tároló földrajzi-replikáció az Azure Storage replikációs ütemezés alapján.

## <a name="how-long-do-you-keep-my-backups"></a>Mennyi ideig megtartja a biztonsági másolatok?
Minden egyes SQL-adatbázis biztonsági mentése van a megőrzési időtartam, amely az adatbázis a szolgáltatási szint alapul, és nem egyezik meg a [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és a [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md). 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>A DTU-alapú alapjául szolgáló vásárlási modell adatbázisának megőrzése
A DTU-alapú alapjául szolgáló vásárlási modell adatbázis megőrzési időtartama attól függ, hogy a szolgáltatási rétegben. Az adatbázis megőrzési időtartama a:

* Alapszintű service érvényben lévő korlát miatt 7 nap.
* Standard szintű szolgáltatási rétegben 35 napon.
* Prémium szolgáltatásszintet 35 napon.
* Általános célú érvényben lévő korlát miatt konfigurálhatók maximális 35 nap (alapértelmezés szerint 7 nap) *
* Üzleti kritikus szint (előzetes verzió) konfigurálhatók maximális 35 nap (alapértelmezés szerint 7 nap) *

\* Előzetes a biztonsági mentések megőrzési időtartam nem konfigurálható, 7 napra rögzített.

Egy adatbázis a biztonsági mentések hosszabb megőrzési rövidebb adatmegőrzési adatbázishoz át, a cél réteg megőrzési időnél régebbi összes létező biztonsági már nem érhetők el.

Ha hosszabb időtartamra adatbázis egy adatbázis egy rövidebb adatmegőrzési időtartammal frissít, SQL-adatbázis meglévő biztonsági másolatok tartja, mindaddig, amíg a hosszabb megőrzési időtartamot. 

Ha töröl egy adatbázist, SQL-adatbázis a biztonsági mentések tartja azt szeretné, online adatbázis azonos módon. Tegyük fel, hogy törli a hét napos megőrzési idővel rendelkező alapszintű adatbázis. Olyan biztonsági mentésből négy napos három további napig kerül.

> [!IMPORTANT]
> Ha törli az Azure SQL-kiszolgálót futtató SQL-adatbázisok, a kiszolgálóhoz tartozó összes adatbázis is törlődnek, és nem állítható helyre. A Törölt kiszolgáló nem tudja visszaállítani.

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>Adatbázis-megőrzési szabályai vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)

Az adatbázis biztonsági mentések tárolási rendelt a pont támogatja az SQL adatbázis idő visszaállítása (PITR) és a hosszú távú megőrzési (LTR) képességeit. Ez a tároló külön-külön az egyes adatbázisok számára lefoglalt és két külön adatbázis-díjak terheli. 

- **PITR**: egyedi biztonsági másolatok átmásolt az RA-GRS tárolóba adatbázis automatikusan vannak. A tároló mérete dinamikusan nő, amikor az új biztonsági mentéseket hoz létre.  A tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez, illetve a tranzakciónaplók 5 percenként másolt biztonsági másolataihoz használja a rendszer. A tároló fogyasztása attól függ, hogy módosítsa az adatbázis és a megőrzési időszak aránya. Konfigurálhatja az egyes adatbázisok 7 és 35 nap közötti külön megőrzési időtartamot. A minimális tárolási mennyiségű egyenlő 1 x adatméret külön díjfizetés nélkül valósul meg. Ez a mennyiség legtöbb adatbázis esetén elegendő 7 nap, a biztonsági mentések tárolására. További információkért lásd: [pont időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)
- **Balról jobbra**: SQL-adatbázis lehetővé teszi a hosszú távú megőrzési teljes biztonsági mentések konfigurálása legfeljebb 10 év. Ha balról jobbra házirend engedélyezve van, ezek biztonsági másolatai RA-GRS tárolási automatikusan, de megadhatja, milyen gyakran a biztonsági mentések kerülnek. Teljesíti-e több megfelelőségi követelményt, jelölje ki az eltérő megőrzési idejű a heti, havi vagy éves biztonsági mentésekhez. Ez a konfiguráció határozza meg az mennyi tárhelyre lesz a balról jobbra biztonsági mentéseket. A balról jobbra árképzési Számológép segítségével balról jobbra tárolási költségét. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="how-to-extend-the-backup-retention-period"></a>Hogyan terjeszthető ki a biztonsági mentés megőrzési időszakot?

Ha az alkalmazás által igényelt, hogy a biztonsági másolatok elérhetők a maximális PITR biztonsági mentés megőrzési időszaknál hosszabb ideig, konfigurálhatja az egyes adatbázisok (balról jobbra házirend) egy hosszú távú biztonsági mentés megőrzési házirend. Ez lehetővé teszi, hogy terjessze ki a beépített informatikai megőrzési időtartam legfeljebb 35 nappal a legfeljebb 10 év. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

Miután hozzáadta a balról jobbra házirend Azure-portálon vagy API-val egy adatbázist, a heti teljes adatbázis biztonsági mentései automatikusan másolódnak RA-GRS külön tárolót a hosszú távú megőrzési (balról jobbra tároló). Ha az adatbázis TDE van titkosítva a rendszer automatikusan titkosítja a biztonsági mentések aktívan. SQL-adatbázis automatikusan törli a lejárt biztonsági mentések időbélyegzőik és a balról jobbra házirend alapján. A házirend telepítése után, nem kell kezelheti a biztonsági mentési ütemezést, vagy a karbantartás, régi fájlok foglalkoznia. Az Azure portálon vagy a PowerShell segítségével megtekintése, állítsa vissza, vagy törölje a biztonsági mentéseket.

## <a name="are-backups-encrypted"></a>Biztonsági mentés titkosítása?

Ha a TDE engedélyezve van az Azure SQL-adatbázis, a biztonsági mentések is titkosítást kapnak. Minden új Azure SQL-adatbázisok TDE alapértelmezés szerint engedélyezve vannak konfigurálva. A TDE további információkért lásd: [átlátható adattitkosítást az Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>Az automatikus biztonsági mentésekhez GDPR megfelelő vannak?
Ha a biztonsági mentés személyes adatokat tartalmaz, amely függvényében általános Data Protection szabályozás (GDPR), meg kell fokozott biztonsági intézkedéseket a jogosulatlan hozzáférés elleni védelméhez. Ahhoz, hogy megfeleljenek a GDPR, szükségük van egy módszerre, az adatok tulajdonosai a kérelmek kezelésére anélkül, hogy a biztonsági másolatok eléréséhez.  A rövid távú biztonsági mentéshez, egy megoldás lehet, hogy csökkentse a biztonsági mentés a 30 napos ablakban, amelyet az az idő engedélyezett az adatok hozzáférési kérelmek befejezéséhez.  Ha hosszabb távú biztonsági mentés szükség, javasoljuk, hogy csak a "álnevesített" adatok tárolása a biztonsági másolatok. Például ha egy személy kapcsolatos adatok törölve vagy frissítve van szüksége, akkor nem kell törlése vagy a meglévő biztonsági másolatok frissítése. További információ a GDPR a bevált gyakorlat található [Adatszabályozást GDPR megfelelőségi](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html).

## <a name="next-steps"></a>További lépések

- Adatbázis biztonsági mentése az üzleti folytonossági és vészhelyreállítási helyreállítási stratégia fontos részét képezik, mivel ezek az adatok védelméhez véletlen sérülése vagy törlése. Az egyéb Azure SQL Database üzleti folytonossági megoldásokkal kapcsolatos további tudnivalókért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
- A pont visszaállítása az Azure portál használatával időpontra, lásd: [ponttá adatbázis visszaállítása az Azure portál használatával időpontra](sql-database-recovery-using-backups.md).
- Visszaállítása egy alkalommal PowerShell használatával, lásd: [adatbázis visszaállítása ponthoz, amikor PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurálásához, kezeléséhez, és állítsa vissza a hosszú távú megőrzési automatikus biztonsági mentés egy Azure Recovery Services-tárolóban az Azure portál használatával, lásd: [kezelése az Azure portál használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).
- Automatikus konfigurálásához, kezeléséhez, és állítsa vissza a hosszú távú megőrzési a az Azure Recovery Services-tároló PowerShell használatával a biztonsági másolatok című [kezelése a PowerShell használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).
