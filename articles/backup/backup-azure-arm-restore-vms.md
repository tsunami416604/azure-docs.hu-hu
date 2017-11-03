---
title: "Az Azure Backup: Visszaállítása a virtuális gépek az Azure-portál használatával |} Microsoft Docs"
description: "Egy Azure virtuális gép visszaállíthatja őket egy helyreállítási pontból az Azure portál használatával"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "biztonsági másolat visszaállításával lehetséges; visszaállítása; helyreállítási pont;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: b659d5dc894afd2beef529c6b4f736e888b4540e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Az Azure-portál használatával állítsa vissza a virtuális gépek
> [!div class="op_single_selector"]
> * [Állítsa vissza a virtuális gépek a klasszikus portál](backup-azure-restore-vms.md)
> * [Állítsa vissza a virtuális gépek Azure-portálon](backup-azure-arm-restore-vms.md)
>
>

Pillanatképek készítése a adatait meghatározott időközönként védeni az adatokat. Ezeket a pillanatképeket nevezzük helyreállítási pontokat, és vannak tárolva, a Recovery Services-tárolók. Szükség esetén javítsa vagy építse újra a virtuális gép (VM), visszaállíthatja a virtuális gép bármelyik mentett helyreállítási pontok. A helyreállítási pont visszaállításakor teheti:

* Hozzon létre egy új virtuális Gépet, amely a biztonsági másolat VM időpontban ábrázolása.
* Állítsa vissza a lemezeket, és a sablon használata, amely rendelkezik a folyamat testreszabása a visszaállított virtuális Géphez, vagy hajtsa végre az egyes fájlok helyreállítása. 

Ez a cikk azt ismerteti, hogyan állítsa helyre a virtuális Gépet egy új virtuális Gépet, vagy állítsa vissza az összes biztonsági másolat lemez. Az egyes fájlok helyreállítása, lásd: [egy Azure virtuális gép biztonsági mentésből fájlokat helyreállítani](backup-azure-restore-files-from-vm.md).

