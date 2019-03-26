---
title: A vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure Site Recovery mobilitási szolgáltatás kapcsolatos |} A Microsoft Docs
description: Ismerje meg a mobilitási szolgáltatás ügynökének vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery szolgáltatással.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 6b06ee7710dedbf2283fc4e365b767aa57547e7c
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417819"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Tudnivalók a mobilitási szolgáltatás VMware virtuális gépek és fizikai kiszolgálókhoz

Amikor, vészhelyreállítás beállítása VMware virtuális gépek és fizikai kiszolgálók [Azure Site Recovery](site-recovery-overview.md), az egyes helyszíni VMware virtuális gép a Site Recovery mobilitási szolgáltatás és a fizikai kiszolgáló telepítését.  A mobilitási szolgáltatást kell-e a gépen adatírásokat, és továbbítja őket a Site Recovery is folyamatkiszolgálón. A mobilitási szolgáltatás a következő módszerekkel helyezhető üzembe:

- [Leküldéses telepítés](#push-installation): Site Recovery a mobilitási ügynök a kiszolgálón telepíti, ha engedélyezve van a védelem az Azure Portalon keresztül.
- Manuális telepítése: Manuálisan telepítheti a mobilitási szolgáltatás a minden gépen keresztül [felhasználói felület](#install-mobility-agent-through-ui) vagy [parancssor](#install-mobility-agent-through-command-prompt).
- [Automatikus központi telepítési](vmware-azure-mobility-install-configuration-mgr.md): A szoftvertelepítési eszközök például a System Center Configuration Manager telepítési automatizálható.

## <a name="anti-virus-on-replicated-machines"></a>A replikált gépek víruskereső

Ha replikálni kívánt gépek aktív víruskereső szoftvert, ellenőrizze, hogy a mobilitási szolgáltatás telepítési mappa kizárása víruskereső operations (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció megfelelően működik-e.

## <a name="push-installation"></a>Ügyfélleküldéses telepítés

Ügyfélleküldéses telepítés szerves részét képezi "[replikáció engedélyezése](vmware-azure-enable-replication.md#enable-replication)" feladat elindul a portálon. Miután kiválasztotta a virtuális gépek halmazát jelenti szeretné védeni, és aktiválja a "Replikáció engedélyezése", a konfigurációs kiszolgáló leküldi a mobilitási ügynök be a kiszolgálókra, az ügynök és a regisztráció elvégzése ügynök telepíti a konfigurációs kiszolgáló. Ez a művelet sikeres befejezését a

- Győződjön meg arról, hogy az összes leküldéssel [Előfeltételek](vmware-azure-install-mobility-service.md) teljesülnek-e.
- Győződjön meg arról, hogy az összes kiszolgáló-konfiguráció alá tartoznak [támogatási mátrix VMware – Azure Vészhelyreállítási forgatókönyvhöz](vmware-physical-azure-support-matrix.md).

Leküldéses telepítési munkafolyamat adatait a következő szakaszok leírása.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>A [9.23 verzió](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) és újabb verziók

A mobilitási ügynök leküldéses telepítése során az alábbi lépéseket kell végrehajtani

1. Leküldéses ügynök a forrásgépen. Másolás a forrásgép be az ügynököt több környezeti hiba miatt sikertelen lehet. Látogasson el [ügyfeleinket](vmware-azure-troubleshoot-push-install.md) leküldéses telepítési hibák elhárítása.
2. Ügynök sikeresen be másolása után az előfeltételeinek ellenőrzése történik a kiszolgálón. Telepítés sikertelen lesz, ha egy vagy több, a [Előfeltételek](vmware-physical-azure-support-matrix.md) nem teljesülnek. Ha minden előfeltétel teljesül, a telepítés akkor indul el.
3. Az Azure Site Recovery VSS provider telepítve van a kiszolgálón a mobilitási ügynök telepítésének részeként. Ez a szolgáltató alkalmazás konzisztens pontok létrehozására szolgál. VSS-szolgáltató telepítése nem sikerül, ha ez a lépés kimarad, és továbbra is az ügynök telepítése.
4. Ha az ügynök telepítése sikeresen befejeződik, de a VSS-szolgáltató telepítése sikertelen lesz, majd feladat állapota – "Figyelmeztetés" van megjelölve. Ez nincs hatással az összeomlási konzisztencia pontok létrehozása.
    a. Alkalmazás konzisztens pontokat létrehozni, tekintse meg [ügyfeleinket](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) manuálisan a Site Recovery VSS-szolgáltató telepítésének befejezéséhez.
    b.  Ha nem szeretne alkalmazás konzisztens pontot kell létrehozni, [módosítani a replikációs szabályzat](vmware-azure-set-up-replication.md#create-a-policy) tiltsa le az alkalmazás konzisztens pontokat.

### <a name="before-922-versions"></a>Mielőtt 9.22 verziók

1. Leküldéses ügynök a forrásgépen. Másolás a forrásgép be az ügynököt több környezeti hiba miatt sikertelen lehet. Látogasson el [ügyfeleinket](vmware-azure-troubleshoot-push-install.md) leküldéses telepítési hibák elhárítása.
2. Ügynök sikeresen be másolása után az előfeltételeinek ellenőrzése történik a kiszolgálón. Telepítés sikertelen lesz, ha egy vagy több, a [Előfeltételek](vmware-physical-azure-support-matrix.md) nem teljesülnek. Ha minden előfeltétel teljesül, a telepítés akkor indul el.
3. Az Azure Site Recovery VSS provider telepítve van a kiszolgálón a mobilitási ügynök telepítésének részeként. Ez a szolgáltató alkalmazás konzisztens pontok létrehozására szolgál. Ha a VSS-szolgáltató telepítése nem sikerül, majd az ügynök telepítése sikertelen lesz. Sikertelen a mobilitási ügynök telepítésének elkerülése érdekében használja [9.23 verzió](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) vagy újabb összeomlás-konzisztens pontokat létrehozni, és manuálisan telepíteni a VSS-szolgáltatót.

## <a name="install-mobility-agent-through-ui"></a>Telepítse a mobilitási ügynök a felhasználói felületen

### <a name="prerequisite"></a>Előfeltétel

- Győződjön meg arról, hogy az összes kiszolgáló-konfiguráció alá tartoznak [támogatási mátrix VMware – Azure Vészhelyreállítási forgatókönyvhöz](vmware-physical-azure-support-matrix.md).
- [Keresse meg a telepítő](#locate-installer-files) annak a kiszolgálónak az operációs rendszer alapján.

>[!IMPORTANT]
> Ha Azure IaaS virtuális Gépen egy Azure-régióból a másikba replikál, ne használja ezt a módszert. A parancssor alapú telepítési módszer használata.

1. Másolja a telepítőfájlt a gépre, és futtassa azt.
2. A **telepítési lehetőség**válassza **telepíteni a mobilitási szolgáltatást**.
3. A telepítés helyének kiválasztása > **telepítése**.

    ![A mobilitási szolgáltatás telepítési lehetőség lapja](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. A telepítés monitorozása **telepítési folyamat**. A telepítés befejezése után jelölje ki a **lépjen tovább a konfigurációs** a szolgáltatás regisztrálja a konfigurációs kiszolgálóval.

    ![A mobilitási szolgáltatás való regisztrációhoz](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. a **konfigurációs kiszolgáló részletei**, adja meg az IP-cím és a beállított jelszót.  

    ![A mobilitási szolgáltatás való regisztrációhoz](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Válassza ki **regisztrálása** a regisztráció befejezéséhez.

    ![A mobilitási szolgáltatás regisztrációs utolsó oldal](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>A mobilitási ügynököt a parancssor használatával

### <a name="prerequisite"></a>Előfeltétel

- Győződjön meg arról, hogy az összes kiszolgáló-konfiguráció alá tartoznak [támogatási mátrix VMware – Azure Vészhelyreállítási forgatókönyvhöz](vmware-physical-azure-support-matrix.md).
- [Keresse meg a telepítő](#locate-installer-files) annak a kiszolgálónak az operációs rendszer alapján.

### <a name="on-a-windows-machine"></a>Egy Windows-gépen

- Másolja a telepítőt egy helyi mappába (például C:\Temp) a kiszolgálón, amely számára védelmet kíván.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- A következőképpen telepítheti:

    ``` 
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- A konfigurációs kiszolgálót regisztrálja az ügynököt.

    ``` 
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
Telepítési naplók | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Kötelező telepítés paraméter. Meghatározza, hogy a mobilitási szolgáltatás (MS) vagy a fő célkiszolgálót (MT) kell telepíteni.
/InstallLocation| Nem kötelező paraméter. Itt adhatja meg a mobilitási szolgáltatás telepítési helyét (bármely mappa).
/Platform | Kötelező. Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. **VMware** VMware virtuális gépek/fizikai kiszolgálók; **Azure** Azure virtuális gépek számára. 
/Silent| Választható. Megadja, hogy a telepítő futtatásához csendes módban.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Az ügynök konfigurációs naplók | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
/PassphraseFilePath |  Kötelező. A hozzáférési kódot helye. Bármely érvényes UNC vagy helyi fájl elérési útját használja.

### <a name="on-a-linux-machine"></a>Linux rendszerű gépen

1. Másolja a telepítőt egy helyi mappába (például ügynökszámítógépen) a kiszolgálón, amely számára védelmet kíván. A terminálban futtassa a következő parancsokat:

    ```
    cd /tmp ;
    tar -xvzf Microsoft-ASR_UA*release.tar.gz
    ```

2. A következőképpen telepítheti:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Miután a telepítés befejeződött, a mobilitási szolgáltatás regisztrálva kell lenniük a konfigurációs kiszolgálón. Futtassa a konfigurációs kiszolgálót regisztrálja a mobilitási szolgáltatás a következő parancsot:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | . / install -d <Install Location> - r < MS|Fő Célkiszolgáló > - v VmWare - q
-r | Kötelező telepítés paraméter. Meghatározza, hogy a mobilitási szolgáltatás (MS) vagy a fő célkiszolgálót (MT) kell telepíteni.
-d | Nem kötelező paraméter. Adja meg a mobilitási szolgáltatás telepítésének helye: /usr/local/ASR.
-v | Kötelező. Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. **VMware** VMware virtuális gépek/fizikai kiszolgálók; **Azure** Azure virtuális gépek számára. 
-q | Választható. Megadja, hogy a telepítő futtatásához csendes módban.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
-P |  Kötelező. Teljes elérési útja, amelyben a jelszót a rendszer menti a fájlt. Bármely érvényes mappát használja.

## <a name="azure-virtual-machine-agent"></a>Az Azure virtuálisgép-ügynök

- **Windows virtuális gépek**: A mobilitási szolgáltatás 9.7.0.0 verziójáról a [Azure Virtuálisgép-ügynök](../virtual-machines/extensions/features-windows.md#azure-vm-agent) a mobilitási szolgáltatás telepítőjének van telepítve. Ez biztosítja, hogy a gép átadja a feladatokat az Azure-ba, amikor az Azure virtuális gép megfelel-e az ügynök telepítése minden olyan Vm-bővítmény használata a előfeltételeit.
- **Linux rendszerű virtuális gépek**: A [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) manuálisan kell telepíteni az Azure virtuális gépen a feladatátvételt követően.

## <a name="locate-installer-files"></a>Keresse meg a installer-fájlok

Nyissa meg a konfigurációs kiszolgálón %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappát. Ellenőrizze, melyik telepítő szükséges operációs rendszer alapján. A következő táblázat összefoglalja a telepítőfájlokat, minden a VMware virtuális gépek és fizikai kiszolgálók operációs rendszer. Áttekintheti [támogatott operációs rendszerek](vmware-physical-azure-support-matrix.md#replicated-machines) megkezdése előtt.

**Telepítőfájl** | **Operációs rendszer (csak 64 bites verzió esetén)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | A Windows Server 2016-ban; A Windows Server 2012 R2; A Windows Server 2012; A Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2 SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5-ös
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | 14.04-es Ubuntu Linux
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>További lépések

[Állítsa be a mobilitási szolgáltatás leküldéses telepítése](vmware-azure-install-mobility-service.md).
