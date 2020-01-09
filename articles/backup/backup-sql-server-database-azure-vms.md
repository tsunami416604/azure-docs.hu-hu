---
title: SQL Server-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést SQL Server adatbázisokról az Azure-beli virtuális gépeken Azure Backup használatával.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 52a7e98702299e790ee097cca871332ebb6a52c5
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611389"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>SQL Server-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

SQL Server adatbázisok olyan kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási célt (RPO) és hosszú távú adatmegőrzést igényelnek. Az Azure Virtual Machines (VM) rendszeren futó SQL Server adatbázisok biztonsági mentését [Azure Backup](backup-overview.md)használatával végezheti el.

Ez a cikk bemutatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup Recovery Services-tárolóra.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása.
> * Adatbázisok felderítése és biztonsági mentések beállítása.
> * Automatikus védelem beállítása adatbázisok számára.

## <a name="prerequisites"></a>Előfeltételek

SQL Server adatbázis biztonsági mentése előtt tekintse meg a következő feltételeket:

1. Azonosítson vagy hozzon létre egy [Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) tárolót ugyanabban a régióban vagy területi beállításban, mint a SQL Server példányt futtató virtuális gép.
2. Ellenőrizze, hogy a virtuális gép rendelkezik-e [hálózati kapcsolattal](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Győződjön meg arról, hogy a SQL Server adatbázisok a [Azure Backup adatbázis-elnevezési irányelveit](#database-naming-guidelines-for-azure-backup)követik.
4. Győződjön meg arról, hogy nincs más biztonsági mentési megoldás, amely engedélyezve van az adatbázishoz. Az adatbázis biztonsági mentése előtt tiltsa le az összes többi SQL Server biztonsági mentést.

> [!NOTE]
> Engedélyezheti az Azure-beli virtuális gépek Azure Backupét, valamint a virtuális gépen futó SQL Server-adatbázis ütközés nélküli működését is.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létesítése

A SQL Server VM az összes művelethez kapcsolódnia kell az Azure nyilvános IP-címeihez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági másolatok konfigurálása, biztonsági másolatok ütemezése, helyreállítási pontok visszaállítása stb.) nem működnek az Azure nyilvános IP-címekkel való kapcsolat nélkül.

Kapcsolat létesítése a következő lehetőségek egyikének használatával:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Az Azure-adatközpont IP-tartományának engedélyezése

Ez a beállítás engedélyezi a letöltött fájl [IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653) . Hálózati biztonsági csoport (NSG) eléréséhez használja a set-AzureNetworkSecurityRule parancsmagot. Ha a biztonságos címzettek listája csak a régióra jellemző IP-címeket tartalmazza, akkor a hitelesítés engedélyezéséhez is frissítenie kell a biztonságos címzettek listáját a Azure Active Directory (Azure AD) szolgáltatás címkével.

#### <a name="allow-access-using-nsg-tags"></a>Hozzáférés engedélyezése NSG-címkék használatával

Ha a NSG-t használja a kapcsolat korlátozására, akkor a AzureBackup szolgáltatás címkével kell rendelkeznie, hogy lehetővé tegye a kimenő hozzáférést a Azure Backuphoz. Emellett az Azure AD-hez és az Azure Storage-hoz kapcsolódó [szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) használatával is engedélyeznie kell a hitelesítést és az adatátvitelt. Ezt a Azure Portal vagy a PowerShell segítségével teheti meg.

Szabály létrehozása a portál használatával:

  1. A **minden szolgáltatás**területen lépjen a **hálózati biztonsági csoportok** elemre, és válassza ki a hálózati biztonsági csoportot.
  2. A **Beállítások**területen válassza a **kimenő biztonsági szabályok** lehetőséget.
  3. Válassza a **Hozzáadás** lehetőséget. Adja meg az új szabály létrehozásához szükséges összes adatot a [biztonsági szabály beállításai](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)című témakörben leírtak szerint. Győződjön meg arról, hogy a **cél** a **Service tag** és a **cél szolgáltatás címkéje** **AzureBackup**értékre van állítva.
  4. Kattintson a **Hozzáadás**gombra az újonnan létrehozott kimenő biztonsági szabály mentéséhez.

Szabály létrehozása a PowerShell használatával:

 1. Azure-fiók hitelesítő adatainak hozzáadása és az országos felhők frissítése<br/>
      `Add-AzureRmAccount`<br/>

 2. Válassza ki a NSG-előfizetést<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Válassza ki a NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Azure Backup szolgáltatási címke engedélyezése Kimenő szabály hozzáadása<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. A Storage Service-címke engedélyezésének engedélyezése Kimenő szabály hozzáadása<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Kimenő engedélyezési szabály hozzáadása a AzureActiveDirectory szolgáltatás címkéjéhez<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. A NSG mentése<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Hozzáférés engedélyezése Azure Firewall címkék használatával**. Ha Azure Firewall használ, hozzon létre egy szabályt a AzureBackup [FQDN-címke](https://docs.microsoft.com/azure/firewall/fqdn-tags)használatával. Ez lehetővé teszi a kimenő hozzáférést Azure Backuphoz.

**Http-proxykiszolgáló üzembe helyezése a forgalom irányításához**. Ha egy Azure-beli virtuális gépen SQL Server adatbázisról készít biztonsági másolatot, a virtuális gépen futó biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat az Azure Storage-ba való Azure Backup és az adattároláshoz. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A bővítmények az egyetlen olyan összetevő, amely a nyilvános internethez való hozzáférésre van konfigurálva.

A csatlakozási lehetőségek a következő előnyökkel és hátrányokkal járnak:

**Beállítás** | **Előnyök** | **Hátrányok**
--- | --- | ---
IP-címtartományok engedélyezése | Nincs további költség | Összetett a kezeléshez, mert az IP-címtartományok változnak az idő múlásával <br/><br/> Hozzáférést biztosít az egész Azure-hoz, nem csak az Azure Storage-hoz
NSG szolgáltatásbeli címkék használata | A tartomány módosításainak könnyebb kezelése automatikusan történik <br/><br/> Nincs további költség <br/><br/> | Csak NSG használható <br/><br/> Hozzáférést biztosít a teljes szolgáltatáshoz
Azure Firewall FQDN-címkék használata | Könnyebben kezelhető, mert a szükséges teljes tartománynevek automatikusan kezelhetők | Csak Azure Firewall használható
HTTP-proxy használata | A proxy részletes vezérlője a tárolási URL-címeken keresztül engedélyezett <br/><br/> A virtuális gépekhez való internetes hozzáférés egyetlen pontja <br/><br/> Nem vonatkozik az Azure IP-címek változásai | További költségek egy virtuális gép futtatásához a proxy szoftverrel

### <a name="database-naming-guidelines-for-azure-backup"></a>Adatbázis-elnevezési irányelvek a Azure Backup

Kerülje a következő elemek használatát az adatbázis-nevekben:

* Záró és kezdő szóközök
* Záró felkiáltójelek (!)
* Szögletes zárójelek zárása (])
* Pontosvessző: ";"
* Perjelek továbbítása

Az alias nem támogatott karakterekhez érhető el, de a rendszer elkerüli a használatát. További információt a [Table Service adatmodelljét ismertető](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) témakörben talál.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server adatbázisok felderítése

Virtuális gépen futó adatbázisok felderítése:

1. A [Azure Portal](https://portal.azure.com)nyissa meg az adatbázis biztonsági mentésére használt Recovery Services-tárolót.

2. A **Recovery Services** -tároló irányítópultján válassza a **biztonsági mentés**lehetőséget.

   ![Válassza a biztonsági mentés lehetőséget a biztonsági mentés célja menü megnyitásához](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **biztonsági mentés célja**beállításnál állítsa be, hogy **hol fut a** számítási feladat? az **Azure**-ban.

4. A **Miről szeretne biztonsági másolatot készíteni**, válassza **a SQL Server lehetőséget az Azure-beli virtuális gépen**.

    ![SQL Server kiválasztása az Azure-beli virtuális gépen a biztonsági mentéshez](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **biztonsági mentés célja** > a **virtuális gépeken található adatbázisok felderítése**területen válassza a **felderítés indítása** lehetőséget a nem védett virtuális gépek kereséséhez az előfizetésben. A keresés eltarthat egy ideig, az előfizetésben nem védett virtuális gépek számától függően.

   * A nem védett virtuális gépeknek a felderítés után meg kell jelennie a listában a név és az erőforráscsoport alapján.
   * Ha egy virtuális gép nem szerepel a várt módon, tekintse meg, hogy már van-e biztonsági másolat egy tárolóban.
   * Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

     ![A biztonsági mentés függőben van a virtuális gépeken lévő adatbázisok keresése közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gép listában válassza ki a SQL Server adatbázist futtató virtuális gépet > a **felderítési adatbázisok**.

7. Az **értesítésekben**nyomon követheti az adatbázis-felderítést. A művelethez szükséges idő a virtuális gépek adatbázisainak számától függ. A kiválasztott adatbázisok észlelésekor megjelenik egy sikert jelző üzenet.

    ![Sikeres üzembe helyezési üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup a virtuális gépen lévő összes SQL Server adatbázist felfedi. A felderítés során a háttérben a következő elemek történnek:

    * Azure Backup regisztrálja a virtuális gépet a tárolóban a munkaterhelés biztonsági mentéséhez. A regisztrált virtuális gépen lévő összes adatbázis biztonsági mentése csak erre a tárolóra lehetséges.
    * Azure Backup telepíti a AzureBackupWindowsWorkload-bővítményt a virtuális gépen. Nincs ügynök telepítve egy SQL-adatbázisra.
    * A Azure Backup létrehozza a szolgáltatási fiók NT-Service\AzureWLBackupPluginSvc a virtuális gépen.
      * Az összes biztonsági mentési és visszaállítási művelet a szolgáltatásfiókot használja.
      * Az NT Service\AzureWLBackupPluginSvc használatához SQL sysadmin engedély szükséges. A piactéren létrehozott összes SQL Server virtuális gép a SqlIaaSExtension van telepítve. A AzureBackupWindowsWorkload bővítmény a SQLIaaSExtension használatával automatikusan beolvassa a szükséges engedélyeket.
    * Ha nem hozta létre a virtuális gépet a piactéren, vagy ha SQL 2008-es és 2008 R2-es verzióban van, előfordulhat, hogy a virtuális gép nem rendelkezik a SqlIaaSExtension, és a felderítési művelet meghiúsul, és a UserErrorSQLNoSysAdminMembership hibaüzenet jelenik meg. A probléma megoldásához kövesse a [virtuális gép engedélyeinek beállítása](backup-azure-sql-database.md#set-vm-permissions)című témakör utasításait.

        ![Válassza ki a virtuális gépet és az adatbázist](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

1. A **biztonsági mentési cél** > **2. lépés: a biztonsági mentés konfigurálása**lapon válassza a **biztonsági mentés konfigurálása**lehetőséget.

   ![Válassza a biztonsági mentés konfigurálása lehetőséget.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. A **biztonsági mentésre kijelölt elemek lapon**megjelenik a regisztrált rendelkezésre állási csoportok és az önálló SQL Server példányok. A sor bal oldalán található nyílra kattintva bontsa ki az adott példányban vagy always on rendelkezésre állási csoportba tartozó nem védett adatbázisok listáját.  

    ![Az összes SQL Server-példány megjelenítése különálló adatbázisokkal](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Válassza ki az összes védelemmel ellátni kívánt adatbázist, majd kattintson **az OK gombra**.

   ![Az adatbázis védelme](./media/backup-azure-sql-database/select-database-to-protect.png)

   A biztonsági mentési terhelések optimalizálásához Azure Backup egy biztonsági mentési feladatban lévő adatbázisok maximális számát 50-re állítja.

     * Több mint 50 adatbázis védelméhez több biztonsági mentést is konfigurálhat.
     * A teljes példány vagy az Always On rendelkezésre állási csoport [engedélyezéséhez](#enable-auto-protection) az **AutoProtect** legördülő listában válassza **a**be lehetőséget, majd kattintson az **OK gombra**.

    > [!NOTE]
    > Az [automatikus védelem](#enable-auto-protection) funkció nem csak egyszerre engedélyezi a védelmet az összes meglévő adatbázison, de automatikusan védi az adott példányhoz vagy a rendelkezésre állási csoporthoz hozzáadott új adatbázisokat is.  

4. A **biztonsági mentési szabályzat**megnyitásához kattintson **az OK gombra** .

    ![Automatikus védelem engedélyezése az Always On rendelkezésre állási csoport számára](./media/backup-azure-sql-database/enable-auto-protection.png)

5. A **biztonsági mentési szabályzatban**válasszon ki egy házirendet, majd kattintson **az OK gombra**.

   * Válassza ki az alapértelmezett szabályzatot HourlyLogBackup.
   * Válassza ki az SQL-hez korábban létrehozott meglévő biztonsági mentési szabályzatot.
   * Adjon meg egy új szabályzatot a RPO és a megőrzési tartomány alapján.

     ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-sql-database/select-backup-policy.png)

6. A **biztonsági mentés**területen válassza a **biztonsági mentés engedélyezése**lehetőséget.

    ![A kiválasztott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

7. A konfigurációs folyamat nyomon követése a portál **értesítések** területén.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy mikor készüljön biztonsági mentés, és mennyi ideig őrzi meg a rendszer.

* A rendszer a tároló szintjén hozza létre a szabályzatot.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.
* Amikor létrehoz egy biztonsági mentési szabályzatot, a rendszer az alapértelmezett napi teljes biztonsági mentést.
* Hozzáadhat egy különbözeti biztonsági másolatot, de csak akkor, ha a teljes biztonsági mentés hetente történik.
* Ismerje meg a [biztonsági mentési szabályzatok különböző típusait](backup-architecture.md#sql-server-backup-types).

Biztonsági mentési szabályzat létrehozásához:

1. A tárolóban válassza a **biztonsági mentési szabályzatok** > **Hozzáadás**elemet.
2. A **Hozzáadás**területen válassza a SQL Server lehetőséget az **Azure-beli virtuális gépen** a szabályzat típusának meghatározásához.

   ![Válassza ki az új biztonsági mentési szabályzathoz tartozó szabályzat típusát](./media/backup-azure-sql-database/policy-type-details.png)

3. A **Házirend neve**mezőben adja meg az új szabályzat nevét.
4. A **teljes biztonsági mentési szabályzatban**válasszon ki egy **biztonsági mentési gyakoriságot**. Válassza a **naponta** vagy **hetente**lehetőséget.

   * **Napi szinten**válassza ki az órát és az időzónát a biztonsági mentési feladatok elkezdése után.
   * **Hetente**válassza ki a hét, óra és időzóna napját a biztonsági mentési feladatok elkezdése után.
   * Futtasson egy teljes biztonsági mentést, mert nem lehet kikapcsolni a **teljes biztonsági mentés** lehetőséget.
   * A szabályzat megtekintéséhez válassza a **teljes biztonsági mentés** lehetőséget.
   * A napi teljes biztonsági mentésekhez nem hozhat létre különbözeti biztonsági másolatokat.

     ![Új biztonsági mentési szabályzat mezői](./media/backup-azure-sql-database/full-backup-policy.png)  

5. A **megőrzési tartomány**területen az összes beállítás alapértelmezés szerint ki van választva. Törölje a nem kívánt adatmegőrzési tartomány korlátait, majd állítsa be a használandó intervallumokat.

    * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes, különbözeti és napló) hét nap.
    * A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
    * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a heti megőrzési beállítás alapján van megjelölve és megőrizve.
    * A havi és az éves adatmegőrzési tartományok hasonló módon viselkednek.

       ![Megőrzési tartomány intervallumának beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

6. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
7. A különbözeti biztonsági mentési szabályzat hozzáadásához válassza a **különbözeti biztonsági mentés**lehetőséget.

   ![megőrzési időtartam beállításai](./media/backup-azure-sql-database/retention-range-interval.png)
   ![nyissa meg a különbözeti biztonsági mentési házirend menüt](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához.

    * Naponta csak egy különbözeti biztonsági mentést lehet kiváltani.
    * A különbözeti biztonsági mentések legfeljebb 180 napig tárolhatók. A hosszú megőrzéshez használja a teljes biztonsági mentést.

9. Kattintson az **OK** gombra a szabályzat mentéséhez és a **biztonsági mentési szabályzat** fő menüjéhez való visszatéréshez.

10. Tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése**lehetőséget.
11. A **napló biztonsági mentése**területen válassza az **Engedélyezés**lehetőséget, majd állítsa be a gyakoriságot és a megőrzési vezérlőket. A naplók biztonsági mentései akár 15 percenként is előfordulhatnak, és akár 35 napig is megtartható.
12. Kattintson az **OK** gombra a szabályzat mentéséhez és a **biztonsági mentési szabályzat** fő menüjéhez való visszatéréshez.

    ![A napló biztonsági mentési szabályzatának szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. A **biztonsági mentési házirend** menüben válassza ki, hogy engedélyezi-e az **SQL biztonsági mentési tömörítést** , vagy sem. A beállítás alapértelmezés szerint le van tiltva. Ha engedélyezve van, SQL Server egy tömörített biztonsági mentési adatfolyamot küld a VDI-nek.  Vegye figyelembe, hogy Azure Backup felülbírálja a példány szintjének alapértelmezett értékét a TÖMÖRÍTÉSi/NO_COMPRESSION záradékkal a vezérlő értékétől függően.

14. Miután befejezte a módosításokat a biztonsági mentési szabályzatban, kattintson az **OK gombra**.

> [!NOTE]
> Minden naplózási biztonsági mentés az előző teljes biztonsági mentéshez van láncolva, amely helyreállítási láncot alkot. Ez a teljes biztonsági mentés a legutóbbi napló biztonsági mentésének lejárta után is megmarad. Ez azt jelentheti, hogy a teljes biztonsági mentést egy további időszakra vonatkozóan kell megőrizni, hogy az összes napló helyreállítható legyen. Tegyük fel, hogy a felhasználó heti teljes biztonsági mentést, napi különbözeti és 2 órás naplókat tartalmaz. Mindegyiket 30 napig őrzi meg a rendszer. A hetente megteltek azonban csak a következő teljes biztonsági mentés elérhetővé tételével, azaz 30 + 7 nap után törlődnek. Tegyük fel, hogy a heti teljes biztonsági mentés november 16-án történik. Az adatmegőrzési szabályzatnak megfelelően a december 16-i-ig meg kell őrizni. Az utolsó napló biztonsági mentése ehhez a teljes, november 22-én a következő beütemezett megtelte előtt történik. Amíg a napló nem érhető el a DEC 22nd-ig, a november 16-i teljes nem törölhető. Így a november 16-i Full megmarad december 22-én.

## <a name="enable-auto-protection"></a>Automatikus védelem engedélyezése  

Az automatikus védelem engedélyezésével automatikusan biztonsági másolatot készíthet az összes meglévő és jövőbeli adatbázisról egy önálló SQL Server-példányra vagy egy always on rendelkezésre állási csoportba.

* Egyszerre csak az automatikus védelemhez választható adatbázisok száma nem korlátozható.
* Az automatikus védelem engedélyezésekor nem lehet szelektív védelemmel ellátni vagy kizárni az adatbázisokat a védelemből egy példányon.
* Ha a példánya már tartalmaz néhány védett adatbázist, akkor az automatikus védelem bekapcsolása után is a megfelelő szabályzatok alatt maradnak. A később hozzáadott nem védett adatbázisok csak egyetlen szabályzattal rendelkeznek, amelyet az automatikus védelem engedélyezésekor a **biztonsági mentés beállítása**szakaszban talál. Az automatikusan védett adatbázishoz tartozó házirendet azonban később is módosíthatja.  

Az automatikus védelem engedélyezése:

  1. A **biztonsági mentéshez használt elemek**területen válassza ki azt a példányt, amelynek engedélyezni szeretné az automatikus védelmet.
  2. Válassza ki a legördülő listát az **AutoProtect**alatt, majd válassza **a be**lehetőséget, majd kattintson **az OK gombra**.

      ![Automatikus védelem engedélyezése a rendelkezésre állási csoportban](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A Backup minden adatbázishoz konfigurálva van, és nyomon követhető a **biztonsági mentési feladatokban**.

Ha le kell tiltania az automatikus védelmet, válassza ki a példány nevét a **biztonsági mentés konfigurálása**területen, majd válassza az automatikus **védelem letiltása** a példányhoz lehetőséget. A rendszer az összes adatbázist biztonsági mentést folytatja, de a jövőbeli adatbázisok nem lesznek automatikusan védve.

![Automatikus védelem letiltása az adott példányon](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Következő lépések

A webinárium témái:

* [SQL Server adatbázisok biztonsági másolatának visszaállítása](restore-sql-database-azure-vm.md)
* [Biztonsági másolatok SQL Server adatbázisok kezelése](manage-monitor-sql-database-backup.md)
