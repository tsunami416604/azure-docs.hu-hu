---
title: Az Azure SQL Server-adatbázisok biztonsági mentése |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan SQL Server biztonsági mentése az Azure-bA. A cikk azt is bemutatja, az SQL Server helyreállítása.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 0d11f42ab8194b9d451f9d21e88db001e189e974
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429451"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Adatbázisok biztonsági mentése az SQL Server Azure virtuális gépeken 

Az SQL Server-adatbázisok olyan alacsony a helyreállításipont-célkitűzés (RPO) és hosszú távú megőrzés igénylő kritikus fontosságú számítási feladatokhoz. Használata Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentését végezheti [Azure Backup](backup-overview.md). 

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot az Azure Backup helyreállítási tár egy Azure-beli virtuális gépen futó SQL Server-adatbázis. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Ellenőrizze az SQL Server-példány mentésére vonatkozó előfeltételeket ismertetjük.
> * Létrehozhat és konfigurálhat egy tárolót.
> * Adatbázisok felderítése, és állítsa be a biztonsági mentéseket.
> * Állítsa be az adatbázisok automatikus védelmét.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, ellenőrizze a következőket:

1. Győződjön meg arról, hogy rendelkezik Azure-ban futó SQL Server-példányt. Is [gyorsan létrehozhat egy SQL Server-példány](../sql-database/sql-database-get-started-portal.md) a Marketplace-en.
2. Tekintse át a nyilvános előzetes verziója az alábbi korlátozások.
3. Tekintse át a forgatókönyv támogatása.
4. [Tekintse át a gyakori kérdéseket](faq-backup-sql-server.md) erről a forgatókönyvről.


## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

A nyilvános előzetes verziója számos korlátozás tartozik.

- Az SQL Server rendszerű virtuális Gépet az Azure nyilvános IP-címek eléréséhez internetkapcsolatra van szükség. 
- Legfeljebb 2000 SQL Server-adatbázisok a tárolóban készíthető. Ha több, hozzon létre egy másik tárolót. 
- A biztonsági mentések [elosztott rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) nem teljesen működnek.
- SQL Server mindig a Feladatátvevőfürt-példányokat (példányoktól) biztonsági mentés esetén nem támogatott.
- Az SQL Server biztonsági másolat a portálon kell konfigurálni. Biztonsági mentés jelenleg nem konfigurálhatja az Azure PowerShell, CLI vagy a REST API-k.
- Az FCI tükrözött adatbázisokat, adatbázis-pillanatképeket és adatbázisok biztonsági mentési és visszaállítási műveletek nem támogatottak.
- Adatbázisok nagy számú fájl esetén nem biztosítható. Támogatott fájlok maximális száma nem determinisztikus. Azt nem csak a fájlok száma attól függ, de is függ, a fájlok elérési út hossza. 

Tekintse meg [gyakori kérdésekkel foglalkozó szakaszban](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) további részleteket a támogatás/nem támogatott forgatókönyvek.

## <a name="scenario-support"></a>A forgatókönyv támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | SQL-piactér Azure virtuális gépek és a-Marketplace (SQL Server telepített manuallly) virtuális gépek támogatottak.
**Támogatott helyeken** | Délkelet-Ausztrália (ASE); Dél-Brazília (BRS); Közép-Kanada (CNC); Kelet-Kanada (CE); USA középső RÉGIÓJA (CUS); Kelet-Ázsia (EA); Kelet-Ausztrália (AE); USA keleti RÉGIÓJA (EUS); USA keleti RÉGIÓJA (EUS2); 2 Közép-India (INC); Dél-India (INS); Kelet-japán (JPE); Nyugat-japán (JPW); Korea középső régiója (KRC); Korea déli régiója (KRS); USA északi középső RÉGIÓJA (NCUS); Észak-Európa (NE); USA déli középső RÉGIÓJA (SCUS); Délkelet-Ázsia (SEA); Egyesült Királyság déli régiója (UKS); Egyesült Királyság nyugati régiója (UKW); USA nyugati középső RÉGIÓJA (WCUS); Nyugat-Európa (WE); USA nyugati RÉGIÓJA (WUS); USA nyugati RÉGIÓJA (WUS 2) 2
**Támogatott operációs rendszerek** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux jelenleg nem támogatott.
**Támogatott SQL Server-verziók** | Az SQL Server 2017; SQL Server 2016-ban, SQL Server 2014, SQL Server 2012-ben.<br/><br/> Enterprise, Standard, Web, Developer, Express.


