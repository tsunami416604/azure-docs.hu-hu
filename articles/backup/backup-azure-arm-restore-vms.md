---
title: Virtuális gépek visszaállítása az Azure Portal használatával
description: Azure-beli virtuális gép visszaállítása helyreállítási pontról az Azure Portal használatával
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: b9cdb187aa3b8750bead8e81ad6d0ee50dcb3d6c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254920"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Az Azure VM-adatok visszaállítása az Azure Portalon

Ez a cikk ismerteti, hogyan állíthatja vissza az Azure virtuális gép adatait az [Azure Backup](backup-overview.md) Recovery Services tárolókban tárolt helyreállítási pontokról.

## <a name="restore-options"></a>Visszaállítási lehetőségek

Az Azure Backup számos lehetőséget kínál a virtuális gép visszaállítására.

**Visszaállítási beállítás** | **Részletek**
--- | ---
**Hozzon létre egy új virtuális gépet** | Gyorsan létrehoz egy egyszerű virtuális gép, és fut egy visszaállítási pontról.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelyben elhelyezi, és megadhat egy tárfiókot a visszaállított virtuális géphez. Az új virtuális gép kell létrehozni ugyanabban a régióban, mint a forrás virtuális gép.
**Lemez visszaállítása** | Visszaállít egy virtuális gép lemez, amely ezután egy új virtuális gép létrehozásához használható.<br/><br/> Az Azure Backup egy sablont biztosít a virtuális gép testreszabásához és létrehozásához. <br/><br> A visszaállítási feladat létrehoz egy sablont, amely letöltheti és használhatja az egyéni virtuális gép beállításainak megadásához, és hozzon létre egy virtuális gép.<br/><br/> A lemezek et a program a megadott erőforráscsoportba másolja.<br/><br/> Másik lehetőségként csatolja a lemezt egy meglévő virtuális géphez, vagy hozzon létre egy új virtuális gép powershell használatával.<br/><br/> Ez a beállítás akkor hasznos, ha testre szeretné szabni a virtuális gép, adja meg a konfigurációs beállításokat, amelyek nem voltak ott a biztonsági mentés idején, vagy adja hozzá a beállításokat, amelyeket a sablon vagy a PowerShell használatával kell konfigurálni.
**Meglévő cseréje** | Visszaállíthatja a lemezt, és a meglévő virtuális gép en lévő lemez lecserélésére használhatja.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha törölték, ez a beállítás nem használható.<br/><br/> Az Azure Backup pillanatképet készít a meglévő virtuális gépről a lemez cseréje előtt, és a megadott átmeneti helyen tárolja. A virtuális géphez csatlakoztatott meglévő lemezeket a kijelölt visszaállítási pont váltja fel.<br/><br/> A pillanatkép a tárolóba kerül, és az adatmegőrzési szabályzatnak megfelelően marad meg. <br/><br/> A lemez csereművelete után az eredeti lemez megmarad az erőforráscsoportban. Ha nincs rájuk szükség, manuálisan törölheti az eredeti lemezeket. <br/><br/>A meglévő csere a titkosítatlan felügyelt virtuális gépek, beleértve az [egyéni lemezképek használatával létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)virtuális gépek et is. A klasszikus virtuális gépek nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezt, mint az aktuális virtuális gép, majd a lemezek száma a visszaállítási pont csak a virtuális gép konfigurációját.<br><br> A meglévő csere nem támogatott a csatolt erőforrásokkal (például [a felhasználó által hozzárendelt felügyelt identitásvagy](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) [a Key Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)rendelkező virtuális gépek esetében, mert a biztonsági mentési ügyfélalkalmazás nem rendelkezik engedélyekkel ezekhez az erőforrásokhoz a visszaállítás végrehajtása közben.
**Régióközi (másodlagos régió)** | A régiók közötti visszaállítás segítségével visszaállíthatja az Azure virtuális gépeket a másodlagos régióban, amely egy [Azure-ban párosított régió.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Visszaállíthatja az összes Azure virtuális gépek a kiválasztott helyreállítási pont, ha a biztonsági mentés a másodlagos régióban történik.<br><br> Ez a funkció az alábbi lehetőségekhez érhető el:<br> * [Virtuális gép létrehozása](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Lemezek visszaállítása](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Jelenleg nem támogatjuk a [Meglévő lemezek cseréje](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) lehetőséget.<br><br> Engedélyek<br> A másodlagos régióvisszaállítási műveletet a biztonsági mentés i. rendszergazdák és az alkalmazásadminisztrátorok végezhetik el.

> [!NOTE]
> Az Azure-beli virtuális gépeken adott fájlokat és mappákat is helyreállíthatja. [További információ](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Tárfiókok

A tárfiókok néhány részlete:

- **Virtuális gép létrehozása:** Amikor új virtuális gép létrehozása, a virtuális gép kerül a megadott tárfiókba.
- **Lemez visszaállítása:** Lemez visszaállításakor a lemez a megadott tárfiókba kerül. A visszaállítási feladat létrehoz egy sablont, amely letölthető és egyéni virtuális gép beállításainak megadásához használható. Ez a sablon a megadott tárfiókba kerül.
- **Lemez cseréje:** Ha egy meglévő virtuális gép lemezét cseréli le, az Azure Backup pillanatképet készít a meglévő virtuális gépről a lemez cseréje előtt. A pillanatkép a megadott átmeneti helyen (tárfiók) tárolódik. Ez a tárfiók a visszaállítási folyamat során a pillanatkép ideiglenes tárolására szolgál, és azt javasoljuk, hogy hozzon létre egy új fiókot ehhez, amely később könnyen eltávolítható.
- **Tárfiók helye:** A tárfióknak ugyanabban a régióban kell lennie, mint a tároló. Csak ezek a fiókok jelennek meg. Ha nincsenek tárfiókok a helyen, létre kell hoznia egyet.
- **Tárolási típus**: Blob storage nem támogatott.
- **Tárolási redundancia**: A zónaredundáns tárolás (ZRS) nem támogatott. A fiók replikációs és redundanciaadatai a fiók neve utáni zárójelben jelennek meg.
- **Prémium szintű tárolás:**
  - Nem prémium szintű virtuális gépek visszaállításakor a prémium szintű tárfiókok nem támogatottak.
  - Felügyelt virtuális gépek visszaállításakor a hálózati szabályokkal konfigurált prémium szintű tárfiókok nem támogatottak.

## <a name="before-you-start"></a>Előkészületek

Virtuális gép visszaállításához (hozzon létre egy új virtuális gép), győződjön meg arról, hogy rendelkezik a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) [engedélyekkel](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a virtuális gép visszaállítása művelethez.

Ha nem rendelkezik engedélyekkel, [visszaállíthatja a lemezt](#restore-disks), majd a lemez visszaállítása után [használhatja a](#use-templates-to-customize-a-restored-vm) visszaállítási művelet részeként létrehozott sablont egy új virtuális gép létrehozásához.

## <a name="select-a-restore-point"></a>Visszaállítási pont kijelölése

1. A visszaállítani kívánt virtuális géphez társított tárolóban kattintson az Azure virtuális gép biztonsági**másolata** **elemre.** > 
2. Kattintson egy virtuális gépre. Alapértelmezés szerint a virtuális gép irányítópultján az elmúlt 30 nap helyreállítási pontjai jelennek meg. Megjelenítheti a 30 napnál régebbi helyreállítási pontokat, vagy szűrhet a helyreállítási pontok kereséséhez dátumok, időtartományok és különböző pillanatkép-konzisztencia alapján.
3. A virtuális gép visszaállításához kattintson a **Virtuálisgép visszaállítása**gombra.

    ![Visszaállítási pont](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Válassza ki a helyreállításhoz használni kívánt visszaállítási pontot.

## <a name="choose-a-vm-restore-configuration"></a>Virtuálisgép-visszaállítási konfiguráció kiválasztása

1. A **Konfiguráció visszaállítása területen**válasszon egy visszaállítási lehetőséget:
    - **Új létrehozása**: Használja ezt a beállítást, ha új virtuális gép létrehozásához. Létrehozhat egy virtuális gép egyszerű beállításokkal, vagy visszaállíthatja a lemezt, és hozzon létre egy testreszabott virtuális gép.
    - **Meglévő cseréje**: Ezzel a beállítással cserélheti le a lemezeket egy meglévő virtuális gépen.

        ![Konfigurációs varázsló visszaállítása](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Adja meg a kijelölt visszaállítási beállítás beállításait.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A [visszaállítási lehetőségek](#restore-options)egyikeként gyorsan létrehozhat egy virtuális gép et az alapvető beállításokat egy visszaállítási pontról.

1. A **Konfiguráció** > visszaállítása Új**visszaállítási típus****létrehozása** > csoportban válassza **a Virtuális gép létrehozása**lehetőséget.
2. A **virtuális gép neve**, adja meg a virtuális gép, amely nem létezik az előfizetésben.
3. Az **Erőforrás csoportban**jelöljön ki egy meglévő erőforráscsoportot az új virtuális géphez, vagy hozzon létre egy újat globálisan egyedi névvel. Ha hozzárendel egy nevet, amely már létezik, az Azure hozzárendeli a csoport ugyanazt a nevet, mint a virtuális gép.
4. A **virtuális hálózatban**válassza ki azt a virtuális hálózatot, amelyben a virtuális gép kerül. Az előfizetéshez társított összes virtuális hálózat jelenik meg. Jelölje ki az alhálózatot. Alapértelmezés szerint az első alhálózat van kiválasztva.
5. A **Tárolási hely területen**adja meg a virtuális gép tárfiókját. [További információ](#storage-accounts).

    ![Konfigurációs varázsló visszaállítása](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. A **Konfiguráció visszaállítása csoportban**válassza **az OK**gombot. A **Visszaállítás alkalmazásban**kattintson a Visszaállítás **gombra** a visszaállítási művelet elindításához.

## <a name="restore-disks"></a>Lemezek visszaállítása

A [visszaállítási lehetőségek](#restore-options)egyikeként lemezt hozhat létre egy visszaállítási pontról. Ezután a lemezsegítségével az alábbi teendők közül választhat:

- Használja a visszaállítási művelet során létrehozott sablont a beállítások testreszabásához és a virtuális gép telepítésének aktiválásához. Szerkeszti az alapértelmezett sablonbeállításokat, és elküldi a sablont a virtuális gép üzembe helyezéséhez.
- [Visszaállított lemezek csatolása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) meglévő virtuális géphez.
- [Hozzon létre egy új virtuális gép](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a visszaállított lemezek powershell használatával.

1. A **Konfiguráció** > visszaállítása Új**visszaállítási típus****létrehozása** > csoportban válassza **a Lemezek visszaállítása**lehetőséget.
2. Az **Erőforrás csoportban**jelöljön ki egy meglévő erőforráscsoportot a visszaállított lemezekhez, vagy hozzon létre egy újat globálisan egyedi névvel.
3. A **Tárfiókban**adja meg azt a fiókot, amelybe a Virtuálisgépeket másolni szeretné. [További információ](#storage-accounts).

    ![A helyreállítási konfiguráció befejeződött](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. A **Konfiguráció visszaállítása csoportban**válassza **az OK**gombot. A **Visszaállítás alkalmazásban**kattintson a Visszaállítás **gombra** a visszaállítási művelet elindításához.

Ha a virtuális gép felügyelt lemezeket használ, és a Virtuálisgép létrehozása lehetőséget **választja,** az Azure Backup nem használja a megadott tárfiókot. A lemezek **visszaállítása** és az **azonnali visszaállítás**esetén a tárfiók csak a sablon tárolására szolgál. A felügyelt lemezek a megadott erőforráscsoportban jönnek létre.
Ha a virtuális gép nem felügyelt lemezeket használ, azok blobokként lesznek visszaállítva a tárfiókba.

### <a name="use-templates-to-customize-a-restored-vm"></a>A visszaállított virtuális gép testreszabásához sablonok használatával szabhatja testre a

A lemez visszaállítása után a visszaállítási művelet részeként létrehozott sablon nal testre szabhatja és létrehozhat egy új virtuális gépet:

1. Nyissa **meg a Feladat részletei visszaállítása a** megfelelő feladathoz.

2. A **Feladat részleteinek visszaállítása**területen válassza **a Sablon telepítése lehetőséget** a sablon központi telepítésének kezdeményezéséhez.

    ![Feladat lebontásának visszaállítása](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. A sablonban megadott virtuális gép beállítás testreszabásához kattintson a **Sablon szerkesztése gombra.** Ha további testreszabásokat szeretne hozzáadni, kattintson **a Paraméterek szerkesztése gombra.**
    - [További információ](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) az erőforrások egyéni sablonból történő üzembe helyezéséről.
    - [További információ](../azure-resource-manager/templates/template-syntax.md) a sablonok szerkesztéséről.

   ![Sablon telepítése](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Adja meg a virtuális gép egyéni értékeit, fogadja el a **részvételi feltételeket,** és kattintson a **Vásárlás gombra.**

   ![Sablon központi telepítése](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Meglévő lemezek cseréje

A [visszaállítási lehetőségek](#restore-options)egyikeként lecserélheti a meglévő virtuális gép lemezét a kijelölt visszaállítási pontra. [Tekintse át](#restore-options) az összes visszaállítási lehetőséget.

1. A **Visszaállítás a konfigurációban**kattintson a **Meglévő cseréje**gombra.
2. A **Visszaállítás típusa csoportban**válassza **a Lemez/s cseréje**lehetőséget. Ez az a visszaállítási pont, amely a meglévő virtuálisgép-lemezek et fogja helyettesíteni.
3. Az **Előkészítési hely területen**adja meg, hogy az aktuális felügyelt lemezek pillanatképeit hol kell menteni a visszaállítási folyamat során. [További információ](#storage-accounts).

   ![Konfigurációs varázsló visszaállítása A meglévő cseréje](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

A [visszaállítási lehetőségek](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)egyikeként a Régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy Azure-párosított régió.

Ha az előnézet során szeretné beszállni a funkciót, olvassa el a [Mielőtt elkezdené a szakaszt](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Annak megtekintéséhez, hogy a CRR engedélyezve van-e, kövesse a [Régióközi visszaállítás konfigurálása](backup-create-rs-vault.md#configure-cross-region-restore) című témakör utasításait.

### <a name="view-backup-items-in-secondary-region"></a>Biztonsági másolat elemeinek megtekintése a másodlagos régióban

Ha a CRR engedélyezve van, megtekintheti a biztonsági mentési elemeket a másodlagos régióban.

1. A portálról nyissa meg a **Recovery Services-tároló** > **biztonsági másolatelemeit**
2. Kattintson a **Másodlagos terület** elemre a másodlagos régió elemeinek megtekintéséhez.

![Virtuális gépek a másodlagos régióban](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Másodlagos terület kiválasztása](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Visszaállítás másodlagos régióban

A másodlagos régió-visszaállításfelhasználói élmény hasonló lesz az elsődleges régió-visszaállítási felhasználói élményhez. Amikor a konfiguráció visszaállítása panelen konfigurálja a részleteket a visszaállítás konfigurálásához, a rendszer csak másodlagos régióparaméterek megadását kéri.

![Válassza a virtuális gép visszaállítása](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Visszaállítási pont kijelölése](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Konfiguráció visszaállítása](./media/backup-azure-arm-restore-vms/rest-config.png)

![Visszaállítás aktiválása folyamatban értesítés](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Virtuális gép visszaállításához és létrehozásához olvassa el [a Virtuálisgép létrehozása](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm)című területet.
- A lemezként való visszaállításhoz olvassa el a [Lemezek visszaállítása hivatkozást.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks)

>[!NOTE]
>A visszaállítás aktiválása és az adatátviteli fázis után a visszaállítási feladat nem szakítható meg.

### <a name="monitoring-secondary-region-restore-jobs"></a>Másodlagos régió visszaállítási feladatának figyelése

1. A portálról nyissa meg a **Recovery Services-tároló** > **biztonsági mentési feladatait**
2. Kattintson a **Másodlagos terület** elemre a másodlagos régió elemeinek megtekintéséhez.

![Szűrt biztonsági mentési feladatok](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Virtuális gépek visszaállítása speciális konfigurációkkal

Számos gyakori forgatókönyv, amelyben előfordulhat, hogy vissza kell állítania a virtuális gépek.

**Forgatókönyv** | **Útmutatás**
--- | ---
**Virtuális gépek visszaállítása hibrid használati előnyökkel** | Ha egy Windows virtuális gép [hibrid használati előny (HUB) licencelést](../virtual-machines/windows/hybrid-use-benefit-licensing.md)használ, állítsa vissza a lemezeket, és hozzon létre egy új virtuális gépet a megadott sablon **(licenctípus** **Windows_Server)** vagy a PowerShell használatával.  Ez a beállítás a virtuális gép létrehozása után is alkalmazható.
**Virtuális gépek visszaállítása az Azure-adatközpont katasztrófája során** | Ha a tároló GRS-t használ, és a virtuális gép elsődleges adatközpontja leáll, az Azure Backup támogatja a biztonsági másolatot készítő virtuális gépek visszaállítása a párosított adatközpontba. Kikell választania egy tárfiókot a párosított adatközpontban, és a szokásos módon állíthatja vissza. Az Azure Backup a párosított régióban lévő számítási szolgáltatást használja a visszaállított virtuális gép létrehozásához. [További információ](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) az adatközpont rugalmasságáról.
**Egyetlen tartományvezérlő virtuális gépének visszaállítása egyetlen tartományban** | Állítsa vissza a virtuális gép, mint bármely más virtuális gép. Vegye figyelembe:<br/><br/> Az Active Directory szempontjából az Azure virtuális gép, mint bármely más virtuális gép.<br/><br/> A címtárszolgáltatások visszaállítási módja (DSRM) is elérhető, így az Active Directory helyreállítási forgatókönyvei életképesek. [További információ](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) a virtualizált tartományvezérlők biztonsági mentésével és visszaállításával kapcsolatos szempontokról.
**Több tartományvezérlő virtuális gépének visszaállítása egyetlen tartományban** | Ha ugyanazon tartomány más tartományvezérlői is elérhetők a hálózaton keresztül, a tartományvezérlő ugyanúgy visszaállítható, mint bármely virtuális gép. Ha ez az utolsó megmaradt tartományvezérlő a tartományban, vagy egy elkülönített hálózat helyreállítása történik, használjon [erdő-helyreállítást.](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)
**Több tartomány visszaállítása egy erdőben** | Erdőutáni [helyreállítást](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)javasolunk.
**Csupasz fém restaurálás** | A fő különbség az Azure virtuális gépek és a helyszíni hipervizorok között az, hogy nincs elérhető virtuálisgép-konzol az Azure-ban. Bizonyos esetekben konzolra van szükség, például a rendszer nélküli helyreállítás (BMR) típusú biztonsági másolat használatával történő helyreállításhoz. Azonban a virtuális gép visszaállítása a tárolóból a BMR teljes cseréje.
**Virtuális gépek visszaállítása speciális hálózati konfigurációkkal** | A speciális hálózati konfigurációk közé tartoznak a belső vagy külső terheléselosztást használó virtuális gépek, több NICS vagy több fenntartott IP-cím használatával. Ezeket a virtuális gépeket a [visszaállítási lemez beállítással állíthatja vissza.](#restore-disks) Ez a beállítás másolatot készít a virtuális gépekről a megadott tárfiókba, és ezután létrehozhat egy virtuális gép egy [belső](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) vagy [külső](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) terheléselosztóval, [több NICS-vel](../virtual-machines/windows/multiple-nics.md)vagy több fenntartott [IP-címmel](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)a konfigurációnak megfelelően.
**Hálózati biztonsági csoport (NSG) a hálózati adapteren/alhálózaton** | Az Azure virtuális gép biztonsági mentése támogatja az NSG-adatok biztonsági mentését és visszaállítását a virtuális hálózat, az alhálózat és a hálózati adapter szintjén.
**Zónában rögzített virtuális gépek** | Az Azure Backup támogatja a zónás rögzített virtuális gépek biztonsági mentését és visszaállítását. [További információ](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>A visszaállítási művelet nyomon követése

A visszaállítási művelet aktiválása után a biztonsági mentési szolgáltatás létrehoz egy feladatot a nyomon követéshez. Az Azure Backup értesítéseket jelenít meg a feladatról a portálon. Ha nem láthatóak, jelölje ki az **Értesítések szimbólumot,** majd az **Összes feladat megtekintése** lehetőséget a Visszaállítási folyamat állapotának megtekintéséhez.

![Visszaállítás aktiválva](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 A visszaállítás nyomon követése az alábbiak szerint:

1. A feladat műveleteinek megtekintéséhez kattintson az értesítések hivatkozásra. Másik lehetőségként a tárolóban kattintson a **Biztonsági mentési feladatok**elemre, majd a megfelelő virtuális gépre.

    ![A tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. A visszaállítási folyamat figyeléséhez kattintson bármelyik folyamatban lévő állapotú **visszaállítási feladatra.** Ez megjeleníti a folyamatjelző sávot, amely a visszaállítás folyamatával kapcsolatos információkat jeleníti meg:

    - **A visszaállítás becsült ideje:** Kezdetben biztosítja a visszaállítási művelet befejezéséhez szükséges időt. A művelet előrehaladtával a szükséges idő csökken, és eléri a nullát, amikor a visszaállítási művelet befejeződik.
    - **A visszaállítás százaléka**. A visszaállítási művelet készültségi arányának megjelenítése.
    - **Átvitt bájtok száma**: Ha új virtuális gép létrehozásával állítja vissza a visszaállítást, akkor az átvitt bájtok az átvihető bájtok teljes száma között jelennek meg.

## <a name="post-restore-steps"></a>A visszaállítás utáni lépések

A virtuális gép visszaállítása után számos dolog rakoncátlevárható:

- A biztonsági mentési konfiguráció során jelen lévő bővítmények telepítve vannak, de nincsenek engedélyezve. Ha problémát lát, telepítse újra a bővítményeket.
- Ha a biztonsági másolatot kapott virtuális gép statikus IP-címmel rendelkezik, a visszaállított virtuális gép dinamikus IP-címmel fog rendelkezni az ütközések elkerülése érdekében. Statikus [IP-címet adhat a visszaállított virtuális géphez.](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
- A visszaállított virtuális gép nem rendelkezik rendelkezésre állási készlet. Ha a visszaállítási lemez beállítást használja, akkor [megadhat egy rendelkezésre állási készletet,](../virtual-machines/windows/tutorial-availability-sets.md) amikor virtuális gép létrehozása a lemezről a megadott sablon vagy a PowerShell használatával.
- Ha egy felhőalapú Linux-disztribúciót használ, például az Ubuntut, biztonsági okokból a jelszó blokkolva lesz a visszaállítás után. A [jelszó alaphelyzetbe állításához](../virtual-machines/linux/reset-password.md)használja a visszaállított virtuális gép VMAccess bővítményét. Javasoljuk, hogy ssh-kulcsokat használjon ezeken a disztribúciókon, így nem kell alaphelyzetbe állítania a jelszót a visszaállítás után.
- Ha nem tudja elérni a virtuális gép visszaállítása miatt virtuális gép megszakadt kapcsolat tartományvezérlővel, majd kövesse az alábbi lépéseket, hogy hozza létre a virtuális gép:
  - Csatlakoztassa az operációsrendszer-lemezt adatlemezként egy helyreállított virtuális géphez.
  - Manuálisan telepítse a virtuálisgép-ügynököt, ha az Azure Agent nem válaszol ezen a [hivatkozáson](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)keresztül.
  - Soros konzol hozzáférésének engedélyezése a virtuális gépen a virtuális gép parancssori elérésének engedélyezéséhez

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - A virtuális gép újraépítésekor használja az Azure Portalt a helyi rendszergazdai fiók és jelszó alaphelyzetbe állításához
  - A virtuális gép tartománytól való lekapcsolódása soros konzolhozzáférés és CMD használatával

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Miután a virtuális gép levan választva, és újraindul, sikeresen rdp-virtuális gép helyi rendszergazdai hitelesítő adatokkal, és újra csatlakozik a virtuális gép vissza a tartományba sikeresen.

## <a name="backing-up-restored-vms"></a>A visszaállított virtuális gépek biztonsági mentése

- Ha visszaállította a virtuális gép ugyanannak az erőforráscsoportnak az eredetileg biztonsági másolatot készítő virtuális gép nevével, a biztonsági mentés a visszaállítás után folytatódik a virtuális gépen.
- Ha visszaállította a virtuális gép egy másik erőforráscsoport, vagy megadott egy másik nevet a visszaállított virtuális gép, be kell állítania a biztonsági mentést a visszaállított virtuális gép.

## <a name="next-steps"></a>További lépések

- Ha nehézségekbe ütközik a visszaállítási folyamat során, [tekintse át](backup-azure-vms-troubleshoot.md#restore) a gyakori problémákat és hibákat.
- A virtuális gép visszaállítása után ismerje meg a [virtuális gépek kezelését](backup-azure-manage-vms.md)
