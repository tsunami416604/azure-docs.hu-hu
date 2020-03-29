---
title: Az Azure azonnali visszaállítási képessége
description: Azure Instant Restore Képesség és GYIK a virtuális gép biztonsági mentési verem, Resource Manager telepítési modell
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705428"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Az Azure Backup azonnali visszaállítási funkcióval továbbfejlesztett biztonsági mentési és visszaállítási teljesítményre számíthat

> [!NOTE]
> A felhasználók visszajelzései alapján átnevezzük **a vm biztonsági mentési verem V2-t** **azonnali visszaállításra,** hogy csökkentsük az Azure Stack funkcióival való összetévesztést.
> Az összes Azure biztonsági mentési felhasználó frissítése azonnali **visszaállításra**lett frissítve.

Az azonnali visszaállítás új modellje a következő funkciófejlesztéseket biztosítja:

* Képes használni a biztonsági mentési feladat részeként készített pillanatképek, amely a helyreállításhoz elérhető, anélkül, hogy megvárna a tárolóba irányuló adatátvitel befejezéséhez. Csökkenti a várakozási időt a pillanatképek másolása a tárolóba visszaállítás a visszaállítás előtt.
* Csökkenti a biztonsági mentési és visszaállítási időt azáltal, hogy a pillanatképeket helyileg, alapértelmezés szerint két napig megőrzi. Ez az alapértelmezett pillanatkép-megőrzési érték 1 és 5 nap közötti bármely értékre konfigurálható.
* Támogatja a 32 TB-os lemezméretet. A lemezek átméretezése nem ajánlott az Azure Backup.
* Támogatja a standard SSD-lemezeket, valamint a standard HDD-lemezeket és a prémium szintű SSD-lemezeket.
* Nem felügyelt virtuális gépek eredeti tárfiókok (lemezenként) használata visszaállításkor. Ez a képesség akkor is fennáll, ha a virtuális gép rendelkezik a tárfiókok között elosztott lemezekkel. Felgyorsítja a visszaállítási műveleteket a virtuális gép konfigurációk széles köréhez.
* A prémium szintű tárhelyet használó virtuális gépek biztonsági mentése esetén az Azonnali visszaállítás szolgáltatással azt javasoljuk, hogy a teljes lefoglalt tárhely *50%-os* szabad területét különítse el, ami **csak** az első biztonsági mentéshez szükséges. Az 50%-os szabad terület nem követelmény a biztonsági mentéshez az első biztonsági mentés befejezése után.

## <a name="whats-new-in-this-feature"></a>Újdonságok ebben a funkcióban

Jelenleg a biztonsági mentési feladat két fázisból áll:

1. Virtuális gép pillanatkép készítése.
2. Virtuálisgép-pillanatkép átvitele az Azure Recovery Services-tárolóba.

A helyreállítási pont csak az 1. A frissítés részeként egy helyreállítási pont jön létre, amint a pillanatkép befejeződött, és ez a helyreállítási pont pillanatkép típusú lehet használni, hogy egy visszaállítási ugyanazon visszaállítási folyamat használatával. Ezt a helyreállítási pontot az Azure Portalon a "pillanatkép" használatával a helyreállítási pont típusa, és miután a pillanatkép át a tárolóba, a helyreállítási pont típusa "pillanatkép és a tároló".

![Biztonságimentési feladat a virtuális gép biztonsági mentési veremében az Erőforrás-kezelő telepítési modelljében – tároló és tároló](./media/backup-azure-vms/instant-rp-flow.png)

Alapértelmezés szerint a pillanatképek két napig megőrződnek. Ez a funkció lehetővé teszi a visszaállítási műveletet ezekből a pillanatképekből a visszaállítási idők csökkentésével. Csökkenti az adatok átalakításához és a tárolóból való visszamásolásához szükséges időt.

## <a name="feature-considerations"></a>A szolgáltatással kapcsolatos szempontok

