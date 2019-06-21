---
title: Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése |} A Microsoft Docs
description: 'Útmutató: Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése'
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sachdevaswati
ms.openlocfilehash: 28577bfc755d80cd479a40b9e2b653af6ddec319
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204469"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>SQL Server-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

Az SQL Server-adatbázisok olyan alacsony helyreállítási-helyreállításipont-célkitűzés (RPO) és hosszú távú megőrzés igénylő kritikus fontosságú számítási feladatokhoz. Készíthet biztonsági másolatot az Azure-beli virtuális gépek (VM) futó SQL Server-adatbázisok [Azure Backup](backup-overview.md).

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot egy Azure Backup helyreállítási tárba-beli virtuális gépen futó SQL Server-adatbázis.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Létrehozhat és konfigurálhat egy tárolót.
> * Adatbázisok felderítéséhez és a biztonsági mentések beállítása.
> * Állítsa be az adatbázisok automatikus védelmét.


## <a name="prerequisites"></a>Előfeltételek

Egy SQL Server-adatbázis biztonsági másolatot, mielőtt ellenőrizze a következő feltételeknek:

1. Azonosítsa, vagy hozzon létre egy [Recovery Services-tároló](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ugyanabban a régióban vagy a virtuális Gépet üzemeltető SQL Server-példány területi beállításról.
2. Ellenőrizze, hogy rendelkezik-e a virtuális gép [hálózati kapcsolat](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Győződjön meg arról, hogy az SQL Server-adatbázisok kövesse a [fájlelnevezési irányelveket adatbázis Azure backup](#database-naming-guidelines-for-azure-backup).
4. Kifejezetten SQL 2008 és 2008 R2, a [beállításkulcs](#add-registry-key-to-enable-registration) kiszolgáló regisztráció engedélyezéséhez. Ebben a lépésben lehet nem lesz szükség a szolgáltatás általánosan elérhető esetén.
5. Ellenőrizze, hogy nincs engedélyezve az adatbázis biztonsági mentési megoldások. Tiltsa le minden más SQL Server biztonsági mentés, mielőtt, az adatbázis biztonsági mentése.

> [!NOTE]
> Az Azure Backup egy Azure virtuális gép és a konfliktus nélkül a virtuális gépen futó SQL Server-adatbázis is engedélyezheti.


### <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

Minden művelet, az SQL Server virtuális gép kapcsolódnia kell az Azure nyilvános IP-címek. Virtuálisgép-műveletek (adatbázis felderítése, biztonsági mentések konfigurálása, a biztonsági mentések ütemezéséhez, helyreállítási pontok visszaállítása és így tovább) anélkül, hogy az Azure nyilvános IP-címek való kapcsolódás sikertelen.

Kapcsolat létrehozása a következő lehetőségek egyikének használatával:

- **Lehetővé teszi az Azure-adatközpont IP-címtartományok**. Ez a beállítás lehetővé teszi, hogy [IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) letölthető. Szeretne hozzáférni a hálózati biztonsági csoport (NSG), a Set-AzureNetworkSecurityRule parancsmaggal. Ha Ön biztonságos címzettek listája csak régióspecifikus IP-címek, is kell frissíteni a megbízható címzettek listáját az Azure Active Directory (Azure AD) szolgáltatáscímke hitelesítés engedélyezéséhez.

- **Engedélyezi a hozzáférést az NSG-címkék használatával**. NSG-k használatával korlátozza a kapcsolatot, ha ezt a beállítást ad hozzá egy szabályt az NSG-t, amely lehetővé teszi a kimenő hozzáférést az Azure Backup az AzureBackup címke használatával. Ez a címke mellett is kell megfelelő [szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) az Azure ad és az Azure Storage, amely engedélyezi a csatlakozást a hitelesítés és az adatátvitelt. Az AzureBackup címkét csak a PowerShell jelenleg érhető el. Olyan szabály létrehozására AzureBackup címkével:

    - Adja hozzá az Azure-fiók hitelesítő adatait, és frissítse az országos felhők<br/>
    `Add-AzureRmAccount`

    - Válassza ki az NSG-előfizetést<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Válassza ki az NSG-hez<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Adja hozzá az Azure Backup szolgáltatás címke kimenő szabály engedélyezése<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Mentse az NSG-hez<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Hozzáférés engedélyezése az Azure-tűzfal a címkék használatával**. Azure tűzfalat használ, ha egy alkalmazás szabály létrehozása az AzureBackup használatával [teljes tartománynév-címke](https://docs.microsoft.com/azure/firewall/fqdn-tags). Ez lehetővé teszi a kimenő hozzáférést az Azure Backup szolgáltatásban.
- **HTTP-proxykiszolgáló üzembe forgalomirányítást**. Egy Azure virtuális gép egy SQL Server-adatbázis biztonsági mentésekor a biztonsági mentési bővítményt a virtuális gépen a HTTPS API-k segítségével felügyeleti parancsokat küldjön az Azure Backup és az Azure Storage-adatok. A biztonsági mentési bővítményt, az Azure AD is használ. A biztonsági mentési bővítményt forgalom irányítása a ezek három szolgáltatást, a HTTP-proxyn keresztül. A bővítmények olyan az egyetlen olyan összetevő, amely konfigurálva van a nyilvános internet-hozzáférés.

Csatlakozási lehetőségek a következők: a következő előnyeit és hátrányait:

**Beállítás** | **Előnyök** | **Disadvantages**
--- | --- | ---
IP-címtartományok engedélyezése | További költségek nélkül | Összetett, mert az idő előrehaladtával változik az IP-címtartományok kezelése <br/><br/> Azure-ban, nem csak az Azure Storage teljes hozzáférést biztosít
NSG-t szolgáltatás címkék használata | Könnyebben kezelhető tartomány automatikus összefésülés <br/><br/> További költségek nélkül <br/><br/> | Csak az NSG-ket használható <br/><br/> A teljes szolgáltatás hozzáférést biztosít
Azure tűzfal FQDN-címkék használata | Könnyebben kezelhető a szükséges teljes tartománynevek automatikusan történik. | Csak Azure tűzfallal együtt használható
HTTP proxyk használatára | Szabályozható a proxy a tároló URL-címek használata engedélyezett <br/><br/> Virtuális gépek internet egyetlen pont a hozzáférést <br/><br/> Nem vonatkozik Azure IP-cím módosításai | Virtuális gépek futtatása a proxy szoftverhez további költség

### <a name="database-naming-guidelines-for-azure-backup"></a>Pokyny Pro pojmenování az Azure Backup adatbázis

Kerülje a következő elemeket az adatbázis neve:

  * Záró és a szóközöket
  * Záró felkiáltójel jelzések (!)
  * A záró szögletes zárójelek (])
  * Pontosvesszővel (;)
  * Forward slash '/'

Aliasképző érhető el a nem támogatott karaktereket, de azokat nem ajánlott. További információt a [Table Service adatmodelljét ismertető](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) témakörben talál.

### <a name="add-registry-key-to-enable-registration"></a>Regisztrációs engedélyező beállításkulcs hozzáadása

1. Nyissa meg a Regedit
2. Hozza létre a beállításjegyzék-könyvtár elérési útja: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (kell WorkloadBackup, amelyet ezután hozhatók létre a Microsoft szerint a "Kulcs" TestHook létrehozása).
3. A beállításjegyzék könyvtár elérési útja alapján hozzon létre egy új "karakterlánc értéket" karakterlánc nevű **AzureBackupEnableWin2K8R2SP1** és értéket: **Igaz**

    ![Regisztráció engedélyezéséhez RegEdit](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

Ez a lépés azt is megteheti, automatizálhatja .reg fájlt a következő parancs futtatásával:

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Az SQL Server-adatbázisok felderítése

Hogyan derítheti fel a virtuális gépeken futó adatbázisok:

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a Recovery Services-tároló használatával készítsen biztonsági másolatot az adatbázisról.

2. Az a **Recovery Services-tároló** irányítópulton válassza **Backup**.

   ![A biztonsági mentés célja menüben nyissa meg a biztonsági másolat kiválasztása](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **biztonsági mentés célja**állítsa be **a számítási feladat futtató?** való **Azure**.

4. A **miről szeretne biztonsági másolatot készíteni**válassza **az SQL Server Azure virtuális gép**.

    ![Válassza ki az SQL Server Azure-beli virtuális gépen a biztonsági mentés](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **biztonsági mentés célja** > **virtuális gépeken lévő adatbázisok felderítése**válassza **felderítés indítása lapra** az előfizetésben nem védett virtuális gépek kereséséhez. A keresés eltarthat egy ideig, az előfizetés nem védett virtuális gépek számától függően.

   - Nem védett virtuális gépek felderítése, nevére és erőforráscsoportjára felsorolt után kell szerepelnek a listán.
   - Ha egy virtuális gép nem találja a várt módon, lásd: e azt már mentve van a tárolóban.
   - Több virtuális gép rendelkezhet ugyanazzal a névvel, de különböző erőforráscsoportokban is tartoznak.

     ![Biztonsági mentés függőben a virtuális gépeken lévő adatbázisok keresés közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gép listában válassza ki a virtuális gép fut az SQL Server-adatbázis > **adatbázisok felderítése**.

7. Adatbázis felderítése követése **értesítések**. Ez a művelet végrehajtásához szükséges időt Virtuálisgép-adatbázisok számától függ. A kijelölt adatbázisok észlelésekor megjelenik a sikert jelző üzenet.

    ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Az Azure Backup a virtuális gép összes SQL Server-adatbázisok deríti fel. A következő elemek felderítése alatt a háttérben fordulhat elő:

    - Az Azure Backup a virtuális gép regisztrálja a tárolóban, a számítási feladatok biztonsági mentéshez. A regisztrált virtuális Gépen található összes adatbázis biztonsági mentésre alkalmas csak ebben a tárolóban.
    - Az Azure Backup telepíti a AzureBackupWindowsWorkload bővítményt a virtuális gépen. Nincs ügynök telepítve van egy SQL-adatbázist.
    - Az Azure Backup a virtuális gépen hoz létre a szolgáltatás fiók NT Service\AzureWLBackupPluginSvc.
      - Minden biztonsági mentési és visszaállítási műveletek service fiókot használja.
      - NT Service\AzureWLBackupPluginSvc SQL-rendszergazdai engedélyekkel kell rendelkeznie. Az összes SQL Server virtuális gépen a Marketplace-en létrehozott a telepített SqlIaaSExtension kapható. A AzureBackupWindowsWorkload bővítményt használ a SQLIaaSExtension a szükséges engedélyek automatikus beszerzéséhez.
    - Ha nem hozott létre a virtuális gép a piactérről, vagy a SQL 2008 és 2008 R2, a virtuális gép nem rendelkezik a telepített SqlIaaSExtension, és a felderítési művelet meghiúsul, a hibaüzenet UserErrorSQLNoSysAdminMembership. A probléma megoldásához kövesse a [Virtuálisgép-engedélyek](backup-azure-sql-database.md#set-vm-permissions).

        ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

1. A **a biztonsági mentés célját** > **2. lépés: Biztonsági mentés konfigurálása**válassza **biztonsági mentés konfigurálása**.

   ![Válassza ki a biztonsági mentés konfigurálása](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. A **válassza ki a biztonsági másolatba foglalandó elemek**, láthatja az összes regisztrált rendelkezésre állási csoportok és önálló SQL Server-példányokat. A nyílra kattintva bontsa ki a nem védett adatbázisok listája az adott példány vagy AlwaysOn rendelkezésre állási csoport egy sor bal oldalán.  

    ![Az önálló adatbázisokhoz minden SQL Server-példányokat megjelenítése](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Válassza ki a védelmét, és válassza ki a kívánt összes adatbázis **OK**.

   ![Az adatbázis védelme érdekében](./media/backup-azure-sql-database/select-database-to-protect.png)

   Biztonsági mentési terhelés optimalizálása érdekében az Azure Backup beállítása adatbázisok maximális száma 50-re egy biztonsági mentési feladat.

     * Több mint 50 adatbázisok védelméhez, több biztonsági mentések konfigurálása.
     * Ahhoz, hogy [ ](#enable-auto-protection) az egész példány vagy az Always On rendelkezésre állási csoport. Az a **AUTOPROTECT** legördülő listában válassza **ON**, majd válassza ki **OK**.

    > [!NOTE]
    > A [automatikus védelem](#enable-auto-protection) funkció csak nem engedélyezi az összes meglévő adatbázis védelmi egyszerre, de automatikusan is védi az adott példány vagy a rendelkezésre állási csoporthoz hozzáadott új adatbázisokat.  

4. Válassza ki **OK** megnyitásához **biztonsági mentési szabályzat**.

    ![Az Always On rendelkezésre állási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

5. A **biztonsági mentési szabályzat**, válassza ki a szabályzatot, majd **OK**.

   - Válassza ki az alapértelmezett szabályzat szerint HourlyLogBackup.
   - Válassza ki a korábban létrehozott SQL meglévő biztonsági mentési házirend.
   - Új szabályzat a helyreállítási Időkorlát és a megőrzési tartomány alapján határozza meg.

     ![Válassza ki a biztonsági mentési szabályzat](./media/backup-azure-sql-database/select-backup-policy.png)

6. A **biztonsági mentési**válassza **biztonsági mentés engedélyezése**.

    ![a választott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

7. A konfigurációs folyamat nyomon a **értesítések** a portál területéhez.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>A biztonsági mentési szabályzat létrehozása

Biztonsági mentési szabályzat határozza meg, amikor a biztonsági másolatokat készít, és mennyi ideig tartott van.

- Egy szabályzat jön létre a tároló szintjén.
- Több tárolóhoz is használhat az azonos mentési házirenddel, de a biztonsági mentési házirendet kell alkalmaznia az egyes tárolókban.
- Amikor létrehoz egy biztonsági mentési szabályzatot, a napi teljes biztonsági mentést az alapértelmezett érték.
- Különbségi biztonsági másolat, de csak is hozzáadhat, ha teljes biztonsági mentés hetente történjen.
- Ismerje meg [különböző típusú biztonsági mentési szabályzatok](backup-architecture.md#sql-server-backup-types).

Biztonsági mentési szabályzat létrehozása:

1. A tárolóban, válassza ki a **biztonsági mentési házirendek** > **Hozzáadás**.
2. A **Hozzáadás**válassza **az SQL Server Azure virtuális gép** meghatározásához a házirend típusát.

   ![Olyan házirendtípus, az új biztonsági mentési házirend kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

3. A **házirendnév**, adja meg az új házirend nevét.
4. A **teljes biztonsági mentés házirend**, jelölje be a **biztonsági mentés gyakorisága**. Válasszon **napi** vagy **heti**.

   - A **napi**, válasszon ki és az időzónát, amikor megkezdődik a biztonsági mentési feladat.
   - A **heti**, amikor megkezdődik a biztonsági mentési feladatot, válassza ki a nap, hét, óra és időzóna.
   - Egy teljes biztonsági mentést futtatni, mert nem kapcsolhatja ki az **teljes biztonsági mentésre** lehetőséget.
   - Válassza ki **teljes biztonsági mentés** a szabályzat megtekintéséhez.
   - Különbségi biztonsági mentés napi teljes biztonsági mentések esetén nem hozható létre.

     ![új biztonsági mentési szabályzat mezők](./media/backup-azure-sql-database/full-backup-policy.png)  

5. A **MEGŐRZÉSI**, minden beállítás alapértelmezés szerint ki van jelölve. Törölje minden megőrzési időtartam korlátozza, hogy nem kívánja elvégezni, majd állítsa be a használandó időközt.

    - Bármilyen típusú biztonsági mentés (teljes, differenciális és napló) a minimális adatmegőrzési idő elsősorban az hét napig.
    - Helyreállítási pontok megőrzésének a megőrzési tartomány alapján címkével vannak ellátva. Ha például napi teljes biztonsági mentést választja, csak egy teljes biztonsági mentés naponta aktiválódik.
    - A biztonsági mentés meghatározott napja címkézett és a megőrzött alapján a heti megőrzési időtartam és a heti adatmegőrzési beállítás.
    - Havi és éves megőrzési időtartamok viselkedése hasonló módon.

       ![Megőrzési tartomány intervallum beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

6. Az a **teljes biztonsági mentés házirend** menüjében válassza **OK** , fogadja el a beállításokat.
7. A különbözeti biztonsági mentési szabályzat hozzáadásához válassza **különbözeti biztonsági mentési**.

   ![Megőrzési tartomány időközének beállításai](./media/backup-azure-sql-database/retention-range-interval.png)
   ![nyissa meg a különbözeti biztonsági mentési szabályzat menüje](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **különbözeti biztonsági mentési szabályzat**válassza **engedélyezése** a gyakoriság és megőrzési vezérlőelemek megnyitásához.

    - Csak egy különbségi biztonsági mentés naponta is indíthat.
    - Különbségi biztonsági másolatok megőrzésének legfeljebb 180 napra. A hosszabb adatmegőrzés megadásához használja a teljes biztonsági mentést.

9. Válassza ki **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.

10. A tranzakciós napló biztonsági mentési szabályzat hozzáadásához válassza **napló biztonsági mentési**.
11. A **Naplóalapú biztonsági mentés**válassza **engedélyezése**, majd állítsa be a gyakoriság és megőrzési szabályozza. Naplóalapú biztonsági mentések fordulhat elő, mint 15 perces gyakorisággal, és akár 35 napig lehet megtartani.
12. Válassza ki **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.

    ![A napló biztonsági mentési szabályzat szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Az a **biztonsági mentési szabályzat** menüben válassza ki, hogy engedélyezi-e **SQL biztonsági másolat tömörítése**.
    - A tömörítés alapértelmezés szerint le van tiltva.
    - A háttérben az Azure Backup használ SQL natív biztonsági mentési tömörítését.

14. Miután elvégezte a módosításokat a biztonsági mentési szabályzathoz, válassza ki a **OK**.


### <a name="modify-policy"></a>Házirend módosítása
A biztonsági mentési gyakoriság és megőrzési időtartam módosítása házirend módosítása.

> [!NOTE]
> Bármilyen módosítás a megőrzési időszak utólag áfakulcs minden a régebbi helyreállítási pontok mellett az újakat.

A tároló irányítópultjának lépjen a **kezelés** > **biztonsági mentési házirendek** , és válassza ki a szerkeszteni kívánt szabályzatot.

  ![A biztonsági mentési szabályzat kezelése](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Automatikus védelem engedélyezése  

Engedélyezheti az automatikus védelem automatikusan adatbázisok biztonsági mentése az összes jelenlegi és jövőbeni önálló SQL Server-példányra, vagy egy Always On rendelkezésre állási csoporthoz.

- Nincs nem választható ki az automatikus védelem egyszerre adatbázisok száma nincs korlátozva.
- Nem szelektív védelme vagy adatbázisok kizárása a védelem példány automatikus védelmét engedélyezi egyidejűleg.
- A példány már tartalmazza az egyes védett adatbázisok, ha azok fogja védett marad a megfelelő házirendeket automatikus védelem bekapcsolása után is. Később hozzáadott összes nem védett adatbázis lesz csak egyetlen házirendet az automatikus védelem, felsorolva időpontjában meghatározó **biztonsági mentés konfigurálása**. Azonban a házirend egy automatikusan védett adatbázishoz társított később módosíthatja.  

Automatikus védelem engedélyezése:

  1. A **biztonsági másolatba foglalandó elemek**, válassza ki azt a példányt, amelynek automatikus védelem engedélyezéséhez.
  2. Válassza ki a legördülő lista alatt **AUTOPROTECT**, válassza a **ON**, majd válassza ki **OK**.

      ![A rendelkezésre állási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Biztonsági mentés van konfigurálva az összes adatbázis együttes, és követhető **biztonsági mentési feladatok**.

Ha automatikus védelem letiltásához van szüksége, válassza ki a példány neve alatt **biztonsági mentés konfigurálása**, majd válassza ki **letiltása Autoprotect** -példány. Minden adatbázis biztonsági mentése továbbra is, de a jövőbeli adatbázisok nem fog automatikusan védve.

![A példány automatikus védelmének letiltásához](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:

- [SQL Server-adatbázisok biztonsági másolat visszaállítása](restore-sql-database-azure-vm.md)
- [Az SQL Server-adatbázisok biztonsági másolatait kezelése](manage-monitor-sql-database-backup.md)
