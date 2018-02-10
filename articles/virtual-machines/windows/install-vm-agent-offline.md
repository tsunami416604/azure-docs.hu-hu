---
title: "Az Azure Virtuálisgép-ügynök telepítése a kapcsolat nélküli módban |} Microsoft Docs"
description: "Ismerje meg, telepítse az Azure Virtuálisgép-ügynök Offline módban."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: b38bfaffad3e214f3b854715e4d8030cde432bae
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="install-the-vm-agent-in-offline-mode-in-an-azure-windows-vm"></a>A Virtuálisgép-ügynök telepítése egy Azure Windows virtuális gép kapcsolat nélküli módban 

Virtuálisgép-ügynök például a helyi rendszergazdai jelszó alaphelyzetbe állítása és parancsfájl leküldéses hasznos funkciókat biztosítja: Ez a cikk bemutatja, hogyan telepítse az ügynököt a virtuális gépek nem kapcsolódik a hálózathoz. 

## <a name="when-to-use-offline-mode"></a>Mikor érdemes használni a kapcsolat nélküli módban

A Virtuálisgép-ügynök telepítése az alábbi példa offline módban kellene:

- Telepít egy Azure virtuális Gépet, amely nincsen telepítve az ügynök vagy a Virtuálisgép-ügynök nem működik.
- Nem emlékszik a rendszergazda jelszavát, vagy nem fér hozzá a virtuális Gépet.

Ebben a forgatókönyvben kellene a Virtuálisgép-ügynök telepítése a kapcsolat nélküli módban. 



## <a name="detailed-steps"></a>Részletes lépések

**1. lépés: Az operációsrendszer-lemezképet a virtuális gép csatolni egy másik virtuális gép adatok lemezként**

1.  Törölje a virtuális Gépet. Győződjön meg arról, hogy kiválassza a **tartsa a lemezek** ezzel lehetőséget.

2.  Az operációs rendszer lemezének adatok lemezként csatlakoztatni egy másik virtuális gép (a virtuális gép hibaelhárító). További információért olvassa el [az adatlemez egy windowsos virtuális géphez az Azure Portalon történő csatlakoztatását](attach-managed-disk-portal.md) ismertető cikket.

3.  Kapcsolódjon a hibaelhárítást végző virtuális gépre. Nyissa meg **számítógép-kezelés** > **felügyeleti lemez**. Győződjön meg arról, hogy online állapotban-e az operációsrendszer-lemezképet, és, hogy a partíció rendelkezik-e a hozzárendelt meghajtóbetűjelek.

**2. lépés: Módosítsa a Virtuálisgép-ügynök telepítése az operációsrendszer-lemez**

1.  Ellenőrizze a távoli asztali kapcsolatot a virtuális gép kapcsolatos problémák elhárítása.

2.  Keresse meg a csatolt operációsrendszer-lemez, **\windows\system32\config**. Másolja az összes biztonsági abban az esetben, ha a visszaállítás szükség.

3.  Indítsa el a Beállításszerkesztőt (regedit.exe).

4.  Kattintson a **HKEY_LOCAL_MACHINE** billentyűt, majd válassza ki **fájl** > **struktúra betöltése** a menüben.

    ![Struktúra betöltése](./media/install-vm-agent-offline/load-hive.png)

5.  Navigáljon a **\windows\system32\config\SYSTEM** az operációs rendszer, amikor a csatlakoztatott lemez, és írja be a BROKENSYSTEM a hive nevével. Ezt követően, a beállításjegyzék-struktúra alatt megjelenik-e **HKEY_LOCAL_MACHINE**.

6.  Navigáljon a **\windows\system32\config\SOFTWARE** az operációsrendszer-lemezképet a csatolt, írja be a BROKENSOFTWARE a hive néven.

7.  Ha az ügynök nem működő aktuális verziója, biztonsági másolat készítése az aktuális konfiguráció. Ha a virtuális gép nincs telepítve ügynök, nyissa meg a következő lépéssel.  
      
    1. Nevezze át a mappát \windowsazure \windowsazure.old

    2. A következő nyilvántartó exportálása
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  A meglévő fájlokat a hibaelhárítási virtuális Gépre használják a tárházat a virtuális gép telepítése: 

    1. A virtuális gép hibaelhárító exportálása a következő alkulcsok beállításjegyzék formátumban (.reg): 

        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

        ![A kép exportálási beállításkulcsok kapcsolatos](./media/install-vm-agent-offline/backup-reg.png)

    2. A három beállításjegyzék-fájlok szerkesztése, módosítsa a **rendszer** bejegyzést kulcs **BROKENSYSTEM**, és mentse a fájlt.
        ![A kép kapcsolatos beállításkulcsok módosítása](./media/install-vm-agent-offline/change-reg.png)
    3. Ehhez kattintson duplán a beállításjegyzék fájlokat importálja a fájlokat.
    4. Győződjön meg arról, hogy a következő kulcsok rendszer importálta a BROKENSYSTEM hive sikeresen: WindowsAzureGuestAgent, WindowsAzureTelemetryService és RdAgent.

9.  A Virtuálisgép-ügynök mappájába másolja a C:\windowsazure\packages a &lt;operációsrendszer-lemez, a mellékelt&gt;: \windowsazure\packages.
    ![A kép kapcsolatos fájlok másolása](./media/install-vm-agent-offline/copy-package.png)
      
    **Megjegyzés:** nem másolja át a logs mappájában. Szolgáltatás elindulása után az új naplók jön létre.

10.  Kattintson a BROKENSYSTEM, és válassza ki **fájl** > **struktúra** a menüből.

11.  Kattintson a BROKENSOFTWARE, és válassza ki **fájl** > **struktúra** a menüből.

12.  Válassza le a lemezt, és hozza létre a virtuális gép operációsrendszer-lemez használatával.

13.  Ha a virtuális gép ekkor futó RDAgent és az első létrehozott naplók.

14. Ha egy virtuális Gépet, a klasszikus telepítési modellel készült, akkor elkészült. Azonban ha egy virtuális Gépet a Resource Manager telepítési modellel készült, akkor is használatával Azure PowerShell frissítenie kell a ProvisionGuestAgent tulajdonság, hogy Azure tudja, hogy a virtuális gép rendelkezik-e a telepített ügynök. Ehhez futtassa a következő parancsokat az Azure PowerShell:

        $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
        $vm.VM.ProvisionGuestAgent = $true
        Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>

    Ha futtatja **Get-AzureVM** után az alábbi lépéseket a **GuestAgentStatus** tulajdonság helyett egy üres oldal jelenik töltse fel:

        Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
        GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus

## <a name="next-steps"></a>További lépések

- [Az Azure virtuális gép ügynökének áttekintése](agent-user-guide.md)
- [Virtuálisgép-bővítmények és a Windows szolgáltatások](extensions-features.md)