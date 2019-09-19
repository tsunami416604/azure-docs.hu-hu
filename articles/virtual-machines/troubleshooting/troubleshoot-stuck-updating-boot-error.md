---
title: Az Azure-beli virtuális gép indítása beragadt Windows Update | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a problémát, ha egy Azure-beli virtuális gép indításakor beragad a Windows Update szolgáltatásba.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 226151d81319dc4e6f132e76ce2d310f88a484e8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087021"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Az Azure-beli virtuális gép indítása a Windows Update szolgáltatásban megakadt

Ez a cikk segítséget nyújt a probléma megoldásában, ha a virtuális gép (VM) a Windows Update fázisban az indítás során megakad. 

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti. Javasoljuk, hogy ezt a modellt a klasszikus üzemi modell használata helyett új központi telepítések esetén használja.

## <a name="symptom"></a>Jelenség

 Egy Windows rendszerű virtuális gép nem indul el. Amikor bejelöli a képernyőképeket a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) ablakban, láthatja, hogy az indítás megakadt a frissítési folyamat során. Az alábbi példákban megjelenhetnek az üzenetek:

- A Windows # #% telepítése nem kapcsolja ki a számítógépet. Ez eltarthat egy ideig, amíg a számítógép többször is újraindul
- Tartsa SZÁMÍTÓGÉPét, amíg ez megtörténik. A # (#) frissítésének telepítése... 
- Nem sikerült befejezni a frissítések visszavonásának módosításait, ne kapcsolja ki a számítógépet
- Sikertelen volt a Windows-frissítések visszavonási beállításainak konfigurálása a számítógép kikapcsolásakor
- Hiba < hibakód > a frissítési műveletek alkalmazása # # # # # # # # # # # # # # # # # # # # # #
- Végzetes hiba történt < hibakód > a frissítési műveletek alkalmazása # # # # # # # # # # # # ($ $...)


## <a name="solution"></a>Megoldás

Attól függően, hogy hány frissítést telepít vagy állít vissza, a frissítési folyamat eltarthat egy ideig. A virtuális gépet 8 órán keresztül hagyja ebben az állapotban. Ha a virtuális gép az adott időszak után még mindig ebben az állapotban van, indítsa újra a virtuális gépet a Azure Portalból, és ellenőrizze, hogy elindulhat-e a szokásos módon. Ha ez a lépés nem működik, próbálja ki a következő megoldást.

### <a name="remove-the-update-that-causes-the-problem"></a>A problémát okozó frissítés eltávolítása

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md). 
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).
3. Ha az operációsrendszer-lemez a helyreállítási virtuális gépen van csatlakoztatva, futtassa a **diskmgmt. msc fájlt** a Lemezkezelés eszköz megnyitásához, és győződjön meg arról, hogy a csatlakoztatott lemez **online állapotban**van. Jegyezze fel a \Windows mappát tároló csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet. Ha a lemez titkosítva van, a rendszer visszafejti a lemezt a dokumentum következő lépéseinek folytatása előtt.

4. Nyisson meg egy rendszergazda jogú parancssor-példányt (Futtatás rendszergazdaként). Futtassa a következő parancsot a csatolt operációsrendszer-lemezen található frissítési csomagok listájának lekéréséhez:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Ha például a csatlakoztatott operációsrendszer-lemez a F meghajtó, futtassa a következő parancsot:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Nyissa meg a C:\temp\Patch_level.txt fájlt, majd olvassa el az alulról. Keresse meg a **telepítés függőben** vagy az **Eltávolítás függőben** állapotú frissítést.  Az alábbi példa a frissítési állapotot mutat be:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Távolítsa el a problémát okozó frissítést:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Példa: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > A csomag méretétől függően a DISM eszköz eltarthat egy ideig, amíg feldolgozza az eltávolítást. Általában a folyamat 16 percen belül befejeződik.

7. [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Ezután győződjön meg arról, hogy a probléma megoldódott-e.
