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
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793376"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Virtuális gépek visszaállítása az Azure portal használatával
Védje adatait az adatpillanatképek meghatározott időközönként végrehajtásával. Ezek a pillanatképek a helyreállítási pontok nevezzük, és a Recovery Services-tárolók tárolásuk. Szükség esetén javítsa ki vagy építse újra a virtuális gép (VM), visszaállíthatja a virtuális gép bármelyik mentett helyreállítási pontokat. Egy helyreállítási pontot állítja vissza, ha Ön a következőket teheti:

* Hozzon létre egy új virtuális Gépet: Helyreállítási pontról történő időponthoz – a biztonsági másolatban szereplő virtuális gép.
* Lemezek visszaállítása: A sablon használata, amely a folyamat testreszabása a visszaállított virtuális Gépet tartalmaz, vagy hajtsa végre az egyes fájlok helyreállítása.

Ez a cikk ismerteti a virtuális gép visszaállítása egy új virtuális géphez, vagy az összes biztonsági másolat lemez visszaállítása. Az egyes fájlhelyreállítás, lásd: [fájlok helyreállítása az Azure VM backup](backup-azure-restore-files-from-vm.md).

![Háromféle lehetőség a virtuális gép biztonsági másolat visszaállítása](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


Virtuális gép és az összes lemez visszaállítása virtuális gépről biztonsági másolat két lépésből áll:

* Válassza ki a visszaállítási pontot a visszaállításhoz.
* A helyreállítás típusának kiválasztása
    - 1. lehetőség: Új virtuális gép létrehozása
    - 2. lehetőség: Állítsa vissza a lemezeket.

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

    - Segítségével történő visszaállításhoz egy visszaállítási pont az elmúlt 30 napban, kattintson a jobb gombbal a virtuális gép > **virtuális gép visszaállítása**.
    - Segítségével történő visszaállításhoz 30 napnál régebbi visszaállítási pontot, kattintson a hivatkozásra kattintva **visszaállítási pontok**.
    - Listázása és testre szabott dátummal rendelkező virtuális gépek szűréséhez kattintson a **virtuális gép visszaállítása** menüjében. A szűrő használatával módosítsa a megjelenített visszaállítási pontok időintervallumát. A különböző típusú adatok konzisztenciájának is végezhet.
8. Tekintse át a helyreállítási pont beállításait:
    - Adatok konzisztencia jeleníti meg a [konzisztencia típusú](backup-azure-vms-introduction.md#consistency-types) a helyreállítási pont.
    - A **helyreállítási típus** jeleníti meg, a pont tárolására (a storage-fiókban, a tároló vagy mindkettőben. [További](https://azure.microsoft.com/blog/large-disk-support/) azonnali helyreállítási pontok.

  ![Visszaállítási pontok](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Válasszon ki egy visszaállítási pontot.

10. Válassza ki **konfiguráció visszaállítása**. A **konfiguráció visszaállítása** panel nyílik meg.

## <a name="choose-a-vm-restore-configuration"></a>Válasszon egy Virtuálisgép-konfiguráció visszaállítása
Miután kiválasztotta a visszaállítási pontot, válassza ki a Virtuálisgép-konfiguráció visszaállítása. Konfigurálhatja a visszaállított virtuális Gépet, használhatja az Azure portal vagy a PowerShell.

### <a name="restore-options"></a>Visszaállítási beállítások

**Beállítás** | **Részletek**
--- | ---
**Hozzon létre új – virtuális gép létrehozása** | A virtuális gépek gyors létrehozás egyenértékű. Lekérdezi egy alapszintű virtuális gép és egy helyreállítási pontról.<br/><br/> A virtuális gép beállításai módosíthatók a visszaállítási folyamat részeként.
**Visszaállítás új lemez létrehozása** | Létrehoz egy virtuális Gépet, testre szabható a visszaállítási folyamat részeként.<br/><br/> Ezt a lehetőséget, adja meg a tárfiók VHD-k másolja.<br/><br/> – A storage-fiók és a tárolónak ugyanazon a helyen kell lennie.<br/><br/> Ha nem rendelkezik egy megfelelő tárfiókot létre kell hoznia egyet.<br/><br/> A tárfiók replikációs típusa zárójelben látható. Zónaredundáns tárolás (ZRS) nem támogatott.<br/><br/> A storage-fiókból is a visszaállított lemez csatolása meglévő virtuális géphez, vagy hozzon létre egy új virtuális Gépet a PowerShell használatával a helyreállított lemezek alapján.
**Cserélje le a meglévő** | Ez a beállítás nem kell egy virtuális gép létrehozása.<br/><br/> Az aktuális virtuális gép már léteznie kell. Ha már törölték a beállítás nem használható.<br/><br/> Az Azure Backup egy pillanatképet készít a meglévő virtuális Gépet, és azt a megadott átmeneti hely tárolja. A meglévő lemezek, a virtuális gép csatlakozik majd a kiválasztott helyreállítási pont helyén. A korábban létrehozott pillanatkép-tárolóba másol, és a biztonsági mentési adatmegőrzési szabályzatának megfelelően helyreállítási pontként tárol.<br/><br/> Cserélje le a meglévő nem titkosított felügyelt virtuális gépek esetén támogatott. Nem felügyelt lemezek esetén nem támogatott [általánosított virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), vagy a virtuális gépek [egyéni rendszerképekből létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Ha a visszaállítási pont több vagy kevesebb, mint a jelenlegi virtuális lemezeket, majd a visszaállítási pont a lemezek száma csak a virtuális gép fogja tartalmazni.

> [!NOTE]
> Ha szeretné visszaállítani a virtuális gép speciális hálózati beállítások, például ha egy belső vagy külső terheléselosztó felügyeli, vagy több hálózati adapterrel, vagy több fenntartott IP-címek visszaállítása a PowerShell használatával. [További információk](#restore-vms-with-special-network-configurations).
> Ha egy Windows virtuális gép használ [központi licencelési](../virtual-machines/windows/hybrid-use-benefit-licensing.md), használja a **hozzon létre új visszaállítási lemez** lehetőséget, majd ezután használhatja a PowerShell vagy a visszaállítási sablon hozhat létre a virtuális Gépet a **licenctípus** beállítása **Windows_Server**. Ez a beállítás a virtuális gép létrehozása után is alkalmazható.


Adja meg a visszaállítási beállítást az alábbiak szerint:

1. A **visszaállítása**, jelölje be a [visszaállítási pont](#select-a-restore-point-for-restore) > **konfiguráció visszaállítása**.
2. A **konfiguráció visszaállítása**válassza ki, hogyan szeretné visszaállítani a virtuális gép megfelelően a fenti táblázatban összefoglalt beállításokat.

    ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Hozzon létre új – a virtuális gép létrehozása

1. A **konfiguráció visszaállítása** > **új létrehozása** > **visszaállítási típus**válassza **hozzon létre egy virtuális gépet** .
2. A **virtuális gép neve**, adjon meg egy virtuális Gépet, amely nem létezik az előfizetésben.
3. A **erőforráscsoport**, válassza ki az új virtuális gép egy meglévő erőforráscsoportot vagy hozzon létre egy újat egy globálisan egyedi névvel. Rendeljen egy nevet, már meglévő, ha az Azure hozzárendeli a csoport neve megegyezik a virtuális Gépet.
4. A **virtuális hálózati**, válassza ki a virtuális hálózatot, amelyben a virtuális Gépet helyez el. Az előfizetéshez tartozó összes virtuális hálózatok jelennek meg. Válassza ki az alhálózatot. Alapértelmezés szerint az első alhálózat van kiválasztva.
5. A **tárolási hely**, adja meg a tárolási típust, a virtuális gép használja.

    ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. A **konfiguráció visszaállítása**válassza **OK**. A **visszaállítása**, kattintson a **visszaállítása** elindítani a visszaállítási műveletet.



## <a name="create-new-restore-disks"></a>Hozzon létre új helyreállítási lemezek

1. A **konfiguráció visszaállítása** > **új létrehozása** > **visszaállítási típus**válassza **lemezek visszaállítása**.
2. A **erőforráscsoport**, válassza ki a visszaállított lemez egy meglévő erőforráscsoportot vagy hozzon létre egy újat egy globálisan egyedi névvel.
3. A **tárfiók**, adja meg a fiókot, amelyhez a VHD-k másolása. Ellenőrizze, hogy a fiók és a tárolónak ugyanabban a régióban.

    ![Helyreállítási konfiguráció befejeződött](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. A **konfiguráció visszaállítása**válassza **OK**. A **visszaállítása**, kattintson a **visszaállítása** elindítani a visszaállítási műveletet.
5. A lemez visszaállítása után a virtuális gép visszaállítási művelet végrehajtásához a következők bármelyikét teheti.

    - A visszaállítási művelet részeként létrejött sablon használatával testre szabhatja a beállításokat, és aktiválja a virtuális gép üzembe helyezése. Szerkesztheti az alapértelmezett sablon beállításait, és küldje el a sablont a Virtuálisgép-telepítéshez.
    - Is [a visszaállított lemezt](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) meglévő virtuális géphez.
    - Is [hozzon létre egy új virtuális Gépet](backup-azure-vms-automation.md#restore-an-azure-vm) a PowerShell használatával a helyreállított lemezek alapján.


## <a name="replace-existing-disks"></a>Cserélje le a meglévő lemezek

Ez a beállítás használatával az aktuális virtuális gép a meglévő lemezek cserélje le a kiválasztott helyreállítási pont.

1. A **konfiguráció visszaállítása**, kattintson a **cserélje le a meglévő**.
2. A **visszaállítási típus**válassza **cserélje le a lemez-vagy HTTPS** (a visszaállítási pont, amely lecseréli a meglévő Virtuálisgép-lemez vagy lemezeket).
3. A **átmeneti hely**, adja meg, ahol az aktuális felügyelt lemezek pillanatképei menteni.

   ![Állítsa vissza a meglévő cserélje le a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>A visszaállítási művelet követése
Miután a visszaállítási műveletet indít el, a biztonsági mentési szolgáltatás létrehoz egy feladatot az nyomon követése a visszaállítási műveletet. A biztonsági mentési szolgáltatás is létrehoz, és átmeneti megjelenítése. az értesítés a **értesítések** területén a portálon. Ha nem látja az értesítési, válassza ki a **értesítések** szimbólumot az értesítések megtekintéséhez.

![Aktivált visszaállítása](./media/backup-azure-arm-restore-vms/restore-notification1.png)

A hivatkozásra, nyissa meg az értesítések **BackupJobs** listája. A részletekről a műveletek esetében egy adott feladat érhető el a **BackupJobs** sorolja fel. Megnyithatja **BackupJobs** válassza ki a tároló irányítópultján kattintson a biztonsági mentési feladatok csempe **Azure virtuális gép** a feladatok a tárolóhoz rendelt megjelenítéséhez.

A **biztonsági mentési feladatok** panel megnyitja és megjeleníti a feladatok listáját.

![A tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

**Folyamatjelző sáv** elérhető a **visszaállítása részletek** panelen. A **visszaállítása részletek** panel bármely visszaállítási feladat, amelynek állapota kattintva megnyitható **folyamatban**. A **folyamatjelző sáv** érhető el a visszaállítások például minden változat **hozzon létre új**, **lemez visszaállítása** és **cserélje le a meglévő**. A visszaállítás folyamatjelző által végzett részletei **becsült idő a visszaállítás**, **álló visszaállítási** és **átvitt bájtok száma**.

Állítsa vissza a folyamatjelző sáv részletei az alábbiakban:

- **Becsült idő a visszaállítás** kezdetben biztosít a visszaállítási művelet végrehajtásához szükséges idő. A művelet futtatása során, az igénybe vett idő csökkenti, és egyszer eléri a 0 a visszaállítási művelet befejeződik.
- **Visszaállítás százaléka** biztosít az adatok hány százalékát visszaállítási művelet befejeződött.
- **Az átvitt bájtok száma** érhető el a részfeladat Ha visszaállítási hozzon létre új virtuális Gépen keresztül történik. Ez lehetővé teszi, hogy hány számú bájt részleteit átruházták átvinni bájtok teljes száma alapján.


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
