---
title: Az Azure SQL Server-adatbázisok biztonsági mentése |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan SQL Server biztonsági mentése az Azure-bA. A cikk azt is bemutatja, az SQL Server helyreállítása.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: raynew
ms.openlocfilehash: 50085336c59f2284f357e32b875eae08ff90d30f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790174"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Biztonsági másolatot az SQL Server-adatbázisok

Az SQL Server-adatbázisok olyan alacsony a helyreállításipont-célkitűzés (RPO) és hosszú távú megőrzés igénylő kritikus fontosságú számítási feladatokhoz. Az Azure Backup egy SQL Server biztonsági mentési megoldást biztosít, amely nulla infrastruktúrát igényel: nincs összetett biztonsági mentési kiszolgálóra, nem felügyeleti ügynök, és nincs biztonsági mentési tár kezelheti. Az Azure Backup lehetővé teszi az SQL Server, vagy akár a különböző számítási feladatokat futtató összes kiszolgáló központi felügyeletet, a biztonsági mentésekhez.

Ez a cikk bemutatja:

> [!div class="checklist"]
> * Az Előfeltételek biztonsági mentése egy SQL Server-példányt az Azure-bA.
> * Hogyan hozhat létre és használhat egy Recovery Services-tárolót.
> * Hogyan konfigurálhatja az SQL Server-adatbázis biztonsági másolatait.
> * Hogyan állíthat be a helyreállítási pontok biztonsági mentése (vagy az adatmegőrzési) szabályzatokat.
> * Hogyan állíthatja vissza az adatbázist.

