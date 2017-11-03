---
title: "Cloud Services közös indítási feladatok |} Microsoft Docs"
description: "Érdemes lehet a felhőalapú szolgáltatások webes szerepkör vagy a feldolgozói szerepkör végrehajtására közös indítási feladatok néhány példát biztosít."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: cee23da5b089b02bfc0ef10afd60f0f2272585b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="common-cloud-service-startup-tasks"></a>A felhőalapú szolgáltatás indítási gyakori feladatok
A cikkben néhány olyan gyakori indítási feladatok érdemes lehet végrehajtania a felhőalapú szolgáltatás. Használhatja az indítási feladatok műveletek végrehajtásához, a szerepkör indítása előtt. Végrehajtani kívánt műveletek közé tartozik egy összetevő telepítésével, COM-összetevők regisztrálása, beállításkulcsok vagy hosszú ideig futó folyamat elindítása. 

Lásd: [Ez a cikk](cloud-services-startup-tasks.md) indítási feladatok működése, és kifejezetten hogyan hozza létre a bejegyzéseket, amelyek meghatározzák egy indítási tevékenységhez.

> [!NOTE]
> Indítási feladatok nem alkalmazhatók, a virtuális gépek, csak a felhőalapú szolgáltatás webes és feldolgozói szerepköröket.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Adja meg a környezeti változókat szerepkör indítása előtt
Ha a környezeti változók egy adott feladat definiálva van szüksége, használja a [környezet] elem belül a [feladat] elem.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Változók is használhatja a [érvényes Azure XPath értékét](cloud-services-role-config-xpath.md) kell hivatkoznia, valamit a központi telepítésről. Használata helyett a `value` attribútumot, adja meg a [RoleInstanceValue] gyermekelemet.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>IIS indítási AppCmd.exe konfigurálása
A [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) parancssori eszköz használható Azure indításkor IIS beállításainak kezelését. *AppCmd.exe* kényelmes, a parancssori hozzáférést biztosít a konfigurációs beállítások indítási feladatok Azure használható. Használatával *AppCmd.exe*, webhely beállításainál fel, módosíthatók, vagy az alkalmazások és a hely eltávolítása.

Van azonban néhány dolog, amit figyelje, hogy a használt *AppCmd.exe* az indítási feladatok:

* Indítási feladatok egynél többször újraindításnál is futtatható. Például ha egy szerepkör újraindul.
* Ha egy *AppCmd.exe* művelet csak egyszer történik, akkor hozhat létre hiba. Például egy szakaszt hozzáadni próbált *Web.config* kétszer tudta előállítani hiba.
* Indítási feladat sikertelen, ha azok egy nem nulla kilépési kóddal tér vissza vagy **errorlevel**. Például ha *AppCmd.exe* hibát generál.

Ellenőrizze, hogy jó gyakorlat a **errorlevel** hívása után *AppCmd.exe*, könnyen a teendő, ha hívása burkolása *AppCmd.exe* rendelkező egy *.cmd* fájlt. Ha egy ismert **errorlevel** választ, amelyet figyelmen kívül vagy adja át azt vissza.

