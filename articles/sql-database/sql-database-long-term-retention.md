---
title: "Azure SQL-adatbázis biztonsági másolatait tárolni legfeljebb tíz éve |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure SQL Database támogatja legfeljebb tíz éve tárolni a biztonsági mentéseket."
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: 2f31e89fce2746e57d6a670aef949d0d534af4c1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL-adatbázis biztonsági másolatait tárolni legfeljebb 10 év
Számos alkalmazás rendelkezik szabályozó, megfelelőségi vagy egyéb üzleti célokat szolgál, amelyek megkövetelik a tartsa meg az adatbázis biztonsági mentése az Azure SQL Database által biztosított 7-35 napon túl [automatikus biztonsági mentésekhez](sql-database-automated-backups.md). A hosszú távú biztonsági másolatok megőrzésének szolgáltatás segítségével tárolhatja az SQL-adatbázis biztonsági mentése az Azure Recovery Services-tároló legfeljebb tíz éve. Legfeljebb 1000 adatbázisokat tároló száma tárolhatja. Ezután kiválaszthatja biztonsági másolat új adatbázisként visszaállítására a tárolóban lévő állapottal.

> [!IMPORTANT]
> Hosszú távú biztonsági másolatok megőrzésének jelenleg jelenleg előzetes és a következő régiókban elérhető: Kelet-Ausztrália, Ausztrália délkeleti, Dél-Brazília, USA középső RÉGIÓJA, Kelet-Ázsia, USA keleti régiója, USA keleti régiója 2. régiója, közép-India, Dél-India, kelet-japán, Nyugat-japán, északi középső Régiójában, Észak Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsiában, Nyugat-Európában és USA nyugati régiója
>

> [!NOTE]
> Engedélyezheti / tárolóban legfeljebb 200 adatbázisok egy 24 órás időszakban. Azt javasoljuk, hogy kiszolgálónként egy külön tárolóba használja-e ezt a határt hatás minimalizálása érdekében. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>SQL-adatbázis hosszú távú biztonsági másolatok megőrzésének működése

A hosszú távú biztonsági másolatok megőrzésének az Azure Recovery Services-tároló SQL adatbázis-kiszolgálót is társíthat. 

* Az azonos Azure-előfizetés, hozott létre az SQL server és az ugyanabban a földrajzi régióban erőforráscsoport, létre kell hoznia a tárolóban. 
* Konfigurálhat egy megőrzési házirend bármely adatbázis. A házirend hatására a heti teljes adatbázis biztonsági másolatait a Recovery Services-tároló másolható és őrzi meg a megadott megőrzési időszak (legfeljebb 10 év). 
* Majd helyreállíthatja az adatbázis a biztonsági mentése bármelyik az előfizetés bármely kiszolgáló új adatbázishoz. Az Azure storage létrehoz egy másolatot a meglévő biztonsági mentésekből, és a példány nincs hatása van a létező adatbázis felett.