Az eljárások ebben a cikkben a Kezdés előtt rendelkeznie kell az Azure-ban futó SQL Server-példányt. [SQL-Piactéri virtuális gépek használatával gyorsan létrehozhat egy SQL Server-példány](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Nyilvános előzetes verzió korlátozásai

A következő elemek ismert korlátozások a nyilvános előzetes verzió:

- Az SQL virtuális gép (VM) az Azure nyilvános IP-címek eléréséhez internetkapcsolatra van szükség. További információkért lásd: [hálózati kapcsolatot](backup-azure-sql-database.md#establish-network-connectivity).
- Egy Recovery Services-tároló legfeljebb 2000 SQL-adatbázisok védelmét. További SQL-adatbázisok külön Recovery Services-tárolóban kell tárolni.
- [Elosztott rendelkezésre állási csoportok biztonsági mentései](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) korlátozások is érvényesek.
- SQL Server mindig a Feladatátvevőfürt-példányokat (példányoktól) biztonsági mentés esetén nem támogatott.
- Az Azure portal segítségével konfigurálhatja az Azure Backup az SQL Server-adatbázisok védelmét. Az Azure PowerShell, az Azure CLI és a REST API-k jelenleg nem támogatottak.
- Az FCI tükrözött adatbázisokat, adatbázis-pillanatképeket és adatbázisok biztonsági mentési és visszaállítási műveletek nem támogatottak.
- Nagy mennyiségű fájlt az adatbázist nem lehet bekapcsolni. Támogatott fájlok maximális száma nem nagyon determinisztikus szám, mert nem csak a fájlok száma attól függ, de a fájlok elérési út hossza attól is függ. Ezekben az esetekben azonban kevésbé gyakran előforduló. Ennek kezelése megoldás készítünk.

Tekintse meg [gyakori kérdésekkel foglalkozó szakaszban](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) további részleteket a támogatás/nem támogatott forgatókönyvek.

## <a name="support-for-azure-geos"></a>Az Azure helyeken támogatása

Az Azure Backup az alábbi geók esetében támogatott:

- Délkelet-Ausztrália (ASE)
- Dél-Brazília (BRS)
- Közép-Kanada (CNC)
- Kelet-Kanada (CE)
- USA középső régiója (CUS)
- Kelet-Ázsia (EA)
- Kelet-Ausztrália (AE)
- USA keleti régiója (EUS)
- USA 2. keleti régiója (EUS2)
- Közép-India (INC)
- Dél-India (INS)
- Kelet-Japán (JPE)
- Nyugat-Japán (JPW)
- Korea középső régiója (KRC)
- Korea déli régiója (KRS)
- USA északi középső régiója (NCUS)
- Észak-Európa (NE)
- USA déli középső régiója (SCUS)
- Délkelet-Ázsia (SEA)
- Egyesült Királyság déli régiója (UKS)
- Egyesült Királyság nyugati régiója (UKW)
- USA nyugati középső régiója (WCUS)
- Nyugat-Európa (WE)
- USA nyugati régiója (WUS)
- USA 2. nyugati régiója (WUS 2)

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Operációs rendszerek és az SQL Server-verziók támogatása

Ez a szakasz ismerteti az Azure Backup támogatása az operációs rendszerek és az SQL Server verziójával. SQL-piactér Azure virtuális gépek és (Ha kézzel telepíti az SQL Server) nem Marketplace-virtuális gépek támogatottak.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux jelenleg nem támogatott.

### <a name="supported-sql-server-versions-and-editions"></a>Támogatott SQL Server-verziók és kiadások

- Az SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- Az SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- Az SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Előfeltételek

Biztonsági másolatot készíteni az SQL Server-adatbázishoz, mielőtt a következőket ellenőrizze:

- Azonosítsa vagy [hozzon létre egy Recovery Services-tároló](backup-azure-sql-database.md#create-a-recovery-services-vault) ugyanabban a régióban vagy területi beállítás a virtuális gép, amelyen az SQL Server-példány is.
- [Ellenőrizze az engedélyeit a virtuális gépen](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) , amely az SQL-adatbázisok biztonsági mentéséhez szükségesek.
- Ellenőrizze, hogy a [SQL virtuális gép rendelkezik hálózati kapcsolattal](backup-azure-sql-database.md#establish-network-connectivity).
- Ellenőrizze, hogy az SQL-adatbázisok megfelelően vannak-e elnevezve a [pokyny Pro pojmenování](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) Azure Backup a sikeres biztonsági másolatok készítése.

> [!NOTE]
> Az SQL Server-adatbázisok biztonsági mentése egyszerre csak egy biztonsági mentési megoldás is rendelkezhet. Tiltsa le a többi SQL biztonsági másolatainak; Ez a funkció használata előtt Ellenkező esetben a biztonsági mentések zavarja és sikertelen lesz. Engedélyezheti az Azure Backup IaaS virtuális gép együtt az SQL biztonsági mentése bármely konfliktus nélkül.
>

Ha ezek a feltételek vannak jelen a környezetében, továbbra is [konfigurálása SQL Server-adatbázisok biztonsági mentését](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Ha az Előfeltételek bármelyike még nem léteznek, olvassa tovább.


## <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

Minden műveletre az SQL virtuális gép kapcsolatot kell létesítenie az Azure nyilvános IP-címeket. Az SQL virtuális gép műveleteit (adatbázis felderítése, például a biztonsági mentések konfigurálása, a biztonsági mentések ütemezéséhez, helyreállítási pontok visszaállítása és így tovább) anélkül, hogy a nyilvános IP-címek való kapcsolódás sikertelen. Világos és egyértelmű utat nyújt a biztonsági mentések forgalmától használja az alábbi lehetőségek egyikét:

- Az Azure-adatközpont engedélyezett IP-címtartományok: Az engedélyezési listára az Azure adatközpont IP-címtartományait, használja a [letöltőközpontlapon az IP-címtartományok és utasításokat](https://www.microsoft.com/download/details.aspx?id=41653).
- HTTP-proxykiszolgáló üzembe forgalom irányítására: Virtuális gép SQL-adatbázis biztonsági mentésekor, a biztonsági mentési bővítményt a virtuális gépen a HTTPS API-k segítségével felügyeleti parancsokat küldjön az Azure Backup és az Azure Storage-adatok. A biztonsági mentési bővítményt, az Azure Active Directory (Azure AD) is használ. A biztonsági mentési bővítményt forgalom irányítása a ezek három szolgáltatást, a HTTP-proxyn keresztül. A bővítmény által az egyetlen olyan összetevő, amely konfigurálva van a nyilvános internet-hozzáférés.

A kompromisszumot kínál a beállítások között a következők: kezelhetőségi, szabályozhatja és költségek.

> [!NOTE]
> Az Azure Backup szolgáltatás címkék általános rendelkezésre állás szerint elérhetőnek kell lennie.
>

| Beállítás | Előnyök | Hátrányai |
| ------ | ---------- | ------------- |
| Engedélyezett IP-cím-tartományok | További költségek nélkül. <br/> Egy NSG-ben nyissa meg a hozzáférést, használja a **Set-AzureNetworkSecurityRule** parancsmagot. | Mivel az idővel módosítható az érintett IP-címtartományok kezelése bonyolult. <br/>Azure-ban, nem csak az Azure Storage teljes hozzáférést biztosít.|
| HTTP proxyk használatára   | Szabályozható a proxy a tároló URL-címek használata engedélyezett. <br/>Virtuális gépek internet egyetlen pont hozzáférés. <br/> Nem vonatkozik Azure IP-cím változik. | Virtuális gépek futtatása a proxy szoftverhez további költség. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>A nem Marketplace-en az SQL virtuális gépek engedélyeinek beállítása

A biztonsági mentéshez egy virtuális gép Azure Backup igényel a **AzureBackupWindowsWorkload** kiterjesztéssel kell telepíteni. Ha Azure Marketplace virtuális gépekhez használja, továbbra is [Fedezze fel az SQL Server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases). Ha a virtuális gép, amelyen az SQL-adatbázisok nem hozott létre az Azure Marketplace-ről, a következő eljárással telepítse a bővítményt, és a megfelelő engedélyek beállítása. Mellett a **AzureBackupWindowsWorkload** bővítmény, az Azure Backup SQL-rendszergazdai jogosultságokkal az SQL-adatbázisok védelméhez szükséges. Csak a virtuális gépen adatbázisok felderítéséhez, az Azure Backup hoz létre a fiók **NT SERVICE\AzureWLBackupPluginSvc**. Ez a fiók használatos biztonsági mentési és visszaállítási, és SQL-rendszergazdai jogosultsággal kell rendelkeznie kell. Ezen túlmenően az Azure Backup használja **NT AUTHORITY\SYSTEM** DB felderítési/lekérdezési, így ennek a fióknak kell lennie egy nyilvános bejelentkezés az SQL-fiók.

Engedélyek beállítása:

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a Recovery Services-tároló, amellyel az SQL-adatbázisok védelmét.

2. Az a **Recovery Services-tároló** irányítópulton válassza **Backup**. A **biztonsági mentés célja** menü megnyitása.

   ![A biztonsági mentés célja menüben nyissa meg a biztonsági másolat kiválasztása](./media/backup-azure-sql-database/open-backup-menu.png)

3. Az a **biztonsági mentés célja** menüben beállítása **a számítási feladat futtató** alapértelmezett: **Azure**.

4. Bontsa ki a **miről szeretne biztonsági másolatot készíteni** legördülő listát, és válassza ki **az SQL Server Azure virtuális gép**.

    ![Válassza ki az SQL Server Azure-beli virtuális gépen a biztonsági mentés](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    A **biztonsági mentés célja** menüben megjelenik a két lépésből áll: **Fedezze fel a virtuális gépeken lévő adatbázisok** és **biztonsági mentés konfigurálása**. A **virtuális gépeken lévő adatbázisok felderítése** lépés indítson keresést Azure-beli virtuális gépek.

    ![Tekintse át a két biztonsági mentés célja lépéseit](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. A **virtuális gépeken lévő adatbázisok felderítése**válassza **felderítés indítása lapra** , keresse meg az előfizetés nem védett virtuális gépek. Keresés az összes virtuális gépet egy ideig is eltarthat. A keresés időtartama attól függ, hogy az előfizetés nem védett virtuális gépek számát.

    ![Biztonsági mentés függőben a virtuális gépeken lévő adatbázisok keresés közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gépek listájából válassza ki a virtuális gép, amely rendelkezik az SQL-adatbázis biztonsági mentését, és válassza ki **adatbázisok felderítése**.

    A felderítési folyamat telepíti a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. A bővítmény lehetővé teszi, hogy a virtuális gép kommunikálni, ezért a biztonsági mentés az SQL-adatbázisok az Azure Backup szolgáltatással. A bővítmény telepítése után az Azure Backup hoz létre a Windows virtuális szolgáltatásfiókot **NT Service\AzureWLBackupPluginSvc** a virtuális gépen. A virtuális szolgáltatásfiókot az SQL-rendszergazdai engedély szükséges. A virtuális fiók telepítés során, ha a hibaüzenet `UserErrorSQLNoSysadminMembership`, lásd: [javítsa ki az SQL-rendszergazdai jogosultságokkal](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    A **értesítések** területen látható az adatbázis-felderítés előrehaladását. Eltarthat egy ideig, amíg a feladat befejeződik. A feladat idő attól függ, hány adatbázist vannak a virtuális gépen. A kijelölt adatbázisok észlelésekor megjelenik a sikert jelző üzenet.

    ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

Miután társította az adatbázis a Recovery Services-tárolóval, a következő lépés az, hogy [a biztonsági mentési feladat konfigurálása](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Javítsa ki az SQL-rendszergazdai engedélyek

A telepítés során, ha a hibaüzenet `UserErrorSQLNoSysadminMembership`, SQL Server SysAdmin (rendszergazda) engedélyekkel rendelkező fiók használatára, jelentkezzen be az SQL Server Management Studio (SSMS). Ha nincs szükség speciális engedélyek, a Windows-hitelesítés működnie kell.

1. Az SQL-kiszolgálón nyissa meg a biztonsági/bejelentkezések mappát.

    ![Nyissa meg a biztonsági/bejelentkezések mappában a fiókok](./media/backup-azure-sql-database/security-login-list.png)

2. Kattintson a jobb gombbal a bejelentkezések mappát, és válassza ki **új bejelentkezés**. Az a **bejelentkezés – új** párbeszédpanelen jelölje ki **keresési**.

    ![A bejelentkezés – új párbeszédpanel, válassza ki a keresés](./media/backup-azure-sql-database/new-login-search.png)

3. A virtuális Windows-szolgáltatásfiók **NT SERVICE\AzureWLBackupPluginSvc** a virtuális gép regisztrációja és SQL felderítési fázis során jött létre. Adja meg a fiók nevét, ahogyan az a **írja be a kijelölendő objektum nevét** mezőbe. Válassza ki **Névellenőrzés** oldani a nevet.

    ![Válassza ki a szolgáltatás ismeretlen név Névellenőrzés](./media/backup-azure-sql-database/check-name.png)

4. Válassza ki **OK** a párbeszédpanel bezárásához.

5. Az a **kiszolgálói szerepkörök** mezőbe, ellenőrizze, hogy a **SysAdmin (rendszergazda)** szerepkör van kiválasztva. Válassza ki **OK** a párbeszédpanel bezárásához.

    ![Győződjön meg arról, hogy a sysadmin (rendszergazda) kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

    A szükséges engedélyekkel most már léteznie kell.

6. Bár az engedélyekkel kapcsolatos hibát kijavított, kell az adatbázis társítani a Recovery Services-tároló. Az Azure Portalon az a **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló, amely a hibát az állami és válassza ki a **adatbázisok újbóli felderítése**.

    ![Ellenőrizze a kiszolgáló rendelkezik-e megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

    A **értesítések** területen látható az adatbázis-felderítés előrehaladását. Eltarthat egy ideig, amíg a feladat befejeződik. A feladat idő attól függ, hány adatbázist vannak a virtuális gépen. A kijelölt adatbázisok találhatók, a sikert jelző üzenet jelenik meg.

    ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

Miután társította az adatbázis a Recovery Services-tárolóval, a következő lépés az, hogy [a biztonsági mentési feladat konfigurálása](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

## <a name="sql-database-naming-guidelines-for-azure-backup"></a>Pokyny Pro pojmenování az Azure Backup SQL-adatbázis
Ahhoz, hogy az SQL Server IaaS-beli virtuális gépen az Azure Backup használatával biztonsági mentések zökkenőmentes lefutását, kerülje az adatbázisok elnevezésekor a következő:

  * Kezdő/záró szóköz
  * Záró '!'

Az Azure-tábla nem támogatott karaktereket aliasképző van, de javasoljuk, hogy kerülje ezek is. További információ: Ez [cikk](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Az SQL Server-adatbázisok felderítése

Az Azure Backup felderíti az összes adatbázis egy SQL Server-példányon. Az adatbázisok védheti meg a biztonsági mentés követelményeinek megfelelően. Az alábbi eljárás segítségével azonosítsa a virtuális gép, amelyen az SQL-adatbázisokat. Miután meghatározta a virtuális gépet, az Azure Backup telepíti egy egyszerűsített bővítmény, amely az SQL Server-adatbázisok felderítéséhez.

1. Jelentkezzen be az előfizetését a [az Azure portal](https://portal.azure.com/).

2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![Válassza ki az összes szolgáltatás](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Az a **minden szolgáltatás** párbeszédpanelen adja meg **Recovery Services**. Írja be, mert a bemeneti szűri az erőforrások listájában. Válassza ki **Recovery Services-tárolók** a listában.

  ![Adja meg, és válassza ki a Recovery Services-tárolók](./media/backup-azure-sql-database/all-services.png) <br/>

    Az előfizetés Recovery Services-tárolók listája megjelenik.

4. A Recovery Services-tárolók listájában válassza ki a tároló SQL-adatbázisok védelmét.

5. Az a **Recovery Services-tároló** irányítópulton válassza **Backup**. A **biztonsági mentés célja** menü megnyitása.

   ![A biztonsági mentés célja menüben nyissa meg a biztonsági másolat kiválasztása](./media/backup-azure-sql-database/open-backup-menu.png)

6. Az a **biztonsági mentés célja** menüben beállítása **a számítási feladat futtató** alapértelmezett: **Azure**.

7. Bontsa ki a **választható biztonsági mentéséhez** legördülő listát, és válassza ki **az SQL Server Azure virtuális gép**.

    ![Válassza ki az SQL Server Azure-beli virtuális gépen a biztonsági mentés](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    A **biztonsági mentés célja** menüben megjelenik a két lépésből áll: **Fedezze fel a virtuális gépeken lévő adatbázisok** és **biztonsági mentés konfigurálása**.

    ![Tekintse át a két biztonsági mentés célja lépéseit](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. A **virtuális gépeken lévő adatbázisok felderítése**válassza **felderítés indítása lapra** , keresse meg az előfizetés nem védett virtuális gépek. Eltarthat egy ideig át a virtuális gépek számát. A keresés időtartama attól függ, hogy az előfizetés nem védett virtuális gépek számát.

    ![Biztonsági mentés függőben a virtuális gépeken lévő adatbázisok keresés közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

    Miután egy nem védett virtuális gép fel van derítve, megjelenik a listában. Több virtuális gép rendelkezhet ugyanazzal a névvel. Ugyanazzal a névvel rendelkező virtuális gépek azonban eltérő erőforráscsoportokban tartozik. A virtuális gép nevére és erőforráscsoportjára csoport által nem védett virtuális gépek jelennek meg. Ha virtuális gépek várt nincs felsorolva, tekintse meg, ha a virtuális gép már védett tárolóra.

9. A virtuális gépek listájából válassza ki a virtuális gép, amely rendelkezik az SQL-adatbázis biztonsági mentését, és válassza ki **adatbázisok felderítése**.

    Az Azure Backup a virtuális gép összes SQL-adatbázisok deríti fel. Mi történik az adatbázis-felderítési fázis során kapcsolatos információkért lásd: [háttér-műveletek](backup-azure-sql-database.md#background-operations). Az SQL-adatbázisok a felderítésüket követően készen áll [a biztonsági mentési feladat konfigurálása](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Háttérbeli műveletek

Ha a **adatbázisok felderítése** eszközt, az Azure Backup a háttérben hajtja végre a következő műveleteket:

- Regisztrálja a virtuális gép számítási feladatok biztonsági mentés a Recovery Services-tárolót. A regisztrált virtuális gép összes adatbázis biztonsági mentésre alkalmas, csak a Recovery Services-tárolót.

- Telepítse a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Készítsen biztonsági másolatot egy SQL database egy ügynök nélküli kivételfigyelés megoldás is. A bővítmény telepítve van a virtuális gépen, és az SQL-adatbázis nincs ügynök telepítve.

- A szolgáltatásfiók létrehozása **NT Service\AzureWLBackupPluginSvc** a virtuális gépen. Minden biztonsági mentési és visszaállítási műveletek service fiókot használja. **NT Service\AzureWLBackupPluginSvc** SQL-rendszergazdai engedélyekre van szüksége. Az összes SQL-Piactéri virtuális gépek rendelkeznek az **SqlIaaSExtension** telepítve. A **AzureBackupWindowsWorkload** kiterjesztést használja a **SQLIaaSExtension** a szükséges engedélyek automatikus beszerzéséhez. Ha a virtuális gép nem rendelkezik a **SqlIaaSExtension** telepítve van, az adatbázis felderítése művelet meghiúsul, a hibaüzenet `UserErrorSQLNoSysAdminMembership`. Biztonsági mentés a sysadmin (rendszergazda) engedéllyel hozzáadásához kövesse a [beállítása az Azure biztonsági mentési engedélyek esetében nem Marketplace-en az SQL virtuális gépek](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>SQL Server-adatbázisok biztonsági mentés konfigurálása

Az Azure Backup az SQL Server-adatbázisok védelmét és biztonsági mentési feladatok kezelése a felügyeleti szolgáltatásokat biztosít. A felügyeleti és monitorozási feladatokat függ a Recovery Services-tárolóba.

> [!NOTE]
> Az SQL Server-adatbázisok biztonsági mentése egyszerre csak egy biztonsági mentési megoldás is rendelkezhet. Tiltsa le a többi SQL biztonsági másolatainak; Ez a funkció használata előtt Ellenkező esetben a biztonsági mentések zavarja és sikertelen lesz. Engedélyezheti az Azure Backup IaaS virtuális gép együtt az SQL biztonsági mentése bármely konfliktus nélkül.
>

SQL-adatbázis védelmének konfigurálása:

1. Nyissa meg a Recovery Services-tároló, amely regisztrálva van az SQL virtuális gép.

2. Az a **Recovery Services-tároló** irányítópulton válassza **Backup**. A **biztonsági mentés célja** menü megnyitása.

   ![A biztonsági mentés célja menüben nyissa meg a biztonsági másolat kiválasztása](./media/backup-azure-sql-database/open-backup-menu.png)

3. Az a **biztonsági mentés célja** menüben beállítása **a számítási feladat futtató** alapértelmezett: **Azure**.

4. Bontsa ki a **választható biztonsági mentéséhez** legördülő listát, és válassza ki **az SQL Server Azure virtuális gép**.

    ![Válassza ki az SQL Server Azure-beli virtuális gépen a biztonsági mentés](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    A **biztonsági mentés célja** menüben megjelenik a két lépésből áll: **Fedezze fel a virtuális gépeken lévő adatbázisok** és **biztonsági mentés konfigurálása**.

    Ha elvégezte a cikkben található lépések sorrendben, a nem védett virtuális gépek megismert, és a tár regisztrálva van a virtuális gépet. Most már készen áll az SQL-adatbázisok védelmének konfigurálása.

5. Az a **biztonsági mentés célja** menüjében válassza **biztonsági mentés konfigurálása**.

    ![Válassza ki a biztonsági mentés konfigurálása](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Az Azure Backup szolgáltatás minden SQL Server-példányokat az önálló adatbázisok és az SQL Server mindig a rendelkezésre állási csoportok jeleníti meg. Az önálló adatbázisok az SQL Server-példány megtekintéséhez jelölje ki a sávnyílra bal oldalán a példány nevét. Hasonló módon válassza ki a sávnyílra bal oldalán mindig a rendelkezésre állási csoport az adatbázisok listájának megtekintéséhez. Az alábbi képen egy példa egy különálló példány és a egy mindig a rendelkezésre állási csoportban.

      ![Az önálló adatbázisokhoz minden SQL Server-példányokat megjelenítése](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Az adatbázisok listájában válassza ki szeretné védeni, majd kattintson az összes adatbázis **OK**.

    ![Az adatbázis védelme érdekében](./media/backup-azure-sql-database/select-database-to-protect.png)

    Egyszerre legfeljebb 50 adatbázisok választhat. Több mint 50 adatbázisok védelméhez, győződjön meg arról, több fázisban. Miután az első 50 adatbázist lát el védelemmel, ismételje meg ezt a lépést a következő set-adatbázisok védelmét.

    > [!Note]
    > Biztonsági mentési terhelés optimalizálása érdekében az Azure Backup bontja nagy biztonsági mentési feladatok több köteg. Egy biztonsági mentési feladat adatbázisok maximális száma érték az 50.
    >

      Engedélyezheti azt is megteheti, [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) a teljes példánya vagy mindig a rendelkezésre állási csoport kiválasztásával a **ON** a megfelelő legördülő listában, a beállítás a **AUTOPROTECT**  oszlop. A [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) szolgáltatás nem csak lehetővé teszi, hogy egy nyissa meg az összes meglévő adatbázis védelmét, de automatikusan is védi a hozzáadni kívánt példányát, vagy a rendelkezésre állási csoport a jövőben az új adatbázisok.  

      ![Az Always On rendelkezésre állási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

7. Hozzon létre vagy válassza ki a biztonsági mentési házirend, a a **biztonsági mentési** menüjében válassza **biztonsági mentési szabályzat**. A **biztonsági mentési szabályzat** menü megnyitása.

    ![Válassza ki a biztonsági mentési szabályzat](./media/backup-azure-sql-database/select-backup-policy.png)

8. Az a **biztonsági mentési házirend kiválasztása** legördülő listában válassza ki a biztonsági mentési szabályzatot, és válassza ki **OK**. Biztonsági mentési szabályzat létrehozásáról információért lásd: [biztonsági mentési házirend meghatározása](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Az előzetes verzióban már nem szerkesztheti a biztonsági mentési szabályzatok. Ha azt szeretné, hogy egy másik szabályzatot mint mi érhető el, a listában, létre kell hoznia a szabályzatot. Egy új biztonsági mentési szabályzat létrehozásával kapcsolatos információkért lásd: a szakasz [biztonsági mentési házirend meghatározása](backup-azure-sql-database.md#define-a-backup-policy).

    ![A biztonsági mentési szabályzat kiválasztása a listából](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Az a **biztonsági mentési szabályzat** menüben, a a **biztonsági mentési házirend kiválasztása** legördülő lista is:
    - Válassza ki az alapértelmezett házirend: **HourlyLogBackup**.
    - Válassza ki a korábban létrehozott SQL meglévő biztonsági mentési házirend.
    - [Adja meg egy új házirendet](backup-azure-sql-database.md#define-a-backup-policy) a helyreállítási Időkorlát és a megőrzési tartomány alapján.

    > [!Note]
    > Az Azure Backup támogatja a hosszú távú megőrzése a nagyapa-apa-fiú biztonsági mentési rendszer alapján. A rendszer optimalizálja a háttér-tároló fogyasztása, miközben értekezlet megfelelőségi igényekkel.
    >

9. A biztonsági mentési házirend kiválasztása után a **biztonsági mentés menü**válassza **biztonsági mentés engedélyezése**.

    ![a választott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

    A konfigurációs folyamat nyomon a **értesítések** a portál területéhez.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)


## <a name="auto-protect-sql-server-in-azure-vm"></a>SQL Server automatikus védelme az Azure-beli virtuális gépen  

Automatikus védelem lehetővé teszi, hogy minden meglévő és adatbázisok, akkor adja hozzá a jövőben az önálló SQL Server-példányt, vagy egy SQL Server mindig a rendelkezésre állási csoport automatikus védelmét. Bekapcsolásával **ON** automatikus védelem és a egy biztonsági mentési házirend kiválasztásához az újonnan védett adatbázisok vonatkoznak, a meglévő védett adatbázisok továbbra is az előző szabályzatot.

![Az Always On rendelkezésre állási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

Egy go get kiválasztott adatbázisok száma nincs korlátozva használatával automatikus védelmet a szolgáltatás. Konfigurálása biztonsági mentés akkor aktiválódik, az adatbázisokat együtt, és követhető nyomon a **biztonsági mentési feladatok**.

Ha valamilyen okból módosítania tiltsa le az automatikus védelem-példányon, kattintson a példány neve alatt **biztonsági mentés konfigurálása** , nyissa meg a jobb oldali információk panelt, amelyen **letiltása Autoprotect** a a felső. Kattintson a **letiltása Autoprotect** azon a példányon automatikus védelem letiltásához.

![Tiltsa le az automatikus védelmét azon a példányon](./media/backup-azure-sql-database/disable-auto-protection.png)

Ebben az esetben az összes adatbázis továbbra is védeni kell. Azonban ez a művelet letiltja a bármely később hozzáadott adatbázisok az automatikus védelem.

### <a name="define-a-backup-policy"></a>Biztonsági mentési szabályzat meghatározása

Biztonsági mentési házirend egy mátrixot határoz meg, amikor a biztonsági másolatokat készít, és mennyi ideig tartott használ. Használja az Azure Backup három típusú SQL-adatbázisok biztonsági mentés ütemezése:

* Teljes biztonsági mentés: Teljes biztonsági mentés a teljes adatbázis biztonsági mentését. Egy teljes biztonsági mentés az adatok egy adott adatbázisban vagy fájlcsoport vagy fájlokat, és elég naplók az adatok helyreállítása egy készletét tartalmazza. Legfeljebb napi egy teljes biztonsági mentés is indíthat. Ha szeretné igénybe vehet egy teljes biztonsági mentés napi vagy heti időközönként.
* Különbségi biztonsági másolat: Különbözeti biztonsági mentése a legújabb, az előző teljes biztonsági mentését alapul. Különbözeti biztonsági mentése csak a teljes biztonsági mentés óta megváltozott adatokat rögzíti. Legfeljebb napi egy különbségi biztonsági mentés is indíthat. Az azonos napi egy teljes biztonsági mentés és a különbözeti biztonsági mentése nem konfigurálható.
* Tranzakciónapló biztonsági mentését: A napló biztonsági mentését lehetővé teszi, hogy legfeljebb egy adott második időponthoz visszaállítást. Egyszerre legfeljebb konfigurálhatja tranzakciós napló biztonsági mentés 15 percenként.

A házirendet létrehozni a Recovery Services-tároló szint. Több tárolóhoz is használhat az azonos mentési házirenddel, de a biztonsági mentési házirendet kell alkalmaznia az egyes tárolókban. Amikor létrehoz egy biztonsági mentési szabályzatot, a napi teljes biztonsági mentést az alapértelmezett érték. Különbségi biztonsági másolat, de csak is hozzáadhat, ha teljes biztonsági mentés hetente történjen. Az alábbi eljárás ismerteti egy SQL Server-példány biztonsági mentési szabályzat létrehozása az Azure virtuális gépként.

> [!NOTE]
> Előzetes verzióban elérhető a biztonsági mentési szabályzat nem szerkeszthető. Ehelyett egy új házirendet kell létrehoznia a kívánt adatokkal.  

Biztonsági mentési szabályzat létrehozása:

1. Kattintson a Recovery Services-tároló, amely védelmet nyújt az SQL database, **biztonsági mentési házirendek**, és kattintson a **Hozzáadás**.

   ![Nyissa meg az új biztonsági mentési szabályzat létrehozása párbeszédpanel](./media/backup-azure-sql-database/new-policy-workflow.png)

   A **Hozzáadás** menü jelenik meg.

2. Az a **Hozzáadás** menüben kattintson a **az SQL Server Azure virtuális gép**.

   ![Olyan házirendtípus, az új biztonsági mentési házirend kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

   Válassza ki az SQL Server Azure virtuális gép határozza meg a házirend típusát, és megnyílik a biztonsági mentési szabályzat menüje. A **biztonsági mentési szabályzat** menü jeleníti meg a mezőket, egy új SQL Server biztonsági mentési szabályzat szükséges.

3. A **házirendnév**, adja meg az új házirend nevét.

4. Egy teljes biztonsági mentés kötelező; nem kapcsolhatja ki az **teljes biztonsági mentés** lehetőséget. Kattintson a **teljes biztonsági mentés** megtekintheti és szerkesztheti a szabályzatot. Akkor is, ha nem módosítja a biztonsági mentési szabályzatot, akkor érdemes a szabályzat részleteinek megtekintéséhez.

    ![új biztonsági mentési szabályzat mezők](./media/backup-azure-sql-database/full-backup-policy.png)

    Az a **teljes biztonsági mentés házirend** menüben a **biztonsági mentés gyakorisága**, válassza a **napi** vagy **heti**. A **napi**, válasszon ki és az időzónát, amikor megkezdődik a biztonsági mentési feladat. Különbségi biztonsági mentés napi teljes biztonsági mentések esetén nem hozható létre.

   ![napi időköz beállítása](./media/backup-azure-sql-database/daily-interval.png)

    A **heti**, amikor megkezdődik a biztonsági mentési feladatot, válassza ki a nap, hét, óra és időzóna.

   ![heti időköz beállítása](./media/backup-azure-sql-database/weekly-interval.png)

5. Alapértelmezés szerint minden **megőrzési** lehetőség be van jelölve: napi, heti, havi és éves. Kapcsolja ki a nemkívánatos megőrzési tartomány korlátait. A használandó időközt beállítani. Az a **teljes biztonsági mentés házirend** menüjében válassza **OK** , fogadja el a beállításokat.

   ![Megőrzési tartomány intervallum beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

    Helyreállítási pontok megőrzésének a megőrzési tartomány alapján címkével vannak ellátva. Ha például napi teljes biztonsági mentést választja, csak egy teljes biztonsági mentés naponta aktiválódik. A biztonsági mentés meghatározott napja címkézett és a megőrzött alapján a heti megőrzési időtartam és a heti adatmegőrzési beállítás. A havi és éves megőrzési időtartamok viselkedése hasonló módon.

6. A különbözeti biztonsági mentési szabályzat hozzáadásához válassza **különbözeti biztonsági mentési**. A **különbözeti biztonsági mentési szabályzat** menü megnyitása.

   ![Nyissa meg a különbözeti biztonsági mentési szabályzat menüje](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Az a **különbözeti biztonsági mentési szabályzat** menüjében válassza **engedélyezése** a gyakoriság és megőrzési vezérlőelemek megnyitásához. Legfeljebb napi egy különbségi biztonsági mentés is indíthat.

    > [!Important]
    > Különbségi biztonsági másolatok megőrzésének legfeljebb 180 napra. Ha hosszabb adatmegőrzés megadásához, teljes biztonsági mentést kell használnia.
    >

   ![A különbözeti biztonsági mentési szabályzat szerkesztése](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Válassza ki **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.

7. A tranzakciós napló biztonsági mentési szabályzat hozzáadásához válassza **napló biztonsági mentési**. A **Naplóalapú biztonsági mentés** menü megnyitása.

    Az a **Naplóalapú biztonsági mentés** menüjében válassza **engedélyezése**, majd állítsa be a gyakoriság és megőrzési szabályozza. Naplóalapú biztonsági mentések fordulhat elő, mint 15 perces gyakorisággal, és akár 35 napig lehet megtartani. Válassza ki **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.

   ![A napló biztonsági mentési szabályzat szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. Az a **biztonsági mentési szabályzat** menüben válassza ki, hogy engedélyezi-e **SQL biztonsági másolat tömörítése**. A tömörítés alapértelmezés szerint le van tiltva.

    A háttérben az Azure Backup használ SQL natív biztonsági mentési tömörítését.

9. Miután elvégezte a módosításokat a biztonsági mentési szabályzathoz, válassza ki a **OK**.

   ![Fogadja el az új biztonsági mentési házirend](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>SQL-adatbázis visszaállítása
Az Azure Backup az önálló adatbázisok visszaállítása egy adott dátum vagy idő (a második) a tranzakciónapló biztonsági mentései használatával funkciót biztosít. Az Azure Backup automatikusan a megfelelő teljes különbségi és naplóalapú biztonsági mentések, amelyek alapján a helyreállítási időt az adatok visszaállításához van szükség, a lánc határozza meg.

Lehetőség kiválasztásával egy teljes vagy különbözeti biztonsági mentési állíthatja vissza egy adott helyreállítási pontot, nem pedig egy adott időpontban.

### <a name="pre-requisite-before-triggering-a-restore"></a>A visszaállítás elindítása előtt előfeltételeként

1. Az adatbázist visszaállíthatja egy példányát olyan SQL Server ugyanazon Azure-régióban. A célkiszolgáló ugyanazt a Recovery Services-tároló forrásaként regisztrálnia kell.  
2. A TDE titkosított adatbázis visszaállítása egy másik SQL Serverre, először állítsa vissza a tanúsítványt a a célkiszolgáló által leírt lépéseket követve [Itt](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Mielőtt a "fő" adatbázis visszaállítási indít el, indítsa el az SQL Server-példány egyfelhasználós módban indítási lehetőséget `-m AzureWorkloadBackup`. Az argumentum a `-m` lehetőség az ügyfél nevét. Csak az ügyfél számára engedélyezett a kapcsolat megnyitásához. Az összes rendszer adatbázisban (modell master, msdb) állítsa le az SQL Agent szolgáltatást a visszaállítás elindítása előtt. Zárja be az olyan alkalmazásokat, amelyek próbálnak kapcsolatot sem ezeknek az adatbázisoknak ellopni.

### <a name="steps-to-restore-a-database"></a>Adatbázis visszaállítása a következő lépéseket:

1. Nyissa meg a Recovery Services-tároló, amely regisztrálva van az SQL virtuális gép.

2. Az a **Recovery Services-tároló** irányítópult alatt **használati**válassza **biztonsági másolati elemek** megnyitásához a **biztonsági másolati elemek** menü.

    ![A biztonsági másolati elemek menü megnyitásához](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Az a **biztonsági másolati elemek** menü alatt **biztonságimásolat-kezelés típusa**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A **biztonsági másolati elemek** menü SQL-adatbázisok listáját jeleníti meg.

4. SQL-adatbázisok listájában válassza ki az adatbázis visszaállításához.

    ![Válassza ki a visszaállítandó adatbázis](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Ha az adatbázist választja, megnyílik a menü. A menü biztosít a biztonsági mentés részletei az adatbázisban, többek között:

    * A legrégebbi és a legújabb visszaállítási pontot.
    * Napló biztonsági mentésének állapota az elmúlt 24 órában (az adatbázisokat a teljes és a tömegesen naplózott helyreállítási modell, ha a tranzakciós napló biztonsági mentés konfigurálva).

5. Válassza a menü a kiválasztott adatbázishoz, **visszaállítása DB**. A **visszaállítása** menü megnyitása.

    ![Válassza ki a Restore DB](./media/backup-azure-sql-database/restore-db-button.png)

    Ha a **visszaállítása** menü megnyílik, a **konfiguráció visszaállítása** menüben is megnyílik. A **konfiguráció visszaállítása** menü első lépése a visszaállítás konfigurálása. Ez a menü segítségével válassza ki, hogy az adatok hol. A következő lehetőségek közül választhat:
    - **Másik helyre**: Állítsa vissza az adatbázist egy másik helyre, és az eredeti adatbázis megőrzése.
    - **Adatbázis felülírása**: Állítsa vissza az adatokat az eredeti forrás, ugyanazon SQL Server-példányon. Ez a beállítás hatását, hogy az eredeti adatbázis felülírásához.

    > [!Important]
    > Ha a kijelölt adatbázis mindig a rendelkezésre állási csoporthoz tartozik, az SQL Server felülírja az adatbázis nem teszi lehetővé. Ebben az esetben, csak a **máshová** beállítás engedélyezve van.
    >

    ![Visszaállítási konfiguráció menü](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

Ez az eljárás lépésről lépésre adatok visszaállítása másik helyre. Az adatbázis felülírásához a visszaállítás során, továbbra is [visszaállítása és az adatbázis felülírása](backup-azure-sql-database.md#restore-and-overwrite-the-database). Ezen a ponton a Recovery Services-tároló meg nyitva, és a **konfiguráció visszaállítása** menü látható-e. Ha Ön nem ezen a ponton, első lépésként [egy SQL-adatbázis visszaállítása](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Az adatbázist visszaállíthatja egy példányát olyan SQL Server ugyanazon Azure-régióban. A célkiszolgáló regisztrálva kell lennie a Recovery Services-tárolóba.
>

Az a **konfiguráció visszaállítása** menüben a **kiszolgáló** legördülő lista csak az SQL Server-példányokat a helyreállítási tárban regisztrált jeleníti meg. Ha azt szeretné, hogy a kiszolgáló nem szerepel a listán, [Fedezze fel az SQL Server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases) keresnie. A felderítési folyamat során az új kiszolgálókat a Recovery Services-tároló van regisztrálva.

1. Az a **konfiguráció visszaállítása** menüben:

    * Alatt **visszaállítás helyének**válassza **máshová**.
    * Nyissa meg a **kiszolgáló** legördülő listában és válassza az SQL Server-példányon állítsa vissza az adatbázist.
    * Nyissa meg a **példány** legördülő listában és válassza az SQL Server-példányt.
    * Az a **visszaállított adatbázis neve** mezőbe írja be a céladatbázis neve.
    * A megfelelő, válassza **felülírása, ha a kiválasztott SQL-példányon már létezik a DB ugyanazzal a névvel**.
    * Válassza ki **OK** konfigurációjához a célhelyen, és továbbra is válasszon visszaállítási pontot.

    ![A konfiguráció visszaállítása menü értékeinek megadása](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Az a **visszaállítási pont kiválasztása** menüben válassza a **naplók (idő)** vagy **teljes és különbségi** visszaállítási pontot. Állítani egy adott időpontban a naplót, folytassa az ehhez a lépéshez. Állítsa vissza a teljes és különbségi visszaállítási pontot, folytassa a 3. lépés.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    A pont – történő visszaállítás csak olyan adatbázisok esetén a teljes és a tömegesen naplózott helyreállítási modell a naplóalapú biztonsági mentések érhető el. Adott időpontra való visszaállítása időben:

    1. Válassza ki **naplók (idő)** visszaállítási típusaként.

        ![Válassza ki a visszaállítási pont](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. A **dátum és idő visszaállításához**, válassza ki a mini naptár megnyitása a **naptár**. Az a **naptár**, félkövérrel szedett dátumokra rendelkezik a helyreállítási pontok, és az aktuális dátumot ki van jelölve. Jelölje ki a helyreállítási pontok dátumát. Dátumok helyreállítási pontok nélkül nem lehet kiválasztani.

        ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Miután kiválasztotta a dátumot, az ütemterv graph folyamatos számos jeleníti meg a rendelkezésre álló helyreállítási pontok.

    3. Az ütemterv graph használata vagy a **idő** párbeszédpanelen megadhatja egy adott a helyreállítási pontok időpontját. Válassza ki **OK** a visszaállítási pont lépésnek a végrehajtásához.

       ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        A **visszaállítási pont kiválasztása** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Az a **speciális konfiguráció** menüben:

        * Ha szeretné megtartani az adatbázis nem működő a visszaállítás után, állítsa be **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** való **engedélyezve**.
        * A célkiszolgálón lévő visszaállítási hely átnevezéséhez írja be az új elérési utat a **cél** oszlop.
        * Válassza ki **OK** hagyható jóvá a beállításokat. Zárja be a **speciális konfiguráció** menü.

    5. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához. A visszaállítás, az előrehaladását úgy követheti nyomon a **értesítések** terület, vagy válasszon **visszaállítási feladatok** adatbázis menüben.

       ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

3. Az a **visszaállítási pont kiválasztása** menüben válassza a **naplók (idő)** vagy **teljes és különbségi** visszaállítási pontot. Vissza szeretné állítani egy időponthoz naplót, lépjen vissza a 2. lépés. Ebben a lépésben egy adott teljes vagy különbözeti visszaállítási pontot állítja vissza. A teljes és különbségi helyreállítási pontok összes láthatja az elmúlt 30 napra vonatkozóan. 30 napnál régebbi helyreállítási pontok megtekintéséhez válasszon **szűrő** megnyitásához a **szűrő visszaállítási pontok** menü. Különbözeti helyreállítási pontok az Azure Backup először visszaállítja a megfelelő teljes helyreállítási pontot, és majd alkalmazza a kiválasztott különbözeti helyreállítási pontot.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Az a **visszaállítási pont kiválasztása** menüben kattintson **teljes és különbségi**.

       ![Válassza ki a teljes és különbségi](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        A menüben elérhető helyreállítási pontok listáját jeleníti meg.

    2. Válasszon ki egy helyreállítási pontot a listából, és válassza ki **OK** a visszaállítási pont eljárást.

        ![Válasszon egy teljes helyreállítási pontot](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        A **visszaállítási pont** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

        ![Speciális konfigurációs menüje](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Az a **speciális konfiguráció** menüben:

        * Ha szeretné megtartani az adatbázis nem működő a visszaállítás után, állítsa be **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** való **engedélyezve**. **Visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** alapértelmezés szerint le van tiltva.
        * A célkiszolgálón lévő visszaállítási hely átnevezéséhez írja be az új elérési utat a **cél** oszlop.
        * Válassza ki **OK** hagyható jóvá a beállításokat. Zárja be a **speciális konfiguráció** menü.

    4. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához. A visszaállítás, az előrehaladását úgy követheti nyomon a **értesítések** terület, vagy válasszon **visszaállítási feladatok** adatbázis menüben.

       ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Állítsa vissza, és az adatbázis felülírása

Ez az eljárás végigvezeti adatok visszaállítása és a egy adatbázis felülírása. Visszaállítás másik helyre, folytassa [visszaállítás másik helyre](backup-azure-sql-database.md#restore-to-an-alternate-location). Ezen a ponton a Recovery Services-tároló meg nyitva, és a **konfiguráció visszaállítása** menü látható (lásd az alábbi képen). Ha Ön nem ezen a ponton, első lépésként [egy SQL-adatbázis visszaállítása](backup-azure-sql-database.md#restore-a-sql-database).

![Visszaállítási konfiguráció menü](./media/backup-azure-sql-database/restore-overwrite-db.png)

Az a **konfiguráció visszaállítása** menüben a **kiszolgáló** legördülő lista csak az SQL Server-példányokat a helyreállítási tárban regisztrált jeleníti meg. Ha azt szeretné, hogy a kiszolgáló nem szerepel a listán, [Fedezze fel az SQL Server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases) keresnie. A felderítési folyamat során az új kiszolgálókat a Recovery Services-tároló van regisztrálva.

1. Az a **konfiguráció visszaállítása** menüben válassza **adatbázis felülírása**, majd válassza ki **OK** a cél a konfiguráció befejezéséhez.

   ![Válassza ki az adatbázis felülírása](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    A **kiszolgáló**, **példány**, és **visszaállított adatbázis neve** beállításai nem szükséges.

2. Az a **visszaállítási pont kiválasztása** menüben válassza a **naplók (idő)** vagy **teljes és különbségi** visszaállítási pontot. Állítani egy adott időpontban a naplót, folytassa az ehhez a lépéshez. Állítsa vissza a teljes és különbségi visszaállítási pontot, folytassa a 3. lépés.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    A pont – történő visszaállítás csak olyan adatbázisok esetén a teljes és a tömegesen naplózott helyreállítási modell a naplóalapú biztonsági mentések érhető el. Egy adott második visszaállítása:

    1. Válassza ki **naplók (idő)** visszaállítási pontot.

        ![Válassza ki a visszaállítási pont](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. A **dátum és idő visszaállításához**, válassza ki a mini naptár megnyitása a **naptár**. Az a **naptár**, félkövérrel szedett dátumokra rendelkezik a helyreállítási pontok, és az aktuális dátumot ki van jelölve. Jelölje ki a helyreállítási pontok dátumát. Dátumok helyreállítási pontok nélkül nem lehet kiválasztani.

        ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Miután kiválasztotta a dátumot, az ütemterv graph megjeleníti a rendelkezésre álló helyreállítási pontok.

    3. Az ütemterv graph használata vagy a **idő** párbeszédpanelen megadhatja egy adott a helyreállítási pontok időpontját. Válassza ki **OK** a visszaállítási pont lépésnek a végrehajtásához.

       ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        A **visszaállítási pont kiválasztása** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

       ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Az a **speciális konfiguráció** menüben:

        * Ha szeretné megtartani az adatbázis nem működő a visszaállítás után, állítsa be **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** való **engedélyezve**.
        * A célkiszolgálón lévő visszaállítási hely átnevezéséhez írja be az új elérési utat a **cél** oszlop.
        * Válassza ki **OK** hagyható jóvá a beállításokat. Zárja be a **speciális konfiguráció** menü.

    5. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához. A visszaállítás, az előrehaladását úgy követheti nyomon a **értesítések** terület, vagy válasszon **visszaállítási feladatok** adatbázis menüben.

       ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

3. Az a **visszaállítási pont kiválasztása** menüben válassza a **naplók (idő)** vagy **teljes és különbségi** visszaállítási pontot. Vissza szeretné állítani egy időponthoz naplót, lépjen vissza a 2. lépés. Ebben a lépésben egy adott teljes vagy különbözeti visszaállítási pontot állítja vissza. A teljes és különbségi helyreállítási pontok összes láthatja az elmúlt 30 napra vonatkozóan. 30 napnál régebbi helyreállítási pontok megtekintéséhez válasszon **szűrő** megnyitásához a **szűrő visszaállítási pontok** menü. Különbözeti helyreállítási pontok az Azure Backup először visszaállítja a megfelelő teljes helyreállítási pontot, és majd alkalmazza a kiválasztott különbözeti helyreállítási pontot.

    ![Válassza ki a helyreállítási pont menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Az a **visszaállítási pont kiválasztása** menüben kattintson **teljes és különbségi**.

       ![Válassza ki a teljes és különbségi](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        A menüben elérhető helyreállítási pontok listáját jeleníti meg.

    2. Válasszon ki egy helyreállítási pontot a listából, és válassza ki **OK** a visszaállítási pont eljárást.

        ![Válasszon egy teljes helyreállítási pontot](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        A **visszaállítási pont** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

        ![Speciális konfigurációs menüje](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Az a **speciális konfiguráció** menüben:

        * Ha szeretné megtartani az adatbázis nem működő a visszaállítás után, állítsa be **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** való **engedélyezve**. **Visszaállítás a NORECOVERY BEÁLLÍTÁSSAL** alapértelmezés szerint le van tiltva.
        * A célkiszolgálón lévő visszaállítási hely átnevezéséhez írja be az új elérési utat a **cél** oszlop.
        * Válassza ki **OK** hagyható jóvá a beállításokat. Zárja be a **speciális konfiguráció** menü.

    4. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához. A visszaállítás, az előrehaladását úgy követheti nyomon a **értesítések** területen vagy kiválasztásával **visszaállítási feladatok** adatbázis menüben.

       ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Azure virtuális gépeken az SQL Azure Backup-műveletek kezelése

Ez a szakasz ismerteti a különböző Azure Backup felügyeleti műveletek, amelyek az SQL Azure-beli virtuális gépeken érhető el. A következő magas szintű műveletek találhatók:

* Feladatok figyelése
* Biztonsági mentési riasztások
* SQL-adatbázis védelmének megszüntetése
* Az SQL-adatbázis védelmének folytatása
* Az ad hoc biztonsági mentési feladat aktiválása
* Egy SQL Servert futtató kiszolgáló regisztrációjának törlése

### <a name="monitor-backup-jobs"></a>Biztonsági mentési feladatok figyelése
Az Azure Backup egy vállalati osztály megoldás által biztosított fejlett biztonsági mentési riasztások és értesítések az esetleges hibákat. (Lásd: [biztonsági mentésekkel kapcsolatos riasztások megtekintése](backup-azure-sql-database.md#view-backup-alerts).) Adott feladatok monitorozásához használja a következő beállításokat a követelményeinek megfelelően.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Az Azure portal használata az ad hoc ad hoc műveletek
Az Azure Backup megjeleníti az összes manuálisan aktivált, vagy ad hoc, a feladatok a **biztonsági mentési feladatok** portálon. A feladatok érhetők el a **biztonsági mentési feladatok** portál tartalmazza:
- Az összes konfigurálja a biztonsági mentési műveleteket.
- Manuálisan aktivált biztonsági mentési műveleteket.
- A visszaállítási műveleteket.
- Regisztráció és Fedezze fel az adatbázis-műveletek.
- Állítsa le a biztonsági mentési műveleteket.

![Biztonsági mentési feladatok portál](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Az összes ütemezett biztonsági mentési feladatot, beleértve a teljes, különbségi és naplóalapú biztonsági mentés, nem láthatók a **biztonsági mentési feladatok** portálon. Az SQL Server Management Studio figyelése ütemezett biztonsági mentési feladatok, a következő szakaszban leírtak szerint.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>A biztonsági mentési feladatok SQL Server Management Studio használata
Az Azure Backup minden biztonsági mentési műveletek az SQL natív API-kat használ. A natív API-k segítségével kéri le az összes feladat adatait a [SQL biztonságimásolat-készletet létrehozták tábla](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) az msdb adatbázisban.

Az alábbi példa egy lekérdezést, amely beolvassa az összes biztonsági mentési feladat nevű adatbázishoz **DB1**. A lekérdezés a speciális monitorozás testreszabása.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

### <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Mivel a naplóalapú biztonsági mentések fordulnak elő, 15 percenként, időnként, biztonsági mentési feladatok figyelése fárasztó feladat lehet. Az Azure Backup ebben a helyzetben segítséget nyújt. E-mailt a figyelmeztetéseket az összes biztonsági mentési hibák. Riasztások az adatbázis szintjén vannak konszolidált hibakód alapján. Értesítő e-mail érkezik, csak az első biztonsági mentés sikertelen egy adatbázis esetében. Jelentkezzen be az adatbázis összes hibák figyelése az Azure Portalon.

Biztonsági mentésekkel kapcsolatos riasztások figyelése:

1. Jelentkezzen be az Azure-előfizetését a [az Azure portal](https://portal.azure.com).

2. Nyissa meg a Recovery Services-tároló, amely regisztrálva van az SQL virtuális gép.

3. Az a **Recovery Services-tároló** irányítópulton válassza **riasztások és események**.

   ![Válassza ki a riasztások és események](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Az a **riasztások és események** menüjében válassza **biztonsági mentési riasztások** riasztások listájának megtekintéséhez.

   ![Válassza ki a biztonsági mentési riasztások](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>SQL Server-adatbázisok védelmének megszüntetése

Az SQL Server-adatbázis védelmének leállításakor az Azure Backup kéri-e őrizni a helyreállítási pontokat. Kétféleképpen SQL-adatbázis védelmének megszüntetése:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törli az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de hagyja meg a helyreállítási pontokat.

Ha úgy dönt, hogy a biztonsági mentés leállítása az adatok megőrzésével, a helyreállítási pontok a biztonsági mentési szabályzatának megfelelően törlődnek. A védett SQL-példány, ingyenesen, valamint a felhasznált, amíg az összes helyreállítási pont megtisztítja a storage díjszabása számítunk fel. Az SQL Azure Backup árairól kapcsolatos további információkért lásd: a [Azure Backup díjszabási oldala](https://azure.microsoft.com/pricing/details/backup/).

Egy adatbázis védelmének megszüntetése:

1. Nyissa meg a Recovery Services-tároló, amely regisztrálva van az SQL virtuális gép.

2. Az a **Recovery Services-tároló** irányítópult alatt **használati**válassza **biztonsági másolati elemek** megnyitásához a **biztonsági másolati elemek** menü.

    ![A biztonsági másolati elemek menü megnyitásához](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Az a **biztonsági másolati elemek** menü alatt **biztonságimásolat-kezelés típusa**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A **biztonsági másolati elemek** menü SQL-adatbázisok listáját jeleníti meg.

4. SQL-adatbázisok listájában válassza ki az adatbázis védelmének leállítása érdekében.

    ![Válassza ki az adatbázis védelmének megszüntetése](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Ha az adatbázist választja, megnyílik a menü.

5. Válassza a menü a kiválasztott adatbázishoz, **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés leállítása](./media/backup-azure-sql-database/stop-db-button.png)

    A **biztonsági mentés leállítása** menü megnyitása.

6. Az a **biztonsági mentés leállítása** menüben válassza ki a **biztonsági másolatok adatainak megőrzése** vagy **biztonsági másolatok adatainak törlése**. Szükség esetén itt adhatja meg a Leállítás, a védelem és a egy megjegyzés.

    ![Állítsa le a biztonsági mentés menü](./media/backup-azure-sql-database/stop-backup-button.png)

7. Válassza ki **biztonsági mentés leállítása** kikapcsolni a védelmet az adatbázison.

  Vegye figyelembe, hogy **biztonsági mentés leállítása** lehetőség nem működik egy adatbázis- [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) példány. Ez az adatbázis védelmének megszüntetése csak úgy, hogy tiltsa le a [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) zajlik a példányon majd válassza a **biztonsági mentés leállítása** lehetőség **biztonsági másolati elemek**az adatbázishoz.<br>
  Automatikus védelem le van tiltva, miután **biztonsági mentés leállítása** alatt az adatbázis **biztonsági másolati elemek**. A példány újra engedélyezheti az automatikus védelemhez most.


### <a name="resume-protection-for-a-sql-database"></a>Az SQL-adatbázis védelmének folytatása

Ha a **biztonsági másolatok adatainak megőrzése** lehetőséget választotta, amikor az SQL-adatbázis védelmét le lett állítva, folytathatja a védelmet. Ha a biztonsági mentési adatok nem őrződnek meg, a védelem nem folytatható.

1. Az SQL-adatbázis védelmének visszaállításához, nyissa meg a biztonsági mentési elemet, és válassza **biztonsági mentés folytatása**.

    ![Válassza ki a biztonsági mentés folytatása adatbázis védelmének folytatása](./media/backup-azure-sql-database/resume-backup-button.png)

   A **biztonsági mentési szabályzat** menü megnyitása.

2. Az a **biztonsági mentési szabályzat** menüben válasszon ki egy szabályzatot, és válassza **mentése**.

### <a name="trigger-an-adhoc-backup"></a>Eseményindító-ad hoc biztonsági mentés

Ad hoc ad hoc biztonsági mentés indítása, igény szerint. Ad hoc ad hoc biztonsági mentések négy típusa van:

* Teljes biztonsági mentés
* Csak másolatot teljes biztonsági mentés
* Különbségi biztonsági mentés
* Naplóalapú biztonsági mentés

További információ az egyes típusok: [típusok az SQL biztonsági másolatainak](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>SQL Server-példány regisztrációjának törlése

SQL Server-példány regisztrációját a védelem eltávolítása után, de a tároló törlése előtt:

1. Nyissa meg a Recovery Services-tároló, amely regisztrálva van az SQL virtuális gép.

2. Az a **Recovery Services-tároló** irányítópult alatt **kezelés**válassza **biztonsági mentési infrastruktúra**.  

   ![Válassza ki a biztonsági mentési infrastruktúrára](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. A **felügyeleti kiszolgálók**válassza **védett kiszolgálók**.

   ![Válassza ki a védett kiszolgálók](./media/backup-azure-sql-database/protected-servers.png)

    A **védett kiszolgálók** menü megnyitása.

4. Az a **védett kiszolgálók** menüben válassza ki a kiszolgáló regisztrációjának törlése. A tároló törléséhez az összes kiszolgáló kell regisztrációját.

5. Az a **védett kiszolgálók** menüben kattintson a jobb gombbal a védett kiszolgálón, és válassza ki **törlése**.

   ![Válassza a Törlés](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>GYIK

A következő szakaszban további információkat az SQL-adatbázis biztonsági mentése tartalmaz.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Szabályozhatja a sebességétől, az SQL Server biztonsági mentési szabályzatot?

Igen. A sebesség, amellyel a biztonsági mentési szabályzat hajt végre egy SQL Server-példány gyakorolt hatás minimalizálása érdekében szabályozhatja.
A beállítás módosítása:
1. Az SQL Server-példányon az a *C:\Program Files\Azure munkaterhelés Backup\bin mappa*, hozzon létre a **ExtensionSettingsOverrides.json** fájlt.
2. Az a **ExtensionSettingsOverrides.json** fájl, módosítsa a **DefaultBackupTasksThreshold** alacsonyabb értékűre (például 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Mentse a módosításokat. Zárja be a fájlt.
4. Nyissa meg az SQL Server-példány **Feladatkezelő**. Indítsa újra a **AzureWLBackupCoordinatorSvc** szolgáltatás.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok egy teljes biztonsági mentés egy másodlagos replikáról?
Nem. Ez a funkció nem támogatott.

### <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok ne hozzon létre riasztásokat?

Nem. A sikeres biztonsági mentési feladatok ne készítsen riasztásokat. Csak a biztonsági mentési feladatok sikertelenek, riasztást kap.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Láthatja a feladatok menü ütemezett biztonsági mentési feladatok részleteit?

Nem. A **biztonsági mentési feladatok** menü mutatja az ad hoc ad hoc feladat részleteit, de nem ütemezett biztonsági mentési feladatot. Ha bármely ütemezett biztonsági mentési feladat sikertelen, a részletek a sikertelen feladat riasztások érhető el. Figyelheti az összes ütemezett és ad hoc ad hoc biztonsági mentési feladatot, használja [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Ha kiválasztom a egy SQL Server-példány azok automatikusan hozzáadott jövőbeli adatbázisok?

Nem. Amikor konfigurál egy SQL Server-példány védelmét, ha a kiszolgáló szintű lehetőséget választja, az összes adatbázis kerülnek. Adatbázisok hozzáadása SQL Server-példányra védelem konfigurálása után, ha manuálisan kell hozzáadni az új adatbázisok biztosítja a védelmet. Az adatbázisok automatikusan nem szerepelnek a konfigurált védelmet.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Ha módosítom a helyreállítási modell, hogyan tegye újraindítok védelmét?

Indítson egy teljes biztonsági mentést. Naplóalapú biztonsági mentések a várt módon kezdődik.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>SQL Always On rendelkezésre állási csoportok az elsődleges másodpéldány esetén a helyszínen is védeni?

Nem. Az Azure Backup védi az Azure-ban futó SQL-kiszolgálók. Ha egy rendelkezésre állási csoport (rendelkezésre állási csoport) az Azure-ban és a helyszíni gépek között megoszlik, a rendelkezésre állási csoport védelme csak akkor, ha az elsődleges replika az Azure-ban fut-e. Emellett az Azure Backup csak az ugyanabban a régióban az Azure és a Recovery Services-tárolónak futtató csomópontok védi.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>SQL Always On rendelkezésre állási csoportok az Azure-régiók között, amelyek védhetők?

Az Azure Backup helyreállítási tár a is észleli, és és a Recovery Services-tárolónak ugyanabban a régióban lévő összes csomópont védelmét. Ha több Azure-régióra kiterjedő SQL mindig a rendelkezésre állási csoport, konfigurálja a régióból, amely rendelkezik az elsődleges csomópont biztonsági mentést szeretne. Az Azure Backup tudják észleli, és a rendelkezésre állási csoport biztonsági mentési preferenciáját állapotokban található összes adatbázis védelmét. Ha a biztonsági mentési preferenciáját nem teljesül, biztonsági mentések sikertelenek lesznek, és kap a hibája című riasztás.

### <a name="while-i-want-to-protect-most-of-the-databases-in-an-instance-i-would-like-to-exclude-a-few-is-it-possible-to-still-use-the-auto-protection-feature"></a>Szeretne a legtöbb példány az adatbázisok védelmét, miközben szeretnék néhány kizárása. Az továbbra is használható az automatikus védelem funkció?

Nem, [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) a teljes példányra vonatkozik. Szelektív védelme nem adatbázis-példány automatikus védelmének használatával.

### <a name="can-i-have-different-policies-for-different-databases-in-an-auto-protected-instance"></a>Használhatok különböző házirendeket különböző adatbázisai számára automatikusan védett példány?

Ha már rendelkezik néhány védett adatbázis egyik példányában, továbbra is a megfelelő szabályzatokkal védett bekapcsolása után, akár **ON** a [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) lehetőséget. Azonban a nem védett adatbázisok együtt, amelyekre a jövőben jelentene csak egyetlen szabályzatot, amely alatt kell **biztonsági mentés konfigurálása** után az adatbázisok ki van jelölve. Valójában a ellentétben más védett adatbázisok nem is módosítható a házirend-adatbázis automatikusan védett példány alatt.
Ha azt szeretné, ehhez a kizárólag az tiltsa le az automatikus védelme a példányon zajlik, és módosíthatja a szabályzatot, hogy az adatbázis. Most már engedélyezheti újra ez a példány automatikus védelmét.

### <a name="if-i-delete-a-database-from-an-auto-protected-instance-will-the-backups-for-that-database-also-stop"></a>Ha törlök egy adatbázis automatikusan védett példány az, hogy az adatbázis biztonsági másolatainak leállítja a?

Nem, az adatbázis automatikusan védett példányából megszakadása, a biztonsági mentéseket, hogy az adatbázis vannak továbbra is kísérlet. Ez azt jelenti, hogy a törölt adatbázisok alatt állapotúként jelenik meg kezdődik-e **biztonsági másolati elemek** és továbbra is védett módon kezelik.

Ez az adatbázis védelmének megszüntetése csak úgy, hogy tiltsa le a [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) zajlik a példányon majd válassza a **biztonsági mentés leállítása** lehetőség **biztonsági másolati elemek**az adatbázishoz. Most már engedélyezheti újra ez a példány automatikus védelmét.

###  <a name="why-cant-i-see-the-newly-added-database-to-an-auto-protected-instance-under-the-protected-items"></a>Miért nem látom, hogy az újonnan hozzáadott adatbázis automatikusan védett példányhoz a védett elemek alatt?

Nem láthatók az újonnan hozzáadott adatbázis- [automatikusan védett](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) azonnal védett példány. Ennek oka az, a felderítés általában 8 óránként futtatja. Azonban a felhasználó segítségével futtathat egy kézi felderítés **adatbázisok helyreállításához** azonnal látható módon adatbázisok felderítéséhez és védelme az új lehetőség a kép alatt:

  ![Újonnan hozzáadott adatbázisait megtekintése](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>További lépések

Az Azure Backuppal kapcsolatos további tudnivalókért tekintse meg a titkosított virtuális gépek biztonsági mentése az Azure PowerShell-minta.

> [!div class="nextstepaction"]
> [Készítsen biztonsági másolatot egy titkosított virtuális gép](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
