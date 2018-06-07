---
title: Telepítse a .NET Azure Felhőszolgáltatások szerepkörökhöz |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan történő manuális telepítés a .NET-keretrendszer a felhőalapú szolgáltatás webes és feldolgozói szerepkörök
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2018
ms.author: adegeo
ms.openlocfilehash: 02e778154662c28b78b284fccb3bd9b9420a8bec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608440"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Telepítse a .NET szerepkörök Azure Cloud Services csomag
Ez a cikk ismerteti, amelyek nem rendelkeznek az Azure vendég operációs rendszer verzió a .NET-keretrendszer telepítése. A vendég operációs rendszer .NET használatával a felhőalapú szolgáltatás webes és feldolgozói szerepkörök konfigurálása.

Például .NET 4.6.1 is telepíthető a Vendég operációsrendszer-család 4, amelyhez nem tartozik a .NET 4.6 bármelyik verzióját. (A Vendég operációsrendszer-család 5 .NET 4.6 kapható.) Az Azure vendég operációs rendszereinek kiadásait a legfrissebb információkért lásd: a [Azure vendég operációs rendszer kiadási hírek](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Az Azure SDK 2.9 tartalmazza a .NET 4.6 telepítéséhez a Vendég operációsrendszer-család 4-es vagy korábbi korlátozását. Egy javítást alkalmaztunk a korlátozás áll rendelkezésre a [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) hely.

A webes és feldolgozói szerepkörök .NET telepítéséhez a .NET-telepítő részeként a felhőszolgáltatás-projekt tartalmazza. A telepítő elindításához a szerepkör indítása feladatok részeként. 

## <a name="add-the-net-installer-to-your-project"></a>A .NET-telepítő hozzáadása a projekthez
A .NET-keretrendszer webes telepítőjének letöltése, hogy melyik verziót telepíteni szeretné:

* [.NET 4.7.2 webalkalmazás-telepítő](http://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET 4.6.1 webalkalmazás-telepítő](http://go.microsoft.com/fwlink/?LinkId=671729)

A telepítő a hozzáadása egy *webes* szerepkör:
  1. A **Megoldáskezelőben**a **szerepkörök** a felhőszolgáltatás-projekt, kattintson a jobb egérgombbal a *webes* szerepkör, és válassza **Hozzáadás**  >  **Új mappa**. Hozza létre a **bin**.
  2. Kattintson a jobb gombbal a bin mappát, és válassza ki **Hozzáadás** > **meglévő cikk**. Válassza ki a .NET-telepítő, és vegye fel a bin mappát.
  
A telepítő a hozzáadása egy *munkavégző* szerepkör:
* Kattintson a jobb gombbal a *munkavégző* szerepkör, és válassza **Hozzáadás** > **meglévő cikk**. Válassza ki a .NET-telepítő, és adja hozzá a szerepkört. 

Fájlok kerülnek, így a szerepkör tartalommappába, ha azok még automatikusan a cloud service-csomag. A fájlok majd telepít egy egységes helyre a virtuális gépen. Ismételje meg az eljárást minden webes és feldolgozói szerepkör esetén a felhőalapú szolgáltatás, hogy az összes szerepkör a telepítő egy példányát.

> [!NOTE]
> .NET 4.6.1 telepítsen a felhőalapú szolgáltatás szerepkör, akkor is, ha az alkalmazáshoz .NET 4.6 célozza. A vendég operációs rendszer tartalmazza a Tudásbázis [3098779 frissítése](https://support.microsoft.com/kb/3098779) és [3097997 frissítése](https://support.microsoft.com/kb/3097997). Probléma akkor fordulhat elő, ha .NET 4.6 fölött a Tudásbázis frissítések telepítve van a .NET-alkalmazások futtatásakor. Ezek a problémák elkerülése érdekében telepítse a .NET 4.6.1 4.6-os verzió helyett. További információkért lásd: a [Tudásbázis 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Szerepkör tartalmak installer-fájlok][1]

## <a name="define-startup-tasks-for-your-roles"></a>Adja meg a szerepkörök feladatainak indítása
Használhatja az indítási feladatok műveletek végrehajtásához, a szerepkör indítása előtt. A .NET-keretrendszer telepítése a indítási feladat részeként biztosítja a keretrendszer telepített bármely alkalmazás kód futtatása előtt. Indítási feladatok további információkért lásd: [indítási feladatok futtatása az Azure-ban](cloud-services-startup-tasks.md). 

1. A következő tartalmat hozzáadni a ServiceDefinition.csdef fájlt a **webrole típusról** vagy **WorkerRole** csomópont összes szerepkörök:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    A konzol parancsot futtatja, a fenti konfigurációs `install.cmd` a .NET-keretrendszer telepítése rendszergazdai jogosultságokkal. A konfiguráció emellett létrehoz egy **LocalStorage** nevű elem **NETFXInstall**. Az indítási parancsfájl állítja be az ideiglenes mappa a helyi tároló-erőforrás használatára. 
    
    > [!IMPORTANT]
    > Ahhoz, hogy helyes-e a keretrendszer telepítése, ehhez az erőforráshoz méretének beállítása számára legalább 1024 MB.
    
    Indítási feladatokkal kapcsolatos további információkért lásd: [közös Azure Felhőszolgáltatások indítási feladatok](cloud-services-startup-tasks-common.md).

2. Hozzon létre egy fájlt **install.cmd** , és adja hozzá a következő parancsfájl telepítse a fájlt.

    A parancsfájl ellenőrzi, hogy a .NET-keretrendszer verzióját már telepítve van a számítógépen a beállításjegyzék lekérdezésével. Ha a .NET-verziója nincs telepítve, a .NET-telepítő van megnyitva. A problémák elhárítása érdekében a parancsfájl az összes tevékenység naplózza a startuptasklog-(jelenlegi dátum és idő) .txt tárolt **InstallLogs** helyi tárterület.
  
    > [!IMPORTANT]
    > Egy egyszerű szövegszerkesztőben, például Windows Jegyzettömb segítségével hozza létre a install.cmd fájlt. Ha a Visual Studio használatával hozzon létre egy szövegfájlt, és módosítsa a bővítmény .cmd, a fájl továbbra is tartalmazhat a UTF-8 bájtos rendelés be van jelölve. Ez a jel hibát okozhat, az első sor a parancsfájl futása során. Ez a hiba elkerülése érdekében győződjön az első sor a parancsfájl egy el utasítást, amely szerint a byte rendelés feldolgozása figyelmen kívül hagyja. 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** http://go.microsoft.com/fwlink/?LinkId=671729
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
    REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** http://go.microsoft.com/fwlink/?LinkId=852095
    REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** http://go.microsoft.com/fwlink/?LinkId=863262
    set netfx="NDP472"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP472" goto NDP472
    if %netfx%=="NDP471" goto NDP471
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
    
    set "netfxinstallfile=NDP452-KB2901954-Web.exe"
    set netfxregkey="0x5cbf5"
    goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    goto logtimestamp
    
    :NDP47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"
    goto logtimestamp
    
    :NDP471
    set "netfxinstallfile=NDP471-KB4033344-Web.exe"
    set netfxregkey="0x709fc"
    goto logtimestamp
    
    :NDP472
    set "netfxinstallfile=NDP472-KB4054531-Web.exe"
    set netfxregkey="0x70BF6"
    goto logtimestamp
    
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```

3. Vegye fel a install.cmd fájl minden egyes szerepkör szerint **Hozzáadás** > **meglévő cikk** a **Megoldáskezelőben** Ez a témakör a korábban ismertetett módon. 

    Ez a lépés befejezése után az összes szerepkör kell a .NET telepítő és a install.cmd fájlt.

   ![Összes fájlját szerepkör tartalma][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Diagnosztika indítása naplók át a Blob-tároló konfigurálása
Egyszerűbbé teheti a hibaelhárítási problémák, konfigurálhatja az indítási parancsfájl vagy az Azure Blob storage a .NET-telepítő által generált naplófájlokat átvitele Azure Diagnostics. Ezt a módszert használja, a naplók megtekintéséhez a napló fájlok letöltését a Blob storage ahelyett, hogy a távoli asztal a szerepkörhöz.

Diagnosztika konfigurálásához nyissa meg a diagnostics.wadcfgx fájlt, és adja hozzá a következő tartalmat a **könyvtárak** csomópont: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Az XML-kód konfigurálja a napló könyvtárban található fájlok átvitele diagnosztika a **NETFXInstall** a diagnosztikai tárfiók az erőforrást a **netfx-telepítés** blob tároló.

## <a name="deploy-your-cloud-service"></a>A felhőalapú szolgáltatás üzembe helyezése
A felhőalapú szolgáltatás telepítésekor az indítási feladatokat a .NET-keretrendszer telepítése, ha még nincs telepítve. A felhőszolgáltatás szerepköreit szerepelnek a *foglalt* állapot, amíg a keretrendszer telepítése folyamatban van. Ha a keretrendszer telepítés újraindítása szükséges, a szolgáltatás szerepkörök is újraindulhat. 

## <a name="additional-resources"></a>További források
* [A .NET-keretrendszer telepítése][Installing the .NET Framework]
* [Határozza meg, melyik .NET-keretrendszer verziója telepítve van][How to: Determine Which .NET Framework Versions Are Installed]
* [Hibaelhárítás a .NET-keretrendszer telepítése][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
