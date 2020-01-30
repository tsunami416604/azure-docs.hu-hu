---
title: SAP HANA-adatbázis biztonsági mentése az Azure-ba Azure Backup
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy SAP HANA-adatbázisról az Azure-beli virtuális gépekre a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: dd4c6fc0e018f3fc8f2a2029ef8a90cdc305e2c2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765514"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

SAP HANA adatbázisok olyan kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási célt (RPO) és hosszú távú adatmegőrzést igényelnek. Az Azure Virtual Machines (VM) rendszeren futó SAP HANA adatbázisok biztonsági mentését [Azure Backup](backup-overview.md)használatával végezheti el.

Ez a cikk az Azure-beli virtuális gépeken futó SAP HANA adatbázisok biztonsági mentését mutatja be egy Azure Backup Recovery Services-tárolón.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:
> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása
> * Igény szerinti biztonsági mentési feladatok futtatása

>[!NOTE]
>Az Azure-beli virtuális gépeken futó **SQL Server-kiszolgáló és az Azure** -beli virtuális gépekhez készült SAP HANA Soft delete már előzetes verzióban érhető el.<br>
>Az előzetes verzióra való feliratkozáshoz írjon nekünk a következő címen: AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis biztonsági mentéshez való beállításához tekintse meg az [Előfeltételek](tutorial-backup-sap-hana-db.md#prerequisites) és az [engedélyek beállítása](tutorial-backup-sap-hana-db.md#setting-up-permissions) szakaszt.

### <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

Az SAP HANA virtuális gépnek minden művelethez kapcsolódnia kell az Azure nyilvános IP-címeihez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági másolatok konfigurálása, biztonsági másolatok ütemezése, helyreállítási pontok visszaállítása stb.) nem működnek az Azure nyilvános IP-címekkel való kapcsolat nélkül.

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

**Http-proxykiszolgáló üzembe helyezése a forgalom irányításához**. Ha egy Azure-beli virtuális gépen SAP HANA adatbázisról készít biztonsági másolatot, a virtuális gépen futó biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat az Azure Storage-ba való Azure Backup és az adattároláshoz. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A bővítmények az egyetlen olyan összetevő, amely a nyilvános internethez való hozzáférésre van konfigurálva.

A csatlakozási lehetőségek a következő előnyökkel és hátrányokkal járnak:

**Beállítás** | **Előnyei** | **Hátrányai**
--- | --- | ---
IP-címtartományok engedélyezése | Nincs további költség | Összetett a kezeléshez, mert az IP-címtartományok változnak az idő múlásával <br/><br/> Hozzáférést biztosít az egész Azure-hoz, nem csak az Azure Storage-hoz
NSG szolgáltatásbeli címkék használata | A tartomány módosításainak könnyebb kezelése automatikusan történik <br/><br/> Nincs további költség <br/><br/> | Csak NSG használható <br/><br/> Hozzáférést biztosít a teljes szolgáltatáshoz
Azure Firewall FQDN-címkék használata | Könnyebben kezelhető, mert a szükséges teljes tartománynevek automatikusan kezelhetők | Csak Azure Firewall használható
HTTP-proxy használata | A proxy részletes vezérlője a tárolási URL-címeken keresztül engedélyezett <br/><br/> A virtuális gépekhez való internetes hozzáférés egyetlen pontja <br/><br/> Nem vonatkozik az Azure IP-címek változásai | További költségek egy virtuális gép futtatásához a proxy szoftverrel

## <a name="onboard-to-the-public-preview"></a>Bevezetés a nyilvános előzetes verzióba

A nyilvános előzetes verzióra az alábbiak szerint kell bejelentkezni:

