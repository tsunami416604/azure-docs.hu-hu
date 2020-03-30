---
title: SQL Server-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági másolatot az Azure virtuális gépeken lévő SQL Server-adatbázisokról az Azure Backup segítségével.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5b10907738feeecbec06669175e82578f2915f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273331"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>SQL Server-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

Az SQL Server-adatbázisok olyan kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási pont célkitűzést (RPO) és hosszú távú megőrzést igényelnek. Az Azure biztonsági másolat szolgáltatásával biztonsági másolatot készíthet az Azure virtuális gépeken (VM-eken) futó SQL [Server-adatbázisokról.](backup-overview.md)

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot egy Azure-alapú virtuális rendszeren futó SQL Server-adatbázisról egy Azure Backup Recovery Services-tárolóba.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Hozzon létre és konfiguráljon egy tárolót.
> * Fedezze fel az adatbázisokat, és állítson be biztonsági másolatokat.
> * Állítsa be az adatbázisok automatikus védelmét.

>[!NOTE]
>**Az Azure Virtuális gépben az SQL-kiszolgáló** előzetes törlése már előzetes verzióban érhető el az SAP HANA ideiglenes törlése az Azure virtuális gép számítási feladatában.<br>
>Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Előfeltételek

Az SQL Server-adatbázis biztonsági mentése előtt ellenőrizze az alábbi feltételeket:

1. Azonosítsa vagy hozzon létre egy [Recovery Services-tárolót](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ugyanabban a régióban és előfizetéssel, mint az SQL Server-példányt üzemeltető virtuális gép.
2. Ellenőrizze, hogy a virtuális gép rendelkezik-e [hálózati kapcsolattal.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
3. Győződjön meg arról, hogy az SQL Server-adatbázisok megfelelnek az [Azure Backup adatbázis-elnevezési irányelveinek.](#database-naming-guidelines-for-azure-backup)
4. Ellenőrizze, hogy nincs-e más biztonsági mentési megoldás engedélyezve az adatbázishoz. Az adatbázis biztonsági mentése előtt tiltsa le az összes többi SQL Server biztonsági mentést.

> [!NOTE]
> Engedélyezheti az Azure Backup egy Azure virtuális gép, valamint egy SQL Server-adatbázis fut a virtuális gép ütközés nélkül.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létrehozása

Az SQL Server virtuális gép minden művelethez szükséges az Azure nyilvános IP-címeivel való kapcsolat. Virtuális gép műveletek (adatbázis-felderítés, biztonsági mentések konfigurálása, ütemezése biztonsági mentések, helyreállítási pontok visszaállítása és így tovább) nem sikerül az Azure nyilvános IP-címek hez való csatlakozás nélkül.

Hozzon létre kapcsolatot az alábbi lehetőségek egyikével:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Az Azure adatközpont IP-tartományai engedélyezése

Ez a beállítás lehetővé teszi a letöltött fájl [IP-tartományait.](https://www.microsoft.com/download/details.aspx?id=41653) Hálózati biztonsági csoport (NSG) eléréséhez használja a Set-AzureNetworkSecurityRule parancsmag. Ha a biztonságos címzettek listája csak régióspecifikus IP-címeket tartalmaz, a hitelesítés engedélyezéséhez frissítenie kell a biztonságos címzettek listáját is az Azure Active Directory (Azure AD) szolgáltatáscímkéjében.

#### <a name="allow-access-using-nsg-tags"></a>Hozzáférés engedélyezése NSG-címkék használatával

Ha nsg használatával korlátozza a kapcsolatot, majd az AzureBackup szolgáltatáscímkét kell használnia az Azure Backup kimenő elérésének lehetővé. Emellett az Azure AD és az Azure Storage [szabályainak](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) használatával is engedélyeznie kell a hitelesítéshez és az adatátvitelhez szükséges kapcsolatot. Ez az Azure Portalon vagy a PowerShellen keresztül végezhető el.

Szabály létrehozása a portál használatával:

  1. A **Minden szolgáltatás területen**nyissa meg a Hálózati biztonsági csoportok **lehetőséget,** és jelölje ki a hálózati biztonsági csoportot.
  2. A Beállítások csoportban válassza **a Kimenő biztonsági szabályok** **lehetőséget.**
  3. Válassza a **Hozzáadás** lehetőséget. Adja meg az új szabály létrehozásához szükséges összes részletet a [biztonsági szabály beállításaiban](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)leírtak szerint. Győződjön meg arról, hogy a **Cél lehetőség** **szolgáltatáscímke** és **a célszolgáltatás címke** van beállítva **AzureBackup.**
  4. Kattintson a **Hozzáadás**gombra az újonnan létrehozott kimenő biztonsági szabály mentéséhez.

Szabály létrehozása a PowerShell használatával:

 1. Azure-fiók hitelesítő adatainak hozzáadása és a nemzeti felhők frissítése<br/>
      `Add-AzureRmAccount`<br/>

 2. Válassza ki az NSG-előfizetést<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Az NSG kiválasztása<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Kimenő engedélyezési szabály hozzáadása az Azure Backup szolgáltatáscímkéjéhez<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Kimenő engedélyezési szabály hozzáadása a Storage szolgáltatáscímkéhez<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Kimenő engedélyezési szabály hozzáadása az AzureActiveDirectory szolgáltatáscímkéhez<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Az NSG mentése<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Hozzáférés engedélyezése az Azure Firewall-címkék használatával.** Ha Az Azure Firewall, hozzon létre egy alkalmazásszabály segítségével az AzureBackup [FQDN címke.](https://docs.microsoft.com/azure/firewall/fqdn-tags) Ez lehetővé teszi a kimenő hozzáférést az Azure Backup.

**HTTP-proxykiszolgáló telepítése a forgalom irányításához.** Amikor egy Azure-beli virtuális gépen biztonsági másolatot készít egy SQL Server-adatbázisról, a virtuális gép biztonsági mentési bővítménye a HTTPS API-k használatával küldfelügyeleti parancsokat az Azure Backup nak és az adatokat az Azure Storage-nak. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa át a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. Nincsenek helyettesítő tartományok az Azure Backup használatával a proxyszabályok engedélyezési listájához való hozzáadáshoz. Az Azure által nyújtott szolgáltatásokhoz a nyilvános IP-címtartományokat kell használnia. A bővítmények az egyetlen olyan összetevő, amely a nyilvános internet eléréséhez van konfigurálva.

A csatlakozási lehetőségek a következő előnyöket és hátrányokat tartalmazzák:

**Beállítás** | **Előnyök** | **Hátrányok**
--- | --- | ---
IP-tartományok engedélyezése | Nincsenek további költségek | Kezelm a kezelni meddő, mert az IP-címtartományok idővel változnak <br/><br/> Hozzáférést biztosít az Azure egészéhez, nem csak az Azure Storage-hoz
NSG-szolgáltatáscímkék használata | Könnyebben kezelhető a tartományváltozások automatikus egyesítése <br/><br/> Nincsenek további költségek <br/><br/> | Csak NSG-kkel használható <br/><br/> Hozzáférést biztosít a teljes szolgáltatáshoz
Az Azure Firewall FQDN-címkéinek használata | Könnyebben kezelhető, mivel a szükséges teljes tartománynokat automatikusan kezeli a rendszer | Csak az Azure Firewall szolgáltatással használható
HTTP-proxy használata | A virtuális gépek internet-hozzáférésének egypontos pontja <br/> | A virtuális gép proxyszoftverrel való futtatásának további költségei <br/> Nincsenek közzétett FQDN-címek, az engedélyezési szabályok az Azure IP-cím változásainak függvényei

#### <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Az Azure Backup adatbázis-elnevezési irányelvei

Ne használja a következő elemeket az adatbázisnevekben:

* Záró és vezető terek
* Záró felkiáltójelek (!)
* Szögletes zárójelek bezárása (])
* Pontosvessző ';'
* Perjel '/'

Az aliasok nem támogatott karakterek esetén elérhetők, de azt javasoljuk, hogy kerüljék el őket. További információt a [Table Service adatmodelljét ismertető](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) témakörben talál.

>[!NOTE]
>A nevükben "+" vagy "&" speciális karakterekkel rendelkező adatbázisok **védelmi konfigurálása** művelete nem támogatott. Módosíthatja az adatbázis nevét, vagy engedélyezheti az **automatikus védelmet,** amely sikeresen védheti ezeket az adatbázisokat.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-adatbázisok felderítése

A virtuális számítógépen futó adatbázisok felderítése:

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a Recovery Services-tárolót, amelyet az adatbázis biztonsági mentéséhez használ.

2. A **Recovery Services-tároló** irányítópultján válassza a **Biztonsági másolat lehetőséget.**

   ![A Biztonsági másolat kiválasztása parancs a Biztonsági másolat menü megnyitásához](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **Biztonsági másolat célja beállításban**adja meg, hogy hol fut a számítási **feladatok?** **Azure**

4. A **Mit szeretne biztonsági másolatot készíteni,** válassza az SQL Server az Azure VM.In What you want to backup ( Mit szeretne biztonsági másolatot készíteni ) lehetőséget **az SQL Server az Azure VM-ben**elemre.

    ![Válassza ki az SQL Server t az Azure VM-ben a biztonsági mentéshez](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **biztonsági mentési cél** > **felderítése a virtuális gépeken**a **Felderítés indítása** lehetőséget választja a nem védett virtuális gépek kereséséhez az előfizetésben. Ez a keresés eltarthat egy ideig, attól függően, hogy hány nem védett virtuális gépek az előfizetésben.

   * A nem védett virtuális gépeknek a felderítés után meg kell jelenniük a listában, név és erőforráscsoport szerint felsorolva.
   * Ha egy virtuális gép nem szerepel a várt módon, tekintse meg, hogy már biztonsági mentést egy tárolóban.
   * Több virtuális gép is rendelkezik ugyanazzal a névvel, de különböző erőforráscsoportokhoz fognak tartozni.

     ![A biztonsági mentés a virtuális gépeken lévő DB-k keresése közben függőben van](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gép listában válassza ki az SQL Server adatbázist futtató virtuális gép > **Fedezze fel a DB-ket.**

7. Az adatbázis-felderítés nyomon követése az **Értesítések területen.** A művelethez szükséges idő a virtuálisgép-adatbázisok számától függ. A kijelölt adatbázisok felderítésekénegy sikeres üzenet jelenik meg.

    ![A telepítés sikeressé tévő üzenete](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Az Azure Backup felderíti a virtuális gép összes SQL Server-adatbázisát. A felderítés során a következő elemek fordulnak elő a háttérben:

    * Az Azure Backup regisztrálja a virtuális gépet a tárolóval a számítási feladatok biztonsági mentéséhez. A regisztrált virtuális gép összes adatbázisa csak erre a tárolóra mentbiztonsági másolatot.
    * Az Azure Backup telepíti az AzureBackupWindowsWorkload bővítményt a virtuális gépre. Nincs ügynök telepítve egy SQL-adatbázisra.
    * Az Azure Backup létrehozza az NT Service\AzureWLBackupPluginSvc szolgáltatásfiókot a virtuális gépen.
      * Minden biztonsági mentési és visszaállítási művelet a szolgáltatásfiókot használja.
      * Az NT-szolgáltatás\Az AzureWLBackupPluginSvc sql sysadmin engedélyeket igényel. A Marketplace-en létrehozott összes SQL Server virtuális gép hez telepítve van az SqlIaaSExtension. Az AzureBackupWindowsWorkload bővítmény az SQLIaaSExtension használatával automatikusan megkapja a szükséges engedélyeket.
    * Ha nem a Piactérről hozták létre a virtuális gép, vagy ha sql 2008 és 2008 R2, a virtuális gép nem lehet az SqlIaaSExtension telepítve, és a felderítési művelet sikertelen a usererrorSQLNoSysAdminMembership hibaüzenettel. A probléma megoldásához kövesse a [Virtuálisgép-engedélyek beállítása](backup-azure-sql-database.md#set-vm-permissions)című útmutató utasításait.

        ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

1. A Biztonsági másolat **Configure Backup** **2.** > **Step 2: Configure Backup**

   ![Válassza a Biztonsági másolat konfigurálása lehetőséget](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. A **Biztonsági másolathoz szükséges elemek kiválasztása**területen megjelenik az összes regisztrált rendelkezésre állási csoport és önálló SQL Server-példány. A sortól balra lévő nyíllal bontsa ki az adott példány összes nem védett adatbázisának vagy az Always On availability csoportnak a listáját.  

    ![Az összes SQL Server-példány megjelenítése önálló adatbázisokkal](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Válassza ki a védeni kívánt összes adatbázist, majd kattintson az **OK gombra.**

   ![Az adatbázis védelme](./media/backup-azure-sql-database/select-database-to-protect.png)

   A biztonsági mentési terhelésoptimalizáláshoz az Azure Backup egy biztonsági mentési feladatban legfeljebb számú adatbázist állít be 50-re.

     * Több mint 50 adatbázis védelme érdekében konfiguráljon több biztonsági másolatot.
     * A teljes példány vagy a Mindig bekapcsolva beállítás csoport [engedélyezéséhez](#enable-auto-protection) az **AUTOPROTECT** legördülő listában válassza a **BE**lehetőséget, majd az **OK**gombot.

    > [!NOTE]
    > Az [automatikus védelem](#enable-auto-protection) szolgáltatás nem csak lehetővé teszi a védelmet az összes meglévő adatbázisok egyszerre, hanem automatikusan védi az adott példányhoz vagy a rendelkezésre állási csoporthoz hozzáadott új adatbázisokat.  

4. A Biztonsági másolat házirend megnyitásához válassza az **OK** **gombot.**

    ![Automatikus védelem engedélyezése a Mindig bekapcsolva álláscsoportszámára](./media/backup-azure-sql-database/enable-auto-protection.png)

5. A **Biztonsági másolat házirendben**válasszon egy házirendet, majd kattintson **az OK gombra.**

   * Válassza ki az alapértelmezett házirendet HourlyLogBackup néven.
   * Válasszon egy korábban az SQL-hez létrehozott biztonsági mentési házirendet.
   * Új házirend definiálása az RPO és a megőrzési tartomány alapján.

     ![Válassza a Biztonsági mentési házirend lehetőséget](./media/backup-azure-sql-database/select-backup-policy.png)

6. A **Biztonsági másolat segédprogramban**válassza **a Biztonsági másolat engedélyezése**lehetőséget.

    ![A kiválasztott biztonsági mentési házirend engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

7. A konfigurációs folyamat nyomon követése a portál **Értesítések** területén.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása

A biztonsági mentési szabályzat határozza meg, hogy mikor és mennyi ideig őrzi meg a biztonsági mentéseket.

* A szabályzat a tároló szintjén jön létre.
* Több tárolóhasználhatja ugyanazt a biztonsági mentési szabályzatot, de minden tárolóra alkalmaznia kell a biztonsági mentési szabályzatot.
* Biztonsági mentési házirend létrehozásakor a napi teljes biztonsági mentés az alapértelmezett.
* Hozzáadhat egy különbözeti biztonsági mentést, de csak akkor, ha a teljes biztonsági mentést hetente konfigurálja.
* További információ [a biztonsági mentési házirendek különböző típusairól.](backup-architecture.md#sql-server-backup-types)

Biztonsági mentési házirend létrehozása:

1. A tárolóban válassza a **Biztonsági mentési házirendek** > **hozzáadása lehetőséget.**
2. A **hozzáadás területen**válassza **az SQL Server az Azure VM-ben** a szabályzat típusának meghatározásához.

   ![Az új biztonsági mentési házirend házirendtípusának kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

3. A **Házirend neve**mezőbe írja be az új házirend nevét.
4. A **Teljes biztonsági mentés házirendben**válassza ki a Biztonsági másolat **gyakoriságát**. Válassza a **Napi** vagy **a Heti**lehetőséget.

   * A **Napi**területen válassza ki azt az órát és időzónát, amikor a biztonsági mentési feladat elkezdődik.
   * A **Heti**lehetőséget válassza ki a hét, az óra és az időzóna napját, amikor a biztonsági mentési feladat megkezdődik.
   * Futtasson teljes biztonsági másolatot, mert nem tudja kikapcsolni a **Teljes biztonsági mentés** beállítást.
   * A házirend megtekintéséhez válassza a **Teljes biztonsági mentés** lehetőséget.
   * A napi teljes biztonsági mentések nem hozhatnak létre különbözeti biztonsági mentéseket.

     ![Új biztonságimentési házirend-mezők](./media/backup-azure-sql-database/full-backup-policy.png)  

5. A **MEGŐRZÉSI TARTOMÁNY mezőben**alapértelmezés szerint az összes beállítás be van jelölve. Törölje a nem kívánt adatmegőrzési tartomány korlátokat, majd állítsa be a használni kívánt időközöket.

    * Minimális megőrzési idő bármely típusú biztonsági mentés (teljes, különbözeti és napló) hét nap.
    * A helyreállítási pontok a megőrzési tartományuk alapján vannak címkézve megőrzésre. Ha például kiválaszt egy napi teljes biztonsági mentést, naponta csak egy teljes biztonsági mentés aktiválódik.
    * Egy adott nap biztonsági mentése címkézve van, és a heti megőrzési tartomány és a heti megőrzési beállítás alapján megmarad.
    * A havi és éves megőrzési tartományok hasonló módon viselkednek.

       ![Adatmegőrzési tartomány időköze beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

6. A **Teljes biztonsági mentés házirend** menüben válassza az **OK** gombot a beállítások elfogadásához.
7. Különbözeti biztonsági mentési házirend hozzáadásához válassza a **Különbözeti biztonsági mentés lehetőséget.**

   ![Adatmegőrzési](./media/backup-azure-sql-database/retention-range-interval.png)
   ![tartomány időközebeállításai A különbözeti biztonsági mentési házirend menüjének megnyitása](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **Különbözeti biztonsági mentés házirendben**válassza az **Engedélyezés** lehetőséget a gyakorisági és megőrzési vezérlők megnyitásához.

    * Naponta csak egy különbözeti biztonsági mentést indíthat el.
    * A különbözeti biztonsági mentések legfeljebb 180 napig őrizhetők meg. Hosszabb megőrzési, teljes biztonsági mentést.

9. Válassza az **OK gombot** a házirend mentéséhez és a **Biztonsági másolat főházirend-menüjéhez** való visszatéréshez.

10. Tranzakciós napló biztonsági mentési házirendjének hozzáadásához válassza **a Biztonsági másolat naplózása**lehetőséget.
11. A **Napló biztonsági másolat a**mezőben válassza az **Engedélyezés**lehetőséget, majd állítsa be a gyakorisági és megőrzési vezérlőket. A naplóbiztonsági mentések 15 percenként is előfordulhatnak, és legfeljebb 35 napig őrizhetők meg.
12. Válassza az **OK gombot** a házirend mentéséhez és a **Biztonsági másolat főházirend-menüjéhez** való visszatéréshez.

    ![A naplóbiztonsági házirend szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. A **Biztonsági másolat házirend** menüjében adja meg, hogy engedélyezi-e **az SQL biztonsági mentés tömörítését.** A beállítás alapértelmezés szerint le van tiltva. Ha engedélyezve van, az SQL Server tömörített biztonsági mentési adatfolyamot küld a VDI-nek.  Vegye figyelembe, hogy az Azure Backup felülbírálja a példányszint alapértelmezett beállításait a COMPRESSION / NO_COMPRESSION záradékkal, a vezérlő értékétől függően.

14. Miután befejezte a biztonsági mentési házirend szerkesztését, válassza az **OK gombot.**

> [!NOTE]
> Minden naplóbiztonsági mentés az előző teljes biztonsági mentéshez van láncolva, hogy helyreállítási láncot hozzon létre. Ez a teljes biztonsági mentés megmarad, amíg az utolsó naplóbiztonsági mentés megőrzése le nem jár. Ez azt jelentheti, hogy a teljes biztonsági mentés megmarad egy további ideig, hogy az összes naplók helyreáll.This might mean that the full backup is retained for a extra period to make sure all the logs can be rerere. Tegyük fel, hogy a felhasználó heti teljes biztonsági mentést, napi különbözetet és 2 órás naplókat tartalmaz. Mindegyiket 30 napig őrzik. De, a heti teli lehet igazán tisztított megjelöl/ töröl egyetlen után a következő teli hát van elérhető i.e., után 30 + 7 napok. Mondjuk, a heti teljes biztonsági mentés történik november 16-án. A megőrzési szabály szerint december 16-ig meg kell őrizni. Az utolsó napló biztonsági mentés a teljes történik, mielőtt a következő tervezett teljes, november 22.. Amíg ez a napló december 22-ig nem érhető el, a november 16-i teljes nem törölhető. Tehát a november 16-i teljes marad december 22-ig.

## <a name="enable-auto-protection"></a>Automatikus védelem engedélyezése  

Engedélyezheti az automatikus védelmet, hogy automatikusan biztonsági másolatot tartson az összes meglévő és jövőbeli adatbázisról egy önálló SQL Server-példányra vagy egy Mindig bekapcsolva állási csoportra.

* Nincs korlátozva, hogy egyszerre hány adatbázist választhat ki automatikus védelemre.
* Az automatikus védelem engedélyezésekor nem védheti vagy zárhatja ki az adatbázisokat a védelemből egy példányban.
* Ha a példány már tartalmaz néhány védett adatbázist, azok az automatikus védelem bekapcsolása után is védettek maradnak a megfelelő szabályzatuk szerint. A később hozzáadott nem védett adatbázisok csak egyetlen házirenddel rendelkeznek, amelyet az automatikus védelem engedélyezésekor határoz meg, a **Biztonsági másolat konfigurálása**csoportban. Később azonban módosíthatja az automatikusan védett adatbázishoz társított házirendet.  

Az automatikus védelem engedélyezése:

  1. A **Biztonsági másolatkészítésre váró elemek**mezőben válassza ki azt a példányt, amelynek engedélyezni szeretné az automatikus védelmet.
  2. Válassza az **AUTOPROTECT (AUTOPROTECT**) legördülő listát, válassza **a BE**lehetőséget, majd kattintson az **OK gombra.**

      ![Automatikus védelem engedélyezése a rendelkezésre állási csoportban](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A biztonsági mentés az összes adatbázishoz együtt van konfigurálva, és nyomon követhető a **Biztonsági másolat készítési feladatok ban.**

Ha le kell tiltania az automatikus védelmet, jelölje ki a példány nevét a **Biztonsági másolat konfigurálása**csoportban, majd válassza az **Automatikus védelem letiltása lehetőséget** a példányhoz. Az összes adatbázisról továbbra is készül biztonsági másolatot, de a jövőbeli adatbázisok nem lesznek automatikusan védve.

![Az automatikus védelem letiltása az adott példányon](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:

* [Biztonsági másolatot készítő SQL Server-adatbázisok visszaállítása](restore-sql-database-azure-vm.md)
* [Biztonsági másolatot készítő SQL Server-adatbázisok kezelése](manage-monitor-sql-database-backup.md)