> [!TIP]
> Útmutató, váltásról [és konfigurálása az Azure SQL Database hosszú távú biztonsági mentés megőrzési visszaállítás](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Hosszú távú biztonsági másolatok megőrzésének engedélyezése

Hosszú távú biztonsági másolatok megőrzésének adatbázis konfigurálása:

1. Hozzon létre egy Azure Recovery Services-tárolónak a ugyanabban a régióban, az előfizetés és az erőforráscsoport SQL adatbázis-kiszolgálót. 
2. Regisztrálja a kiszolgálót a tárolón.
3. Hozzon létre egy Azure Recovery Services védelmi házirendet.
4. A védelmi házirend alkalmazása a hosszú távú biztonsági másolatok megőrzésének igénylő adatbázisokhoz.

Konfigurálásához, kezeléséhez, és állítsa vissza egy adatbázist egy Azure Recovery Services-tároló az automatikus biztonsági másolatok hosszú távú biztonsági másolatok megőrzésének, tegye a következők:

* Az Azure portál használatával: kattintson a **hosszú távú biztonsági másolatok megőrzésének**, válasszon ki egy adatbázist, és kattintson a **konfigurálása**. 

   ![Válasszon egy adatbázist, a hosszú távú biztonsági másolatok megőrzésének](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* PowerShell használatával: Ugrás a [és konfigurálása az Azure SQL Database hosszú távú biztonsági mentés megőrzési visszaállítás](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>A hosszú távú biztonsági másolatok megőrzésének szolgáltatással tárolt adatbázis visszaállítása

A hosszú távú biztonsági másolatok megőrzésének biztonsági mentés helyreállítása:

1. A tároló, a biztonsági mentési tároló listában.
2. Listázza a tárolóhoz, amelybe a logikai kiszolgálóhoz van rendelve.
3. Az adatforrás a tárolóban, az adatbázis leképezett belül listában.
4. A helyreállítási pontok, amelyek segítségével listázza.
5. Állítsa vissza az adatbázist a helyreállítási pont a célkiszolgálóra, az előfizetésen belül.

Konfigurálásához, kezeléséhez, és állítsa vissza egy adatbázist egy Azure Recovery Services-tároló az automatikus biztonsági másolatok hosszú távú biztonsági másolatok megőrzésének, tegye a következők:

* Az Azure portál használatával: Ugrás [kezelése az Azure portál használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md). 

* PowerShell használatával: Ugrás a [kezelése a PowerShell használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>A hosszú távú biztonsági másolatok megőrzésének Árképzés beolvasása

Hosszú távú biztonsági másolatok megőrzésének SQL-adatbázis megfelelően fel van töltve a [díjszabás árképzési Azure biztonsági mentési szolgáltatások](https://azure.microsoft.com/pricing/details/backup/).

Az SQL-adatbáziskiszolgáló a tárolóba való regisztrálása után van szó, a heti biztonsági mentései a tárolóban tárolt által használt tárhelyet.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Elérhető biztonsági másolatok megtekintése, hosszú távú biztonsági másolatok megőrzésének vannak tárolva

Konfigurálásához, kezeléséhez, és az Azure portál használatával állítsa vissza egy adatbázist egy Azure Recovery Services-tároló az automatikus biztonsági másolatok hosszú távú biztonsági másolatok megőrzésének, tegye a következők:

* Az Azure portál használatával: Ugrás [kezelése az Azure portál használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md). 

* PowerShell használatával: Ugrás a [kezelése a PowerShell használatával hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Hosszú távú megőrzési letiltása

A helyreállítási szolgáltatás automatikusan kezeli a biztonsági mentések a megadott megőrzési házirend alapján karbantartása. 

Állítsa le a tároló egy adott adatbázis biztonsági mentésének küld, távolítsa el az adatmegőrzési, hogy az adatbázis.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> A biztonsági mentések, amelyek már a tárolóban nem érinti. Automatikusan akkor azok törlődnek a helyreállítási szolgáltatás által, ha a megőrzési időtartam lejár.

## <a name="long-term-backup-retention-faq"></a>Hosszú távú biztonsági másolatok megőrzésének – gyakori kérdések

**I manuálisan törölheti a tárolóban lévő meghatározott biztonsági mentések?**

Jelenleg nem. A tároló automatikusan törli azokat a biztonsági mentés a megőrzési időszak lejárta.

**Regisztrálhatja a saját kiszolgáló egynél több tárolóba biztonsági másolatok tárolására?**

Egyszerre nem, biztonsági másolatok csak egy tárolóba jelenleg tárolhatja.

**Is kell a tároló és a kiszolgáló különböző előfizetésekhez?**

Nem, jelenleg a tároló és a kiszolgáló szerepelnie kell a ugyanabban az előfizetésben és erőforráscsoportban.

**Használhatók egy olyan, amely eltér a kiszolgálója régió régióban létrehozott tárolóból?**

Nem, a tároló és a kiszolgáló másolási idő minimalizálása és a forgalom díjak elkerülése ugyanabban a régióban kell lennie.

**Hány adatbázisokat is tárolnak a külön-külön tárolóba?**

Jelenleg támogatott / tárolóban legfeljebb 1000 adatbázisok. 

**Hány tárolók hozhat létre előfizetésenként?**

Előfizetésenként legfeljebb 25 tárolók hozhat létre.

**Hány adatbázisokat is konfigurálja / nap / tároló?**

200 adatbázisok / nap / tároló állíthat be.

**Hosszú távú biztonsági másolatok megőrzésének rugalmas készletek működik?**

Igen. A készlet valamelyik adatbázisban az adatmegőrzési konfigurálható.

**Választhat, ahol a biztonsági másolat létrehozásakor?**

Nem, SQL-adatbázis az adatbázisok a teljesítményre gyakorolt hatás minimalizálása érdekében a biztonsági mentés ütemezése határozza meg.

**Átlátható adattitkosítás az adatbázis engedélyezve van. Használhatok azt a tárolót?** 

Igen, támogatja a transzparens adattitkosítás. Helyreállíthatja az adatbázist a tárolóból, még akkor is, ha az eredeti adatbázist már nem létezik.

**Mi történik a biztonsági másolat a tárolóban, ha az előfizetés fel van függesztve?** 

Ha az előfizetés fel van függesztve, azt a meglévő adatbázisok és a biztonsági másolatok megőrzése mellett. A tároló nem kerülnek új biztonsági másolatok. Az előfizetés újraaktiválásához után a szolgáltatás folytatása biztonsági mentések másolása a tárolóba A tároló a biztonsági mentések másolt nincs előtt fel lett függesztve, az előfizetés használatával a visszaállítási műveletek számára elérhető lesz. 

**Kaphatok az SQL-adatbázis biztonsági mentési fájlok eléréséhez, így képes letölteni vagy visszaállítja azokat az SQL server?**

Nem, jelenleg nem.

**Az SQL adatmegőrzési belül több ütemezés (napi, heti, havi, évente) szerepelhet.**

Nem, több ütemezések jelenleg csak virtuális gépek biztonsági mentéseinek érhető el.

**Mi történik, ha hosszú távú biztonsági másolatok megőrzésének olyan adatbázisban, amely egy aktív georeplikáció másodlagos adatbázis beállítani?**

A replikák biztonsági mentések nem vesszük, mert jelenleg nincs beállítás, a hosszú távú biztonsági másolatok megőrzésének a másodlagos adatbázisok. Azonban fontos, a felhasználók számára egy aktív georeplikáció másodlagos adatbázison hosszú távú biztonsági másolatok megőrzésének beállítása ezen okok miatt:
* Ha feladatátvétel történik, és az adatbázis elsődleges adatbázis válik, azt egy teljes biztonsági másolatok készítéséhez, amely feltöltött tároló.
* Nincs ingyenesen az ügyfélnek a másodlagos adatbázison hosszú távú biztonsági másolatok megőrzésének beállítása.

## <a name="next-steps"></a>További lépések
Adatbázis biztonsági másolatait véletlen sérülése vagy a törlés adatok védelme, mert nagyon fontos részét képezik az üzletmenet folytonosságának és a vész-helyreállítási stratégiát fontosságúak. A más SQL-adatbázis üzleti folytonosságot biztosító megoldásokkal kapcsolatos további tudnivalókért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
