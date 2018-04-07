---
title: Az Azure Virtuálisgép-ügynök telepítése a kapcsolat nélküli módban |} Microsoft Docs
description: Útmutató az Azure Virtuálisgép-ügynök telepítése a kapcsolat nélküli módban.
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
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 8b9d1ea3e4b5bf959484a737453d7f6f1f63d1e6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Az Azure virtuális gép ügynökének telepítése kapcsolat nélküli módban 

Az Azure virtuálisgép-ügynök (Virtuálisgép-ügynök) biztosít hasznos szolgáltatások, például helyi rendszergazdai jelszó alaphelyzetbe állítása és parancsfájl kérdez le. Ez a cikk bemutatja, hogyan egy offline Windows virtuális gép (VM) a Virtuálisgép-ügynök telepítéséhez. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>A Virtuálisgép-ügynök használatával kapcsolat nélküli módban

A Virtuálisgép-ügynök telepítése kapcsolat nélküli módban, a következő esetekben:

- A telepített Azure virtuális gép nem rendelkezik a Virtuálisgép-ügynök van telepítve, vagy az ügynök nem működik.
- A virtuális gép elfelejtette a rendszergazdai jelszót, vagy nem fér hozzá a virtuális Gépet.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>A Virtuálisgép-ügynök telepítése a kapcsolat nélküli módban

Az alábbi lépések segítségével a Virtuálisgép-ügynök telepítése a kapcsolat nélküli módban.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>1. lépés: Az operációsrendszer-lemezképet a virtuális gép csatolni egy másik virtuális gép adatok lemezként

1.  Törölje a virtuális Gépet. Ügyeljen arra, hogy válassza ki a **tartsa a lemezek** lehetőséget a virtuális gép törlésekor.

2.  Az operációs rendszer lemezének adatok lemezként csatlakoztatni egy másik virtuális gép (néven egy _hibaelhárító_ VM). További információkért lásd: [adatlemezt csatolni egy Windows virtuális Gépet az Azure portálon](attach-managed-disk-portal.md).

3.  Csatlakoztassa a virtuális gép hibaelhárító. Nyissa meg **számítógép-kezelés** > **felügyeleti lemez**. Győződjön meg arról, hogy online állapotban-e az operációsrendszer-lemezképet, és, hogy a meghajtóbetűjelek lemezpartícióival legyenek-e rendelve.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>2. lépés: Módosítsa az operációsrendszer-lemezképet, az Azure Virtuálisgép-ügynök telepítése

1.  A virtuális gép hibaelhárító ne a távoli asztali kapcsolat.

2.  Keresse meg az operációsrendszer-lemezképet, a mellékelt \windows\system32\config mappába. Másolja az összes fájl ebben a mappában biztonsági mentéséhez, abban az esetben, ha a visszaállítás szükség.

3.  Indítsa el a **beállításjegyzék-szerkesztővel** (regedit.exe).

4.  Válassza ki a **HKEY_LOCAL_MACHINE** kulcs. Válassza a menü **fájl** > **struktúra betöltése**:

    ![A hive betöltése](./media/install-vm-agent-offline/load-hive.png)

5.  Tallózással keresse meg az operációsrendszer-lemezképet, a mellékelt \windows\system32\config\SYSTEM mappájába. A hive-neveként, írja be a **BROKENSYSTEM**. Az új beállításjegyzék-struktúrát jelenik meg a **HKEY_LOCAL_MACHINE** kulcs.

6.  Tallózással keresse meg az operációsrendszer-lemezképet, a mellékelt \windows\system32\config\SOFTWARE mappájába. A hive szoftver nevét, adja meg a **BROKENSOFTWARE**.

7.  A Virtuálisgép-ügynök nem működik, készítsen biztonsági másolatot az aktuális konfigurációt.

    >[!NOTE]
    >Ha a virtuális gép nem rendelkezik a telepített ügynök, ugorjon a 8. 
      
    1. Nevezze át a \windowsazure mappát \windowsazure.old.

    2. A következő nyilvántartó exportálása:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  A tárház a Virtuálisgép-ügynök telepítéséhez a meglévő fájlokat a virtuális gép hibaelhárító a használják. Hajtsa végre a következő lépéseket:

    1. A virtuális gép hibaelhárító exportálása a következő alkulcsok beállításjegyzék formátumban (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

        ![A beállításkulcsok exportálása](./media/install-vm-agent-offline/backup-reg.png)

    2. Szerkessze a beállításjegyzék fájljairól. Minden fájl, módosítsa a bejegyzés értéke **rendszer** való **BROKENSYSTEM** (ahogy az alábbi képek), és mentse a fájlt.

        ![A beállításjegyzékbeli alkulcs értékek módosítása](./media/install-vm-agent-offline/change-reg.png)

    3. A beállításjegyzék-fájlok importálása a tárház egyes beállításjegyzék-fájlra duplán kattintva.

    4. Győződjön meg róla, hogy a következő három további sikeresen importálta a **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  A Virtuálisgép-ügynök mappájába másolja a C:\windowsazure\packages a &lt;operációsrendszer-lemez, a mellékelt&gt;: \windowsazure\packages.

    ![Másolja a Virtuálisgép-ügynök fájlokat az operációsrendszer-lemez](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Ne másolja a **naplók** mappát. A szolgáltatás elindulása után az új naplók jönnek létre.

10.  Válassza ki **BROKENSYSTEM**. Válassza a menü **fájl** > **struktúra**.

11.  Válassza ki **BROKENSOFTWARE**. Válassza a menü **fájl** > **struktúra**.

12.  Válassza le az operációsrendszer-lemezképet, és az operációs rendszer lemezének használatával hozza létre a virtuális gép.

13.  Hozzáférés a virtuális Gépet. Figyelje meg, hogy fut-e a RdAgent, és a naplók létrehozása folyamatban.

Ha a virtuális gép által a klasszikus üzembe helyezési modellel létrehozott, az elkészült.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Használja a ProvisionGuestAgent tulajdonságot az Azure Resource Manager eszközzel létrehozott virtuális gépek

Ha a virtuális Gépet a Resource Manager üzembe helyezési modellel létrehozott, használja az Azure PowerShell modul frissítése a **ProvisionGuestAgent** tulajdonság. A tulajdonság az Azure tájékoztatja, hogy a virtuális gép rendelkezik-e a Virtuálisgép-ügynök telepítve.

Beállítása a **ProvisionGuestAgent** tulajdonság, az Azure PowerShell futtassa a következő parancsokat:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Ezután futtassa a `Get-AzureVM` parancsot. Figyelje meg, hogy a **GuestAgentStatus** tulajdonság most feltöltve adatokkal:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>További lépések

- [Az Azure virtuális gép ügynökének áttekintése](agent-user-guide.md)
- [Virtuálisgép-bővítmények és a Windows szolgáltatások](extensions-features.md)
