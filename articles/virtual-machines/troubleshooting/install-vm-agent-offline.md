---
title: Az Azure-beli virtuálisgép-ügynök telepítése kapcsolat nélküli módban | Microsoft Docs
description: Ismerje meg, hogyan telepítheti az Azure-os virtuálisgép-ügynököt kapcsolat nélküli módban.
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
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85983223"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Az Azure-beli virtuális gép ügynökének telepítése kapcsolat nélküli módban 

Az Azure virtuálisgép-ügynök (VM-ügynök) hasznos funkciókat kínál, például a helyi rendszergazda jelszó-visszaállítást és a parancsfájlok futtatását. Ez a cikk bemutatja, hogyan telepítheti a virtuálisgép-ügynököt egy offline Windows rendszerű virtuális géphez (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Mikor kell használni a virtuálisgép-ügynököt kapcsolat nélküli módban

Telepítse a virtuálisgép-ügynököt offline módban a következő esetekben:

- Az üzembe helyezett Azure-beli virtuális gép nem rendelkezik telepített virtuálisgép-ügynökkel, vagy az ügynök nem működik.
- Elfelejtette a virtuális gép rendszergazdai jelszavát, vagy nem fér hozzá a virtuális géphez.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>A virtuálisgép-ügynök telepítése offline módban

A virtuálisgép-ügynök offline módban történő telepítéséhez kövesse az alábbi lépéseket.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>1. lépés: a virtuális gép operációsrendszer-lemezének csatlakoztatása egy másik virtuális géphez adatlemezként

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről, hozzon létre egy lemezt a pillanatképből, majd csatolja a lemezt egy hibakeresési virtuális géphez. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával a Azure Portal használatával](troubleshoot-recovery-disks-portal-windows.md). A klasszikus virtuális gép esetében törölje a virtuális gépet, és tartsa meg az operációsrendszer-lemezt, majd csatolja az operációsrendszer-lemezt a virtuális gép hibakereséséhez.

2.  Kapcsolódjon a hibakereső virtuális géphez. Nyissa meg a **Számítógép-kezelés**  >  **Lemezkezelés**programot. Ellenőrizze, hogy az operációsrendszer-lemez online állapotban van-e, és hogy a meghajtóbetűjelek hozzá vannak-e rendelve a lemezpartíciókhöz.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>2. lépés: az operációsrendszer-lemez módosítása az Azure VM-ügynök telepítéséhez

1.  Létesítsen távoli asztali kapcsolatokat a hibakereső virtuális géphez.

2.  A hibakereső virtuális gépen keresse meg a csatlakoztatott operációsrendszer-lemezt, és nyissa meg a \Windows\System32\Config mappát. Másolja a mappában található összes fájlt biztonsági másolatként, ha visszaállításra van szükség.

3.  Indítsa el a **Beállításszerkesztőt** (regedit.exe).

4.  Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. A menüben válassza a **fájl**  >  **Load struktúra**elemet:

    ![A struktúra betöltése](./media/install-vm-agent-offline/load-hive.png)

5.  Keresse meg a \windows\system32\config\SYSTEM mappát a csatlakoztatott operációsrendszer-lemezen. A struktúra neveként írja be a következőt: **BROKENSYSTEM**. Az új beállításjegyzék-struktúra a **HKEY_LOCAL_MACHINE** kulcs alatt jelenik meg.

6.  Keresse meg a \windows\system32\config\SOFTWARE mappát a csatlakoztatott operációsrendszer-lemezen. A kaptár szoftver neveként írja be a következőt: **BROKENSOFTWARE**.

