---
title: Az Azure SQL Server-adatbázisok biztonsági mentése |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan SQL Server biztonsági mentése az Azure-bA. A cikk azt is bemutatja, az SQL Server helyreállítása.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847299"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Információk az Azure-beli virtuális gépeken futó SQL Server Backupról

Az SQL Server-adatbázisok olyan alacsony a helyreállításipont-célkitűzés (RPO) és hosszú távú megőrzés igénylő kritikus fontosságú számítási feladatokhoz. Használata Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentését végezheti [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Biztonsági mentési folyamat

Ez a megoldás az SQL natív API-k érvénybe az SQL-adatbázisok biztonsági másolatait használja.

* Adja meg az SQL Server virtuális gép, amelyeket szeretne védeni, és lekérdezéseket az informatikai adatbázisai, miután Azure Backup szolgáltatás telepíti a számítási feladatok biztonsági mentési bővítményt a virtuális gép neve `AzureBackupWindowsWorkload`  bővítmény.
* Ez a bővítmény koordinátor és a egy SQL-beépülő modul áll. Bár a koordinátor különféle műveletek, például konfigurálja a biztonsági mentés, biztonsági mentési és visszaállítási munkafolyamatok kiváltásáért felelős, a beépülő modul felelős tényleges adatfolyam.
* Az, hogy a virtuális gépen adatbázisok felderítéséhez, az Azure Backup hoz létre a fiók `NT SERVICE\AzureWLBackupPluginSvc`. Ez a fiók biztonsági mentési és visszaállítási használja, és az SQL-rendszergazdai engedélyekkel kell rendelkeznie. Az Azure Backup használja a `NT AUTHORITY\SYSTEM` adatbázis felderítési/lekérdezési, így ennek a fióknak kell lennie egy nyilvános bejelentkezés az SQL-fiók. Ha az SQL Server rendszerű virtuális gép az Azure Marketplace-ről nem hozott létre, akkor előfordulhat, hogy megjelenik egy hibaüzenet **UserErrorSQLNoSysadminMembership**. Ha ez történik [az alábbi lépéseket követve](backup-azure-sql-database.md).
* Eseményindító, konfigurálja a védelmet a kiválasztott adatbázisok, miután a biztonsági mentési szolgáltatás beállítja a koordinátor más házirend részletei, amelyek a bővítmény helyben gyorsítótárazza a virtuális gép és a biztonsági mentés ütemezése 
* Az ütemezett időpontban a koordinátor a beépülő modul kommunikál, és elindítja a streamelési VDI használata SQL-kiszolgálóról a biztonsági mentési adatok.  
* A beépülő modul elküldi az adatokat közvetlenül a recovery services-tároló, így kiküszöböli az előkészítési hely van szükség. Az adatok titkosítva és a storage-fiókok az Azure Backup szolgáltatás által tárolt.
* Ha az adatátvitel befejeződött, koordinátor megerősíti, hogy a biztonsági mentési szolgáltatásban a véglegesítés.

  ![SQL biztonsági mentési architektúra](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, ellenőrizze a következőket:

1. Győződjön meg arról, hogy rendelkezik Azure-ban futó SQL Server-példányt. Is [gyorsan létrehozhat egy SQL Server-példány](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) a Marketplace-en.
2. Tekintse át a [szempont funkció](#feature-consideration-and-limitations) és [forgatókönyv támogatási](#scenario-support).
3. [Tekintse át a gyakori kérdéseket](faq-backup-sql-server.md) erről a forgatókönyvről.

## <a name="scenario-support"></a>A forgatókönyv támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | SQL piactér Azure-beli és nem Marketplace (manuálisan telepített SQL Server) virtuális gépek támogatottak.
**Támogatott helyeken** | Délkelet-Ausztrália (ASE), Kelet-Ausztrália (AE) <br> Dél-Brazília (BRS)<br> Közép-Kanada (CNC), kelet-Kanada (CE)<br> Délkelet-Ázsia (SEA), Kelet-Ázsia (EA) <br> USA keleti RÉGIÓJA (EUS), USA keleti RÉGIÓJA (EUS2), USA nyugati középső RÉGIÓJA (WCUS), USA nyugati RÉGIÓJA (WUS); 2 USA nyugati RÉGIÓJA (WUS 2) 2 USA északi középső RÉGIÓJA (NCUS) USA középső RÉGIÓJA (CUS) déli középső RÉGIÓJA (SCUS) <br> Közép-India (INC), Dél-India (INS) <br> Kelet-japán (JPE), Nyugat-japán (JPW) <br> Korea középső régiója (KRC), Korea déli régiója (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli régiója (UKS), az Egyesült Királyság nyugati régiója (UKW)
**Támogatott operációs rendszerek** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux jelenleg nem támogatott.
**Támogatott SQL Server-verziók** | Az SQL Server 2017; SQL Server 2016-ban, SQL Server 2014, SQL Server 2012-ben.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Támogatott .NET-verziók** | .NET-keretrendszer 4.5.2-es verziója és az újabb verzióját a virtuális gépen

## <a name="feature-consideration-and-limitations"></a>A szolgáltatás szempontok és korlátozások

- Az Azure Portalon konfigurálhatja az SQL Server biztonsági másolat vagy **PowerShell**. Nem támogatjuk a parancssori felület.
- Az SQL Server rendszerű virtuális Gépet az Azure nyilvános IP-címek eléréséhez internetkapcsolatra van szükség.
- Az SQL Server **feladatátvételi fürtbeli példány (FCI)** és SQL Server-alapú folyamatos a Feladatátvevőfürt-példány használata nem támogatott.
- Tükrözött adatbázisok és az adatbázis-pillanatképeket a biztonsági mentési és visszaállítási műveletek nem támogatottak.
- Egynél több biztonsági mentési megoldások biztonsági másolatot készíteni az önálló SQL Server példányt, vagy az SQL Always on rendelkezésre állási csoport vezethet, a biztonsági mentési hiba; így tartózkodik.
- Biztonsági másolatot egy rendelkezésre állási csoport külön-külön az azonos vagy eltérő megoldások két csomópontot, a biztonsági is vezethet. Az Azure Backup a is észleli, és a a tárral azonos régióban lévő összes csomópont védelmét. Ha az SQL Server mindig a rendelkezésre állási csoport is kiterjed a több Azure-régióban, állítsa be a biztonsági mentés a régióból, amely rendelkezik az elsődleges csomópont. Az Azure Backup a is észleli, és a biztonsági mentési preferenciáját szerint a rendelkezésre állási csoportban található összes adatbázis védelmét.  
- Csak teljes biztonsági mentés az Azure támogatja, és csak másolatot teljes biztonsági mentési típusai **csak olvasható** adatbázisok
- Adatbázisok nagy számú fájl esetén nem biztosítható. A fájlok maximális számát, amelyet támogat a **~ 1000**.  
- Akár biztonsági **nagyjából 2000** SQL Server-adatbázisok a tárolóban. Több tároló hozható létre, amennyiben rendelkezik a nagyobb adatbázisok száma.
- Konfigurálhatja a biztonsági mentés akár **50** adatbázisok egy nyissa meg; ez a korlátozás segít optimalizálni a biztonsági mentési terhelések.
- Akár támogatjuk az adatbázisok **2 TB-os** méretű; számára, hogy nagyobb méretű, a teljesítménnyel kapcsolatos problémák is kapja meg.
- Már ismeri, feltárhatja, hogy hány adatbázist kiszolgálónként lehet védeni, kell figyelembe venni a tényezőket, például sávszélesség, Virtuálisgép-méretet, biztonsági mentés gyakoriságát, adatbázis mérete, stb. A planner, amelyek kiszámítása ezeket a felhasználó tulajdonában segítené is dolgozunk. Azt fogja kell közzétételével, kis türelmet kérünk.
- Rendelkezésre állási csoportok esetén a biztonsági mentések néhány tényezők alapján különböző csomópontjából kerül. A rendelkezésre állási csoport biztonsági mentési viselkedés összefoglalása.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Biztonsági mentési viselkedés esetén mindig rendelkezésre állási csoportok

Attól függően, a biztonsági mentési preferenciáját és a biztonsági mentések típusok (teljes és különbségi/log/csak másolatot teljes) a biztonsági mentések kerül egy adott csomópontról (elsődleges-másodlagos).

- **Biztonsági mentési preferenciáját: Elsődleges**

**Biztonsági mentés típusa** | **Node**
    --- | ---
    Korlátlan | Elsődleges
    Különbségi | Elsődleges
    Napló |  Elsődleges
    Csak másolatot teljes |  Elsődleges

- **Biztonsági mentési preferenciáját: Csak másodlagos**

**Biztonsági mentés típusa** | **Node**
--- | ---
Korlátlan | Elsődleges
Különbségi | Elsődleges
Napló |  Másodlagos
Csak másolatot teljes |  Másodlagos

- **Biztonsági mentési preferenciáját: Másodlagos**

**Biztonsági mentés típusa** | **Node**
--- | ---
Korlátlan | Elsődleges
Különbségi | Elsődleges
Napló |  Másodlagos
Csak másolatot teljes |  Másodlagos

- **Nincs biztonsági mentés preferencia**

**Biztonsági mentés típusa** | **Node**
--- | ---
Korlátlan | Elsődleges
Különbségi | Elsődleges
Napló |  Másodlagos
Csak másolatot teljes |  Másodlagos

## <a name="fix-sql-sysadmin-permissions"></a>Javítsa ki az SQL-rendszergazdai engedélyek

  Ha szeretné javítani az engedélyek miatt egy **UserErrorSQLNoSysadminMembership** hiba, tegye a következőket:

  1. Az SQL Server SysAdmin (rendszergazda) engedélyekkel rendelkező fiók használatával jelentkezzen be az SQL Server Management Studio (SSMS). Ha nincs szükség speciális engedélyek, a Windows-hitelesítés működnie kell.
  2. Nyissa meg az SQL Server, a **biztonsági/bejelentkezések** mappát.

      ![Nyissa meg a biztonsági/bejelentkezések mappában a fiókok](./media/backup-azure-sql-database/security-login-list.png)

  3. Kattintson a jobb gombbal a **bejelentkezések** mappára, és válassza **új bejelentkezés**. A **bejelentkezés – új**válassza **keresési**.

      ![A bejelentkezés – új párbeszédpanel, válassza ki a keresés](./media/backup-azure-sql-database/new-login-search.png)

  4. A virtuális Windows-szolgáltatásfiók **NT SERVICE\AzureWLBackupPluginSvc** a virtuális gép regisztrációja és SQL felderítési fázis során jött létre. Adja meg a fiók nevét, ahogyan az **írja be a kijelölendő objektum nevét**. Válassza ki **Névellenőrzés** oldani a nevet. Kattintson az **OK** gombra.

      ![Válassza ki a szolgáltatás ismeretlen név Névellenőrzés](./media/backup-azure-sql-database/check-name.png)

  5. A **kiszolgálói szerepkörök**, győződjön meg arról, hogy a **SysAdmin (rendszergazda)** szerepkör van kiválasztva. Kattintson az **OK** gombra. A szükséges engedélyekkel most már léteznie kell.

      ![Győződjön meg arról, hogy a sysadmin (rendszergazda) kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Az adatbázis mostantól társíthat a Recovery Services-tároló. Az Azure Portalon az a **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló, amely hibás állapotú > **adatbázisok újbóli felderítése**.

      ![Ellenőrizze a kiszolgáló rendelkezik-e megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Folyamat ellenőrzéséhez a **értesítések** területen. A kijelölt adatbázisok találhatók, a sikert jelző üzenet jelenik meg.

      ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>További lépések

- [Ismerje meg](backup-sql-server-database-azure-vms.md) SQL Server-adatbázisok biztonsági mentése.
- [Ismerje meg](restore-sql-database-azure-vm.md) biztonsági másolat az SQL Server-adatbázisok visszaállítása.
- [Ismerje meg](manage-monitor-sql-database-backup.md) kezelése biztonsági mentés az SQL Server-adatbázisok.