* A pillanatképek a lemezekkel együtt tárolódnak a helyreállítási pont létrehozásának fokozása és a visszaállítási műveletek felgyorsítása érdekében. Ennek eredményeképpen az ebben az időszakban készített pillanatképeknek megfelelő tárolási költségeket fog látni.
* A növekményes pillanatképek lapblobokként tárolódnak. A nem felügyelt lemezeket használó összes felhasználónak díjat kell fizetnie a helyi tárfiókban tárolt pillanatképekért. Mivel a felügyelt virtuális gépek biztonsági másolatai által használt visszaállítási pontgyűjtemények blob pillanatképeket használnak az alapul szolgáló tárolási szinten, a felügyelt lemezeknél a blob pillanatkép-díjszabásának megfelelő költségek et fog látni, és növekményesek.
* Prémium szintű tárfiókok esetén az azonnali helyreállítási pontokhoz készített pillanatképek a lefoglalt terület 10 TB-os korlátjába számítanak.
* A pillanatkép-megőrzés konfigurálása a visszaállítási igények alapján konfigurálható. A követelménytől függően beállíthatja a pillanatkép-megőrzési legalább egy napra a biztonsági mentési szabályzat panelen az alábbiak szerint. Ez segít a pillanatkép-megőrzés költségeinek megtakarításában, ha nem hajt végre gyakran visszaállítást.
* Ez egy irányított frissítés, miután frissített azonnali visszaállításra, nem mehet vissza.

>[!NOTE]
>Ezzel az azonnali visszaállítási frissítéssel az összes ügyfél pillanatkép-megőrzési időtartama **(új és meglévő is tartalmazza)** két napos alapértelmezett értékre lesz állítva. Az időtartamot azonban a szükséglet szerint 1 és 5 nap közötti értékre állíthatja be.

## <a name="cost-impact"></a>Költséghatás

