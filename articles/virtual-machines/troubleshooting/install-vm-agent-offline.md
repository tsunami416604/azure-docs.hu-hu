---
title: Azure Virtuálisgép-ügynök telepítése kapcsolat nélküli módban |} A Microsoft Docs
description: Útmutató az Azure Virtuálisgép-ügynök telepítése kapcsolat nélküli módban.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e9fc8351b5e9a4f2274f0906d4071f86dcbcff26
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259682"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Az Azure virtuálisgép-ügynök telepítése kapcsolat nélküli módban 

Az Azure-beli virtuálisgép-ügynök (VM-ügynök) hasznos funkciók, például a helyi rendszergazdai jelszó-változtatási és leküldése parancsfájl biztosít. Ez a cikk bemutatja, hogyan egy offline Windows virtuális gép (VM) a Virtuálisgép-ügynök telepítése. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Mikor érdemes használni a Virtuálisgép-ügynök a kapcsolat nélküli módban

Virtuálisgép-ügynök telepítése kapcsolat nélküli módban a következő esetekben:

- Az üzembe helyezett Azure virtuális gép nem rendelkezik a Virtuálisgép-ügynök, vagy az ügynök nem működik.
- Elfelejtette a rendszergazdai jelszót a virtuális gép vagy a virtuális gép nem férhet hozzá.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>A Virtuálisgép-ügynök telepítése kapcsolat nélküli módban

Az alábbi lépések segítségével Virtuálisgép-ügynök telepítése kapcsolat nélküli módban.

