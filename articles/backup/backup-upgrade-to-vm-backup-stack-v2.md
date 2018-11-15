---
title: Frissítés az Azure virtuális gép biztonsági másolat verem v2
description: Virtuális gép biztonsági másolat verem, Resource Manager üzemi modell frissítési folyamata, és a gyakori kérdések
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 10/3/2018
ms.author: trinadhk
ms.openlocfilehash: c65cfedd398bbb18d65f36a3f2a768e11443687a
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636509"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Azure virtuális gép biztonsági mentési vermének v2 verziójára frissítsen.

A Resource Manager üzemi modell a virtuális gép (VM) biztonsági másolat verem frissítés a következő funkciófejlesztéseket nyújtja:

* Lehetővé teszi a biztonsági mentési feladatot, amely a helyreállításhoz elérhető adatátvitel befejezésére való várakozás nélkül részeként készített pillanatkép megtekintéséhez. Ez csökkenti a várakozási idő a pillanatképek visszaállításának előtt a tárolóba másolja. Emellett ennek a képességnek a további tárterületre vonatkozó követelmény, prémium szintű virtuális gépeket, kivéve az első biztonsági mentésére.  

* Csökkenti a biztonsági mentési és helyreállítási idők helyileg, a pillanatképek megőrzik a hét napja szerint.

* Lemez támogatása akár 4 TB-os mérete.

* Lehetővé teszi egy nem felügyelt virtuális gép eredeti tárfiókokban, visszaállításához használni. Ez a lehetőség létezik, akkor is, ha a virtuális gép lemezei tárfiókokban vannak elosztva. Ez felgyorsítja a visszaállítási műveletek számos különböző Virtuálisgép-konfigurációk esetében.
    > [!NOTE]
    > Ez a lehetőség nem ugyanaz, mint a virtuális gép lemezének cseréje a helyreállítási pont adatait.


## <a name="whats-changing-in-the-new-stack"></a>Mi változik a új verem?
A biztonsági mentési feladat jelenleg két fázisból áll:
1.  Virtuális gép pillanatképének elkészítése.
2.  Átvitele az Azure Backup-tárolót egy virtuális gép pillanatképét.

Helyreállítási pont számít jönnek létre csak azt követően 1. és 2 fázisban kell elvégezni. Az új stack részeként a helyreállítási pont jön létre, amint a pillanatkép befejeződött. Is visszaállíthatja a helyreállítási pont használatával ugyanezt a helyreállítási folyamatot. A helyreállítási pont típusa "pillanatkép" segítségével azonosíthatja a helyreállítási pont, az Azure Portalon. Miután a pillanatkép átkerülnek a tárolóba, a helyreállítási pont típusa változik "pillanatkép és tár."

![Virtuális gép biztonsági másolat verem Resource Manager üzemi modell – tárolási és a tárolóban lévő biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-flow.jpg)

Alapértelmezés szerint a pillanatképek őrzi meg a hét napja. Ez a funkció lehetővé teszi, hogy a visszaállítás a pillanatképek a gyorsabb befejezéséhez. Ez csökkenti az adatokat másolja vissza a tárolóból az ügyfél tárfiókja szükséges idő.

## <a name="considerations-before-upgrade"></a>Frissítés előtt megfontolandó szempontok

* A frissítés, a virtuális gép biztonsági másolat verem az alábbiak irányt, minden biztonsági mentés nyissa meg a flow-bA. Mivel a módosítás az előfizetés szintjén történik, minden virtuális gép nyissa meg a flow-bA. Az összes új szolgáltatás hozzáadása a azonos verem alapulnak. Jelenleg nem tudja vezérelni a verem házirend szintjén.

* A pillanatképek a rendszer helyben tárolja, a helyreállítási pont létrehozása növelése érdekében és visszaállítási műveletek felgyorsítása érdekében. Ennek eredményeképpen láthatja a tárolási költségek, amelyek megfelelnek a 7 napos időszakban készített pillanatkép.

* Növekményes pillanatképek lapblobként tárolt. Nem felügyelt lemezeket használó összes ügyfelei a hét nap, a pillanatképek vannak tárolva az ügyfél helyi tárfiók díjkötelesek. Mivel a visszaállítási pont gyűjtemények által felügyelt virtuális gép biztonsági mentéseinek használt blobpillanatképeket használja az alapul szolgáló tárolási szinten, a felügyelt lemezek érhet el megfelelő költségeket [blob-pillanatkép díjszabás](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges) és a növekményes-e.

* Egy prémium szintű virtuális gép egy pillanatkép helyreállítási pontból állítja vissza, ha egy ideiglenes tárolási helyet szolgál, míg a virtuális gép létrehozása.

