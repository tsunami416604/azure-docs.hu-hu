---
title: Virtuális gépek visszaállítása a Azure Portal használatával
description: Azure-beli virtuális gép visszaállítása helyreállítási pontról a Azure Portal használatával
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 9426a66115513cf02af501eb6271cf1f1b9fdf76
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996340"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure-beli virtuális gépekre vonatkozó Azure Portal visszaállítása

Ez a cikk azt ismerteti, hogyan állíthatja vissza az Azure-beli virtuális gépek adatait a [Azure Backup](backup-overview.md) Recovery Services-tárolókban tárolt helyreállítási pontokból.

## <a name="restore-options"></a>Visszaállítási lehetőségek

A Azure Backup számos módszert biztosít a virtuális gépek visszaállítására.

**Visszaállítási lehetőség** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | Gyorsan létrehoz és lekéri egy alapszintű virtuális gépet egy visszaállítási pontról.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelyben a rendszer elhelyezi, majd megadhat egy Storage-fiókot a visszaállított virtuális géphez. Az új virtuális gépet a forrásoldali virtuális géppel azonos régióban kell létrehozni.
**Lemez visszaállítása** | Visszaállítja a virtuális gép lemezét, amely azután új virtuális gép létrehozásához használható.<br/><br/> Azure Backup biztosít egy sablont, amely segítséget nyújt a virtuális gépek testreszabásához és létrehozásához. <br/><br> A visszaállítási feladattípus létrehoz egy sablont, amelyet letöltheti, és használhatja az egyéni virtuálisgép-beállítások megadására és a virtuális gép létrehozásához.<br/><br/> A lemezeket a rendszer az Ön által megadott erőforráscsoport-csoportba másolja.<br/><br/> Azt is megteheti, hogy csatlakoztatja a lemezt egy meglévő virtuális géphez, vagy létrehozhat egy új virtuális gépet a PowerShell használatával.<br/><br/> Ez a beállítás akkor hasznos, ha testre szeretné szabni a virtuális gépet, olyan konfigurációs beállításokat ad hozzá, amelyek nem voltak a biztonsági mentés idején, vagy olyan beállításokat adhatnak hozzá, amelyeket a sablon vagy a PowerShell használatával kell konfigurálni.
**Meglévő cseréje** | Visszaállíthat egy lemezt, és lecserélheti a meglévő virtuális gép lemezét.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha törölték, ez a beállítás nem használható.<br/><br/> Azure Backup a lemez cseréje előtt pillanatképet készít a meglévő virtuális gépről, és a megadott átmeneti helyen tárolja azt. A virtuális géphez csatlakoztatott meglévő lemezeket a rendszer a kijelölt visszaállítási pontra cseréli.<br/><br/> A rendszer átmásolja a pillanatképet a tárolóba, és megőrzi a megőrzési szabályzatnak megfelelően. <br/><br/> A lemez cseréje művelet után az eredeti lemez megmarad az erőforráscsoporthoz. Dönthet úgy is, hogy manuálisan törli az eredeti lemezeket, ha azok nem szükségesek. <br/><br/>A meglévő cseréje a nem titkosított felügyelt virtuális gépek esetében támogatott. Nem felügyelt lemezek, [általánosított virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)vagy [Egyéni lemezképek használatával létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)virtuális gépek esetén nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezzel rendelkezik, mint az aktuális virtuális gép, akkor a visszaállítási pontban lévő lemezek száma csak a virtuális gép konfigurációját fogja tükrözni.<br/><br/>

> [!NOTE]
> Az Azure-beli virtuális gépeken meghatározott fájlokat és mappákat is helyreállíthatja. [További információk](backup-azure-restore-files-from-vm.md).
>
> Ha az Azure-beli virtuális gépek (azaz azonnali visszaállítás) Azure Backup [legújabb verzióját](backup-instant-restore-capability.md) futtatja, a pillanatképek akár hét napig is megmaradnak, és a biztonsági mentési adatoknak a tárolóba történő elküldése előtt visszaállíthatja a virtuális gépet a pillanatképekről. Ha az elmúlt hét nap biztonsági másolatából szeretné visszaállítani a virtuális gépet, akkor a pillanatképből való visszaállítás gyorsabb, és nem a tárból.

