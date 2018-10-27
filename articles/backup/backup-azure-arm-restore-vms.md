---
title: 'Az Azure Backup: Virtuális gépek visszaállítása az Azure portal használatával'
description: Az Azure virtuális gép visszaállítása egy helyreállítási pontból az Azure portal használatával
services: backup
author: geethalakshmig
manager: vijayts
keywords: biztonsági másolatának visszaállítása hogyan lehet visszaállítani; helyreállítási pont;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: 5812bafbcfa104150325dcdde374f88200b071c0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155201"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Virtuális gépek visszaállítása az Azure portal használatával
Védje adatait az adatpillanatképek meghatározott időközönként végrehajtásával. Ezek a pillanatképek a helyreállítási pontok nevezzük, és a Recovery Services-tárolók tárolásuk. Szükség esetén javítsa ki vagy építse újra a virtuális gép (VM), visszaállíthatja a virtuális gép bármelyik mentett helyreállítási pontokat. Egy helyreállítási pontot állítja vissza, ha Ön a következőket teheti:

* Hozzon létre egy új virtuális Gépet, amely a biztonsági másolatban szereplő virtuális gép időponthoz ábrázolása.
* Állítsa vissza a lemezeket, és a sablon használata, amely a folyamat testreszabása a visszaállított virtuális Gépet tartalmaz, vagy hajtsa végre az egyes fájlok helyreállítása.

Ez a cikk ismerteti a virtuális gép visszaállítása egy új virtuális géphez, vagy az összes biztonsági másolat lemez visszaállítása. Az egyes fájlhelyreállítás, lásd: [fájlok helyreállítása az Azure VM backup](backup-azure-restore-files-from-vm.md).