7. Ha a csatlakoztatott operációsrendszer-lemez rendelkezik a virtuálisgép-ügynökkel, hajtsa végre az aktuális konfiguráció biztonsági mentését. Ha nincs telepítve a virtuálisgép-ügynök, lépjen a következő lépésre.
      
    1. A \windowsazure mappa átnevezése a \windowsazure.old.

    2. Exportálja a következő jegyzékeket:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Használja a hibakereső virtuális gép meglévő fájljait adattárként a virtuálisgép-ügynök telepítéséhez. Végezze el a következő lépéseket:

    1. A hibakereső virtuális gépről exportálja a következő alkulcsokat a beállításjegyzék-formátumban (. reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Beállításjegyzékbeli alkulcsok exportálása](./media/install-vm-agent-offline/backup-reg.png)

    2. Szerkessze a beállításjegyzék fájljait. Minden fájlban módosítsa a **BROKENSYSTEM** (a következő **SYSTEM** lemezképekben látható módon), és mentse a fájlt. Jegyezze fel az aktuális virtuálisgép-ügynök **ImagePath** . A kapcsolódó mappát át kell másolnia a csatlakoztatott operációsrendszer-lemezre. 

        ![Beállításkulcs értékének módosítása](./media/install-vm-agent-offline/change-reg.png)

    3. Importálja a beállításjegyzék fájljait a tárházba úgy, hogy duplán kattint az egyes beállításjegyzék-fájlokra.

    4. Győződjön meg arról, hogy a következő két alkulcs sikeresen importálva lett a **BROKENSYSTEM** -struktúrába:
        - WindowsAzureGuestAgent
        - RdAgent

    5. Másolja a jelenlegi virtuálisgép-ügynök telepítési mappáját a mellékelt operációsrendszer-lemezre: 

        1.  A csatlakoztatott operációsrendszer-lemezen hozzon létre egy WindowsAzure nevű mappát a gyökér elérési útjában.

        2.  Nyissa meg a C:\WindowsAzure a hibakereső virtuális gépen, keresse meg a C:\WindowsAzure\ GuestAgent_X. X. XXXX. XXX nevű mappát. Másolja a Vendégügynök mappát, amely a legújabb verziószámmal rendelkezik a C:\WindowsAzure-ből a csatlakoztatott operációsrendszer-lemez WindowsAzure mappájába. Ha nem biztos abban, hogy melyik mappát kell másolni, másolja az összes Vendégügynök mappát. Az alábbi képen egy példa látható a csatlakoztatott operációsrendszer-lemezre másolt Vendégügynök mappára.

             ![Vendégügynök mappa másolása](./media/install-vm-agent-offline/copy-files.png)

9.  Válassza a **BROKENSYSTEM**lehetőséget. A menüből válassza ki a **fájl**  >  **kitöltése struktúrát**.

10.  Válassza a **BROKENSOFTWARE**lehetőséget. A menüből válassza ki a **fájl**  >  **kitöltése struktúrát**.

11.  Válassza le az operációsrendszer-lemezt, majd [módosítsa az érintett virtuális gép operációsrendszer-lemezét](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). A klasszikus virtuális gép esetében hozzon létre egy új virtuális gépet a javított operációsrendszer-lemezzel.

12.  A virtuális gép elérése. Figyelje meg, hogy a RdAgent fut, és a rendszer létrehozza a naplókat.

Ha a virtuális gépet a Resource Manager-alapú üzemi modell használatával hozta létre, akkor elkészült.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>A klasszikus virtuális gépek ProvisionGuestAgent tulajdonságának használata

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Ha a klasszikus modellt használva hozta létre a virtuális gépet, használja a Azure PowerShell modult a **ProvisionGuestAgent** tulajdonság frissítéséhez. A tulajdonság arról tájékoztatja az Azure-t, hogy a virtuális gépen telepítve van a virtuálisgép-ügynök.

A **ProvisionGuestAgent** tulajdonság beállításához futtassa a következő parancsokat a Azure PowerShellban:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Ezután futtassa a `Get-AzureVM` parancsot. Figyelje meg, hogy a **GuestAgentStatus** tulajdonság már fel van töltve adatokkal:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>További lépések

- [Az Azure Virtual Machine Agent áttekintése](../extensions/agent-windows.md)
- [Virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](../extensions/features-windows.md)
