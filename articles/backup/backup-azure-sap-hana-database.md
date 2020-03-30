---
title: Készítsen biztonsági másolatot egy SAP HANA-adatbázisról az Azure-ba az Azure biztonsági mentén
description: Ebben a cikkben megtudhatja, hogyan készíthet biztonsági másolatot egy SAP HANA-adatbázisról az Azure biztonsági mentési szolgáltatással az Azure-alapú virtuális gépekre.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248059"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

AZ SAP HANA-adatbázisok kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási pont célkitűzést (RPO) és hosszú távú megőrzést igényelnek. Az Azure biztonsági [másolat](backup-overview.md)szolgáltatásával biztonsági másolatot készíthet az Azure virtuális gépeken (VM-eken) futó SAP HANA-adatbázisokról.

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóba.

Ebben a cikkben megtudhatja, hogyan:
> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása
> * Igény szerinti biztonsági mentési feladat futtatása

>[!NOTE]
>**Az Azure Virtuális gépben az SQL-kiszolgáló** előzetes törlése már előzetes verzióban érhető el az SAP HANA ideiglenes törlése az Azure virtuális gép számítási feladatában.<br>
>Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Előfeltételek

Tekintse meg az [előfeltételeket,](tutorial-backup-sap-hana-db.md#prerequisites) és [a Mi az előzetes regisztrációs parancsfájl nem](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) szakaszok az adatbázis beállítása a biztonsági mentés.

### <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

Az SAP HANA virtuális gép minden művelethez szükséges az Azure nyilvános IP-címeivel való kapcsolat. Virtuális gép műveletek (adatbázis-felderítés, biztonsági mentések konfigurálása, ütemezése biztonsági mentések, helyreállítási pontok visszaállítása és így tovább) nem sikerül az Azure nyilvános IP-címek hez való csatlakozás nélkül.

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

**HTTP-proxykiszolgáló telepítése a forgalom irányításához.** Amikor egy SAP HANA-adatbázis egy Azure virtuális gép, a biztonsági mentési bővítmény a virtuális gép a HTTPS API-k at az Azure Backup és az adatok azure storage-ba küldéséhez felügyeleti parancsokat. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa át a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A bővítmények az egyetlen olyan összetevő, amely a nyilvános internet eléréséhez van konfigurálva.

A csatlakozási lehetőségek a következő előnyöket és hátrányokat tartalmazzák:

**Beállítás** | **Előnyök** | **Hátrányok**
--- | --- | ---
IP-tartományok engedélyezése | Nincsenek további költségek | Kezelm a kezelni meddő, mert az IP-címtartományok idővel változnak <br/><br/> Hozzáférést biztosít az Azure egészéhez, nem csak az Azure Storage-hoz
NSG-szolgáltatáscímkék használata | Könnyebben kezelhető a tartományváltozások automatikus egyesítése <br/><br/> Nincsenek további költségek <br/><br/> | Csak NSG-kkel használható <br/><br/> Hozzáférést biztosít a teljes szolgáltatáshoz
Az Azure Firewall FQDN-címkéinek használata | Könnyebben kezelhető, mivel a szükséges teljes tartománynokat automatikusan kezeli a rendszer | Csak az Azure Firewall szolgáltatással használható
HTTP-proxy használata | A tároló URL-címének a proxyban lévő részletes vezérlés engedélyezett <br/><br/> A virtuális gépek internet-hozzáférésének egypontos pontja <br/><br/> Nem vonatkoznak az Azure IP-címváltozásai | A virtuális gép proxyszoftverrel való futtatásának további költségei

#### <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Az adatbázisok felfedezése

1. A tárolóelső **lépések**területén kattintson a **Biztonsági másolat gombra.** A **Hol fut a számítási feladatok?** válassza ki az SAP **HANA az Azure virtuális gép.**
2. Kattintson **a Felderítés indítása gombra.** Ez elindítja a nem védett Linux-virtuális gépek felderítését a tároló régióban.

   * A felderítés után a nem védett virtuális gépek megjelennek a portálon, név és erőforráscsoport szerint listázva.
   * Ha egy virtuális gép nem szerepel a várt módon, ellenőrizze, hogy már biztonsági mentést egy tárolóban.
   * Több virtuális gép is rendelkezhet ugyanazzal a névvel, de különböző erőforráscsoportokhoz tartoznak.

3. A Válassza ki a **virtuális gépek,** kattintson a hivatkozásra, hogy töltse le a parancsfájlt, amely engedélyeket biztosít az Azure Backup szolgáltatás eléréséhez az SAP HANA virtuális gépek adatbázis-felderítéshez.
4. Futtassa a parancsfájlt minden sap HANA-adatbázisokat üzemeltető virtuális gépeken, amelyekről biztonsági másolatot szeretne.
5. Miután futtatta a parancsfájlt a virtuális gépeken, válassza ki a **virtuális gépeket.** Ezután kattintson **a DB-ek felfedezése gombra.**
6. Az Azure Backup felderíti az összes SAP HANA-adatbázist a virtuális gépen. A felderítés során az Azure Backup regisztrálja a virtuális gépet a tárolóval, és telepíti a bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázisra.

    ![SAP HANA-adatbázisok felderítése](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Most engedélyezze a biztonsági mentést.

1. A **2.**

    ![Biztonsági mentés konfigurálása](./media/backup-azure-sap-hana-database/configure-backup.png)
2. A **Biztonsági másolatot tartalmazó elemek kiválasztása**csoportban jelölje ki a védeni kívánt összes adatbázist > **OK**.

    ![A biztonsági másolatot adó elemek kijelölése](./media/backup-azure-sap-hana-database/select-items.png)
3. A **Biztonsági másolat házirend:** > **Biztonsági mentési házirend kiválasztása**területen hozzon létre egy új biztonsági mentési házirendet az adatbázisokhoz az alábbi utasításoknak megfelelően.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-sap-hana-database/backup-policy.png)
4. A házirend létrehozása után kattintson a **Biztonsági másolat** menü Biztonsági másolat **engedélyezése parancsára.**

    ![Biztonsági mentés engedélyezése](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Kövesse nyomon a biztonsági mentés konfigurációjának előrehaladását a portál **Értesítések** területén.

### <a name="create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása

A biztonsági mentési szabályzat határozza meg, hogy mikor kell biztonsági mentéseket készíteni, és mennyi ideig őrzi meg őket.

* A szabályzat a tároló szintjén jön létre.
* Több tárolóhasználhatja ugyanazt a biztonsági mentési szabályzatot, de minden tárolóra alkalmaznia kell a biztonsági mentési szabályzatot.

Adja meg a házirend-beállításokat az alábbiak szerint:

1. A **Házirend neve**mezőbe írja be az új házirend nevét.

   ![Házirend nevének megadása](./media/backup-azure-sap-hana-database/policy-name.png)
2. A **Teljes biztonsági mentés házirendben**válassza a Biztonsági mentés **gyakoriságát,** és válassza a **Napi** vagy **heti**lehetőséget.
   * **Napi**: Válassza ki azt az órát és időzónát, amelyben a biztonsági mentési feladat megkezdődik.
       * Teljes biztonsági mentést kell futtatnia. Ez a beállítás nem kapcsolható ki.
       * A házirend megtekintéséhez kattintson a **Teljes biztonsági másolat** gombra.
       * A napi teljes biztonsági mentések nem hozhatnak létre különbözeti biztonsági mentéseket.
   * **Heti**: Válassza ki a hét napját, az órát és az időzónát, amelyben a biztonsági mentési feladat fut.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Az **Adatmegőrzési tartomány ban**konfigurálja a teljes biztonsági mentés adatmegőrzési beállításait.
    * Alapértelmezés szerint minden beállítás ki van jelölve. Törölje a megőrzési tartomány nem használni kívánt, és állítsa be azokat, amelyeket csinál.
    * A minimális megőrzési idő bármely típusú biztonsági mentés (teljes/különbözeti/log) hét nap.
    * A helyreállítási pontok a megőrzési tartományuk alapján vannak címkézve megőrzésre. Ha például kiválaszt egy napi teljes biztonsági mentést, naponta csak egy teljes biztonsági mentés aktiválódik.
    * Egy adott nap biztonsági mentése címkézve van, és a heti megőrzési tartomány és beállítás alapján megmarad.
    * A havi és éves megőrzési tartományok hasonlóan viselkednek.

4. A **Teljes biztonsági mentés házirend** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. A különbözeti házirend hozzáadásához válassza a **Különbözeti biztonsági mentés** lehetőséget.
6. A **Különbözeti biztonsági mentés házirendben**válassza az **Engedélyezés** lehetőséget a gyakorisági és megőrzési vezérlők megnyitásához.
    * Legtöbb, akkor kiváltó egy differenciál biztonsági mentés naponta.
    * A különbözeti biztonsági mentések legfeljebb 180 napig őrizhetők meg. Ha hosszabb megőrzésre van szüksége, teljes biztonsági mentést kell használnia.

    ![Különbözeti biztonsági mentési házirend](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > A növekményes biztonsági mentések jelenleg nem támogatottak.

7. Kattintson az **OK** gombra a házirend mentéséhez és a **Biztonsági másolat fő menüjéhez** való visszatéréshez.
8. A tranzakciós napló biztonsági mentési házirendjének hozzáadásához válassza a **Biztonsági másolat napló** kiválasztása lehetőséget.
    * A **Napló biztonsági másolat a mezőben**válassza az **Engedélyezés**lehetőséget.  Ez nem tiltható le, mert az SAP HANA kezeli az összes naplóbiztonsági mentést.
    * Állítsa be a gyakoriság- és megőrzési vezérlőket.

    > [!NOTE]
    > A naplóbiztonsági mentések csak a sikeres teljes biztonsági mentés befejezése után kezdenek el folyni.

9. Kattintson az **OK** gombra a házirend mentéséhez és a **Biztonsági másolat fő menüjéhez** való visszatéréshez.
10. Miután befejezte a biztonsági mentési házirend definiálását, kattintson az **OK**gombra.

> [!NOTE]
> Minden naplóbiztonsági mentés az előző teljes biztonsági mentéshez van láncolva, hogy helyreállítási láncot hozzon létre. Ez a teljes biztonsági mentés megmarad, amíg az utolsó naplóbiztonsági mentés megőrzése le nem jár. Ez azt jelentheti, hogy a teljes biztonsági mentés megmarad egy további ideig, hogy az összes naplók helyreáll.This might mean that the full backup is retained for a extra period to make sure all the logs can be rerere. Tegyük fel, hogy a felhasználó heti teljes biztonsági mentést, napi különbözetet és 2 órás naplókat tartalmaz. Mindegyiket 30 napig őrzik. De, a heti teli lehet igazán tisztított megjelöl/ töröl egyetlen után a következő teli hát van elérhető i.e., után 30 + 7 napok. Mondjuk, a heti teljes biztonsági mentés történik november 16-án. A megőrzési szabály szerint december 16-ig meg kell őrizni. Az utolsó napló biztonsági mentés a teljes történik, mielőtt a következő tervezett teljes, november 22.. Amíg ez a napló december 22-ig nem érhető el, a november 16-i teljes nem törölhető. Tehát a november 16-i teljes marad december 22-ig.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

A biztonsági mentések a házirend-ütemezésnek megfelelően futnak. Igény szerint futtathat biztonsági másolatot az alábbiak szerint:

1. A tároló menüjében kattintson a **Biztonsági másolat parancsra.**
2. A **Biztonsági másolat elemei ben**jelölje ki az SAP HANA-adatbázist futtató virtuális gépét, majd kattintson a Biztonsági mentés **gombra.**
3. A **Biztonsági másolat most**eszközben a naptár vezérlővel jelölje ki azt az utolsó napot, amikor a helyreállítási pontot meg kell őrizni. Ezt követően kattintson az **OK** gombra.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján figyelheti > folyamatban lévő **biztonsági mentési feladatok** > **.** Az adatbázis méretétől függően a kezdeti biztonsági mentés létrehozása eltarthat egy ideig.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>AZ SAP HANA Studio biztonsági mentésének futtatása olyan adatbázison, amelyen engedélyezve van az Azure Backup

Ha az Azure Backup szolgáltatással biztonsági másolat ban lévő adatbázis helyi biztonsági másolatát (a HANA Studio használatával) szeretné készíteni, tegye a következőket:

1. Várja meg, amíg az adatbázis befejeződik a teljes vagy naplós biztonsági mentésre. Ellenőrizze az SAP HANA Studio / Cockpit állapotát.
2. Tiltsa le a naplóbiztonsági mentéseket, és állítsa a biztonsági másolat katalógusát a megfelelő adatbázis fájlrendszerére.
3. Ehhez kattintson duplán **a systemdb** > **Configuration** > **Select adatbázisszűrőre** > **(napló)**.
4. Állítsa **enable_auto_log_backup** **Nem**beállításra.
5. A **log_backup_using_backint** értéke **Hamis**.
6. Igény szerinti teljes biztonsági mentést készítsen az adatbázisról.
7. Várja meg, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
8. Az előző beállítások visszaállítása az Azure-beállításokra:
    * A **enable_auto_log_backup** **beállítása Igen**.
    * Állítsa **log_backup_using_backint** **Igaz**értékre.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [állíthatja vissza az Azure virtuális gépeken futó SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan kezelheti az Azure Backup használatával biztonsági mentést készítő [SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
