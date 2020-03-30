---
title: Az Azure Arc kezelése kiszolgálókhoz (előzetes verziójú) ügynökhöz
description: Ez a cikk ismerteti a különböző felügyeleti feladatokat, amelyekáltalában az Azure Arc a kiszolgálók csatlakoztatott gép ügynöke általában végrehajtani.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 758e6123fd09df1e3f8b2e883a729b9fec4328d1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367290"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>A csatlakoztatott számítógép ügynökének kezelése és karbantartása

Az Azure Arc üzembe helyezése után a kiszolgálók (előzetes verzió) Csatlakoztatott gép ügynök Windows vagy Linux, előfordulhat, hogy újra kell konfigurálnia az ügynök, frissítse, vagy távolítsa el a számítógépről, ha elérte a kivonási szakaszban az életciklusa. Ezeket a rutinkarbantartási feladatokat egyszerűen kezelheti manuálisan vagy automatizálással, ami csökkenti a működési hibákat és a költségeket.

## <a name="upgrading-agent"></a>Ügynök frissítése

A Windows és Linux Azure Connected Machine ügynök manuálisan vagy automatikusan frissíthető a legújabb kiadásra a követelményektől függően. Az alábbi táblázat az ügynökfrissítés végrehajtásához támogatott módszereket ismerteti.

| Operációs rendszer | Frissítési módszer |
|------------------|----------------|
| Windows | Manuálisan<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper között](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows-ügynök

Ha windowsos gépen szeretné frissíteni az ügynököt a legújabb verzióra, az ügynök elérhető a Microsoft Update szolgáltatásból, és a meglévő szoftverfrissítés-kezelési folyamattal telepíthető. Manuálisan is futtatható a parancssorból, egy parancsfájlból vagy más automatizálási megoldásból, vagy a felhasználói felület varázslójából a `AzureConnectedMachine.msi`végrehajtással. 

> [!NOTE]
> * Az ügynök frissítéséhez rendszergazdai engedélyekkel kell *rendelkeznie.*
> * A manuális frissítéshez először le kell töltenie és át kell másolnia a Telepítő csomagot a célkiszolgáló natérébe vagy egy megosztott hálózati mappába. 

Ha nem ismeri a Windows Installer csomagok parancssori beállításait, tekintse át az [Msiexec szabványos parancssori beállításait](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) és az [Msiexec parancssori kapcsolókat.](https://docs.microsoft.com/windows/win32/msi/command-line-options)

#### <a name="to-upgrade-using-the-setup-wizard"></a>Frissítés a Telepítő varázslóval

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az **AzureConnectedMachineAgent.msi** fájl végrehajtása a telepítővarázsló elindításához.

A telepítővarázsló felderíti, hogy létezik-e korábbi verzió, majd automatikusan végrehajtja az ügynök frissítését. A frissítés befejezéseután a telepítővarázsló automatikusan bezárul.

#### <a name="to-upgrade-from-the-command-line"></a>Frissítés a parancssorból

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az ügynök csendes frissítéséhez és a mappában lévő `C:\Support\Logs` telepítési naplófájl létrehozásához futtassa a következő parancsot.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux ügynök

Az ügynök frissítése egy Linux gépen a legújabb verzióra, két parancsot foglal magában. Egy parancs a helyi csomagindex frissítésére az adattárakból elérhető legújabb csomagok listájával, és egy parancs a helyi csomag frissítéséhez. 

#### <a name="upgrade-ubuntu"></a>Az Ubuntu frissítése

1. Ha frissíteni szeretné a helyi csomagindexet az adattárakban végrehajtott legújabb módosításokkal, futtassa a következő parancsot:

    ```bash
    apt update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    apt upgrade
    ```

Az [apt](https://help.ubuntu.com/lts/serverguide/apt.html) parancs lépései, például a csomagok telepítése `/var/log/dpkg.log` és eltávolítása, a naplófájlba kerülnek.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Frissítés Red Hat / CentOS / Amazon Linux

1. Ha frissíteni szeretné a helyi csomagindexet az adattárakban végrehajtott legújabb módosításokkal, futtassa a következő parancsot:

    ```bash
    yum check-update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    yum update
    ```

A [yum](https://access.redhat.com/articles/yum-cheat-sheet) parancs lépései, például a csomagok telepítése `/var/log/yum.log` és eltávolítása a naplófájlba kerülnek. 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise frissítése

1. Ha frissíteni szeretné a helyi csomagindexet az adattárakban végrehajtott legújabb módosításokkal, futtassa a következő parancsot:

    ```bash
    zypper refresh
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    zypper update
    ```

A [zypper](https://en.opensuse.org/Portal:Zypper) parancs műveletek, például a csomagok telepítése és `/var/log/zypper.log` eltávolítása a naplófájlba kerülnek. 

## <a name="remove-the-agent"></a>Az ügynök eltávolítása

Az alábbi eljárások egyikével távolítsa el a Windows vagy Linux ügynököt az ebben a szakaszban ismertetett parancssorból vagy telepítővarázslóból. Az ügynök eltávolítása előtt először válassza le a gépet az Azure Arc kiszolgálók (előzetes verzió) az alábbi lépések végrehajtásával: 

1. Nyissa meg az Azure Arc kiszolgálókhoz (előzetes verzió) az [Azure Portalon.](https://aka.ms/hybridmachineportal)

2. Jelölje ki a gépet a listában, jelölje ki a három pontot (**...**), majd kattintson a **Törlés gombra.**

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="uninstall-from-control-panel"></a>Eltávolítás a Vezérlőpultról

1. A Windows-ügynök számítógépről való eltávolításához tegye a következőket:

    a. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.  
    b. A **Vezérlőpulton**válassza a **Programok és szolgáltatások**lehetőséget.  
    c. A **Programok és szolgáltatások**területen válassza az Azure Connected Machine **Agent**lehetőséget, válassza **az Eltávolítás**lehetőséget, majd az **Igen**lehetőséget.  

    >[!NOTE]
    > Az ügynök telepítővarázslóját az **AzureConnectedMachineAgent.msi** telepítőcsomagra duplán kattintva is futtathatja.

#### <a name="uninstall-from-the-command-line"></a>Eltávolítás a parancssorból

Ha manuálisan szeretné eltávolítani az ügynököt a parancssorból, vagy automatikus módszert, például parancsfájlt szeretne használni, a következő példát használhatja. Először le kell kérnie a termékkódot, amely egy GUID, amely az alkalmazáscsomag fő azonosítója az operációs rendszerből. Az eltávolítás az Msiexec.exe parancssorból `msiexec /x {Product Code}`történik .
    
1. Nyissa meg a Rendszerleíróadatbázis-szerkesztőt.

2. A rendszerleíró `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`kulcs alatt keresse meg és másolja a termékkód GUID azonosítóját.

3. Ezután az Msiexec használatával eltávolíthatja az ügynököt az alábbi példák használatával:

   * A parancssori típusból:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Ugyanazokat a lépéseket a PowerShell használatával hajthatja végre:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux ügynök

A Linux-ügynök eltávolításához a használni kívánt parancs a Linux operációs rendszertől függ.

- Ubuntu esetén futtassa a következő parancsot:

    ```bash
    sudo apt purge azcmagent
    ```

- RHEL, CentOS és Amazon Linux esetén futtassa a következő parancsot:

    ```bash
    sudo yum remove azcmagent
    ```

- Az SLES esetében futtassa a következő parancsot:

    ```bash
    sudo zypper remove azcmagent
    ```
