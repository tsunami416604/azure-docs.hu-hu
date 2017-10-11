---
title: "A Site Recovery-tároló frissítsen az Azure Recovery Services-tároló"
description: "Ismerje meg, hogyan frissítheti az Azure Site Recovery-tároló Recovery Services-tároló"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>A Site Recovery-tároló frissítsen az Azure Resource Manager-alapú Recovery Services-tároló

Ez a cikk ismerteti, hogyan frissítése a helyreállítási szolgáltatás Azure Resource Manager-alapú tárolók nélkül folyamatban lévő replikáció hatással az Azure Site Recovery-tárolóhoz. További információ az Azure erőforrás-kezelő szolgáltatásait és előnyeit: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Bevezetés
Recovery Services-tároló egy Azure Resource Manager szerinti erőforrás natív a felhőben a biztonsági mentés és katasztrófa-helyreállítás kezelését. Egy egységes tárolóra, használhatja az új Azure-portálon, és azt a felváltja a klasszikus biztonsági mentés és a Site Recovery-tárolók.

Helyreállítási szolgáltatások tárolók funkciót, beleértve a tömbje kínál:

* Az Azure Resource Manager támogatása: védeni, és az Azure Resource Manager-verembe feladatátvételt a virtuális gépek és fizikai gépek.

* Lemez kizárása: Ha ideiglenes fájlokat vagy a teljes sávszélesség használata nem kívánt magas forgalom adatokat, kötetek lehet kizárni a replikálásból. Ez a funkció jelenleg engedélyezve van *Azure VMware* és *Hyper-V Azure* és egyéb forgatókönyvek, valamint az időtartam.

* Támogatja a premium és helyileg redundáns tárolás: kiszolgálók most védelméhez a prémium szintű storage, amelyek lehetővé teszik az alkalmazások magasabb védelmét felhasználói fiókok bemeneti/kimeneti műveletek száma másodpercenként (IOPS). Ez a funkció be van kapcsolva *Azure VMware*.

* Zökkenőmentes élményt első lépések: az első lépéseket élmény úgy tervezték, vész-helyreállítási telepítés megkönnyítése.

* Biztonsági mentés és helyreállítás felügyeleti azonos tárolóból: most vész-helyreállítási kiszolgálók védelme vagy készítsen biztonsági mentést a terhelés jelentősen csökkentheti a felügyelet azonos tárolójából.