* A portálon regisztrálja előfizetési AZONOSÍTÓját a Recovery Services szolgáltatónak a [jelen cikkben](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)leírtak szerint.
* A PowerShellben a "az" modul esetében futtassa ezt a parancsmagot. A műveletnek "regisztrálva" kell lennie.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* Ha a PowerShellben a "AzureRM" modult használja, futtassa ezt a parancsmagot. A műveletnek "regisztrálva" kell lennie.

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban **első lépések**kattintson a **biztonsági mentés**elemre. A **hol fut a**számítási feladat? területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**.
2. Kattintson a **felderítés indítása**gombra. Ez elindítja a nem védett Linux rendszerű virtuális gépek felderítését a tároló régiójában.

   * A felderítést követően a nem védett virtuális gépek a portálon jelennek meg a név és az erőforráscsoport alapján.
   * Ha a virtuális gép nem a várt módon van felsorolva, ellenőrizze, hogy már van-e biztonsági másolat egy tárolóban.
   * Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

3. A **Virtual Machines kiválasztása lapon**kattintson a hivatkozásra azon parancsfájl letöltéséhez, amely a Azure Backup szolgáltatás számára engedélyeket biztosít a SAP HANA virtuális gépek adatbázis-felderítéshez való hozzáféréséhez.
4. Futtassa a szkriptet minden olyan virtuális gépen, amely SAP HANA-adatbázisokat üzemeltet, amelyekről biztonsági másolatot szeretne készíteni.
5. Miután futtatta a szkriptet a virtuális gépeken, a **Virtual Machines kiválasztása**területen válassza ki a virtuális gépeket. Ezután kattintson az **adatbázisok felderítése**elemre.
6. Azure Backup a virtuális gépen lévő összes SAP HANA adatbázist felfedi. A felderítés során Azure Backup regisztrálja a virtuális gépet a tárolóban, és telepít egy bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázison.

    ![SAP HANA adatbázisok felderítése](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Most engedélyezze a biztonsági mentést.

1. A 2. lépésben kattintson a **biztonsági mentés konfigurálása**elemre.

    ![A Backup konfigurálása](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Az **elemek kijelölése biztonsági mentéshez**területen jelölje ki az összes védelemmel ellátni kívánt adatbázist > **az OK gombra**.

    ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/backup-azure-sap-hana-database/select-items.png)
3. A **biztonsági mentési szabályzat** > **válassza a biztonsági mentési szabályzat lehetőséget**, hozzon létre egy új biztonsági mentési szabályzatot az adatbázisokhoz az alábbi utasítások szerint.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-sap-hana-database/backup-policy.png)
4. A szabályzat létrehozása után a **biztonsági mentés** menüben kattintson a **biztonsági mentés engedélyezése**elemre.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-sap-hana-database/enable-backup.png)
5. A biztonsági mentési konfiguráció előrehaladásának nyomon követése a portál **értesítések** területén.

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza a biztonsági másolatok készítésének idejét, valamint azt, hogy mennyi ideig őrzi meg a rendszer.

* A rendszer a tároló szintjén hozza létre a szabályzatot.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.

A házirend-beállításokat a következőképpen adhatja meg:

1. A **Házirend neve**mezőben adja meg az új szabályzat nevét.

   ![Adja meg a szabályzat nevét](./media/backup-azure-sap-hana-database/policy-name.png)
2. A **teljes biztonsági mentési szabályzatban**válassza ki a **biztonsági mentés gyakoriságát**, és válassza a **naponta** vagy **hetente**lehetőséget.
   * **Napi**: válassza ki azt az órát és időzónát, amelyben a biztonsági mentési feladatok megkezdődnek.
       * Teljes biztonsági mentést kell futtatnia. Ezt a beállítást nem lehet kikapcsolni.
       * A szabályzat megtekintéséhez kattintson a **teljes biztonsági mentés** elemre.
       * A napi teljes biztonsági mentésekhez nem hozhat létre különbözeti biztonsági másolatokat.
   * **Hetente**: válassza ki a hét azon napját, óráját és időzónáját, amelyben a biztonsági mentési feladatot futtatja.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. A **megőrzési tartomány**területen konfigurálja a teljes biztonsági mentés megőrzési beállításait.
    * Alapértelmezés szerint minden beállítás ki van választva. Törölje az összes olyan megőrzési időtartamra vonatkozó korlátozást, amelyet nem kíván használni, és állítsa be azokat.
    * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) hét nap.
    * A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
    * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a beállítás alapján van megcímkézve és megtartva.
    * A havi és az éves adatmegőrzési tartomány hasonló módon viselkedik.

4. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. Válasszon különbözeti **biztonsági másolatot** a különbözeti szabályzat hozzáadásához.
6. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához.
    * A legtöbb esetben naponta egy különbözeti biztonsági mentést indíthat.
    * A különbözeti biztonsági mentések legfeljebb 180 napig tárolhatók. Ha nagyobb adatmegőrzésre van szüksége, teljes biztonsági mentést kell használnia.

    ![Különbözeti biztonsági mentési szabályzat](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > A növekményes biztonsági mentések jelenleg nem támogatottak.

7. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
8. A tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése** lehetőséget,
    * A **napló biztonsági mentése**területen válassza az **Engedélyezés**lehetőséget.  Ez nem tiltható le, mert SAP HANA kezeli az összes napló biztonsági mentését.
    * Állítsa be a gyakoriságot és a megőrzési vezérlőket.

    > [!NOTE]
    > A biztonsági másolatok naplózása csak a sikeres teljes biztonsági mentés befejezése után kezdi a folyamatot.

9. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
10. Miután befejezte a biztonsági mentési szabályzat definiálását, kattintson **az OK**gombra.

> [!NOTE]
> Minden naplózási biztonsági mentés az előző teljes biztonsági mentéshez van láncolva, amely helyreállítási láncot alkot. Ez a teljes biztonsági mentés a legutóbbi napló biztonsági mentésének lejárta után is megmarad. Ez azt jelentheti, hogy a teljes biztonsági mentést egy további időszakra vonatkozóan kell megőrizni, hogy az összes napló helyreállítható legyen. Tegyük fel, hogy a felhasználó heti teljes biztonsági mentést, napi különbözeti és 2 órás naplókat tartalmaz. Mindegyiket 30 napig őrzi meg a rendszer. A hetente megteltek azonban csak a következő teljes biztonsági mentés elérhetővé tételével, azaz 30 + 7 nap után törlődnek. Tegyük fel, hogy a heti teljes biztonsági mentés november 16-án történik. Az adatmegőrzési szabályzatnak megfelelően a december 16-i-ig meg kell őrizni. Az utolsó napló biztonsági mentése ehhez a teljes, november 22-én a következő beütemezett megtelte előtt történik. Amíg a napló nem érhető el a DEC 22nd-ig, a november 16-i teljes nem törölhető. Így a november 16-i Full megmarad december 22-én.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

A biztonsági mentések a szabályzat ütemezésével összhangban futnak. Az igény szerinti biztonsági mentést az alábbi módon futtathatja:

1. A tároló menüjében kattintson a **biztonsági másolati elemek elemre**.
2. A **biztonsági másolati elemek**területen válassza ki a SAP HANA adatbázist futtató virtuális gépet, majd kattintson a **biztonsági mentés**elemre.
3. A **biztonsági mentés most**a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ezután kattintson az **OK** gombra.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > a **biztonsági mentési feladatok** > **folyamatban**van. Az adatbázis méretétől függően a kezdeti biztonsági mentés hosszabb időt is igénybe vehet.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio Backup futtatása Azure Backup engedélyezve lévő adatbázison

Ha egy olyan adatbázis helyi biztonsági másolatát kívánja használni, amelyről biztonsági mentés készül Azure Backupsal, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Az állapot ellenõrzése SAP HANA Studio/pilótafülkében.
2. Tiltsa le a naplók biztonsági mentését, és állítsa a biztonsági mentési katalógust a fájlrendszerre a megfelelő adatbázishoz.
3. Ehhez kattintson duplán a **systemdb** > **konfiguráció** > válassza az **adatbázis** > **szűrő (napló)** lehetőséget.
4. A **enable_auto_log_backup** beállítása **nem**értékre.
5. **Log_backup_using_backint** beállítása **hamis**értékre.
6. Igény szerint készítsen teljes biztonsági mentést az adatbázisról.
7. Várjon, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
8. A korábbi beállítások visszaállítása az Azure-ba:
    * Állítsa a Enable_auto_log_backup **értéket igen**értékre.
    * A **log_backup_using_backint** beállítása **igaz**értékre.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
