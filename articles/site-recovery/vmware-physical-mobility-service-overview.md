---
title: A Virtuálisgép-automaták és a fizikai kiszolgálók vész-helyreállítási mobilitási szolgáltatása az Azure Site Recovery szolgáltatással | Microsoft dokumentumok
description: Ismerje meg a Mobility service agent a VMware virtuális gépek és a fizikai kiszolgálók az Azure-ba az Azure Site Recovery szolgáltatás használatával a Mobilitási szolgáltatás ügynöke.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259796"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>A VMware virtuális gépek és fizikai kiszolgálók mobilitási szolgáltatása

Ha az [Azure Site Recovery](site-recovery-overview.md)használatával vészhelyreállítást állít be a VMware virtuális gépekhez (VM) és a fizikai kiszolgálókhoz, a Site Recovery Mobility szolgáltatást minden helyszíni VMware virtuális gépen és fizikai kiszolgálón telepíti. A Mobilitás szolgáltatás rögzíti az adatok írásait a számítógépen, és továbbítja azokat a Site Recovery folyamatkiszolgálóra. A Mobilitás szolgáltatást a Mobility service agent szoftver telepíti, amelyet a következő módszerekkel telepíthet:

- [Leküldéses telepítés:](#push-installation)Ha a védelem engedélyezve van az Azure Portalon keresztül, a Site Recovery telepíti a mobilrendszer-kiszolgálón.
- Kézi telepítés: A Mobility szolgáltatást manuálisan telepítheti minden gépre a [felhasználói felületen (UI)](#install-the-mobility-service-using-ui) vagy a [parancssoron](#install-the-mobility-service-using-command-prompt)keresztül.
- [Automatikus telepítés](vmware-azure-mobility-install-configuration-mgr.md): A Mobility szolgáltatás telepítését szoftvertelepítési eszközökkel, például a Configuration Manager-rel automatizálhatja.

> [!NOTE]
> A Mobility szolgáltatás a memória körülbelül 6–10%-át használja a vmware virtuális gépek vagy fizikai gépek forrásgépein.

## <a name="antivirus-on-replicated-machines"></a>Víruskereső replikált gépeken

Ha a replikálni kívánt gépek víruskereső szoftvert futtatnak, zárja ki a Mobilitás szolgáltatás Telepítési mappáját _C:\ProgramData\ASR\agent_ a víruskereső műveletekből. Ez a kizárás biztosítja, hogy a replikáció a várt módon működjön.

## <a name="push-installation"></a>Push telepítés

A leküldéses telepítés az Azure Portalról a [replikáció engedélyezéséhez](vmware-azure-enable-replication.md#enable-replication)futó feladat szerves része. A védeni kívánt és engedélyezni kívánt virtuális gépek készletének kiválasztása után a konfigurációs kiszolgáló lelöki a mobilitási szolgáltatási ügynököt a kiszolgálókra, telepíti az ügynököt, és befejezi az ügynök regisztrációját a konfigurációs kiszolgálóval.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy minden leküldéses telepítési [előfeltételeteljesül.](vmware-azure-install-mobility-service.md)
- Győződjön meg arról, hogy minden kiszolgálókonfiguráció megfelel a [virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítási támogatási mátrixának feltételeinek.](vmware-physical-azure-support-matrix.md)

A leküldéses telepítési munkafolyamat leírása a következő szakaszokban található:

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobilitási szolgáltatásügynök 9.23-as vagy újabb verzió

A 9.23-as verzióról további információt az [Azure Site Recovery 35-ös összegző frissítőcsomagja című](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)témakörben talál.

A Mobilitás szolgáltatás leküldéses telepítése során a következő lépéseket hajtjuk végre:

1. Az ügynök a forrásgépre kerül. Az ügynök másolása a forrásgépre több környezeti hiba miatt sikertelen lehet. Tekintse meg [útmutatónkat](vmware-azure-troubleshoot-push-install.md) a leküldéses telepítési hibák elhárításához.
1. Miután az ügynököt sikeresen átmásolta a kiszolgálóra, a rendszer előfeltétel-ellenőrzést hajt végre a kiszolgálón.
   - Ha minden előfeltétel teljesül, a telepítés megkezdődik.
   - A telepítés sikertelen, ha egy vagy több [előfeltétel](vmware-physical-azure-support-matrix.md) nem teljesül.
1. Az ügynök telepítésének részeként a kötet árnyékmásolata szolgáltatás (VSS) szolgáltató az Azure Site Recovery telepítve van. A VSS-szolgáltató alkalmazáskonzisztens helyreállítási pontok létrehozására szolgál. Ha a VSS-szolgáltató telepítése sikertelen, a rendszer kihagyja ezt a lépést, és az ügynök telepítése folytatódik.
1. Ha az ügynök telepítése sikeres, de a VSS-szolgáltató telepítése sikertelen, akkor a feladat állapota **Figyelmeztetés**ként lesz megjelölve. Ez nincs hatással az összeomlás-konzisztens helyreállítási pont generálására.

    - Alkalmazáskonzisztens helyreállítási pontok létrehozásához olvassa el a Site Recovery VSS-szolgáltató manuális telepítésének befejezéséhez [adott útmutatónkat.](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)
    - Ha nem szeretne alkalmazáskonzisztens helyreállítási pontokat létrehozni, [módosítsa a replikációs házirendet az](vmware-azure-set-up-replication.md#create-a-policy) alkalmazáskonzisztens helyreállítási pontok kikapcsolására.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobilitási szolgáltatás ügynök 9.22-es és újabb verziója

1. Az ügynök a forrásgépre kerül. Az ügynök másolása a forrásgépre több környezeti hiba miatt sikertelen lehet. Tekintse meg [útmutatónkat](vmware-azure-troubleshoot-push-install.md) a leküldéses telepítési hibák elhárításához.
1. Miután az ügynököt sikeresen átmásolta a kiszolgálóra, a rendszer előfeltétel-ellenőrzést hajt végre a kiszolgálón.
   - Ha minden előfeltétel teljesül, a telepítés megkezdődik.
   - A telepítés sikertelen, ha egy vagy több [előfeltétel](vmware-physical-azure-support-matrix.md) nem teljesül.

1. Az ügynök telepítésének részeként a kötet árnyékmásolata szolgáltatás (VSS) szolgáltató az Azure Site Recovery telepítve van. A VSS-szolgáltató alkalmazáskonzisztens helyreállítási pontok létrehozására szolgál.
   - Ha a VSS-szolgáltató telepítése sikertelen, az ügynök telepítése sikertelen lesz. Az ügynök telepítésének meghibásodása elkerülése érdekében a [9.23-as](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) vagy újabb verzióval hozza létre az összeomlás-konzisztens helyreállítási pontokat, és végezze el a VSS-szolgáltató manuális telepítését.

## <a name="install-the-mobility-service-using-ui"></a>A Mobilitás szolgáltatás telepítése a felhasználói felület használatával

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy minden kiszolgálókonfiguráció megfelel a [virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítási támogatási mátrixának feltételeinek.](vmware-physical-azure-support-matrix.md)
- [Keresse meg a](#locate-installer-files) kiszolgáló operációs rendszerének telepítőját.

>[!IMPORTANT]
> Ne használja a felhasználói felület telepítési módszerét, ha egy Azure-infrastruktúrát szolgáltatásként (IaaS) szolgáltatásként (IaaS) replikál az egyik Azure-régióból a másikba. Használja a [parancssori](#install-the-mobility-service-using-command-prompt) telepítést.

1. Másolja a telepítőfájlt a számítógépre, és futtassa azt.
1. A **Telepítési beállításban**válassza **a Mobilitási szolgáltatás telepítése**lehetőséget.
1. Válassza ki a telepítési helyet, és válassza a **Telepítés**lehetőséget.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="A mobilitási szolgáltatás telepítési beállításoldalának.":::

1. A telepítés figyelése a **Telepítés folyamata során**. A telepítés befejezése után válassza a **Folytatás a konfigurációs kiszolgálóhoz** lehetőséget a szolgáltatás regisztrálásához.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Mobilitási szolgáltatás regisztrációs oldalán.":::

1. A **Konfigurációs kiszolgáló részletei területen**adja meg a konfigurált IP-címet és jelszót.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Mobilitási szolgáltatás regisztrációs oldalán.":::

1. A regisztráció befejezéséhez válassza a **Regisztráció** lehetőséget.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Mobilitási szolgáltatás regisztrációs záró oldalán.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>A Mobilitás szolgáltatás telepítése parancssorból

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy minden kiszolgálókonfiguráció megfelel a [virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítási támogatási mátrixának feltételeinek.](vmware-physical-azure-support-matrix.md)
- [Keresse meg a](#locate-installer-files) kiszolgáló operációs rendszerének telepítőját.

### <a name="windows-machine"></a>Windows-gép

- A parancssorból futtassa a következő parancsokat a telepítő helyi mappába (például _C:\Temp_) történő másolásához a védeni kívánt kiszolgálón. Cserélje le a telepítő fájlnevét a tényleges fájlnévre.

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

- Futtassa ezeket a parancsokat az ügynök regisztrálásához a konfigurációs kiszolgálón.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Telepítési beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Telepítési naplók | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Kötelező telepítési paraméter. Itt adható meg, hogy a mobilitási szolgáltatást (MS) vagy a fő célt (MT) kell-e telepíteni.
`/InstallLocation`| Nem kötelező megadni. Megadja a Mobilitásszolgáltatás telepítési helyét (bármely mappát).
`/Platform` | Kötelező. Megadja azt a platformot, amelyre a Mobilitás szolgáltatás telepítve van: <br/> **VMware** vmware virtuális gépekhez/fizikai kiszolgálókhoz. <br/> **Azure** Azure-beli virtuális gépekhez.<br/><br/> Ha az Azure virtuális gépeket fizikai gépekként kezeli, adja meg a **VMware értéket.**
`/Silent`| Választható. Itt adható meg, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Ügynök konfigurációs naplói | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Kötelező paraméter. `<CSIP>`a konfigurációs kiszolgáló IP-címét adja meg. Használjon érvényes IP-címet.
`/PassphraseFilePath` |  Kötelező. A jelszó helye. Használjon érvényes UNC-t vagy helyi fájlelérési utat.

### <a name="linux-machine"></a>Linux gép

1. Terminálmunkamenetből másolja a telepítőt egy helyi mappába, például a védeni kívánt kiszolgálón a _/tmp_ mappába. Cserélje le a telepítő fájlnevét a Linux-disztribúció tényleges fájlnevére, majd futtassa a parancsokat.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Telepítse az alábbiak szerint:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. A telepítés befejezése után a Mobilitás szolgáltatást regisztrálni kell a konfigurációs kiszolgálón. Futtassa a következő parancsot a Mobilitás szolgáltatás regisztrálásához a konfigurációs kiszolgálóval.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Telepítési beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Kötelező telepítési paraméter. Itt adható meg, hogy a mobilitási szolgáltatást (MS) vagy a fő célt (MT) kell-e telepíteni.
`-d` | Nem kötelező megadni. A Mobilitási szolgáltatás telepítési `/usr/local/ASR`helyét adja meg: . .
`-v` | Kötelező. Megadja azt a platformot, amelyre a Mobilitás szolgáltatás telepítve van. <br/> **VMware** vmware virtuális gépekhez/fizikai kiszolgálókhoz. <br/> **Azure** Azure-beli virtuális gépekhez.
`-q` | Választható. Itt adható meg, hogy a telepítőt csendes módban kell-e futtatni.

#### <a name="registration-settings"></a>Regisztrációs beállítások

Beállítás | Részletek
--- | ---
Szintaxis | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Kötelező paraméter. `<CSIP>`a konfigurációs kiszolgáló IP-címét adja meg. Használjon érvényes IP-címet.
`-P` |  Kötelező. Annak a fájlnak a teljes fájlelérési útja, amelybe a jelszót menti a rendszer. Használjon bármilyen érvényes mappát.

## <a name="azure-virtual-machine-agent"></a>Azure virtuális gép ügynöke

- **Windows virtuális gépek:** A Mobility szolgáltatás 9.7.0.0-s verziójából az [Azure virtuálisgép-ügynököt](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) a Mobility szolgáltatás telepítője telepíti. Ez biztosítja, hogy ha a gép átadja a átadja a dedikálást az Azure-nak, az Azure virtuális gép megfelel az ügynök telepítési előfeltétele bármely virtuálisgép-bővítmény használatának.
- **Linux virtuális gépek:** A [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) manuálisan kell telepíteni az Azure virtuális gép feladatátvétel után.

## <a name="locate-installer-files"></a>Telepítőfájlok megkeresése

A konfigurációs kiszolgálón nyissa meg a _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappát._ Ellenőrizze, hogy melyik telepítőre van szüksége az operációs rendszer alapján. Az alábbi táblázat összefoglalja az egyes VMware virtuális gépek és fizikai kiszolgálóoperációs rendszerek telepítőfájljait. Mielőtt elkezdené, áttekintheti a [támogatott operációs rendszereket.](vmware-physical-azure-support-matrix.md#replicated-machines)

> [!NOTE]
> A fájlnevek az alábbi táblázatban látható szintaxist használják, _amelynek verziója_ és _dátuma_ a valós értékek helyőrzői. A tényleges fájlnevek az alábbi példákhoz hasonlóan fognak kinézni:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Telepítőfájl | Operációs rendszer (csak 64 bites)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS, 6.
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Tartalmazza az SP2 és SP3 szervizcsomagot.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS szerver
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian, 7.
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>További lépések

[Állítsa be a push installációt a Mobilitás szolgáltatáshoz.](vmware-azure-install-mobility-service.md)
