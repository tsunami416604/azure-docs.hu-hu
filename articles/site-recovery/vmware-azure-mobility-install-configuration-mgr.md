---
title: Automatizálható a mobilitási szolgáltatás telepítése az Azure Site Recovery a System Center Configuration Manager |} Microsoft Docs
description: Ez a cikk segítséget nyújt a mobilitási szolgáltatás telepítési automatizálásához a System Center Configuration Manager használatával.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 50328fc3b594a9162ffb9f82f699f43f9106640f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "33942513"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizálható a mobilitási szolgáltatás telepítése a System Center Configuration Managerrel

A mobilitási szolgáltatás telepítve van a VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure használatával kívánt [Azure Site Recovery](site-recovery-overview.md)

Ez a cikk azt mutatja, hogyan használhatók fel a System Center Configuration Manager központi telepítése az Azure Site Recovery mobilitási szolgáltatás a VMware virtuális gép. Szoftverfrissítési központi telepítési eszköz használatával, például a Configuration Manager a következő előnyökkel:

* A tervezett karbantartási időszakban, a szoftverfrissítések friss telepítések és frissítések ütemezése
* Központi telepítési kiszolgáló száz egyidejűleg méretezése

Ez a cikk a System Center Configuration Manager 2012 R2 használatával bemutatják a telepítési tevékenység. Azt feltételezi, hogy a verzió a **9.9.4510.1** vagy magasabb a mobilitási szolgáltatást.

