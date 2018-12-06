---
title: A vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure Site Recovery mobilitási szolgáltatás kapcsolatos |} A Microsoft Docs
description: Ismerje meg a mobilitási szolgáltatás ügynökének vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery szolgáltatással.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: e3967319cd41399209bd50886bce88efc8ba6ba6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956516"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Tudnivalók a mobilitási szolgáltatás VMware virtuális gépek és fizikai kiszolgálókhoz

Amikor, vészhelyreállítás beállítása VMware virtuális gépek és fizikai kiszolgálók [Azure Site Recovery](site-recovery-overview.md), az egyes helyszíni VMware virtuális gép a Site Recovery mobilitási szolgáltatás és a fizikai kiszolgáló telepítését.  A mobilitási szolgáltatást kell-e a gépen adatírásokat, és továbbítja őket a Site Recovery is folyamatkiszolgálón. A mobilitási szolgáltatás a következő módszerekkel helyezhető üzembe:

[Leküldéses telepítés](vmware-azure-install-mobility-service.md): konfigurálni a Site Recovery a mobilitási szolgáltatás leküldéses telepítés végrehajtásához: ehhez, ha beállította a vész-helyreállítási is meg egy fiókot, amely a Site Recovery folyamatkiszolgáló használatával is hozzáférhetnek a virtuális gép vagy fizikai kiszolgáló a szolgáltatás telepítése céljából.
[Telepítse manuálisan](vmware-physical-mobility-service-install-manual.md): a mobilitási szolgáltatást manuálisan is telepíti, az összes olyan számítógépen, a felhasználói felületen vagy az parancssor használatával.
[Automatikus központi telepítési](vmware-azure-mobility-install-configuration-mgr.md): a szoftver központi telepítési eszközök például a System Center Configuration Manager telepítési automatizálható.

## <a name="azure-virtual-machine-agent"></a>Az Azure virtuálisgép-ügynök

- **Windows virtuális gépek**: a mobilitási szolgáltatás 9.7.0.0 verziójáról a [Azure Virtuálisgép-ügynök](../virtual-machines/extensions/features-windows.md#azure-vm-agent) a mobilitási szolgáltatás telepítőjének van telepítve. Ez biztosítja, hogy a gép átadja a feladatokat az Azure-ba, amikor az Azure virtuális gép megfelel-e az ügynök telepítése minden olyan Vm-bővítmény használata a előfeltételeit.
- **Linux rendszerű virtuális gépek**: A [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) manuálisan kell telepíteni az Azure virtuális gépen a feladatátvételt követően.

## <a name="installer-files"></a>Installer-fájlok

A táblázat összefoglalja a telepítőfájlokat, minden a VMware virtuális gépek és fizikai kiszolgálók operációs rendszer. Áttekintheti [támogatott operációs rendszerek](vmware-physical-azure-support-matrix.md#replicated-machines) megkezdése előtt.


**Telepítőfájl** | **Operációs rendszer (csak 64 bites verzió esetén)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | A Windows Server 2016-ban; A Windows Server 2012 R2; A Windows Server 2012; A Windows Server 2008 R2 SP1 
A Microsoft-ASR\_UA\*bites RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
A Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2 SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5-ös
A Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | 14.04-es Ubuntu Linux
A Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>A replikált gépek víruskereső

Ha replikálni kívánt gépek aktív víruskereső szoftvert, ellenőrizze, hogy a mobilitási szolgáltatás telepítési mappa kizárása víruskereső operations (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció megfelelően működik-e.

## <a name="update-the-mobility-service"></a>A mobilitási szolgáltatás frissítése

1. Megkezdése előtt győződjön meg arról, hogy a konfigurációs kiszolgáló, horizontális felskálázási folyamatkiszolgáló, és bármely fő célkiszolgálókat, amelyek a központi telepítés részei frissítve lett, mielőtt frissítené a mobilitási szolgáltatás a védett gépek.
2. A portálon nyissa meg a tároló > **replikált elemek**.
3. Ha a konfigurációs kiszolgálót a legújabb verzióra, megjelenik egy értesítés, olvasó, "új Site recovery replikációs ügynökének frissítése érhető el. Telepítéséhez kattintson ide."

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kattintson az értesítésre, és a **ügynök frissítése**, válassza ki a gépeket, amelyeken szeretné frissíteni a mobilitási szolgáltatást. Ezután kattintson az **OK** gombra.

     ![Replikált elemek Virtuálisgép-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. A mobilitási szolgáltatás frissítése feladat elindul, a kiválasztott gépek mindegyikéhez.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>A mobilitási szolgáltatás leküldéses telepítéséhez használja a fiók frissítése

A mobilitási szolgáltatás leküldéses telepítésének engedélyezése a Site Recovery üzembe helyezésekor megadott egy fiókot, amely a Site Recovery folyamatkiszolgáló használja a gépek eléréséhez, és telepítse a szolgáltatást, amikor a replikáció engedélyezve van a számítógépen. Ha szeretné frissíteni a fiók hitelesítő adatait, hajtsa végre a [ezek az utasítások](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása

### <a name="on-a-windows-machine"></a>Egy Windows-gépen

Távolítsa el a felhasználói felületen vagy a parancssorból.

- **A felhasználói felületről**: A a Vezérlőpulton a gép, jelölje be **programok**. Válassza ki **a Microsoft Azure Site Recovery mobilitási szolgáltatás vagy fő célkiszolgálóként kiszolgáló** > **Eltávolítás**.
- **Egy parancssorból**: nyisson meg egy parancssori ablakot rendszergazdaként azon a gépen. Futtassa az alábbi parancsot: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Linux rendszerű gépen
1. A Linux rendszerű gépen jelentkezzen be egy **legfelső szintű** felhasználói.
2. A terminálban keresse /user/local/ASR.
3. Futtassa az alábbi parancsot:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>További lépések

[Állítsa be a mobilitási szolgáltatás leküldéses telepítése](vmware-azure-install-mobility-service.md).
