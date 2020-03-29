---
title: Az Azure VM indítása beragadt a Windows Update-nél| Microsoft dokumentumok
description: Megtudhatja, hogyan háríthatja el a problémát, ha egy Azure-beli virtuális gép indítása a Windows-frissítésnél elakad.
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
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919430"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Az Azure virtuális gép indítása beragadt a Windows-frissítésnél

Ez a cikk segít megoldani a problémát, ha a virtuális gép (VM) elakad a Windows Update szakaszában indításkor. 


## <a name="symptom"></a>Hibajelenség

 A Windows virtuális gép nem indul el. Amikor a [Rendszerindítás diagnosztika](../troubleshooting/boot-diagnostics.md) ablakban ellenőrzi a képernyőképeket, láthatja, hogy az indítás beragadt a frissítési folyamatban. Az alábbi példák a kapott üzenetekre:

- A Windows ##% telepítése Ne kapcsolja ki a számítógépet. Ez eltart egy ideig A számítógép többször is újraindul
- Tartsa bekapcsolva a számítógépet, amíg ez nem történik meg. A #... 
- Nem sikerült végrehajtani a frissítéseket A módosítások visszavonása Ne kapcsolja ki a számítógépet
- A Windows-frissítések konfigurálása sikertelen: A módosítások visszaállítása Ne kapcsolja ki a számítógépet
- Hiba < hibakód > ##### (\Regist...) frissítési műveletek alkalmazásával.
- Végzetes hiba < hibakód > ##### ($$...) frissítési műveletek alkalmazásával.


## <a name="solution"></a>Megoldás

A telepített vagy hengerelt biztonsági frissítések számától függően a frissítési folyamat eltarthat egy ideig. Hagyja a virtuális gép ebben az állapotban 8 órán keresztül. Ha a virtuális gép még ebben az időszakban, indítsa újra a virtuális gép az Azure Portalon, és nézd meg, hogy elindulanak a szokásos módon. Ha ez a lépés nem működik, próbálkozzon az alábbi megoldási javaslattal.

### <a name="remove-the-update-that-causes-the-problem"></a>A problémát okozó frissítés eltávolítása

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál. 
2. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md)
3. Miután az operációs rendszer lemeze csatlakoztatva van a helyreállítási virtuális géphez, futtassa a **diskmgmt.msc** futtatását a Lemezkezelés megnyitásához, és győződjön meg arról, hogy a csatlakoztatott lemez **online állapotban**van. Jegyezze fel a csatolt operációsrendszer-lemezhez rendelt meghajtóbetűjelet, amely a \windows mappát tartja. Ha a lemez titkosítva van, a dokumentum következő lépései előtt fejtse vissza a lemezt.

4. Nyisson meg egy rendszergazdai parancssorpéldányt (Futtatás rendszergazdaként). A következő parancs futtatásával leszeretné késni a csatlakoztatott operációsrendszer-lemezen található frissítőcsomagok listáját:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Ha például a csatlakoztatott operációsrendszer-lemez az F meghajtó, futtassa a következő parancsot:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Nyissa meg a C:\temp\Patch_level.txt fájlt, majd olvassa el alulról felfelé. Keresse meg a **Telepítés függőben** vagy **Az Eltávolítás függőben** állapotban lévő frissítést.  A frissítés állapotának mintája a következő:

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
    > A csomag méretétől függően a DISM eszköz feldolgozása eltarthat egy ideig. Normális esetben a folyamat 16 percen belül befejeződik.

7. [Válassza le az operációs rendszer lemezét, és hozza létre újra a virtuális gép](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Ezután ellenőrizze, hogy a probléma megoldódott-e.