## <a name="storage-accounts"></a>Tárfiókok

A Storage-fiókok részletei:

- **Virtuális gép létrehozása**: új virtuális gép létrehozásakor a virtuális gép a megadott Storage-fiókba kerül.
- **Lemez visszaállítása**: a lemez visszaállításakor a rendszer átmásolja a lemezt a megadott Storage-fiókba. A visszaállítási feladattípus létrehoz egy sablont, amelyet letöltheti és használhatja az egyéni virtuálisgép-beállítások megadásához. Ez a sablon a megadott Storage-fiókba kerül.
- **Lemez cseréje**: Ha egy meglévő virtuális gépen lévő lemezt cserél le, Azure Backup a lemez cseréje előtt pillanatképet készít a meglévő virtuális gépről. A pillanatképet a megadott átmeneti helyen (Storage-fiókban) tárolja a rendszer. A rendszer ezt a Storage-fiókot használja a pillanatkép ideiglenes tárolására a visszaállítási folyamat során, ezért javasoljuk, hogy hozzon létre egy új fiókot, amely ezt követően könnyen eltávolítható.
- **Storage-fiók helye**: a Storage-fióknak ugyanabban a régióban kell lennie, mint a tárolónak. Csak ezek a fiókok jelennek meg. Ha a helyen nincsenek Storage-fiókok, létre kell hoznia egyet.
- **Tárolás típusa**: a blob Storage nem támogatott.
- **Tárolási redundancia**: a zóna redundáns tárolási (ZRS) nem támogatott. A fiók replikációs és redundancia-információi a fiók neve után zárójelben jelennek meg.
- **Prémium szintű Storage**:
  - A nem prémium szintű virtuális gépek visszaállításakor a Premium Storage-fiókok nem támogatottak.
  - A felügyelt virtuális gépek visszaállításakor a hálózati szabályokkal konfigurált Premium Storage-fiókok nem támogatottak.

## <a name="before-you-start"></a>Előkészületek

Ha egy virtuális gépet szeretne visszaállítani (hozzon létre egy új virtuális gépet), győződjön meg arról, hogy megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) [engedélyekkel](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) rendelkezik a virtuális gép visszaállítása művelethez.

