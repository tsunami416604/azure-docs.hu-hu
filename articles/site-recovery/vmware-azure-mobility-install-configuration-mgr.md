---
title: A mobilitási szolgáltatás automatizálása a telepítés vészhelyreállítása érdekében az Azure Site Recovery szolgáltatásban
description: A VMware-hez /fizikai kiszolgálóvész-helyreállítási mobilitási szolgáltatás automatikus telepítése az Azure Site Recovery szolgáltatással.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252227"
---
# <a name="automate-mobility-service-installation"></a>A mobilitási szolgáltatások telepítésének automatizálása

Ez a cikk azt ismerteti, hogyan automatizálható a telepítés és a frissítések a mobilszolgáltató ügynök az [Azure Site Recovery.](site-recovery-overview.md)

Amikor telepíti a Site Recovery a vész-helyreállítási helyszíni VMware virtuális gépek és a fizikai kiszolgálók az Azure-ba, telepíti a Mobility Service ügynök minden gépen szeretné replikálni. A mobilitási szolgáltatás rögzíti a számítógépen lévő adatok írásait, és továbbítja azokat a Site Recovery folyamatkiszolgálóra replikációra. A mobilitási szolgáltatást többféleképpen is üzembe helyezheti:

- **Leküldéses telepítés:** Hagyja, hogy a Site Recovery telepítse a mobilitási szolgáltatásügynök, ha engedélyezi a replikációt egy gép az Azure Portalon.
- **Kézi telepítés**: Telepítse a Mobility szolgáltatást manuálisan minden gépre. [További információ](vmware-physical-mobility-service-overview.md) a leküldéses és kézi telepítésről.
- **Automatikus telepítés:** Automatizálhatja a telepítést olyan szoftvertelepítési eszközökkel, mint a Microsoft Endpoint Configuration Manager vagy a külső gyártótól származó eszközök, például a JetPatch.

Az automatikus telepítés és frissítés megoldást kínál, ha:

- A szervezet nem engedélyezi a leküldéses telepítést a védett kiszolgálókon.
- A vállalati szabályzat megköveteli a jelszavak rendszeres módosítását. Meg kell adnia egy jelszót a leküldéses telepítéshez.
- A biztonsági házirend nem teszi lehetővé tűzfalkivételek hozzáadását bizonyos gépekhez.
- Tárhelyszolgáltatóként jár el, és nem szeretné megadni az ügyfélszámítógép hitelesítő adatait, amelyek szükségesek a Site Recovery leküldéses telepítéséhez.
- Egyszerre sok kiszolgálóra kell skáláznia az ügynöktelepítéseket.
- A telepítéseket és a frissítéseket a tervezett karbantartási időszakok ban szeretné ütemezni.

## <a name="prerequisites"></a>Előfeltételek

A telepítés automatizálásához a következő elemekre van szükség:

