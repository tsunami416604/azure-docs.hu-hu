---
title: Oktatóanyag – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből az oktatóanyagból megtudhatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépen futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóra.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 31958a4d4e3af4f747ab2f9de7b1bc67560e87d7
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248243"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Oktatóanyag: SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépen

Ez az oktatóanyag bemutatja, hogyan készíthet biztonsági mentést SAP HANA Azure-beli virtuális gépeken futó adatbázisokról egy Azure Backup Recovery Services-tárolóra. Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása

[Itt](sap-hana-backup-support-matrix.md#scenario-support) találja az összes olyan forgatókönyvet, amelyet jelenleg támogatunk.

>[!NOTE]
>[Ismerkedés](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) a RHEL SAP HANA Backup előzetes verziójával (7,4, 7,6, 7,7 vagy 8,1). További lekérdezések írásához írjon nekünk a következő címen: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Előfeltételek

A biztonsági mentések konfigurálása előtt győződjön meg arról, hogy a következőket végzi el:

* Engedélyezze a virtuális gép és az Internet közötti kapcsolat használatát, hogy az elérhető legyen az Azure-ban, az alábbi [hálózati csatlakozási eljárás beállítása](#set-up-network-connectivity) című témakörben leírtak szerint.
* Léteznie kell egy kulcsnak a **hdbuserstore** , amely megfelel a következő feltételeknek:
  * Jelen kell lennie az alapértelmezett **hdbuserstore**
  * A MDC a kulcsnak a **NÉVSZERVER**SQL-portjára kell mutatnia. SDC esetén a **INDEXSERVER** SQL-portjára kell mutatnia.
  * A felhasználónak hitelesítő adatokkal kell rendelkeznie a felhasználók hozzáadásához és törléséhez
* Futtassa a SAP HANA biztonsági mentési konfigurációs parancsfájlt (előzetes regisztrációs parancsfájl) azon a virtuális gépen, ahol a HANA telepítve van, a legfelső szintű felhasználóként. [Ez a szkript](https://aka.ms/scriptforpermsonhana) a HANA-rendszer biztonsági mentésre kész állapotba helyezését kéri le. Az előzetes regisztrációs szkripttel kapcsolatos további információkért tekintse meg az [előzetes regisztrációs parancsfájlt](#what-the-pre-registration-script-does) ismertető szakaszt.

>[!NOTE]
>Az előregisztrációs parancsfájl a RHEL-on (7,4, 7,6 és 7,7) futó SAP HANA-munkaterhelések unixODBC234 telepíti, valamint a RHEL 8,1 **-** **unixODBC** . [Ez a csomag a RHEL for SAP HANA (a RHEL 7 Server esetében) Update Services for SAP Solutions (RPMs)](https://access.redhat.com/solutions/5094721)tárházban található.  Az Azure Marketplace RHEL rendszerképében a tárház a következő lenne: **rhui-RHEL-SAP-Hana-for-RHEL-7-Server-rhui-e4s-RPMs**.

## <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

Az SAP HANA virtuális gépnek minden művelethez kapcsolódnia kell az Azure nyilvános IP-címeihez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági másolatok konfigurálása, biztonsági másolatok ütemezése, helyreállítási pontok visszaállítása stb.) nem működnek az Azure nyilvános IP-címekkel való kapcsolat nélkül.

Kapcsolat létesítése a következő lehetőségek egyikének használatával:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Az Azure-adatközpont IP-tartományának engedélyezése

Ez a beállítás engedélyezi a letöltött fájl [IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653) . Hálózati biztonsági csoport (NSG) eléréséhez használja a set-AzureNetworkSecurityRule parancsmagot. Ha a biztonságos címzettek listája csak a régióra jellemző IP-címeket tartalmazza, akkor a hitelesítés engedélyezéséhez is frissítenie kell a biztonságos címzettek listáját a Azure Active Directory (Azure AD) szolgáltatás címkével.

### <a name="allow-access-using-nsg-tags"></a>Hozzáférés engedélyezése NSG-címkék használatával

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

**Beállítás** | **Előnyök** | **Hátrányok**
--- | --- | ---
IP-címtartományok engedélyezése | Nincs további költség | Összetett a kezeléshez, mert az IP-címtartományok változnak az idő múlásával <br/><br/> Hozzáférést biztosít az egész Azure-hoz, nem csak az Azure Storage-hoz
NSG szolgáltatásbeli címkék használata | A tartomány módosításainak könnyebb kezelése automatikusan történik <br/><br/> Nincs további költség <br/><br/> | Csak NSG használható <br/><br/> Hozzáférést biztosít a teljes szolgáltatáshoz
Azure Firewall FQDN-címkék használata | Könnyebben kezelhető, mert a szükséges teljes tartománynevek automatikusan kezelhetők | Csak Azure Firewall használható
HTTP-proxy használata | A proxy részletes vezérlője a tárolási URL-címeken keresztül engedélyezett <br/><br/> A virtuális gépekhez való internetes hozzáférés egyetlen pontja <br/><br/> Nem vonatkozik az Azure IP-címek változásai | További költségek egy virtuális gép futtatásához a proxy szoftverrel

## <a name="what-the-pre-registration-script-does"></a>Az előzetes regisztrációs parancsfájl

Az előzetes regisztrációs parancsfájl futtatása a következő funkciókat hajtja végre:

* Telepíti vagy frissíti a Azure Backup ügynök által a terjesztéshez szükséges szükséges csomagokat.
* Kimenő hálózati kapcsolati ellenőrzéseket hajt végre Azure Backup-kiszolgálókkal és függő szolgáltatásokkal (például Azure Active Directory és Azure Storage).
* Bejelentkezik a HANA rendszerbe az [Előfeltételek](#prerequisites)részeként felsorolt felhasználói kulccsal. A felhasználói kulcs használatával biztonsági mentési felhasználó (AZUREWLBACKUPHANAUSER) hozható létre a HANA-rendszeren, és a felhasználói kulcs törölhető az előzetes regisztrációs parancsfájl sikeres futtatása után.
* A AZUREWLBACKUPHANAUSER a szükséges szerepköröket és engedélyeket rendeli hozzá:
  * ADATBÁZIS-rendszergazda (MDC esetén) és biztonsági mentési rendszergazda (SDC esetén): új adatbázisok létrehozása a visszaállítás során.
  * Katalógus OLVASása: a biztonsági mentési katalógus beolvasása.
  * SAP_INTERNAL_HANA_SUPPORT: néhány privát tábla eléréséhez.
* A parancsfájl egy kulcsot hoz létre a HANA Backup beépülő modulhoz tartozó AZUREWLBACKUPHANAUSER **hdbuserstore** , amely az összes műveletet (adatbázis-lekérdezések, visszaállítási műveletek, a biztonsági mentés konfigurálása és futtatása) kezeli.

>[!NOTE]
> Explicit módon megadhatja az [előfeltételként](#prerequisites) megadott felhasználói kulcsot az előfeltételek paraméterként az előzetes regisztrációs parancsfájlhoz:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>A parancsfájl által elfogadott egyéb paraméterek megismeréséhez használja a parancsot`bash msawb-plugin-config-com-sap-hana.sh --help`

A kulcs létrehozásának megerősítéséhez futtassa a HDBSQL parancsot a HANA gépen a SIDADM hitelesítő adataival:

```hdbsql
hdbuserstore list
```

A parancs kimenetének meg kell jelennie a {SID} {DBNAME} kulcsnak, amely a felhasználó AZUREWLBACKUPHANAUSER jelenik meg.

>[!NOTE]
> Győződjön meg arról, hogy rendelkezik a SSFS-fájlok egyedi készletével `/usr/sap/{SID}/home/.hdb/` . Ezen az elérési úton csak egy mappa lehet.

## <a name="create-a-recovery-service-vault"></a>Recovery Service-tároló létrehozása

A Recovery Services-tároló egy olyan entitás, amely az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat tárolja. A Recovery Services-tároló a védett virtuális gépekhez társított biztonsági mentési házirendeket is tartalmazza.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetésbe a [Azure Portalban](https://portal.azure.com/).

2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget.

   ![Minden szolgáltatás kiválasztása](./media/tutorial-backup-sap-hana-db/all-services.png)

3. A **minden szolgáltatás** párbeszédpanelen írja be a **Recovery Services**értéket. Az erőforrás-szűrők listája a bemenet alapján. Az erőforrások listájában válassza a **Recovery Services**-tárolók lehetőséget.

   ![Recovery Services tárolók kiválasztása](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. A **Recovery Services** -tárolók irányítópultján válassza a **Hozzáadás**lehetőséget.

   ![Recovery Services-tároló hozzáadása](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Megnyílik az **Recovery Services** -tároló párbeszédpanel. Adja meg a **név, az előfizetés, az erőforráscsoport** és a **hely** értékét.

   ![Helyreállítási tár létrehozása](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Név**: a név a Recovery Services-tároló azonosítására szolgál, és egyedinek kell lennie az Azure-előfizetésben. Adjon meg legalább két, de legfeljebb 50 karakterből álló nevet. A névnek betűvel kell kezdődnie, és csak betűt, számot és kötőjelet tartalmazhat. Ebben az oktatóanyagban a **SAPHanaVault**nevet használtuk.
   * **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, akkor ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Több választási lehetőség is van, ha a munkahelyi vagy iskolai fiók egynél több Azure-előfizetéshez van társítva. Itt a **SAP HANA Solution Lab előfizetési** előfizetést használtuk.
   * **Erőforráscsoport**: használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Itt **SAPHANADemo**használunk.<br>
   Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a nevet. Az erőforráscsoportok részletes ismertetését itt tekintheti meg: [Azure Resource Manager Overview (áttekintés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)).
   * **Hely**: válassza ki a tároló földrajzi régióját. A tárolónak ugyanabban a régióban kell lennie, mint ahol a virtuális gép SAP HANA fut. Használtuk az **USA 2. keleti**régióját.

5. Válassza a **felülvizsgálat + létrehozás**lehetőséget.

   ![& létrehozási áttekintés kiválasztása](./media/tutorial-backup-sap-hana-db/review-create.png)

Ekkor létrejön a Recovery Services-tároló.

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban **első lépések**kattintson a **biztonsági mentés**elemre. A **hol fut a**számítási feladat? területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**.
2. Kattintson a **felderítés indítása**gombra. Ez elindítja a nem védett Linux rendszerű virtuális gépek felderítését a tároló régiójában. Ekkor megjelenik a védelemmel ellátni kívánt Azure-beli virtuális gép.
3. A **Virtual Machines kiválasztása lapon**kattintson a hivatkozásra azon parancsfájl letöltéséhez, amely a Azure Backup szolgáltatás számára engedélyeket biztosít a SAP HANA virtuális gépek adatbázis-felderítéshez való hozzáféréséhez.
4. Futtassa a szkriptet azon a virtuális gépen, amely a biztonsági mentésre használni kívánt SAP HANA adatbázis (oka) t futtatja.
5. Miután futtatta a szkriptet a virtuális gépen, a **Virtual Machines kiválasztása**lapon válassza ki a virtuális gépet. Ezután kattintson az **adatbázisok felderítése**elemre.
6. Azure Backup a virtuális gépen lévő összes SAP HANA adatbázist felfedi. A felderítés során Azure Backup regisztrálja a virtuális gépet a tárolóban, és telepít egy bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázison.

   ![Az adatbázisok felderítése](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

Most, hogy felderítjük a biztonsági mentéshez használni kívánt adatbázisokat, engedélyezzük a biztonsági mentést.

1. Kattintson a **biztonsági mentés konfigurálása**elemre.

   ![Biztonsági mentés konfigurálása](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Az **elemek kijelölése biztonsági mentéshez**területen válasszon ki egy vagy több védelemmel ellátni kívánt adatbázist, majd kattintson **az OK**gombra.

   ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. A **biztonsági mentési szabályzat > válassza a biztonsági mentési szabályzat lehetőséget**, hozzon létre egy új biztonsági mentési szabályzatot az adatbázis (ok) hoz a következő szakaszban található utasítások szerint.

   ![Biztonsági mentési házirend kiválasztása](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. A szabályzat létrehozása után a **biztonsági mentés menüben**kattintson a **biztonsági mentés engedélyezése**elemre.

   ![Kattintson a biztonsági mentés engedélyezése elemre.](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. A biztonsági mentési konfiguráció előrehaladásának nyomon követése a portál **értesítések** területén.

## <a name="creating-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza a biztonsági másolatok készítésének idejét, valamint azt, hogy mennyi ideig őrzi meg a rendszer.

* A rendszer a tároló szintjén hozza létre a szabályzatot.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.

A házirend-beállításokat a következőképpen adhatja meg:

1. A **Házirend neve**mezőben adja meg az új szabályzat nevét. Ebben az esetben adja meg a **SAPHANA**.

   ![Adja meg az új szabályzat nevét](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. A **teljes biztonsági mentési szabályzatban**válasszon ki egy **biztonsági mentési gyakoriságot**. **Napi** vagy **heti**választ is választhat. Ebben az oktatóanyagban a **napi** biztonsági mentést választottuk.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. A **megőrzési tartomány**területen konfigurálja a teljes biztonsági mentés megőrzési beállításait.
   * Alapértelmezés szerint az összes beállítás ki van választva. Törölje az összes olyan megőrzési időtartamra vonatkozó korlátozást, amelyet nem kíván használni, és állítsa be azokat.
   * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) hét nap.
   * A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
   * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a beállítás alapján van megcímkézve és megtartva.
   * A havi és az éves adatmegőrzési tartomány hasonló módon viselkedik.
4. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. Ezt követően válassza a **különbözeti biztonsági mentés** lehetőséget a különbözeti szabályzat hozzáadásához.
6. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához. Minden **vasárnap** ( **2:00 órakor**) elvégezte a különbözeti biztonsági mentést, amely **30 napig**tart.

   ![Különbözeti biztonsági mentési szabályzat](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >A növekményes biztonsági mentések jelenleg nem támogatottak.
   >

7. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
8. A tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése** lehetőséget,
   * A **napló biztonsági mentése** alapértelmezés szerint **engedélyezve**van. Ez nem tiltható le, mert SAP HANA kezeli az összes napló biztonsági mentését.
   * A biztonsági mentési ütemtervnek és a megőrzési idő **15 napjának** **2 óra** van beállítva.

    ![Biztonsági mentési szabályzat naplózása](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > A naplók biztonsági mentései csak egy sikeres teljes biztonsági mentés befejezése után kezdődnek.
   >

9. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
10. Miután befejezte a biztonsági mentési szabályzat definiálását, kattintson **az OK**gombra.

Sikeresen konfigurálta a (z) SAP HANA adatbázis (ok) hoz készült biztonsági másolat (oka) t.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [futtathat igény szerinti biztonsági mentést az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokon](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](sap-hana-db-restore.md)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](sap-hana-db-manage.md)
* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során](backup-azure-sap-hana-database-troubleshoot.md) felmerülő gyakori problémákat
