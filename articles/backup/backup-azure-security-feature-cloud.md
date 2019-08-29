---
title: Biztonsági funkciók a Azure Backupt használó Felhőbeli munkaterhelések védelmének biztosításához
description: Megtudhatja, hogyan teheti biztonságosabbá a biztonsági mentéseket a Azure Backup biztonsági funkciói segítségével.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/04/2019
ms.author: dacurwin
ms.openlocfilehash: 938c65371b05807c774c9f2d73d4c27efe706c47
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143569"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Biztonsági funkciók a Azure Backupt használó Felhőbeli munkaterhelések védelmének biztosításához

A biztonsági problémákkal, például a kártevővel, a ransomware és a behatolással kapcsolatos problémák egyre nagyobbak. Ezek a biztonsági problémák költségesek lehetnek a pénz és az adatmennyiség tekintetében. Az ilyen támadások elleni védelem érdekében Azure Backup mostantól biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében. Az egyik ilyen funkció a Soft delete. A helyreállítható törléssel, még akkor is, ha egy rosszindulatú szereplő töröl egy virtuális gép biztonsági mentését (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat 14 további napig marad, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. Ezen további 14 nap biztonsági mentési adatok megőrzése a "Soft Delete" állapotban nem jár költséggel az ügyfélnek.

> [!NOTE]
> A helyreállítható törlés csak a törölt biztonsági mentési adatvédelmet védi. Ha egy virtuális gépet biztonsági mentés nélkül töröl, a helyreállítható funkció nem fogja megőrizni az adatvédelmet. A teljes rugalmasság biztosítása érdekében minden erőforrást Azure Backup védelemmel kell ellátni.
>

## <a name="soft-delete"></a>Helyreállítható törlés

### <a name="supported-regions"></a>Támogatott régiók

A Soft delete jelenleg az USA nyugati középső régiójában támogatott.

### <a name="soft-delete-for-vms"></a>Soft Delete a virtuális gépekhez

1. Egy virtuális gép biztonsági mentési adatmennyiségének törléséhez le kell állítani a biztonsági mentést. A Azure Portal lépjen a Recovery Services-tárolóba, kattintson a jobb gombbal a biztonsági mentési elemre, és válassza a **biztonsági mentés leállítása**lehetőséget.

    ![Azure Portal biztonsági másolati elemek képernyőképe](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. A következő ablakban lehetősége lesz törölni vagy megőrizni a biztonsági másolati adatait. Ha a biztonsági másolati elemek **törlése** és a **biztonsági mentés leállítása**lehetőséget választja, a virtuális gép biztonsági mentése nem lesz véglegesen törölve. Ehelyett a rendszer 14 napig őrzi meg a biztonsági mentési adatait a helyreállítható törölt állapotban. Ha a **biztonsági mentési adatok törlése** lehetőség van kiválasztva, a rendszer az e-mailek törlésére vonatkozó riasztást küldi el a felhasználó számára, hogy 14 nap maradjon a biztonsági mentési adatok megőrzéséhez. Emellett e-mailben értesítést küldünk a 12. napon arról, hogy a törölt adat feltámasztása még két nappal megmaradt. A törlés Elhalasztva marad a 15. napon, amikor végleges törlés történik, és a rendszer a végleges e-mail riasztást küldi el az adat végleges törléséről.

    ![Képernyőkép a Azure Portalről, a biztonsági mentési képernyő leállítása](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. A 14 nap során a Recovery Services-tárolóban a helyreállított virtuális gép megjelenik egy piros "Soft-Delete" ikon mellett.

    ![Képernyőkép a Azure Portalről, a virtuális gép a Soft delete állapotában](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

> [!NOTE]
> Ha a tárolóban nem találhatók meg a helyreállított biztonsági mentési elemek, a tár ekkor nem törölhető. A biztonsági mentési elemek végleges törlését követően próbálja meg a tár törlését, és nincs olyan elem, amely a tárolóban marad.

4. A helyreállított virtuális gép visszaállításához először törölni kell a szolgáltatást. A törlés visszavonásához válassza a Soft-Deleted VM elemet, majd kattintson a **Törlés**visszavonása lehetőségre.

     ![Azure Portal képernyőkép a virtuális gép törléséről](./media/backup-azure-security-feature-cloud/choose-undelete.png)

Megjelenik egy ablak, amely figyelmeztetést jelenít meg, ha a törlés lehetőség van kiválasztva, a virtuális gép összes visszaállítási pontja törölve lesz, és a visszaállítási művelet elvégezhető. A virtuális gép "megőrzött adatmegőrzés" állapotban marad, a biztonsági mentések felfüggesztésével és a biztonsági mentési szabályzat hatályba lépésével megőrizve.

 ![Azure Portal képernyőképe – virtuális gép törlésének megerősítése](./media/backup-azure-security-feature-cloud/undelete-vm.png)

Ezen a ponton a virtuális GÉPET a kiválasztott visszaállítási pontról a **virtuális gép visszaállítása** lehetőség kiválasztásával is visszaállíthatja.  

   ![Képernyőkép a Azure Portalről, virtuális gép visszaállítása lehetőség](./media/backup-azure-security-feature-cloud/restore-vm.png)

> [!NOTE]
> A rendszer csak akkor futtatja a szemét-gyűjtőt, amikor a felhasználó elvégezte a **biztonsági mentési** művelet folytatását.

5. A törlési folyamat befejezése után az állapot vissza fog térni a "biztonsági mentés leállítása az adatmegőrzéshez" értékre, majd a **biztonsági mentés folytatása**lehetőségre kattint. A **biztonsági mentés folytatása** művelet visszaadja az aktív állapotú biztonsági mentési elemet, amely a felhasználó által a biztonsági mentési és adatmegőrzési ütemtervet definiáló biztonsági mentési házirendhez van társítva.

      ![Képernyőkép a Azure Portalről, biztonsági mentés folytatása lehetőség](./media/backup-azure-security-feature-cloud/resume-backup.png)

Ez a folyamatábra a biztonsági másolati elemek különböző lépéseit és állapotait mutatja be:

   ![A nem törölt biztonságimásolat-elemek életciklusa](./media/backup-azure-security-feature-cloud/lifecycle.png)

További információkért lásd az alábbi [Gyakori kérdések](backup-azure-security-feature-cloud.md#frequently-asked-questions) szakaszt.

## <a name="other-security-features"></a>Egyéb biztonsági funkciók

### <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az Azure Storage automatikusan titkosítja az adatait a felhőben való megőrzés során. A titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz. A Azure Backup a tárolása előtt automatikusan titkosítja az adattitkosítást. Az Azure Storage visszafejti az adatok beolvasása előtt.  

Az Azure-on belül az Azure Storage és a tároló közötti adatátvitel HTTPS-védelemmel történik. Ezek az adatközpontok az Azure gerinc hálózatán maradnak.

További információ: az [Azure Storage titkosítása](https://docs.microsoft.com/en-in/azure/storage/common/storage-service-encryption)inaktív adatokhoz.

### <a name="vm-encryption"></a>VM-titkosítás

A Azure Backup szolgáltatás használatával biztonsági mentést készíthet és visszaállíthat Windows vagy Linux rendszerű Azure-beli virtuális gépeket (VM-ket) titkosított lemezekkel. Útmutatásért tekintse meg a [titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption)című témakört.

### <a name="protection-of-azure-backup-recovery-points"></a>Azure Backup helyreállítási pontok védelme

A Recovery Services-tárolók által használt Storage-fiókok el vannak különítve, és a felhasználók nem férhetnek hozzá semmilyen kártékony célra. A hozzáférés csak Azure Backup felügyeleti műveleteken, például a visszaállításon keresztül engedélyezett. Ezeket a kezelési műveleteket szerepköralapú Access Controlon (RBAC) keresztül vezérli a rendszer.

További információ: [szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](https://docs.microsoft.com/en-us/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="soft-delete"></a>Helyreállítható törlés

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Engedélyezni kell a Soft-delete szolgáltatást minden tárolón?

Nem, alapértelmezés szerint a Recovery Services-tárolók számára van felépítve és engedélyezve.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Megadhatom, hogy a törlési művelet befejezése után a rendszer hány napig őrizze meg az adataimat a törölt állapotban?

Nem, a törlési művelet után a rendszer 14 napig további megőrzési időt rögzített.
  
#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Fizetnem kell a további 14 napos adatmegőrzési díjat?

Nem, ez a 14 napos további megőrzés a Soft-delete funkció részeként díjmentes.
  
#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Végezhetek visszaállítási műveletet, ha az adatok helyreállítható törlési állapotban vannak?

Nem, vissza kell állítania a nem törölt erőforrás törlését a visszaállításhoz. A törlési művelet visszaállítja az erőforrást a **stop Protection szolgáltatásba, és megőrzi** az adatállapotot, ahol bármikor visszaállíthatja azokat. Ebben az állapotban a szemetet gyűjtő szüneteltetve marad.
  
#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>A pillanatképek ugyanazt az életciklust követik, mint a tároló helyreállítási pontjai?

Igen.
  
#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hogyan indíthatom újra az ütemezett biztonsági mentéseket egy helyreállított erőforráshoz?

Törlés után a folytatási művelet ismét védi az erőforrást. A folytatási művelet társít egy biztonsági mentési szabályzatot az ütemezett biztonsági mentések a kiválasztott megőrzési időtartammal való elindításához. Emellett a szemetet gyűjtő is fut, amint a folytatási művelet befejeződik. Ha olyan helyreállítási pontról szeretne visszaállítást végezni, amelyik lejárt a lejárati dátumnál, azt javasoljuk, hogy a folytatási művelet elindítása előtt végezze el ezt a műveletet.
  
#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Törölhetem a tárolót, ha a tárolóban nem találhatók a törölt elemek?

Recovery Services tár nem törölhető, ha a tárolóban található helyreállítható állapotú biztonsági másolati elemek vannak. A törölt elemek véglegesen törlődnek a törlési művelet 14 napja után. A tárolót csak akkor törölheti, ha az összes helyreállított elemet törölte.  

#### <a name="how-can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Hogyan törölhetem a 14 napnál régebbi adattörlési időszakot a törlés után?

A törlést követő 14 nap előtt nincs lehetőség az adattörlésre. Ha blokkoló vagy megfelelőségi probléma merül fel, forduljon a Microsoft ügyfélszolgálatához.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Végrehajthatók a helyreállítható törlési műveletek a PowerShellben vagy a parancssori felületen?

Nem, a PowerShell vagy a parancssori felület támogatása jelenleg nem érhető el.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>A Soft delete támogatott más felhőalapú számítási feladatokhoz, például az Azure-beli virtuális gépeken SQL Server és az Azure-beli virtuális gépeken SAP HANA?

Nem. Jelenleg az Azure Virtual Machines szolgáltatásban csak a Soft delete használata támogatott.

## <a name="next-steps"></a>További lépések

* További információ [a Azure Backup biztonsági attribútumairól](https://docs.microsoft.com/en-us/azure/backup/backup-security-attributes).
