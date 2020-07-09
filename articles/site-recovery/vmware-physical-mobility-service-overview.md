---
title: Tudnivalók a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási mobilitási szolgáltatásáról Azure Site Recovery-mel | Microsoft Docs
description: Ismerje meg a mobilitási szolgáltatás ügynökét a VMware virtuális gépek és fizikai kiszolgálók Azure-beli vész-helyreállításához a Azure Site Recovery szolgáltatás használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: d73e2776d0d9c86fe0331f9804bfeade3f1de676
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131792"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Tudnivalók a VMware virtuális gépek és a fizikai kiszolgálók mobilitási szolgáltatásáról

A VMware virtuális gépek (VM) és a fizikai kiszolgálók [Azure site Recovery](site-recovery-overview.md)használatával történő vész-helyreállításának beállításakor a site Recovery mobilitási szolgáltatást minden helyszíni VMWare virtuális gépen és fizikai kiszolgálón telepítenie kell. A mobilitási szolgáltatás rögzíti az adatírásokat a gépen, és továbbítja azokat a Site Recovery Process Servernek. A mobilitási szolgáltatást a mobilitási szolgáltatás ügynökének szoftvere telepíti, amelyet a következő módszerek használatával telepíthet:

- [Leküldéses telepítés](#push-installation): Ha a védelem engedélyezve van a Azure Portalon keresztül, site Recovery telepíti a mobilitási szolgáltatást a kiszolgálón.
- Manuális telepítés: a mobilitási szolgáltatást manuálisan is telepítheti minden gépen a [felhasználói felületen](#install-the-mobility-service-using-ui) vagy a [parancssorban](#install-the-mobility-service-using-command-prompt).
- [Automatikus üzembe helyezés](vmware-azure-mobility-install-configuration-mgr.md): a mobilitási szolgáltatás telepítésének automatizálása a szoftver központi telepítési eszközeivel, például a Configuration Manager használatával.

> [!NOTE]
> A mobilitási szolgáltatás körülbelül 6%-10%-os memóriát használ a VMware virtuális gépekhez vagy fizikai gépekhez.

## <a name="antivirus-on-replicated-machines"></a>Víruskereső a replikált gépeken

Ha a replikálni kívánt gépek víruskereső szoftvert futtatnak, zárja ki a mobilitási szolgáltatás telepítési mappáját a _C:\ProgramData\ASR\agent_ . Ez a kizárás biztosítja, hogy a replikáció a várt módon fog működni.

## <a name="push-installation"></a>Leküldéses telepítés

A leküldéses telepítés a Azure Portal futtatott feladatok szerves része a [replikáció engedélyezéséhez](vmware-azure-enable-replication.md#enable-replication). Miután kiválasztotta a védelemmel ellátni kívánt virtuális gépeket, és engedélyezi a replikációt, a konfigurációs kiszolgáló leküldi a mobilitási szolgáltatás ügynökét a kiszolgálókra, telepíti az ügynököt, és befejezi az ügynök regisztrációját a konfigurációs kiszolgálóval.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a leküldéses telepítés összes [előfeltétele](vmware-azure-install-mobility-service.md) teljesül.
- Győződjön meg arról, hogy az összes kiszolgálói konfiguráció megfelel a [VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítására szolgáló támogatási mátrix](vmware-physical-azure-support-matrix.md)feltételeinek.

A leküldéses telepítési munkafolyamatot az alábbi szakaszokban ismertetjük:

### <a name="mobility-service-agent-version-923-and-higher"></a>A mobilitási szolgáltatás ügynökének 9,23-es és újabb verziója

További információ a 9,23-es verzióról: a 35-es [kumulatív frissítés Azure site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

A mobilitási szolgáltatás leküldéses telepítése során a következő lépéseket kell végrehajtani:

1. Az ügynököt a rendszer leküldi a forrásoldali gépre. Az ügynöknek a forrásoldali gépre való másolása több környezeti hiba miatt sikertelen lehet. A leküldéses telepítési hibák elhárításához tekintse meg [a útmutatást](vmware-azure-troubleshoot-push-install.md) .
1. Miután az ügynököt sikeresen átmásolta a kiszolgálóra, az Előfeltételek ellenőrzése a kiszolgálón történik.
   - Ha minden előfeltétel teljesül, a telepítés megkezdődik.
   - A telepítés sikertelen lesz, ha egy vagy több [előfeltétel](vmware-physical-azure-support-matrix.md) nem teljesül.
1. Az ügynök telepítésének részeként telepítve van a Azure Site Recovery Kötet árnyékmásolata szolgáltatás (VSS) szolgáltatója. A VSS-szolgáltató az alkalmazás-konzisztens helyreállítási pontok előállítására szolgál. Ha a VSS-szolgáltató telepítése nem sikerül, a rendszer kihagyja ezt a lépést, és folytatja az ügynök telepítését.
1. Ha az ügynök telepítése sikeres, de a VSS-szolgáltató telepítése meghiúsul, akkor a feladatok állapota **figyelmeztetésként**van megjelölve. Ez nem befolyásolja az összeomlás-konzisztens helyreállítási pontok létrehozását.

    - Az alkalmazással konzisztens helyreállítási pontok létrehozásához tekintse meg a Site Recovery VSS-szolgáltató manuális telepítésének befejezésére vonatkozó [útmutatást](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) .
    - Ha nem szeretné, hogy az alkalmazással konzisztens helyreállítási pontok legyenek létrehozva, [módosítsa a replikációs házirendet](vmware-azure-set-up-replication.md#create-a-policy) az alkalmazás-konzisztens helyreállítási pontok kikapcsolásához.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobilitási szolgáltatás ügynökének 9,22-es vagy újabb verziója

1. Az ügynököt a rendszer leküldi a forrásoldali gépre. Az ügynöknek a forrásoldali gépre való másolása több környezeti hiba miatt sikertelen lehet. A leküldéses telepítési hibák elhárításához tekintse meg a [útmutatást](vmware-azure-troubleshoot-push-install.md) .
1. Miután az ügynököt sikeresen átmásolta a kiszolgálóra, az Előfeltételek ellenőrzése a kiszolgálón történik.
   - Ha minden előfeltétel teljesül, a telepítés megkezdődik.
   - A telepítés sikertelen lesz, ha egy vagy több [előfeltétel](vmware-physical-azure-support-matrix.md) nem teljesül.

1. Az ügynök telepítésének részeként telepítve van a Azure Site Recovery Kötet árnyékmásolata szolgáltatás (VSS) szolgáltatója. A VSS-szolgáltató az alkalmazás-konzisztens helyreállítási pontok előállítására szolgál.
   - Ha a VSS-szolgáltató telepítése sikertelen, az ügynök telepítése sikertelen lesz. Az ügynök telepítésének elmulasztása érdekében a 9,23-es vagy újabb [verzióval](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) összeomlás-konzisztens helyreállítási pontokat állítson elő, és végezze el a VSS-szolgáltató manuális telepítését.

## <a name="install-the-mobility-service-using-ui"></a>A mobilitási szolgáltatás telepítése felhasználói felület használatával

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az összes kiszolgálói konfiguráció megfelel a [VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítására szolgáló támogatási mátrix](vmware-physical-azure-support-matrix.md)feltételeinek.
- Keresse meg a kiszolgáló operációs rendszerének [telepítőjét](#locate-installer-files) .

>[!IMPORTANT]
> Ne használja a felhasználói felület telepítési módszerét, ha egy Azure-beli infrastruktúra-szolgáltatás (IaaS) virtuális gépet replikál egy Azure-régióból egy másikba. Használja a [parancssor](#install-the-mobility-service-using-command-prompt) telepítését.

1. Másolja a telepítési fájlt a gépre, és futtassa.
1. A **telepítés lehetőségnél**válassza a **mobilitási szolgáltatás telepítése**lehetőséget.
1. Válassza ki a telepítési helyet, és válassza a **telepítés**lehetőséget.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Mobilitási szolgáltatás telepítési lehetőség lapja.":::

1. A telepítés figyelése a **telepítési folyamatban**. A telepítés befejezése után válassza a **Folytatás a konfigurációval** lehetőséget a szolgáltatás konfigurációs kiszolgálóval való regisztrálásához.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Mobilitási szolgáltatás regisztrációs lapja.":::

1. A **konfigurációs kiszolgáló részletei**területen válassza ki a konfigurált IP-címet és jelszót.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Mobilitási szolgáltatás regisztrációs lapja.":::

1. A regisztráció befejezéséhez válassza a **regisztráció** lehetőséget.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Mobilitási szolgáltatás regisztrációjának utolsó lapja.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>A mobilitási szolgáltatás telepítése parancssor használatával

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az összes kiszolgálói konfiguráció megfelel a [VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítására szolgáló támogatási mátrix](vmware-physical-azure-support-matrix.md)feltételeinek.
- Keresse meg a kiszolgáló operációs rendszerének [telepítőjét](#locate-installer-files) .

### <a name="windows-machine"></a>Windows rendszerű gép

- A parancssorból futtassa a következő parancsokat a telepítő helyi mappába (például _C:\Temp_) való másolásához a védelemmel ellátni kívánt kiszolgálón. Cserélje le a telepítő fájlnevét a tényleges fájl nevére.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Futtassa ezt a parancsot az ügynök telepítéséhez.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- A parancsok futtatásával regisztrálja az ügynököt a konfigurációs kiszolgálóval.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Telepítési beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Telepítési naplók | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Kötelező telepítési paraméter. Megadja, hogy kell-e telepíteni a mobilitási szolgáltatást (MS) vagy a fő célt (MT).
`/InstallLocation`| Nem kötelező megadni. Megadja a mobilitási szolgáltatás telepítési helyét (bármely mappa).
`/Platform` | Kötelező. Megadja azt a platformot, amelyen a mobilitási szolgáltatás telepítve van: <br/> **VMware** VMWare virtuális gépekhez/fizikai kiszolgálókhoz. <br/> **Azure** Azure-beli virtuális gépekhez.<br/><br/> Ha fizikai gépekként kezeli az Azure-beli virtuális gépeket, a **VMware**-et kell megadnia.
`/Silent`| Választható. Megadja, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Ügynök konfigurációs naplói | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Kötelező paraméter. `<CSIP>`a konfigurációs kiszolgáló IP-címét adja meg. Bármilyen érvényes IP-címet használjon.
`/PassphraseFilePath` |  Kötelező. A jelszó helye. Használjon bármely érvényes UNC-vagy helyi elérési útvonalat.

### <a name="linux-machine"></a>Linux rendszerű gép

1. Egy terminál-munkamenetből másolja a telepítőt egy helyi mappába, például a _/tmp_ a védelemmel ellátni kívánt kiszolgálón. Cserélje le a telepítő fájlnevét a Linux-disztribúció tényleges fájljára, majd futtassa a parancsokat.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. A telepítés a következőképpen történik:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. A telepítés befejezése után a mobilitási szolgáltatást regisztrálni kell a konfigurációs kiszolgálón. Futtassa a következő parancsot a mobilitási szolgáltatás a konfigurációs kiszolgálóval való regisztrálásához.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Telepítési beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Kötelező telepítési paraméter. Megadja, hogy kell-e telepíteni a mobilitási szolgáltatást (MS) vagy a fő célt (MT).
`-d` | Nem kötelező megadni. Megadja a mobilitási szolgáltatás telepítési helyét: `/usr/local/ASR` .
`-v` | Kötelező. Meghatározza azt a platformot, amelyen a mobilitási szolgáltatás telepítve van. <br/> **VMware** VMWare virtuális gépekhez/fizikai kiszolgálókhoz. <br/> **Azure** Azure-beli virtuális gépekhez.
`-q` | Választható. Megadja, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Kötelező paraméter. `<CSIP>`a konfigurációs kiszolgáló IP-címét adja meg. Bármilyen érvényes IP-címet használjon.
`-P` |  Kötelező. A fájl teljes elérési útja, amelyben a rendszer menti a jelszót. Bármilyen érvényes mappát használjon.

## <a name="azure-virtual-machine-agent"></a>Azure-beli virtuálisgép-ügynök

- **Windows rendszerű virtuális gépek**: a mobilitási szolgáltatás telepítője az Azure virtuálisgép- [ügynököt](../virtual-machines/extensions/features-windows.md#azure-vm-agent) a mobilitási szolgáltatás 9.7.0.0 telepíti. Ez biztosítja, hogy amikor a gép feladatátvételt hajt végre az Azure-ba, az Azure-beli virtuális gép megfelel a virtuálisgép-bővítmények használatára vonatkozó előfeltételnek.
- **Linux rendszerű virtuális gépek**: a [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) manuálisan kell telepíteni az Azure-beli virtuális gépen a feladatátvételt követően.

## <a name="locate-installer-files"></a>Telepítőfájlok megkeresése

A konfigurációs kiszolgálón lépjen a _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_mappába. Győződjön meg arról, hogy melyik telepítőre van szükség az operációs rendszer alapján. A következő táblázat összefoglalja az egyes VMware-alapú virtuális gépek és a fizikai kiszolgálói operációs rendszerek telepítési fájljait. Mielőtt elkezdené, tekintse át a [támogatott operációs rendszereket](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> A fájlnevek a következő táblázatban látható szintaxist használják a valós _version_ értékekhez _date_ tartozó helyőrzőként. A tényleges fájlnevek az alábbi példákhoz hasonlóan néznek ki:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Telepítőfájl | Operációs rendszer (csak 64-bit)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> SP2 és SP3 szervizcsomagot tartalmaz.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | 11 SP4 SUSE Linux Enterprise Server
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6,4 </br> Oracle Enterprise Linux 6,5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14,04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16,04 LTS-kiszolgáló
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Következő lépések

[A mobilitási szolgáltatás leküldéses telepítésének beállítása](vmware-azure-install-mobility-service.md).