A növekményes pillanatképek a virtuális gép tárfiók, amely az azonnali helyreállításhoz használt. A növekményes pillanatkép azt jelenti, hogy a pillanatkép által elfoglalt terület megegyezik a pillanatkép létrehozása után írt oldalak által elfoglalt területtel. A számlázás továbbra is a pillanatkép által elfoglalt GB-onkénti használt területre vonatkozik, és a GB-onkénti ár megegyezik az [árképzési oldalon említettekével.](https://azure.microsoft.com/pricing/details/managed-disks/) A nem felügyelt lemezeket használó virtuális gépek esetében a pillanatképek az egyes lemezek Virtuálismerevlemez-fájljának menüjében láthatók. Felügyelt lemezek esetén a pillanatképek egy kijelölt erőforráscsoportban lévő visszaállítási pontgyűjtemény-erőforrásban tárolódnak, és maguk a pillanatképek közvetlenül nem láthatók.

>[!NOTE]
> A pillanatkép-megőrzés heti szabályzatok esetén 5 nap.

## <a name="configure-snapshot-retention"></a>Pillanatkép-megőrzés konfigurálása

### <a name="using-azure-portal"></a>Az Azure Portal használata

Az Azure Portalon láthatja a **virtuális gép biztonsági mentési szabályzata** panelen az azonnali **visszaállítás** szakaszban hozzáadott mezőt. Módosíthatja a pillanatkép-megőrzési időtartamot a **virtuális gép biztonsági mentési szabályzata** panelen az adott biztonsági mentési szabályzathoz társított összes virtuális géphez.

![Azonnali visszaállítási képesség](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>A PowerShell használata

>[!NOTE]
> Az Az PowerShell 1.6.0-s verziójától kezdve frissítheti az azonnali visszaállítás pillanatkép-megőrzési időszakát a szabályzatban a PowerShell használatával

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Az egyes házirendek alapértelmezett pillanatkép-megőrzése két napra van állítva. A felhasználó az értéket legalább 1-re, de legfeljebb öt napra módosíthatja. Heti szabályzatok esetén a pillanatkép-megőrzés öt napra van rögzítve.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Milyen költségvonzatokkal jár az azonnali visszaállítás?

A pillanatképek a lemezekkel együtt tárolódnak a helyreállítási pont létrehozásának és a visszaállítási műveleteknek a felgyorsítása érdekében. Ennek eredményeképpen a virtuális gép biztonsági mentési szabályzata részeként kiválasztott pillanatkép-megőrzési költségek et.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>A prémium szintű storage-fiókokban az azonnali helyreállítási ponthoz készített pillanatképek foglalják el a 10 TB-os pillanatkép korlátot?

Igen, a prémium szintű tárfiókok az azonnali helyreállítási pont hozkészített pillanatképek elfoglalják 10 TB lefoglalt pillanatkép terület.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hogyan működik a pillanatkép-megőrzési munka az öt napos időszakban?

Minden nap egy új pillanatkép készül, majd öt egyedi növekményes pillanatképek. A pillanatkép mérete az adatforgalomtól függ, amelyek a legtöbb esetben 2%7% körül vannak.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Az azonnali visszaállítás pillanatképe egy növekményes pillanatkép vagy teljes pillanatkép?

Az azonnali visszaállítási képesség részeként készített pillanatképek növekményes pillanatképek.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hogyan számíthatom ki az azonnali visszaállítási funkció miatti hozzávetőleges költségnövekedést?

Ez a virtuális gép lemorzsolódásátótól függ. Állandó állapotban feltételezheti, hogy a költségnövekedése = Pillanatkép-megőrzési időszak napi lemorzsolódás virtuálisgép-tárolási költség gb-onként.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Ha a visszaállítási pont helyreállítási típusa "Pillanatkép és tároló", és visszaállítási műveletet hajtok végre, melyik helyreállítási típust fogja használni?

Ha a helyreállítási típus "pillanatkép és tároló", visszaállítás automatikusan történik a helyi pillanatkép, amely sokkal gyorsabb lesz, mint a tárolóból végzett visszaállítás.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Mi történik, ha kiválasztom a visszaállítási pont (Tier 2) megőrzési időszakát, amely kisebb, mint a pillanatkép (Tier1) megőrzési időszak?

Az új modell csak akkor engedélyezi a visszaállítási pont (Tier2) törlését, ha a pillanatkép (Tier1) törlődik. Azt javasoljuk, hogy a pillanatkép-megőrzési időszaknál nagyobb visszaállítási pont (Tier2) megőrzési időszakot ütemező.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Miért létezik a pillanatképem a beállított megőrzési időszak után is a biztonsági mentési szabályzatban?

Ha a helyreállítási pont pillanatképpel rendelkezik, és ez a legújabb elérhető RP, a következő sikeres biztonsági mentésig megmarad. Ez a tervezett "szemétgyűjtés" (GC) házirend ma, amely előírja, hogy legalább egy legújabb RP mindig jelen van abban az esetben, ha az összes biztonsági mentések tovább nem miatt egy probléma a virtuális gép. Normál esetben a rp-k a lejáratuk után legfeljebb 24 órán belül törlődnek.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Nincs szükségem azonnali visszaállítási funkcióra. Lehet letiltani?

Az azonnali visszaállítás mindenki számára engedélyezve van, és nem tiltható le. A pillanatkép-megőrzés legalább egy napra csökkentheti.

>[!NOTE]
> **Az Azure Backup mostantól támogatja a szelektív lemezbiztonsági mentést és visszaállítást az Azure virtuális gép biztonsági mentési megoldásával.**
>
>Az Azure Backup ma támogatja az összes lemez (operációs rendszer és adatok) biztonsági mentését a virtuális gépben a virtuális gép biztonsági mentési megoldásának használatával. A exclude-disk funkcióval lehetőséget kap arra, hogy egy vagy néhány biztonsági másolatot készítsen a virtuális gép számos adatlemezéről. Ez hatékony és költséghatékony megoldást nyújt a biztonsági mentési és visszaállítási igényekhez. Minden helyreállítási pont a biztonsági mentési műveletben szereplő lemezek adatait tartalmazza, ami lehetővé teszi, hogy a lemezek egy részhalmaza visszaálljon az adott helyreállítási pontról a visszaállítási művelet során. Ez a pillanatképből és a tárolóból történő visszaállításra vonatkozik.
>
>**Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com**
