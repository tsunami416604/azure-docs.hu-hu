---
title: Automatizálhatja a mobilitási szolgáltatás telepítése az Azure Site Recovery használatával a System Center Configuration Manager |} A Microsoft Docs
description: Ez a cikk segítségével automatizálhatja a mobilitási szolgáltatás telepítése a System Center Configuration Manager használatával.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 6aff17908d220557720776a2d7884adc28b5deee
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37917169"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizálhatja a mobilitási szolgáltatás telepítése a System Center Configuration Managerrel

A mobilitási szolgáltatás telepítve van a VMware virtuális gépek és fizikai kiszolgálók Azure-bA replikálni kívánt [Azure Site Recovery](site-recovery-overview.md)

Ez a cikk egy példát, hogyan használhatja a System Center Configuration Managerben az Azure Site Recovery mobilitási szolgáltatást a VMware virtuális gép üzembe helyezéséhez. Szoftvertelepítő eszköz használatával, mint a Configuration Manager a következő előnyökkel jár:

* A tervezett karbantartási időszakban, a szoftverfrissítések friss telepítések és frissítések ütemezése
* Üzembe helyezés több száz, a kiszolgálók egyszerre méretezése

Ez a cikk a System Center Configuration Manager 2012 R2 használatával mutatja be a telepítési tevékenységet. Azt feltételezi, hogy a verzió a **9.9.4510.1** vagy újabb verzióját a mobilitási szolgáltatást.

