---
title: Az Azure VM-ügynök telepítése offline módban | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure VM Agent offline módban.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920858"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Az Azure virtuálisgép-ügynök telepítése offline módban 

Az Azure virtuálisgép-ügynök (VM-ügynök) hasznos funkciókat biztosít, például a helyi rendszergazdai jelszó-visszaállítás és a parancsfájl lenyomása. Ez a cikk bemutatja, hogyan telepítheti a virtuálisgép-ügynök egy offline Windows virtuális gép (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Mikor kell használni a virtuálisgép-ügynököt kapcsolat nélküli módban?

Telepítse a virtuálisgép-ügynököt kapcsolat nélküli módban a következő esetekben:

- Az üzembe helyezett Azure virtuális gép nem rendelkezik a virtuális gép ügynök telepítve, vagy az ügynök nem működik.
- Elfelejtette a virtuális gép rendszergazdai jelszavát, vagy nem tud hozzáférni a virtuális géphez.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>A virtuális gépügynök telepítése offline módban

A virtuálisgép-ügynök offline módban történő telepítéséhez kövesse az alábbi lépéseket.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>1. lépés: A virtuális gép operációs rendszerlemezének csatolása egy másik virtuális géphez adatlemezként

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről, hozzon létre egy lemezt a pillanatképből, majd csatolja a lemezt egy hibaelhárítási virtuális géphez. További információt a [Windows-virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure Portalon keresztül idomuló virtuális géphez való csatolásával](troubleshoot-recovery-disks-portal-windows.md)című témakörben talál. A klasszikus virtuális gép, törölje a virtuális gép, és tartsa meg az operációs rendszer lemezét, majd csatolja az operációs rendszer lemeze a hibaelhárítási virtuális gép.

2.  Csatlakozzon a hibaelhárító virtuális géphez. Nyissa **meg a Számítógép-kezelés** > **Lemezkezelés segédprogramot.** Ellenőrizze, hogy az operációs rendszer lemeze online állapotban van-e, és hogy a meghajtóbetűjelek a lemezpartíciókhoz vannak-e rendelve.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>2. lépés: Az operációs rendszer lemezének módosítása az Azure VM-ügynök telepítéséhez

1.  Távoli asztali kapcsolat létesítése a hibaelhárító virtuális géphez.

2.  A hibaelhárító virtuális gépében keresse meg a csatlakoztatott operációsrendszer-lemezt, nyissa meg a \windows\system32\config mappát. Másolja a mappában lévő összes fájlt biztonsági másolatként, ha visszaállításra van szükség.

3.  Indítsa el a **Rendszerleíróadatbázis-szerkesztőt** (regedit.exe).

4.  Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. A menüben válassza a > **Fájlbetöltő hive parancsot:** **File**

    ![A kaptár betöltése](./media/install-vm-agent-offline/load-hive.png)

5.  Tallózással keresse meg a csatolt operációsrendszer-lemez \windows\system32\config\SYSTEM mappáját. A struktúra nevéhez írja be a **BROKENSYSTEM**. Az új beállításstruktúra a **HKEY_LOCAL_MACHINE** kulcs alatt jelenik meg.

6.  Tallózással keresse meg a csatolt operációsrendszer-lemez \windows\system32\config\SOFTWARE mappáját. A kaptárszoftver nevéhez írja be a **BROKENSOFTWARE**.

7. Ha a csatolt operációsrendszer-lemezen telepítve van a virtuálisgép-ügynök, készítsen biztonsági másolatot az aktuális konfigurációról. Ha nincs telepítve a virtuálisgép-ügynök, lépjen a következő lépésre.
      
    1. Nevezze át a \windowsazure mappát \windowsazure.old névre.

    2. Exportálja a következő nyilvántartásokat:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Használja a hibaelhárító virtuális gép meglévő fájljait a virtuális gép ügynök telepítésének tárházaként. Hajtsa végre a következő lépéseket:

    1. A hibaelhárító virtuális gépéből exportálja a következő alkulcsokat rendszerleíró formátumban (.reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![A rendszerleíró alkulcsok exportálása](./media/install-vm-agent-offline/backup-reg.png)

    2. A beállításjegyzék-fájlok szerkesztése. Minden fájlban módosítsa a **SYSTEM** bejegyzés értékét **BROKENSYSTEM** értékre (ahogy az a következő képeken látható), és mentse a fájlt. Ne feledje, az Aktuális Virtuálisgép-ügynök **ImagePath.** Meg kell másolni a megfelelő mappát a csatolt operációsrendszer-lemezre. 

        ![A rendszerleíró alkulcs értékeinek módosítása](./media/install-vm-agent-offline/change-reg.png)

    3. Importálja a rendszerleíró fájlokat a tárházba úgy, hogy duplán kattint az egyes beállításfájlokra.

    4. Ellenőrizze, hogy a következő három alkulcs sikeresen importálva **van-e** a BROKENSYSTEM struktúrabe:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemettrySzolgáltatás
        - RdÜgynök

    5. Másolja az aktuális virtuálisgép-ügynök telepítési mappáját a csatlakoztatott operációsrendszer-lemezre: 

        1.  A csatolt operációsrendszer-lemezen hozzon létre egy WindowsAzure nevű mappát a gyökérelérési úton.

        2.  Nyissa meg a Hibaelhárító Virtuális gép C:\WindowsAzure webhelyét, és keressen meg minden C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX nevű mappát. Másolja a legutolsó verziószámot tartalmazó GuestAgent mappát a C:\WindowsAzure szolgáltatásból a csatlakoztatott operációsrendszer-lemez WindowsAzure mappájába. Ha nem biztos abban, hogy melyik mappát kell másolni, másolja az összes GuestAgent mappát. Az alábbi képen egy példa látható a csatolt operációsrendszer-lemezre másolt GuestAgent mappára.

             ![GuestAgent mappa másolása](./media/install-vm-agent-offline/copy-files.png)

9.  Válassza **a BROKENSYSTEM**lehetőséget. A menüben válassza a **Fájl** > **eltávolítása a hive parancsot.**

10.  Válassza **a BROKENSOFTWARE**lehetőséget. A menüben válassza a **Fájl** > **eltávolítása a hive parancsot.**

11.  Válassza le az operációs rendszer lemezét, majd [módosítsa az érintett virtuális gép operációsrendszer-lemezét.](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm) A klasszikus virtuális gép, hozzon létre egy új virtuális gép a javított operációsrendszer-lemez használatával.

12.  Hozzáférés a virtuális géphez. Figyelje meg, hogy a RdAgent fut, és a naplók létrehozása folyamatban van.

Ha a virtuális gép az Erőforrás-kezelő telepítési modell használatával hozta létre, akkor kész.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>A ProvisionGuestAgent tulajdonság használata klasszikus virtuális gépekhez

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Ha a virtuális gép a klasszikus modell használatával hozta létre, az Azure PowerShell-modul segítségével frissítse a **ProvisionGuestAgent** tulajdonság. A tulajdonság tájékoztatja az Azure-t, hogy a virtuális gép telepítette a virtuálisgép-ügynököt.

A **ProvisionGuestAgent** tulajdonság beállításához futtassa a következő parancsokat az Azure PowerShellben:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Ezután `Get-AzureVM` futtassa a parancsot. Figyelje meg, hogy a **GuestAgentStatus** tulajdonság most már adatokkal van feltöltve:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>További lépések

- [Az Azure virtuálisgép-ügynök – áttekintés](../extensions/agent-windows.md)
- [Virtuálisgép-bővítmények és -szolgáltatások a Windows rendszerhez](../extensions/features-windows.md)
