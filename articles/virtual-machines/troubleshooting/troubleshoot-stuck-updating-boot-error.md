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
ms.openlocfilehash: 2d42d2014432b72f35e9b0d9543fe499a6ab721b
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355488"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Az Azure virtuális gép indítási megakadt Windows update

Ez a cikk segít megoldani a problémát, ha a virtuális gép (VM) a Windows Update szakaszban elakadt indítása során. 

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell használatával. Azt javasoljuk, hogy az új központi telepítéseknél a klasszikus üzemi modell helyett ezt a modellt használja.

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
3. Miután az operációsrendszer-lemez a helyreállítási virtuális Géphez van csatolva, nyissa meg a **logikailemez-kezelő** , és győződjön meg arról, hogy **ONLINE**. Jegyezze fel a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez a \windows mappát tároló van rendelve. Ha a lemez titkosítva vannak, visszafejteni a lemez ebben a dokumentumban a következő lépések végrehajtása előtt.

3. A csatlakoztatott operációsrendszer-lemez a frissítési csomagok listájának lekérése:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Például ha a csatlakoztatott operációsrendszer-lemez meghajtó F, futtassa a következő parancsot:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
4. Nyissa meg a C:\temp\Patch_level.txt fájlt, és ezután olvassa el, a lista aljáról. Keresse meg a frissítést, amely a **telepítés függőben** vagy **függőben lévő eltávolítása** állapota.  Az alábbiakban látható a frissítés állapotának mintáira érvényesek:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
5. Távolítsa el a frissítést, amelyiket okozta a problémát:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Példa: 

    ```
    dism /Image:F:\ /Remove-Package /Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > A csomag méretétől függően a a DISM eszköz eltarthat egy ideig, az eltávolítási feldolgozásához. Általában a folyamat 16 percen belül befejeződik.

6. Az operációsrendszer-lemez leválasztása, majd [építse újra a virtuális Gépet az operációsrendszer-lemez használatával](troubleshoot-recovery-disks-portal-windows.md). 