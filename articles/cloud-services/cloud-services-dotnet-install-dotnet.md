---
title: A .NET telepítése az Azure Cloud Services szerepköreire | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy miként telepíthető manuálisan a .
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c830dc0ee38ad808579a62274e3db87d0696e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214721"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>A .NET telepítése az Azure Cloud Services szerepköreire
Ez a cikk ismerteti, hogyan telepítheti a .NET Framework olyan verzióit, amelyek nem az Azure Vendég operációs rendszerhez származnak. A .NET használatával konfigurálhatja a felhőszolgáltatás webes és feldolgozói szerepköreit.

A .NET Framework 4.6.2-t például telepítheti a Vendég operációsrendszer-család 4-re, amely nem jár a .NET Framework 4.6 bármely kiadásával. (A Vendég operációsrendszer-család 5 nem jön a .NET Framework 4.6.) Az Azure Vendég operációs rendszer kiadásairól az Azure Vendég operációs rendszer kiadásáról szóló legfrissebb információkért tekintse meg az [Azure Vendég operációs rendszer kiadásának híreit.](cloud-services-guestos-update-matrix.md) 

>[!IMPORTANT]
>Az Azure SDK 2.9 tartalmazza a . A korlátozás javítása a [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) webhelyen érhető el.

A .NET telepítése a webes és feldolgozói szerepkörökön, a .NET web telepítő a felhőszolgáltatási projekt részeként. Indítsa el a telepítőt a szerepkör indítási feladatainak részeként. 

## <a name="add-the-net-installer-to-your-project"></a>A .NET telepítő hozzáadása a projekthez
A .

