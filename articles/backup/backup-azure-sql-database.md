---
title: Az Azure SQL Server-adatbázis biztonsági mentése |} Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, SQL Server biztonsági másolat az Azure-bA. A cikk azt is ismerteti, az SQL Server helyreállítása.
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
ms.date: 6/29/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 89a1df607c220e5dc12bc6263955d6e445e529bd
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116129"
---
# <a name="back-up-sql-server-database-in-azure"></a>Az Azure SQL Server-adatbázis biztonsági mentése

SQL Server-adatbázisok kis helyreállítási-célkitűzés (RPO) és a hosszú távú megőrzési igénylő kritikus fontosságú munkaterhelésekhez. Azure biztonsági mentés egy SQL Serverbiztonsági másolat megoldást nyújt, amely nulla infrastruktúrát igényel, ami azt jelenti, hogy nem összetett tartalék kiszolgáló, nem frissíti a kezelőügynököt vagy biztonsági másolatok tárolásának kezeléséhez. Az Azure Backup minden SQL Server-kiszolgálók, vagy akár a különböző munkaterhelések lehetővé teszi az a biztonsági másolatok központi kezelésére.

 Ebben a cikkben megtudhatja:

> [!div class="checklist"]
> * Azure biztonsági mentés SQL Server előfeltételei
> * Létrehozhat és használhat a Recovery Services-tároló
> * SQL Server-adatbázis biztonsági mentések konfigurálása
> * A helyreállítási pontok biztonsági mentése (vagy az adatmegőrzési) házirend beállítása
> * Az adatbázis visszaállítása

