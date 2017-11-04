---
title: "Telepítse a mobilitási szolgáltatás (VMware vagy fizikai az Azure-bA) |} Microsoft Docs"
description: "Ismerje meg, hogyan kell telepíteni a mobilitási szolgáltatás ügynököt a helyi számítógép védelme érdekében."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 10/30/2017
ms.author: anoopkv
ms.openlocfilehash: aa7bb25387efbc603dac9aaa0a56b3e30d0bfb4d
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>Telepítse a mobilitási szolgáltatás (VMware vagy fizikai az Azure-bA)
Az Azure Site Recovery mobilitási szolgáltatás egy számítógépen végbemenő adatírásokat, és ezután továbbítja őket a folyamatkiszolgálónak. Minden számítógépre (VMware virtuális gép vagy fizikai kiszolgálón), amely az Azure-bA replikálni kívánt telepíteni a mobilitási szolgáltatás. Az alábbi módszerek használatával védeni kívánt kiszolgálók mobilitási szolgáltatás telepítése:


* [Telepítse a mobilitási szolgáltatás szoftvertelepítési eszközök például a System Center Configuration Manager használatával](site-recovery-install-mobility-service-using-sccm.md)
* [Mobilitási szolgáltatás telepítése Azure Automation és célállapot-konfiguráció (Automation DSC)](site-recovery-automate-mobility-service-install.md)
* [Telepítse manuálisan a mobilitási szolgáltatást a grafikus felhasználói felületen (GUI)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [Telepítse manuálisan a mobilitási szolgáltatást a parancssorba](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Telepítse a mobilitási szolgáltatás leküldéses telepítése a Site Recovery által](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Kezdve verziója 9.7.0.0, a Windows virtuális gépek (VM), a mobilitási szolgáltatás telepítési is telepíti a legújabb elérhető [Azure Virtuálisgép-ügynök](../virtual-machines/windows/extensions-features.md#azure-vm-agent). Ha a számítógép nem keresztül az Azure-ba, a számítógép megfelel-e a Virtuálisgép-bővítmény használatára vonatkozó előfeltételek telepítése.

## <a name="prerequisites"></a>Előfeltételek
Előfeltételként szükséges lépések végrehajtása előtt manuálisan telepítenie mobilitási szolgáltatás a kiszolgálón:
1. Jelentkezzen be a konfigurációs kiszolgáló, és nyissa meg egy parancssori ablakot rendszergazdaként.
2. Módosítsa a könyvtárat a bin mappát, és hozza létre jelszót fájlt:

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. A jelszó fájlt tárolja biztonságos helyen. A fájlt a mobilitási szolgáltatás telepítése során használja.
4. Minden támogatott operációs rendszerek a mobilitási szolgáltatás telepítők a %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappában találhatók.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mobilitási szolgáltatás installer-operációs rendszer leképezése

| Telepítési fájl neve| Operációs rendszer |
|---|--|
|Microsoft-ASR\_EE\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bites) </br> A Windows Server 2012 (64 bites) </br> Windows Server 2012 R2 (64 bites) </br> Windows Server 2016 (64 bites) |
|Microsoft-ASR\_EE\*bites RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4 6.5, 6.6, 6.7, 6.8, 6.9 (csak 64 bites verzió esetén) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (csak 64 bites verzió esetén) |
|Microsoft-ASR\_EE\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (csak 64 bites verzió esetén) </br> CentOS 7.0, 7.1, 7.2, 7.3 (csak 64 bites verzió esetén) |
|Microsoft-ASR\_EE\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_EE\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_EE\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4 6.5 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_EE\*UBUNTU 14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_EE\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server (csak 64 bites verzió esetén)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (csak 64 bites verzió esetén)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (csak 64 bites verzió esetén)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Telepítse manuálisan a mobilitási szolgáltatást a grafikus felhasználói felület használatával

>[!IMPORTANT]
> Használatakor a **konfigurációs kiszolgáló** replikálásához **Azure IaaS virtuális gépek** a egy Azure előfizetés vagy régió másik majd **használhatja a parancssor alapú telepítési** módszer

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Telepítse manuálisan a mobilitási szolgáltatást a parancssorba

### <a name="command-line-installation-on-a-windows-computer"></a>Parancssori telepítés Windows-számítógépen
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Parancssori telepítés, a Linux számítógépre
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Telepítse a mobilitási szolgáltatás leküldéses telepítése az Azure Site Recovery által
A mobilitási szolgáltatás leküldéses telepítéséhez tegye a Site Recovery segítségével, minden célszámítógépen a következő előfeltételeknek kell megfelelnie:

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Az Azure-portálon a mobilitási szolgáltatás telepítése után válassza ki a **+ replikálás** gombra kattintva indítsa el a virtuális gépek védelmét.

## <a name="update-mobility-service"></a>Frissítse a mobilitási szolgáltatás

> [!WARNING]
> Győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővített folyamat kiszolgálók és a kiszolgálók bármelyike a fő célkiszolgálón, amelyek a központi telepítés részét frissül a mobilitási szolgáltatást a védett kiszolgálókon a frissítés megkezdése előtt. További információ a [frissítése a konfigurációs kiszolgáló](site-recovery-vmware-to-azure-manage-configuration-server.md#upgrading-a-configuration-server) és [a kibővített folyamat kiszolgálók frissítése](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#upgrading-a-scale-out-process-server)

1. Az Azure portálon keresse meg a <Your Vault> replikált elemek Nézet ->.
2. Ha a **konfigurációs kiszolgáló** már frissült a legújabb verzióra, akkor megjelenik egy értesítés, hogy az olvasások *új helyet a helyreállítási replikációs ügynök frissítés érhető el. Történő telepítéséhez kattintson a*

     ![ReplicatedItems](.\media\site-recovery-vmware-to-azure-install-mob-svc\replicated-item-notif.png)
3. Kattintson az értesítés nyissa meg a virtuális gép kiválasztása lapon.
4. Válassza ki a virtuális gépeknek meg futó mobilitási szolgáltatás frissítése, és kattintson az OK gombra.

     ![ReplicatedItemsVMList](.\media\site-recovery-vmware-to-azure-install-mob-svc\update-okpng.png)
5. A kijelölt virtuális gépek mindegyikének a frissítés Mobilitásiszolgáltatás feladat elindul.

> [!NOTE]
> [További](site-recovery-vmware-to-azure-manage-configuration-server.md) frissítésével a mobilitási szolgáltatás telepítéséhez használt fiók jelszava

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Távolítsa el a mobilitási szolgáltatást a Windows Server rendszerben
Az alábbi módszerek valamelyikével távolítsa el a mobilitási szolgáltatás egy Windows Server rendszeren.

### <a name="uninstall-by-using-the-gui"></a>Távolítsa el a grafikus felhasználói felület használatával
1. A Vezérlőpulton válassza **programok**.
2. Válassza ki **Microsoft Azure Site helyreállítási mobilitási szolgáltatás/fő célkiszolgáló**, majd válassza ki **Eltávolítás**.

### <a name="uninstall-at-a-command-prompt"></a>Távolítsa el a parancssorba
1. Nyisson meg egy parancssori ablakot rendszergazdaként.
2. Távolítsa el a mobilitási szolgáltatást, futtassa a következő parancsot:

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Távolítsa el a mobilitási szolgáltatás egy Linux-számítógép
1. A Linux-kiszolgálón jelentkezzen be egy **legfelső szintű** felhasználó.
2. A terminálban keresse /user/local/ASR.
3. Távolítsa el a mobilitási szolgáltatást, futtassa a következő parancsot:

```
uninstall.sh -Y
```
