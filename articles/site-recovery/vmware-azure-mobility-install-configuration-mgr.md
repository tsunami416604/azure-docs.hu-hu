---
title: A Azure Site Recovery mobilitási szolgáltatás telepítésének automatizálása a VMware-alapú virtuális gépek és fizikai kiszolgálók Azure-beli helyreállításához a System Center Configuration Manager használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan automatizálható a mobilitási szolgáltatás telepítése a System Center Configuration Managerokkal, a VMware virtuális gépek és fizikai kiszolgálók Azure-ba történő helyreállítását Site Recovery használatával.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ee92ad6e0687018f69044bf3edde76b9f98cee52
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255590"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>A mobilitási szolgáltatás telepítésének automatizálása System Center Configuration Manager

A mobilitási szolgáltatás telepítve van az Azure-ba replikálni kívánt VMware virtuális gépeken és fizikai kiszolgálókon [Azure site Recovery](site-recovery-overview.md)

Ez a cikk bemutatja, hogyan használhatja a System Center Configuration Managert a Azure Site Recovery mobilitási szolgáltatás VMware virtuális gépen való üzembe helyezésére. A szoftveres központi telepítési eszköz (például a Configuration Manager) használata a következő előnyökkel jár:

* Friss telepítések és frissítések ütemezett frissítése a tervezett karbantartási időszakon belül a szoftverfrissítések esetében
* Az üzembe helyezés egyszerre több száz kiszolgálóra méretezhető

Ez a cikk System Center Configuration Manager 2012 R2-t használ a központi telepítési tevékenység bemutatására. Feltételezzük, hogy a mobilitási szolgáltatás **9.9.4510.1** vagy újabb verzióját használja.

Másik lehetőségként automatizálhatja a mobilitási szolgáltatás telepítését [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md)-vel.

## <a name="prerequisites"></a>Előfeltételek