![Háromféle lehetőség a virtuális gép biztonsági másolat visszaállítása](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, hogy információkat és eljárásokat visszaállítására használt üzembe helyezett virtuális gépek a Resource Manager-modell használatával.
>
>

Virtuális gép és az összes lemez visszaállítása virtuális gépről biztonsági másolat két lépésből áll:

* Válassza ki a visszaállítási pontot a visszaállításhoz.
* Válassza ki a helyreállítás típusát, hozzon létre egy új virtuális Gépet lemezek visszaállítása, és adja meg a szükséges paramétereket.

## <a name="select-a-restore-point-for-restore"></a>Válasszon egy visszaállítási pontot a visszaállításhoz
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

2. Az Azure menüben válassza ki a **minden szolgáltatás**. A szolgáltatások listájában, írja be **Recovery Services** , vagy **tárolási** ahol a **Recovery Service-tárolók** van a listában, válassza ki azt.

    ![Recovery Services-tároló](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. Az előfizetés-tárolók listája megjelenik.

    ![Listája a Recovery Services-tárolók](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Recovery Service-tárolók listájából válassza ki a visszaállítani kívánt virtuális Géphez társított tárolót. Amikor kiválasztja a tárolót, megnyílik az irányítópultja.

    ![Kijelölt Recovery Services-tároló](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. A tároló irányítópultján található a **biztonsági másolati elemek** csempéről válassza **Azure virtuális gép**.

    ![Tároló irányítópultján](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. A **biztonsági másolati elemek** Azure virtuális gépek listáját tartalmazó panel megnyílik.

    ![Tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. A listában jelölje ki a virtuális gép az irányítópult megnyitásához. A figyelés területet, amely tartalmazza a virtuális gép irányítópult megnyílik a **helyreállítási pontok**. Az összes virtuális gép szintű műveletek, például **biztonsági mentés**, **helyreállítási fájl**, **biztonsági mentés leállítása** ezen a panelen végezheti el.
Visszaállítás ezen a panelen számos különféle módon hajtható végre. Vegye figyelembe, hogy a panel felsorolja az elmúlt 30 napban helyreállítási pontokat.

    a) kattintson a jobb gombbal a helyreállítási pont ezen a panelen (kevesebb mint 30 nap), és a **virtuális gép visszaállítása**.

    (b) visszaállítása a helyreállítási pontok nagyobb, mint 30 nap, kattintson a panel az itt megadott is használható.

    c) **virtuális gép visszaállítása** menüjében fejléc listázása és a virtuális gépek előnyben részesített testre szabott dátumok szűrés lehetőséget biztosít.

    Használja a szűrőt, hogy a visszaállítási pontok időtartománya alter jelenik meg. Alapértelmezés szerint az összes kényszerítené a felhasználókat a visszaállítási pontok jelennek meg. Az összes visszaállítási pontok szűrőt az egy adott helyreállítási pont konzisztencia módosításához. További információ a különböző típusú visszaállítási pont: [adatkonzisztencia](backup-azure-vms-introduction.md#data-consistency).

    Visszaállítási pont konzisztencia lehetőségek:
    - Összeomlás-konzisztens visszaállítási pontok
    - Alkalmazáskonzisztens visszaállítási pontok
    - A fájlrendszer konzisztens visszaállítási pontok
    - Minden visszaállítási pont

  ![Visszaállítási pontok](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > Helyreállítási típus jelölik, ha az ügyfél tárfiókja, a tároló vagy mindkettőt. Tudjon meg többet [azonnali helyreállítási pont](https://azure.microsoft.com/blog/large-disk-support/).

8. Az a **visszaállítása** panelen válassza ki **visszaállítási pont**.

    ![Visszaállítási pont kijelölése](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    A **visszaállítása** panelen jelenik meg, hogy a visszaállítási pont gombra való kattintás után értéke **OK**.
9. Ha Ön még nem létezik, a **visszaállítása** panelen. Ügyeljen arra, hogy egy [visszaállítási pontot választja](#select-a-restore-point-for-restore), és válassza ki **konfiguráció visszaállítása**. A **konfiguráció visszaállítása** panel nyílik meg.

## <a name="choose-a-vm-restore-configuration"></a>Válasszon egy Virtuálisgép-konfiguráció visszaállítása
Miután kiválasztotta a visszaállítási pontot, válassza ki a Virtuálisgép-konfiguráció visszaállítása. Konfigurálhatja a visszaállított virtuális Gépet, használhatja az Azure portal vagy a PowerShell.

1. Ha Ön még nem létezik, a **visszaállítása** panelen. Ügyeljen arra, hogy egy [visszaállítási pontot választja](#select-a-restore-point-for-restore), és válassza ki **konfiguráció visszaállítása**. A **konfiguráció visszaállítása** panel nyílik meg.
2. Ezen a panelen jelenleg tartalmaz két lehetőség egy folyamatban **hozzon létre új** van olyan alapértelmezett és az egyéb **cserélje le a meglévő** Ez csak a meglévő konfiguráció megőrzésével, cserélje le a lemezek helyi visszaállítása és -bővítmények.

> [!NOTE]
> Dolgozunk a teljes virtuális gép cseréje a (eke) t, a hálózati beállításokat, a konfigurációk és a bővítményeket a következő néhány hónap során.
>
>

 Az a **hozzon létre új** , amely visszaállítja az új virtuális Gépet vagy új (eke) t, az adatokat a két lehetőség közül választhat:

 ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **Virtuális gép létrehozása**
 - **Lemezek visszaállítása**

![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

A portál biztosít egy **Gyorslétrehozás** a visszaállított virtuális gép. A Virtuálisgép-konfigurációt vagy egy új virtuális gép választott létrehozásának részeként létrehozott erőforrások nevei egyéni beállításához használja PowerShell vagy a portál lemezek biztonsági másolat visszaállításához. PowerShell-parancsok segítségével csatolja őket a kiválasztott Virtuálisgép-konfigurációhoz. Vagy használhatja a sablont, amely együttműködik a helyreállított lemezek szabhatja testre a visszaállított virtuális Gépet. Virtuális gép több hálózati adapterrel rendelkezik, vagy egy terheléselosztó alatt visszaállításával kapcsolatos további információkért lásd: [speciális hálózati konfigurációval rendelkező virtuális gép visszaállítása](#restore-vms-with-special-network-configurations). Ha a Windows virtuális gép használ [központi licencelési](../virtual-machines/windows/hybrid-use-benefit-licensing.md), állítsa vissza a lemezeket, és a virtuális gép létrehozásához használja a PowerShell/sablon ebben a cikkben megadott. Győződjön meg arról, hogy megadja a **licenctípus** mint "Windows_Server" elérhetővé tételéhez a HUB előnyei a visszaállított virtuális Gépet a virtuális gép létrehozásakor. Vegye figyelembe, hogy ez később is virtuális gép létrehozása után végezhető.

## <a name="create-a-new-vm-from-a-restore-point"></a>Hozzon létre egy új virtuális Gépet egy visszaállítási pont
1. Az a **konfiguráció visszaállítása** említett panelen a előtt szakaszban adja meg vagy válassza ki az értékeket a következő mezők mindegyike esetében:

    a. **Visszaállítási típus**. Virtuális gépet hoz létre.

    b. **A virtuális gép neve**. Adja meg a virtuális gép nevét, amely nem létezik az előfizetésben.

    c. **Erőforráscsoport**. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ha a klasszikus virtuális gép visszaállításához a mező használatával adja meg az új felhőalapú szolgáltatás nevét. Egy új erőforrás/felhőszolgáltatáshoz hoz létre, ha a nevének globálisan egyedinek kell lennie. A felhőszolgáltatás neve általában egy nyilvános URL-cím társítva: [felhőszolgáltatás] a példában. cloudapp.net. Ha megkísérli a felhőbeli erőforrások/felhőszolgáltatáshoz nevét már használja a használatban lévő, az Azure az erőforrás-csoport/felhőalapú szolgáltatás neve megegyezik a virtuális gép rendeli hozzá. Az Azure erőforrás-csoportok-/ cloud services és a nem minden affinitáscsoportok társított virtuális gépek jeleníti meg. További információkért lásd: [hogyan kell áttelepíteni egy regionális virtuális hálózat az affinitáscsoportok](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuális hálózat**. Válassza ki a virtuális hálózatot, virtuális gép létrehozásakor. A mező biztosít az előfizetéshez tartozó összes virtuális hálózatot. Az erőforráscsoport, a virtuális gép zárójelben jelenik meg.

    e. **Alhálózat**. Ha a virtuális hálózati alhálózatokhoz, alapértelmezés szerint az első alhálózat van kiválasztva. Ha további alhálózatokat, válassza ki a kívánt alhálózat.

    f. **Tárolási hely**. Storage-fiókok az előkészítési helyet. Ebben a menüben a storage-fiókok és a Recovery Services-tárolónak ugyanazon a helyen sorolja fel. Amelyek a zónaredundáns tárfiókok nem támogatottak. Ha nem ugyanazon a helyen, és a Recovery Services-tárolónak a storage-fiókok, létre kell hoznia egyet a visszaállítási művelet megkezdése előtt. A storage-fiók replikációs típusának zárójelben jelenik meg.

    ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * Virtuális hálózat egy klasszikus virtuális gép esetén nem kötelező és kötelező a Resource Manager által telepített virtuális gép.

    > * Tárolási írja be a megadott Storage előkészítési helyét (prémium vagy standard) fiókot úgy dönt, a visszaállítási lemezes tárolás típusára. Jelenleg nem támogatjuk a vegyes módú lemezek visszaállításakor.
    >
    >

2. Az a **konfiguráció visszaállítása** panelen válassza ki **OK** a visszaállítási konfigurációjának véglegesítéséhez. Az a **visszaállítása** panelen válassza ki **visszaállítása** elindítani a visszaállítási műveletet.

## <a name="restore-backed-up-disks"></a>Lemezek biztonsági másolat visszaállítása
Állítsa vissza a típusú értékké **visszaállítási lemez** a **konfiguráció visszaállítása** panel lehetővé teszi a virtuális gép létrehozása a testre szabott konfigurációval. Miközben a lemezek visszaállítását, ki kell jelölni a Storage-fiók és Recovery services-tárolónak ugyanazon a helyen kell lennie. Ez nem kötelező a storage-fiók létrehozása, ha nem ugyanazon a helyen, és a Recovery Services-tárolónak a storage-fiókok. A ZRS tárfiókok nem támogatottak. Storage-fiók replikációs típusának zárójelben jelenik meg.

Közzététel a visszaállítási művelet, használja az alábbi:
* [A sablon használatával testre szabhatja a visszaállított virtuális Gépet](#use-templates-to-customize-restore-vm)
* [A visszaállított lemez használata a meglévő virtuális géphez csatolása](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Hozzon létre egy új virtuális gép helyreállított lemezekből a PowerShell használatával](./backup-azure-vms-automation.md#restore-an-azure-vm)

Az a **konfiguráció visszaállítása** panelen válassza ki **OK** a visszaállítási konfigurációjának véglegesítéséhez. Az a **visszaállítása** panelen válassza ki **visszaállítása** elindítani a visszaállítási műveletet.

![Helyreállítási konfiguráció befejeződött](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

A **hely visszaállítása** lapján történik **cserélje le a meglévő**.

## <a name="replace-existing-disks-from-a-restore-point"></a>Cserélje le a meglévő lemezek egy visszaállítási pont
**Cserélje le a meglévő** funkció lehetővé teszi az aktuális virtuális gép a meglévő lemezek lecseréli a kiválasztott helyreállítási pont. Ez a művelet csak elvégezhető, ha az aktuális virtuális gép létezik. Ha bármilyen okból miatt törölve lett, a művelet nem hajtható végre; azt is megteheti, javasoljuk, hogy **új létrehozása** virtuális gép vagy lemezek folytassa a visszaállítási műveleteket. Során cserélje le a meglévő operations (eke) t, óvintézkedésként, hogy az adatok biztonsági másolatának kezdeményezése a csere lemezek műveletek előtt. Ha a visszaállítási pont lemezzel rendelkezik. a több vagy kevesebb, mint a jelenlegi virtuális Gépet, majd a visszaállítási pont a lemezek száma csak a virtuális gépen fogja tartalmazni. **Cserélje le a meglévő** lehetőség jelenleg nem támogatja a nem felügyelt lemezek és virtuális gépek titkosítva. Azt is nem támogatja a [általánosított virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) és a használatával létrehozott virtuális gépek [egyéni rendszerképek](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).  

 Az a **konfiguráció visszaállítása** panelen csak az input, amelyet ki kell választani a **átmeneti hely**.

   ![Állítsa vissza a meglévő cserélje le a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **Visszaállítási típus**. Cserélje le, hogy a kijelölt visszaállítási pont lecseréli a meglévő virtuális gép (eke) t jelképező (eke) t.

 b. **Átmeneti hely**. Storage-fiókok az előkészítési hely számára a felügyelt lemezek. Ebben a menüben a storage-fiókok és a Recovery Services-tárolónak ugyanazon a helyen sorolja fel. Amelyek a zónaredundáns tárfiókok nem támogatottak. Ha nem ugyanazon a helyen, és a Recovery Services-tárolónak a storage-fiókok, létre kell hoznia egyet a visszaállítási művelet megkezdése előtt. A storage-fiók replikációs típusának zárójelben jelenik meg.

## <a name="track-the-restore-operation"></a>A visszaállítási művelet követése
Miután a visszaállítási műveletet indít el, a biztonsági mentési szolgáltatás létrehoz egy feladatot az nyomon követése a visszaállítási műveletet. A biztonsági mentési szolgáltatás is létrehoz, és átmeneti megjelenítése. az értesítés a **értesítések** területén a portálon. Ha nem látja az értesítési, válassza ki a **értesítések** szimbólumot az értesítések megtekintéséhez.

![Aktivált visszaállítása](./media/backup-azure-arm-restore-vms/restore-notification1.png)

A hivatkozásra, nyissa meg az értesítések **BackupJobs** listája. A részletekről a műveletek esetében egy adott feladat érhető el a **BackupJobs** sorolja fel. Megnyithatja **BackupJobs** válassza ki a tároló irányítópultján kattintson a biztonsági mentési feladatok csempe **Azure virtuális gép** a feladatok a tárolóhoz rendelt megjelenítéséhez.

A **biztonsági mentési feladatok** panel megnyitja és megjeleníti a feladatok listáját.

![A tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-completed.png)

## <a name="use-templates-to-customize-a-restored-vm"></a>A visszaállított virtuális gép testreszabása sablonok használatával
Miután a [lemezvisszaállítási műveletet elkészült](#Track-the-restore-operation), a sablon használatával létrehozott a visszaállítási művelet részeként létrehozhat egy új virtuális gép konfigurálása eltér a biztonsági mentési konfiguráció. Is használhatja azt egy új virtuális gép létrehozása egy helyreállítási pontról, a folyamat során létrehozott erőforrások neveit testreszabásához.

![Állítsa vissza a feladat részletes elemzés](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

A lemezeket a helyreállítás részeként létrehozott sablon lekérése:

1. Nyissa meg a **visszaállítási feladat részletei** megfelelő a feladatot.

2. Az a **visszaállítási feladat részletei** képernyőn válassza ki **üzembe helyezése sablon** sablon telepítésének kezdeményezéséhez.

3. Az a **üzembe helyezés sablon** irányuló sablon-üzembehelyezés használata egyéni üzembe helyezés panelen [szerkesztése és a sablon üzembe helyezéséhez](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) vagy hozzáfűzhetik a további testreszabásokat által [sablont készít](../azure-resource-manager/resource-group-authoring-templates.md) Mielőtt üzembe helyezi.

  ![Sablonalapú telepítés betöltése](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Miután megadta a szükséges értékeket, fogadja el a **feltételek és kikötések** válassza **beszerzési**.

  ![Küldje el a sablon telepítése](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>Visszaállítás utáni lépések
* Cloud-init-alapú Linux-disztribúciók, például az Ubuntu, a biztonsági okokból használja, ha blokkolva van-e a jelszó küldése a helyreállítás. A visszaállított virtuális gép a VMAccess bővítmény használatához [alaphelyzetbe állíthatja a jelszót](../virtual-machines/linux/reset-password.md). Azt javasoljuk, hogy SSH-kulcsokat használnak-e a disztribúciók alaphelyzetbe a jelszót utáni visszaállítást elkerülése érdekében.
* A biztonsági mentési konfiguráció során jelen bővítmények telepítve, de nem engedélyezett. Ha problémát észlel, telepítse újra a bővítményeket.
* Ha a biztonsági másolatban szereplő virtuális gép statikus IP-utáni helyreállítás, a visszaállított virtuális gép rendelkezik a visszaállított virtuális gép létrehozásakor ütközés elkerülése érdekében a dinamikus IP-cím. További információ arról, hogyan lehet [statikus IP-cím hozzáadása egy visszaállított virtuális Géphez](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* A visszaállított virtuális Gépet egy rendelkezésre állási értéke nem rendelkezik. Javasoljuk, hogy használja a restore lemezek lehetősége [hozzáadása egy rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md) létrehozásakor egy virtuális Gépet a PowerShell vagy a sablonok segítségével visszaállítani a lemezeket.


## <a name="backup-for-restored-vms"></a>A visszaállított virtuális gépek biztonsági mentése
Ha egy virtuális Gépet, a neve megegyezik az eredeti biztonsági másolatban szereplő virtuális gép ugyanabban az erőforráscsoportban állítja vissza, a virtuális gép utáni visszaállítást a biztonsági mentés továbbra is. Ha egy másik erőforráscsoportba visszaállította a virtuális gép vagy egy másik nevet a visszaállított virtuális Gépet a megadott, a virtuális gép, ha új virtuális gép kezeli. Biztonsági mentés beállítása a visszaállított virtuális Gépet kell.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Virtuális gép visszaállítása során egy Azure-adatközpont vészhelyreállítási
Az Azure Backup lehetővé teszi a biztonsági másolatban szereplő virtuális gépek visszaállítása a párosított adatközpontba, abban az esetben az elsődleges adatközpont, ahol a virtuális gépek futnak a katasztrófa, és a konfigurált a backup-tároló lehet georedundáns. Ilyen esetekben alatt válassza ki a storage-fiók, amely egy párosított adatközpontban található. A többi, a visszaállítási folyamat változatlan marad. Biztonsági mentés az a párosított földrajzi számítási szolgáltatását használja a visszaállított virtuális gép létrehozásához. További információkért lásd: [Azure-adatközpont rugalmasság](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Tartományvezérlő virtuális gépek visszaállítása
Biztonsági mentési tartományvezérlő (DC) virtuális gépek támogatott lehetőség a biztonsági mentés. Meg kell Ügyeljen azonban arra a visszaállítási folyamat során. A megfelelő visszaállítási folyamat attól függ, hogy a tartomány struktúráját. A legegyszerűbb esetben van egy egyetlen tartományvezérlő egyetlen tartományt. Leggyakrabban éles terhelés esetén rendelkezik egyetlen tartományt a több tartományvezérlők talán az egyes tartományvezérlők a helyszíni. Végül lehetséges, hogy egy erdő, több tartománnyal.

Az Active Directory szempontból az Azure virtuális gép olyan, mint bármely más virtuális Gépet egy modern támogatott hipervizoron. A helyszíni kiépítéshez a fő különbség a legyen VM-konzol nem érhető el az Azure-ban. A konzol bizonyos helyzetekben, például egy operációs rendszer nélküli helyreállítás (BMR) használatával helyreállításához szükséges – típusú biztonsági mentést. Viszont a virtuális gép visszaállítása biztonsági mentési tárolóból, operációs rendszer nélküli teljes értékű alternatívájaként. Directory címtárszolgáltatások helyreállító módjának (DSRM) is rendelkezésre áll, így minden Active Directory-helyreállítási forgatókönyvek is kivitelezhető. További információkért lásd: [virtualizált tartományvezérlők biztonsági mentési és helyreállítási szempontjai](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) és [tervezése az Active Directory-erdő helyreállítási](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Egyetlen tartományvezérlő tartományra
A virtuális gép az Azure Portalról vagy a PowerShell használatával (mint bármely más virtuális Gépet) állíthatók vissza.

### <a name="multiple-dcs-in-a-single-domain"></a>Ugyanazon tartományba több tartományvezérlők
Ha más tartományvezérlők ugyanannak a tartománynak a hálózaton keresztül érhető el, a tartományvezérlő például a virtuális Gépeket állíthatók vissza. Az utolsó megmaradt tartományvezérlő abban a tartományban, vagy a helyreállítás egy elkülönített hálózaton történik, ha egy erdő helyreállítási eljárást kell követnie.

### <a name="multiple-domains-in-one-forest"></a>Több erdő több tartomány
Ha más tartományvezérlők ugyanannak a tartománynak a hálózaton keresztül érhető el, a tartományvezérlő például a virtuális Gépeket állíthatók vissza. Minden más esetben javasoljuk, hogy egy erdő helyreállítási.

## <a name="restore-vms-with-special-network-configurations"></a>Speciális hálózati konfigurációval rendelkező virtuális gépek visszaállítása
Biztonsági mentéséhez és a virtuális gépek visszaállítása a következő speciális hálózati konfigurációval rendelkező lehetőség. Ezek a beállítások azonban szükség van az egyes különös figyelmet a visszaállítási folyamat során:

* Virtuális gépek terheléselosztók (belső és külső)
* Virtuális gépek több fenntartott IP-címmel
* Több hálózati adapterrel rendelkező virtuális gépek

> [!IMPORTANT]
> A speciális hálózati konfigurációt a virtuális gépek létrehozásakor a PowerShell hozhat létre virtuális gépeket a helyreállított lemezekből kell használnia.
>
>

Teljes körűen újra létre a virtuális gépek lemez visszaállítása után, kövesse az alábbi lépéseket:

1. Visszaállítás a lemezeket egy Recovery Services-tároló használatával [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

1. A virtuális gép konfigurációjához szükséges load Balancer létrehozása / több hálózati adapter és több fenntartott IP a PowerShell-parancsmagok használatával. Ezzel a konfigurációval, ha azt szeretné, a virtuális gép létrehozása:

   a. A felhőszolgáltatás a virtuális gép létrehozása egy [belső load balancer](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Hozzon létre egy virtuális Gépet szeretne csatlakozni egy [internetre irányuló terheléselosztó](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Egy virtuális gépet a [több hálózati adapterrel](../virtual-machines/windows/multiple-nics.md).

   d. Egy virtuális gépet a [több fenntartott IP-címek](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>További lépések
Most, hogy a virtuális gépek visszaállításához tanulmányozza a hibaelhárítási információk a virtuális gépek gyakori hibák: Emellett tekintse meg a cikk a feladatok a virtuális gépekkel kezelését.

* [Hibák elhárítása](backup-azure-vms-troubleshoot.md#restore)
* [Virtuális gépek kezelése](backup-azure-manage-vms.md)
