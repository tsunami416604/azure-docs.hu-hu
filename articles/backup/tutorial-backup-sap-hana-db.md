---
title: Oktatóanyag – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből az oktatóanyagból megtudhatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépen futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóra.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: a622370fca3144aeb6a5d7c071c227b3c21cf135
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288761"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Oktatóanyag: SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépen

Ez az oktatóanyag bemutatja, hogyan készíthet biztonsági mentést SAP HANA Azure-beli virtuális gépeken futó adatbázisokról egy Azure Backup Recovery Services-tárolóra. Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása

[Itt](sap-hana-backup-support-matrix.md#scenario-support) találja az összes olyan forgatókönyvet, amelyet jelenleg támogatunk.

## <a name="onboard-to-the-public-preview"></a>Bevezetés a nyilvános előzetes verzióba

A nyilvános előzetes verzióra az alábbiak szerint kell bejelentkezni:

* A portálon regisztrálja előfizetési AZONOSÍTÓját a Recovery Services szolgáltatónak a [jelen cikkben](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)leírtak szerint.

* A PowerShell esetében futtassa ezt a parancsmagot. A műveletnek "regisztrálva" kell lennie.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Előfeltételek

A biztonsági mentések konfigurálása előtt győződjön meg arról, hogy a következőket végzi el:

1. A SAP HANA adatbázist futtató virtuális gépen telepítse és engedélyezze az ODBC-illesztőprogram csomagjait a hivatalos SLES-csomagból/adathordozóról a Zypper használatával, az alábbiak szerint:

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> Ha nem frissíti a adattárakat, győződjön meg arról, hogy a unixODBC legalább 2.3.4-es verziója van. A uniXODBC verziójának megismeréséhez futtassa a `odbcinst -j` root-ként
>

2. Az [alábbi eljárásban](#set-up-network-connectivity)leírtak alapján engedélyezze a kapcsolatot a virtuális gépről az internetre, hogy az elérhető legyen az Azure-ban.

3. Futtassa az előzetes regisztrációs parancsfájlt a virtuális gépen, ahol a HANA a root felhasználóként van telepítve. [Ez a parancsfájl](https://aka.ms/scriptforpermsonhana) a [megfelelő engedélyeket](#setting-up-permissions)fogja beállítani.

## <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

Az SAP HANA virtuális gépnek minden művelethez kapcsolódnia kell az Azure nyilvános IP-címeihez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági másolatok konfigurálása, biztonsági másolatok ütemezése, helyreállítási pontok visszaállítása stb.) nem működhetnek kapcsolat nélkül. Kapcsolat létesítése az Azure-adatközpont IP-tartományokhoz való hozzáférés engedélyezésével:

* Letöltheti az Azure-adatközpontok [IP-](https://www.microsoft.com/download/details.aspx?id=41653) címtartományt, majd engedélyezheti a hozzáférést ezekhez az IP-címekhez.
* Hálózati biztonsági csoportok (NSG) használata esetén a AzureCloud [szolgáltatás címkével](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) engedélyezheti az összes Azure-beli nyilvános IP-címet. A NSG szabályok módosításához a [set-AzureNetworkSecurityRule parancsmag](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) használható.
* Az 443-es portot fel kell venni az engedélyezési listára, mivel az átvitel HTTPS-kapcsolaton keresztül történik.

## <a name="setting-up-permissions"></a>Engedélyek beállítása

Az előzetes regisztrációs parancsfájl a következő műveleteket hajtja végre:

1. AZUREWLBACKUPHANAUSER hoz létre a HANA-rendszeren, és hozzáadja ezeket a szükséges szerepköröket és engedélyeket:
   * ADATBÁZIS-rendszergazda: új adatbázisok létrehozása a visszaállítás során.
   * Katalógus OLVASása: a biztonsági mentési katalógus beolvasása.
   * SAP_INTERNAL_HANA_SUPPORT: néhány privát tábla eléréséhez.
2. Feltesz egy kulcsot a HANA beépülő modul Hdbuserstore az összes művelet (adatbázis-lekérdezések, visszaállítási műveletek, a biztonsági mentés konfigurálása és futtatása) kezelésére.

A kulcs létrehozásának megerősítéséhez futtassa a HDBSQL parancsot a HANA gépen a SIDADM hitelesítő adataival:

```hdbsql
hdbuserstore list
```

A parancs kimenetének meg kell jelennie a {SID} {DBNAME} kulcsnak, amely a felhasználó AZUREWLBACKUPHANAUSER jelenik meg.

>[!NOTE]
> Győződjön meg arról, hogy a SSFS-fájlok egyedi készlete van a/usr/sap/{SID}/home/.hdb/. alatt Ezen az elérési úton csak egy mappa lehet.
>

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

   ![& Létrehozási áttekintés kiválasztása](./media/tutorial-backup-sap-hana-db/review-create.png)

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

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [futtathat igény szerinti biztonsági mentést az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokon](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](sap-hana-db-restore.md)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](sap-hana-db-manage.md)
* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során](backup-azure-sap-hana-database-troubleshoot.md) felmerülő gyakori problémákat
