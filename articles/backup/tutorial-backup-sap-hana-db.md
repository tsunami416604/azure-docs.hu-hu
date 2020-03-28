---
title: Oktatóanyag – AZ SAP HANA-adatbázisok biztonsági mentése az Azure virtuális gépein
description: Ebben az oktatóanyagban megtudhatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépen futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóba.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501452"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Oktatóanyag: Az SAP HANA-adatbázisok biztonsági mentése egy Azure virtuális gép

Ez az oktatóanyag bemutatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóba. Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása

[Itt](sap-hana-backup-support-matrix.md#scenario-support) van az összes forgatókönyv, amelyet jelenleg támogatunk.

## <a name="prerequisites"></a>Előfeltételek

A biztonsági mentések konfigurálása előtt győződjön meg arról, hogy a következőket teszi:

* Engedélyezze a kapcsolatot a virtuális gépről az internetre, hogy elérhesse az Azure-t, az alábbi [hálózati kapcsolatbeállítási](#set-up-network-connectivity) eljárásban leírtak szerint.
* A **hdbuserstore-ban** olyan kulcsnak kell léteznie, amely megfelel a következő feltételeknek:
  * Meg kell jelen lennie az alapértelmezett **hdbuserstore**
  * Az MDC esetében a kulcsnak a NAMESERVER SQL portjára kell **mutatnia.** Az SDC esetében az INDEXSERVER SQL portjára kell **mutatnia**
  * A felhasználók hozzáadásához és törléséhez hitelesítő adatokkal kell rendelkeznie
* Futtassa az SAP HANA biztonsági mentési konfigurációs parancsfájlt (előzetes regisztrációs parancsfájl) a virtuális gépen, ahol a HANA telepítve van, gyökérfelhasználóként. [Ez](https://aka.ms/scriptforpermsonhana) a parancsfájl lekéri a HANA rendszer készen áll a biztonsági mentésre. Tekintse meg a [Mi az előzetes regisztrációs parancsfájl nem](#what-the-pre-registration-script-does) szakaszt, hogy jobban megismerjék az előzetes regisztrációs parancsfájlt.

## <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

Az SAP HANA virtuális gép minden művelethez szükséges az Azure nyilvános IP-címeivel való kapcsolat. Virtuális gép műveletek (adatbázis-felderítés, biztonsági mentések konfigurálása, ütemezése biztonsági mentések, helyreállítási pontok visszaállítása és így tovább) nem sikerül az Azure nyilvános IP-címek hez való csatlakozás nélkül.

Hozzon létre kapcsolatot az alábbi lehetőségek egyikével:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Az Azure adatközpont IP-tartományai engedélyezése

Ez a beállítás lehetővé teszi a letöltött fájl [IP-tartományait.](https://www.microsoft.com/download/details.aspx?id=41653) Hálózati biztonsági csoport (NSG) eléréséhez használja a Set-AzureNetworkSecurityRule parancsmag. Ha a biztonságos címzettek listája csak régióspecifikus IP-címeket tartalmaz, a hitelesítés engedélyezéséhez frissítenie kell a biztonságos címzettek listáját is az Azure Active Directory (Azure AD) szolgáltatáscímkéjében.

### <a name="allow-access-using-nsg-tags"></a>Hozzáférés engedélyezése NSG-címkék használatával

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

## <a name="what-the-pre-registration-script-does"></a>Mit csinál az előzetes regisztrációs szkript?

Az előzetes regisztrációs parancsfájl futtatása a következő funkciókat hajtja végre:

* Telepíti vagy frissíti az Azure Backup ügynök által a terjesztési szükséges szükséges csomagokat.
* Kimenő hálózati kapcsolati ellenőrzéseket hajt végre az Azure Backup kiszolgálókkal és a függő szolgáltatásokkal, például az Azure Active Directoryval és az Azure Storage-tal.
* Az [előfeltételek](#prerequisites)részeként felsorolt felhasználói kulcs használatával jelentkezik be a HANA rendszerbe. A felhasználói kulcs segítségével hozzon létre egy biztonsági mentési felhasználó (AZUREWLBACKUPHANAUSER) a HANA rendszer és a felhasználói kulcs törölhető, miután az előzetes regisztrációs parancsfájl sikeresen fut.
* AZ AZUREWLBACKUPHANAUSER a következő szükséges szerepkörökhöz és engedélyekhez van rendelve:
  * DATABASE ADMIN (MDC esetén) és BACKUP ADMIN (SDC esetén): új adatbázisok létrehozása a visszaállítás során.
  * KATALÓGUS READ: a biztonsági másolat katalógusának olvasásához.
  * SAP_INTERNAL_HANA_SUPPORT: néhány privát asztal eléréséhez.
* A parancsfájl hozzáad egy kulcsot a **HDBUSERStore** for AZUREWLBACKUPHANAUSER számára a HANA biztonsági mentési beépülő modulhoz az összes művelet (adatbázis-lekérdezések, visszaállítási műveletek, konfigurálása és a biztonsági mentés futtatása) kezeléséhez.

>[!NOTE]
> Az [előfeltételek](#prerequisites) részeként felsorolt felhasználói kulcsot kifejezetten átadhatja az előzetes regisztrációs parancsfájlnak:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Ha meg szeretné tudni, hogy a parancsfájl milyen egyéb paramétereket fogad el, használja a`bash msawb-plugin-config-com-sap-hana.sh --help`

A kulcs létrehozásának megerősítéséhez futtassa a HDBSQL parancsot a HANA-gépen SIDADM hitelesítő adatokkal:

```hdbsql
hdbuserstore list
```

A parancskimenetnek meg kell jelenítenie a(z) {SID}{DBNAME} kulcsot, a felhasználó pedig AZUREWLBACKUPHANAUSER néven.

>[!NOTE]
> Győződjön meg arról, hogy a SSFS-fájlok egyedi készletével rendelkezik a területen. `/usr/sap/{SID}/home/.hdb/` Az elérési útnak csak egy mappája lehet.

## <a name="create-a-recovery-service-vault"></a>Helyreállítási szolgáltatás tárolójának létrehozása

A Recovery Services-tároló egy entitás, amely tárolja a biztonsági mentések és helyreállítási pontok idővel létrehozott. A Recovery Services-tároló is tartalmazza a biztonsági mentési szabályzatok, amelyek a védett virtuális gépek társított.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetésbe az [Azure Portalon.](https://portal.azure.com/)

2. A bal oldali menüben válassza a **Minden szolgáltatás lehetőséget**

   ![Az összes szolgáltatás kijelölése](./media/tutorial-backup-sap-hana-db/all-services.png)

3. A **Minden szolgáltatás** párbeszédpanelen adja meg a **Helyreállítási szolgáltatások párbeszédpanelt.** Az erőforrások listája a bevitt adatok nak megfelelően szűr. Az erőforrások listájában válassza a **Helyreállítási szolgáltatások tárolói lehetőséget.**

   ![A Helyreállítási szolgáltatások tárolóinak kiválasztása](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. A **Recovery** Services-tárolók irányítópultján válassza a **Hozzáadás lehetőséget.**

   ![Helyreállítási szolgáltatások tárolójának hozzáadása](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Megnyílik **a Helyreállítási szolgáltatások tárolója** párbeszédpanel. Értékek biztosítása a **Név, Előfizetés, Erőforrás csoport** és **Hely értékéhez**

   ![Helyreállítási tár létrehozása](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Név:** A név a helyreállítási szolgáltatások tárolójának azonosítására szolgál, és egyedinek kell lennie az Azure-előfizetésben. Adjon meg legalább két, de legfeljebb 50 karakterből álló nevet. A névnek betűvel kell kezdődnie, és csak betűkből, számokból és kötőjelekből állhat. Ebben az oktatóanyagban az **SAPHanaVault**nevet használtuk.
   * **Előfizetés**: Válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést használja, használja az alapértelmezett (javasolt) előfizetést. Több lehetőség csak akkor áll meg, ha a munkahelyi vagy iskolai fiókja egynél több Azure-előfizetéshez van társítva. Itt az SAP **HANA megoldáslabor-előfizetést** használtuk.
   * **Erőforráscsoport**: Használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Itt használtuk **a SAPHANADemo -t**.<br>
   Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **Meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza **az Új létrehozása lehetőséget,** és írja be a nevet. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
   * **Hely**: Válassza ki a tároló földrajzi régióját. A tárolónak ugyanabban a régióban kell lennie, mint az SAP HANA-t futtató virtuális gép. Használtunk **Kelet-US 2**.

5. Válassza **a Véleményezés + Létrehozás lehetőséget.**

   ![Válassza a Véleményezés & létrehozása lehetőséget.](./media/tutorial-backup-sap-hana-db/review-create.png)

A helyreállítási szolgáltatások tárolója most jön létre.

## <a name="discover-the-databases"></a>Az adatbázisok felfedezése

1. A tárolóelső **lépések**területén kattintson a **Biztonsági másolat gombra.** A **Hol fut a számítási feladatok?** válassza ki az SAP **HANA az Azure virtuális gép.**
2. Kattintson **a Felderítés indítása gombra.** Ez elindítja a nem védett Linux-virtuális gépek felderítését a tároló régióban. Látni fogja az Azure virtuális gép, amely védeni kívánt.
3. A Válassza ki a **virtuális gépek,** kattintson a hivatkozásra, hogy töltse le a parancsfájlt, amely engedélyeket biztosít az Azure Backup szolgáltatás eléréséhez az SAP HANA virtuális gépek adatbázis-felderítéshez.
4. Futtassa a parancsfájlt a virtuális gép üzemeltetése SAP HANA adatbázis(ok), amely szeretne biztonsági másolatot.
5. Miután futtatta a parancsfájlt a virtuális gépen, válassza ki a **virtuális gépek,** válassza ki a virtuális gépet. Ezután kattintson **a DB-ek felfedezése gombra.**
6. Az Azure Backup felderíti az összes SAP HANA-adatbázist a virtuális gépen. A felderítés során az Azure Backup regisztrálja a virtuális gépet a tárolóval, és telepíti a bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázisra.

   ![Az adatbázisok felfedezése](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

Most, hogy a biztonsági mentést akarunk készíteni, engedélyezzük a biztonsági mentést.

1. Kattintson **a Biztonsági másolat beállítása gombra.**

   ![Biztonsági mentés konfigurálása](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. A **Biztonsági másolatot tartalmazó elemek kijelölése**csoportban jelölje ki a védeni kívánt adatbázisokat, majd kattintson **az OK**gombra.

   ![A biztonsági másolatot adó elemek kijelölése](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. A Biztonsági másolat házirend > Válassza ki a **biztonsági mentési házirendet,** hozzon létre egy új biztonsági mentési házirendet az adatbázis(ok) számára a következő szakaszutasításainak megfelelően.

   ![Biztonsági mentési házirend kiválasztása](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. A házirend létrehozása után kattintson a **Biztonsági másolat menü**Biztonsági másolat engedélyezése **parancsára.**

   ![Kattintson a Biztonsági másolat engedélyezése gombra](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Kövesse nyomon a biztonsági mentés konfigurációjának előrehaladását a portál **Értesítések** területén.

## <a name="creating-a-backup-policy"></a>Biztonsági mentési házirend létrehozása

A biztonsági mentési szabályzat határozza meg, hogy mikor kell biztonsági mentéseket készíteni, és mennyi ideig őrzi meg őket.

* A szabályzat a tároló szintjén jön létre.
* Több tárolóhasználhatja ugyanazt a biztonsági mentési szabályzatot, de minden tárolóra alkalmaznia kell a biztonsági mentési szabályzatot.

Adja meg a házirend-beállításokat az alábbiak szerint:

1. A **Házirend neve**mezőbe írja be az új házirend nevét. Ebben az esetben adja meg az **SAPHANA**értéket.

   ![Új házirend nevének megadása](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. A **Teljes biztonsági mentés házirendben**válassza ki a Biztonsági másolat **gyakoriságát**. Választhat **a Daily** vagy **a Weekly**lehetőséget. Ebben az oktatóanyagban a **Napi** biztonsági mentést választottuk.

   ![Biztonsági mentési gyakoriság kiválasztása](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Az **Adatmegőrzési tartomány ban**konfigurálja a teljes biztonsági mentés adatmegőrzési beállításait.
   * Alapértelmezés szerint az összes beállítás ki van jelölve. Törölje a megőrzési tartomány korlátjait, amelyeket nem szeretne használni, és állítsa be azokat, amelyeket használ.
   * A minimális megőrzési idő bármely típusú biztonsági mentés (teljes/különbözeti/log) hét nap.
   * A helyreállítási pontok a megőrzési tartományuk alapján vannak címkézve megőrzésre. Ha például kiválaszt egy napi teljes biztonsági mentést, naponta csak egy teljes biztonsági mentés aktiválódik.
   * Egy adott nap biztonsági mentése címkézve van, és a heti megőrzési tartomány és beállítás alapján megmarad.
   * A havi és éves megőrzési tartományok hasonlóan viselkednek.
4. A **Teljes biztonsági mentés házirend** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. Ezután válassza **a különbözeti biztonsági mentés** kiválasztásához különbözeti házirend.
6. A **Különbözeti biztonsági mentés házirendben**válassza az **Engedélyezés** lehetőséget a gyakorisági és megőrzési vezérlők megnyitásához. Minden **vasárnap** **2:00**órakor engedélyeztük a különbözeti biztonsági mentést , amely **30 napig**marad meg .

   ![Különbözeti biztonsági mentési házirend](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >A növekményes biztonsági mentések jelenleg nem támogatottak.
   >

7. Kattintson az **OK** gombra a házirend mentéséhez és a **Biztonsági másolat fő menüjéhez** való visszatéréshez.
8. A tranzakciós napló biztonsági mentési házirendjének hozzáadásához válassza a **Biztonsági másolat napló** kiválasztása lehetőséget.
   * **A naplóbiztonsági mentés** alapértelmezés szerint **engedélyezésre**van állítva. Ez nem tiltható le, mert az SAP HANA kezeli az összes naplóbiztonsági mentést.
   * **2 órát** állítottunk be biztonsági mentési ütemezésként és **15 napos** megőrzési időszakot.

    ![Biztonsági mentési házirend naplózása](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > A naplóbiztonsági mentések csak egy sikeres teljes biztonsági mentés befejezése után kezdenek el folyni.
   >

9. Kattintson az **OK** gombra a házirend mentéséhez és a **Biztonsági másolat fő menüjéhez** való visszatéréshez.
10. Miután befejezte a biztonsági mentési házirend definiálását, kattintson az **OK**gombra.

Most már sikeresen konfigurálta a biztonsági mentés(ek) az SAP HANA adatbázis(ak)on.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [futtathat igény szerinti biztonsági másolatokat az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokban](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Megtudhatja, hogyan [állíthatja vissza az Azure virtuális gépeken futó SAP HANA-adatbázisokat](sap-hana-db-restore.md)
* Megtudhatja, hogyan kezelheti az Azure Backup használatával biztonsági mentést készítő [SAP HANA-adatbázisokat](sap-hana-db-manage.md)
* Ismerje meg, hogyan [háríthatók el az SAP HANA-adatbázisok biztonsági mentésekor felmerülő gyakori problémák](backup-azure-sap-hana-database-troubleshoot.md)