Ha nem rendelkezik megfelelő engedélyekkel, [visszaállíthat egy lemezt](#restore-disks), majd a lemez visszaállítása után a visszaállítási művelet részeként létrehozott [sablont használhatja](#use-templates-to-customize-a-restored-vm) új virtuális gép létrehozásához.

## <a name="select-a-restore-point"></a>Válasszon egy visszaállítási pontot

1. A visszaállítani kívánt virtuális géphez társított **tárolóban kattintson a biztonsági másolati elemek** > Azure-beli **virtuális gép**elemre.
2. Kattintson egy virtuális gépre. Alapértelmezés szerint a virtuális gép irányítópultján az utolsó 30 nap helyreállítási pontjai jelennek meg. 30 napnál régebbi helyreállítási pontokat jeleníthet meg, vagy szűréssel megkeresheti a dátumokon, időtartományokon és a pillanatkép-konzisztencia különböző típusain alapuló helyreállítási pontokat.
3. A virtuális gép visszaállításához kattintson a **virtuális gép visszaállítása**elemre.

    ![Visszaállítási pont](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Válassza ki a helyreállításhoz használni kívánt visszaállítási pontot.

## <a name="choose-a-vm-restore-configuration"></a>Virtuális gép visszaállítási konfigurációjának kiválasztása

1. A **visszaállítási konfiguráció**területen válasszon egy visszaállítási lehetőséget:
    - **Új létrehozása**: akkor használja ezt a beállítást, ha új virtuális gépet szeretne létrehozni. Létrehozhat egy egyszerű beállításokkal rendelkező virtuális gépet, vagy visszaállíthat egy lemezt, és létrehozhat egy testreszabott virtuális gépet.
    - **Meglévő cseréje**: akkor használja ezt a beállítást, ha egy meglévő virtuális gépen szeretné lecserélni a lemezeket.

        ![Konfiguráció visszaállítása varázsló](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Adja meg a kijelölt visszaállítási beállítás beállításait.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A [visszaállítási lehetőségek](#restore-options)egyike gyorsan létrehozhat egy virtuális gépet egy visszaállítási pont alapbeállításaival.

1. A **konfiguráció visszaállítása** > **új** > - **visszaállítási típus**létrehozása területen válassza **a virtuális gép létrehozása**lehetőséget.
2. A **virtuális gép neve**mezőben adjon meg egy olyan virtuális gépet, amely nem létezik az előfizetésben.
3. Az **erőforráscsoport**területen válasszon ki egy meglévő erőforráscsoportot az új virtuális géphez, vagy hozzon létre egy újat globálisan egyedi névvel. Ha már létező nevet rendel, az Azure a virtuális géppel megegyező nevű csoportot rendeli hozzá.
4. A **Virtual Network (virtuális hálózat**) területen válassza ki azt a VNet, amelyben a virtuális gép el lesz helyezve. Megjelenik az előfizetéshez társított összes virtuális hálózatok. Válassza ki az alhálózatot. Alapértelmezés szerint az első alhálózat van kiválasztva.
5. A **tárolási hely**mezőben határozza meg a virtuális gép Storage-fiókját. [További információk](#storage-accounts).

    ![Konfiguráció visszaállítása varázsló](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. A **visszaállítási konfiguráció**területen kattintson **az OK gombra**. A **visszaállítás**elemre **kattintva aktiválja a visszaállítási** műveletet.

## <a name="restore-disks"></a>Lemezek visszaállítása

Az egyik [visszaállítási lehetőségként](#restore-options)létrehozhat egy lemezt egy visszaállítási pontból. Ezután a lemezzel a következők egyikét végezheti el:

- A visszaállítási művelet során létrehozott sablon használatával testreszabhatja a beállításokat, és aktiválhatja a virtuális gépek üzembe helyezését. Szerkessze az alapértelmezett sablon beállításait, és küldje el a sablont a virtuális gép telepítéséhez.
- [Visszaállított lemezek csatolása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) egy meglévő virtuális géphez.
- [Hozzon létre egy új virtuális gépet](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a helyreállított lemezekről a PowerShell használatával.

1. A **konfiguráció visszaállítása** > **új** > - **visszaállítási típus**létrehozása területen válassza a **lemezek visszaállítása**lehetőséget.
2. Az **erőforráscsoport**területen válasszon ki egy meglévő erőforráscsoportot a visszaállított lemezekhez, vagy hozzon létre egy újat globálisan egyedi névvel.
3. A **Storage-fiók**területen válassza ki azt a fiókot, ahová a virtuális merevlemezeket másolni szeretné. [További információk](#storage-accounts).

    ![A helyreállítási konfiguráció befejeződött](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. A **visszaállítási konfiguráció**területen kattintson **az OK gombra**. A **visszaállítás**elemre **kattintva aktiválja a visszaállítási** műveletet.

Ha a virtuális gép felügyelt lemezeket használ, és kiválasztja a **virtuális gép létrehozása** lehetőséget, Azure Backup nem a megadott Storage-fiókot használja. **Visszaállítási lemezek** és **azonnali visszaállítás**esetén a Storage-fiók csak a sablon tárolására szolgál. A felügyelt lemezek a megadott erőforráscsoporthoz jönnek létre.
Ha a virtuális gép nem felügyelt lemezeket használ, a rendszer blobként állítja vissza őket a Storage-fiókba.

### <a name="use-templates-to-customize-a-restored-vm"></a>Sablonok használata visszaállított virtuális gép testreszabásához

A lemez visszaállítása után használja a visszaállítási művelet részeként létrehozott sablont az új virtuális gép testreszabásához és létrehozásához:

1. Nyissa meg a megfelelő feladathoz tartozó **visszaállítási feladatok részleteit** .

2. A **visszaállítási feladatok részletei**területen válassza a sablon **üzembe helyezése** lehetőséget a sablon központi telepítésének elindításához.

    ![Visszaállítási feladatok részletezése](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. A sablonban megadott virtuálisgép-beállítás testreszabásához kattintson a **Sablon szerkesztése**elemre. Ha további testreszabásokat szeretne hozzáadni, kattintson a **Paraméterek szerkesztése**elemre.
    - [További](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) információ az erőforrások egyéni sablonból való üzembe helyezéséről.
    - [További](../azure-resource-manager/resource-group-authoring-templates.md) információ a sablonok létrehozásáról.

   ![Sablon üzembe helyezése](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Adja meg a virtuális gép egyéni értékeit, fogadja el a **használati feltételeket** , majd kattintson a **vásárlás**elemre.

   ![Sablon központi telepítésének elküldése](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Meglévő lemezek cseréje

A [visszaállítási lehetőségek](#restore-options)egyike lecserélheti a meglévő virtuálisgép-lemezeket a kijelölt visszaállítási pontra. [Tekintse át](#restore-options) az összes visszaállítási beállítást.

1. A **visszaállítási konfiguráció**területen kattintson a **meglévő cseréje**elemre.
2. A **visszaállítás típusa**területen válassza a **lemez/mp cseréje**lehetőséget. Ez az a visszaállítási pont, amelyet a rendszer a meglévő VM-lemezek cseréjére használ.
3. Az **előkészítési hely**területen adja meg, hogy az aktuális felügyelt lemezek pillanatképei a visszaállítási folyamat során legyenek mentve. [További információk](#storage-accounts).

   ![Konfiguráció visszaállítása varázsló meglévő cseréje](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="restore-vms-with-special-configurations"></a>Virtuális gépek visszaállítása speciális konfigurációkkal

Számos gyakori forgatókönyv létezik, amelyekben szükség lehet a virtuális gépek helyreállítására.

**Forgatókönyv** | **Útmutatás**
--- | ---
**Virtuális gépek visszaállítása Hybrid use Benefit használatával** | Ha egy Windows rendszerű virtuális gép a [hibrid használati juttatás (hub) licencelését](../virtual-machines/windows/hybrid-use-benefit-licensing.md)használja, állítsa vissza a lemezeket, és hozzon létre egy új virtuális gépet a megadott sablonnal (a **licenc típusa** **Windows_Server**) vagy a PowerShell.  Ezt a beállítást a virtuális gép létrehozása után is lehet alkalmazni.
**Virtuális gépek visszaállítása Azure-adatközponti katasztrófa esetén** | Ha a tároló a GRS-t használja, és a virtuális gép elsődleges adatközpontja leáll, a Azure Backup támogatja a biztonsági másolatban lévő virtuális gépeknek a párosított adatközpontba történő visszaállítását. Válasszon egy Storage-fiókot a párosított adatközpontban, és állítsa vissza a szokásos módon. A Azure Backup a párosított régió számítási szolgáltatását használja a visszaállított virtuális gép létrehozásához. [További](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) információ az adatközpont rugalmasságáról.
**Önálló tartományvezérlő virtuális gép visszaállítása egyetlen tartományból** | Állítsa vissza a virtuális gépet, mint bármely más virtuális gépet. Vegye figyelembe:<br/><br/> Egy Active Directory perspektívából az Azure-beli virtuális gép olyan, mint bármely más virtuális gép.<br/><br/> A Címtárszolgáltatások helyreállító módja (címtárszolgáltatás-javító mód) is elérhető, így az összes Active Directory-helyreállítási forgatókönyv életképes. [További](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) információ a virtualizált tartományvezérlők biztonsági mentési és visszaállítási szempontjairól.
**Több tartományvezérlőt tartalmazó virtuális gép visszaállítása egyetlen tartományból** | Ha ugyanabban a tartományban más tartományvezérlők is elérhetők a hálózaton keresztül, akkor a tartományvezérlőt bármely virtuális géphez hasonlóan vissza lehet állítani. Ha a tartomány utolsó fennmaradó tartományvezérlője, vagy egy elszigetelt hálózatban lévő helyreállítást hajt végre, használjon [erdő-helyreállítást](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Több tartomány visszaállítása egy erdőben** | Az [erdő helyreállítását](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)javasoljuk.
**Operációs rendszer nélküli helyreállítás** | Az Azure-beli virtuális gépek és a helyszíni hypervisorok közötti legnagyobb különbség az, hogy nincs elérhető virtuálisgép-konzol az Azure-ban. Bizonyos helyzetekben szükség van egy konzolra, például egy operációs rendszer nélküli helyreállítás (BMR) típusú biztonsági mentés használatával történő helyreállításra. Azonban a virtuális gépek visszaállítása a tárolóból teljes mértékben helyettesíti a BMR.
**Virtuális gépek visszaállítása speciális hálózati konfigurációkkal** | A speciális hálózati konfigurációk belső vagy külső terheléselosztást használó virtuális gépeket tartalmaznak, több hálózati adapter vagy több fenntartott IP-cím használatával. Ezeket a virtuális gépeket a [lemez visszaállítása lehetőség](#restore-disks)használatával állíthatja vissza. Ez a beállítás lehetővé teszi a virtuális merevlemezek másolatát a megadott Storage-fiókba, és ezután létrehozhat egy [belső](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) vagy [külső](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) terheléselosztó, [több hálózati adapter](../virtual-machines/windows/multiple-nics.md)vagy [több fenntartott IP-cím](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)használatával létrehozott virtuális gépet a konfigurációnak megfelelően.
**Hálózati biztonsági csoport (NSG) hálózati ADAPTERen/alhálózaton** | Az Azure virtuális gép biztonsági mentése támogatja a NSG-adatok biztonsági mentését és visszaállítását a vnet, az alhálózat és a NIC szintjén.
**Zónában rögzített virtuális gépek** | Azure Backup támogatja a zónázott rögzített virtuális gépek biztonsági mentését és visszaállítását. [További információ](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>A visszaállítási művelet nyomon követése

A visszaállítási művelet elindítását követően a Backup szolgáltatás létrehoz egy feladatot a követéshez. A Azure Backup megjeleníti a feladatokkal kapcsolatos értesítéseket a portálon. Ha nem láthatók, kattintson az **értesítések** szimbólumra, majd válassza az **összes feladat megtekintése** lehetőséget a visszaállítási folyamat állapotának megtekintéséhez.

![A visszaállítás aktiválva](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 A visszaállítás követése a következőképpen történik:

1. Ha meg szeretné tekinteni a feladatok műveleteit, kattintson az értesítések hivatkozásra. Másik lehetőségként a **tárolóban kattintson a biztonsági mentési feladatok**elemre, majd kattintson a megfelelő virtuális gépre.

    ![A tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. A visszaállítási folyamat figyeléséhez kattintson a **folyamatban lévő**állapotú visszaállítási feladatokra. Ekkor megjelenik a folyamatjelző sáv, amely a visszaállítási folyamattal kapcsolatos információkat jeleníti meg:

    - A **visszaállítás becsült ideje**: először a visszaállítási művelet befejezéséhez szükséges időt adja meg. A művelet előrehaladtával az igénybe vett idő csökkenti, és a visszaállítási művelet befejeződése után nullára ér.
    - **A visszaállítás százalékos értéke**. Megjeleníti a végrehajtott visszaállítási művelet százalékos arányát.
    - **Átvitt bájtok száma**: Ha új virtuális gép létrehozásával állítja vissza, az átvitt bájtok száma az átvitt bájtok teljes számát mutatja.

## <a name="post-restore-steps"></a>Visszaállítás utáni lépések

A virtuális gépek visszaállítása után számos dolgot érdemes megjegyezni:

- A biztonsági mentési konfigurációban lévő bővítmények telepítve vannak, de nem engedélyezettek. Ha problémát tapasztal, telepítse újra a bővítményeket.
- Ha a biztonsági másolatban szereplő virtuális gép statikus IP-címmel rendelkezik, a visszaállított virtuális gép dinamikus IP-címmel fog rendelkezni, hogy elkerülje az ütközést. [Statikus IP-címet adhat hozzá a visszaállított virtuális géphez](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- A helyreállított virtuális gépek nem rendelkeznek rendelkezésre állási csoporttal. Ha a lemez visszaállítása lehetőséget használja, [megadhat egy rendelkezésre állási készletet](../virtual-machines/windows/tutorial-availability-sets.md) , ha a megadott sablonnal vagy PowerShell-lel hozza létre a virtuális gépet a lemezről.
- Ha felhő-init-alapú Linux-disztribúciót, például Ubuntut használ, biztonsági okokból a rendszer letiltja a jelszót a visszaállítás után. A [jelszó alaphelyzetbe állításához](../virtual-machines/linux/reset-password.md)használja a VISSZAÁLLÍTOTT virtuális gép VMAccess bővítményét. Javasoljuk, hogy SSH-kulcsokat használjon ezeken a disztribúciókban, így a visszaállítás után nem kell alaphelyzetbe állítania a jelszót.
- Ha a virtuális gép a tartományvezérlővel megszakadt kapcsolat miatt nem fér hozzá a virtuális géphez, akkor az alábbi lépéseket követve hozza létre a virtuális gépet:
  - OPERÁCIÓSRENDSZER-lemez csatlakoztatása adatlemezként egy helyreállított virtuális géphez.
  - Manuálisan telepítse a virtuálisgép-ügynököt, ha az Azure-ügynök nem válaszol a [hivatkozás](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)követésével.
  - A soros konzol elérésének engedélyezése a virtuális gépen a virtuális gép parancssori elérésének engedélyezéséhez

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - A virtuális gép újraépítése Azure Portal a helyi rendszergazdai fiók és a jelszó alaphelyzetbe állításához
  - A virtuális gép leválasztása Serial console hozzáférés és a CMD használatával a tartományból

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- A virtuális gép leválasztása és újraindítása után a virtuális gép a helyi rendszergazdai hitelesítő adatokkal sikeresen újraindulhat, és a virtuális gépet sikeresen újra csatlakoztathatja a tartományhoz.

## <a name="backing-up-restored-vms"></a>Visszaállított virtuális gépek biztonsági mentése

- Ha egy virtuális gépet ugyanarra az erőforráscsoporthoz állítja vissza, mint az eredetileg mentett virtuális gép neve, a Backup a virtuális gépen folytatódik a visszaállítás után.
- Ha egy másik erőforráscsoporthoz állítja vissza a virtuális gépet, vagy ha a visszaállított virtuális gép számára más nevet adott meg, be kell állítania a visszaállított virtuális gép biztonsági mentését.

## <a name="next-steps"></a>Következő lépések

- Ha a visszaállítási folyamat során nehézségekbe ütközik, [tekintse át](backup-azure-vms-troubleshoot.md#restore) a gyakori problémákat és hibákat.
- A virtuális gép visszaállítása után megismerheti a [virtuális gépek kezelését](backup-azure-manage-vms.md) .
