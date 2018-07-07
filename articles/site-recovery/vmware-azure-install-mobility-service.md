---
title: A mobilitási szolgáltatás (VMware vagy fizikai az Azure-bA) telepítése |} A Microsoft Docs
description: Ismerje meg, hogy a védelme érdekében a helyszíni VMware virtuális gépek és fizikai kiszolgálók az Azure Site Recovery mobilitási szolgáltatás ügynökének telepítése.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: ramamill
ms.openlocfilehash: e98e67c453e909662c509598acc4cf65c5a0a6fa
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887657"
---
# <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése 

Az Azure Site Recovery mobilitási szolgáltatás VMware virtuális gépeken és fizikai kiszolgálók Azure-bA replikálni kívánt telepítve van. A szolgáltatás rögzíti az adatok írásáért egy számítógépen, és továbbítja őket a folyamatkiszolgálónak. Minden olyan számítógép (a VMware virtuális gépek vagy fizikai kiszolgáló), amely az Azure-bA replikálni kívánt mobilitási szolgáltatás üzembe helyezése. A mobilitási szolgáltatást a kiszolgálókon és a VMware virtuális gépeket szeretné a védelmét, a következő módszerekkel helyezhető üzembe:


* [Telepítés szoftveres üzembehelyezési eszközök például a System Center Configuration Manager használatával](vmware-azure-mobility-install-configuration-mgr.md)
* [Az Azure Automation és a Desired State Configuration (Automation DSC) telepítése](vmware-azure-mobility-deploy-automation-dsc.md)
* [Telepítse manuálisan a felhasználói felületről](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Manuális telepítése a parancssorból](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Telepítse a Site Recovery ügyfélleküldéses telepítés használatával](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Verziójától kezdve 9.7.0.0, a Windows virtuális gépeken, a mobilitási szolgáltatás telepítője is telepíti a legújabb elérhető [Azure Virtuálisgép-ügynök](../virtual-machines/extensions/features-windows.md#azure-vm-agent). Amikor egy számítógép átadja a feladatokat az Azure-ba, a számítógép megfelel-e az ügynök telepítése minden olyan VM-bővítmény használata a előfeltételeit.

## <a name="prerequisites"></a>Előfeltételek
Előfeltételként szükséges lépések végrehajtása előtt, manuálisan telepíteni a mobilitási szolgáltatást a kiszolgálón:
1. Jelentkezzen be a konfigurációs kiszolgáló, és nyissa meg egy parancssori ablakot rendszergazdaként.
2. Módosítsa a könyvtárat a bin mappát, és ezután hozzon létre egy hozzáférési kódot fájlt.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. A hozzáférési fájl kódjának Store biztonságos helyen. A mobilitási szolgáltatás telepítése során a fájlt használja.
4. A mobilitási szolgáltatás telepítők az összes támogatott operációs rendszerek %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappában találhatók.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>A mobilitási szolgáltatás telepítő – operációs rendszer leképezése

| Telepítőfájl sablon neve| Operációs rendszer |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | A Windows Server 2008 R2 SP1 (64 bites) </br> A Windows Server 2012 (64 bites) </br> A Windows Server 2012 R2 (64 bites) </br> A Windows Server 2016 (64 bites) |
|A Microsoft-ASR\_UA\*bites RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (csak 64 bites verzió esetén) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (csak 64 bites verzió esetén) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (csak 64 bites verzió esetén) </br> 7.0, 7.1, 7.2 centOS 7.3 (csak 64 bites verzió esetén) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (csak 64 bites verzió esetén)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (csak 64 bites verzió esetén)|
|A Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (csak 64 bites verzió esetén)|
|A Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server (csak 64 bites verzió esetén)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (csak 64 bites verzió esetén)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (csak 64 bites verzió esetén)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Mobilitási szolgáltatás manuális telepítése a grafikus felhasználói felület használatával

>[!IMPORTANT]
> Ha Azure IaaS virtuális gépek replikálása az Azure-régióból egy előfizetést vagy egy másikra a konfigurációs kiszolgáló segítségével, a parancssor alapú telepítési módszert használja.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Mobilitási szolgáltatás manuális telepítése parancssorból

### <a name="command-line-installation-on-a-windows-computer"></a>Egy Windows-számítógépen parancssori telepítés
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Egy Linux rendszerű számítógépen parancssori telepítés
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Telepítse a mobilitási szolgáltatás leküldéses telepítése az Azure Site Recovery szolgáltatásból
A mobilitási szolgáltatás leküldéses telepítéséhez a Site Recovery használatával teheti meg. Minden célszámítógépen a következő előfeltételeknek kell megfelelnie.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
A mobilitási szolgáltatás telepítése után, az Azure Portalon válassza ki a **+ replikálás** ezek a virtuális gépek védelmének megkezdéséhez.

## <a name="update-mobility-service"></a>Mobilitási szolgáltatás frissítése

> [!WARNING]
> Győződjön meg arról, hogy a konfigurációs kiszolgáló, horizontális felskálázási folyamatkiszolgáló, és bármely fő célkiszolgálókat, amelyek a központi telepítés részei frissítve lett, mielőtt elkezdené a mobilitási szolgáltatás frissítése a védett kiszolgálókon.

1. Az Azure Portalon keresse meg a *a tároló neve* > **replikált elemek** megtekintése.
2. Ha a konfigurációs kiszolgáló már frissítették a legújabb verzióra, akkor megjelenik egy értesítés, olvasó, "új Site recovery replikációs ügynökének frissítése érhető el. Telepítéséhez kattintson ide."

     ![Replikált elemek ablak](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Válassza ki az értesítés a virtuális gép kiválasztására szolgáló lap megnyitásához.
4. Válassza ki a futó mobilitási szolgáltatás frissítése, és válassza ki a kívánt virtuális gépeket **OK**.

     ![Replikált elemek Virtuálisgép-lista](.\media\vmware-azure-install-mobility-service\update-okpng.png)

A mobilitási szolgáltatás frissítése feladat elindul, a kiválasztott virtuális gépek mindegyikéhez.

> [!NOTE]
> [További információ](vmware-azure-manage-configuration-server.md) hogyan lehet frissíteni a mobilitási szolgáltatás telepítéséhez használt fiók jelszavát.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Távolítsa el a mobilitási szolgáltatást a Windows Server-számítógépen
Az alábbi módszerek valamelyikével távolítsa el a mobilitási szolgáltatást a Windows Server rendszerben.

### <a name="uninstall-by-using-the-gui"></a>Távolítsa el a grafikus felhasználói felület használatával
1. A Vezérlőpulton válassza **programok**.
2. Válassza ki **a Microsoft Azure Site Recovery mobilitási szolgáltatás vagy fő célkiszolgálóként kiszolgáló**, majd válassza ki **Eltávolítás**.

### <a name="uninstall-at-a-command-prompt"></a>Távolítsa el a parancsot a parancssorba
1. Nyisson meg egy parancssori ablakot rendszergazdaként.
2. Távolítsa el a mobilitási szolgáltatást, futtassa a következő parancsot:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Távolítsa el a mobilitási szolgáltatást egy Linux rendszerű számítógépen
1. A Linux-kiszolgálón, jelentkezzen be egy **legfelső szintű** felhasználói.
2. A terminálban keresse /user/local/ASR.
3. Távolítsa el a mobilitási szolgáltatást, futtassa a következő parancsot:

    ```
    uninstall.sh -Y
    ```
