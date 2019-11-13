---
title: Azure azonnali visszaállítási képesség
description: Azure azonnali visszaállítási képesség és gyakori kérdések a virtuális gépek biztonsági mentési vereméhez, Resource Manager-alapú üzemi modell
ms.reviewer: sogup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dacurwin
ms.openlocfilehash: 1faa8efc5cbb39f94a390a0cf32dd2cd1ef0793c
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012109"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Továbbfejlesztett biztonsági mentési és visszaállítási teljesítmény Azure Backup azonnali visszaállítási képességgel

> [!NOTE]
> A felhasználók visszajelzései alapján átnevezzük a **VM Backup stack v2** -et az **azonnali visszaállításra** , hogy csökkentse a Azure stack funkcióinak összekeveredését.
> Az Azure Backup összes felhasználója most már frissítve lett az **azonnali visszaállításra**.

Az azonnali visszaállítás új modellje a következő funkciókat biztosítja:

* A helyreállításhoz elérhető biztonsági mentési feladatok részeként készített pillanatképek használatának lehetősége anélkül, hogy a tárolóba való adatátvitelre kellene várni. Ez csökkenti a várakozási időt a pillanatképek másolásához a tárolóba a visszaállítás elindítása előtt.
* Csökkenti a biztonsági mentési és visszaállítási időt a pillanatképek helyi megőrzésével, alapértelmezés szerint két napig. Ez az alapértelmezett pillanatkép-megőrzési érték 1 és 5 nap közötti értékre állítható be.
* A legfeljebb 4 TB méretű lemezeket támogat. A lemezek átméretezését Azure Backup nem ajánlott. A 4 TB-nál nagyobb méretű lemezek Azure Backup nagyméretű lemezes támogatásának korlátozott nyilvános előzetes verziójára való regisztráláshoz lásd: a [virtuális gép biztonsági mentése, amelynek mérete legfeljebb 30 TB](backup-azure-vms-introduction.md#limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb).
* A standard SSD lemezeket standard HDD lemezekkel és prémium SSD lemezekkel együtt támogatja.
* A nem felügyelt virtuális gépek eredeti Storage-fiókjai (lemezenként) való használatának lehetősége a visszaállításkor. Ez a képesség akkor is fennáll, ha a virtuális gépnek vannak olyan lemezek, amelyek a Storage-fiókok között oszlanak el. A rendszer a virtuálisgép-konfigurációk széles körére felgyorsítja a visszaállítási műveleteket.
* A Premium Storage szolgáltatást használó virtuális gépek biztonsági mentését azonnali visszaállítással ajánlott kiosztani a teljes lefoglalt tárterület *50%-os* szabad területét, amely **csak** az első biztonsági mentéshez szükséges. Az első biztonsági mentés befejezése után az 50%-os szabad terület nem követelmény a biztonsági mentéshez.

## <a name="whats-new-in-this-feature"></a>A szolgáltatás újdonságai

A biztonsági mentési feladatok jelenleg két fázisból állnak:

1. A virtuális gép pillanatképének készítése.
2. Virtuális gép pillanatképének továbbítása az Azure Recovery Services-tárba.

A helyreállítási pontok csak az 1. és 2. fázisok befejezése után tekinthetők meg. A frissítés részeként létrejön egy helyreállítási pont, amint a pillanatkép elkészült, és a pillanatkép-típus helyreállítási pontja ugyanazzal a visszaállítási folyamattal végezheti el a visszaállítást. Ezt a helyreállítási pontot a Azure Portal a helyreállítási pont típusaként a "pillanatkép" használatával azonosíthatja, és a pillanatkép a tárolóba való átvitele után a helyreállítási pont típusa a "Snapshot and Vault" értékre változik.

![Biztonsági mentési feladatok a virtuális gépek biztonsági mentési verem Resource Manager-alapú üzemi modelljében – tárolás és tár](./media/backup-azure-vms/instant-rp-flow.png)

Alapértelmezés szerint a pillanatképek két napig őrződnek meg. Ez a funkció lehetővé teszi ezen Pillanatképek visszaállítási műveletét a visszaállítási idő csökkentésével. Ez csökkenti az adatok tárolóból való visszaalakításához és másolásához szükséges időt.

## <a name="feature-considerations"></a>A szolgáltatással kapcsolatos megfontolások

* A rendszer a pillanatképeket a lemezekkel együtt tárolja a helyreállítási pontok létrehozásának és a visszaállítási műveletek felgyorsításának fokozása érdekében. Ennek eredményeképpen megjelennek a tárolási költségek, amelyek megfelelnek az adott időszakban készített pillanatképeknek.
* A növekményes pillanatképeket blobként tárolja a rendszer. A nem felügyelt lemezeket használó összes felhasználó díja a helyi Storage-fiókban tárolt pillanatképeknek. Mivel a felügyelt virtuális gépek biztonsági mentései által használt visszaállítási pontok gyűjteményei blob-pillanatképeket használnak az alapul szolgáló tárolási szinten, a felügyelt lemezek esetében látni fogja a blob-pillanatkép díjszabásának megfelelő költségeket, és növekményes.
* A prémium szintű Storage-fiókok esetében az azonnali helyreállítási pontok számára készített Pillanatképek a lefoglalt terület 10 TB-os korlátján alapulnak.
* Lehetőség van a pillanatkép-megőrzés konfigurálására a visszaállítási igények alapján. A követelménytől függően a biztonsági mentési szabályzat paneljén legalább egy napig állíthatja be a pillanatkép-megőrzést, az alább leírtak szerint. Ezzel a művelettel megtakaríthatja a pillanatképek megőrzésének költségeit, ha nem végez gyakran visszaállítást.
* Ez egy irányított frissítés, amely az azonnali visszaállításra való frissítés után nem mehet vissza.

>[!NOTE]
>Ezzel az azonnali visszaállítással az összes ügyfél pillanatkép-megőrzési időtartamát (az**új és a meglévőket is beleértve**) két nap alapértelmezett értékre állítja a rendszer. Az időtartamot azonban igény szerint állíthatja be az 1 és 5 nap közötti értékre.

## <a name="cost-impact"></a>Cost Impact

A növekményes pillanatképeket a rendszer a virtuális gép Storage-fiókjában tárolja, amely az azonnali helyreállításhoz használható. A növekményes pillanatkép azt jelenti, hogy a pillanatkép által elfoglalt terület egyenlő a pillanatkép létrehozása után írt lapok által elfoglalt területtel. A számlázási szolgáltatás továbbra is a pillanatkép által foglalt GB-nyi felhasznált területre vonatkozik, és a GB-onként érvényes ár megegyezik a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/managed-disks/)említettek szerint.