A frissített élmény és a szolgáltatásokkal kapcsolatos további információkért lásd: a [tároló, biztonsági mentési és helyreállítási blog](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Következő funkciói

* Ne legyen hatással a folyamatban lévő replikáció: folyamatban lévő replikáció során minden megszakadása nélkül, és frissítés utáni.

* További költség nélkül: egy frissített képességek teljes készlete beolvasása minden további költség nélkül.

* Adatvesztés nélküli: mivel ezt a folyamatot a frissítés és az áttelepítés nem, a meglévő replikációs helyreállítási pontok és beállításai változatlanok maradnak során, és a frissítés után.


## <a name="what-happens-during-the-vault-upgrade"></a>Mi történik, a tároló frissítés során?

A frissítés közben nem hajtható végre műveletek, például egy új kiszolgáló vagy a replikáció a virtuális gép (VM). Műveletek, például az adatok írása a tárolóval, például a folyamatban lévő replikáció a tárolóba, a védett elemek vagy adatok olvasása megszakításmentes folytatásához.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Automatizálási és a frissítés után tooling módosítása
A tároló típusa a klasszikus telepítési modellből a Resource Manager üzembe helyezési modellel frissít, frissítse a meglévő automatizációk vagy tooling annak érdekében, hogy az tovább működni a frissítés után.

### <a name="prepare-your-environment-for-the-upgrade"></a>A frissítés a környezet előkészítése

* [Telepítse a PowerShell, vagy frissítse az 5-ös vagy újabb verzió](https://www.microsoft.com/download/details.aspx?id=50395)
* [Telepítse a legújabb verziót az Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases)
* [A Recovery Services-tároló frissítési parancsfájl letöltése](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Előfeltételek
A Site Recovery-tárolóhoz frissíthet helyreállítási szolgáltatás Azure Resource Manager-alapú tárolók, a következő követelményeknek kell megfelelniük:

* Minimális verziója: az Azure Site Recovery Provider a kiszolgálón telepítve kell lennie 5.1.1700.0 vagy újabb.

* Támogatott konfiguráció: a tároló nem lehet konfigurálni a tárolóhálózat (SAN) vagy SQL Server AlwaysOn rendelkezésre állási csoportok. Minden más konfigurációk vannak támogatva.

    >[!NOTE]
    >A frissítés után tárolási leképezése csak a PowerShell segítségével is kezelheti.

* Támogatott központi telepítési forgatókönyv: A tároló nem lehet a *Azure VMware* a hagyományos telepítési modellt. Mielőtt továbblépne, először helyezze át a továbbfejlesztett üzembe helyezési modellben.

* A felhasználó által kezdeményezett nem aktív feladatok, például a felügyeleti műveletek sík: mert a frissítés során a felügyeleti vezérlősík elérése történik, hajtsa végre a felügyeleti vezérlősík műveletet indít el a frissítés előtt. Ez a folyamat nem tartalmazza a folyamatban lévő replikáció.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Ez a frissítés hatással van a folyamatban lévő replikáció?**

Nem. A folyamatban lévő replikáció továbbra is folyamatos, során, és a frissítés után.

**Mi történik a hálózati beállítások, például a pont-pont VPN és IP-beállítások?**

A frissítés nincs hatással a hálózati beállításokat. Az összes Azure-– helyi kapcsolat változatlanok maradnak.

**Mi történik a tárolók, ha szeretnék nem kívánja frissíteni a közeljövőben?**

A régi Azure-portálon a Site Recovery-tároló támogatása szeptember 2017 indítása megszűnnek. Határozottan javasoljuk, hogy a frissítési szolgáltatás használatával helyezze át az új portál.

**Mi az áttelepítési terv jelent a saját meglévő eszközt használunk erre?**  

A Resource Manager üzembe helyezési modellben a tooling frissítése az egyik legfontosabb módosításait, figyelembe kell venni a frissítési tervek. A Recovery Services-tárolók a Resource Manager üzembe helyezési modellel alapulnak. 

**A felügyeleti-vezérlősík állásidő mennyi ideig does utolsó?**

A frissítés általában a körülbelül 15 – 30 percet vesz igénybe, és legfeljebb egy órával eltarthat.

**I vissza lehet vonni a frissítés után?**

Nem. Miután az erőforrások frissítése sikeresen befejeződött a rollback utasítás nem támogatott.

**Az előfizetés vagy erőforrások megjelenítéséhez, hogy azok frissítése is ellenőrzi?**

Igen. A platform által támogatott frissítési lehetőséget a frissítés első lépése, hogy ellenőrizze, hogy az erőforrások képesek-e a frissítésre. Ha az érvényesítés meghiúsul, a megfelelő hibaüzeneteket és figyelmeztetéseket fog kapni.

**Hogyan jelenthetem a problémát a frissítést?**

A frissítés során a esetleges hibákat tapasztal, vegye figyelembe a műveleti azonosító szerepel-e a hibát. Microsoft Support proaktív módon fog működni a probléma megoldását. Forduljon a támogatási csoportot, az előfizetés-azonosító, a tároló neve és a művelet azonosítója. Támogatás a probléma megoldásához minél gyorsabban fog működni. Nem próbálja meg újra a műveletet csak akkor vannak explicit módon kifejezetten kéri a Microsoft által.

## <a name="run-the-script"></a>A parancsfájl futtatása

A PowerShell a következő parancsot:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* Előfizetés-azonosító: Az előfizetés-azonosító, amely a tárolóra, frissítés van társítva.

* VaultName: A tárolóra, frissítés neve.

* Hely: A tárolóra, frissítés helye.

* Indítása. ResourceType: A Site Recovery HyperVRecoveryManagerVault tárolók.

* TargetResourceGroupName: A szeretné helyezni a frissített tároló erőforráscsoport. TargetResourceGroupName lehet egy meglévő erőforráscsoportot az Azure Resource Manager vagy egy újat. Ha a megadott TargetResourceGroupName nem létezik, és a tárolónak ugyanazon a helyen a frissítés részeként létrehozták. További információkért lásd: a "Erőforráscsoportok" szakaszában [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >Az erőforrás-csoport elnevezési bizonyos megkötéseknek. Tároló frissítés nem sikerülne elkerülése érdekében ügyeljen arra, hogy az elnevezési gondosan figyelje meg.
    >
    >Példa:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 - SubscriptionId 1234-54123-354354-56416-8645 - VaultName gen2dr-helyre "Észak-Európa" - ResourceType hypervrecoverymanagervault - TargetResourceGroupName abc

Ehelyett futtathatja a következő parancsfájlt. Adja meg az értékeket a szükséges paramétereket.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. A PowerShell-parancsfájlt a hitelesítő adatok megadását kéri. Kétszer meg kell adnia őket, egyszer a klasszikus üzembe helyezési modell fiókhoz tartozó és egyszer az Azure Resource Manager-fiók.

2. Után a megadott hitelesítő adatait, a parancsfájl fut egy ellenőrzést annak meghatározásához, hogy az infrastruktúra telepítése megfelel-e a korábban leírt követelményeknek.

3. Miután az Előfeltételek be van jelölve, és megerősítette, kéri a tároló frissítés folytatásához. A frissítési folyamat elindul, a tároló frissítése. A teljes frissítés 15-30 percet is igénybe vehet.

4. Miután a frissítés sikeresen befejeződött, a frissített tárolót az új Azure-portálon végezheti el.

## <a name="post-upgrade-vault-management"></a>A frissítés utáni tároló kezelése

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>A Recovery Services-tároló az Azure Site Recovery segítségével replikálása

* Másik egy régióban most megvédheti az Azure virtuális gépeken. További információkért lásd: [Azure virtuális gépek replikálása az Azure Site Recovery régiók közötti](site-recovery-azure-to-azure.md).

* További információ a VMware virtuális gépek replikálása Azure-bA: [VMware virtuális gépek replikálása az Azure Site Recovery szolgáltatással](vmware-walkthrough-overview.md).

* További információ a (VMM nélkül) a Hyper-V virtuális gépek replikálása Azure-bA: [replikálásához a Hyper-V virtuális gépek (VMM nélkül) az Azure-bA](hyper-v-site-walkthrough-overview.md).

* (A VMM-mel) a Hyper-V virtuális gépek replikálása Azure-bA kapcsolatos további információkért lásd: [replikálásához a Hyper-V virtuális gépek VMM-felhőkben az Azure Site Recovery használata az Azure portálon](vmm-to-azure-walkthrough-overview.md).

* További információ a Hyper-virtuális gépek (a VMM-mel) replikálása egy másodlagos hely: [replikálásához a Hyper-V virtuális gépek VMM-felhőkben VMM másodlagos hely az Azure portál használatával](site-recovery-vmm-to-vmm.md).

* További információ a VMware virtuális gépek replikálása másodlagos helyre: [replikálja a helyszíni VMware virtuális gépek vagy fizikai kiszolgálók egy másodlagos helyre, a klasszikus Azure portálon](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>A replikált elemek megtekintése

A következő kép bemutatja a Recovery Services tároló irányítópult-oldalon, amely megjeleníti a tároló kulcs entitások. Válassza ki, ha a védett entitás listáját a tárolóban, **Site Recovery** > **replikált elemek**.


![Replikált elemek](./media/upgrade-site-recovery-vaults/replicateditems.png)

A következő kép bemutatja a munkafolyamat a replikált elemek megtekintéséhez és a **feladatátvételi** parancsot a feladatátvétel kezdeményezése.

![Replikált elemek](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>A replikációs beállítások megtekintése

A Site Recovery-tárolóban mindegyik védelmi csoporthoz úgy van konfigurálva, a Másolás gyakoriságát, a helyreállítási pontok megőrzésének ideje, a alkalmazás alkalmazáskonzisztens pillanatképek gyakorisága és a más replikációs beállítások. A Recovery Services-tároló ezek a beállítások vannak konfigurálva replikációs házirend. A házirend neve a védelmi csoport neve vagy a *primarycloud_Policy*.

További információ a replikációs házirendhez: [replikációs házirend kezelése az Azure-bA VMware](site-recovery-setup-replication-settings-vmware.md).