1. A környezetében már üzembe helyezett szoftvertelepítési eszköz, például Configuration Manager.
2. Hozzon létre két [webhelycsoportot](https://technet.microsoft.com/library/gg682169.aspx), egyet az összes **Windows-kiszolgálóhoz**, és egy másikat az összes olyan **Linux-kiszolgálóhoz**, amelyet site Recovery használatával szeretne védelemmel ellátni.
3. Olyan konfigurációs kiszolgáló, amely már regisztrálva van a Recovery Services-tárolóban.
4. Biztonságos hálózati fájlmegosztás (SMB-megosztás), amelyet a Configuration Manager-gép érhet el.

## <a name="deploy-on-windows-machines"></a>Üzembe helyezés Windows rendszerű gépeken
> [!NOTE]
> Ez a cikk azt feltételezi, hogy a konfigurációs kiszolgáló IP-címe 192.168.3.121, és a biztonságos hálózati fájlmegosztás \\ \ ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Az üzembe helyezés előkészítése
1. Hozzon létre egy mappát a hálózati megosztáson, és nevezze el **MobSvcWindows**.
2. Jelentkezzen be a konfigurációs kiszolgálóra, és nyisson meg egy rendszergazdai parancssort.
3. Futtassa a következő parancsokat a jelmondat létrehozásához:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Másolja a **MobSvc. jelmondat** fájlt a hálózati megosztás **MobSvcWindows** mappájába.
5. A következő parancs futtatásával keresse meg a telepítési tárházat a konfigurációs kiszolgálón:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Másolja a **Microsoft-ASR @ no__t-1UA @ no__t-2*verziót*\_Windows @ no__t-5GA @ no__t-6*Date*@no__t -8release. exe fájlt** a hálózati megosztás **MobSvcWindows** mappájába.
7. Másolja a következő kódot, és mentse a **install. bat** néven a **MobSvcWindows** mappába.

   > [!NOTE]
   > Cserélje le a parancsfájl [CSIP] helyőrzőjét a konfigurációs kiszolgáló IP-címének tényleges értékeire.

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

1. Jelentkezzen be Configuration Manager-konzolra.
2. Tallózással **Keresse meg a**következőt:  > **Application Management** > **csomag**.
3. Kattintson a jobb gombbal a **csomagok**elemre, majd válassza a **csomag létrehozása**lehetőséget.
4. Adja meg a név, a leírás, a gyártó, a nyelv és a verzió értékeit.
5. Jelölje be az **Ez a csomag forrásfájlokat tartalmaz** jelölőnégyzetet.
6. Kattintson a **Tallózás**gombra, és válassza ki a hálózati megosztást, ahol a telepítőt tárolja (\\ \ ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. A **válassza ki a létrehozandó program típusát** lapon válassza a **normál program**lehetőséget, majd kattintson a **tovább**gombra.

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. A **standard program adatainak megadása** lapon adja meg a következő adatokat, majd kattintson a **tovább**gombra. (A többi bemenet az alapértelmezett értékeket is használhatja.)

   | **Paraméter neve** | **Érték** |
   |--|--|
   | Név | Microsoft Azure mobilitási szolgáltatás telepítése (Windows) |
   | Parancssor | install. bat |
   | A program futtatható | Annak megadása, hogy van-e bejelentkezett felhasználó |

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. A következő lapon válassza ki a cél operációs rendszereket. 
10. A varázsló befejezéséhez kattintson kétszer a **tovább** gombra.


> [!NOTE]
> A parancsfájl a mobilitási szolgáltatási ügynökök új telepítéseit és a már telepített ügynökök frissítését is támogatja.

### <a name="deploy-the-package"></a>A csomag üzembe helyezése
1. A Configuration Manager-konzolon kattintson a jobb gombbal a csomagra, és válassza a **Tartalom terjesztése**lehetőséget.
   @no__t – Configuration Manager Console @ no__t-1 0Screenshot
2. Válassza ki azokat a **[terjesztési pontokat](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , amelyeken a csomagokat át szeretné másolni.
3. Fejezze be a varázslót. A csomag ezután elindítja a replikálást a megadott terjesztési pontokra.
4. A csomag terjesztésének befejezése után kattintson a jobb gombbal a csomagra, majd válassza a **telepítés**lehetőséget.
   @no__t – Configuration Manager Console @ no__t-1 0Screenshot
5. Válassza ki az előfeltételek szakaszban létrehozott Windows Server-eszköz gyűjteményt a központi telepítéshez célként megadott gyűjteményként.

   ![A szoftver központi telepítése varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. A **tartalom célhelyének megadása** lapon válassza ki a **terjesztési pontokat**.
7. A **szoftver központi telepítését vezérlő beállítások megadása** lapon győződjön meg arról, hogy a cél **szükséges**.

   ![A szoftver központi telepítése varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Az **ehhez a központi telepítéshez tartozó ütemterv megadása** lapon írja be az ütemtervet. További információ: [csomagok ütemezése](https://technet.microsoft.com/library/gg682178.aspx).
9. A **terjesztési pontok** lapon konfigurálja a tulajdonságokat az adatközpont igényei szerint. Ezután fejezze be a varázslót.

> [!TIP]
> A szükségtelen újraindítások elkerülése érdekében ütemezze a csomag telepítését a havi karbantartási időszak vagy a szoftverfrissítések ablakában.

A központi telepítés folyamatát a Configuration Manager-konzol használatával figyelheti. Lépjen a **figyelés** > **központi telepítések** >  *[a csomag neve]* .

  ![A központi telepítések figyelésére szolgáló Configuration Manager képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Üzembe helyezés Linux rendszerű gépeken
> [!NOTE]
> Ez a cikk azt feltételezi, hogy a konfigurációs kiszolgáló IP-címe 192.168.3.121, és a biztonságos hálózati fájlmegosztás \\ \ ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Az üzembe helyezés előkészítése
1. Hozzon létre egy mappát a hálózati megosztáson, és nevezze el **MobSvcLinux**.
2. Jelentkezzen be a konfigurációs kiszolgálóra, és nyisson meg egy rendszergazdai parancssort.
3. Futtassa a következő parancsokat a jelmondat létrehozásához:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Másolja a **MobSvc. jelmondat** fájlt a hálózati megosztás **MobSvcLinux** mappájába.
5. A következő parancs futtatásával keresse meg a telepítési tárházat a konfigurációs kiszolgálón:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Másolja a következő fájlokat a hálózati megosztás **MobSvcLinux** mappájába:
   * Microsoft-ASR @ no__t-0UA @ no__t-1RHEL6-64 * Release. tar. gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1RHEL7-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1OL6-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1UBUNTU-14.04-64\*release.tar.gz


7. Másolja a következő kódot, és mentse **install_linux. sh** néven a **MobSvcLinux** mappába.
   > [!NOTE]
   > Cserélje le a parancsfájl [CSIP] helyőrzőjét a konfigurációs kiszolgáló IP-címének tényleges értékeire.

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

1. Jelentkezzen be Configuration Manager-konzolra.
2. Tallózással **Keresse meg a**következőt:  > **Application Management** > **csomag**.
3. Kattintson a jobb gombbal a **csomagok**elemre, majd válassza a **csomag létrehozása**lehetőséget.
4. Adja meg a név, a leírás, a gyártó, a nyelv és a verzió értékeit.
5. Jelölje be az **Ez a csomag forrásfájlokat tartalmaz** jelölőnégyzetet.
6. Kattintson a **Tallózás**gombra, és válassza ki a hálózati megosztást, ahol a telepítőt tárolja (\\ \ ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. A **válassza ki a létrehozandó program típusát** lapon válassza a **normál program**lehetőséget, majd kattintson a **tovább**gombra.

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. A **standard program adatainak megadása** lapon adja meg a következő adatokat, majd kattintson a **tovább**gombra. (A többi bemenet az alapértelmezett értékeket is használhatja.)

    | **Paraméter neve** | **Érték** |
   |--|--|
   | Név | Microsoft Azure mobilitási szolgáltatás telepítése (Linux) |
   | Parancssor | ./install_linux.sh |
   | A program futtatható | Annak megadása, hogy van-e bejelentkezett felhasználó |

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. A következő lapon válassza ki, hogy a **program bármely platformon futtatható legyen**.
   @no__t – csomag és program létrehozása varázsló 0Screenshot @ no__t-1

10. A varázsló befejezéséhez kattintson kétszer a **tovább** gombra.

> [!NOTE]
> A parancsfájl a mobilitási szolgáltatási ügynökök új telepítéseit és a már telepített ügynökök frissítését is támogatja.

### <a name="deploy-the-package"></a>A csomag üzembe helyezése
1. A Configuration Manager-konzolon kattintson a jobb gombbal a csomagra, és válassza a **Tartalom terjesztése**lehetőséget.
   @no__t – Configuration Manager Console @ no__t-1 0Screenshot
2. Válassza ki azokat a **[terjesztési pontokat](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , amelyeken a csomagokat át szeretné másolni.
3. Fejezze be a varázslót. A csomag ezután elindítja a replikálást a megadott terjesztési pontokra.
4. A csomag terjesztésének befejezése után kattintson a jobb gombbal a csomagra, majd válassza a **telepítés**lehetőséget.
   @no__t – Configuration Manager Console @ no__t-1 0Screenshot
5. Válassza ki az előfeltételek szakaszban létrehozott Linux Server-eszköz gyűjteményt a központi telepítéshez célként megadott gyűjteményként.

   ![A szoftver központi telepítése varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. A **tartalom célhelyének megadása** lapon válassza ki a **terjesztési pontokat**.
7. A **szoftver központi telepítését vezérlő beállítások megadása** lapon győződjön meg arról, hogy a cél **szükséges**.

   ![A szoftver központi telepítése varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Az **ehhez a központi telepítéshez tartozó ütemterv megadása** lapon írja be az ütemtervet. További információ: [csomagok ütemezése](https://technet.microsoft.com/library/gg682178.aspx).
9. A **terjesztési pontok** lapon konfigurálja a tulajdonságokat az adatközpont igényei szerint. Ezután fejezze be a varázslót.

A mobilitási szolgáltatás a konfigurált ütemtervnek megfelelően települ a Linux Server-eszközök gyűjteményén.


## <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása
Configuration Manager csomagokat a mobilitási szolgáltatás eltávolításához hozhat létre. Ehhez használja a következő parancsfájlt:

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

## <a name="next-steps"></a>Következő lépések
Most már készen áll a virtuális gépek [védelmének engedélyezésére](vmware-azure-enable-replication.md) .