> [!NOTE]
> A Virtuálisgép-ügynök telepítése kapcsolat nélküli módban automatizálható.
> Ehhez használja a [Azure virtuális gép helyreállítási parancsfájlok](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Ha az Azure virtuális gép helyreállítási parancsfájlok használatát választja, használhatja az alábbi eljárást:
> 1. 1. lépés kihagyása az érintett virtuális gép operációsrendszer-lemez egy helyreállítási virtuális Géphez csatolni a parancsfájlok használatával.
> 2. 2 – 10 kockázatcsökkentő alkalmazandó lépésekkel.
> 3. Ugorjon a 11. lépés a parancsfájlok használatával építse újra a virtuális gép.
> 4. Hajtsa végre a 12. lépés.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>1. lépés: Adatlemezként egy másik virtuális Géphez a virtuális gép operációsrendszer-lemez csatolása

1.  Törölje a virtuális Gépet. Ügyeljen arra, hogy válassza ki a **tartsa a lemezek** lehetőséget a virtuális gép törlésekor.

2.  Csatlakoztassa az operációsrendszer-lemezt adatlemezként egy másik virtuális géphez (más néven egy _hibaelhárító_ virtuális gép). További információkért lásd: [adatlemez csatolása az Azure Portalon Windows virtuális gép](../windows/attach-managed-disk-portal.md).

3.  Csatlakozzon a hibaelhárító virtuális Géphez. Nyissa meg **számítógép-kezelés** > **lemez kezelése**. Győződjön meg arról, hogy online állapotban-e az operációsrendszer-lemez és, hogy a meghajtóbetűjelek lemezpartícióinak legyenek-e rendelve.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>2. lépés: Az Azure Virtuálisgép-ügynök telepítése az operációsrendszer-lemez módosítása

1.  Végezzen egy távoli asztali kapcsolatot a hibaelhárító virtuális Géphez.

2.  A csatolt operációsrendszer-lemezen tallózzon a \windows\system32\config mappához. Másolja az összes fájl ebben a mappában biztonsági mentéséhez, abban az esetben egy visszaállítási megadása kötelező.

3.  Indítsa el a **beállításjegyzék-szerkesztővel** (regedit.exe).

4.  Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. Válassza a menü **fájl** > **a struktúra betöltése**:

    ![A struktúra betöltése](./media/install-vm-agent-offline/load-hive.png)

5.  Keresse meg a \windows\system32\config\SYSTEM mappa a csatolt operációsrendszer-lemez. A hive-neveként, írja be a **BROKENSYSTEM**. Az új beállításjegyzék-struktúrát alatt jelenik meg a **HKEY_LOCAL_MACHINE** kulcsot.

6.  Keresse meg a csatolt operációsrendszer-lemez \windows\system32\config\SOFTWARE mappájába. Adja meg a hive-szoftver neve, **BROKENSOFTWARE**.

7. Ha a csatolt operációsrendszer-lemez a Virtuálisgép-ügynök telepítve van, hajtsa végre az aktuális konfiguráció biztonsági másolata. Ha nem rendelkezik Virtuálisgép-ügynök telepítve van, helyezze át a következő lépéssel.
      
    1. Nevezze át a \windowsazure mappa \windowsazure.old.

    2. Az alábbi nyilvántartások exportálása:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  A meglévő fájlokat a hibaelhárító virtuális Géphez vagy a használata a Virtuálisgép-ügynök telepítésének. Hajtsa végre a következő lépéseket:

    1. A hibaelhárító virtuális Géphez, a beállításjegyzék-formátumban (.reg) a következő alkulcsok exportálása: 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![A beállításkulcsok exportálása](./media/install-vm-agent-offline/backup-reg.png)

    2. Szerkessze a beállításjegyzék fájljairól. Minden fájl, módosítsa a bejegyzés értéke **rendszer** való **BROKENSYSTEM** (ahogyan az alábbi képeken látható), és mentse a fájlt. Ne felejtse el a **ImagePath** az aktuális virtuális gép-ügynök. Szükségünk lesz a megfelelő mappába másolja a csatlakoztatott operációsrendszer-lemez. 

        ![A beállításjegyzékbeli alkulcs értékeinek módosítása](./media/install-vm-agent-offline/change-reg.png)

    3. A beállításjegyzék-fájlok importálása a tárházba ehhez kattintson duplán az egyes beállításjegyzék-fájlt.

    4. Győződjön meg róla, hogy a következő három további sikeresen importálta a **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Az aktuális virtuális gép-ügynök telepítési mappáját másolja a csatlakoztatott operációsrendszer-lemez: 

        1.  Az operációsrendszer-lemez csatolt hozzon létre egy mappát a gyökér elérési útját a WindowsAzure neve.

        2.  Nyissa meg a hibaelhárító virtuális Géphez a C:\WindowsAzure, keresse meg a bármely C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX nevű mappa. A Vendégügynök mappa, amely rendelkezik a legújabb verziószámot C:\WindowsAzure a csatlakoztatott operációsrendszer-lemez WindowsAzure mappájába másolja. Ha nem biztos abban, hogy melyik mappába kell másolni, másolja az összes Vendégügynök mappa. Az alábbi képen egy példa a Vendégügynök mappa a csatolt operációsrendszer-lemez másolt.

             ![Vendégügynök mappába másolja.](./media/install-vm-agent-offline/copy-files.png)

9.  Válassza ki **BROKENSYSTEM**. Válassza a menü **fájl** > **struktúra**.

10.  Válassza ki **BROKENSOFTWARE**. Válassza a menü **fájl** > **struktúra**.

11.  Az operációsrendszer-lemez leválasztása, és hozza létre a virtuális Gépet az operációsrendszer-lemez használatával.

12.  Hozzáférés a virtuális Gépet. Figyelje meg, hogy a RdAgent fut, és a naplók generált.

Ha a virtuális gép a Resource Manager-alapú üzemi modell használatával hozta létre, akkor végzett.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>A klasszikus virtuális gépeket a ProvisionGuestAgent tulajdonság használata

A virtuális Gépet a klasszikus modellt használó hozta létre, ha használata az Azure PowerShell-modul frissítéséhez a **ProvisionGuestAgent** tulajdonság. A tulajdonság tájékoztatja, hogy a virtuális gép rendelkezik-e a Virtuálisgép-ügynök Azure.

Beállítása a **ProvisionGuestAgent** tulajdonság, az Azure PowerShell a következő parancsokat:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Ezután futtassa a `Get-AzureVM` parancsot. Figyelje meg, hogy a **GuestAgentStatus** tulajdonság már van feltöltve adatokkal:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>További lépések

- [Az Azure virtuálisgép-ügynök – áttekintés](../extensions/agent-windows.md)
- [Virtuálisgép-bővítmények és szolgáltatások Windows](../extensions/features-windows.md)
