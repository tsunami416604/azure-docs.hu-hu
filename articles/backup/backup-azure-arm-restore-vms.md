---
title: 'Azure Backup: Virtuális gépek visszaállítása az Azure portal használatával'
description: Az Azure virtuális gép visszaállítása egy helyreállítási pontból az Azure portal használatával
services: backup
author: geethalakshmig
manager: vijayts
keywords: biztonsági másolatának visszaállítása hogyan lehet visszaállítani; helyreállítási pont;
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: geg
ms.openlocfilehash: 2253e729daedc3b130919913c1616449245f9cc1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315385"
---
# <a name="restore-azure-vms"></a>Azure-beli virtuális gépek visszaállítása

Ez a cikk ismerteti az Azure-beli Virtuálisgép-adatok visszaállítása a tárolt helyreállítási pontok [Azure Backup](backup-overview.md) Recovery Services-tárolók.

Visszaállítása egy virtuális gép győződjön meg arról, hogy rendelkezik a szükséges [RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) engedéllyel.

> [!NOTE]
> Ha nem rendelkezik [RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) engedély hajthat végre [lemez visszaállítása](backup-azure-arm-restore-vms.md#create-new-restore-disks) , és hozzon létre virtuális gépet [-sablon telepítése](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) funkció.

### <a name="restore-options"></a>Visszaállítási beállítások

Az Azure Backup a virtuális gép visszaállítása többféleképpen biztosít.

**Visszaállítási lehetőség** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | Gyorsan hoz létre, és lekér egy egyszerű virtuális Gépet és a egy visszaállítási pontot.<br/><br/> Most adja meg a virtuális gép nevét, válassza ki az erőforráscsoportot és a virtuális hálózat (VNet), amelyben kerül, és adja meg a tárolás típusát.
**Lemez visszaállítása** | Visszaállít egy Virtuálisgép-lemez, amely majd használható új virtuális gép létrehozásához.<br/><br/> Az Azure Backup segítségével testre szabhatja, és hozzon létre egy virtuális Gépet sablonból biztosít. <br/><br/> Ezt a lehetőséget, adja meg a tárfiók VHD-k másolja. A visszaállítási feladat egy sablont, hogy letöltheti és adhatja meg az egyéni virtuális gép beállításait, és hozzon létre egy virtuális Gépet hoz létre.<br/><br/> – A storage-fiók és a tárolónak ugyanazon a helyen kell lennie. Ha nem rendelkezik ilyennel, hozzon létre egy tárfiókot.<br/><br/> A tárfiók replikációs típusa jelenik meg. Zónaredundáns tárolás (ZRS) nem támogatott.<br/><br/> Azt is megteheti a lemez csatolása meglévő virtuális géphez, vagy hozzon létre egy új virtuális Gépet PowerShell-lel.<br/><br/> Ez a beállítás akkor hasznos, ha a virtuális gép testreszabása, adja hozzá a konfigurációs beállítások, amelyek nem létezik a biztonsági mentés idején vagy adja hozzá a beállítások a sablonban vagy a PowerShell használatával kell konfigurálni kívánja.
**Cserélje le a meglévő** | Lemez visszaállítása és kicserél egy lemezt a meglévő virtuális gép használatával.<br/><br/> Az aktuális virtuális gép már léteznie kell. Ha már törölték a beállítás nem használható.<br/><br/> Az Azure biztonsági mentési pillanatképet készít a meglévő virtuális gép a lemez cseréje előtt. A pillanatkép, adja meg az átmeneti helyen tárolja. A virtuális géphez csatlakoztatott lemezek majd cseréje a kiválasztott helyreállítási pont használatával. A pillanatkép, amely hibaállapota-tárolóba másol és őrzi meg a megadott adatmegőrzési szabályzatának megfelelően.<br/><br/> Cserélje le a meglévő nem titkosított felügyelt virtuális gépek esetén támogatott. Nem felügyelt lemezek esetén nem támogatott [általánosított virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), vagy a virtuális gépek [egyéni rendszerképekből létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Ha a visszaállítási pont több vagy kevesebb, mint a jelenlegi virtuális lemezeket, majd a visszaállítási pont a lemezek száma csak a Virtuálisgép-konfigurációt fogja tartalmazni.

> [!NOTE]
> Meghatározott fájlokat és mappákat egy Azure-beli virtuális gépen is helyreállíthatja. [További információk](backup-azure-restore-files-from-vm.md).
>
> Ha futtatja a [legújabb verzió](backup-instant-restore-capability.md) az Azure Backup az Azure virtuális gépek (más néven azonnali visszaállítása), a pillanatképek őrzi meg a hét napig, és visszaállíthatja egy virtuális Gépet a pillanatképek a biztonsági mentési adatok összmennyiségét elküldése előtt. Ha szeretne egy virtuális gép visszaállítása biztonsági másolatból, az elmúlt hét nap, gyorsabb és visszaállíthatja a pillanatkép, és nem a tárolóból.


## <a name="select-a-restore-point"></a>Egy visszaállítási pont kijelölése

1. Kattintson a tárolóban, a virtuális Géphez visszaállítandó társított **biztonsági mentési elemek** > **Azure virtuális gép**.
2. Kattintson a virtuális gép. A virtuális gép irányítópulton alapértelmezés szerint az elmúlt 30 napban a helyreállítási pontok jelennek meg. Helyreállítási pontok régebbi, mint 30 napig, vagy keresse meg a helyreállítási pontok alapján a dátumok, időintervallumok és pillanatkép konzisztencia különböző típusú szűrő jeleníthet meg.
3. A virtuális gép visszaállításához kattintson **virtuális gép visszaállítása**.

    ![Visszaállítási pont](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Válassza ki a helyreállításhoz használható egy visszaállítási pontot.

## <a name="choose-a-vm-restore-configuration"></a>Válasszon egy Virtuálisgép-konfiguráció visszaállítása

1. A **konfiguráció visszaállítása**, válasszon egy visszaállítási beállítást:
    - **Új létrehozása**. Használja ezt a beállítást, ha szeretne létrehozni egy új virtuális Gépet. Hozzon létre egy virtuális gép egyszerű beállításokkal, vagy a lemez visszaállítása és a egy egyéni virtuális gép létrehozása.
    - **Cserélje le a meglévő**: Használja ezt a beállítást, ha azt szeretné, cserélje le egy meglévő virtuális Gépen található lemezek.

        ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Adja meg a kiválasztott helyreállítási lehetőség beállításait.

## <a name="create-new-create-a-vm"></a>Hozzon létre új – a virtuális gép létrehozása

Egyik a [visszaállítási lehetőségek](#restore-options), egy virtuális gép gyorsan hozhat létre, egy visszaállítási pont alapvető beállításokkal.

1. A **konfiguráció visszaállítása** > **új létrehozása** > **visszaállítási típus**válassza **hozzon létre egy virtuális gépet** .
2. A **virtuális gép neve**, adjon meg egy virtuális Gépet, amely nem létezik az előfizetésben.
3. A **erőforráscsoport**, válassza ki az új virtuális gép egy meglévő erőforráscsoportot vagy hozzon létre egy újat egy globálisan egyedi névvel. Ha olyan nevet, amely már létezik, az Azure hozzárendeli a csoport neve megegyezik a virtuális gép.
4. A **virtuális hálózati**, válassza ki a virtuális hálózatot, amelyben a virtuális Gépet helyez el. Az előfizetéshez tartozó összes virtuális hálózatok jelennek meg. Válassza ki az alhálózatot. Alapértelmezés szerint az első alhálózat van kiválasztva.
5. A **tárolási hely**, adja meg a tárolási típust a virtuális gép számára.

    ![Állítsa vissza a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. A **konfiguráció visszaállítása**válassza **OK**. A **visszaállítása**, kattintson a **visszaállítása** elindítani a visszaállítási műveletet.


## <a name="create-new-restore-disks"></a>Hozzon létre új helyreállítási lemezek

Egyik a [visszaállítási lehetőségek](#restore-options), létrehozhat egy lemezt egy helyreállítási pontról. Ezután a lemezzel teheti az alábbi lehetőségek közül:

- A sablon használata, amely akkor jön létre, a beállításokat, és aktiválja a virtuális gép üzembe helyezése a visszaállítási művelet során. Szerkesztheti az alapértelmezett sablon beállításait, és küldje el a sablont a Virtuálisgép-telepítéshez.
- [A visszaállított lemezt](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) meglévő virtuális géphez.
- [Hozzon létre egy új virtuális Gépet](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a PowerShell használatával a helyreállított lemezek alapján.

1. A **konfiguráció visszaállítása** > **új létrehozása** > **visszaállítási típus**válassza **lemezek visszaállítása**.
2. A **erőforráscsoport**, válassza ki a visszaállított lemez egy meglévő erőforráscsoportot vagy hozzon létre egy újat egy globálisan egyedi névvel.
3. A **tárfiók**, adja meg a fiókot, amelyhez a VHD-k másolása. Ellenőrizze, hogy a fiók és a tárolónak ugyanabban a régióban.

    ![Helyreállítási konfiguráció befejeződött](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. A **konfiguráció visszaállítása**válassza **OK**. A **visszaállítása**, kattintson a **visszaállítása** elindítani a visszaállítási műveletet.

### <a name="use-templates-to-customize-a-restored-vm"></a>A visszaállított virtuális gép testreszabása sablonok használatával

A lemez visszaállítása után a sablon segítségével létrehozott a visszaállítási művelet részeként testre szabhatja, és hozzon létre egy új virtuális Gépet:

1. Nyissa meg **visszaállítási feladat részletei** a megfelelő feladat.

2. A **visszaállítási feladat részletei**válassza **-sablon telepítése** sablon telepítésének kezdeményezéséhez.

    ![Állítsa vissza a feladat részletes elemzés](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. A virtuális gép beállítása a sablonban megadott testreszabásához kattintson **szerkesztési sablon**. Ha szeretne további testreszabásokat, kattintson a **paraméterek szerkesztése**.
    - [További](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) erőforrások egyéni sablonból való telepítéséről.
    - [További](../azure-resource-manager/resource-group-authoring-templates.md) kapcsolatos sablonok készítése.

   ![Sablonalapú telepítés betöltése](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Adja meg az egyéni értékek a virtuális gép, és fogadja el a **feltételek és kikötések** kattintson **beszerzési**.

   ![Küldje el a sablon telepítése](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Cserélje le a meglévő lemezek

Egyik a [visszaállítási lehetőségek](#restore-options), egy meglévő Virtuálisgép-lemez lecserélheti a kiválasztott helyreállítási pont. [Felülvizsgálat](#restore-options) minden visszaállítási lehetőségek.

1. A **konfiguráció visszaállítása**, kattintson a **cserélje le a meglévő**.
2. A **visszaállítási típus**válassza **cserélje le a lemez-vagy HTTPS**. Ez az a visszaállítási pont, amely lesz a meglévő virtuális gép lemezeinek használt cseréje.
3. A **átmeneti hely**, adja meg, ahol az aktuális felügyelt lemezek pillanatképei menteni.

   ![Állítsa vissza a meglévő cserélje le a konfigurációs varázsló](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>Virtuális gépek visszaállítása a speciális konfigurációk

Számos gyakori helyzetek, amelyekben szüksége lehet a virtuális gépek visszaállítása.

**Forgatókönyv** | **Útmutatás**
--- | ---
**Hybrid Use Benefit használatával virtuális gépek visszaállítása** | Ha egy Windows virtuális gép használ [hibrid használati Benefit (HUB) licencelési](../virtual-machines/windows/hybrid-use-benefit-licensing.md), állítsa vissza a lemezeket, és hozzon létre egy új virtuális Gépet a megadott sablon használatával (a **licenctípus** beállítása **Windows_Server**) , vagy a PowerShell használatával.  Ez a beállítás a virtuális gép létrehozása után is alkalmazható.
**Virtuális gépek visszaállítása az Azure-adatközpont katasztrófa során** | Ha a tárolót használja a GRS, és a virtuális gép az elsődleges adatközpont leáll, az Azure Backup támogatja visszaállítása biztonsági másolat virtuális gépeket a párosított adatközpontba. Válassza ki a tárfiókot a párosított adatközpontban, és állítsa vissza a szokásos módon. Az Azure Backup a párosított helyen a számítási szolgáltatás használatával a visszaállított virtuális gép létrehozásához. [További](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) adatközpont rugalmasságára vonatkozó.
**Egyetlen tartományvezérlőt egy tartományban lévő virtuális gép visszaállítása** | Állítsa vissza a virtuális Gépet, mint bármely más virtuális Gépet. Vegye figyelembe:<br/><br/> az Active Directory-perspektíva ROM, az Azure virtuális gép olyan, mint bármely más virtuális Gépet.<br/><br/> Directory címtárszolgáltatások helyreállító módjának (DSRM) is rendelkezésre áll, így minden Active Directory-helyreállítási forgatókönyvek is kivitelezhető. [További](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) kapcsolatos virtualizált tartományvezérlők biztonsági mentési és helyreállítási szempontjai.
**Több tartományvezérlőt egy tartományban lévő virtuális gépek visszaállítása** | f a hálózaton keresztül elérhető más tartományvezérlők azonos tartományban, a tartományvezérlő is lehet visszaállítani, például a virtuális Gépeket. A tartomány az utolsó megmaradt tartományvezérlő, vagy a helyreállítás egy elkülönített hálózaton történik, ha egy [helyreállítási erdő](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Több erdő több tartomány visszaállítása** | Javasoljuk, hogy egy [helyreállítási erdő](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Operációs rendszer nélküli helyreállítás** | A fő különbség az Azure-beli és helyszíni kiépítéshez között, hogy nincs egyetlen VM-konzol az Azure-ban érhető el. A konzol bizonyos helyzetekben, például egy operációs rendszer nélküli helyreállítás (BMR) használatával helyreállításához szükséges – típusú biztonsági mentést. Viszont a virtuális gép visszaállítása a tárból, operációs rendszer nélküli teljes értékű alternatívájaként.
**Speciális hálózati konfigurációval rendelkező virtuális gépek visszaállítása** | Speciális hálózati konfigurációk közé tartozik a virtuális gépek belső vagy külső load terheléselosztást, a több hálózati Adapterrel, vagy több fenntartott IP-cím használatával. Ezek a virtuális gépek használatával állítsa vissza a [lemez beállítást vissza](#create-new-restore-disks). Ez a beállítás a virtuális merevlemezeket másolatot készít a megadott tárfiókba, és, létrehozhat egy virtuális Gépet egy [belső](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) vagy [külső](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) terheléselosztó, [több hálózati adapter](../virtual-machines/windows/multiple-nics.md), vagy [több fenntartott IP-címek](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), a konfiguráció megfelelően.
**Hálózati biztonsági csoport (NSG) a hálózati adapter/alhálózat** | Az Azure virtuális gépek biztonsági mentését támogatja a biztonsági mentés és visszaállítás az NSG információkat a virtuális hálózat, alhálózat és hálózati adapterek szintjén.

## <a name="track-the-restore-operation"></a>A visszaállítási művelet követése
Miután a visszaállítási műveletet indít el, a biztonsági mentési szolgáltatás létrehoz egy feladatot az nyomon követése. Az Azure Backup a feladattal kapcsolatos értesítéseket a portál jeleníti meg. Ha nem látható, kattintson a **értesítések** szimbólum megtekintheti őket.

![Aktivált visszaállítása](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Visszaállítás nyomon követheti a következőképpen:

1. A feladat operations megtekintéséhez kattintson az értesítések hivatkozás. Másik lehetőségként a tárolóban kattintson **biztonsági mentési feladatok**, majd kattintson a megfelelő virtuális Gépet.

    ![A tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Visszaállítási folyamat állapotának monitorozásához kattintson bármely visszaállítási feladat állapottal **folyamatban**. Ekkor megjelenik a folyamatjelző sáv, amely a visszaállítási folyamat információit jeleníti meg:

    - **Becsült idő a visszaállítás**: Kezdetben biztosít a visszaállítási művelet végrehajtásához szükséges idő. A művelet futtatása során, az igénybe vett idő csökkenti és nullára a visszaállítás befejeztével.
    - **Visszaállítás százaléka**. A visszaállítási művelet által elvégzett százalékát jeleníti meg.
    - **Az átvitt bájtok száma**: Ha Ön visszaállítás, hozzon létre egy új virtuális Gépet, a továbbított bájtok teljes száma alapján átvitt bájtok jeleníti meg.

## <a name="post-restore-steps"></a>Visszaállítás utáni lépések

Számos virtuális gép visszaállítása után jegyezze fel az alábbiakkal:

- Jelen a biztonsági mentés konfigurálása során bővítmények telepítve van, de nincs engedélyezve. Ha problémát észlel, telepítse újra a bővítményeket.
- Ha a biztonsági másolatban szereplő virtuális gép statikus IP-címet, akkor a visszaállított virtuális Gépet ütközés elkerülése érdekében a dinamikus IP-cím lesz. Is [statikus IP-címet ad hozzá a visszaállított virtuális Gépet](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- A visszaállított virtuális Gépet egy rendelkezésre állási nincs beállítva. Ha a szervezeti egység használja a helyreállítás lemez Önnek is [adja meg a rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md) Amikor létrehozta a virtuális Gépet a lemezről a megadott sablonban vagy a PowerShell használatával.
- Ha cloud-init-alapú Linux-disztribúciók, például az Ubuntu, a biztonsági okokból a jelszót a visszaállítás után le van tiltva. A visszaállított virtuális gép a VMAccess bővítmény használatához [alaphelyzetbe állíthatja a jelszót](../virtual-machines/linux/reset-password.md). Azt javasoljuk, hogy SSH-kulcsokat használnak-e a disztribúciók, így nem kell a jelszót a visszaállítás után.


## <a name="backing-up-restored-vms"></a>A visszaállított virtuális gépek biztonsági mentésének

- Ha egy virtuális Gépet, visszaállította a neve megegyezik az eredeti biztonsági másolatban szereplő virtuális gép ugyanabban az erőforráscsoportban, biztonsági mentés visszaállítása után továbbra is a virtuális gépen.
- Ha állítja vissza, a virtuális gép egy másik erőforráscsoportba, vagy egy másik nevet a visszaállított virtuális Gépet a megadott, szüksége a visszaállított virtuális gép biztonsági másolat beállítása.


## <a name="next-steps"></a>További lépések

- A visszaállítási folyamat során tapasztal nehézségek [tekintse át](backup-azure-vms-troubleshoot.md#restore) gyakori problémákat és hibákat.
- A virtuális gép visszaállítása után ismerje [virtuális gépek kezelése](backup-azure-manage-vms.md)
