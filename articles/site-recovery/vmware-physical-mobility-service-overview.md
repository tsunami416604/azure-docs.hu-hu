---
title: Tudnivalók a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási mobilitási szolgáltatásáról Azure Site Recovery-mel | Microsoft Docs
description: Ismerje meg a mobilitási szolgáltatás ügynökét a VMware virtuális gépek és fizikai kiszolgálók Azure-beli vész-helyreállításához a Azure Site Recovery szolgáltatás használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: aeb00b84ac254232e0d68fd9631fb539a928e67d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931890"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Tudnivalók a VMware virtuális gépek és a fizikai kiszolgálók mobilitási szolgáltatásáról

Ha a [Azure site Recovery](site-recovery-overview.md)használatával állítja be a VMWare virtuális gépek és a fizikai kiszolgálók vész-helyreállítását, akkor a site Recovery mobilitási szolgáltatást minden helyszíni VMWare virtuális gépen és fizikai kiszolgálón telepítenie kell.  A mobilitási szolgáltatás rögzíti az adatírásokat a gépen, és továbbítja azokat a Site Recovery Process Servernek. A mobilitási szolgáltatást a következő módszerekkel telepítheti:

- [Leküldéses telepítés](#push-installation): A Site Recovery a mobilitási ügynököt a kiszolgálón telepíti, ha a védelem Azure Portalon keresztül engedélyezve van.
- Telepítés manuálisan: A mobilitási szolgáltatást manuálisan is telepítheti az egyes gépeken a [](#install-mobility-agent-through-command-prompt) [felhasználói felületen](#install-mobility-agent-through-ui) vagy a parancssorban.
- [Automatikus telepítés](vmware-azure-mobility-install-configuration-mgr.md): A telepítést a szoftver központi telepítési eszközeivel (például System Center Configuration Manager) automatizálhatja.

## <a name="anti-virus-on-replicated-machines"></a>Vírusirtó a replikált gépeken

Ha a replikálni kívánt gépeken aktív víruskereső szoftver fut, akkor győződjön meg róla, hogy kizárja a mobilitási szolgáltatás telepítési mappáját a víruskereső műveleteiből (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció a várt módon működjön.

## <a name="push-installation"></a>Leküldéses telepítés

A leküldéses telepítés a portálon aktivált "[replikáció engedélyezése](vmware-azure-enable-replication.md#enable-replication)" művelet szerves része. Miután kiválasztotta a védelemmel ellátni kívánt virtuális gépeket, és elindítja a "replikáció engedélyezése" beállítást, a konfigurációs kiszolgáló leküldi a mobilitási ügynököt a kiszolgálókra, telepíti az ügynököt, és befejezi az ügynök regisztrációját a konfigurációs kiszolgálóval. A művelet sikeres befejezéséhez

- Győződjön meg arról, hogy [](vmware-azure-install-mobility-service.md) a leküldéses telepítés összes előfeltétele teljesül.
- Győződjön meg arról, hogy a kiszolgálók összes konfigurációja a [VMware-ből az Azure Dr-re vonatkozó támogatási mátrix](vmware-physical-azure-support-matrix.md)alá esik.

A leküldéses telepítési munkafolyamat részletei a következő szakaszokban olvashatók.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>[9,23 verziótól](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) kezdődően

A mobilitási ügynök leküldéses telepítése során a következő lépéseket kell végrehajtania

1. Leküldi az ügynököt a forrásoldali gépre. Az ügynöknek a forrás számítógépre való másolása több környezeti hiba miatt sikertelen lehet. A leküldéses telepítési hibák elhárításához tekintse meg [a útmutatást](vmware-azure-troubleshoot-push-install.md) .
2. Miután az ügynök sikeresen átmásolva a kiszolgálóra, az előfeltétel-ellenőrzésekre a kiszolgálón kerül sor. A telepítés sikertelen lesz, ha egy vagy [](vmware-physical-azure-support-matrix.md) több előfeltétel nem teljesül. Ha minden előfeltétel teljesül, a rendszer elindítja a telepítést.
3. Azure Site Recovery VSS-szolgáltató a mobilitási ügynök telepítésének részeként van telepítve a kiszolgálón. Ez a szolgáltató az alkalmazások konzisztens pontjainak létrehozásához használatos. Ha a VSS-szolgáltató telepítése nem sikerül, a rendszer kihagyja ezt a lépést, és az ügynök telepítése folytatódik.
4. Ha az ügynök telepítése sikeres, de a VSS-szolgáltató telepítése meghiúsul, akkor a feladatok állapota "figyelmeztetés" lesz. Ez nem befolyásolja az összeomlási konzisztencia-pontok létrehozását.

    a. Az alkalmazások konzisztens pontjainak létrehozásához olvassa el a Site Recovery VSS-szolgáltató manuális telepítésének befejezésére vonatkozó [útmutatót](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) . </br>
    b.  Ha nem szeretné, hogy az alkalmazás konzisztens pontjai legyenek létrehozva, [módosítsa a replikációs házirendet](vmware-azure-set-up-replication.md#create-a-policy) az alkalmazás konzisztens pontjainak kikapcsolásához.

### <a name="before-922-versions"></a>9,22-verziók előtt

1. Leküldi az ügynököt a forrásoldali gépre. Az ügynöknek a forrás számítógépre való másolása több környezeti hiba miatt sikertelen lehet. A leküldéses telepítési hibák elhárításához tekintse meg [a útmutatást](vmware-azure-troubleshoot-push-install.md) .
2. Miután az ügynök sikeresen átmásolva a kiszolgálóra, az előfeltétel-ellenőrzésekre a kiszolgálón kerül sor. A telepítés sikertelen lesz, ha egy vagy [](vmware-physical-azure-support-matrix.md) több előfeltétel nem teljesül. Ha minden előfeltétel teljesül, a rendszer elindítja a telepítést.
3. Azure Site Recovery VSS-szolgáltató a mobilitási ügynök telepítésének részeként van telepítve a kiszolgálón. Ez a szolgáltató az alkalmazások konzisztens pontjainak létrehozásához használatos. Ha a VSS-szolgáltató telepítése nem sikerül, akkor az ügynök telepítése sikertelen lesz. A mobilitási ügynök telepítésének elmulasztása érdekében a [9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) -es vagy újabb verzióval állítson elő összeomlás-konzisztens pontokat, és telepítse manuálisan a VSS-szolgáltatót.

## <a name="install-mobility-agent-through-ui"></a>A mobilitási ügynök telepítése felhasználói felületen

### <a name="prerequisite"></a>Előfeltétel

- Győződjön meg arról, hogy a kiszolgálók összes konfigurációja a [VMware-ből az Azure Dr-re vonatkozó támogatási mátrix](vmware-physical-azure-support-matrix.md)alá esik.
- [Keresse meg a telepítőt](#locate-installer-files) a-kiszolgáló operációs rendszere alapján.

>[!IMPORTANT]
> Ha az Azure IaaS virtuális gépet egy Azure-régióból egy másikba replikálja, ne használja ezt a metódust. Ehelyett használja a parancssori telepítési módszert.

1. Másolja a telepítési fájlt a gépre, és futtassa.
2. A **telepítés lehetőségnél**válassza a **mobilitási szolgáltatás telepítése**lehetőséget.
3. Válassza ki a telepítési helyet > **telepítse**.

    ![Mobilitási szolgáltatás telepítési lehetőség lapja](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. A telepítés figyelése a **telepítési folyamatban**. A telepítés befejezése után válassza a **Folytatás a konfigurációval** lehetőséget a szolgáltatás konfigurációs kiszolgálóval való regisztrálásához.

    ![Mobilitási szolgáltatás regisztrációs lapja](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. A **konfigurációs kiszolgáló részletei**területen válassza ki a konfigurált IP-címet és jelszót.  

    ![Mobilitási szolgáltatás regisztrációs lapja](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. A regisztráció befejezéséhez válassza a **regisztráció** lehetőséget.

    ![Mobilitási szolgáltatás regisztrációjának utolsó lapja](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>A mobilitási ügynök telepítése parancssorból

### <a name="prerequisite"></a>Előfeltétel

- Győződjön meg arról, hogy a kiszolgálók összes konfigurációja a [VMware-ből az Azure Dr-re vonatkozó támogatási mátrix](vmware-physical-azure-support-matrix.md)alá esik.
- [Keresse meg a telepítőt](#locate-installer-files) a-kiszolgáló operációs rendszere alapján.

### <a name="on-a-windows-machine"></a>Windows rendszerű gépen

- Másolja a telepítőt egy helyi mappába (például C:\Temp) a védelemmel ellátni kívánt kiszolgálón.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- A telepítés a következőképpen történik:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Regisztrálja az ügynököt a konfigurációs kiszolgálóval.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgent. exe/role \<MS/MT >/INSTALLLOCATION \<telepítési hely >/platform "VMware"/Silent
Telepítési naplók | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Kötelező telepítési paraméter. Megadja, hogy kell-e telepíteni a mobilitási szolgáltatást (MS) vagy a fő célt (MT).
/InstallLocation| Nem kötelező paraméter. Megadja a mobilitási szolgáltatás telepítési helyét (bármely mappa).
/Platform | Kötelező. Meghatározza azt a platformot, amelyen a mobilitási szolgáltatás telepítve van. **VMware** a VMWare virtuális gépekhez/fizikai kiszolgálókhoz; **Azure** Azure-beli virtuális gépekhez.<br/><br/> Ha fizikai gépekként kezeli az Azure-beli virtuális gépeket, a **VMware**-et kell megadnia.
/Silent| Nem kötelező. Megadja, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgentConfigurator. exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
Ügynök konfigurációs naplói | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Kötelező paraméter. A konfigurációs kiszolgáló IP-címét adja meg. Bármilyen érvényes IP-címet használjon.
/PassphraseFilePath |  Kötelező. A jelszó helye. Használjon bármely érvényes UNC-vagy helyi elérési útvonalat.

### <a name="on-a-linux-machine"></a>Linux rendszerű gépen

1. Másolja a telepítőt egy helyi mappába (például/tmp) a védelemmel ellátni kívánt kiszolgálón. Futtassa a következő parancsokat egy terminálon:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. A telepítés a következőképpen történik:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. A telepítés befejezése után a mobilitási szolgáltatást regisztrálni kell a konfigurációs kiszolgálón. Futtassa a következő parancsot a mobilitási szolgáltatás a konfigurációs kiszolgálóval való regisztrálásához:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | ./install-d \<telepítési hely >-r \<MS/MT >-v VMware-q
-r | Kötelező telepítési paraméter. Megadja, hogy kell-e telepíteni a mobilitási szolgáltatást (MS) vagy a fő célt (MT).
-d | Nem kötelező paraméter. Megadja a mobilitási szolgáltatás telepítési helyét:/usr/local/ASR
-v | Kötelező. Meghatározza azt a platformot, amelyen a mobilitási szolgáltatás telepítve van. **VMware** a VMWare virtuális gépekhez/fizikai kiszolgálókhoz; **Azure** Azure-beli virtuális gépekhez.
-q | Nem kötelező. Megadja, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Kötelező paraméter. A konfigurációs kiszolgáló IP-címét adja meg. Bármilyen érvényes IP-címet használjon.
-P |  Kötelező. A fájl teljes elérési útja, amelyben a rendszer menti a jelszót. Bármilyen érvényes mappát használjon.

## <a name="azure-virtual-machine-agent"></a>Azure-beli virtuálisgép-ügynök

- **Windows rendszerű virtuális gépek**: A mobilitási szolgáltatás verziójának 9.7.0.0 az [Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) -beli virtuálisgép-ügynököt a mobilitási szolgáltatás telepítője telepíti. Ez biztosítja, hogy amikor a gép feladatátvételt hajt végre az Azure-ba, az Azure-beli virtuális gép megfelel a virtuálisgép-bővítmények használatára vonatkozó előfeltételnek.
- **Linux rendszerű virtuális gépek**: A [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) manuálisan kell telepíteni az Azure-beli virtuális gépen a feladatátvétel után.

## <a name="locate-installer-files"></a>Telepítőfájlok megkeresése

Nyissa meg a%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappát a konfigurációs kiszolgálón. Győződjön meg arról, hogy melyik telepítőre van szükség az operációs rendszer alapján. A következő táblázat összefoglalja az egyes VMware-alapú virtuális gépek és a fizikai kiszolgálói operációs rendszerek telepítési fájljait. A megkezdése előtt tekintse át a [támogatott operációs rendszereket](vmware-physical-azure-support-matrix.md#replicated-machines) .

**Telepítőfájl** | **Operációs rendszer (csak 64-bit)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| 11 SP4 SUSE Linux Enterprise Server
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14,04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16,04 LTS-kiszolgáló
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>További lépések

[A mobilitási szolgáltatás leküldéses telepítésének beállítása](vmware-azure-install-mobility-service.md).
