---
title: Az Azure SQL Server-adatbázis biztonsági mentése |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, az SQL Server biztonsági másolat az Azure-bA. A cikk azt is bemutatja, az SQL Server helyreállítása.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918050"
---
# <a name="back-up-sql-server-database-in-azure"></a>Az Azure-beli SQL Server-adatbázis biztonsági mentése

Az SQL Server-adatbázisok olyan alacsony helyreállítási időkorlátot (RPO) és a hosszú távú megőrzés igénylő kritikus fontosságú számítási feladatokhoz. Az Azure Backup egy SQL Server biztonsági mentési megoldás, amely nulla infrastruktúrát igényel, ami azt jelenti, hogy nincs összetett biztonsági mentési kiszolgálóra, nem felügyeleti ügynök, vagy biztonsági mentési tár kezeléséhez biztosít. Az Azure Backup minden SQL Server-kiszolgálók vagy akár a különböző számítási feladatok lehetővé teszi az központi felügyelet, a biztonsági mentésekhez.

 Ez a cikk bemutatja:

> [!div class="checklist"]
> * SQL Server biztonsági mentése az Azure-bA előfeltételei
> * Létrehozhat és használhat egy Recovery Services-tároló
> * Az SQL Server-adatbázis biztonsági mentések konfigurálása
> * A helyreállítási pontok biztonsági mentése (vagy az adatmegőrzési) szabályzat beállítása
> * Az adatbázis visszaállítása

