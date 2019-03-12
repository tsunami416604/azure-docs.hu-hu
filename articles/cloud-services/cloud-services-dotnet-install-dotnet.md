---
title: A .NET telepítése az Azure Cloud Services szerepkörei |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepítheti manuálisan a .NET-keretrendszer a felhőalapú szolgáltatás webes és feldolgozói szerepkörök
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: jeconnoc
ms.openlocfilehash: 4b6aeb2968d7642881535753cb6c8434cfe10886
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534071"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>A .NET telepítése az Azure Cloud Services-szerepkörök
Ez a cikk ismerteti, amelyek nem rendelkeznek Azure vendég operációs rendszer verzió a .NET-keretrendszer telepítése. A vendég operációs rendszer a .NET használatával a felhőalapú szolgáltatás webes és feldolgozói szerepkörök konfigurálása.

Például .NET 4.6.2-es verziójára is telepíthető a vendég operációs rendszerek 4, ezeket a nem a .NET 4.6 bármelyik verzióját. (A Vendég operációsrendszer-család 5 .NET 4.6 jár.) Az Azure vendég operációs rendszer kiadásai a legfrissebb információkért tekintse meg a [Azure vendég operációs rendszer kiadási hírei](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Az Azure SDK 2.9 tartalmazza a .NET 4.6 telepítése a Vendég operációsrendszer-család 4-es vagy korábbi korlátozását. A korlátozás a javítás érhető el a [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) hely.

A webes és feldolgozói szerepkörök a .NET telepítéséhez a .NET-telepítő a felhőszolgáltatás-projekt részeként tartalmazza. Indítsa el a telepítőt a szerepkört az indítási feladatok részeként. 

## <a name="add-the-net-installer-to-your-project"></a>A .NET-telepítő hozzáadása a projekthez
A .NET-keretrendszer webes telepítőjének letöltéséhez válassza ki a telepíteni kívánt verzió:

* [.NET 4.7.2 webalkalmazás-telepítő](https://go.microsoft.com/fwlink/?LinkId=863262)
* [A .NET 4.6.2-es webalkalmazás-telepítő](https://www.microsoft.com/download/details.aspx?id=53345)

A telepítő a hozzáadása egy *webes* szerepkör:
  1. A **Megoldáskezelőben**alatt **szerepkörök** a felhőszolgáltatási projektet, kattintson a jobb egérgombbal a *webes* szerepkört, és válassza ki **Hozzáadás**  >  **Új mappa**. Hozza létre a **bin**.
  2. Kattintson a jobb gombbal a bin mappát, és válassza ki **Hozzáadás** > **meglévő elem**. Válassza ki a .NET-telepítőt, és adja hozzá a bin mappát.
  
A telepítő a hozzáadása egy *feldolgozó* szerepkör:
* Kattintson a jobb gombbal a *feldolgozó* szerepkört, és válassza ki **Hozzáadás** > **meglévő elem**. Válassza ki a .NET-telepítőt, és adja hozzá a szerepkört. 

Ha fájlokat adnak a szerepkörhöz tartalommappába ily módon, a rendszer automatikusan hozzáadja, a cloud service-csomag. A fájlok majd egy egységes helyre a virtuális gépen vannak telepítve. Ismételje meg a folyamat egyes webes és feldolgozói szerepkörök a cloud service-ben, hogy az összes szerepkör a telepítő egy példányát.

> [!NOTE]
> A felhőszolgáltatási szerepkör a .NET 4.6.2-es verziójára kell telepítenie, még akkor is, ha az alkalmazás a .NET 4.6 célozza. A vendég operációs rendszer tartalmazza a Tudásbázis [3098779 frissítése](https://support.microsoft.com/kb/3098779) és [3097997 frissítése](https://support.microsoft.com/kb/3097997). Ha a .NET-alkalmazásokat is futtat, ha felül a Tudásbázis következő frissítések telepítve van a .NET 4.6 előfordulhatnak olyan problémák. Ezek a problémák elkerülése érdekében telepítse a 4.6-os verzió helyett a .NET 4.6.2-es verziójára. További információkért lásd: a [Tudásbázis 3118750](https://support.microsoft.com/kb/3118750) és [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Szerepkör tartalmak installer-fájlok][1]

## <a name="define-startup-tasks-for-your-roles"></a>A szerepkörök indítási feladatok meghatározása
Indítási feladatok segítségével hajtsa végre a műveleteket a szerepkör elindítása előtt. A .NET-keretrendszer telepítése az indítási feladat részeként biztosítja, hogy a keretrendszer telepítve van-e, alkalmazáskódot futtatása előtt. Az indítási feladatokról további információkért lásd: [indítási feladatok futtatása az Azure-ban](cloud-services-startup-tasks.md). 

1. Adja hozzá a következő tartalmat a ServiceDefinition.csdef fájl alatt a **WebRole** vagy **WorkerRole** csomópont összes szerepköre:
   
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
   
    A konzol parancsot futtatja, az előző konfigurációs `install.cmd` a .NET-keretrendszer telepítéséhez rendszergazdai jogosultságokkal. A konfiguráció is létrehoz egy **LocalStorage** nevű elem **NETFXInstall**. Az indítási szkript állítja be a temp mappa a helyi storage-erőforrások használatára. 
    
    > [!IMPORTANT]
    > A keretrendszer megfelelő telepítése érdekében, állítsa be az erőforrás méretét legalább 1024 MB-ra.
    
    Indítási feladatok kapcsolatos további információkért lásd: [gyakori Azure Cloud Services indítási feladatok](cloud-services-startup-tasks-common.md).

2. Hozzon létre egy fájlt **install.cmd** , és adja hozzá a következő telepítési parancsfájlját a fájlt.

   A parancsfájl ellenőrzi, hogy a megadott verzióját, a .NET-keretrendszer már telepítve van a gépen a beállításjegyzék lekérdezésével. Ha nincs telepítve a .NET-verziót, a .NET-telepítő van megnyitva. Az esetleges problémák elhárítása érdekében a parancsfájl naplózza az összes tevékenységet a startuptasklog-(jelenlegi dátum és idő) .txt tárolt **InstallLogs** helyi tárterület.
   
   > [!IMPORTANT]
   > Egy egyszerű szövegszerkesztőben, például a Jegyzettömbben Windows használatával hozza létre a install.cmd fájlt. A Visual Studio használatával hozzon létre egy szövegfájlt, és módosítsa a bővítmény .cmd, ha a fájl továbbra is tartalmazhat az UTF-8 bájtsorrendjelző. Ez a jel hibát okozhat, az első sort a szkript futása során. Ez a hiba elkerülése érdekében, az első sort a szkript egy Távolítási utasítás, amely is lehet kihagyta a byte megrendelések feldolgozása. 
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

3. Vegye fel a install.cmd fájl minden egyes szerepkör szerint **Hozzáadás** > **meglévő elem** a **Megoldáskezelőben** ebben a témakörben korábban ismertetett módon. 

    Ez a lépés befejezése után az összes szerepkör a .NET telepítő és a install.cmd fájlt kell rendelkeznie.

   ![Szerepkör tartalmát az összes fájlját][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Rendszerindítási naplók átviteléhez a Blob Storage-diagnosztika konfigurálása
Hibaelhárítási telepítési problémák egyszerűbb, konfigurálhatja az Azure Diagnostics átvitelét bármely az indítási szkript vagy az Azure Blob storage a .NET-telepítő által létrehozott naplófájlokat. Ez a módszer használatával a naplók megtekintéséhez a naplófájlok letöltése a Blob storage-ból, ahelyett, hogy a távoli asztal a szerepkörhöz.


Diagnosztikai konfigurálásához nyissa meg a diagnostics.wadcfgx fájlt, és adja hozzá a következő tartalmat alatt a **könyvtárak** csomópont: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Az XML konfigurálja a naplókönyvtárban az átvitelhez diagnosztika a **NETFXInstall** a diagnosztikai tárfiók az erőforrást a **netfex-telepítés** blob-tárolóba.

## <a name="deploy-your-cloud-service"></a>A cloud Services üzembe helyezése
A cloud Services telepítésekor az indítási feladatok telepítse a .NET-keretrendszer, ha még nem telepítette az. A cloud service szerepkörök vannak a *foglalt* állapot, amíg a keretrendszer telepítése folyamatban van. Ha a keretrendszer telepítése újraindítást igényel, a szerepkörök is újraindulhat. 

## <a name="additional-resources"></a>További források
* [A .NET-keretrendszer telepítése][Installing the .NET Framework]
* [Meghatározza, mely a .NET-keretrendszer-verziókat vannak telepítve.][How to: Determine Which .NET Framework Versions Are Installed]
* [Hibaelhárítás a .NET-keretrendszer telepítése][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