* Prémium szintű tárfiókok esetén az azonnali helyreállítási pontok száma legfeljebb 10 TB-os felé készített pillanatképeket lefoglalt terület.

> [!NOTE]
> Frissítés az Azure virtuális gép biztonsági mentési vermének v2 verziójára kérhet támogatást az Azure Backup az a [SSD standard szintű Managed Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

## <a name="upgrade"></a>Frissítés
### <a name="the-azure-portal"></a>Az Azure Portal
Ha használja az Azure Portalon, a tároló irányítópultjának megjelenik egy értesítés. Ezt az értesítést a nagyméretű lemezek támogatása és a biztonsági mentés és visszaállítás sebességének fejlesztései vonatkozik. Másik lehetőségként megnyithatja a tároló frissítési lehetősége tulajdonságok lapján.

![A virtuális gép biztonsági másolat verem Resource Manager üzemi modell – értesítés a támogatás megszűnéséről biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-banner.png)

Az új stack verziójára való frissítéshez egy képernyő megnyitásához, válassza ki a szalagcímet.

![A virtuális gép biztonsági másolat verem Resource Manager üzemi modell – biztonsági mentési feladat frissítése](./media/backup-azure-vms/instant-rp.png)

### <a name="powershell"></a>PowerShell
Futtassa a következő parancsmagokat egy emelt szintű PowerShell terminálon:
1.  Jelentkezzen be az Azure-fiókjával:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Regisztrálni kívánt előfizetés kiválasztásához:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Ez az előfizetés regisztrálása:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```
### <a name="cli"></a>parancssori felület
Futtassa a következő parancsokat egy rendszerhéjból:
1.  Jelentkezzen be az Azure-fiókjával:

    ```
    az login
    ```

2.  Regisztrálni kívánt előfizetés kiválasztásához:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Ez az előfizetés regisztrálása:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Győződjön meg arról, hogy a frissítés befejeződött
### <a name="powershell"></a>PowerShell
Egy rendszergazda jogú PowerShell terminálból futtassa a következő parancsmagot:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>parancssori felület
Ashell futtassa a következő parancsot:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Ugyanakkor "Regisztrálva", majd az előfizetés biztonsági mentési vermének V2 van frissítve.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi kérdések és válaszok összegyűjtése a fórumok és a vásárlói kérdésre.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>Negatívan befolyásolja jelenlegi biztonsági mentések V2 verzióra?
V2 verzióra frissíti, van-e a jelenlegi biztonsági mentések nem érinti, és nem kell konfigurálnia a környezetben. A frissítés és a biztonsági mentési környezet továbbra is működik, mert.

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>Mit költséggel jár a frissítsen az Azure VM Backup stack v2?
Nincs frissítés a verem v2 költség nélkül. A pillanatképek rendszer helyben tárolja a helyreállítási pont létrehozása felgyorsítása és visszaállítási műveletek. Ennek eredményeképpen láthatja a tárolási költségek, amelyek megfelelnek a 7 napos időszakban készített pillanatképeket.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Megnöveli a prémium szintű storage fiók pillanatképekre vonatkozó korlát 10 TB-os által a verem v2 verzióra?
Nem, a tárolási fiók továbbra is megmarad, 10 TB-os teljes pillanatkép-korlátozást.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>A prémium szintű Storage-fiókok hajtsa végre az azonnali helyreállítási pontok készített pillanatkép foglalhat el a 10 TB-os pillanatképekre vonatkozó korlát?
Igen, prémium szintű tárfiókok esetén az azonnali helyreállítási pontok, készített pillanatképeket foglalhat el a lefoglalt 10 TB-nyi területet.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Hogyan működik a pillanatkép a 7 napos időszakban?
Minden nap, egy új pillanatkép készítésének időpontjában. Hét egyes pillanatképek vannak. A szolgáltatásnak nincs **nem** készítsen róla egy másolatot az első napon, és adja hozzá a módosítások a következő hat nap.

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Egy v2 pillanatkép, olyan növekményes pillanatkép vagy a teljes pillanatkép?
Nem felügyelt lemezek növekményes pillanatképek szolgálnak. Felügyelt lemezek visszaállítási pont gyűjteményt használ az Azure Backup által létrehozott blob-pillanatképekkel, és ezért növekményes.

### <a name="how-to-get-standard-ssd-managed-disk-support-for-a-virtual-machine"></a>Standard SSD beszerzése felügyelt virtuális gépek számára?
Frissítés az Azure virtuális gép biztonsági mentési vermének v2 verziójára kérhet támogatást az Azure Backup az a [SSD standard szintű Managed Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).
