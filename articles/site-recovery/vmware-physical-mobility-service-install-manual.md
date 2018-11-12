---
title: Telepítse a mobilitási szolgáltatást manuálisan a VMware virtuális gépek és fizikai kiszolgálók vészhelyreállítása az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerje meg, hogyan kell telepíteni a mobilitási szolgáltatás ügynökének vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: dfed0209131379843b97ff8050c2f2ba7294537d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019319"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>A mobilitási szolgáltatás manuális telepítése a VMware virtuális gépek és fizikai kiszolgálókon

Amikor, vészhelyreállítás beállítása VMware virtuális gépek és fizikai kiszolgálók [Azure Site Recovery](site-recovery-overview.md), telepíti a [Site Recovery mobilitási szolgáltatás](vmware-physical-mobility-service-overview.md) minden helyszíni VMware virtuális gépek és fizikai kiszolgálón.  A mobilitási szolgáltatást kell-e a gépen adatírásokat, és továbbítja őket a Site Recovery is folyamatkiszolgálón.

Ez a cikk ismerteti a mobilitási szolgáltatás manuális telepítése minden egyes védeni kívánt számítógépen.

## <a name="create-a-passphrase"></a>Hozzon létre egy hozzáférési kódot

Mielőtt telepít, hozzon létre egy hozzáférési kódot, a telepítés során használt.

1. Jelentkezzen be a konfigurációs kiszolgálón.
2. Nyisson meg egy parancssort rendszergazdaként.
3. Módosítsa a könyvtárat a bin mappát, és ezután hozzon létre egy hozzáférési kódot fájlt.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. A hozzáférési fájl kódjának Store biztonságos helyen. 


## <a name="install-the-service-from-the-ui"></a>A szolgáltatás telepítése a felhasználói felületről

>[!IMPORTANT]
> Ha Azure IaaS virtuális gépek egy Azure-régióból a másikba replikál, ne használja ezt a módszert. A parancssor alapú telepítési módszer insteadl használja.

1. Keresse meg a szüksége van a gép operációs rendszerének installer verziószámát. A telepítők a %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappában találhatók. [Ellenőrizze](vmware-physical-mobility-service-overview.md#installer-files) szükséges telepítőt.
2. Másolja a telepítőfájlt a gépre, és futtassa azt.
3. A **telepítési lehetőség**válassza **telepíteni a mobilitási szolgáltatást**.
4. A telepítés helyének kiválasztása > **telepítése**.

    ![A mobilitási szolgáltatás telepítési lehetőség lapja](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. A telepítés monitorozása **telepítési folyamat**. A telepítés befejezése után jelölje ki a **lépjen tovább a konfigurációs** a szolgáltatás regisztrálja a konfigurációs kiszolgálóval.

    ![A mobilitási szolgáltatás való regisztrációhoz](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  a **konfigurációs kiszolgáló részletei**, adja meg az IP-cím és a beállított jelszót.  

    ![A mobilitási szolgáltatás való regisztrációhoz](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Válassza ki **regisztrálása** a regisztráció befejezéséhez.

    ![A mobilitási szolgáltatás regisztrációs utolsó oldal](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>A szolgáltatás telepítése a parancssor használatával

### <a name="on-a-windows-machine"></a>Egy Windows-gépen

1. Másolja a telepítőt egy helyi mappába (például C:\Temp) a kiszolgálón, amely számára védelmet kíván. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. A következőképpen telepítheti:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. A konfigurációs kiszolgálót regisztrálja az ügynököt.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Telepítési beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgent.exe/Role < MS|Fő Célkiszolgáló > /InstallLocation  <Install Location> /platform "VmWare" /Silent
Telepítési naplók | A % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/ Role | Kötelező telepítés paraméter. Meghatározza, hogy a mobilitási szolgáltatás (MS) vagy a fő célkiszolgálót (MT) kell telepíteni.
/InstallLocation| Nem kötelező paraméter. Itt adhatja meg a mobilitási szolgáltatás telepítési helyét (bármely mappa).
És platformok | Kötelező. Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. **VMware** Mware virtuális gépek/fizikai kiszolgálók; **Azure** Azure virtuális gépek számára. 
/ Csendes| Választható. Megadja, hogy a telepítő futtatásához csendes módban.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgentConfigurator.exe/csendpoint  <CSIP> /passphrasefilepath <PassphraseFilePath>
Az ügynök konfigurációs naplók | A % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
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
-v | Kötelező. Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. **VMware** Mware virtuális gépek/fizikai kiszolgálók; **Azure** Azure virtuális gépek számára. 
-q | Választható. Megadja, hogy a telepítő futtatásához csendes módban.

#### <a name="registration-settings"></a>Regisztrációs beállítások
**Beállítás** | **Részletek**
--- | ---
Használat | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
-P |  Kötelező. Teljes elérési útja, amelyben a jelszót a rendszer menti a fájlt. Bármely érvényes mappát használni

## <a name="next-steps"></a>További lépések
- [Vészhelyreállítás beállítása VMware virtuális gépekhez](vmware-azure-tutorial.md)
- [Állítsa be a fizikai kiszolgálók vészhelyreállítása](physical-azure-disaster-recovery.md)
