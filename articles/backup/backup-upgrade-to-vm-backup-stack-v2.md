---
title: Az Azure Resource Manager telepítési modelljét Azure virtuális gép biztonsági mentési csomagjának frissítése
description: Virtuális gép biztonsági mentési csomagjának, erőforrás-kezelő telepítési modell frissítése-folyamat és a gyakori kérdések
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606902"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Az Azure Resource Manager telepítési modelljét Azure virtuális gép biztonsági mentési csomagjának frissítése
A Resource Manager üzembe helyezési modellben a virtuális gép (VM) biztonsági mentési verem frissítés a következő továbbfejlesztett szolgáltatásokat nyújtja:
* Tekintse meg a biztonsági mentési feladatot, amely elérhető a helyreállítás befejezéséhez adatátvitel várakozás nélkül részeként végrehajtott pillanatképek lehetősége. Csökkenti a várakozási idő a visszaállítás elindítása előtt a tárolóba másolja a pillanatképek. Ez a lehetőség kizárjuk a prémium szintű virtuális gépeket, kivéve az első biztonsági mentés biztonsági mentéséről további tárhely követelmény.  

* Biztonsági mentés és visszaállítás alkalommal megtartják a pillanatképek helyi hét napig csökkentése.

* Támogatja a lemez méretének akár 4 TB.

* Lehetővé teszi egy nem felügyelt virtuális gép eredeti tárfiókok visszaállításához használni. Ez a lehetőség létezik, akkor is, ha a virtuális gép lemezzel rendelkezik, amelyet tárfiókok különböző pontjain. Így visszaállítások gyorsabban számos különböző Virtuálisgép-konfigurációk.
    > [!NOTE] 
    > Lehetősége nem ugyanaz, mint az eredeti virtuális felülbírálása. 
    >

## <a name="whats-changing-in-the-new-stack"></a>Mi az új verem változik?
A biztonsági mentési feladat jelenleg két fázisból áll:
1.  Virtuális gép pillanatkép létrehozása van folyamatban. 
2.  A virtuális gép pillanatfelvétele átvitele az Azure Backup-tárolóban. 

A helyreállítási pont tekinthető létre csak azt követően 1 és 2 fázisban történik. Az új verem részeként, amint elkészült a pillanatkép jön létre egy helyreállítási pontot. Is helyreállíthatja a helyreállítási pont az adott visszaállítási adatfolyam használatával. A helyreállítási pont típusa "snapshot" segítségével azonosíthatja a helyreállítási pont, az Azure portálon. Követően a pillanatkép átkerül a tárolóban, a helyreállítási pont típusa "pillanatkép és tárolóban. a" módosításait 

![Virtuális gép biztonsági mentési verem Resource Manager üzembe helyezési modellel – tárolás és a tároló biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-flow.jpg) 

Alapértelmezés szerint pillanatfelvételek megmaradnak hét napja. Ez a funkció lehetővé teszi, hogy a visszaállítás ezeket a pillanatképeket a gyorsabb befejezéséhez. Csökkenti az adatok másolása a tárolóból vissza az ügyfél tárfiókjával szükséges idő. 

## <a name="considerations-before-upgrade"></a>Frissítés előtt kapcsolatos szempontok
* A virtuális gép biztonsági mentési csomagjának frissítése egyike irányt. Így minden biztonsági mentés nyissa meg a folyamatot. Engedélyezve van az előfizetés szintjén, mert ez a folyamat kísérhet minden virtuális gép. Az összes új funkciót elemek ugyanazt a vermet alapulnak. Ez a házirend szinten későbbi hamarosan vezérlése kiadását.

* A pillanatképek helyileg tárolja a helyreállítási pont létrehozása növelése érdekében és visszaállítási felgyorsítása érdekében. Ezért tekintse meg, amelyek megfelelnek a 7 napos időszak alatt a pillanatképek tárolási költségeket.

* Növekményes pillanatképek lapblobokat tárolódnak. Nem felügyelt lemezeket használó összes ügyfél számára van szó, az ügyfél helyi tárfiók tárolja a pillanatképek a hét napja. Az aktuális árképzési modellt megfelelően nincs költség nélkül kezelt lemezeken található ügyfelek számára.

* Ha így tesz, a visszaállítás egy prémium szintű virtuális gép pillanatkép helyreállítási pontból, megjelenik egy ideiglenes tárolási helye, amely a virtuális gép kerül létrehozásra részeként a visszaállítása közben.

* Prémium szintű storage-fiókok a pillanatképek azonnali helyreállítási elvégzett 10 TB-nyi számára lefoglalt terület helyezkednek el.

## <a name="upgrade"></a>Frissítés
### <a name="the-azure-portal"></a>Az Azure-portálon
Ha használja az Azure-portálon, a tároló irányítópulton megjelenik egy értesítés. Az értesítés támogatja a nagyméretű lemezek és a biztonsági mentési és visszaállítási sebesség javítása vonatkozik.

![Virtuális gép biztonsági mentési verem Resource Manager üzembe helyezési modellel – támogatási értesítési biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-banner.png) 

Nyissa meg az új verem történő frissítés képernyő, jelölje be a szalagcím. 

![Biztonsági mentési feladatot a virtuális gép biztonsági mentési csomagjának Resource Manager üzembe helyezési modellben--frissítése](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Futtassa a következő parancsmagokat egy emelt szintű PowerShell terminál:
1.  Jelentkezzen be az Azure-fiókjával: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Válassza ki az előfizetést, az előzetes regisztrálni kívánt:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Az előfizetés regisztrálása a private Preview verziójára:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Győződjön meg arról, hogy a frissítés befejeződött
Egy rendszergazda jogú PowerShell terminálról futtassa az alábbi parancsmagot:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

"Regisztrált" felirat látható, ha az előfizetés virtuális gép biztonsági mentési verem Resource Manager üzembe helyezési modellben legyen frissítve.