Azt is megteheti, automatizálhatja a mobilitási szolgáltatás telepítése [Azure Automation DSC ](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Előfeltételek

1. Egy szoftverfrissítési központi telepítési eszköz, például a Configuration Manager, a környezetében már telepített.
2. Hozzon létre két [eszközgyűjtemények](https://technet.microsoft.com/library/gg682169.aspx), egy az összes **Windows kiszolgálók**, és a egy másik, az összes **Linux-kiszolgálók**, hogy szeretné-e a Site Recovery használatával védi.
3. Konfigurációs kiszolgáló, amely már regisztrálva van a Recovery Services-tároló.
4. Egy biztonságos hálózati fájlmegosztásra (SMB-megosztás), amely szerint a configuration manager-alapú gépre érhető el.

## <a name="deploy-on-windows-machines"></a>Windows-gépeken üzembe helyezése
> [!NOTE]
> Ez a cikk azt feltételezi, hogy a konfigurációs kiszolgáló IP-címét 192.168.3.121, és hogy a biztonságos hálózati fájlmegosztás \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
1. Hozzon létre egy mappát a hálózati megosztást, és adja neki **MobSvcWindows**.
2. Jelentkezzen be a konfigurációs kiszolgáló, és nyisson meg egy rendszergazdai parancssort.
3. Futtassa a következő parancsokat egy hozzáférési fájl kódjának létrehozásához:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Másolás a **MobSvc.passphrase** fájlt a **MobSvcWindows** mappa a hálózati megosztáson.
5. Keresse meg a konfigurációs kiszolgálón a telepítő tárházat a következő parancs futtatásával:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Másolás a **Microsoft-ASR\_UA\_*verzió*\_Windows\_GA\_*dátum*\_Release.exe**  , a **MobSvcWindows** mappa a hálózati megosztáson.
7. Másolja az alábbi kódot, és mentse a fájt **install.bat** be a **MobSvcWindows** mappát.

   > [!NOTE]
   > Ez a szkript a [CSIP] helyőrzőt cserélje le tényleges értéke alapján a konfigurációs kiszolgáló IP-cím.

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
4. Adja meg a nevét, leírását, a gyártó, a nyelv és a verzió.
5. Válassza ki a **Ez a csomag forrásfájlokat tartalmaz** jelölőnégyzetet.
6. Kattintson a **Tallózás**, és válassza ki a hálózati megosztást, a telepítő tárolására (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Az a **válassza ki a létrehozni kívánt** lapon jelölje be **normál Program**, és kattintson a **tovább**.

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Az a **adja meg a normál programra vonatkozó információkat** lapon adja meg az alábbi ráfordítások, és kattintson a **tovább**. (A többi bemenet alapértelmezett értékre használhatja.)

  | **Paraméter neve** | **Érték** |
  |--|--|
  | Name (Név) | Telepítse a Microsoft Azure a mobilitási szolgáltatás (Windows) |
  | Parancssor | Install.bat |
  | A program futtatható | E van bejelentkezett felhasználó |

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. A következő oldalon válassza ki a cél operációs rendszereket. A mobilitási szolgáltatás csak a Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2 rendszerre telepíthető.

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. A varázsló befejezéséhez kattintson a **tovább** kétszer.


> [!NOTE]
> A parancsfájl mobilitási szolgáltatási ügynökök és a már telepített ügynökök frissítését új telepítései egyaránt támogatja.

### <a name="deploy-the-package"></a>A csomag telepítése
1. A Configuration Manager konzolon kattintson a jobb gombbal a csomagot, és válassza **tartalom terjesztése**.
  ![Képernyőfelvétel: a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Válassza ki a **[terjesztési pontok](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** be, amely a csomagokat másolja.
3. A varázsló befejezése. A csomag elindítja a megadott terjesztési pontokra történő replikálásához.
4. Miután a csomag terjesztési elkészült, kattintson a jobb gombbal a csomagot, és válassza **telepítés**.
  ![Képernyőfelvétel: a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Válassza ki a központi telepítés a célgyűjteményt, az Előfeltételek szakaszban létrehozott Windows Server-eszközök gyűjteményére.

  ![Képernyőfelvétel: a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Az a **adja meg a tartalom célhelye** lapon válassza ki a **terjesztési pontok**.
7. Az a **adja meg a szoftver telepítési módját szabályozó beállítások** lapon, győződjön meg arról, hogy a célja **szükséges**.

  ![Képernyőfelvétel: a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Az a **adja meg azt az ütemezést az üzembe helyezés** csoportjában adja meg az ütemezés szerint. További információkért lásd: [csomagok ütemezés](https://technet.microsoft.com/library/gg682178.aspx).
9. Az a **terjesztési pontok** lapon, konfigurálhatja a tulajdonságait az Adatközpont igényeinek megfelelően. Fejezze be a varázslót.

> [!TIP]
> A szükségtelen újraindítások elkerülése érdekében a csomag telepítése során a havi karbantartási időszak vagy szoftver frissítéseinek ablak ütemezése.

A Configuration Manager konzol használatával figyelheti az üzembehelyezési folyamatot. Lépjen a **figyelés** > **központi telepítések** > *[a package name]*.

  ![Képernyőfelvétel: a Configuration Manager-beállítás központi telepítéseinek figyeléséhez](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>A Linuxos gépek üzembe helyezése
> [!NOTE]
> Ez a cikk azt feltételezi, hogy a konfigurációs kiszolgáló IP-címét 192.168.3.121, és hogy a biztonságos hálózati fájlmegosztás \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
1. Hozzon létre egy mappát a hálózati megosztást, és adja neki **MobSvcLinux**.
2. Jelentkezzen be a konfigurációs kiszolgáló, és nyisson meg egy rendszergazdai parancssort.
3. Futtassa a következő parancsokat egy hozzáférési fájl kódjának létrehozásához:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Másolás a **MobSvc.passphrase** fájlt a **MobSvcLinux** mappa a hálózati megosztáson.
5. Keresse meg a konfigurációs kiszolgálón a telepítő tárház a parancs futtatásával:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. A következő fájlokat másolja a **MobSvcLinux** mappájába, a hálózati megosztás:
   * A Microsoft-ASR\_UA\*bites RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * A Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Másolja az alábbi kódot, és mentse a fájt **install_linux.sh** be a **MobSvcLinux** mappát.
   > [!NOTE]
   > Ez a szkript a [CSIP] helyőrzőt cserélje le tényleges értéke alapján a konfigurációs kiszolgáló IP-cím.

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
4. Adja meg a nevét, leírását, a gyártó, a nyelv és a verzió.
5. Válassza ki a **Ez a csomag forrásfájlokat tartalmaz** jelölőnégyzetet.
6. Kattintson a **Tallózás**, és válassza ki a hálózati megosztást, a telepítő tárolására (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Az a **válassza ki a létrehozni kívánt** lapon jelölje be **normál Program**, és kattintson a **tovább**.

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Az a **adja meg a normál programra vonatkozó információkat** lapon adja meg az alábbi ráfordítások, és kattintson a **tovább**. (A többi bemenet alapértelmezett értékre használhatja.)

    | **Paraméter neve** | **Érték** |
  |--|--|
  | Name (Név) | Telepítse a Microsoft Azure a mobilitási szolgáltatás (Linux) |
  | Parancssor | ./install_linux.sh |
  | A program futtatható | E van bejelentkezett felhasználó |

  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. A következő oldalon válassza ki a **a program bármilyen platformon futtatható**.
  ![Képernyőfelvétel: a csomag létrehozása és a Program varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. A varázsló befejezéséhez kattintson a **tovább** kétszer.

> [!NOTE]
> A parancsfájl mobilitási szolgáltatási ügynökök és a már telepített ügynökök frissítését új telepítései egyaránt támogatja.

### <a name="deploy-the-package"></a>A csomag telepítése
1. A Configuration Manager konzolon kattintson a jobb gombbal a csomagot, és válassza **tartalom terjesztése**.
  ![Képernyőfelvétel: a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Válassza ki a **[terjesztési pontok](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** be, amely a csomagokat másolja.
3. A varázsló befejezése. A csomag elindítja a megadott terjesztési pontokra történő replikálásához.
4. Miután a csomag terjesztési elkészült, kattintson a jobb gombbal a csomagot, és válassza **telepítés**.
  ![Képernyőfelvétel: a Configuration Manager konzol](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Válassza ki az üzembe helyezés a célgyűjteményt, az Előfeltételek szakaszban létrehozott Linux Server eszközgyűjteményt.

  ![Képernyőfelvétel: a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Az a **adja meg a tartalom célhelye** lapon válassza ki a **terjesztési pontok**.
7. Az a **adja meg a szoftver telepítési módját szabályozó beállítások** lapon, győződjön meg arról, hogy a célja **szükséges**.

  ![Képernyőfelvétel: a szoftver központi telepítése varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Az a **adja meg azt az ütemezést az üzembe helyezés** csoportjában adja meg az ütemezés szerint. További információkért lásd: [csomagok ütemezés](https://technet.microsoft.com/library/gg682178.aspx).
9. Az a **terjesztési pontok** lapon, konfigurálhatja a tulajdonságait az Adatközpont igényeinek megfelelően. Fejezze be a varázslót.

A mobilitási szolgáltatás telepíti a Linux rendszerű kiszolgáló-eszközök gyűjteményére, a konfigurált ütemezés szerint.


## <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása
A mobilitási szolgáltatás eltávolítása a Configuration Manager csomagokat hozhat létre. Ehhez használja a következő szkriptet:

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
Most már készen áll [engedélyezni a védelmet](vmware-azure-enable-replication.md) a virtuális gépek számára.