- Telepített szoftvertelepítési megoldás, például [Configuration Manager](/configmgr/) vagy [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Üzembe helyezési előfeltételek az [Azure-ban](vmware-azure-tutorial-prepare-on-premises.md) és a helyszíni VMware vész-helyreállítási, vagy a [fizikai kiszolgáló](physical-azure-disaster-recovery.md) vész-helyreállítási. [Azure](tutorial-prepare-azure.md) Tekintse át a [vész-helyreállítási támogatási követelményeket.](vmware-physical-azure-support-matrix.md)

## <a name="prepare-for-automated-deployment"></a>Felkészülés az automatikus telepítésre

Az alábbi táblázat a Mobility Service telepítésének automatizálására szolgáló eszközöket és folyamatokat foglalja össze.

**Eszköz** | **Részletek** | **Utasítások**
--- | --- | ---
**Configuration Manager** | 1. Ellenőrizze, hogy a fent felsorolt [előfeltételek](#prerequisites) a helyükön vannak-e. <br/><br/> 2. Telepítse a vészhelyreállítást a forráskörnyezet beállításával, beleértve egy OVA-fájl letöltését a Site Recovery konfigurációs kiszolgáló vmware virtuális gépként való telepítéséhez egy OVF-sablon használatával.<br/><br/> 3. Regisztrálja a konfigurációs kiszolgálót a Site Recovery szolgáltatással, állítsa be a cél Azure-környezetet, és konfiguráljon replikációs szabályzatot.<br/><br/> 4. A Mobility Service automatikus telepítéséhez hozzon létre egy hálózati megosztást, amely tartalmazza a konfigurációs kiszolgáló jelmondatát és a Mobilitási szolgáltatás telepítési fájljait.<br/><br/> 5. Hozzon létre egy Configuration Manager csomagot, amely tartalmazza a telepítést vagy frissítéseket, és előkészíti a Mobility Service telepítését.<br/><br/> 6. Ezután engedélyezheti a replikációt az Azure-ba a mobilszolgáltatással telepített gépeken. | [Automatizálás a Configuration Manager rel](#automate-with-configuration-manager)
**JetPatch (JetPatch)** | 1. Ellenőrizze, hogy a fent felsorolt [előfeltételek](#prerequisites) a helyükön vannak-e. <br/><br/> 2. Telepítse a vészhelyreállítást a forráskörnyezet beállításával, beleértve a JetPatch Agent Manager for Azure Site Recovery letöltését és üzembe helyezését a Site Recovery környezetben, egy OVF-sablon használatával.<br/><br/> 3. Regisztrálja a konfigurációs kiszolgálót a Site Recovery szolgáltatással, állítsa be a cél Azure-környezetet, és konfiguráljon replikációs szabályzatot.<br/><br/> 4. Az automatikus telepítéshez inicializálja és töltse ki a JetPatch Agent Manager konfigurációját.<br/><br/> 5. A JetPatch hozhat létre a Site Recovery házirendet, hogy automatizálják a telepítés és a frissítés a Mobilitási szolgáltatás ügynök. <br/><br/> 6. Ezután engedélyezheti a replikációt az Azure-ba a mobilszolgáltatással telepített gépeken. | [Automatizálás a JetPatch Agent Manager segítségével](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Ügynök telepítésének hibaelhárítása a JetPatch-ben](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizálás a Configuration Manager rel

### <a name="prepare-the-installation-files"></a>A telepítési fájlok előkészítése

1. Győződjön meg róla, hogy az előfeltételek a helyükön vannak.
1. Hozzon létre egy biztonságos hálózati fájlmegosztást (SMB-megosztást), amelyet a konfigurációs kiszolgálót futtató gép érhet el.
1. A Configuration Manager alkalmazásban [kategorizálja azaz kiszolgálókat,](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) amelyekre telepíteni vagy frissíteni szeretné a Mobilitási szolgáltatást. Az egyik gyűjteménynek tartalmaznia kell az összes Windows-kiszolgálót, a másiknak az összes Linux-kiszolgálót.
1. A hálózati megosztáson hozzon létre egy mappát:

   - Windows gépeken történő telepítéshez hozzon létre egy _MobSvcWindows_nevű mappát.
   - Linux gépeken történő telepítéshez hozzon létre egy _MobSvcLinux_nevű mappát.

1. Jelentkezzen be a konfigurációs kiszolgálógépre.
1. A konfigurációs kiszolgáló gépen nyisson meg egy felügyeleti parancssort.
1. A jelszófájl létrehozásához futtassa a következő parancsot:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Másolja a _MobSvc.passphrase_ fájlt a Windows mappába és a Linux mappába.
1. A telepítőfájlokat tartalmazó mappát tallózással futtassa a következő parancsokkal:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Másolja ezeket a telepítési fájlokat a hálózati megosztásra:

   - Windows esetén másolja a _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ programot a _MobSvcWindows programba._
   - Linux esetén másolja a következő fájlokat a _MobSvcLinux-ra:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Az alábbi eljárásokban leírtak szerint másolja a kódot a Windows vagy Linux mappákba. Feltételezzük, hogy:

   - A konfigurációs kiszolgáló IP-címe `192.168.3.121`.
   - A biztonságos hálózati `\\ContosoSecureFS\MobilityServiceInstallers`fájlmegosztás a.

### <a name="copy-code-to-the-windows-folder"></a>Kód másolása a Windows mappába

Másolja a következő kódot:

- Mentse a kódot a _MobSvcWindows_ mappába _install.bat_.
- Cserélje `[CSIP]` le a parancsfájl helyőrzőit a konfigurációs kiszolgáló IP-címének tényleges értékeire.
- A parancsfájl támogatja a Mobility Service ügynök új telepítését, valamint a már telepített ügynökök frissítéseit.

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

### <a name="copy-code-to-the-linux-folder"></a>Kód másolása a Linux mappába

Másolja a következő kódot:

- Mentse a kódot a _MobSvcLinux_ mappába _install_linux.sh_.
- Cserélje `[CSIP]` le a parancsfájl helyőrzőit a konfigurációs kiszolgáló IP-címének tényleges értékeire.
- A parancsfájl támogatja a Mobility Service ügynök új telepítését, valamint a már telepített ügynökök frissítéseit.

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

1. Jelentkezzen be a Configuration Manager konzolra, és nyissa meg a **Szoftvertár** > **alkalmazáskezelő** > **csomagjait.**
1. Kattintson a jobb gombbal **a Csomagok** > **létrehozása csomagra.**
1. Adja meg a csomag adatait, beleértve a nevet, a leírást, a gyártót, a nyelvet és a verziót.
1. Jelölje **be: Ez a csomag forrásfájlokat tartalmaz.**
1. Kattintson a **Tallózás**gombra, és válassza ki a megfelelő telepítőt _(MobSvcWindows_ vagy _MobSvcLinux)_ tartalmazó hálózati megosztást és mappát. Ezután válassza a **Tovább gombot.**

   ![Képernyőkép: Csomag és program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. A **Lapon Válassza ki a létrehozni kívánt programtípust,** válassza a Szokásos program tovább **választása** > **Next**lehetőséget.

   ![Képernyőkép: Csomag és program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. A **Szabványos programlap adatainak megadása** csoportban adja meg a következő értékeket:

    **Paraméter** | **Windows-érték** | **Linux érték**
    --- | --- | ---
    **Név** | A Microsoft Azure Mobility Service (Windows) telepítése | Telepítse a Microsoft Azure Mobility Service (Linux) szolgáltatást.
    **Parancssor** | Install.bat | ./install_linux.sh
    **A program futtatható** | Azt jelzi, hogy egy felhasználó be van-e jelentkezve | Azt jelzi, hogy egy felhasználó be van-e jelentkezve
    **Egyéb paraméterek** | Alapértelmezett beállítás használata | Alapértelmezett beállítás használata

   ![Képernyőkép: Csomag és program létrehozása varázsló](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. A **Szabványos program követelményeinek megadása**a következő műveleteket végezze el:

   - Windows gépek esetén válassza **a Ez a program csak meghatározott platformokon futtatható.** Ezután jelölje ki a [támogatott Windows operációs rendszereket,](vmware-physical-azure-support-matrix.md#replicated-machines) és válassza a **Tovább**gombot.
   - Linux gépek esetén válassza **ezt a programot bármely platformon futtathatja.** Ezután válassza a **Tovább**gombot.

1. Fejezze be a varázsló futtatását.

### <a name="deploy-the-package"></a>A csomag telepítése

1. A Configuration Manager konzolon kattintson a jobb gombbal a csomagra, és válassza **a Tartalom terjesztése parancsot.**

   ![Képernyőkép a Configuration Manager konzolról](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Válassza ki azokat a terjesztési pontokat, amelyekre a csomagokat másolni szeretné. [További információ](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Végezze el a varázsló lépéseit. A csomag ezután elkezdi replikálni a megadott terjesztési pontokra.
1. A csomagterjesztés befejezése után kattintson a jobb gombbal a telepítés > **csomagra.**

   ![Képernyőkép a Configuration Manager konzolról](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Válassza ki a korábban létrehozott Windows- vagy Linux-eszközgyűjteményt.
1. A **Tartalom célának megadása** lapon válassza a **Terjesztési pontok**lehetőséget.
1. A **Beállítások megadása a szoftver telepítésének szabályozásához** a lapon állítsa be a Cél **beállítást** **Kötelező**re.

   ![Képernyőkép a Szoftver telepítése varázslóról](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. A **Telepítés ütemezésének megadása**területen állítson be ütemezést. [További információ](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - A mobilitási szolgáltatás a megadott ütemezésnek megfelelően van telepítve.
   - A szükségtelen újraindítások elkerülése érdekében ütemezze a csomag telepítését a havi karbantartási időszak vagy a szoftverfrissítések ablakában.

1. A **Terjesztési pontok** lapon adja meg a beállításokat, és fejezze be a varázslót.
1. A telepítés folyamatának figyelése a Configuration Manager konzolon. Nyissa meg a > **Központi telepítések figyelése** > _\<a csomag nevét.\>_ **Monitoring**

### <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása

A Mobility Szolgáltatás eltávolításához konfigurációkezelő-csomagokat hozhat létre. A következő parancsfájl például eltávolítja a Mobilitási szolgáltatást:

```DOS
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

A virtuális gépek [replikációjának engedélyezése.](vmware-azure-enable-replication.md)