Ez a cikk az eljárások előtt rendelkeznie kell egy Azure-ban futó SQL Server. [Az SQL-Piactéri virtuális gépek segítségével gyorsan létrehozhat egy SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Nyilvános előzetes verzió korlátozásai

A következő elemeket az ismert korlátozás a nyilvános előzetes verzió.

- Az SQL virtuális gép eléréséhez az Azure nyilvános IP-címek internetkapcsolatra van szükség. További részletekért lásd a szakasz [hálózati kapcsolatot](backup-azure-sql-database.md#establish-network-connectivity).
- Egy Recovery Services-tároló legfeljebb 2000 SQL-adatbázisok védelme biztosítható. További SQL-adatbázisok külön Recovery Services-tárolóban kell tárolni.
- [Elosztott rendelkezésre állási csoportok biztonsági mentési korlátozásokkal rendelkezik](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- Az SQL a feladatátvevő fürt példány (FCI) használata nem támogatott.
- Az Azure portal segítségével konfigurálhatja az Azure Backup az SQL Server-adatbázisok védelmét. Azure PowerShell, CLI és REST API-k támogatása jelenleg nem áll rendelkezésre.

## <a name="supported-azure-geos"></a>Az Azure helyeken támogatott

- Délkelet-Ausztrália (ASE) 
- Dél-Brazília (BRS)
- Közép-Kanada (CNC)
- Kelet-Kanada (CE)
- USA középső régiója (CUS)
- Kelet-Ázsia (EA)
- Kelet-Ausztrália (AE) 
- USA keleti régiója (EUS)
- USA 2. keleti régiója (EUS2)
- Kelet-Japán (JPE)
- Nyugat-Japán (JPW)
- Közép-India (INC) 
- Dél-India (INS)
- Korea középső régiója (KRC)
- Korea déli régiója (KRS)
- USA északi középső régiója (NCUS) 
- Észak-Európa (NE) 
- USA déli középső régiója (SCUS) 
- Délkelet-Ázsia (SEA)
- Egyesült Királyság déli régiója (UKS) 
- Egyesült Királyság nyugati régiója (UKW) 
- Nyugat-Európa (WE) 
- USA nyugati régiója (WUS)
- USA nyugati középső régiója (WCUS)
- USA 2. nyugati régiója (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Támogatott operációs rendszerek és az SQL server verziói

A következő operációs rendszerek támogatottak. SQL-piactér Azure-beli virtuális gépek és (Ha kézzel telepíti az SQL Server) nem marketplace-virtuális gépek támogatottak.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux jelenleg nem támogatott.

### <a name="supported-versionseditions-of-sql-server"></a>Az SQL Server támogatott verziók és kiadások

- Az SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- Az SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Az SQL Server védelme az Azure Backup használatára vonatkozó Előfeltételek 

Mielőtt készíthet biztonsági mentést az SQL Server-adatbázishoz, ellenőrizze az alábbi feltételek. :

- Azonosítsa vagy [hozzon létre egy Recovery Services-tároló](backup-azure-sql-database.md#create-a-recovery-services-vault) az azonos régióban, vagy területi beállítás, a virtuális gép, SQL Server szoftvert futtatja.
- [Ellenőrizze az engedélyeit a virtuális gépen](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) SQL-adatbázisok biztonsági mentéséhez szükséges.
- [Az SQL virtuális gép rendelkezik hálózati kapcsolattal](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Az SQL Server-adatbázisok biztonsági mentéséhez egyszerre csak egy biztonsági mentési megoldás is rendelkezhet. A funkció használata előtt tiltsa le minden olyan egyéb SQL biztonsági mentési, más biztonsági mentések fog zavarja és sikertelen lesz. Engedélyezheti az Azure Backup IaaS virtuális gép mellett az SQL biztonsági mentése bármely konfliktus nélkül 
>

Ha ezek a feltételek vannak jelen a környezetében, folytassa a szakasz [SQL-adatbázis védelme érdekében a tároló konfigurálása](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Ha az Előfeltételek bármelyike nem léteznek, továbbra is a fejezet elolvasása.


## <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

Minden műveletre az SQL virtuális gép kapcsolatot kell létesítenie az Azure nyilvános IP-címeket. Az SQL virtuális gép műveletek (például az adatbázis, a biztonsági mentés konfigurálása, ütemezett biztonsági mentések, visszaállítás helyreállítási pontok, és így tovább) anélkül, hogy a nyilvános IP-címek való kapcsolódás sikertelen. Világos és egyértelmű utat nyújt a biztonsági mentések forgalmától használja az alábbi lehetőségek egyikét:

- Engedélyezett az Azure-adatközpont IP-tartományait – az engedélyezési listára az Azure datacenter IP-címtartományok, használja a [töltse le az IP-címtartományok és utasításokat center oldalán](https://www.microsoft.com/download/details.aspx?id=41653). 
- Irányítaná a forgalmat a HTTP-proxykiszolgáló üzembe helyezése – egy SQL-adatbázis egy virtuális gép biztonsági, amikor a biztonsági mentési bővítményt a virtuális gép HTTPS API-k segítségével felügyeleti parancsokat küldjön az Azure Backup és az Azure Storage-adatok. A biztonsági mentési bővítményt, az Azure Active Directory (AAD) is használ. A biztonsági mentési bővítményt forgalom irányítása a ezek három szolgáltatást, a HTTP-proxyn keresztül a nyilvános internet-hozzáférés konfigurált csak összetevő, mert.

A választható lehetőségek terén érvényesíthető kompromisszumokat vannak: kezelhetőségi, részletes ellenőrzésére és költség.

> [!NOTE]
>Az Azure Backup szolgáltatás címkék általános rendelkezésre állás szerint elérhetőnek kell lennie.
>

| Beállítás | Előnyök | Hátrányai |
| ------ | ---------- | ------------- |
| Engedélyezett IP-cím-tartományok | További költségek nélkül. <br/> Nyissa meg a hozzáférést egy NSG-ben, használja a **Set-AzureNetworkSecurityRule** parancsmagot. | Összetett kezelhetők az érintett IP-címtartományok idővel változni. <br/>Az Azure storage nem csak a teljes hozzáférést biztosít.|
| HTTP proxyk használatára   | SQL-rendszergazdai engedélyek kijavítása <br/>A telepítés során, ha a hibát látja UserErrorSQLNoSysadminMembership, jelentkezzen be az SQL Server Management Studio (SSMS) egy olyan fiókkal, amely az SQL-rendszergazdai engedéllyel rendelkezik. <br/> Hacsak nem szükséges különleges engedély, ismeri fel a fiókot a Windows-hitelesítés használatával képesnek kell lennie. | Nyissa meg az SQL Server, a biztonsági/bejelentkezések mappát. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Nyissa meg az SQL Server és a biztonság és a bejelentkezési mappák fiókok megtekintéséhez.

A bejelentkezések mappára, kattintson a jobb gombbal, és válassza ki **új bejelentkezés**, és a bejelentkezés – új párbeszédpanel, kattintson a keresése Keresés a bejelentkezés – új párbeszédpanel megnyitásához. A Windows virtuális szolgáltatásfiókot, mert NT Service\AzureWLBackupPluginSvc már megtörtént a virtuális gép regisztrációja és SQL felderítési fázis során létrehozott, adja meg a fiók nevét, ahogyan megjelenik az  Írja be a kijelölendő objektum nevét párbeszédpanel. Kattintson a **Névellenőrzés** oldani a nevet. Nevek ellenőrzése gombra kattintva a ismeretlen szolgáltatás név feloldása Az Azure Backup SQL-adatbázisok felderítéséhez, a NT Service\AzureWLBackupPluginSvc fióknak rendelkeznie kell az SQL és az SQL rendszergazdai jogosultságokkal. Az a kiszolgálói szerepkörök párbeszédpanelen győződjön meg arról, hogy a SysAdmin (rendszergazda) szerepkör van kiválasztva.

Kattintson a OK bezárásához bejelentkezés – új.

1. Győződjön meg arról, hogy a sysadmin (rendszergazda) kiszolgálói szerepkör van kiválasztva
2. A szükséges engedélyekkel most már léteznie kell.

   ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/open-backup-menu.png)

3. Az Azure Portalon **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló hibát, és válassza ki **adatbázisok újbóli felderítése**.

4. Ellenőrizze a kiszolgáló rendelkezik a megfelelő engedélyekkel

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Amikor a kijelölt adatbázisok találhatók, a sikert jelző üzenet az értesítési területen jelenik meg. **Az SQL Server-adatbázisok felderítése

    ![Az Azure Backup egy SQL Server-példány összes adatbázisára képes felderíteni, így azok a biztonsági mentési igényeknek is védelme.](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Az alábbi eljárás segítségével azonosítja a virtuális gépet, az SQL-adatbázisok üzemeltetéséhez. Miután azonosította a virtuális gépet, az Azure Backup egy egyszerűsített bővítmény, amely az SQL server-adatbázisok felderítése telepíti.

    ![Biztonsági mentés függőben](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Jelentkezzen be az előfizetését a az Azure portal. A bal oldali menüben válassza ki a minden szolgáltatás. Az összes szolgáltatás lehetőséget a főmenü Az összes szolgáltatás párbeszédpanelen írja be a Recovery Services. Ahogy elkezd gépelni, a bemeneti szűri az erőforrások listájában.

6. Ha azt látja, válassza ki a **Recovery Services-tárolók**. 

    Az összes szolgáltatások párbeszédpanelen írja be a Recovery Services Az előfizetés Recovery Services-tárolók listája megjelenik. A Recovery Services-tároló listából válassza ki a tárolót, az SQL-adatbázisok védelméhez használni kívánt. Miután választott, a biztonsági mentés célja menüben megjelenik a két lépést: adatbázisok felderítése a virtuális gépek és a biztonsági mentés konfigurálása. Több virtuális gép rendelkezhet ugyanazzal a névvel.

    Ezzel a névvel több virtuális gépek azonban eltérő erőforráscsoportokban tartozik. A nem védett virtuális gépek a virtuális gép nevére és erőforráscsoportjára csoport alapján vannak listázva. Ha a virtuális gépek várt nincs felsorolva, tekintse meg, ha a virtuális gép már védett tárolóra.

    ![Virtuális gépek listájából válassza ki a jelölőnégyzetet a virtuális gép, amely tartalmazza az SQL-adatbázisok védelmét, és kattintson a kívánt adatbázisok felderítése.](./media/backup-azure-sql-database/notifications-db-discovered.png)

Az Azure Backup a virtuális gép összes SQL-adatbázisok deríti fel.

### <a name="fixing-sql-sysadmin-permissions"></a>Mi történik az adatbázis-felderítési fázis során kapcsolatos információkért tekintse meg a következő szakaszban háttérbeli műveletek során az SQL Database-adatbázisok felderítéséhez.

A telepítés során, ha a hibát látja **UserErrorSQLNoSysadminMembership**, SQL-rendszergazdai engedélyekkel rendelkező fiók használatára, hogy jelentkezzen be az SQL Server Management Studio (SSMS). Ha nincs szükség speciális engedélyek, a Windows-hitelesítés működnie kell.

1. Ha a **adatbázisok felderítése** eszközt, az Azure Backup a háttérben hajtja végre a következő műveleteket:

    ![regisztrálja a virtuális gép számítási feladatok biztonsági mentés a Recovery Services-tárolót.](./media/backup-azure-sql-database/security-login-list.png)

2. A bejelentkezések mappára, kattintson a jobb gombbal, és válassza ki **új bejelentkezés**, és a bejelentkezés – új párbeszédpanel, kattintson a **keresése**

    ![telepíti a AzureBackupWindowsWorkload bővítményt a virtuális gépen.](./media/backup-azure-sql-database/new-login-search.png)

3. Egy SQL-adatbázis biztonsági mentése nem egy ügynök nélküli megoldás, az azt jelenti, a bővítményt a virtuális gépre telepíteni, a nincs ügynök van telepítve az SQL Database. a szolgáltatás-fiókot hoz létre **NT Service\AzureWLBackupPluginSvc**, a virtuális gépen. 

    ![Minden biztonsági mentési és visszaállítási műveletek service fiókot használja.](./media/backup-azure-sql-database/check-name.png)

4. NT Service\AzureWLBackupPluginSvc** SQL-rendszergazdai engedélyekre van szüksége.

5. Az összes SQL-Piactéri virtuális gépek kapható a SqlIaaSExtension telepítve van, és AzureBackupWindowsWorkload SQLIaaSExtension használja a szükséges engedélyek automatikus beszerzéséhez. Ha a virtuális gép nem rendelkezik telepített SqlIaaSExtension, az adatbázis felderítése művelet meghiúsul, és a hibaüzenet jelenik meg, **UserErrorSQLNoSysAdminMembership**.

    ![Biztonsági mentés a sysadmin (rendszergazda) engedéllyel hozzáadásához kövesse a SQL nem marketplace-beli virtuális gépek az Azure biztonsági mentési engedélyek beállítása.](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Válassza ki a virtuális gép és az adatbázis

6. SQL Server-adatbázis biztonsági mentésének konfigurálása Az Azure Portalon **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló hibát, és válassza ki **adatbázisok újbóli felderítése**.

    ![A felügyeleti és monitorozási képességeket függ a Recovery Services-tárolóba.](./media/backup-azure-sql-database/check-erroneous-server.png)

    Ezzel a névvel több virtuális gépek azonban eltérő erőforráscsoportokban tartozik. A nem védett virtuális gépek a virtuális gép nevére és erőforráscsoportjára csoport alapján vannak listázva. Az SQL-adatbázis védelmének konfigurálása:

    ![Virtuális gépek listájából válassza ki a jelölőnégyzetet a virtuális gép, amely tartalmazza az SQL-adatbázisok védelmét, és kattintson a kívánt adatbázisok felderítése.](./media/backup-azure-sql-database/notifications-db-discovered.png)

Az Azure Backup a virtuális gép összes SQL-adatbázisok deríti fel.

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Nyissa meg a helyreállítási tár regisztrálva van az SQL virtuális gép.

Ha Ön a mintajelentéshez Ez a cikk ahhoz, a nem védett virtuális gépek már megismert, és a tár regisztrálva van a virtuális gép. Most már készen áll az SQL-adatbázisok védelmének konfigurálása. A biztonsági mentés célja menüben kattintson a biztonsági mentés konfigurálása.

1. Az Azure Backup szolgáltatás minden SQL Server-példányok az önálló adatbázisok, valamint az SQL AlwaysOn rendelkezésre állási csoportok jeleníti meg.
2. Az SQL-példányhoz az önálló adatbázisok megtekintéséhez kattintson a sávnyílra a példány nevét, az adatbázisok megtekintése mellett.

    ![Az alábbi képeken példák egy különálló példány és a egy Always On rendelkezésre állási csoportban.](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Esetén az SQL Always On rendelkezésre állási csoport hogy az SQL biztonsági mentési preferenciáját fogadja el. De SQL platformkorlátozásai miatt teljes és különbségi biztonsági másolatok kell az elsődleges csomópontról történik. Naplóalapú biztonsági mentés a biztonsági mentési preferenciáját akkor fordulhat elő a rendszer.

    ![Ez a korlátozás miatt a rendelkezésre állási csoportokra vonatkozó mindig az elsődleges csomópont kell regisztrálni.](./media/backup-azure-sql-database/all-services.png) <br/>

    SQL-példány szereplő adatbázisok listája 

4. Kattintson a sávnyílra mellett az AlwaysOn rendelkezésre állási csoportok az adatbázisok listájának megtekintéséhez.

5. A szükséges engedélyekkel most már léteznie kell.

   ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/open-backup-menu.png)

6. Az Azure Portalon **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló hibát, és válassza ki **adatbázisok újbóli felderítése**.

7. Ellenőrizze a kiszolgáló rendelkezik a megfelelő engedélyekkel

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    AlwaysOn rendelkezésre állási csoportban szereplő adatbázisok listája 

    ![Az Azure Backup egy SQL Server-példány összes adatbázisára képes felderíteni, így azok a biztonsági mentési igényeknek is védelme.](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Az alábbi eljárás segítségével azonosítja a virtuális gépet, az SQL-adatbázisok üzemeltetéséhez. Miután azonosította a virtuális gépet, az Azure Backup egy egyszerűsített bővítmény, amely az SQL server-adatbázisok felderítése telepíti.

    ![Biztonsági mentés függőben](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Jelentkezzen be az előfizetését a az Azure portal. Az adatbázisok a listából válassza ki az összes védeni, és kattintson a kívánt OK. Válassza ki a védelmükhöz, több adatbázis Egyszerre legfeljebb 50 adatbázisok választhat. Ha azt szeretné, több mint 50 adatbázisok védelméhez, győződjön meg arról, több fázisban.

9. Miután az első 50 adatbázist lát el védelemmel, ismételje meg ezt a lépést a következő set-adatbázisok védelmét.

    Biztonsági mentési terhelés optimalizálása érdekében az Azure Backup bontja nagy biztonsági mentési feladatok több köteg. Egy biztonsági mentési feladat adatbázisok maximális száma érték az 50. Létrehozásához vagy egy biztonsági mentési szabályzatot, válassza ki a [biztonsági mentési](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database) menüjében válassza biztonsági mentési szabályzat, a menü megnyitásához.

### <a name="backend-operations-when-discovering-sql-databases"></a>Válassza ki a biztonsági mentési házirend-beállítás

Az a **biztonsági mentési házirend kiválasztása** legördülő menüben válasszon egy biztonsági mentési szabályzatot, majd kattintson OK.

- A saját biztonsági mentési szabályzat létrehozásával kapcsolatos információkért lásd: a szakasz biztonsági mentési házirend meghatározása. a legördülő menüből válassza ki a biztonsági mentési házirend 

- A biztonsági mentési szabályzat menüje a a **biztonsági mentési házirend kiválasztása** legördülő menüben kiválaszthatja: az alapértelmezett HourlyLogBackup házirend

- korábban létrehozott SQL, a meglévő biztonsági mentési házirend a definiáljon egy új szabályzatot a helyreállításipont-célkitűzés (RPO) és a megőrzési tartomány alapján. **Az Azure Backup támogatja a hosszú távú megőrzés alapján a nagyapa-apa-fiú biztonsági mentési sémát, és optimalizálhatja a háttérrendszer tárhelyhasználat megfelelőségi igények teljesítése közben. Az összes SQL-Piactéri virtuális gépek kapható a SqlIaaSExtension telepítve van, és AzureBackupWindowsWorkload SQLIaaSExtension használja a szükséges engedélyek automatikus beszerzéséhez. Ha a virtuális gép nem rendelkezik telepített SqlIaaSExtension, az adatbázis felderítése művelet meghiúsul, és a hibaüzenet jelenik meg, **UserErrorSQLNoSysAdminMembership**. Biztonsági mentés a sysadmin (rendszergazda) engedéllyel hozzáadásához kövesse a [SQL nem marketplace-beli virtuális gépek az Azure biztonsági mentési engedélyek beállítása](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>SQL Server-adatbázis biztonsági mentésének konfigurálása

Az Azure Backup az SQL Server-adatbázisok védelmét és biztonsági mentési feladatok kezelése a felügyeleti szolgáltatásokat biztosít. A felügyeleti és monitorozási képességeket függ a Recovery Services-tárolóba. 

> [!NOTE]
> Az SQL Server-adatbázisok biztonsági mentéséhez egyszerre csak egy biztonsági mentési megoldás is rendelkezhet. A funkció használata előtt tiltsa le minden olyan egyéb SQL biztonsági mentési, más biztonsági mentések fog zavarja és sikertelen lesz. Engedélyezheti az Azure Backup IaaS virtuális gép mellett az SQL biztonsági mentése bármely konfliktus nélkül 
>

Az SQL-adatbázis védelmének konfigurálása:

1. Nyissa meg a helyreállítási tár regisztrálva van az SQL virtuális gép.

2. A szükséges engedélyekkel most már léteznie kell.

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/open-backup-menu.png)

3. Az Azure Portalon **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló hibát, és válassza ki **adatbázisok újbóli felderítése**.

4. Ellenőrizze a kiszolgáló rendelkezik a megfelelő engedélyekkel

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    AlwaysOn rendelkezésre állási csoportban szereplő adatbázisok listája Ha Ön a mintajelentéshez Ez a cikk ahhoz, a nem védett virtuális gépek már megismert, és a tár regisztrálva van a virtuális gép. Most már készen áll az SQL-adatbázisok védelmének konfigurálása.

5. A biztonsági mentés célja menüben kattintson a **biztonsági mentés konfigurálása**.

    ![Az Azure Backup egy SQL Server-példány összes adatbázisára képes felderíteni, így azok a biztonsági mentési igényeknek is védelme.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Az Azure Backup szolgáltatás minden SQL Server-példányok az önálló adatbázisok, valamint az SQL AlwaysOn rendelkezésre állási csoportok jeleníti meg. Az SQL-példányhoz az önálló adatbázisok megtekintéséhez kattintson a sávnyílra a példány nevét, az adatbázisok megtekintése mellett. Az alábbi képeken példák egy különálló példány és a egy Always On rendelkezésre állási csoportban.

    > [!NOTE]
    > Esetén az SQL Always On rendelkezésre állási csoport hogy az SQL biztonsági mentési preferenciáját fogadja el. De SQL platformkorlátozásai miatt teljes és különbségi biztonsági másolatok kell az elsődleges csomópontról történik. Naplóalapú biztonsági mentés a biztonsági mentési preferenciáját akkor fordulhat elő a rendszer. Ez a korlátozás miatt a rendelkezésre állási csoportokra vonatkozó mindig az elsődleges csomópont kell regisztrálni.
    >

    ![SQL-példány szereplő adatbázisok listája](./media/backup-azure-sql-database/discovered-databases.png)

    Kattintson a sávnyílra mellett az AlwaysOn rendelkezésre állási csoportok az adatbázisok listájának megtekintéséhez.

    ![AlwaysOn rendelkezésre állási csoportban szereplő adatbázisok listája](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Az adatbázisok a listából válassza ki az összes védeni, és kattintson a kívánt **OK**.

    ![Válassza ki a védelmükhöz, több adatbázis](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Egyszerre legfeljebb 50 adatbázisok választhat. Ha azt szeretné, több mint 50 adatbázisok védelméhez, győződjön meg arról, több fázisban. Miután az első 50 adatbázist lát el védelemmel, ismételje meg ezt a lépést a következő set-adatbázisok védelmét.
    > [!Note] 
    > Biztonsági mentési terhelés optimalizálása érdekében az Azure Backup bontja nagy biztonsági mentési feladatok több köteg. Egy biztonsági mentési feladat adatbázisok maximális száma érték az 50.
    >
    >

7. Létrehozásához vagy egy biztonsági mentési szabályzatot, válassza ki a **biztonsági mentési** menüjében válassza **biztonsági mentési szabályzat**, a menü megnyitásához.

    ![Válassza ki a biztonsági mentési házirend-beállítás](./media/backup-azure-sql-database/select-backup-policy.png)

8. Az a **biztonsági mentési házirend kiválasztása** legördülő menüben válasszon egy biztonsági mentési szabályzatot, majd kattintson **OK**. A saját biztonsági mentési szabályzat létrehozásával kapcsolatos információkért lásd: a szakasz [biztonsági mentési házirend meghatározása](backup-azure-sql-database.md#define-a-backup-policy).

    ![a legördülő menüből válassza ki a biztonsági mentési házirend](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    A biztonsági mentési szabályzat menüje a a **biztonsági mentési házirend kiválasztása** legördülő menüben kiválaszthatja: 
    - az alapértelmezett HourlyLogBackup házirend 
    - korábban létrehozott SQL, a meglévő biztonsági mentési házirend
    - a [definiáljon egy új szabályzatot](backup-azure-sql-database.md#define-a-backup-policy) a helyreállításipont-célkitűzés (RPO) és a megőrzési tartomány alapján. 

    > [!Note]
    > Az Azure Backup támogatja a hosszú távú megőrzés alapján a nagyapa-apa-fiú biztonsági mentési sémát, és optimalizálhatja a háttérrendszer tárhelyhasználat megfelelőségi igények teljesítése közben.
    >

9. Miután kiválasztotta a biztonsági mentési házirend, az a **biztonsági mentés menü** kattintson **biztonsági mentés engedélyezése**.

    ![a választott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

    A konfigurációs folyamatot a portálon értesítések területén követheti nyomon.

    ![értesítési terület megjelenítése](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Biztonsági mentési szabályzat meghatározása

Biztonsági mentési házirend egy mátrixot határoz meg, amikor a biztonsági másolatokat készít, és mennyi ideig megőrződnek, a biztonsági mentéseket. Azure Backup használatával háromféle SQL-adatbázisok biztonsági mentés ütemezése:

* Teljes biztonsági mentés – teljes biztonsági mentés biztonsági másolatot készít a teljes adatbázisról. Egy teljes biztonsági mentés egy adott adatbázishoz, vagy a fájlok és fájlcsoportok lévő összes adatot, és elég log helyreállítani az adatokat tartalmazza. Legfeljebb napi egy teljes biztonsági mentés is indíthat. Ha szeretné igénybe vehet egy teljes biztonsági mentés napi vagy heti időközönként. 
* Különbségi biztonsági másolat - különbözeti biztonsági mentése a legújabb, az előző teljes biztonsági mentését alapul. Különbözeti biztonsági mentése csak a teljes biztonsági mentés óta megváltozott adatokat rögzíti. Legfeljebb napi egy különbségi biztonsági mentés is indíthat. Az azonos napi egy teljes biztonsági mentés és a különbözeti biztonsági mentése nem konfigurálható.
* Tranzakciónapló biztonsági mentését – a napló biztonsági mentését lehetővé teszi, hogy legfeljebb egy adott időpontban a visszaállítás a második. Egyszerre legfeljebb konfigurálhatja tranzakciós napló biztonsági mentés 15 percenként.

Szabályzat jön létre, a Recovery Services-tároló szint. Ha több tárolóra van, a tárolók használhatja az azonos mentési házirenddel, de a biztonsági mentési házirendet kell alkalmaznia az egyes tárolókban. A napi biztonsági mentési szabályzat létrehozásakor teljes biztonsági mentés az alapértelmezett érték. A különbségi biztonsági mentés, de csak ha váltson át a heti teljes biztonsági mentések is hozzáadhat. Az alábbi eljárás ismerteti egy SQL Server biztonsági mentési szabályzat létrehozása az Azure virtuális gépként.

Biztonsági mentési szabályzat létrehozása

1. A biztonsági mentési szabályzat menüje a a **biztonsági mentési házirend kiválasztása** legördülő menüjében válassza **hozzon létre új**.

   ![új biztonsági mentési szabályzat létrehozása](./media/backup-azure-sql-database/create-new-backup-policy.png)

    A biztonsági mentési szabályzat menüje vált, amennyiben az adja meg minden olyan új SQL server biztonsági mentési szabályzat szükséges mezőket.

   ![új biztonsági mentési szabályzat mezők](./media/backup-azure-sql-database/blank-new-policy.png)

2. A **házirendnév**, adjon meg egy nevet. 

3. Egy teljes biztonsági mentés megadása kötelező. A teljes biztonsági mentés fogadja el az alapértékeket, vagy kattintson **teljes biztonsági mentés** a szabályzat szerkesztéséhez.

    ![új biztonsági mentési szabályzat mezők](./media/backup-azure-sql-database/full-backup-policy.png)

    A teljes biztonsági mentés házirend választhatja a naponta vagy hetente gyakoriságát. Ha úgy dönt, hogy a napi, válassza ki az óra és időzóna, amikor megkezdődik a biztonsági mentési feladat. Ha naponkénti teljes biztonsági mentést, a különbségi biztonsági mentések nem hozható létre.

   ![napi időköz beállítása](./media/backup-azure-sql-database/daily-interval.png)

    Ha úgy dönt, hetente, válassza ki a nap, hét, óra és időzóna, amikor megkezdődik a biztonsági mentési feladat.

   ![heti időköz beállítása](./media/backup-azure-sql-database/weekly-interval.png)

4. Alapértelmezés szerint minden megőrzési időtartam (napi, heti, havi és éves) beállítás ki van jelölve. Törölje a megőrzési tartomány korlátozott nem szeretné, és állítsa be a használandó időközt. A teljes biztonsági mentés házirend menüben kattintson a **OK** , fogadja el a beállításokat.

   ![megőrzési tartomány időközének beállítása](./media/backup-azure-sql-database/retention-range-interval.png)

    Helyreállítási pontok megőrzésének, a megőrzési tartomány alapján címkével vannak ellátva. Ha például napi választja, teljes biztonsági mentés csak egy teljes biztonsági mentés naponta aktiválódik. A heti megőrzési függően az adott napon létrehozott biztonsági mentést címkézve, és megőrzi a heti megőrzési tartomány alapján. A havi és évi megőrzési hasonlóan viselkedik.

5. A különbözeti biztonsági mentési szabályzat hozzáadásához kattintson **különbözeti biztonsági mentési** a menü megnyitásához. 

   ![Nyissa meg a különbözeti házirend](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Válassza ki a különbözeti biztonsági mentési szabályzat menüje **engedélyezése** a gyakoriság és megőrzési vezérlőelemek megnyitásához. Is indíthat, legfeljebb napi egy különbségi biztonsági mentés.
    > [!Important] 
    > Különbségi biztonsági másolatok legfeljebb 180 napig lehet megtartani. Ha hosszabb adatmegőrzés megadásához, teljes biztonsági mentést kell használnia, a különbségi biztonsági mentések nem használhat.
    >

   ![különbözeti szabályzat szerkesztése](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Kattintson a **OK** mentse a szabályzatot, és térjen vissza a fő biztonsági mentési szabályzat menüje.

6. A tranzakciós napló biztonsági mentési szabályzat hozzáadásához kattintson **Naplóalapú biztonsági mentés** a menü megnyitásához. A Naplóalapú biztonsági mentés menü, válassza ki a **engedélyezése**, és állítsa be a gyakoriság és megőrzési szabályozza. Naplóalapú biztonsági mentések fordulhat elő, mint 15 perces gyakorisággal, és akár 35 napig lehet megtartani. Kattintson a **OK** mentse a szabályzatot, és térjen vissza a fő biztonsági mentési szabályzat menüje.

   ![napló biztonsági mentési szabályzat szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Döntse el, hogy az SQL biztonsági másolat tömörítésének engedélyezéséhez. A tömörítés alapértelmezés szerint le van tiltva.

    A háttérben az Azure Backup használja az SQL natív biztonsági mentési tömörítését.

8. Ha a biztonsági mentési szabályzat az összes módosításokat végzett, kattintson az **OK**. 

   ![új szabályzat elfogadása](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>SQL-adatbázis visszaállítása

Az Azure Backup a tranzakciónaplók biztonsági mentése segítségével állíthatja vissza az egyes adatbázisok egy adott dátum vagy idő, akár egy adott második, funkciót biztosít. Teljes, különbségi és naplóalapú biztonsági mentések szükség az adatok helyreállításához láncolata alapján ad meg, az Azure Backup automatikusan meghatározza, hogy a megfelelő helyreállítási időt.

Másik lehetőségként választhat egy teljes és különbségi biztonsági másolat visszaállítása egy adott időpont helyett egy adott helyreállítási pontot.
 > [!Note]
 > "Fő" adatbázis visszaállítási elindítása előtt indítsa el az SQL Server egyfelhasználós üzemmódban az indítási beállítást "-m AzureWorkloadBackup". Az -m argumentuma az ügyfél nevét, az ügyfél csak engedélyezett lesz a kapcsolat megnyitásához. Az összes rendszeradatbázisokban (master, model, msdb) állítsa le az SQL Agent szolgáltatás visszaállításának előtt. Zárja be, előfordulhat, hogy megpróbálja ellopni a kapcsolatot ezen adatbázisok bármelyikét alkalmazásokat.
>

Egy adatbázis visszaállítása

1. Nyissa meg a helyreállítási tár regisztrálva van az SQL virtuális gép.

2. Válassza ki a tároló irányítópultjának **használati** biztonsági másolati elemek a biztonsági másolati elemek menü megnyitásához.

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Az a **biztonsági másolati elemek** menüben válassza ki a biztonságimásolat-felügyelet típusa **SQL Azure-beli virtuális gépen**. 

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A biztonsági mentési elemek listája itt állíthatja be az SQL Database-adatbázisok listájának megjelenítéséhez. 

4. Az SQL-adatbázisok a listából válassza ki a visszaállítani kívánt adatbázis.

    ![Válassza ki az SQL Azure-beli virtuális gépen a listából](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Ha az adatbázist választja, megnyílik a menü. Ebben a menüben a biztonsági mentési részleteit jeleníti meg az adatbázis többek között:

    * a legrégebbi és a legújabb helyreállítási pontok
    * napló biztonsági mentésének állapota az elmúlt 24 órában (az adatbázisokat a teljes és a tömegesen naplózott helyreállítási modell, ha a tranzakciós napló biztonsági mentés konfigurálva)

5. A kijelölt adatbázis menüben kattintson a **visszaállítása DB** a visszaállítási menü megnyitásához.

    ![Válassza ki az adatbázis visszaállítása](./media/backup-azure-sql-database/restore-db-button.png)

    A **visszaállítása** menü megnyílik, és így biztosítja a **konfiguráció visszaállítása** menü. A **konfiguráció visszaállítása** menü az első lépés a visszaállítást a rendszer. Ebből a menüből választhatja meg, ahol szeretné visszaállítani az adatokat. A következő lehetőségek közül választhat:
    * Másik helyre, – használja ezt a beállítást, ha azt szeretné, miközben megőrzik az eredeti adatbázis állítsa vissza az adatbázist egy másik helyre.
    * Adatbázis - visszaállítás az eredeti forrás, ugyanazon SQL Server-példány az adatok felülírása. Ennek következménye, az eredeti adatbázis felülírásához.

    > [!Important]
    > Ha a kijelölt adatbázis Always On rendelkezésre állási csoporthoz tartozik, az SQL nem engedélyezi a felülírja az adatbázis. Ebben az esetben, csak a **máshová** beállítás engedélyezve van.
    >

    ![a konfigurálás elemre kattintva visszaállítási konfiguráció menü megnyitása](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

Ez az eljárás bemutatja az adatok visszaállítása másik helyre. Ha szeretné felülírni az adatbázis visszaállításakor, lépjen a szakaszban [visszaállítása és az adatbázis felülírása](backup-azure-sql-database.md#restore-and-overwrite-the-database). Ez az eljárás feltételezi, hogy a Recovery Services-tároló nyílt, és jelenleg a konfiguráció visszaállítása menüben. Ha nem, indítsa el a szakasz [SQL-adatbázis visszaállítása](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Az adatbázist visszaállíthatja egy SQL Server ugyanazon Azure-régióban, és a célkiszolgálón kell lennie a helyreállítási tárban való regisztrálását. 
>

A **kiszolgáló** legördülő menüben csak az SQL Server, a helyreállítási tárban regisztrált jeleníti meg. Ha azt szeretné, a kiszolgáló nem szerepel a következőben a **kiszolgáló** list, tekintse meg a [Fedezze fel az SQL server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases) keresnie. A felderítési adatbázist során új kiszolgálókat a Recovery Services-tároló van regisztrálva.

1. Az a **konfiguráció visszaállítása** menüben:

    * Válassza ki **máshová**,
    * a **kiszolgáló**, válassza ki az SQL server, ahol szeretné állítani az adatbázist.
    * az a **példány** legördülő menüben válasszon egy SQL-példányt
    * az a **visszaállított adatbázis neve** párbeszédpanelen adja meg a céladatbázis nevét.
    * Ha a alkalmazni, válassza ki a **felülírása, ha a kiválasztott SQL-példányon már létezik a DB ugyanazzal a névvel**.
    * Kattintson a **OK** befejezéséhez konfigurálását, és vigye át egy visszaállítási pontot választja.

    ![Visszaállítási konfiguráció menüből válassza másik helyre, a példány és a név](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Az a **visszaállítási pont kiválasztása** menüben kiválaszthatja vagy egy naplók (idő) vagy a teljes és különbségi visszaállítási pont. Ha szeretné visszaállítani egy adott időpontban a naplóba, továbbra is az ehhez a lépéshez. Ha szeretné visszaállítani a teljes és különbségi visszaállítási pontot, folytassa a 3. lépés.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    A pont, időponthoz kötött visszaállítás csak érhető el az adatbázisok teljes naplóalapú biztonsági mentések & Tömegesen naplózott helyreállítási modell. Egy adott pont kötött visszaállítása:

    1. Válassza ki **naplók (idő)** visszaállítási paramétert.

        ![Válassza ki a helyreállítási pont típusa](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. A **dátum és idő visszaállításához**, kattintson a naptár ikonra, a naptár megnyitása. A félkövérrel szedett dátumok helyreállítási pontokat tartalmaznak, és az aktuális dátumot ki van jelölve. Válassza ki a helyreállítási pontok a naptárban egy dátumot. Nem választhat ki nincs helyreállítási pontok dátumát.

        ![Naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Miután kiválasztotta a dátumot, az idővonal graph a rendelkezésre álló helyreállítási pontok folyamatos széles jeleníti meg.

    3. Az ütemterv graph, vagy a párbeszédpanelen, adja meg a helyreállítási pontot, majd kattintson a megadott idő **OK** a visszaállítási pont lépésnek a végrehajtásához.
    
       ![Naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        A **visszaállítási pont kiválasztása** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Az a **speciális konfiguráció** menüben:

        * Hogy az adatbázis nem működő, visszaállítás után a a **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** menüjében válassza **engedélyezve**.
        * Ha a célkiszolgálón lévő visszaállítási hely módosítani szeretné, adja meg az új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    5. Az a **visszaállítása** menüben kattintson a **visszaállítása** a visszaállítási feladat elindításához. Az értesítési területen követheti a folyamat állapotát. Is követheti, hogy az adatbázis-visszaállítási feladat a folyamatban.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-job-notification.png)

3. Az a **visszaállítási pont kiválasztása** menüben válasszon egy helyreállítási pontot. Választhat, vagy egy naplók (idő) vagy a teljes és különbségi. Ha szeretné visszaállítani az adott pont a napló, lépjen vissza, a 2. lépés. Ebben a lépésben egy adott teljes vagy különbözeti visszaállítási pontot állítja vissza. Ezzel a beállítással megjelenik az elmúlt 30 napra vonatkozó összes teljes és különbségi helyreállítási pontokat. Ha meg szeretné tekinteni a 30 napnál régebbi helyreállítási pontokat, kattintson a **szűrő** megnyitásához a **szűrő visszaállítási pontok** menü. Ha egy különbözeti helyreállítási pontot választja, az Azure Backup először visszaállítja a megfelelő teljes helyreállítási pontot, és majd alkalmazza a kiválasztott különbözeti helyreállítási pontot.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Az a **visszaállítási pont kiválasztása** menüben kattintson **teljes és különbségi**.

       ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        A rendelkezésre álló helyreállítási pontok listája jelenik meg.

    2. Helyreállítási pontok listájából válasszon ki egy helyreállítási pontot, és kattintson a **OK** a visszaállítási pont eljárást. 

        ![Válassza ki a teljes helyreállítási pontot](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        A **visszaállítási pont** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

        ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Az a **speciális konfiguráció** menüben:

        * Hogy az adatbázis nem működő, visszaállítás után a a **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** menüjében válassza **engedélyezve**. **Visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** alapértelmezés szerint le van tiltva.
        * Ha a célkiszolgálón lévő visszaállítási hely módosítani szeretné, adja meg az új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    4. Az a **visszaállítása** menüben kattintson a **visszaállítása** a visszaállítási feladat elindításához. Az értesítési területen követheti a folyamat állapotát. Is követheti, hogy az adatbázis-visszaállítási feladat a folyamatban.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Állítsa vissza, és az adatbázis felülírása

Ez az eljárás végigvezeti a adatainak visszaállítása, és az adatbázis felülírása. Ha szeretne egy másodlagos helyre történő helyreállításához, lépjen a szakaszban [visszaállítás másik helyre](backup-azure-sql-database.md#restore-to-an-alternate-location). Ez az eljárás feltételezi, hogy a Recovery Services-tároló nyílt, és jelenleg a **konfiguráció visszaállítása** menü (lásd az alábbi képen). Ha nem, indítsa el a szakasz [SQL-adatbázis visszaállítása](backup-azure-sql-database.md#restore-a-sql-database).

![a konfigurálás elemre kattintva visszaállítási konfiguráció menü megnyitása](./media/backup-azure-sql-database/restore-overwrite-db.png)

A **kiszolgáló** legördülő menüben csak az SQL Server, a helyreállítási tárban regisztrált jeleníti meg. Ha azt szeretné, a kiszolgáló nem szerepel a következőben a **kiszolgáló** list, tekintse meg a [Fedezze fel az SQL server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases) keresnie. A felderítési adatbázist során új kiszolgálókat a Recovery Services-tároló van regisztrálva.

1. Az a **konfiguráció visszaállítása** menüjében válassza **felülírása DB** kattintson **OK** konfigurálását végrehajtásához. 

   ![Kattintson az adatbázis felülírása](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    A **kiszolgáló**, **példány**, és **visszaállított adatbázis neve** párbeszédpanelek nem szükségesek.

2. Az a **visszaállítási pont kiválasztása** menüben kiválaszthatja vagy egy naplók (idő) vagy a teljes és különbségi visszaállítási pont. Ha szeretné visszaállítani az adott pont a napló, továbbra is az ehhez a lépéshez. Ha szeretné visszaállítani a teljes és különbségi visszaállítási pontot, folytassa a 3. lépés.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    A pont, időponthoz kötött visszaállítás csak érhető el az adatbázisok teljes naplóalapú biztonsági mentések & Tömegesen naplózott helyreállítási modell. A második válasszon pontot egy adott időponthoz kötött visszaállítás:

    1. Válassza ki **naplók (idő)** visszaállítási paramétert.

        ![Válassza ki a helyreállítási pont típusa](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. A **dátum és idő visszaállításához**, kattintson a naptár ikonra, a naptár megnyitása. A félkövérrel szedett dátumok helyreállítási pontokat tartalmaznak, és az aktuális dátumot ki van jelölve. Válassza ki a helyreállítási pontok a naptárban egy dátumot. Nem választhat ki nincs helyreállítási pontok dátumát.

        ![Naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Miután kiválasztotta a dátumot, az idővonal graph a rendelkezésre álló helyreállítási pontok jeleníti meg.

    3. Az ütemterv graph, vagy a párbeszédpanelen, adja meg a helyreállítási pontot, majd kattintson a megadott idő **OK** a visszaállítási pont lépésnek a végrehajtásához.
    
       ![Naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        A **visszaállítási pont kiválasztása** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Az a **speciális konfiguráció** menüben:

        * Hogy az adatbázis nem működő, visszaállítás után a a **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** menüjében válassza **engedélyezve**.
        * Ha a célkiszolgálón lévő visszaállítási hely módosítani szeretné, adja meg az új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    5. Az a **visszaállítása** menüben kattintson a **visszaállítása** a visszaállítási feladat elindításához. Az értesítési területen követheti a folyamat állapotát. Is követheti, hogy az adatbázis-visszaállítási feladat a folyamatban.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-job-notification.png)

3. Az a **visszaállítási pont kiválasztása** menüben válasszon egy helyreállítási pontot. Választhat, vagy egy naplók (idő) vagy a teljes és különbségi. Ha szeretné visszaállítani az adott pont a napló, lépjen vissza, a 2. lépés. Ebben a lépésben egy adott teljes vagy különbözeti visszaállítási pontot állítja vissza. Ezzel a beállítással megjelenik az elmúlt 30 napra vonatkozó összes teljes és különbségi helyreállítási pontokat. Ha meg szeretné tekinteni a 30 napnál régebbi helyreállítási pontokat, kattintson a **szűrő** megnyitásához a **szűrő visszaállítási pontok** menü. Ha egy különbözeti helyreállítási pontot választja, az Azure Backup először visszaállítja a megfelelő teljes helyreállítási pontot, és majd alkalmazza a kiválasztott különbözeti helyreállítási pontot.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Az a **visszaállítási pont kiválasztása** menüben kattintson **teljes és különbségi**.

       ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        A rendelkezésre álló helyreállítási pontok listája jelenik meg.

    2. Helyreállítási pontok listájából válasszon ki egy helyreállítási pontot, és kattintson a **OK** a visszaállítási pont eljárást. 

        ![Válassza ki a teljes helyreállítási pontot](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        A **visszaállítási pont** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

        ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Az a **speciális konfiguráció** menüben:

        * Hogy az adatbázis nem működő, visszaállítás után a a **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** menüjében válassza **engedélyezve**. **Visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** alapértelmezés szerint le van tiltva.
        * Ha a célkiszolgálón lévő visszaállítási hely módosítani szeretné, adja meg az új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    4. Az a **visszaállítása** menüben kattintson a **visszaállítása** a visszaállítási feladat elindításához. Az értesítési területen követheti a folyamat állapotát. Is követheti, hogy az adatbázis-visszaállítási feladat a folyamatban.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Azure virtuális gépeken az SQL Azure Backup-műveletek kezelése

Ez a szakasz ismerteti a különböző Azure Backup felügyeleti műveletek érhető el az SQL Azure-beli virtuális gépeken. A következő magas szintű műveletek találhatók:

* Feladatok figyelése
* Biztonsági mentési riasztások
* SQL-adatbázis védelmének megszüntetése
* Az SQL-adatbázis védelmének folytatása
* Az ad hoc biztonsági mentési feladat aktiválása
* SQL-kiszolgáló regisztrációjának törlése

### <a name="monitor-jobs"></a>Feladatok figyelése
Folyamatban van egy vállalati osztály megoldás az Azure Backup biztosítja a speciális biztonsági mentési riasztások és értesítések-e meghibásodva (lásd az alábbi biztonsági mentési riasztások szakaszban). Ha továbbra is szeretné meghatározott feladatok figyelése a követelmény alapján a következő lehetőségek bármelyikét használhatja:

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>Az Azure portal használata az ad hoc ad hoc minden művelet
Az Azure minden manuálisan aktivált biztonsági mentés mutat be, vagy ad hoc, feladatok, a biztonsági mentési feladatok portálon. A feladatok a portál beágyazás érhető el: az összes biztonsági mentési műveletek konfigurálása, manuálisan aktivált biztonsági mentési műveleteket, visszaállítási műveletek, regisztrációs és Fedezze fel az adatbázis-műveletek és állítsa le a biztonsági mentési műveletek. 
![Speciális konfiguráció menü](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Az összes ütemezett biztonsági mentési feladatok többek között a teljes, különbségi és Naplóalapú biztonsági mentés nem fognak megjelenni a portálon, és az SQL Server Management Studio használatával, az alább ismertetett figyelhető.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>A biztonsági mentési feladatok SQL Server Management Studio használata
Az Azure Backup minden biztonsági mentési műveletek az SQL natív API-kat használ. Natív API-k, az összes feladat adatait lehet beolvasni a [SQL biztonságimásolat-készletet létrehozták tábla](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) az msdb adatbázisban.

Az alábbi példa egy lekérdezést beolvasni egy adatbázist, az összes biztonsági mentési feladat **DB1**. A lekérdezés speciális figyelés testre szabhatja.
```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="backup-alerts"></a>Biztonsági mentési riasztások

A naplóalapú biztonsági mentések előforduló 15 percenként a biztonsági mentési feladatok alkalmanként figyelése fárasztó lehet. Az Azure Backup a potenciálisan fárasztó helyzet tervezett azáltal, hogy minden biztonsági mentési hiba váltott e-mailes riasztásokhoz. Riasztások az adatbázis szintjén vannak konszolidált hibakód alapján. Például ha egy adatbázis több biztonsági mentési hibák, egyes sikertelen, riasztás fogadása helyett e-mailt kap az első sikertelen. Akkor is majd jelentkezzen be az Azure Portalon az adatbázishoz tartozó további hibák figyelése. 

Biztonsági mentésekkel kapcsolatos riasztások figyelése:

1. Jelentkezzen be az Azure-előfizetését a [az Azure portal](https://portal.azure.com).

2. Nyissa meg a helyreállítási tár regisztrálva van az SQL virtuális gép.

3. A Recovery Services-tároló menüjében válassza **riasztások és események**. 

   ![Speciális konfiguráció menü](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Az a **riasztások és események** menüjében válassza **biztonsági mentési riasztások** riasztások listájának megtekintéséhez.

   ![Speciális konfiguráció menü](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>SQL Server-adatbázisok védelmének megszüntetése

Ha SQL Server-adatbázisok védelmének megszüntetéséhez az Azure Backup kéri, hogy szeretné-e őrizni a helyreállítási pontokat. SQL-adatbázisok védelmének leállítani két módja van:

* Az összes jövőbeli biztonsági mentési feladatok leállítása és összes helyreállítási pont törlése
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de hagyja meg a helyreállítási pontok 

A helyreállítási pontok meghagyásának végzi a költség, a helyreállítási pontok az SQL végrehajtása SQL-védett példány díja, valamint a felhasznált storage szolgáltatás díjszabása. Az SQL Azure Backup árairól kapcsolatos további információkért lásd: a [Azure Backup díjszabási oldala](https://azure.microsoft.com/pricing/details/backup/). Az adatbázis védelmének megszüntetése:

1. Nyissa meg a helyreállítási tár regisztrálva van az SQL virtuális gép.

2. Válassza ki a tároló irányítópultjának **használati** biztonsági másolati elemek a biztonsági másolati elemek menü megnyitásához.

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Az a **biztonsági másolati elemek** menüben válassza ki a biztonságimásolat-felügyelet típusa **SQL Azure-beli virtuális gépen**. 

    ![Bár az engedélyekkel kapcsolatos hibát kijavított, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló.](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A biztonsági mentési elemek listája itt állíthatja be az SQL Database-adatbázisok listájának megjelenítéséhez. 

4. Az SQL-adatbázisok a listából válassza ki az adatbázis védelmét le szeretné.

    ![Válassza ki az SQL Azure-beli virtuális gépen a listából](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Ha az adatbázist választja, megnyílik a menü. 

5. A kijelölt adatbázis menüben kattintson a **biztonsági mentés leállítása** leállítani az adatbázis védelme érdekében.

    ![Válassza ki az adatbázis visszaállítása](./media/backup-azure-sql-database/stop-db-button.png)

    A **biztonsági mentés leállítása** menü megnyitása.

6. Az a **biztonsági mentés leállítása** menüben válassza a biztonsági másolatok adatainak megőrzése, vagy a biztonsági másolatok adatainak törlése. Szükség esetén is adja meg a védelem és a egy megjegyzést leállításának oka.

    ![Válassza ki az adatbázis visszaállítása](./media/backup-azure-sql-database/stop-backup-button.png)

7. Kattintson a **biztonsági mentés leállítása** kikapcsolni a védelmet az adatbázison. 

### <a name="resume-protection-for-a-sql-database"></a>Az SQL-adatbázis védelmének folytatása

Ha a **biztonsági másolatok adatainak megőrzése** lehetőséget választotta, amikor az SQL Database védelem leállításával, lehetőség a védelem folytatásához. Ha a rendszer nem őrzi meg a biztonsági mentési adatokat, a védelem nem folytatható. 

1. Az SQL-adatbázis védelmének visszaállításához, nyissa meg a biztonsági mentési elemet, és kattintson **biztonsági mentés folytatása**.

    ![adatbázis védelmének folytatása](./media/backup-azure-sql-database/resume-backup-button.png)

   Ekkor megnyílik a biztonsági mentési szabályzat menüje.

2. Az a **biztonsági mentési szabályzat** menüben válasszon ki egy szabályzatot, és kattintson a **mentése**.

### <a name="trigger-an-adhoc-backup"></a>Eseményindító-ad hoc biztonsági mentés

Az ad hoc biztonsági mentés, ha azt szeretné, is indíthat. Ad hoc ad hoc biztonsági mentések négy típusa van. Az egyes típusok, részletek: a cikk [típusok az SQL biztonsági másolatainak](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Teljes biztonsági mentés
* Csak teljes biztonsági mentés másolása
* Különbségi biztonsági másolat
* Naplóalapú biztonsági mentés

### <a name="unregister-a-sql-server"></a>SQL-kiszolgáló regisztrációjának törlése

SQL-kiszolgáló regisztrációjának törlése, védelem eltávolítása után, de a tároló törlése előtt

1. Nyissa meg a helyreállítási tár regisztrálva van az SQL virtuális gép.

2. Az a **kezelés** szakasz a tároló menüjében kattintson **biztonsági mentési infrastruktúra**.  

   ![adatbázis védelmének folytatása](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Az a **felügyeleti kiszolgálók** területén kattintson **védett kiszolgálók**.

   ![adatbázis védelmének folytatása](./media/backup-azure-sql-database/protected-servers.png)

    A védett kiszolgálók menü megnyitása. 

4. Az a **védett kiszolgálók** menüben válassza ki a kiszolgálót szeretné törölni a regisztrációját. Ha azt szeretné, törölje a tárat, meg kell összes kiszolgáló regisztrációját.

5. A védett kiszolgálók menüben kattintson a jobb gombbal a védett kiszolgálón, és válassza ki **törlése**. 

   ![adatbázis védelmének folytatása](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>Az SQL database backup – gyakori kérdések

A következő szakaszban további információkat az SQL-adatbázis biztonsági mentése tartalmaz.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>I szabályozhatja az SQL biztonsági mentési szabályzat sebességétől, az SQL server gyakorolt hatás minimálisra csökkenthető

Igen, képes szabályozni a sebesség, a biztonsági mentési szabályzat hajt végre. A beállítás módosítása:

1. Az SQL Server az a `C:\Program Files\Azure Workload Backup\bin` mappában nyissa meg **TaskThrottlerSettings.json**.

2. Az a **TaskThrottlerSettings.json** fájl, módosítsa **DefaultBackupTasksThreshold** alacsonyabb értékre, ha például 5.

3. Mentse a módosítást, és zárja be a fájlt.

4. Az SQL-kiszolgálón nyissa meg a Feladatkezelőt, és indítsa újra a **Azure Backup számítási feladatok koordinátora szolgáltatás**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok egy teljes biztonsági mentés egy másodlagos replikáról

Nem, ez a funkció nem támogatott.

### <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok ne hozzon létre riasztásokat

Nem. A sikeres biztonsági mentési feladatok nem hoznak létre riasztásokat. Csak a biztonsági mentési feladatok sikertelenek, riasztást kap.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>A feladatok menü látható ütemezett biztonsági mentési feladat részleteit

Nem. A feladatok menü ad hoc ad hoc feladat részleteit jeleníti meg, de nem jeleníti meg az ütemezett biztonsági mentési feladatot. Minden ütemezett biztonsági mentési feladat sikertelen, ha megtalálhatja a részletekről a sikertelen feladat riasztások. Ha azt szeretné, a figyelő minden ütemezett és ad hoc ad hoc biztonsági mentési feladatok, [az SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>Ha kiválasztom a egy SQL server jövőbeli adatbázisok automatikusan hozzáadódnak

Nem. Ha egy SQL server védelmének beállítása, ha a kiszolgáló szintjén jelölőnégyzet bejelölésével, minden adatbázis ad hozzá. Azonban ha az adatbázisok az SQL server védelmének konfigurálása után vesz fel, manuálisan kell hozzáadni az új adatbázisok a védelmükhöz. Az adatbázisok nem automatikusan szerepelnek a konfigurált védelmet.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Ha módosítom a helyreállítási modell hogyan tegye újraindítok védelmét

Ha módosítja a helyreállítási modell, indítson egy teljes biztonsági mentést, és a naplóalapú biztonsági mentések a várt módon megkezdődik.

## <a name="next-steps"></a>További lépések

Az Azure Backuppal kapcsolatos további információért tekintse meg a titkosított virtuális gépek biztonsági mentéséhez készült PowerShell-mintát.

> [!div class="nextstepaction"]
> [Titkosított virtuális gép biztonsági mentése](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