Az által visszaadott errorlevel *AppCmd.exe* a winerror.h fájlban találhatók, és meg is látható [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>A hibaszintet kezelése – példa
Ebben a példában hozzáadja tömörítési szakaszt és egy tömörítési bejegyzést a JSON a *Web.config* fájl, a hiba- és naplózás.

A fontos szakasza a [ServiceDefinition.csdef] fájl itt látható, amely többek között a a [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) attribútumot `elevated` adhat *AppCmd.exe* módosítása a megfelelő engedélyekkel a *Web.config* fájlt:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

A *Startup.cmd* batch-fájl által használt *AppCmd.exe* tömörítés szakaszt és egy tömörítési bejegyzés hozzáadása a JSON a *Web.config* fájlt. A várt **errorlevel** 183, értéke nulla használatával győződjön meg a. A következő EXE parancssori program. Váratlan errorlevels StartupErrorLog.txt naplózása.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>A tűzfalszabályok hozzáadása
Nincsenek hatékonyan két tűzfal Azure-ban. Az első tűzfal szabályozza a virtuális gép és a külvilág közötti kapcsolatok. Ez a tűzfal vezérli a [végpontok] eleme a [ServiceDefinition.csdef] fájlt.

A második tűzfal szabályozza a virtuális gép és a virtuális gépen belül a folyamatok közötti kapcsolatok. Ez a tűzfal vezérelhető a `netsh advfirewall firewall` parancssori eszközt.

A szerepkörök belül elindított folyamat vonatkozó tűzfalszabályok az Azure létrehoz. Például egy szolgáltatás vagy program indításakor Azure automatikusan létrehozza a szükséges tűzfalszabályok ahhoz, hogy, hogy az internettel történő kommunikációra. Azonban a szerepkör (például egy COM + szolgáltatást, vagy a Windows ütemezett feladat) kívül a folyamat által elindított szolgáltatás létrehozása, ha szeretné manuálisan hozzon létre egy tűzfalszabályt, hogy a szolgáltatás eléréséhez. A tűzfalszabályok is létrehozható egy indítási tevékenységhez használatával.

Rendelkeznie kell egy olyan tűzfalszabályt hoz indítási tevékenységhez egy [executionContext][feladat] a **emelt szintű**. Adja hozzá a következő indítási feladat a [ServiceDefinition.csdef] fájlt.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

A tűzfalszabály hozzáadásához használja a megfelelő `netsh advfirewall firewall` parancsok a indítási parancsfájlba. Ebben a példában a indítási feladathoz biztonsági és a titkosítás a 80-as TCP-porton.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Egy adott IP-cím letiltása
Korlátozhatja egy Azure webes szerepkör elérés megadott IP-címek egy készletének a IIS módosításával **web.config** fájlt. Azt is szeretné használni, egy parancsfájlt, amely feloldja a **ipSecurity** szakasza a **ApplicationHost.config** fájlt.

Feloldásához a **ipSecurity** szakasza a **ApplicationHost.config** fájlt, hozzon létre egy parancsfájlt, amely a szerepkör indítása. Hozzon létre egy mappát a gyökérszinten a webes szerepkör nevű **indítási** és az ebben a mappában hozzon létre egy kötegfájlt nevű **startup.cmd**. Adja hozzá ezt a fájlt a Visual Studio-projektet, és állítsa be a tulajdonságokat **másolási mindig** annak érdekében, hogy a csomagban található.

Adja hozzá a következő indítási feladat a [ServiceDefinition.csdef] fájlt.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Adja hozzá ezt a parancsot a **startup.cmd** fájlt:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

E feladat futtatásakor a **startup.cmd** kötegfájlt futtat minden alkalommal, amikor a webes szerepkör inicializálva van, győződjön meg arról, hogy a szükséges futtatandó **ipSecurity** szakasz meg oldva.

Végül módosítsa úgy a [system.webServer szakasz](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) a webes szerepkör **web.config** fájl hozzáadása, amely hozzáféréssel, IP-címek listája az alábbi példában látható módon:

Ez a minta config **lehetővé teszi, hogy** eléréséhez a kiszolgálót, kivéve a két meghatározott összes IP-címek

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Ez a minta config **megtagadja** hozzáférését az a kiszolgáló kivételével a két meghatározott összes IP-címek.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>PowerShell indítási feladat létrehozása
A Windows PowerShell-parancsfájlok nem hívható közvetlenül a [ServiceDefinition.csdef] fájlt, de ezek is elindítható az indítási parancsfájlban.

PowerShell (alapértelmezés) nem fut az aláíratlan parancsfájlok. Ha nem jelentkezik a parancsfájlt, kell konfigurálni a PowerShell használatával az aláíratlan parancsfájlok futtatása. Aláíratlan parancsfájlok futtatásához a **ExecutionPolicy** értékre kell állítani **nem korlátozott**. A **ExecutionPolicy** beállítás használata alapján a Windows PowerShell verzióját.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

A vendég operációs rendszer, amely futtatja a PowerShell 2.0 vagy kényszerítheti futtatásához 2-es 1.0 használata, és ha nem érhető el, használja az 1-es verziójával.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Az indítási feladat a helyi tárterület-fájlok létrehozása
Az indítási feladat, amelyhez a később az alkalmazás által létrehozott fájlok tárolására használhatja a helyi tároló egyik erőforrásához.

A helyi tároló-erőforrás létrehozása, vegye fel a [LocalResources] szakaszban a [ServiceDefinition.csdef] fájlt, majd adja hozzá a [LocalStorage] gyermekelemet. A helyi tároló-erőforrás adjon egyedi nevet és egy megfelelő méretű az indítási tevékenységhez.

Az indítási feladat a helyi tároló egyik erőforrásához használatához szüksége egy környezeti változó hivatkozást a helyi erőforrás tárolóhely létrehozásához. Ezután a indítási feladat és az alkalmazás képesek fájlok olvasása és írása a helyi tároló-erőforrás.

A fontos szakasza a **ServiceDefinition.csdef** fájl itt látható:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Tegyük fel ez **Startup.cmd** batch-fájlt használ a **PathToStartupStorage** környezeti változó létrehozása a fájl **MyTest.txt** a helyi tárolóhelyen.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Elérhető helyi tároló mappát az Azure SDK használatával a [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metódust.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Futtassa az emulátor vagy a felhőben
Az eltérő lépésekkel, ha működik a felhőben, ha a compute emulator a képest indítási feladat lehet. Például előfordulhat, hogy használandó friss másolati példányának SQL-adatok csak az emulátorban futtatásakor. Vagy előfordulhat, hogy szeretné a felhőben, amelyek nem kell tennie, ha az emulátorban futtatja az egyes teljesítményoptimalizálását.

Így meghatározhatja, hogy a compute emulator és a felhő végre különböző műveleteket érhető el a környezeti változó létrehozása a [ServiceDefinition.csdef] fájlt. Az indítási tevékenységhez érték környezeti változó tesztelje.

A környezeti változó létrehozása, vegye fel a [változó]/[RoleInstanceValue] elemet, és hozzon létre egy XPath értékét `/RoleEnvironment/Deployment/@emulated`. Értékét a **ComputeEmulatorRunning %** környezeti változó `true` futtatásakor a compute emulator és `false` a felhő futtatásakor.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

A feladat most ellenőrizheti a **ComputeEmulatorRunning %** környezeti változó különböző műveleteket hajt végre a szerepkör fut-e a felhő vagy az emulátorban a alapján. Íme egy .cmd héjparancsfájlt, amely ellenőrzi, hogy a környezeti változó.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Észleli, hogy a feladat már futott
A szerepkör, amely az indítási feladatok futtassa újra az újraindítás nélkül indítsa újra a is. Nincs a jelzőt, amely jelzi, hogy a feladat már futott a üzemeltetési virtuális Gépre van. Előfordulhat, hogy egyes feladatokat hol nem számít, hogy futnak-e több alkalommal. Azonban az olyan helyzet, ahol meg kell akadályozni a feladat futtatását egynél többször is futtathatja.

A legegyszerűbben úgy észleli, hogy a feladat már futott, hogy a fájl létrehozása a **% TEMP %** mappát, ha a feladat sikeres volt, és keresse meg azt a feladat elején. Íme egy minta cmd héjparancsfájlt, amelyet, amely meg.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%RoleRoot%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>A feladat gyakorlati tanácsok
Az alábbiakban néhány gyakorlati tanácsok a feladatot a webes vagy feldolgozói szerepkör konfigurálásakor kell követnie.

### <a name="always-log-startup-activities"></a>Mindig a napló indítási tevékenységek
A Visual Studio nem biztosít a hibakereső gombra kattintva végighaladhat parancsfájlokat, ezért ezt a megfelelő parancsfájlokat működéséről a lehető legtöbb adatok eléréséhez. A parancsfájlokat, naplózására mindkét **stdout** és **stderr**, tudhatja meg fontos információk hibakeresését és javítsa ki a parancsfájlokat tett kísérlet során. Mindkét bejelentkezni **stdout** és **stderr** a StartupLog.txt a fájl a könyvtárban által hivatkozott a **% TEMP %** környezeti változó, adja hozzá a szöveget `>>  "%TEMP%\\StartupLog.txt" 2>&1` végéig adott sorok bejelentkezik. Ahhoz például, hogy a setup.exe hajtható végre a **PathToApp1Install %** könyvtár:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Egyszerűbbé teheti az XML-kódot, hozzon létre egy burkoló *cmd* meghívó összes az indítási és naplózás feladatokkal, és biztosítja, hogy minden gyermek-tevékenység osztja meg ugyanazt a környezeti változók.

Előfordulhat, hogy találja zavaró való használata `>> "%TEMP%\StartupLog.txt" 2>&1` minden indítási tevékenységhez végén. Hozzon létre egy burkoló, amely kezeli az Ön naplózási kényszerítheti a feladat naplózás. A burkoló meghívja a futtatni kívánt valós kötegfájlt futtat. A célfájl kötegelt bármely olyan kimenete irányítja át a *Startuplog.txt* fájlt.

A következő példa bemutatja, hogyan indítási parancsfájlt minden kimenetét. Ebben a példában a ServerDefinition.csdef hoz létre, amely behívja indítási feladat *logwrap.cmd*. *logwrap.cmd* hívások *Startup2.cmd*, minden kimenet átirányítása **% TEMP %\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

A kimeneti példa a **StartupLog.txt** fájlt:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> A **StartupLog.txt** fájl található a *C:\Resources\temp\\{szerepkör-azonosító} \RoleTemp* mappa.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Állítsa be megfelelően az indítási feladatok executionContext
Állítsa be megfelelően az indítási tevékenységhez tartozó jogosultságok. Néha indítási feladatok futtatásához emelt szintű jogosultságokkal annak ellenére, hogy a szerepkört futtató normál jogosultsággal.

A [executionContext][feladat] attribútum állítja be az indítási feladat a jogosultsági szint. Használatával `executionContext="limited"` azt jelenti, hogy az indítási feladat a szerepkör az azonos jogosultsági szinten. Használatával `executionContext="elevated"` azt jelenti, hogy az indítási tevékenységhez rendszergazdai jogosultságokkal rendelkezik, amely lehetővé teszi, hogy a rendszergazdai feladatok elvégzéséhez rendszergazdai jogosultságokkal a szerepkör nélkül a indítási feladat.

Emelt szintű jogosultság szükséges indítási tevékenységhez példa, hogy egy indítási feladat által használt **AppCmd.exe** IIS konfigurálásához. **AppCmd.exe** szükséges `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Használja a megfelelő taskType
A [taskType][feladat] attribútum meghatározza, hogy a módját a indítási feladat végrehajtása. Három értékei: **egyszerű**, **háttér**, és **előtér**. A háttér és előtérben történő műveleteket aszinkron módon indulnak el, és majd az egyszerű feladatokat végre párhuzamosan egyenként.

A **egyszerű** indítási feladatok állíthatja be a sorrendben, amelyben a feladatokat a ServiceDefinition.csdef fájlban felsorolt által a feladatok futásának sorrendje. Ha egy **egyszerű** feladat egy nem nulla kilépési kóddal véget ér, akkor az indítási eljárás leáll, és a szerepkör nem indul el.

A különbség a között **háttér** indítási feladatok és **előtér** indítási feladatok, hogy **előtér** feladatok megőrzése a szerepkört, amíg nem fut a  **előtérben** feladat befejeződik. Ez azt is jelenti, hogy ha a **előtér** feladat válaszol vagy összeomlik, a szerepkör nem indul újra amíg a **előtér** feladat kényszeríti lezárva. Emiatt **háttér** feladatok ajánlott aszinkron indítási feladatok, kivéve, ha az adott szolgáltatást van szüksége a **előtér** feladat.

### <a name="end-batch-files-with-exit-b-0"></a>A 0 kilépési /B End parancsfájlokat
A szerepkör csak akkor kezdi, ha a **errorlevel** minden a egyszerű indítási tevékenységhez értéke nulla. Nem minden program beállítása a **errorlevel** (kilépési kód) megfelelően, ezért a parancsfájlt kell végződnie egy `EXIT /B 0` Ha minden megfelelően már futott.

Egy hiányzó `EXIT /B 0` indítási köteg végén fájl az oka leggyakrabban szerepköröket, amelyek nem indulnak el.

> [!NOTE]
> I észrevette, hogy a beágyazott kötegelt fájlok néha lefagy használata esetén a `/B` paraméter. Érdemes lehet győződjön meg arról, hogy lefagy a probléma nem fordulhat elő, ha egy másik köteg fájl az aktuális köteg fájl, például ha használja a [napló burkoló](#always-log-startup-activities). Akkor kihagyhatja a `/B` ebben az esetben paraméter.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Várt indítási feladatokat futtatni egynél többször
Nem minden szerepkör újrahasznosítja azt újraindítás többek között az összes szerepkör újrahasznosítja azt közé tartoznak az éppen futó összes indítási feladatok. Ez azt jelenti, hogy indítási feladatok többször is lefuthat gond nélkül újraindításnál képesnek kell lennie. Ez ismertet a [szakasz megelőző](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Használja a helyi tárolót tárolja a fájlokat kell elérni a szerepkörben
Ha szeretné másolni, vagy hozzon létre egy fájlt a indítási feladat, amely majd elérhető az Ön szerepköre alatt, majd a helyi tároló kell helyezni, hogy a fájl. Tekintse meg a [szakasz megelőző](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Következő lépések
Tekintse át a felhőbe [service modell és a csomag](cloud-services-model-and-package.md)

További tudnivalók [feladatok](cloud-services-startup-tasks.md) működik.

[Létrehozhat és telepíthet](cloud-services-how-to-create-deploy-portal.md) a cloud service-csomag.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[feladat]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[környezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[változó]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Végpontok]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
