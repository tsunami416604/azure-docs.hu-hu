---
title: Az Azure virtuális gép indítási megakadt Windows Update |} A Microsoft Docs
description: Ismerje meg a hiba elhárításához, ha egy Azure virtuális gép indítási megakadt Windows update.
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 7fbf157152bd5c2c35c496b1fd8e707884d0f3da
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731463"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Az Azure virtuális gép indítási megakadt Windows update

Ez a cikk segít megoldani a problémát, ha a virtuális gép (VM) a Windows Update szakaszban elakadt indítása során. 

> [!NOTE] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell használatával. Azt javasoljuk, hogy az új központi telepítéseknél a klasszikus üzemi modell helyett ezt a modellt használja.

## <a name="symptom"></a>Jelenség

 Windows virtuális gép nem indul el. Amikor ellenőrizheti a képernyőképek a a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) ablakban megtekintheti, hogy az indítási elakadt a frissítési folyamat során. A következő példák üzeneteket kaphat:

- Telepítése Windows ##% ne kapcsolja ki a Számítógéphez. Ez eltarthat egy ideig a számítógép többször újraindul.
- Amíg ez történik, hogy az a számítógépen. A # # telepítése frissítése... 
- Módosítások visszavonása nem kapcsolja ki a számítógépet a frissítések nem lehetett végrehajtani
- Hiba konfigurálása Windows-frissítések Reverting módosítások ne kapcsolja ki a számítógépet
- Hiba történt < hibakód > frissítési műveletek alkalmazásakor ### a ### (\Regist...)
- Végzetes hiba < hibakód > frissítési műveletek alkalmazása ##, ## ($$...)


## <a name="solution"></a>Megoldás

Frissítések, amelyekre a számától függően telepítve, vagy biztonsági állítva, a frissítési folyamat eltarthat egy ideig. A virtuális gép ebben az állapotban hagyja a 8 óra. Ha a virtuális gép továbbra is ebben az állapotban az időszak elteltével, indítsa újra a virtuális Gépet az Azure Portalról, és tekintse meg, ha, megkezdheti a szokásos módon. Ha ez a lépés nem működik, próbálja ki a következő megoldást.

### <a name="remove-the-update-that-causes-the-problem"></a>Távolítsa el a hibát okozó frissítés

1. Pillanatkép készítése az operációsrendszer-lemez az érintett virtuális gépek biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md). 
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).
3. Miután az operációsrendszer-lemez a helyreállítási virtuális Géphez van csatolva, futtassa **diskmgmt.msc** nyissa meg a Lemezkezelés eszközben, és győződjön meg arról, a csatlakoztatott lemez **ONLINE**. Jegyezze fel a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez a \windows mappát tároló van rendelve. Ha a lemez titkosítva vannak, visszafejteni a lemez ebben a dokumentumban a következő lépések végrehajtása előtt.

4. Nyissa meg a rendszergazda jogú parancssort (Futtatás rendszergazdaként). A következő parancsot a csatlakoztatott operációsrendszer-lemez a frissítési csomagok listájának beolvasása:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Például ha a csatlakoztatott operációsrendszer-lemez meghajtó F, futtassa a következő parancsot:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Nyissa meg a C:\temp\Patch_level.txt fájlt, és ezután olvassa el, a lista aljáról. Keresse meg a frissítést, amely a **telepítés függőben** vagy **függőben lévő eltávolítása** állapota.  Az alábbiakban látható a frissítés állapotának mintáira érvényesek:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Távolítsa el a frissítést, amelyiket okozta a problémát:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Példa: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > A csomag méretétől függően a a DISM eszköz eltarthat egy ideig, az eltávolítási feldolgozásához. Általában a folyamat 16 percen belül befejeződik.

7. [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Ezután ellenőrizze, hogy a probléma megoldódott-e.