Alternatív megoldásként automatizálható a mobilitási szolgáltatás telepítési [Azure Automation DSC ](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Előfeltételek

1. Szoftverfrissítési központi telepítési eszköz, például a Configuration Manager, már üzembe van helyezve a környezetben.
2. Hozzon létre két [eszközgyűjtemények](https://technet.microsoft.com/library/gg682169.aspx), egy, az összes **Windows kiszolgálók**, és egy másik összes **Linux kiszolgálók**, hogy szeretné-e a Site Recovery-védelemmel.
3. A konfigurációs kiszolgáló, amely már regisztrálva van a Recovery Services-tároló.
4. A biztonságos hálózati fájlmegosztást (SMB-megosztás), amelyet a configuration manager számítógép által elérhető.

## <a name="deploy-on-windows-machines"></a>A Windows-gépek telepítése
> [!NOTE]
> Ez a cikk feltételezi, hogy a konfigurációs kiszolgáló IP-címe 192.168.3.121, és hogy van-e a biztonságos hálózati fájlmegosztás \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
1. Hozzon létre egy mappát a hálózati megosztáson található, és adjon neki nevet **MobSvcWindows**.
2. Jelentkezzen be a konfigurációs kiszolgáló, és nyisson meg egy rendszergazdai parancssort.
3. Jelszó fájlt létrehozni a következő parancsok futtatásával:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Másolás a **MobSvc.passphrase** fájlt a **MobSvcWindows** mappa a hálózati megosztáson.
5. Keresse meg a telepítő tárház a konfigurációs kiszolgálón a következő parancs futtatásával:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Másolás a **Microsoft-ASR\_EE\_*verzió*\_Windows\_GA\_*dátum*\_Release.exe**  számára a **MobSvcWindows** mappa a hálózati megosztáson.
7. Másolja az alábbi kódot, és mentse a fájt **install.bat** azokat a **MobSvcWindows** mappa.

   > [!NOTE]
   > Cserélje le a [CSIP] helyőrzőket a parancsfájl a tényleges értékek az IP-cím a konfigurációs kiszolgáló.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="create-a-package"></a>Csomag létrehozása

1. Jelentkezzen be a Configuration Manager konzolt.
2. Keresse meg a **szoftverkönyvtár** > **Alkalmazáskezelés** > **csomagok**.
3. Kattintson a jobb gombbal **csomagok**, és válassza ki **csomag létrehozása**.
4. Adja meg a nevét, leírását, a gyártó, a nyelvi és a verzió.
5. Válassza ki a **Ez a csomag forrásfájlokat tartalmaz** jelölőnégyzetet.
6. Kattintson a **Tallózás**, és válassza ki a telepítő tárolására szolgáló hálózati megosztáshoz (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Az a **válassza ki a létrehozandó programtípust** lapon jelölje be **normál Program**, és kattintson a **következő**.

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Az a **adja meg a normál programra vonatkozó információk** lapon adja meg az alábbi adatokat, és kattintson a **következő**. (A bemeneti adatok használhatja az alapértelmezett értékekre.)

  | **Paraméter neve** | **Érték** |
  |--|--|
  | Name (Név) | Telepítse a Microsoft Azure mobilitási szolgáltatás (Windows) |
  | Parancssor | Install.bat |
  | A program futtatható | -E a bejelentkezett felhasználó |

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. A következő oldalon válassza ki a cél operációs rendszereket. Csak a Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2 telepítheti a mobilitási szolgáltatást.

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. A varázsló befejezéséhez kattintson a **következő** kétszer.


> [!NOTE]
> A parancsfájl támogatja mindkét új telepítései Mobilitásiszolgáltatás ügynökök és a már telepített ügynökök frissítését.

### <a name="deploy-the-package"></a>A csomag központi telepítéséhez
1. A Configuration Manager konzolon kattintson a jobb gombbal a csomagot, és válassza ki **tartalom terjesztése**.
  ![Képernyőfelvétel a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Válassza ki a **[terjesztési pontok](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** be, amely a csomagot másolni szeretné.
3. A varázsló befejezéséhez. A csomag ezután elindítja a megadott terjesztési pontokra replikálni.
4. A csomag terjesztését a után kattintson jobb gombbal a csomagra, és válassza ki **telepítés**.
  ![Képernyőfelvétel a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Válassza ki a Windows Server-eszközök gyűjteményére, a célgyűjteményt központi telepítéséhez, az Előfeltételek című részben létrehozott.

  ![Képernyőfelvétel a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Az a **adja meg a tartalom célhelyét** lapon jelölje be a **terjesztési pontok**.
7. Az a **a szoftver központi telepítését vezérlő beállítások megadása a** lapon, győződjön meg arról, hogy erre a célra **szükséges**.

  ![Képernyőfelvétel a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Az a **adja meg a központi telepítés ütemezése** lapján adjon meg egy ütemezést. További információkért lásd: [csomagok ütemezés](https://technet.microsoft.com/library/gg682178.aspx).
9. Az a **terjesztési pontok** lapon, az Adatközpont igényeinek megfelelően tulajdonságainak konfigurálása. Fejezze be a varázslót.

> [!TIP]
> Felesleges újraindítások elkerülése érdekében ütemezni a csomag telepítése során a havi karbantartási vagy szoftver frissítések ablakot.

A Configuration Manager konzol használatával figyelheti a központi telepítés folyamatban van. Ugrás a **figyelés** > **központi telepítések** > *[a csomag neve]*.

  ![Képernyőfelvétel a Configuration Manager beállítás központi telepítéseinek figyeléséhez](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Linux-gépek telepítése
> [!NOTE]
> Ez a cikk feltételezi, hogy a konfigurációs kiszolgáló IP-címe 192.168.3.121, és hogy van-e a biztonságos hálózati fájlmegosztás \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
1. Hozzon létre egy mappát a hálózati megosztáson található, és nevezze el, mint **MobSvcLinux**.
2. Jelentkezzen be a konfigurációs kiszolgáló, és nyisson meg egy rendszergazdai parancssort.
3. Jelszó fájlt létrehozni a következő parancsok futtatásával:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Másolás a **MobSvc.passphrase** fájlt a **MobSvcLinux** mappa a hálózati megosztáson.
5. Keresse meg a konfigurációs kiszolgálón a telepítő tárház a parancs futtatásával:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. A következő fájlokat másolja át a **MobSvcLinux** a hálózati megosztáson mappába:
   * Microsoft-ASR\_EE\*bites RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_EE\*UBUNTU 14.04-64\*release.tar.gz


7. Másolja az alábbi kódot, és mentse a fájt **install_linux.sh** azokat a **MobSvcLinux** mappa.
   > [!NOTE]
   > Cserélje le a [CSIP] helyőrzőket a parancsfájl a tényleges értékek az IP-cím a konfigurációs kiszolgáló.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Csomag létrehozása

1. Jelentkezzen be a Configuration Manager konzolt.
2. Keresse meg a **szoftverkönyvtár** > **Alkalmazáskezelés** > **csomagok**.
3. Kattintson a jobb gombbal **csomagok**, és válassza ki **csomag létrehozása**.
4. Adja meg a nevét, leírását, a gyártó, a nyelvi és a verzió.
5. Válassza ki a **Ez a csomag forrásfájlokat tartalmaz** jelölőnégyzetet.
6. Kattintson a **Tallózás**, és válassza ki a telepítő tárolására szolgáló hálózati megosztáshoz (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Az a **válassza ki a létrehozandó programtípust** lapon jelölje be **normál Program**, és kattintson a **következő**.

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Az a **adja meg a normál programra vonatkozó információk** lapon adja meg az alábbi adatokat, és kattintson a **következő**. (A bemeneti adatok használhatja az alapértelmezett értékekre.)

    | **Paraméter neve** | **Érték** |
  |--|--|
  | Name (Név) | Telepítse a Microsoft Azure mobilitási szolgáltatás (Linux) |
  | Parancssor | ./install_linux.sh |
  | A program futtatható | -E a bejelentkezett felhasználó |

  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. A következő oldalon válassza ki a **Ez a program bármilyen platformon futtatható**.
  ![Képernyőfelvétel a csomag és Program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. A varázsló befejezéséhez kattintson a **következő** kétszer.

> [!NOTE]
> A parancsfájl támogatja mindkét új telepítései Mobilitásiszolgáltatás ügynökök és a már telepített ügynökök frissítését.

### <a name="deploy-the-package"></a>A csomag központi telepítéséhez
1. A Configuration Manager konzolon kattintson a jobb gombbal a csomagot, és válassza ki **tartalom terjesztése**.
  ![Képernyőfelvétel a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Válassza ki a **[terjesztési pontok](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** be, amely a csomagot másolni szeretné.
3. A varázsló befejezéséhez. A csomag ezután elindítja a megadott terjesztési pontokra replikálni.
4. A csomag terjesztését a után kattintson jobb gombbal a csomagra, és válassza ki **telepítés**.
  ![Képernyőfelvétel a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Válassza ki a Linux Server-eszközök gyűjteményére, a célgyűjteményt központi telepítéséhez, az Előfeltételek című részben létrehozott.

  ![Képernyőfelvétel a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Az a **adja meg a tartalom célhelyét** lapon jelölje be a **terjesztési pontok**.
7. Az a **a szoftver központi telepítését vezérlő beállítások megadása a** lapon, győződjön meg arról, hogy erre a célra **szükséges**.

  ![Képernyőfelvétel a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Az a **adja meg a központi telepítés ütemezése** lapján adjon meg egy ütemezést. További információkért lásd: [csomagok ütemezés](https://technet.microsoft.com/library/gg682178.aspx).
9. Az a **terjesztési pontok** lapon, az Adatközpont igényeinek megfelelően tulajdonságainak konfigurálása. Fejezze be a varázslót.

A Linux Server-eszközök gyűjteményére, mobilitási szolgáltatás a konfigurált ütemezés szerint lekérdezi telepítve.


## <a name="uninstall-the-mobility-service"></a>Távolítsa el a mobilitási szolgáltatást
Távolítsa el a mobilitási szolgáltatást a Configuration Manager-csomagokat akkor hozhatja létre. Ehhez használja az alábbi parancsfájlt:

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>További lépések
Most már készen áll a [engedélyezni a védelmet](vmware-azure-enable-replication.md) a virtuális gépek számára.
