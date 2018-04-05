---
title: Telepítse a mobilitási szolgáltatás (VMware vagy fizikai az Azure-bA) |} Microsoft Docs
description: Ismerje meg, hogyan kell telepíteni a mobilitási szolgáltatás ügynököt a helyszíni VMware virtuális gépek és fizikai kiszolgálók az Azure Site Recovery védelmét.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 445a5f10eac0959dab57e10680659c0792ad6fba
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése 

Az Azure Site Recovery mobilitási szolgáltatás telepítve van a VMware virtuális gépek és fizikai kiszolgálók Azure-bA replikálni kívánt. A szolgáltatás egy számítógépen végbemenő adatírásokat, és ezután továbbítja őket a folyamatkiszolgálónak. Minden számítógépre (VMware virtuális gép vagy fizikai kiszolgálón), amely az Azure-bA replikálni kívánt telepíteni a mobilitási szolgáltatás. A mobilitási szolgáltatást a kiszolgálókon és az alábbi eljárások segítségével védeni kívánt VMware virtuális gépeket telepíthet:


* [Telepítés szoftvertelepítési eszközök például a System Center Configuration Manager használatával](vmware-azure-mobility-install-configuration-mgr.md)
* [Telepítse az Azure Automation szolgáltatásbeli és célállapot-konfiguráció (Automation DSC)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Telepítse manuálisan a felhasználói felületen](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Telepítse manuálisan a parancssorból](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Telepítse a Site Recovery ügyfélleküldéses telepítés használata](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Verzió 9.7.0.0, a Windows virtuális gépeken, kezdve a mobilitási szolgáltatás telepítési is telepíti a legújabb elérhető [Azure Virtuálisgép-ügynök](../virtual-machines/windows/extensions-features.md#azure-vm-agent). Ha a számítógép nem keresztül az Azure-ba, a számítógép megfelel-e a Virtuálisgép-bővítmény használatára vonatkozó előfeltételek telepítése.

## <a name="prerequisites"></a>Előfeltételek
Előfeltételként szükséges lépések végrehajtása előtt manuálisan telepítenie mobilitási szolgáltatás a kiszolgálón:
1. Jelentkezzen be a konfigurációs kiszolgáló, és nyissa meg egy parancssori ablakot rendszergazdaként.
2. Módosítsa a könyvtárat a bin mappát, és hozza létre jelszót fájlt.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. A jelszó fájlt tárolja biztonságos helyen. A fájlt használja a mobilitási szolgáltatás telepítése során.
4. Minden támogatott operációs rendszerek a mobilitási szolgáltatás telepítők a %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappában találhatók.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mobilitási szolgáltatás installer-operációs rendszer leképezése

| Telepítési fájl neve| Operációs rendszer |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bites) </br> A Windows Server 2012 (64 bites) </br> Windows Server 2012 R2 (64 bites) </br> Windows Server 2016 (64 bites) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4 6.5, 6.6, 6.7, 6.8, 6.9 (csak 64 bites verzió esetén) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (csak 64 bites verzió esetén) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (csak 64 bites verzió esetén) </br> CentOS 7.0, 7.1, 7.2, 7.3 (csak 64 bites verzió esetén) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4 6.5 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server (csak 64 bites verzió esetén)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (csak 64 bites verzió esetén)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (csak 64 bites verzió esetén)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Telepítse manuálisan a mobilitási szolgáltatást a grafikus felhasználói felület használatával

>[!IMPORTANT]
> Ha a konfigurációs kiszolgáló Azure IaaS virtuális gépek replikálásához egy Azure előfizetés vagy régió egy másik, a parancssor alapú telepítési módszer használata.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Telepítse manuálisan a mobilitási szolgáltatást a parancssorba

### <a name="command-line-installation-on-a-windows-computer"></a>Parancssori telepítés Windows-számítógépen
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Parancssori telepítés, a Linux számítógépre
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Telepítse a mobilitási szolgáltatás leküldéses telepítése az Azure Site Recovery által
A mobilitási szolgáltatás leküldéses telepítéséhez a Site Recovery segítségével teheti meg. Minden célszámítógépen meg kell felelnie a következő előfeltételek teljesülését.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Az Azure-portálon a mobilitási szolgáltatás telepítése után válasszon **+ replikálás** ezen virtuális gépek védelmének megkezdéséhez.

## <a name="update-mobility-service"></a>Frissítse a mobilitási szolgáltatás

> [!WARNING]
> Győződjön meg arról, hogy a konfigurációs kiszolgáló, kibővített folyamat kiszolgálókat, és bármely fő célkiszolgálóra, amely a központi telepítés részét képezik frissítve lett, mielőtt megkezdené a mobilitási szolgáltatás frissítése a védett kiszolgálókon.

1. Az Azure-portálon keresse meg a *a tároló neve* > **replikált elemek** nézet.
2. Ha a kiszolgáló már frissült a legújabb verzióra, megjelenik egy értesítés, hogy olvassa be az "új hely helyreállítási replikációs ügynök frissítés érhető el. Ide kattintva telepítheti."

     ![Replikált elemek ablak](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Válassza ki a virtuális gép kiválasztása lapon nyissa meg az értesítést.
4. Válassza ki a virtuális gépek futó mobilitási szolgáltatás frissítése, és válassza ki a kívánt **OK**.

     ![Replikált elemek VM listája](.\media\vmware-azure-install-mobility-service\update-okpng.png)

A frissítés Mobilitásiszolgáltatás feladat elindul a kijelölt virtuális gépek mindegyikéhez.

> [!NOTE]
> [További](vmware-azure-manage-configuration-server.md) frissítésével a mobilitási szolgáltatás telepítéséhez használt fiók jelszavát.

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