* [.NET Framework 4.8 webes telepítő](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET Framework 4.7.2 webes telepítő](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework 4.6.2 webes telepítő](https://www.microsoft.com/download/details.aspx?id=53345)

Webes *szerepkör* telepítőjének hozzáadása:
  1. A **Megoldáskezelő**ben a **Szerepkörök** a felhőalapú szolgáltatásprojektben csoportban kattintson a jobb gombbal a *webes* szerepkörre, és válassza az Új mappa **hozzáadása** > **parancsot.** Hozzon létre egy **bin**nevű mappát .
  2. Kattintson a jobb gombbal a raktárhely mappára, és válassza a Meglévő elem **hozzáadása** > **parancsot.** Jelölje ki a .NET telepítőt, és adja hozzá a tárolómappához.
  
A feldolgozói *szerepkör* telepítőjének hozzáadása:
* Kattintson a jobb gombbal a *dolgozói* szerepkörre, és válassza a Meglévő elem **hozzáadása** > **parancsot.** Jelölje ki a .NET telepítőt, és adja hozzá a szerepkörhöz. 

Ha így ad hozzá fájlokat a szerepkörtartalom mappájához, a rendszer automatikusan hozzáadja őket a felhőszolgáltatási csomaghoz. A fájlok ezután a virtuális gépen egy konzisztens helyre kerülnek. Ismételje meg ezt a folyamatot a felhőszolgáltatásban lévő minden webes és feldolgozói szerepköresetében, hogy minden szerepkör rendelkezik a telepítő egy-egy másolatával.

> [!NOTE]
> A .NET Framework 4.6.2-t akkor is telepítse a felhőszolgáltatási szerepkörre, ha az alkalmazás a .NET Framework 4.6-ot célozza meg. A vendég operációs rendszer tartalmazza a Tudásbázis [3098779-es](https://support.microsoft.com/kb/3098779) és [3097997-es és 3097997-es frissítését.](https://support.microsoft.com/kb/3097997) Problémák merülhetnek fel a .NET alkalmazások futtatásakor, ha a .NET Framework 4.6 a Tudásbázis frissítései nek tetejére van telepítve. A problémák elkerülése érdekében a 4.6-os verzió helyett telepítse a .NET Framework 4.6.2-es verzióját. További információt a [Tudásbázis 3118750](https://support.microsoft.com/kb/3118750) és [4340191.](https://support.microsoft.com/kb/4340191)
> 
> 

![Szerepkör tartalma telepítőfájlokkal][1]

## <a name="define-startup-tasks-for-your-roles"></a>Indítási feladatok definiálása a szerepkörökhöz
Az indítási feladatok segítségével műveleteket hajthat végre a szerepkör indítása előtt. A . Az indítási feladatokról az [Indítási feladatok futtatása az Azure-ban című témakörben](cloud-services-startup-tasks.md)talál további információt. 

1. Adja hozzá a következő tartalmat a ServiceDefinition.csdef fájlhoz a **WebRole** vagy **workerrole** csomópont alatt az összes szerepkörhöz:
   
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
   
    Az előző konfiguráció a `install.cmd` .NET framework telepítéséhez rendszergazdai jogosultságokkal futtatja a konzolparancsot. A konfiguráció létrehoz egy **NETFXInstall**nevű **LocalStorage** elemet is. Az indítási parancsfájl úgy állítja be az ideiglenes mappát, hogy ezt a helyi tárolási erőforrást használja. 
    
    > [!IMPORTANT]
    > A keretrendszer megfelelő telepítésének biztosítása érdekében állítsa az erőforrás méretét legalább 1024 MB-ra.
    
    Az indítási feladatokról az [Azure Cloud Services gyakori indítási feladatai című](cloud-services-startup-tasks-common.md)témakörben talál további információt.

2. Hozzon létre egy **install.cmd** nevű fájlt, és adja hozzá a következő telepítési parancsfájlt a fájlhoz.

   A parancsfájl a rendszerleíró adatbázis lekérdezésével ellenőrzi, hogy a . Ha a . A problémák elhárítása érdekében a parancsfájl naplózza az összes tevékenységet a fájl startuptasklog-(aktuális dátum és idő).txt, amely a **InstallLogs** helyi tárolóban található.
   
   > [!IMPORTANT]
   > A install.cmd fájl létrehozásához használjon egyszerű szövegszerkesztőt, például a Windows Jegyzettömböt. Ha a Visual Studio segítségével hoz létre szövegfájlt, és a kiterjesztést .cmd-re módosítja, a fájl továbbra is tartalmazhat UTF-8 bájtos rendelési jelet. Ez a jel hibát okozhat a parancsfájl első sorának futtatásakor. A hiba elkerülése érdekében a parancsfájl első sorában adjon olyan REM utasítást, amelyet a bájtrendelés feldolgozása kihagyhat. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
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
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Adja hozzá a install.cmd fájlt az egyes szerepkörökhöz a**Meglévő elem** **hozzáadása** > a **Megoldáskezelőben** című témakörkorábbi témakörben leírtak szerint. 

    A lépés befejezése után minden szerepkörnek rendelkeznie kell a .NET telepítő fájllal és a install.cmd fájllal.

   ![Szerepkör tartalma az összes fájllal][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>A Diagnosztika konfigurálása az indítási naplók Blob-tárolóba való átviteléhez
A telepítési problémák elhárításának egyszerűsítése érdekében beállíthatja, hogy az Azure Diagnostics átvigye az indítási parancsfájl vagy a .NET telepítő által létrehozott naplófájlokat az Azure Blob storage-ba. Ezzel a módszerrel megtekintheti a naplókat a naplófájlok letöltésével a Blob storage ahelyett, hogy a távoli asztalon a szerepkörbe.


A Diagnosztika konfigurálásához nyissa meg a diagnostics.wadcfgx fájlt, és adja hozzá a következő tartalmat a **Könyvtárak** csomóponthoz: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Ez az XML úgy konfigurálja a Diagnosztikát, hogy a **NETFXInstall** erőforrás naplókönyvtárában lévő fájlokat átvigye a **netfx-install** blob tároló diagnosztikai tárfiókjába.

## <a name="deploy-your-cloud-service"></a>A felhőszolgáltatás üzembe helyezése
A felhőszolgáltatás telepítésekor az indítási feladatok telepítik a .NET Framework rendszert, ha még nincs telepítve. A felhőszolgáltatási szerepkörök *foglalt* állapotban vannak, miközben a keretrendszer telepítése folyamatban van. Ha a keretrendszer telepítése újraindítást igényel, a szolgáltatásszerepkörök is újraindulhatnak. 

## <a name="additional-resources"></a>További források
* [A .][Installing the .NET Framework]
* [A .NET Framework telepített verzióinak meghatározása][How to: Determine Which .NET Framework Versions Are Installed]
* [A .][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