>[!NOTE]
> Heti szabályzatok esetén a pillanatkép-megőrzés 5 napig rögzített.

## <a name="configure-snapshot-retention"></a>Pillanatkép-megőrzés konfigurálása

### <a name="using-azure-portal"></a>Az Azure Portal használata

A Azure Portalban megjelenik egy mező, amelyet a **virtuális gép biztonsági mentési szabályzat** paneljén, az **azonnali visszaállítás** szakaszban találhat. A pillanatképek megőrzésének időtartamát a **virtuális gép biztonsági mentési szabályzatának** paneljén módosíthatja az adott biztonsági mentési szabályzathoz társított összes virtuális géphez.

![Azonnali visszaállítási képesség](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>A PowerShell használata

>[!NOTE]
> Az az PowerShell Version 1.6.0-től kezdődően a PowerShell használatával frissítheti az azonnali visszaállítás pillanatképének megőrzési időtartamát a szabályzatban

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Az egyes szabályzatok alapértelmezett pillanatkép-megőrzése két napra van állítva. A felhasználó módosíthatja az értéket legalább 1 és legfeljebb öt napig. A heti házirendek esetében a pillanatképek megőrzésének öt napig kell megállapítania.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Milyen költségeket érint az azonnali visszaállítás?

A rendszer a pillanatképeket a lemezekkel együtt tárolja a helyreállítási pontok létrehozásának és visszaállítási műveleteinek felgyorsításához. Ennek eredményeképpen a virtuális gép biztonsági mentési szabályzatának részeként kiválasztott pillanatkép-megőrzésnek megfelelő tárolási költségek láthatók.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Storage fiókok esetében az azonnali helyreállítási ponthoz készített Pillanatképek elfoglalják a 10 TB-os pillanatkép-korlátot?

Igen, a Premium Storage-fiókok esetében az azonnali helyreállítási ponthoz készített Pillanatképek 10 TB-nyi lefoglalt pillanatképet foglalnak magukban.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hogyan működik a pillanatképek megőrzése az ötnapos időszakban?

Minden nap új pillanatkép készül, és öt egyéni növekményes pillanatkép van. A pillanatkép mérete az adatváltozástól függ, amely a legtöbb esetben a 2%-7% körül van.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Egy növekményes pillanatkép vagy teljes pillanatkép azonnali visszaállítási pillanatképe?

Az azonnali visszaállítási képesség részeként készített Pillanatképek növekményes Pillanatképek.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hogyan számítható ki az azonnali visszaállítási funkció miatt megbecsült költségmegtakarítás?

Ez a virtuális gép adatforgalmával függ. Állandó állapot esetén feltételezheti, hogy a költségeket a rendszer a virtuális gép tárterületének napi adatforgalmára vonatkozó, óránkénti adatváltozási időszakra vonatkozóan veszi igénybe.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Ha egy visszaállítási pont helyreállítási típusa "Snapshot and Vault", és elvégezek egy visszaállítási műveletet, amely a helyreállítási típust fogja használni?

Ha a helyreállítás típusa "Snapshot and Vault", a visszaállítás automatikusan a helyi pillanatképből történik, ami sokkal gyorsabb lesz, mint a tárból végzett visszaállítás.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Mi történik, ha a visszaállítási pont megőrzési időtartamát (2. szakasz) kisebb, mint a pillanatkép (Tier1) megőrzési ideje?

Az új modell nem engedélyezi a visszaállítási pont (szint) törlését, kivéve, ha a pillanatképet (Tier1) törli. Azt javasoljuk, hogy a visszaállítási pont (szint) adatmegőrzési időszaka nagyobb legyen, mint a pillanatkép megőrzési időtartama.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Miért van a pillanatképem még a biztonsági mentési szabályzatban megadott megőrzési időszak után is?

Ha a helyreállítási pont pillanatképtel rendelkezik, és ez a legújabb RP, akkor a rendszer megőrzi a következő sikeres biztonsági mentés idejét. Ez a tervezett "Garbage Collection" (GC) szabályzatnak megfelelően, amely a virtuális gép hibája miatt nem tesz kötelezővé legalább egy legújabb RP-t, hogy mindig legyen jelen. Normál forgatókönyvekben az RPs-t a lejárat után legfeljebb 24 órával töröljük.