Ez a cikk az eljárások megkezdése előtt rendelkeznie kell egy Azure-ban futó SQL Server. [SQL piactér virtuális gépek használatával gyorsan hozzon létre egy SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Nyilvános előzetes verzió korlátozásai

A következő elemek érhetők a Public Preview ismert korlátozásai.

- Az SQL-virtuális gép az Azure nyilvános IP-címek eléréséhez internetkapcsolatra van szükség. A további részletei című szakaszban [hálózati kapcsolatok létrehozása](backup-azure-sql-database.md#establish-network-connectivity).
- A Recovery Services-tároló legfeljebb 2000 SQL-adatbázisok védelme biztosítható. További SQL-adatbázisok külön Recovery Services-tárolónak kell tárolni.
- [Elosztott rendelkezésre állási csoportok biztonsági mentési korlátai](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- SQL feladatátvevő fürt példány (FCI) nem támogatottak.
- Az Azure portál segítségével konfigurálja az Azure biztonsági mentést az SQL Server-adatbázisok védelméhez. Azure PowerShell, a parancssori felület és a REST API-k támogatása jelenleg nem áll rendelkezésre.

## <a name="supported-azure-geos"></a>Támogatott Azure GEOs

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

A következő támogatott operációs rendszerek és az SQL Server verziói vonatkoznak SQL piactéren Azure virtuális gépeken, és nem a piactérről virtuális gépek (amikor kézzel telepíti az SQL Server).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux jelenleg nem támogatott.

### <a name="supported-versionseditions-of-sql-server"></a>Az SQL Server támogatott verziói/kiadásai

- Az SQL 2012 Enterprise, Standard, webalkalmazás, fejlesztői, Express
- SQL 2014 Enterprise, Standard, webalkalmazás, fejlesztői, Express
- Az SQL 2016 Enterprise, Standard, webalkalmazás, fejlesztői, Express
- SQL 2017 Enterprise, Standard, webalkalmazás, fejlesztői, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Az SQL Server védelme az Azure biztonsági mentés használatának előfeltételei 

Mielőtt készíthet biztonsági másolatot az SQL Server adatbázis, ellenőrizze az alábbi feltételek. :

- Azonosításához vagy [Recovery Services-tároló létrehozása](backup-azure-sql-database.md#create-a-recovery-services-vault) azonos régióban, vagy területi beállítás, mint a virtuális gépet üzemeltető SQL Server.
- [Ellenőrizze az engedélyeit a virtuális gépen](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) SQL-adatbázisok biztonsági mentése szükséges.
- [SQL virtuális gép rendelkezik hálózati kapcsolattal](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> A biztonsági mentés SQL Server-adatbázisok egyszerre csak egy biztonsági mentési megoldás lehet. Más SQL biztonsági másolat a szolgáltatás használata előtt tiltsa le, más biztonsági mentések fog zavaró és sikertelen lesz. Engedélyezheti Azure biztonsági mentés az infrastruktúra-szolgáltatási virtuális együtt SQL biztonsági mentési bármely konfliktus nélkül 
>

Ha ezek a feltételek vannak jelen a környezetében, folytassa a szakasz [konfigurálása a tároló SQL-adatbázis védelme](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Ha az Előfeltételek bármelyike nem léteznek, továbbra is a fejezet elolvasása.


## <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

Minden műveletnél az SQL-virtuális gép kapcsolódnia kell az Azure nyilvános IP-címeket. SQL virtuális gép műveletek (például adatbázis felderítési, konfigurálását biztonsági mentés, ütemezett biztonsági mentések, visszaállítás helyreállítási pontok, és így tovább) és a nyilvános IP-címek nélkül sikertelen lesz. A következő lehetőségek egyikével meg egyértelmű elérési útvonalát a biztonsági mentési forgalmat:

- Engedélyezett az Azure datacenter IP-címtartományok,-az engedélyezési lista az Azure datacenter IP-címtartományok, használja a [töltse le a központ lapon talál részletes információt az IP-címtartományok és utasítások](https://www.microsoft.com/download/details.aspx?id=41653). 
- A forgalom útválasztási HTTP-proxy kiszolgáló telepítése – amikor biztonsági mentést készít a virtuális gépen SQL-adatbázis, a biztonsági mentési bővítményt a virtuális Gépre segítségével HTTPS API-k Azure biztonsági mentési és az adatok Azure Storage management parancsainak elküldését. A tartalék mellék, Azure Active Directory (AAD) is használ. A tartalék mellék forgalom ezen három szolgáltatások a HTTP-proxyn keresztül történő irányításához, mivel a nyilvános internet-hozzáférés beállítása csak összetevő már.

A választási lehetőségek között a mellékhatásokkal vannak: kezelhetőség, a tanúsítványhasználat pontos szabályzása és költségek.

> [!NOTE]
>Az Azure Backup szolgáltatás címkék általánosan rendelkezésre álló által elérhetőnek kell lennie.
>

| Beállítás | Előnyei | Hátrányok |
| ------ | ---------- | ------------- |
| Engedélyezett IP-címtartományok | Nincsenek további költségek. <br/> Hozzáférés nyithatók meg a egy NSG-t, használjon **Set-AzureNetworkSecurityRule** parancsmag. | Az érintett rendszer kezelése bonyolult IP-címtartományok változnak az idők. <br/>Azure storage nem csak a teljes hozzáférést biztosít.|
| HTTP proxyk használatára   | A proxy a keresztül a tárolási a tanúsítványhasználat pontos szabályzása URL-címek használata engedélyezett. <br/>Virtuális gépek egyetlen pont, internet-hozzáférést. <br/> Nem vonatkozik a Azure IP-címet érintő módosításait. | További költségek a virtuális gépek futtatásához a proxy szoftverrel. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Nem a piactérről SQL virtuális gépek engedélyeinek beállítása

Azure biztonsági mentés szükséges biztonsági mentése a virtuális gépek, a **AzureBackupWindowsWorkload** bővítményt telepíteni. Ha az Azure piactér virtuális gépeket használ, ugorjon előre [felderíteni az SQL server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases). Ha a virtuális gép az SQL-adatbázisokat üzemeltető nem jött létre az Azure piactérről, végezze el az alábbi szakasz a bővítmény telepítését, és állítsa be a megfelelő engedélyekkel. Kívül a **AzureBackupWindowsWorkload** -bővítményhez, Azure biztonsági mentés olyan SQL sysadmin jogosultsággal az SQL-adatbázisok védelméhez. Azure biztonsági mentés közben a virtuális gépen adatbázisok felderítése, hoz létre a fiók, NT Service\AzureWLBackupPluginSvc. Azure biztonsági mentés SQL-adatbázisok felderítése a NT Service\AzureWLBackupPluginSvc fióknak SQL bejelentkezési és az SQL-rendszergazdai engedélyekkel kell rendelkeznie. Az alábbi eljárás ismerteti, hogyan adja meg azokat az engedélyeket.

Engedélyek konfigurálására:

1. Az a [Azure-portálon](https://portal.azure.com), nyissa meg a Recovery Services-tároló SQL-adatbázisok védelméhez használata.
2. A tároló irányítópult menüben kattintson a **+ biztonsági mentés** megnyitásához a **biztonsági mentési cél** menü.

   ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/open-backup-menu.png)

3. Az a **biztonsági mentési cél** menüben, a a **a számítási feladatok futtató?** menü, hagyja **Azure** alapértelmezés szerint.

4. Az a **miről szeretne biztonsági másolatot készíteni** menüben bontsa ki a legördülő menüből, és válassza **SQL Server Azure virtuális gép**.

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    A **biztonsági mentési cél** menü két új lépéseket tartalmazza: **adatbázisok felderítése a virtuális gépek** és **Backup konfigurálása**. **A virtuális gépek adatbázisok felderítése** Azure virtuális gépek kereshetők.

    ![Az új biztonsági mentési cél lépéseit mutatja be](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Kattintson a **Start felderítési** nem védett virtuális gépek az előfizetéshez kereséséhez. Az előfizetés nem védett virtuális gépek számától függően haladhat végig az összes virtuális gép egy ideig is igénybe vehet.

    ![Biztonsági mentés függőben](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Miután egy nem védett virtuális gép fel van derítve, megjelenik a listában. Nem védett virtuális gépek a virtuális gép nevét és az erőforrás-csoport alapján vannak listázva. A több virtuális gép neve. Virtuális gépek ugyanazzal a névvel, azonban eltérő erőforráscsoportokban tartozik. Ha a virtuális gépek várt nem jelenik meg a listában, lásd: Ha a virtuális gép már védett egy tárolóba.

6. A virtuális gépek listájából válassza ki a virtuális Gépet, amely tartalmazza az SQL-adatbázis biztonsági mentéséhez, és kattintson a kívánt **adatbázisok felderítése**. 

    A felderítési folyamat telepíti a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. A bővítmény lehetővé teszi, hogy az SQL-adatbázisok készíthető, a virtuális gép kommunikálni az Azure Backup szolgáltatás. Egyszer a bővítmény telepítését az Azure Backup hoz létre a Windows virtuális szolgáltatásfiók **NT Service\AzureWLBackupPluginSvc**, a virtuális gépen. A virtuális fiók SQL rendszergazdai engedélyre van szüksége. A virtuális szolgáltatás fiók telepítés során, ha a hibát látja **UserErrorSQLNoSysadminMembership**, tekintse meg a [kijavítása SQL sysadmin jogosultságok](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Értesítési terület adatbázis felderítés végrehajtási állapotát tartalmazza. Attól függően, hogy hány adatbázisok vannak a virtuális gépen eltarthat egy ideig, míg a futó feladat befejeződésére. A kiválasztott adatbázisoknál felderített, a sikeres üzenet jelenik meg.

    ![a sikeres telepítést értesítési üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

Ha az adatbázis társítja a Recovery Services-tároló, a következő lépés, hogy [a biztonsági mentés konfigurálása](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>SQL-rendszergazdai engedélyek kijavítása

A telepítés során, ha a hibát látja **UserErrorSQLNoSysadminMembership**, bejelentkezési az SQL Server Management Studio (SSMS) egy olyan fiókkal, amely SQL rendszergazdai engedéllyel rendelkezik. Ha szüksége van speciális engedélyek, ismeri fel a fiókot a Windows-hitelesítés használatával kell lennie.

1. Az SQL-kiszolgálón nyissa meg a **biztonsági/bejelentkezések** mappát.

    ![Nyissa meg az SQL Server és a biztonság és a bejelentkezési mappák fiókok megtekintéséhez](./media/backup-azure-sql-database/security-login-list.png)

2. A bejelentkezési adatok mappára, kattintson a jobb gombbal, és válassza ki **új bejelentkezés**, és a bejelentkezés - az új párbeszédpanel, kattintson a **keresése**

    ![A bejelentkezés – új keresés párbeszédpanel megnyitásához.](./media/backup-azure-sql-database/new-login-search.png)

3. Windows virtuális szolgáltatásfiók óta **NT Service\AzureWLBackupPluginSvc** már megtörtént a virtuális gép regisztrációs és SQL felderítési fázis során létrehozott, adja meg a fiók nevét, ahogyan megjelenik az  **Adja meg a kiválasztandó objektum nevét** párbeszédpanel. Kattintson a **Névellenőrzés** feloldani a nevet. 

    ![A szolgáltatás ismeretlen név Névellenőrzés gombra](./media/backup-azure-sql-database/check-name.png)

4. Kattintson a **OK** a felhasználó vagy csoport kiválasztása párbeszédpanel bezárásához.

5. Az a **kiszolgálói szerepkörök** párbeszédpanelen győződjön meg arról, hogy a **sysadmin** szerepkör van kiválasztva. Kattintson a **OK** bezárásához **- új bejelentkezés**.

    ![Győződjön meg arról, hogy a sysadmin (rendszergazda) kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Most már léteznie kell a szükséges engedélyekkel.

6. Bár a rögzített az engedélyekkel kapcsolatos hibát, továbbra is szeretné az adatbázis társítani a Recovery Services-tároló. Az Azure portálon **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló hibát, és válassza ki **elküldtük adatbázisok**.

    ![Ellenőrizze a kiszolgáló rendelkezik-e a megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

    Értesítési terület adatbázis felderítés végrehajtási állapotát tartalmazza. Attól függően, hogy hány adatbázisok vannak a virtuális gépen eltarthat egy ideig, míg a futó feladat befejeződésére. Ha a kijelölt adatbázisok találhatók, a sikerről szóló üzenetet az értesítési területen jelenik meg.

    ![a sikeres telepítést értesítési üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

Ha az adatbázis társítja a Recovery Services-tároló, a következő lépés, hogy [a biztonsági mentés konfigurálása](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-adatbázisok felderítése

Azure biztonsági mentés felderíthetők az összes adatbázis SQL Server-példányon, így megvédheti azokat a biztonsági mentés követelményeinek /. A következő eljárással az SQL-adatbázisokat üzemeltető virtuális gépének azonosítását. Ha azonosította a virtuális gép, Azure biztonsági mentés telepíti egy egyszerűsített bővítménye felderíteni az SQL server-adatbázisok.

1. Jelentkezzen be az előfizetéshez a [Azure-portálon](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![A főmenü minden szolgáltatás választania](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Az összes szolgáltatások párbeszédpanelen írja be a *Recovery Services*. Írja be megkezdése előtt, a megadott erőforrások listájának szűrése. Miután látja, válassza ki a **Recovery Services-tárolók**.

    ![Az összes szolgáltatások párbeszédpanelen írja be a helyreállítási szolgáltatások](./media/backup-azure-sql-database/all-services.png) <br/>

    Az előfizetés Recovery Services-tárolók listája jelenik meg. 

4. A Recovery Services-tároló listában jelölje ki a tárolóval, amelyet az SQL-adatbázisok védelme érdekében.

5. A tároló irányítópult menüben kattintson a **+ biztonsági mentés** megnyitásához a **biztonsági mentési cél** menü.

   ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/open-backup-menu.png)

6. Az a **biztonsági mentési cél** menüben, a a **a számítási feladatok futtató?** menü, hagyja **Azure** alapértelmezés szerint.

7. Az a **miről szeretne biztonsági másolatot készíteni** menüben bontsa ki a legördülő menüből, és válassza **SQL Server Azure virtuális gép**.

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Egyszer jelölve, a **biztonsági mentési cél** menü jeleníti meg két lépésből áll: adatbázisok felderítése a virtuális gépek és a biztonsági mentés konfigurálása. 

    ![Az új biztonsági mentési cél lépéseit mutatja be](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Kattintson a **Start felderítési** nem védett virtuális gépek az előfizetéshez kereséséhez. Az előfizetés nem védett virtuális gépek számától függően haladhat végig az összes virtuális gép egy ideig is igénybe vehet.

    ![Biztonsági mentés függőben](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Miután egy nem védett virtuális gép fel van derítve, megjelenik a listában. Több virtuális gép lehet-e azonos nevük. Ezzel a névvel több virtuális gép azonban a különböző erőforráscsoportokra tartozik. A nem védett virtuális gépek a virtuális gép nevét és az erőforrás-csoport alapján vannak listázva. Ha nem szerepel a virtuális gép várható, tekintse meg, ha, hogy a virtuális gép már védett egy tárolóba.

9. A virtuális gépek listájából válassza ki a jelölőnégyzetet a virtuális gép, amely tartalmazza az SQL-adatbázisok védelmét, és kattintson a kívánt **adatbázisok felderítése**.

    Azure biztonsági mentés a virtuális gépen található SQL-adatbázisok deríti fel. Mi történik, az adatbázis felderítési fázis során kapcsolatos információkért tekintse meg a következő szakaszban találhat [háttérbeli műveletek során az SQL-adatbázisok felderítése](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Miután értesült az SQL-adatbázisok, készen áll [konfigurálja a biztonsági mentési feladat](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Ha az SQL-adatbázisok felderítése háttérbeli műveletek

Ha a **adatbázisok felderítése** eszköz, az Azure Backup szolgáltatás a háttérben hajt végre a következő műveleteket:

- a virtuális gépet regisztrálja a Recovery Services-tároló, a munkaterhelések biztonsági mentéséhez. A Recovery Services-tárolóba csak készíthető a regisztrált virtuális gépen lévő összes adatbázis. 

- telepíti a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Az ügynök nélküli megoldás egy SQL-adatbázis biztonsági mentése, ez azt jelenti, hogy a virtuális gépen telepített kiterjesztésű nincs ügynök telepítve van az SQL-adatbázis.

- a szolgáltatási fiókot hoz létre **NT Service\AzureWLBackupPluginSvc**, a virtuális gépen. Az összes biztonsági mentési és visszaállítási műveletek a szolgáltatás fiók használata. **NT Service\AzureWLBackupPluginSvc** SQL rendszergazdai engedélyekkel kell. Az összes SQL piactér virtuális gépek telepítve SqlIaaSExtension rendelkeznek, és AzureBackupWindowsWorkload SQLIaaSExtension segítségével automatikusan lekérni a szükséges engedélyekkel. Ha a virtuális gép nem rendelkezik telepített SqlIaaSExtension, a DB felderítése művelet sikertelen lesz, és a hibaüzenet jelenik meg, **UserErrorSQLNoSysAdminMembership**. A sysadmin (rendszergazda) engedéllyel a biztonsági mentéshez hozzáadásához kövesse az utasításokat a [nem Piactéri SQL virtuális gépek Azure biztonsági mentési Engedélybeállításának](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>SQL Server-adatbázis biztonsági mentés konfigurálása

Azure biztonsági mentés az SQL Server-adatbázisok védelme és kezelése a biztonsági mentési feladatok-felügyeleti szolgáltatásokat biztosít. A felügyeleti és megfigyelési lehetőségek függ a Recovery Services-tároló. 

> [!NOTE]
> A biztonsági mentés SQL Server-adatbázisok egyszerre csak egy biztonsági mentési megoldás lehet. Más SQL biztonsági másolat a szolgáltatás használata előtt tiltsa le, más biztonsági mentések fog zavaró és sikertelen lesz. Engedélyezheti Azure biztonsági mentés az infrastruktúra-szolgáltatási virtuális együtt SQL biztonsági mentési bármely konfliktus nélkül 
>

Az SQL-adatbázis védelmének konfigurálásához:

1. Nyissa meg a Recovery Services-tároló regisztrált az SQL-virtuális gép.

2. A tároló irányítópult menüben kattintson a **+ biztonsági mentés** megnyitásához a **biztonsági mentési cél** menü.

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/open-backup-menu.png)

3. Az a **biztonsági mentési cél** menüben, a a **a számítási feladatok futtató?** menü, hagyja **Azure** alapértelmezés szerint.

4. Az a **miről szeretne biztonsági másolatot készíteni** menüben bontsa ki a legördülő menüből, és válassza **SQL Server Azure virtuális gép**.

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Egyszer jelölve, a **biztonsági mentési cél** menü jeleníti meg két lépésből áll: adatbázisok felderítése a virtuális gépek és a biztonsági mentés konfigurálása. Ha, de ez a cikk sorrendben keresztül, hogy már megismerte a nem védett virtuális gépek, és ennek a tárolónak regisztrálva van egy virtuális gépet. Most már készen áll az SQL-adatbázisok védelmének konfigurálása.

5. A biztonsági mentési cél menüben kattintson a **Backup konfigurálása**.

    ![Az új biztonsági mentési cél lépéseit mutatja be](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Az Azure Backup szolgáltatás önálló adatbázisok, valamint az SQL AlwaysOn rendelkezésre állási csoportokat az SQL Server-példányok jeleníti meg. SQL-példány az önálló adatbázisok megtekintéséhez kattintson az adatbázisok megtekintéséhez a példány neve melletti sávnyíl. A következő ábrákon láthatók példák egy önálló példányát és egy Always On rendelkezésre állási csoporthoz.

    > [!NOTE]
    > Teljes és különbözeti biztonsági mentések fordulhat elő, az elsődleges csomópont, az SQL-platform, hogy a korlátozás van érvényben. Napló biztonsági mentését a biztonsági mentési beállítás akkor fordulhat elő a rendszer. Ez a korlátozás miatt az elsődleges csomópont regisztrálva kell lennie.
    >

    ![Az SQL-példány az adatbázisok listája](./media/backup-azure-sql-database/discovered-databases.png)

    Kattintson a sávnyílra melletti AlwaysOn rendelkezésre állási csoportok az adatbázisok listájának megtekintéséhez.

    ![Az AlwaysOn rendelkezésre állási csoport adatbázisok listája](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Az adatbázisok listájának megtekintéséhez válassza ki az összes védeni, és kattintson a kívánt **OK**.

    ![Válassza ki a védelmükhöz, több adatbázis](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Egyszerre legfeljebb 50 adatbázisok választhat. Ha azt szeretné, több mint 50 adatbázisok védelméhez, hogy több fázisban. Az első 50-adatbázisok védelme után ismételje meg ezt a következő set-adatbázisok védelmét.
    > [!Note] 
    > A biztonsági mentési terhelések optimalizálása érdekében Azure biztonsági mentési bontja nagy biztonsági mentési feladatok több kötegek. Egy biztonsági mentési feladat adatbázisok maximális száma érték az 50.
    >
    >

7. Hozzon létre vagy válassza ki a biztonsági mentési házirend, az a **biztonsági mentési** menüjében válassza **biztonsági mentési házirend**, a menü megnyitásához.

    ![Válassza ki a biztonsági mentési házirend-beállítás](./media/backup-azure-sql-database/select-backup-policy.png)

8. Az a **válassza ki a biztonsági mentési házirend** legördülő menüben válassza ki a biztonsági mentési házirend, és kattintson **OK**. Létrehozásával kapcsolatos információkat a saját biztonsági mentési házirend, című szakaszban [a biztonsági mentési házirend meghatározása](backup-azure-sql-database.md#define-a-backup-policy).

    ![a legördülő menüből válassza ki a biztonsági mentési házirend](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    A biztonsági mentési házirend menüben az a **válassza ki a biztonsági mentési házirend** legördülő menüben kiválaszthatja: 
    - az alapértelmezett HourlyLogBackup házirend 
    - az SQL, korábban létrehozott meglévő biztonsági mentési házirend
    - a [adja meg egy új házirendet](backup-azure-sql-database.md#define-a-backup-policy) a helyreállítási időkorlát (RPO) és a megőrzési tartomány alapján. 

    > [!Note]
    > Azure biztonsági mentés közben megfelelőségi igényeket háttér-tároló fogyasztása optimalizálása érdekében a szerzett-édesapja-fia biztonsági mentés módját alapján hosszú távú megőrzési támogatja.
    >

9. Miután kiválasztotta a biztonsági mentési házirend, az a **biztonsági mentés menü** kattintson **biztonságimásolat-készítés engedélyezése**.

    ![a választott biztonsági mentési házirend engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

    Az értesítési területen a portál konfigurációs előrehaladásának nyomon követheti.

    ![értesítési terület](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Biztonsági mentési szabályzat meghatározása

A biztonsági mentési házirend egy mátrixot határoz meg, amikor a biztonsági mentést készít, és mennyi ideig megőrzi a biztonsági mentéseket. Azure Backup segítségével háromféle az SQL-adatbázisok biztonsági mentés ütemezése:

* Teljes biztonsági mentés - teljes adatbázis biztonsági másolatának biztonsági másolatot készít a teljes adatbázisról. Teljes biztonsági mentést tartalmaz egy adott adatbázis vagy fájlcsoport vagy -fájlok készlete az adatokat, és elég naplót, hogy az adatok helyreállításához. A tartalom legfeljebb egy teljes biztonsági mentés naponta indíthat el. Ha szeretné, napi vagy heti időközönkénti teljes biztonsági másolatok készítéséhez. 
* Különbözeti biztonsági másolat - különbözeti biztonsági mentése a legújabb, előző teljes biztonsági mentését alapul. Különbözeti biztonsági mentése csak a teljes biztonsági mentés óta módosult adatokat rögzíti. A tartalom legfeljebb egy különbözeti biztonsági másolat / nap indíthat el. Nem lehet konfigurálni a teljes biztonsági mentés és a különbözeti biztonsági mentése ugyanarra a napra esnek.
* Tranzakciós napló biztonsági mentését – a napló biztonsági mentését lehetővé teszi, hogy legfeljebb egy adott időpontban visszaállítás második. A tartalom legfeljebb konfigurálható tranzakciós naplók biztonsági másolatainak 15 percenként.

Házirend jön létre a Recovery Services-tároló szintjén. Ha több tárolóból, a tárolók használhatja ugyanazt a biztonsági mentési házirend, de a biztonsági mentési házirend minden egyes tárolóban kell alkalmaznia. A biztonsági mentési házirend, a napi létrehozásakor teljes biztonsági mentését az alapértelmezett beállítás. Különbözeti biztonsági mentése, de csak akkor adhat meg, ha heti teljes biztonsági mentések vált. Az alábbi eljárás ismerteti, hogyan hozzon létre egy SQL Server biztonsági mentési házirend egy Azure virtuális gépen.

A biztonsági mentési házirend létrehozása

1. A biztonsági mentési házirend menüben a a **válassza ki a biztonsági mentési házirend** legördülő menüben válassza **hozzon létre új**.

   ![Hozzon létre új biztonsági mentési házirend](./media/backup-azure-sql-database/create-new-backup-policy.png)

    A biztonsági mentési házirend menü vált egy-egy új SQL server biztonsági mentési házirend szükséges mezőket.

   ![új biztonsági mentési házirend mezők](./media/backup-azure-sql-database/blank-new-policy.png)

2. A **házirendnév**, adjon meg egy nevet. 

3. Teljes biztonsági mentés megadása kötelező. Fogadja el az alapértelmezett értékeket a teljes biztonsági mentéshez, vagy kattintson a **teljes biztonsági mentés** a házirend szerkesztése.

    ![új biztonsági mentési házirend mezők](./media/backup-azure-sql-database/full-backup-policy.png)

    A teljes biztonsági mentési házirend válassza a naponta vagy hetente gyakoriságát. Ha úgy dönt, hogy a napi, válassza ki az óra és időzóna, a biztonsági mentési feladat megkezdésekor. Ha úgy dönt, hogy a napi teljes biztonsági mentés, különbözeti biztonsági mentések nem hozható létre.

   ![napi időközének beállítása](./media/backup-azure-sql-database/daily-interval.png)

    Ha úgy dönt, hetente, nap, válassza ki a nap, hét, óra és időzóna, a biztonsági mentési feladat megkezdésekor.

   ![heti időközének beállítása](./media/backup-azure-sql-database/weekly-interval.png)

4. Alapértelmezés szerint minden megőrzési időtartam beállítások (napi, heti, havi és éves) van kiválasztva. Ön nem kíván, és az intervallumok használandó minden megőrzési időtartam azon korlátozásainak törölje. A teljes biztonsági mentés házirend menüben kattintson a **OK** elfogadja a beállításokat.

   ![megőrzési tartomány időközének beállítása](./media/backup-azure-sql-database/retention-range-interval.png)

    Helyreállítási pontok megőrzési, a megőrzési tartomány alapján az címkével rendelkeznek. Például ha napi, teljes biztonsági mentés csak egy teljes biztonsági mentés akkor váltódik ki, minden nap. Attól függően, hogy a heti megőrzési, az adott napon létrehozott biztonsági mentést címkézett és megtartása a heti megőrzési tartomány alapján. A havi és éves megőrzési időtartamot hasonlóan viselkedik.

5. A különbözeti biztonsági mentési házirend hozzáadásához kattintson **különbözeti biztonsági másolat** a menü megnyitásához. 

   ![Nyissa meg a különbözeti házirend](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    A különbözeti biztonsági mentési házirend menüben válasszon ki **engedélyezése** a gyakoriság és megőrzési felügyelet megnyitásához. Indíthat el, a tartalom legfeljebb egy különbözeti biztonsági másolat / nap.
    > [!Important] 
    > A tartalom legfeljebb különbözeti biztonsági mentések megőrzésére alkalmas 180 napig tart. Ha hosszabb megőrzési van szüksége, a teljes biztonsági mentést kell használnia, különbözeti biztonsági mentések nem használható.
    >

   ![különbözeti házirend szerkesztése](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Kattintson a **OK** menteni a házirendet, és térjen vissza a főmenübe biztonsági mentési házirend.

6. A tranzakciós napló biztonsági mentési házirend hozzáadásához kattintson **napló biztonsági mentési** a menü megnyitásához. A napló biztonsági mentési menüben válasszon ki **engedélyezése**, és állítsa be a gyakoriság és megőrzési szabályozza. Napló-biztonságimásolatokat akkor fordulhat elő, akár 15 perces gyakorisággal, és legfeljebb 35 napon megőrzésére alkalmas. Kattintson a **OK** menteni a házirendet, és térjen vissza a főmenübe biztonsági mentési házirend.

   ![napló biztonsági mentési házirend szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Válasszon, hogy az SQL biztonsági másolat tömörítésének engedélyezéséhez. Tömörítés alapértelmezés szerint le van tiltva.

    A háttérkiszolgálón Azure biztonsági mentési használja az SQL natív biztonsági másolatok tömörítését.

8. Ha a biztonsági mentési házirend minden módosításokat végzett, kattintson **OK**. 

   ![különbözeti megőrzési időtartamot](./media/backup-azure-sql-database/differential-backup-policy.png)

## <a name="restore-a-sql-database"></a>SQL-adatbázis visszaállítása

Az Azure Backup a tranzakciónapló biztonsági mentései használatával történő visszaállításához, az egyes adatbázisok egy adott napon vagy időpontban, akár egy adott, funkciókat biztosít. Teljes, különbségi és a Naplók biztonsági másolatainak az adatok visszaállításához szükséges lánc alapján ad meg, Azure Backup szolgáltatás automatikusan meghatározza, hogy a megfelelő helyreállítási időt.

Másik lehetőségként választhatja meg egy teljes vagy különbségi biztonsági másolat visszaállítása egy adott időpont helyett egy adott helyreállítási pontot.

Egy adatbázis visszaállítása

1. Nyissa meg a Recovery Services-tároló regisztrált az SQL-virtuális gép.

2. Válassza ki a tároló irányítópult **használati** biztonsági mentés elemeket a biztonsági mentés elemek menü megnyitásához.

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Az a **biztonsági másolati elemei** menüben válassza ki a biztonsági mentési felügyeleti típusát **SQL Azure virtuális gép**. 

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A biztonsági mentés elemek listája itt állíthatja be az SQL-adatbázisok listájának megjelenítéséhez. 

4. SQL-adatbázisok listájának megtekintéséhez jelölje ki a visszaállítani kívánt adatbázist.

    ![Válassza ki az SQL Azure virtuális gépen a listából](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Ha az adatbázis lehetőséget választja, a menü megnyitása. Ebben a menüben a biztonsági mentési részleteit jeleníti meg az adatbázis többek között:

    * a visszaállítási pontok, a legrégebbi és a legújabb
    * napló biztonsági mentési állapota az elmúlt 24 órában (a teljes telepítési és a tömegesen naplózott helyreállítási modell, ha a tranzakciós napló biztonsági mentéshez konfigurált adatbázisok)

5. A kijelölt adatbázis menüben kattintson a **Restore DB** a visszaállítási menü megnyitásához.

    ![Válassza ki az adatbázis visszaállítása](./media/backup-azure-sql-database/restore-db-button.png)

    A **visszaállítása** menü megnyitása, és így biztosítja a **visszaállítása konfigurációs** menü. A **visszaállítása konfigurációs** menü első lépése a konfigurálásához a visszaállítást. Az ebben a menüben választja, ahová az adatok helyreállítását. A következő lehetőségek közül választhat:
    * Másik helyre, – használja ezt a beállítást, ha szeretné visszaállítani az adatbázis másik helyre, miközben megőrzik az eredeti adatbázis.
    * Felülírja az adatbázis - helyreállítást az adatokat, mint az eredeti forrás ugyanazon SQL Server-példányra. Ennek következménye felül az eredeti adatbázist.

    > [!Important]
    > Ha a kijelölt adatbázis Always On rendelkezésre állási csoporthoz tartozik, az SQL nem teszi lehetővé felülírja az adatbázis. Ebben az esetben, csak a **másik helyre** engedélyezve van.
    >

    ![Kattintson a Konfigurálás gombra visszaállítási konfigurációs menü megnyitása](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

Ez az eljárás végigvezeti az adatok helyreállítása másik helyre. Ha azt szeretné, felülírja az adatbázis visszaállítása során, a szakasz Ugrás [visszaállítása és az adatbázis felülírása](backup-azure-sql-database.md#restore-and-overwrite-the-database). Ez az eljárás azt feltételezi, hogy a Recovery Services-tároló megnyitott, és a visszaállítás konfigurációs menü erővel. Ha nem, indítsa el a szakasz [SQL-adatbázis visszaállítása](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Visszaállíthatja az adatbázist egy SQL Server ugyanazon Azure-régióban, és a célkiszolgálón kell lennie a Recovery Services-tároló a rendszerünkben. 
>

A **Server** legördülő menü csak az SQL Server a Recovery Services-tároló regisztrált jeleníti meg. Ha nincs a kiszolgálón, a **Server** listában, tekintse meg a [felderíteni az SQL server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases) található a kiszolgálón. A felderítési adatbázist során bármely új kiszolgáló regisztrálva van a Recovery Services-tároló.

1. Az a **visszaállítása konfigurációs** menüben:

    * Válassza ki **másik helyre**,
    * a **Server**, válassza ki az SQL server, ahol szeretné visszaállítani az adatbázist.
    * az a **példány** legördülő menüben válassza ki az SQL-példány
    * az a **visszaállított adatbázis neve** párbeszédpanelen adja meg a célként megadott adatbázis nevét.
    * Ha van ilyen, válassza ki a **felülírása, ha a kiválasztott SQL-példányon már létezik ilyen nevű adatbázis**.
    * Kattintson a **OK** befejeződik, a célkiszolgáló beállítása, majd helyezze át a helyreállítási pont kiválasztása.

    ![Visszaállítás konfigurációs menüben válassza másik helyre, a példány és a neve](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Az a **visszaállítási pont kiválasztása** menü, dönthet úgy, vagy a naplók (Point in Time) vagy a teljes & különbségi visszaállítási pont. Ha egy adott időpontban napló a visszaállítani kívánt, folytassa a ezt a lépést. Ha szeretné visszaállítani egy teljes vagy különbségi-visszaállítási pontot, ugorjon előre 3.

    ![Válasszon visszaállítási pontot menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    Az időponthoz kötött visszaállításra csak érhető el az adatbázisok teljes naplómentésekre & Tömegesen naplózott helyreállítási modell. Egy adott-időpontban visszaállítása:

    1. Válassza ki **naplók (Point in Time)** a visszaállítási lehetőséggel.

        ![Válasszon visszaállítási pontot típust](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. A **dátum és idő visszaállításához**, kattintson a naptár ikonra, és nyissa meg a naptárban. A félkövérrel szedett dátumok helyreállítási pontokat tartalmaznak, és az aktuális dátumot ki van jelölve. Válassza ki a dátum és a helyreállítási pontok a naptárban. Nem választhat ki a helyreállítási pontok dátumát.

        ![Nyissa meg a naptárban](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Miután egy dátumot, az ütemterv graph a rendelkezésre álló helyreállítási pontok folyamatos széles jeleníti meg.

    3. Az ütemterv graph, vagy a idő párbeszédpanelen adja meg a helyreállítási pontot, majd kattintson az adott időszak **OK** a visszaállítási pontot a lépés befejezéséhez.
    
       ![Nyissa meg a naptárban](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        A **visszaállítási pont kiválasztása** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

       ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Az a **speciális konfiguráció** menüben:

        * Tartsa az adatbázis nem működő, visszaállítása után a a **Restore with NORECOVERY** menü **engedélyezve**.
        * Ha azt szeretné, hogy a célkiszolgálón a visszaállítás helyének módosításához, adjon meg egy új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    5. Az a **visszaállítása** menüben kattintson a **visszaállítása** elindítja a helyreállítási feladatot. Az értesítési területen előrehaladásának. Is nyomon követheti a folyamatot a az adatbázis-visszaállítási feladat.

       ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-job-notification.png)

3. Az a **visszaállítási pont kiválasztása** menüben válassza ki a helyreállítási pont létrehozása. Dönthet úgy, vagy a naplók (Point in Time) vagy a teljes, és az eltérés. Ha szeretné visszaállítani egy időpontban naplót, lépjen vissza, a 2. lépés. Ebben a lépésben egy adott teljes vagy különbözeti visszaállítási pontot állítja vissza. Ezzel a beállítással teljes és a különbségi helyreállítási pontok láthatja az elmúlt 30 napban. Ha meg szeretné tekinteni a 30 napnál régebbi helyreállítási pontok, kattintson a **szűrő** megnyitásához a **szűrő visszaállítási pontok** menü. Ha úgy dönt, hogy a különbözeti helyreállítási pont, az Azure Backup először visszaállítja a megfelelő teljes helyreállítási pontot, és majd alkalmazza a kiválasztott különbözeti helyreállítási pont.

    ![Válasszon visszaállítási pontot menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Az a **visszaállítási pont kiválasztása** menüben válasszon **teljes & különbségi**.

       ![Válasszon visszaállítási pontot menü](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Megjelenik a rendelkezésre álló helyreállítási pontok listáján.

    2. A helyreállítási pontok listáján, válasszon ki egy helyreállítási pontot, és kattintson a **OK** a visszaállítási pontot a művelet elvégzéséhez. 

        ![a teljes helyreállítási pont kiválasztása](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        A **visszaállítási pont** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

        ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Az a **speciális konfiguráció** menüben:

        * Tartsa az adatbázis nem működő, visszaállítása után a a **Restore with NORECOVERY** menü **engedélyezve**. **Állítsa vissza a NORECOVERY** alapértelmezés szerint le van tiltva.
        * Ha azt szeretné, hogy a célkiszolgálón a visszaállítás helyének módosításához, adjon meg egy új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    4. Az a **visszaállítása** menüben kattintson a **visszaállítása** elindítja a helyreállítási feladatot. Az értesítési területen előrehaladásának. Is nyomon követheti a folyamatot a az adatbázis-visszaállítási feladat.

       ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Állítsa vissza, és az adatbázis felülírása

Ez az eljárás végigvezeti az adatok helyreállításához és az adatbázis felülírása. Ha szeretne egy másik helyre végezze a helyreállítást, a szakasz ugrani [másik helyre végezze a helyreállítást](backup-azure-sql-database.md#restore-to-an-alternate-location). Ez az eljárás azt feltételezi, hogy a Recovery Services-tároló megnyitott, és amelyeken a **visszaállítása konfigurációs** menü (lásd az alábbi kép). Ha nem, indítsa el a szakasz [SQL-adatbázis visszaállítása](backup-azure-sql-database.md#restore-a-sql-database).

![Kattintson a Konfigurálás gombra visszaállítási konfigurációs menü megnyitása](./media/backup-azure-sql-database/restore-overwrite-db.png)

A **Server** legördülő menü csak az SQL Server a Recovery Services-tároló regisztrált jeleníti meg. Ha nincs a kiszolgálón, a **Server** listában, tekintse meg a [felderíteni az SQL server-adatbázisok](backup-azure-sql-database.md#discover-sql-server-databases) található a kiszolgálón. A felderítési adatbázist során bármely új kiszolgáló regisztrálva van a Recovery Services-tároló.

1. A a **visszaállítása konfigurációs** menüjében válassza **felülírása DB** kattintson **OK** konfigurálása a cél befejezéséhez. 

   ![Kattintson az adatbázis felülírása](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    A **Server**, **példány**, és **visszaállított adatbázis neve** párbeszédpanelek nem szükségesek.

2. Az a **visszaállítási pont kiválasztása** menü, dönthet úgy, vagy a naplók (Point in Time) vagy a teljes & különbségi visszaállítási pont. Ha szeretné visszaállítani egy időpontban naplót, folytassa a ezt a lépést. Ha szeretné visszaállítani egy teljes & különbségi-visszaállítási pontot, ugorjon előre 3.

    ![Válasszon visszaállítási pontot menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    Az időponthoz kötött visszaállításra csak érhető el az adatbázisok teljes naplómentésekre & Tömegesen naplózott helyreállítási modell. Második idő visszaállítás egy adott kiválasztása egy pontot:

    1. Válassza ki **naplók (Point in Time)** a visszaállítási lehetőséggel.

        ![Válasszon visszaállítási pontot típust](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. A **dátum és idő visszaállításához**, kattintson a naptár ikonra, és nyissa meg a naptárban. A félkövérrel szedett dátumok helyreállítási pontokat tartalmaznak, és az aktuális dátumot ki van jelölve. Válassza ki a dátum és a helyreállítási pontok a naptárban. Nem választhat ki a helyreállítási pontok dátumát.

        ![Nyissa meg a naptárban](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Miután egy dátumot, az ütemterv graph a rendelkezésre álló helyreállítási pontok jeleníti meg.

    3. Az ütemterv graph, vagy a idő párbeszédpanelen adja meg a helyreállítási pontot, majd kattintson az adott időszak **OK** a visszaállítási pontot a lépés befejezéséhez.
    
       ![Nyissa meg a naptárban](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        A **visszaállítási pont kiválasztása** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

       ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Az a **speciális konfiguráció** menüben:

        * Tartsa az adatbázis nem működő, visszaállítása után a a **Restore with NORECOVERY** menü **engedélyezve**.
        * Ha azt szeretné, hogy a célkiszolgálón a visszaállítás helyének módosításához, adjon meg egy új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    5. Az a **visszaállítása** menüben kattintson a **visszaállítása** elindítja a helyreállítási feladatot. Az értesítési területen előrehaladásának. Is nyomon követheti a folyamatot a az adatbázis-visszaállítási feladat.

       ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-job-notification.png)

3. Az a **visszaállítási pont kiválasztása** menüben válassza ki a helyreállítási pont létrehozása. Dönthet úgy, vagy a naplók (Point in Time) vagy a teljes, és az eltérés. Ha szeretné visszaállítani egy időpontban naplót, lépjen vissza, a 2. lépés. Ebben a lépésben egy adott teljes vagy különbözeti visszaállítási pontot állítja vissza. Ezzel a beállítással teljes és a különbségi helyreállítási pontok láthatja az elmúlt 30 napban. Ha meg szeretné tekinteni a 30 napnál régebbi helyreállítási pontok, kattintson a **szűrő** megnyitásához a **szűrő visszaállítási pontok** menü. Ha úgy dönt, hogy a különbözeti helyreállítási pont, az Azure Backup először visszaállítja a megfelelő teljes helyreállítási pontot, és majd alkalmazza a kiválasztott különbözeti helyreállítási pont.

    ![Válasszon visszaállítási pontot menü](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Az a **visszaállítási pont kiválasztása** menüben válasszon **teljes & különbségi**.

       ![Válasszon visszaállítási pontot menü](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Megjelenik a rendelkezésre álló helyreállítási pontok listáján.

    2. A helyreállítási pontok listáján, válasszon ki egy helyreállítási pontot, és kattintson a **OK** a visszaállítási pontot a művelet elvégzéséhez. 

        ![a teljes helyreállítási pont kiválasztása](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        A **visszaállítási pont** menü bezárul, és a **speciális konfiguráció** menü megnyitása.

        ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Az a **speciális konfiguráció** menüben:

        * Tartsa az adatbázis nem működő, visszaállítása után a a **Restore with NORECOVERY** menü **engedélyezve**. **Állítsa vissza a NORECOVERY** alapértelmezés szerint le van tiltva.
        * Ha azt szeretné, hogy a célkiszolgálón a visszaállítás helyének módosításához, adjon meg egy új elérési utat a **cél** oszlop.
        * Kattintson a **OK** hagyja jóvá a beállításokat, és zárja be a **speciális konfiguráció**.

    4. Az a **visszaállítása** menüben kattintson a **visszaállítása** elindítja a helyreállítási feladatot. Az értesítési területen előrehaladásának. Is nyomon követheti a folyamatot a az adatbázis-visszaállítási feladat.

       ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Az Azure virtuális gépeken futó SQL Azure Backup szolgáltatás műveleteinek kezelésére

Ez a szakasz tájékoztatást ad azokról a különböző Azure Backup felügyeleti műveletek érhető el az SQL Azure virtuális gépeken. A következő magas szintű műveletek érhetők el:

* Feladatok figyelése
* Biztonsági mentési riasztás
* SQL-adatbázis védelmének megszüntetéséről
* SQL-adatbázis a védelem folytatásához
* Az ad hoc biztonsági mentési feladatot indít
* SQL-kiszolgáló regisztrációjának törlése

### <a name="monitor-jobs"></a>Feladatok figyelése
Azure biztonsági mentés alatt álló egy vállalati osztály megoldást biztosít a speciális biztonsági riasztások és értesítések e fel (lásd az alábbi biztonsági mentési riasztások szakaszban). Ha továbbra is szeretné meghatározott feladatok figyelése a követelmény alapján a következő beállítások bármelyikét használhatja:

#### <a name="using-azure-portal---recovery-services-vault-for-all-ad-hoc-operations"></a>Recovery Services-tároló Azure-portál használatával -> minden alkalmi műveleteihez
Azure biztonsági mentése azt mutatja be, minden manuálisan elindítva, vagy ad hoc, a biztonsági mentési feladatok portálon feladatok. A portál belefoglalása elérhető feladatok: összes konfigurálni a biztonsági mentési műveleteket, manuálisan indított biztonsági mentési műveleteket, visszaállítási műveletek regisztrációs és Fedezze fel az adatbázis-műveletek és állítsa le a biztonsági mentési műveletek. 
![Speciális konfigurációs menü](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Az összes ütemezett biztonsági mentési feladatok többek között a teljes, különbségi és a napló biztonsági mentése nem jelenik meg a portálon, és SQL Server Management Studio segítségével, az alább ismertetett követhetők nyomon.
>

#### <a name="using-sql-server-management-studio-ssms-for-backup-jobs"></a>SQL Server Management Studio (SSMS) használatával a biztonsági mentési feladatok
Az Azure Backup minden biztonsági mentési műveletek SQL natív API-kat használ. A natív API-k használatával, az összes feladat adatait is lehívni a [SQL Fájljelző tábla](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) az msdb adatbázisban. 

Használhatja az alábbi lekérdezés "D1" nevű adott adatbázis biztonsági mentési feladataihoz lehívása példaként. Testre szabhatja a alatt több lekérdezés speciális figyelést.
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

### <a name="backup-alerts"></a>Biztonsági mentési riasztás

A Naplók biztonsági másolatainak előforduló 15 percenként a biztonsági mentési feladatok alkalmanként figyelése lehet fárasztó. Az Azure Backup minden biztonsági mentési hiba által indított e-mailes riasztásokhoz megadásával tervezett potenciálisan fárasztó ebben a helyzetben. Riasztások hibakód: az adatbázis szintjén egyesíti. Például ha egy adatbázis több biztonsági mentési hibák helyett minden egyes hibához tartozó riasztás fogadására, e-maileket az első sikertelen. Majd jelentkezhet be az Azure portálon, hogy az adatbázis ezt követő fellépő hibák figyelése. 

Biztonsági mentésekkel kapcsolatos riasztások figyelése:

1. Jelentkezzen be az Azure-előfizetése a [Azure-portálon](https://portal.azure.com).

2. Nyissa meg a Recovery Services-tároló regisztrált az SQL-virtuális gép.

3. Válassza ki a Recovery Services-tároló menü **riasztások és események**. 

   ![Speciális konfigurációs menü](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Az a **riasztások és események** menüjében válassza **biztonsági mentési riasztások** riasztások listájának megtekintéséhez.

   ![Speciális konfigurációs menü](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>SQL Server-adatbázisok védelmének megszüntetéséről

Ha kikapcsolja egy SQL Server-adatbázis védelmét, Azure biztonsági mentés megkérdezi a helyreállítási pontok megőrzése. SQL-adatbázis védelmének kikapcsolását két módja van:

* Állítsa le az összes jövőbeli biztonsági mentési feladat és összes helyreállítási pont törlése
* Állítsa le az összes jövőbeli biztonsági mentési feladat, de hagyja meg a helyreállítási pontok 

Költségekkel hagyja a helyreállítási pontok hordoz magában, mint a helyreállítási pontok az SQL végrehajtása a költség, valamint a felhasznált tárterület árképzési védett SQL-példány. Az SQL Azure Backup árairól kapcsolatos további információkért tekintse meg a [Azure biztonsági mentés árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/backup/). Szüntesse meg az adatbázis védelmét:

1. Nyissa meg a Recovery Services-tároló regisztrált az SQL-virtuális gép.

2. Válassza ki a tároló irányítópult **használati** biztonsági mentés elemeket a biztonsági mentés elemek menü megnyitásához.

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Az a **biztonsági másolati elemei** menüben válassza ki a biztonsági mentési felügyeleti típusát **SQL Azure virtuális gép**. 

    ![Kattintson a kívánt biztonsági másolatot a biztonsági mentési cél menü megnyitása](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A biztonsági mentés elemek listája itt állíthatja be az SQL-adatbázisok listájának megjelenítéséhez. 

4. SQL-adatbázisok listájának megtekintéséhez jelölje ki az adatbázis le kívánja állítani a védelmét.

    ![Válassza ki az SQL Azure virtuális gépen a listából](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Ha az adatbázis lehetőséget választja, a menü megnyitása. 

5. A kijelölt adatbázis menüben kattintson a **Stop biztonsági mentés** az adatbázis védelmének kikapcsolását.

    ![Válassza ki az adatbázis visszaállítása](./media/backup-azure-sql-database/stop-db-button.png)

    A **állítsa le a biztonsági mentés** menü megnyitása.

6. Az a **állítsa le a biztonsági mentés** menü, biztonsági mentési adatok megőrzése mellett dönt, vagy a biztonságimásolat-adatok törlése. Igény szerint is adja meg a védelmet, és a Megjegyzés leállításának oka.

    ![Válassza ki az adatbázis visszaállítása](./media/backup-azure-sql-database/stop-backup-button.png)

7. Kattintson a **Stop biztonsági mentés** kikapcsolni a védelmet az adatbázishoz. 

### <a name="resume-protection-for-a-sql-database"></a>SQL-adatbázis a védelem folytatásához

Ha a **biztonsági mentési adatok megőrzése** lehetőséget választotta, ha az SQL-adatbázis védelmét leáll, lehetséges a védelem folytatásához. Ha a rendszer nem őrzi meg a biztonsági mentési adatokat, a védelem nem lehet folytatni. 

1. Az SQL-adatbázis a védelem folytatásához, nyissa meg a biztonsági mentési elemet, és kattintson a **biztonsági mentés folytatása**.

    ![az adatbázis védelme folytatása](./media/backup-azure-sql-database/resume-backup-button.png)

   A biztonsági mentési házirend menü megnyitása.

2. Az a **biztonsági mentési házirend** menüben válassza ki a házirendet, és kattintson **mentése**.

### <a name="trigger-an-adhoc-backup"></a>Ad hoc biztonsági mentés

Ha azt szeretné, hogy egy ad hoc biztonsági mentés indíthat el. Ad hoc biztonsági mentés négy típusa van. További információ az egyes típusok: a cikk [típusok az SQL biztonsági mentések](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Teljes biztonsági mentés
* Csak teljes biztonsági mentés másolása
* Különbözeti biztonsági mentése
* Napló biztonsági mentése

### <a name="unregister-a-sql-server"></a>SQL-kiszolgáló regisztrációjának törlése

SQL-kiszolgáló regisztrációjának törlése, védelem eltávolítása után, de a tároló törlése előtt

1. Nyissa meg a Recovery Services-tároló regisztrált az SQL-virtuális gép.

2. Az a **kezelése** szakasz tároló menüben kattintson **biztonsági infrastruktúra**.  

   ![az adatbázis védelme folytatása](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Az a **felügyeleti kiszolgálók** kattintson **védett kiszolgálók**.

   ![az adatbázis védelme folytatása](./media/backup-azure-sql-database/protected-servers.png)

    A védett kiszolgálók menü megnyitása. 

4. Az a **védett kiszolgálók** menüben válassza ki a kiszolgáló regisztrációjának törlése. Ha azt szeretné, törli a tárolót, törölje regisztrációját összes kiszolgálójára.

5. A védett kiszolgálók menüben kattintson a jobb gombbal a védett kiszolgálón, és válassza ki **törlése**. 

   ![az adatbázis védelme folytatása](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="next-steps"></a>További lépések

Az Azure Backuppal kapcsolatos további információért tekintse meg a titkosított virtuális gépek biztonsági mentéséhez készült PowerShell-mintát.

> [!div class="nextstepaction"]
> [Titkosított virtuális gép biztonsági mentése](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
