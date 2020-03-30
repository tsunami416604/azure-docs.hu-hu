---
title: A Mobility Service a VMware virtuális gépek és a fizikai kiszolgálók vészhelyreállításához az Azure Site Recovery használatával | Microsoft dokumentumok
description: Ismerje meg a Mobility Service ügynök a VMware virtuális gépek és a fizikai kiszolgálók az Azure-ba az Azure Site Recovery szolgáltatás használatával a vész-helyreállítási ügynök.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256834"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>A VMware virtuális gépek és fizikai kiszolgálók mobilitási szolgáltatása

Ha az [Azure Site Recovery](site-recovery-overview.md)használatával vészhelyreállítást állít be a VMware virtuális gépekhez és a fizikai kiszolgálókhoz, a Site Recovery Mobility szolgáltatást minden helyszíni VMware virtuális gépre és fizikai kiszolgálóra telepíti.  A Mobilitás szolgáltatás rögzíti az adatok írásait a számítógépen, és továbbítja azokat a Site Recovery folyamatkiszolgálóra. A mobilitási szolgáltatást a következő módszerekkel telepítheti:

- [Leküldéses telepítés:](#push-installation)A Site Recovery telepíti a mobilitási ügynököt a kiszolgálón, ha a védelem engedélyezve van az Azure Portalon keresztül.
- Manuális telepítés: A Mobilitás szolgáltatást manuálisan telepítheti minden számítógépre a [felhasználói felületen](#install-mobility-agent-through-ui) vagy a [parancssorban](#install-mobility-agent-through-command-prompt)keresztül.
- [Automatikus telepítés](vmware-azure-mobility-install-configuration-mgr.md): A telepítést szoftvertelepítési eszközökkel, például a Configuration Managerrel automatizálhatja.

> [!NOTE]
> A Mobility ügynök a memória körülbelül 6–10%-át használja a virtuális gépekkel működő forrásgépeken vmware virtuális gépekhez vagy fizikai gépekhez.

## <a name="anti-virus-on-replicated-machines"></a>Víruselleni szer másolt gépeken

Ha a replikálni kívánt gépek aktív víruskereső szoftvert futtatnak, győződjön meg arról, hogy kizárja a Mobility szolgáltatás telepítési mappáját a víruskereső műveletekből (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció a várt módon működjön.

## <a name="push-installation"></a>Push telepítés

A leküldéses telepítés a portálon aktivált "[Replikáció engedélyezése](vmware-azure-enable-replication.md#enable-replication)" feladat szerves része. A védeni kívánt és a "Replikáció engedélyezése" aktiválásához szükséges virtuális gépek kiválasztása után a konfigurációs kiszolgáló lenyomja a mobilitási ügynököt a kiszolgálókra, telepíti az ügynököt és az ügynök teljes regisztrációját a konfigurációs kiszolgálóval. A művelet sikeres befejezéséhez

- Győződjön meg arról, hogy minden leküldéses telepítési [előfeltételeteljesül.](vmware-azure-install-mobility-service.md)
- Győződjön meg arról, hogy a kiszolgálók minden konfigurációja [a VMware és az Azure DR forgatókönyv támogatási mátrixa](vmware-physical-azure-support-matrix.md)alá tartozik.

A leküldéses telepítési munkafolyamat részleteit a következő szakaszok ismertetik.

### <a name="from-923-version-onwards"></a>[9.23-as verziótól](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

A mobilitási ügynök leküldéses telepítése során a következő lépéseket kell végrehajtani

1. Lenyomja az ügynököt a forrásgépre. Az ügynök forrásgépre másolása több környezeti hiba miatt sikertelen lehet. Tekintse meg [útmutatónkat](vmware-azure-troubleshoot-push-install.md) a leküldéses telepítési hibák elhárításához.
2. Miután az ügynököt sikeresen átmásolta a kiszolgálóra, a kiszolgáló előfeltételei ellenőrzéseket hajtanak végre a kiszolgálón. A telepítés sikertelen, ha egy vagy több [előfeltétel](vmware-physical-azure-support-matrix.md) nem teljesül. Ha minden előfeltétel teljesül, a telepítés aktiválódik.
3. Az Azure Site Recovery VSS-szolgáltató a mobilügynök telepítésének részeként települ a kiszolgálóra. Ez a szolgáltató alkalmazáskonzisztens pontok létrehozására szolgál. Ha a VSS-szolgáltató telepítése sikertelen, a lépés kimarad, és az ügynök telepítése folytatódik.
4. Ha az ügynök telepítése sikeres, de a VSS-szolgáltató telepítése sikertelen, akkor a feladat állapota "Figyelmeztetés" jelöléssel lesz ellátva. Ez nincs hatással az összeomlási konzisztenciapontok generálására.

    a. Alkalmazáskonzisztens pontok létrehozásához olvassa el a Site Recovery VSS-szolgáltató manuális telepítésének manuális befejezéséhez [vonatkozó útmutatásunkat.](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) </br>
    b.  Ha nem szeretné, hogy alkalmazáskonzisztens pontok jönnek létre, [módosítsa a replikációs házirendet az](vmware-azure-set-up-replication.md#create-a-policy) alkalmazáskonzisztens pontok kikapcsolására.

### <a name="before-922-versions"></a>9.22 verzió előtt

1. Lenyomja az ügynököt a forrásgépre. Az ügynök forrásgépre másolása több környezeti hiba miatt sikertelen lehet. Tekintse meg [útmutatónkat](vmware-azure-troubleshoot-push-install.md) a leküldéses telepítési hibák elhárításához.
2. Miután az ügynököt sikeresen átmásolta a kiszolgálóra, a kiszolgáló előfeltételei ellenőrzéseket hajtanak végre a kiszolgálón. A telepítés sikertelen, ha egy vagy több [előfeltétel](vmware-physical-azure-support-matrix.md) nem teljesül. Ha minden előfeltétel teljesül, a telepítés aktiválódik.
3. Az Azure Site Recovery VSS-szolgáltató a mobilügynök telepítésének részeként települ a kiszolgálóra. Ez a szolgáltató alkalmazáskonzisztens pontok létrehozására szolgál. Ha a VSS-szolgáltató telepítése sikertelen, akkor az ügynök telepítése sikertelen lesz. A mobilitási ügynök telepítésének sikertelensítése érdekében a [9.23-as](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) vagy újabb verzióval hozzon létre összeomlási konzisztens pontokat, és telepítse manuálisan a VSS-szolgáltatót.

## <a name="install-mobility-agent-through-ui"></a>Mobilitási ügynök telepítése a felhasználói felületen keresztül

### <a name="prerequisite"></a>Előfeltétel

- Győződjön meg arról, hogy a kiszolgálók minden konfigurációja [a VMware és az Azure DR forgatókönyv támogatási mátrixa](vmware-physical-azure-support-matrix.md)alá tartozik.
- [Keresse meg a telepítőt](#locate-installer-files) a kiszolgáló operációs rendszere alapján.

>[!IMPORTANT]
> Ha az Azure IaaS virtuális gép replikálása egyik Azure-régióból a másikba, ne használja ezt a módszert. Használja inkább a parancssori telepítési módszert.

1. Másolja a telepítőfájlt a számítógépre, és futtassa azt.
2. A **Telepítési beállításban**válassza **a Mobilitási szolgáltatás telepítése**lehetőséget.
3. Válassza ki a telepítési helyet > **Telepítés lehetőséget.**

    ![A Mobilitási szolgáltatás telepítési beállítási oldala](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. A telepítés figyelése a **Telepítés folyamata során**. A telepítés befejezése után válassza a **Folytatás a konfigurációs kiszolgálóhoz** lehetőséget a szolgáltatás regisztrálásához.

    ![Mobilitási szolgáltatás regisztrációs oldala](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. A **Konfigurációs kiszolgáló részletei területen**adja meg a konfigurált IP-címet és jelszót.

    ![Mobilitási szolgáltatás regisztrációs oldala](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. A regisztráció befejezéséhez válassza a **Regisztráció** lehetőséget.

    ![Mobilitási Szolgáltatás regisztrációjának záróoldala](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>A mobilitási ügynök telepítése a parancssorból

### <a name="prerequisite"></a>Előfeltétel

- Győződjön meg arról, hogy a kiszolgálók minden konfigurációja [a VMware és az Azure DR forgatókönyv támogatási mátrixa](vmware-physical-azure-support-matrix.md)alá tartozik.
- [Keresse meg a telepítőt](#locate-installer-files) a kiszolgáló operációs rendszere alapján.

### <a name="on-a-windows-machine"></a>Windows rendszerű számítógépen

- Másolja a telepítőt a védeni kívánt kiszolgálón található helyi mappába (például C:\Temp).

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Telepítse az alábbiak szerint:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Regisztrálja az ügynököt a konfigurációs kiszolgálón.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgent.exe /Role \<MS/MT> \</InstallLocation Telepítési hely> /Platform "VmWare" /Silent
Telepítési naplók | A %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log fájlban.
/Szerepkör | Kötelező telepítési paraméter. Itt adható meg, hogy a mobilitási szolgáltatást (MS) vagy a fő célt (MT) kell-e telepíteni.
/InstallLocation| Nem kötelező megadni. Megadja a Mobilitásszolgáltatás telepítési helyét (bármely mappát).
/Platform | Kötelező. Megadja azt a platformot, amelyre a Mobilszolgáltatás telepítve van. **VMware** vmware virtuális gépekhez/fizikai kiszolgálókhoz; **Azure** Azure-beli virtuális gépekhez.<br/><br/> Ha az Azure virtuális gépeket fizikai gépekként kezeli, adja meg a **VMware értéket.**
/Silent| Választható. Itt adható meg, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Ügynök konfigurációs naplói | A %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log fájlban.
/CSEndPoint | Kötelező paraméter. Megadja a konfigurációs kiszolgáló IP-címét. Használjon érvényes IP-címet.
/PassphraseFilePath |  Kötelező. A jelszó helye. Használjon érvényes UNC-t vagy helyi fájlelérési utat.

### <a name="on-a-linux-machine"></a>Linux gépen

1. Másolja a telepítőt a védeni kívánt kiszolgálón lévő helyi mappába (például /tmp). Terminálon futtassa a következő parancsokat:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Telepítse az alábbiak szerint:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. A telepítés befejezése után a Mobilszolgáltatás regisztrálva kell lennie a konfigurációs kiszolgálón. Futtassa a következő parancsot a Mobility Service regisztrálásához a konfigurációs kiszolgálón:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | ./install -d \<Telepítési hely \<> -r MS/MT> -v VmWare -q
-r | Kötelező telepítési paraméter. Itt adható meg, hogy a mobilitási szolgáltatást (MS) vagy a fő célt (MT) kell-e telepíteni.
-d | Nem kötelező megadni. A Mobilitási szolgáltatás telepítési helyét adja meg: /usr/local/ASR.
-v | Kötelező. Megadja azt a platformot, amelyre a Mobilszolgáltatás telepítve van. **VMware** vmware virtuális gépekhez/fizikai kiszolgálókhoz; **Azure** Azure-beli virtuális gépekhez.
-q | Választható. Itt adható meg, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP \<> -P PassphraseFilePath>
-i | Kötelező paraméter. Megadja a konfigurációs kiszolgáló IP-címét. Használjon érvényes IP-címet.
-P |  Kötelező. Annak a fájlnak a teljes fájlelérési útja, amelybe a jelszót menti a rendszer. Használjon bármilyen érvényes mappát.

## <a name="azure-virtual-machine-agent"></a>Azure virtuális gép ügynöke

- **Windows virtuális gépek:** A Mobility szolgáltatás 9.7.0.0-s verziójából az [Azure virtuálisgép-ügynököt](../virtual-machines/extensions/features-windows.md#azure-vm-agent) a Mobility szolgáltatás telepítője telepíti. Ez biztosítja, hogy ha a gép átadja a átadja a dedikálást az Azure-nak, az Azure virtuális gép megfelel az ügynök telepítési előfeltétele bármely virtuálisgép-bővítmény használatának.
- **Linux virtuális gépek:** A [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) manuálisan kell telepíteni az Azure virtuális gép feladatátvétel után.

## <a name="locate-installer-files"></a>Telepítőfájlok megkeresése

Nyissa meg a %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappát a konfigurációs kiszolgálón. Ellenőrizze, hogy melyik telepítőre van szüksége az operációs rendszer alapján. Az alábbi táblázat összefoglalja az egyes VMware virtuális gépek és fizikai kiszolgálóoperációs rendszerek telepítőfájljait. A [támogatott operációs rendszereket](vmware-physical-azure-support-matrix.md#replicated-machines) az indítás előtt áttekintheti.

**Telepítőfájl** | **Operációs rendszer (csak 64 bites)**
--- | ---
Microsoft-ASR\_\*UA\*Windows-kiadás.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*kiadás.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*kiadás.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*kiadás.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*kiadás.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*kiadás.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*kiadás.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS szerver
Microsoft-ASR_UA\*DEBIAN7-64\*kiadás.tar.gz | Debian, 7.
Microsoft-ASR_UA\*DEBIAN8-64\*kiadás.tar.gz | Debian 8

## <a name="next-steps"></a>További lépések

[Állítsa be a push installációt a Mobilitás szolgáltatáshoz.](vmware-azure-install-mobility-service.md)