## <a name="prerequisites"></a>Előfeltételek

Biztonsági másolatot készíteni az SQL Server-adatbázishoz, mielőtt a következőket ellenőrizze:

1. Azonosítsa vagy [létrehozása](backup-azure-sql-database.md#create-a-recovery-services-vault) ugyanabban a régióban vagy a virtuális Gépet üzemeltető SQL Server-példány területi beállításról egy Recovery Services-tárolót. V
2. [Ellenőrizze a virtuális gép engedélyeit](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) szükséges az SQL-adatbázisok biztonsági mentését.
3. Ellenőrizze, hogy rendelkezik-e a virtuális gép [hálózati kapcsolat](backup-azure-sql-database.md#establish-network-connectivity).
4. Ellenőrizze, hogy az SQL Server-adatbázisok összhangban vannak elnevezve [pokyny Pro pojmenování](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) az Azure Backup szolgáltatáshoz.
5. Győződjön meg arról, hogy nincs engedélyezve az adatbázis biztonsági mentési megoldások. Tiltsa le az összes többi SQL Server biztonsági mentés előtt végzi el ebben a forgatókönyvben. Egy Azure virtuális gép Azure Backup és az Azure Backup bármely konfliktus nélkül a virtuális gépen futó SQL Server-adatbázis számára engedélyezheti.


### <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

Minden műveletre az SQL Server rendszerű virtuális gép virtuális gép kapcsolatot kell létesítenie az Azure nyilvános IP-címeket. Virtuális gép operations (adatbázis-felderítés, biztonsági mentések konfigurálása, a biztonsági mentések ütemezéséhez, állítsa vissza a helyreállítási pontok stb) anélkül, hogy a nyilvános IP-címek való kapcsolódás sikertelen. A kapcsolatot az alábbi lehetőségek közül:

- **Lehetővé teszi az Azure-adatközpont IP-címtartományok**: Lehetővé teszi a [IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) letölthető. Hozzáférés az egy hálózati biztonsági csoport (NSG), használja a **Set-AzureNetworkSecurityRule** parancsmagot.
- **HTTP-proxykiszolgáló üzembe forgalomirányítást**: Egy Azure virtuális gép egy SQL Server-adatbázis biztonsági mentésekor a biztonsági mentési bővítményt a virtuális gépen a HTTPS API-k segítségével felügyeleti parancsokat küldjön az Azure Backup és az Azure Storage-adatok. A biztonsági mentési bővítményt, az Azure Active Directory (Azure AD) is használ. A biztonsági mentési bővítményt forgalom irányítása a ezek három szolgáltatást, a HTTP-proxyn keresztül. A bővítmény által az egyetlen olyan összetevő, amely konfigurálva van a nyilvános internet-hozzáférés.

Minden egyes lehetőségeket vannak előnyei és hátrányai

**Beállítás** | **Előnyök** | **Disadvantages**
--- | --- | ---
IP-címtartományok engedélyezése | További költségek nélkül. | Mert az idő előrehaladtával változik az IP-címtartományok kezelése bonyolult. <br/><br/> Azure-ban, nem csak az Azure Storage teljes hozzáférést biztosít.
HTTP proxyk használatára   | Szabályozható a proxy a tároló URL-címek használata engedélyezett. <br/><br/> Virtuális gépek internet egyetlen pont hozzáférés. <br/><br/> Nem vonatkozik Azure IP-cím változik. | Virtuális gépek futtatása a proxy szoftverhez további költség. 

### <a name="set-vm-permissions"></a>Virtuális gép engedélyeinek beállítása

Az Azure Backup több minden hajtja végre az SQL Server-adatbázis biztonsági másolatainak konfigurálásakor:

- Hozzáadja a **AzureBackupWindowsWorkload** bővítmény.
- A virtuális gépen adatbázisok felderítéséhez, az Azure Backup hoz létre a fiók **NT SERVICE\AzureWLBackupPluginSvc**. Ennek a fióknak a biztonsági mentés és visszaállítás szolgál, és az SQL-rendszergazdai engedélyekkel kell rendelkeznie.
- Az Azure Backup használja a **NT AUTHORITY\SYSTEM** adatbázis felderítési/lekérdezési, így ennek a fióknak kell lennie egy nyilvános bejelentkezés az SQL-fiók.

Ha az SQL Server rendszerű virtuális gép az Azure Marketplace-ről nem hozott létre, akkor előfordulhat, hogy megjelenik egy hibaüzenet **UserErrorSQLNoSysadminMembership**. Ha ez történik [az alábbi lépéseket követve](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Az Azure Backup database elnevezési irányelvei ellenőrzése

Kerülje az adatbázis neve a következőket:

  * Kezdő/záró szóköz
  * Záró '!'
  * Záró szögletes zárójel "]"

Az Azure-tábla nem támogatott karaktereket aliasképző van, de azokat nem ajánlott. [További információk](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Az SQL Server-adatbázisok felderítése

Fedezze fel a virtuális gépen futó adatbázisok. 
1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a Recovery Services-tároló használatával készítsen biztonsági másolatot az adatbázisról.

2. Az a **Recovery Services-tároló** irányítópulton válassza **Backup**. 

   ![A biztonsági mentés célja menüben nyissa meg a biztonsági másolat kiválasztása](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **biztonsági mentés célja**állítsa be **a számítási feladat futtató** való **Azure** (alapértelmezett).

4. A **miről szeretne biztonsági másolatot készíteni**válassza **az SQL Server Azure virtuális gép**.

    ![Válassza ki az SQL Server Azure-beli virtuális gépen a biztonsági mentés](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


5. A **biztonsági mentés célja** > **virtuális gépeken lévő adatbázisok felderítése**válassza **felderítés indítása lapra** az előfizetésben nem védett virtuális gépek kereséséhez. Eltarthat egy ideig, az előfizetés nem védett virtuális gépek számától függően.

    ![Biztonsági mentés függőben a virtuális gépeken lévő adatbázisok keresés közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gép listában válassza ki a virtuális gép fut az SQL Server-adatbázis > **adatbázisok felderítése**.

7. Adatbázis felderítése nyomon követése a **értesítések** területen. A feladat befejeződik, attól függően, hány adatbázist vannak a virtuális gép egy ideig is eltarthat. A kijelölt adatbázisok észlelésekor megjelenik a sikert jelző üzenet.

    ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - Nem védett virtuális gépek felderítése, nevére és erőforráscsoportjára felsorolt után kell szerepelnek a listán.
    - Ha egy virtuális gép nem találja a várt módon, ellenőrizze-e már biztonsági mentését egy tárolóban.
    - Több virtuális gép is rendelkezhet ugyanazzal a névvel, de különböző erőforráscsoportokban is tartoznak. 

9. Válassza ki a virtuális gép fut az SQL Server-adatbázis > **adatbázisok felderítése**. 
10. Az Azure Backup a virtuális gép összes SQL Server-adatbázisok deríti fel. A felderítés során a következő a háttérben történik:

    - Az Azure Backup regisztrálja a virtuális Gépet a tárolóval, a számítási feladatok biztonsági mentéshez. Ebben a tárolóban csak készíthető a regisztrált virtuális gép összes adatbázist.
    - Az Azure Backup telepíti a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Nincs ügynök telepítve van az SQL-adatbázis.
    - Az Azure Backup a szolgáltatás-fiókot hoz létre **NT Service\AzureWLBackupPluginSvc** a virtuális gépen.
        - Minden biztonsági mentési és visszaállítási műveletek service fiókot használja.
        - **NT Service\AzureWLBackupPluginSvc** SQL-rendszergazdai engedélyekre van szüksége. Az összes SQL Server virtuális gépek az Azure MArkplace létrehozott kapható a **SqlIaaSExtension** telepítve. A **AzureBackupWindowsWorkload** kiterjesztést használja a **SQLIaaSExtension** a szükséges engedélyek automatikus beszerzéséhez.
    - Ha nem hozott létre a virtuális gép a piactérről, akkor a virtuális gép nem rendelkezik a **SqlIaaSExtension** telepítve van, és a felderítési művelet meghiúsul, a hibaüzenet **UserErrorSQLNoSysAdminMembership**. Kövesse az utasításokat a [# – sql-rendszergazdai-engedélyek fix] a probléma megoldásához.

        ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása 

Biztonsági mentési terhelés optimalizálása érdekében az Azure Backup beállítása adatbázisok maximális száma 50-re egy biztonsági mentési feladat.

- Több mint 50 adatbázisok védelméhez, több biztonsági mentések konfigurálása.
- Automatikus védelem alternatily, engedélyezheti. Automatikus védelem védi, lépjen egy meglévő adatbázist, és automatikusan megvédi a rendelkezésre állási csoport példányához hozzáadott új adatbázisokat.


Biztonsági mentés a következőképpen konfigurálja:

1. Válassza ki a tároló irányítópultjának **Backup**. 

   ![A biztonsági mentés célja menüben nyissa meg a biztonsági másolat kiválasztása](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **biztonsági mentés célja** menüben beállítása **a számítási feladat futtató** való **Azure**.

4. A **választható biztonsági mentéséhez**válassza **az SQL Server Azure virtuális gép**.

    ![Válassza ki az SQL Server Azure-beli virtuális gépen a biztonsági mentés](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. A **biztonsági mentés célja** kiválasztása **biztonsági mentés konfigurálása**.

    ![Válassza ki a biztonsági mentés konfigurálása](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Az önálló adatbázisokhoz minden SQL Server-példányokat megjelenítése](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Válassza ki a védeni kívánt összes adatbázis > **OK**.

    ![Az adatbázis védelme érdekében](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Az összes SQL Server-példányok jelennek meg (önálló és a rendelkezésre állási csoportok).
    - Válassza ki a lefelé mutató nyílra a bal oldalon, a példány neve vagy rendelkezésre állási csoport szűrése.

7. Az a **Backup** menüjében válassza **biztonsági mentési szabályzat**. 

    ![Válassza ki a biztonsági mentési szabályzat](./media/backup-azure-sql-database/select-backup-policy.png)

8. A **biztonsági mentési házirend kiválasztása**, válasszon ki egy szabályzatot, majd kattintson a **OK**.

    - Válassza ki az alapértelmezett házirend: **HourlyLogBackup**.
    - Válassza ki a korábban létrehozott SQL meglévő biztonsági mentési házirend.
    - [Adja meg egy új házirendet](backup-azure-sql-database.md#define-a-backup-policy) a helyreállítási Időkorlát és a megőrzési tartomány alapján.
    - Az előzetes verzióban már nem szerkesztheti a meglévő biztonsági mentési házirend.
    
9. A **biztonsági mentés menü**válassza **biztonsági mentés engedélyezése**.

    ![a választott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

10. A konfigurációs folyamat nyomon a **értesítések** a portál területéhez.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>A biztonsági mentési szabályzat létrehozása

Biztonsági mentési szabályzat határozza meg, amikor a biztonsági másolatokat készít, és mennyi ideig tartott van. 

- Egy szabályzat jön létre a tároló szintjén.
- Több tárolóhoz is használhat az azonos mentési házirenddel, de a biztonsági mentési házirendet kell alkalmaznia az egyes tárolókban.
- Amikor létrehoz egy biztonsági mentési szabályzatot, a napi teljes biztonsági mentést az alapértelmezett érték.
- Különbségi biztonsági másolat, de csak is hozzáadhat, ha teljes biztonsági mentés hetente történjen.
- [Ismerje meg](backup-architecture.md#sql-server-backup-types) különböző típusú biztonsági mentési szabályzatok.


Biztonsági mentési szabályzat létrehozása:

1. Kattintson a tárolóban **biztonsági mentési házirendek** > **Hozzáadás**.
2. A **Hozzáadás** menüben kattintson a **az SQL Server Azure virtuális gép**. A házirend típusát határozza meg.

   ![Olyan házirendtípus, az új biztonsági mentési házirend kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

3. A **házirendnév**, adja meg az új házirend nevét. 
4. A **teljes biztonsági mentés házirend**, jelölje be a **biztonsági mentés gyakorisága**, válassza a **napi** vagy **heti**.

    - A **napi**, válasszon ki és az időzónát, amikor megkezdődik a biztonsági mentési feladat.
    - Egy teljes biztonsági mentést kell futtatásakor, nem kapcsolhatja ki az **teljes biztonsági mentésre** lehetőséget.
    - Kattintson a **teljes biztonsági mentés** a szabályzat megtekintéséhez. 
    - Különbségi biztonsági mentés napi teljes biztonsági mentések esetén nem hozható létre.
    - A **heti**, amikor megkezdődik a biztonsági mentési feladatot, válassza ki a nap, hét, óra és időzóna.

    ![új biztonsági mentési szabályzat mezők](./media/backup-azure-sql-database/full-backup-policy.png)  

5. A **megőrzési**, alapértelmezés szerint minden lehetőség ki van jelölve. Törölje minden olyan nemkívánatos adatmegőrzési tartomány korlátok nem kívánja használni, és állítsa be a használandó időközt. 

    - Helyreállítási pontok megőrzésének a megőrzési tartomány alapján címkével vannak ellátva. Ha például napi teljes biztonsági mentést választja, csak egy teljes biztonsági mentés naponta aktiválódik.
    - A biztonsági mentés meghatározott napja címkézett és a megőrzött alapján a heti megőrzési időtartam és a heti adatmegőrzési beállítás.
    - A havi és éves megőrzési időtartamok viselkedése hasonló módon.

   ![Megőrzési tartomány intervallum beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

    

6. Az a **teljes biztonsági mentés házirend** menüjében válassza **OK** , fogadja el a beállításokat.
7. A különbözeti biztonsági mentési szabályzat hozzáadásához válassza **különbözeti biztonsági mentési**. 

   ![Megőrzési tartomány időközének beállításai](./media/backup-azure-sql-database/retention-range-interval.png)
   ![nyissa meg a különbözeti biztonsági mentési szabályzat menüje](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **különbözeti biztonsági mentési szabályzat**válassza **engedélyezése** a gyakoriság és megőrzési vezérlőelemek megnyitásához. 

    - Legfeljebb napi egy különbségi biztonsági mentés is indíthat.
    - Különbségi biztonsági másolatok megőrzésének legfeljebb 180 napra. Ha hosszabb adatmegőrzés megadásához, teljes biztonsági mentést kell használnia.

9. Válassza ki **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.

10. A tranzakciós napló biztonsági mentési szabályzat hozzáadásához válassza **napló biztonsági mentési**.
11. A **Naplóalapú biztonsági mentés**válassza **engedélyezése**, majd állítsa be a gyakoriság és megőrzési szabályozza. Naplóalapú biztonsági mentések fordulhat elő, mint 15 perces gyakorisággal, és akár 35 napig lehet megtartani.
12. Válassza ki **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.

   ![A napló biztonsági mentési szabályzat szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Az a **biztonsági mentési szabályzat** menüben válassza ki, hogy engedélyezi-e **SQL biztonsági másolat tömörítése**.
    - A tömörítés alapértelmezés szerint le van tiltva.
    - A háttérben az Azure Backup használ SQL natív biztonsági mentési tömörítését.

14. Miután elvégezte a módosításokat a biztonsági mentési szabályzathoz, válassza ki a **OK**.



## <a name="enable-auto-protection"></a>Automatikus védelem engedélyezése  

Engedélyezze az automatikus védelem automatikusan biztonsági másolatot az összes meglévő adatbázisok és a jövőben egy önálló SQL Server-példány és a egy SQL Server mindig a rendelkezésre állási csoport hozzáadott adatbázisok. 

- Automatikus védelem bekapcsolása és a egy házirendet választ ki, ha a védett pexisting adatbázisokat továbbra is az előző szabályzatot.
- Egy, lépjen az automatikus védelemhez választhat adatbázisok száma nincs korlátozva van.

A következő engedélyezze az automatikus védelem:

1. A **biztonsági másolatba foglalandó elemek**, válassza ki azt a példányt, amelynek automatikus védelem engedélyezéséhez.
2. Válassza ki a legördülő lista alatt **Autoprotect**, és állítsa **a**. Ezután kattintson az **OK** gombra.

    ![Az Always On rendelkezésre állási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

3. Biztonsági mentés van konfigurálva az összes adatbázis együttes, és követhető **biztonsági mentési feladatok**.


Automatikus védelem letiltásához van szüksége, ha a példány neve alatt kattintson **biztonsági mentés konfigurálása**, és válassza ki **letiltása Autoprotect** -példány. Minden adatbázis biztonsági mentéséhez továbbra is. Azonban a jövőbeli adatbázisok nem fog automatikusan védett.

![Tiltsa le az automatikus védelmét azon a példányon](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Javítsa ki az SQL-rendszergazdai engedélyek

Ha szeretné javítani az engedélyek miatt egy **UserErrorSQLNoSysadminMembership** hiba, tegye a következőket:

1. Az SQL Server SysAdmin (rendszergazda) engedélyekkel rendelkező fiók használatával jelentkezzen be az SQL Server Management Studio (SSMS). Ha nincs szükség speciális engedélyek, a Windows-hitelesítés működnie kell.
2. Nyissa meg az SQL Server, a **biztonsági/bejelentkezések** mappát.

    ![Nyissa meg a biztonsági/bejelentkezések mappában a fiókok](./media/backup-azure-sql-database/security-login-list.png)

3. Kattintson a jobb gombbal a **bejelentkezések** mappára, és válassza **új bejelentkezés**. A **bejelentkezés – új**válassza **keresési**.

    ![A bejelentkezés – új párbeszédpanel, válassza ki a keresés](./media/backup-azure-sql-database/new-login-search.png)

3. A virtuális Windows-szolgáltatásfiók **NT SERVICE\AzureWLBackupPluginSvc** a virtuális gép regisztrációja és SQL felderítési fázis során jött létre. Adja meg a fiók nevét, ahogyan az **írja be a kijelölendő objektum nevét**. Válassza ki **Névellenőrzés** oldani a nevet. Kattintson az **OK** gombra.

    ![Válassza ki a szolgáltatás ismeretlen név Névellenőrzés](./media/backup-azure-sql-database/check-name.png)


4. A **kiszolgálói szerepkörök**, győződjön meg arról, hogy a **SysAdmin (rendszergazda)** szerepkör van kiválasztva. Kattintson az **OK** gombra. A szükséges engedélyekkel most már léteznie kell.

    ![Győződjön meg arról, hogy a sysadmin (rendszergazda) kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. Az adatbázis mostantól társíthat a Recovery Services-tároló. Az Azure Portalon az a **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló, amely hibás állapotú > **adatbázisok újbóli felderítése**.

    ![Ellenőrizze a kiszolgáló rendelkezik-e megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

6. Folyamat ellenőrzéséhez a **értesítések** területen. A kijelölt adatbázisok találhatók, a sikert jelző üzenet jelenik meg.

    ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

Engedélyezheti azt is megteheti, [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) a teljes példánya vagy mindig a rendelkezésre állási csoport kiválasztásával a **ON** a megfelelő legördülő listában, a beállítás a **AUTOPROTECT**  oszlop. A [automatikus védelem](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) szolgáltatás nem csak lehetővé teszi, hogy egy nyissa meg az összes meglévő adatbázis védelmét, de automatikusan is védi a hozzáadni kívánt példányát, vagy a rendelkezésre állási csoport a jövőben az új adatbázisok.  

   ![Az Always On rendelkezésre állási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>További lépések

- [Ismerje meg](restore-sql-database-azure-vm.md) biztonsági másolat az SQL Server-adatbázisok visszaállítása.
- [Ismerje meg](manage-monitor-sql-database-backup.md) kezelése biztonsági mentés az SQL Server-adatbázisok.