![Háromféle virtuális gép biztonsági másolatból történő visszaállítását](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, hogy információkat és eljárásokat visszaállítására használt által központilag telepített virtuális gépek a Resource Manager modellt használja.
>
>

A virtuális gép vagy az összes lemez visszaállítása a virtuális gép biztonsági mentése két lépésből áll:

* Válassza ki a visszaállítási visszaállítási pontot.
* Válassza ki a helyreállítás típusát, hozzon létre egy új virtuális Gépet vagy lemezek visszaállítása, és adja meg a szükséges paramétereket. 

## <a name="select-a-restore-point-for-restore"></a>A visszaállítási visszaállítási pont kiválasztása
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

2. Válassza ki az Azure menü **Tallózás**. Írja be a szolgáltatások listájában **Recovery Services**. Milyen típusú szolgáltatások listájában igazodik. Amikor látja **Recovery Services-tárolók**, válassza ki azt.

    ![Recovery Services-tároló](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Az előfizetés tárolók listája jelenik meg.

    ![Helyreállítási szolgáltatások listája tárolók](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. A listában jelölje ki a visszaállítani kívánt virtuális Géphez társított tárolót. A tároló kiválasztásakor megnyílik az irányítópulton.

    ![Recovery Services-tároló kiválasztott](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. A tároló irányítópultjának a a **biztonsági mentés elemek** csempe, jelölje be **Azure virtuális gépek**.

    ![tároló irányítópult](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    A **biztonsági mentés elemek** panel nyílik meg, és Azure virtuális gépek listáját jeleníti meg.

    ![a tárolóban található virtuális gépek listájára](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. A listában jelölje ki a virtuális gépek az irányítópult megnyitásához. A virtuális gép irányítópult nyitja meg a figyelés területet, amely tartalmazza a **visszaállítási pontok** csempére.

    ![A visszaállítási pontok](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. Válassza ki a virtuális gép irányítópult menü **visszaállítása**.

    ![Visszaállítás gomb](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    A **visszaállítása** panel nyílik meg.

    ![visszaállítás panel](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Az a **visszaállítása** panelen válassza **visszaállítási pont**. A **visszaállítási pont kiválasztása** panel nyílik meg.

    ![Válasszon visszaállítási pontot](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Alapértelmezés szerint a párbeszédpanel megjeleníti az utolsó 30 nap visszaállítási pontok. Használja a **szűrő** módosítják a visszaállítási pontok időtartománya jelenik meg. Alapértelmezés szerint az összes consistencies visszaállítási pontok jelennek meg. Módosítsa a **minden visszaállítási pontok** szűrőt az adott helyreállítási pont konzisztencia. A helyreállítási pont típusonkénti kapcsolatos további információkért lásd: [adatkonzisztenciát](backup-azure-vms-introduction.md#data-consistency).

    Visszaállítási pont konzisztencia lehetőségek:

     * Összeomlás-konzisztens visszaállítási pontok
     * Alkalmazáskonzisztens visszaállítási pontok
     * A fájlrendszer konzisztens visszaállítási pontok
     * Minden visszaállítási pontok

8. Válasszon visszaállítási pontot, és válassza ki **OK**.

    ![Visszaállítási pont lehetőségek](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    A **visszaállítása** panelt jeleníti meg, hogy van-e állítva a visszaállítási pontot.

9. Ha most már nem létezik, a **visszaállítása** panelen. Győződjön meg arról, hogy egy [visszaállítási pont kiválasztása](#select-restore-point-for-restore), és válassza ki **konfiguráció visszaállítása**. A **konfiguráció visszaállítása** panel nyílik meg.

## <a name="choose-a-vm-restore-configuration"></a>Válassza ki a helyreállítási Virtuálisgép-konfiguráció
Miután kiválasztotta a visszaállítási pontot, válassza ki a helyreállítási Virtuálisgép-konfiguráció. Adja meg a visszaállított virtuális Géphez, használhatja az Azure portálon vagy a PowerShell.

1. Ha most már nem létezik, a **visszaállítása** panelen. Győződjön meg arról, hogy egy [visszaállítási pont kiválasztása](#select-restore-point-for-restore), és válassza ki **konfiguráció visszaállítása**. A **konfiguráció visszaállítása** panel nyílik meg.

    ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Az a **konfiguráció visszaállítása** panelen, két lehetősége van:

   * **Virtuális gép létrehozása**

   * **Lemezek visszaállítása**

A portál biztosít egy **Gyorslétrehozás** a visszaállított virtuális gépek lehetőséget. A Virtuálisgép-konfiguráció vagy egy új virtuális gép választott létrehozásának részeként létrejött erőforrásokat nevei egyéni beállításához használja PowerShell vagy a portál lemezek biztonsági másolat visszaállítása. PowerShell-parancsok segítségével csatolja őket a kiválasztott Virtuálisgép-konfigurációhoz. Vagy a visszaállított lemezek testreszabása a visszaállított virtuális Gépet a sablon is használhat. Állítsa vissza, amely több hálózati adapterrel rendelkezik, vagy a terheléselosztó információkért lásd: [állítsa helyre a virtuális Gépet speciális hálózati konfigurációkkal](#restore-a vm-with-special-network-configurations). Ha a Windows virtuális gép használ [HUB licencelési](../virtual-machines/windows/hybrid-use-benefit-licensing.md), állítsa vissza a lemezek és a PowerShell/sablon használatával Ez a cikk a virtuális gép létrehozása. Győződjön meg arról, hogy megadja a **licenctípus** mint "Windows_Server" a virtuális gép, további bejelentkezések nélkül hozzáférhet a központ előnyeit a visszaállított virtuális Gépre való létrehozása során. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Hozzon létre egy új virtuális Gépet egy visszaállítási pont
1. Ha még nem tette, [válasszon ki egy helyreállítási pontot](#restore-a vm-with-special-network-configurations) új virtuális gép egy visszaállítási pont létrehozása előtt. Miután kijelölt egy visszaállítási pontot a a **konfiguráció visszaállítása** panelen adja meg vagy válassza ki az értékeket az egyes a következő mezőket:

    a. **Állítsa vissza a típusú**. Hozzon létre egy virtuális gépet.

    b. **Virtuális gép neve**. Adja meg a virtuális gép nevét. A név az erőforráscsoportnak (az Azure Resource Manager telepített virtuális esetén) vagy a felhőszolgáltatás (az egy klasszikus virtuális gépekhez) egyedinek kell lennie. A virtuális gép nem cserélhető le, ha az előfizetés már létezik.

    c. **Erőforráscsoport**. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ha a klasszikus virtuális gépek visszaállításához a mező használatával adja meg egy új felhőalapú szolgáltatás nevét. Ha hoz létre egy új erőforrás csoport/felhőalapú szolgáltatás, a nevének globálisan egyedinek kell lennie. A felhőszolgáltatás neve általában társítva van egy nyilvánosan elérhető URL-cím: például: [cloudservice]. cloudapp.net. Ha használja a felhő erőforrás csoport/felhőszolgáltatás nevét már használja, Azure az erőforrás-csoport/felhőalapú szolgáltatás neve megegyezik a virtuális gép rendeli hozzá. Azure erőforrás-csoportok/felhőszolgáltatások és virtuális gépek nem társított bármely affinitáscsoportok jeleníti meg. További információkért lásd: [hogyan kell áttelepíteni egy regionális virtuális hálózat affinitáscsoportok](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuális hálózati**. Válassza ki a virtuális hálózatot, a virtuális gép létrehozásakor. A mező teszi lehetővé az előfizetéshez tartozó összes virtuális hálózatot. Az erőforráscsoport, a virtuális gép zárójelben jelennek meg.

    e. **Alhálózati**. Ha a virtuális hálózati alhálózat, az első alhálózat alapértelmezettként van beállítva. Ha további alhálózatokat, válassza ki a kívánt alhálózat.

    f. **A Tárfiók**. A listában megtalálható a storage-fiókok és a Recovery Services-tárolónak ugyanazon a helyen. Zónaredundáns tárolás fiókok nem támogatottak. Ha nem ugyanazon a helyen, és a Recovery Services-tárolónak tárolási fiók is létezik, kell létrehoznia egyet a visszaállítási művelet indítása előtt. A tárfiók replikációs típusát zárójelben jelenik meg.

    ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Ha az erőforrás-kezelő telepített virtuális gép, meg kell adnia egy virtuális hálózatot. Virtuális hálózat nem kötelező megadni egy klasszikus virtuális Gépet.

    > * Ha az felügyelt lemezzel rendelkező virtuális gépek, győződjön meg arról, hogy a kiválasztott tárolási fiók nincs beállítva a Azure Storage szolgáltatás titkosítási élettartama során.

    > * A kiválasztott tárfiók (prémium és standard) tárolási típus alapján vissza az összes lemez lesz vagy a prémium szintű, vagy a standard lemezek. Jelenleg nem támogatjuk lemezek vegyes üzemmódú visszaállításakor.
    >
    >

2. Az a **konfiguráció visszaállítása** panelen válassza **OK** a visszaállítási konfigurációjának véglegesítéséhez. Az a **visszaállítása** panelen válassza **visszaállítása** elindítani a visszaállítási műveletet.

## <a name="restore-backed-up-disks"></a>A biztonsági mentésben lemezek visszaállítása
A létrehozandó biztonsági másolat lemezekből azonos jelen a virtuális gép testreszabása a **konfiguráció visszaállítása** panelen válassza **lemezek visszaállítása** értéke **típusvisszaállítása**. Ez a beállítás egy tárfiókot, ahol a biztonsági mentésekből lemezek vannak-e a másolandó kér. Ha úgy dönt, hogy a storage-fiók, válassza ki a fiókot, amely megosztja a Recovery Services-tárolónak ugyanazon a helyen. Zónaredundáns tárolás fiókok nem támogatottak. Ha nem ugyanazon a helyen, és a Recovery Services-tárolónak tárolási fiók is létezik, kell létrehoznia egyet a visszaállítási művelet indítása előtt. A tárfiók replikációs típusát zárójelben jelenik meg.

A visszaállítási művelet befejezése után végezhetők el:

* [A sablon használatával testre szabhatja a visszaállított virtuális Géphez](#use-templates-to-customize-restore-vm)
* [A visszaállított lemez használata a meglévő virtuális csatolása](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Hozzon létre egy új virtuális gép visszaállított lemezekből PowerShell használatával](./backup-azure-vms-automation.md#restore-an-azure-vm)

Az a **konfiguráció visszaállítása** panelen válassza **OK** a visszaállítási konfigurációjának véglegesítéséhez. Az a **visszaállítása** panelen válassza **visszaállítása** elindítani a visszaállítási műveletet.

![Helyreállítási konfiguráció befejeződött](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>A visszaállítási művelet nyomon követése
A visszaállítási műveletet indít el, miután a biztonsági mentési szolgáltatás létrehoz egy feladatot az nyomon követése a visszaállítási műveletet. A biztonsági mentési szolgáltatás is létrehoz, és átmeneti megjelenítése a értesítések a **értesítések** a portál területen. Ha nem látja az értesítési, válassza ki a **értesítések** szimbólum az értesítések megtekintéséhez.

![A visszaállítási elindítva](./media/backup-azure-arm-restore-vms/restore-notification.png)

Megtekintheti a művelet feldolgozása közben, vagy arra, hogy megtekintse, amikor befejeződött, nyissa meg a **biztonsági mentési feladatok** listája.

1. Válassza ki az Azure menü **Tallózás**, és a szolgáltatások listájának megtekintéséhez írja be **Recovery Services**. Milyen típusú szolgáltatások listájában igazodik. Amikor látja **Recovery Services-tárolók**, válassza ki azt.

    ![Nyissa meg a Recovery Services-tároló](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Az előfizetés tárolók listája jelenik meg.

    ![Helyreállítási szolgáltatások listája tárolók](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. A listában jelölje ki a tárolóval a visszaállított virtuális Géphez társított. A tároló kiválasztásakor megnyílik az irányítópulton.

3. A tároló-irányítópulton a **biztonsági mentési feladatok** csempe, jelölje be **Azure virtuális gépek** a tárolóhoz rendelt feladatok megjelenítéséhez.

    ![tároló irányítópult](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    A **biztonsági mentési feladatok** panel nyílik meg, és a feladatok listáját jeleníti meg.

    ![Az adott tárolóban található virtuális gépek listájára](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>A sablonok segítségével testre szabhatja a visszaállított virtuális Géphez
Miután a [visszaállítási lemezek művelet befejezése](#Track-the-restore-operation), a sablon használatával lett létrehozva, a visszaállítási művelet részeként létrehozhat egy új virtuális gép konfigurálása eltér a biztonsági mentési konfigurációt. Is használhatja azoknak az erőforrásoknak az új virtuális gép egy visszaállítási pont létrehozása a folyamat során létrehozott neveivel testreszabásához. 

> [!NOTE]
> Sablonok visszaállítási lemezeket a helyreállítási pontok 2017. március 1. utáni része hozzá szeretné adni. Nem felügyelt szabad virtuális gépek alkalmazható fontosságúak. Felügyelt lemezes virtuális gépek támogatása hamarosan a jövőbeli kiadásokban. 
>
>

A sablon lett létrehozva, a lemezek helyreállítás részeként beolvasása:

1. Ugrás a feladat megfelelő visszaállítási feladat részleteit.

2. Az a **visszaállítása feladat részletei** képernyőn válassza ki **sablon telepítése** sablon központi telepítésének indítására. 

     ![Állítsa vissza a feladat részletes](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. Az a **telepítés sablon** panel egyéni telepítéshez használjon irányuló sablon-üzembehelyezés [szerkesztése és a sablon telepítéséhez](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) vagy hozzáfűző további testreszabásai [sablont készít](../azure-resource-manager/resource-group-authoring-templates.md) Ahhoz, hogy telepíteni. 

   ![Sablon-üzembehelyezés betöltése](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Miután megadta a szükséges értékeket, fogadja el a **feltételek és kikötések** válassza **beszerzési**.

   ![Küldje el a sablon telepítése](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Visszaállítás utáni lépéseket
* Egy felhőalapú init-Linux terjesztési, például Ubuntu, biztonsági okokból használatakor blokkolva van-e a jelszó visszaállítás utáni. A visszaállított virtuális Gépet a VMAccess bővítmény használatát [a jelszó alaphelyzetbe állítása](../virtual-machines/linux/classic/reset-access.md). Azt javasoljuk, SSH-kulcsok a ezek terjesztéseket alaphelyzetbe állítása a jelszó post visszaállítási elkerülése érdekében.
* A biztonsági mentési konfiguráció során Extensions telepítve, de nem engedélyezett. Ha azt látja, hogy a problémát, telepítse újra a bővítmények. 
* Ha a biztonsági másolat a virtuális gép statikus IP-post visszaállítási rendelkezik, a visszaállított virtuális Géphez van egy dinamikus IP-címe a visszaállított virtuális gépek létrehozásakor ütközés elkerülése érdekében. Tudjon meg többet adhat [egy statikus IP-cím hozzáadása a visszaállított virtuális Géphez](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* A visszaállított virtuális gép nem rendelkezik egy rendelkezésre állási értéket. Visszaállítás lemezek beállítás használatát javasoljuk [adja hozzá a rendelkezésre állási csoportok](../virtual-machines/windows/tutorial-availability-sets.md) lemezek létrehozásakor egy virtuális Gépet a PowerShell vagy a sablonok segítségével visszaállítani. 


## <a name="backup-for-restored-vms"></a>A visszaállított virtuális gépek biztonsági mentése
Ha egy virtuális gép visszakerül a neve megegyezik az eredeti biztonsági másolat virtuális gép ugyanabban az erőforráscsoportban, a biztonsági mentés továbbra is fennáll, a virtuális gép post visszaállításkor meg. Ha visszaállította a virtuális gép egy másik erőforráscsoportba vagy egy másik nevet a visszaállított virtuális géphez megadott, a virtuális gép kell kezelni, mintha ez egy új virtuális Gépet. Biztonsági mentés beállítása a visszaállított virtuális Géphez van szüksége.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Állítsa helyre a virtuális Gépet egy Azure-adatközpontban vészhelyreállítás során
Azure biztonsági mentés lehetővé teszi, hogy a biztonsági másolat virtuális gépek visszaállítása a párosított datacenter, abban az esetben az elsődleges adatközpont, ahol a virtuális gépek futnak észlel egy olyan vészhelyzet esetén, és konfigurálta a mentési tárolóban kell lennie a georedundáns. Ilyen forgatókönyvek során válasszon egy tárfiókot, amely párosított adatközpontban található. A helyreállítási folyamatot a többi változatlan marad. Biztonsági mentés a számítási szolgáltatás párosított földrajzi használja a visszaállított virtuális Géphez. További információkért lásd: [Azure datacenter rugalmassági](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Virtuális gépek tartományvezérlő visszaállítása
Biztonsági mentési tartományvezérlő (DC) virtuális gépek támogatott lehetőség biztonsági mentéssel. Azonban meg kell legyen óvatos a visszaállítási folyamat során. A megfelelő helyreállítási folyamatot attól függ, hogy a tartomány struktúráját. A legegyszerűbb esetben egyetlen tartományvezérlő egyetlen tartományban van. Gyakrabban üzemi terhelés esetén meg kell egyetlen tartományt a több tartományvezérlők, lehet, hogy egyes tartományvezérlők helyszíni. Végül lehetséges, hogy több tartomány erdő. 

Az Active Directory szempontból az Azure virtuális gép olyan, mint bármely más virtuális gép modern támogatott hipervizor alkalmazáson. A fő különbség a helyszíni hipervizorok, hogy nincs virtuális gép konzol az Azure-ban érhető el. A konzol bizonyos helyzetekben, például egy operációs rendszer nélküli helyreállítás (BMR) használatával helyreállításához szükség-típusú biztonsági mentést. VM-visszaállítást a biztonsági mentési tárolóból azonban a teljes operációs rendszer nélküli Helyreállítás váltja fel. Directory címtárszolgáltatások helyreállító módjában (DSRM) is rendelkezésre áll, ezért az összes Active Directory-helyreállítási forgatókönyvek életképes. További információkért lásd: [virtualizált tartományvezérlők biztonsági mentés és helyreállítás szempontjai](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) és [tervezése az Active Directory-erdő helyreállítási](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Egyetlen tartományvezérlő egyetlen tartományban
A virtuális Gépet az Azure portálról vagy a PowerShell használatával (mint bármely más virtuális gép) állíthatók vissza.

### <a name="multiple-dcs-in-a-single-domain"></a>Ugyanazon tartományba több tartományvezérlők
Ugyanabban a tartományban más tartományvezérlők elérhetők a hálózaton keresztül, a tartományvezérlő is visszaállítani, mint bármely virtuális gép. Az utolsó megmaradt tartományvezérlő abban a tartományban, vagy egy elkülönített hálózatot visszaállítás történik, ha egy erdő helyreállítási eljárást kell követni.

### <a name="multiple-domains-in-one-forest"></a>Több tartomány egy erdőben
Ugyanabban a tartományban más tartományvezérlők elérhetők a hálózaton keresztül, a tartományvezérlő is visszaállítani, mint bármely virtuális gép. Minden más esetben egy erdő helyreállítási javasoljuk.

## <a name="restore-vms-with-special-network-configurations"></a>Virtuális gépek visszaállítást a speciális hálózati konfigurációk
A biztonsági mentése és visszaállítása a virtuális gépek a következő speciális hálózati konfigurációkkal lehetőség. Ezek a beállítások azonban keresztül a visszaállítási folyamat során egy különös figyelmet igényel:

* A terheléselosztók (belső és külső) a virtuális gépek
* Virtuális gépek több foglalt IP-cím
* Több hálózati adapterrel rendelkező virtuális gépek

> [!IMPORTANT]
> A speciális hálózati konfigurációt a virtuális gépek létrehozásakor a PowerShell használatával hozhat létre virtuális gépeket a visszaállított lemezeket kell használnia.
>
>

Teljes hozza létre a virtuális lemez visszaállítása után, kövesse az alábbi lépéseket:

1. A lemezeket a Recovery Services-tároló segítségével visszaállítási [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. A terheléselosztóhoz szükséges Virtuálisgép-konfiguráció létrehozása / több hálózati adapter/több foglalt IP-cím a PowerShell-parancsmagok használatával. Ezzel a virtuális gép létrehozása a kívánt konfiguráció:

   a. A felhőszolgáltatás a virtuális gép létrehozása egy [belső terheléselosztó](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Hozzon létre egy virtuális gép csatlakozni egy [internetre terheléselosztó](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/).

   c. A virtuális gép létrehozása [több hálózati adapter](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. A virtuális gép létrehozása [több foglalt IP-címek](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gépek is helyreállíthatja, tanulmányozza a hibaelhárítási információk a virtuális gépek az előforduló hibákat: Emellett tekintse meg a cikk a feladatokat a virtuális gépek kezelésére.

* [Kapcsolatos hibák elhárítása](backup-azure-vms-troubleshoot.md#restore)
* [Virtuális gépek kezelése](backup-azure-manage-vms.md)
