---
title: Gyakori indítási feladatok Cloud Serviceshez |} A Microsoft Docs
description: Gyakori indítási feladatok végrehajtásához a cloud services webes szerepkör vagy a feldolgozói szerepkör érdemes példákat.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 2aa32d75edbacdfff6428a6526d9898cb6732d6b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534735"
---
# <a name="common-cloud-service-startup-tasks"></a>Gyakori Felhőszolgáltatás indítási feladatok
Ez a cikk példákat néhány gyakori indítási feladatok, érdemes lehet végrehajtani a cloud service-ben. Indítási feladatok segítségével hajtsa végre a műveleteket a szerepkör elindítása előtt. Esetlegesen végrehajtani kívánt műveletek közé tartozik egy összetevő telepítése, COM-összetevők regisztrálása, beállításkulcsokat vagy hosszú ideig futó folyamat indítása. 

Lásd: [Ez a cikk](cloud-services-startup-tasks.md) indítási feladatok működését, és kifejezetten a bejegyzéseket, amelyek meghatározzák egy indítási feladat létrehozásához.

> [!NOTE]
> Indítási feladatok nem vonatkoznak, a virtuális gépek, csak felhőalapú szolgáltatás webes és feldolgozói szerepkörök.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Környezeti változókat határozhat meg, a szerepkör elindítása előtt
Ha egy adott tevékenységhez meghatározott környezeti változók van szüksége, használja a [környezet] elem belül a [Tevékenység] elemet.

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

Változók is használhatja a [érvényes Azure XPath értéket](cloud-services-role-config-xpath.md) való hivatkozáshoz az üzembe helyezéssel kapcsolatos hiba. Használata helyett a `value` attribútumot, meghatározása egy [RoleInstanceValue] gyermekelemet.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurálja az IIS indítási AppCmd.exe
A [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) parancssori eszköz használható az Azure-ban való indításkor az IIS-beállítások kezelése. *AppCmd.exe* kényelmes, a parancssori hozzáférést biztosít a konfigurációs beállításokhoz használt indítási feladatok az Azure-ban. Használatával *AppCmd.exe*, webhely beállításainak hozzáadhatja, módosítás, vagy eltávolítva a alkalmazásokat és webhelyeket.

Vannak azonban néhány dolgot figyelheti a használatát a *AppCmd.exe* indítási feladatként:

* Indítási feladatok újraindításnál csak egyszer futtatható. Például ha a szerepkör újraindul.
* Ha egy *AppCmd.exe* művelet többször történik, akkor előfordulhat, hogy egy hiba jön létre. Például próbál meg hozzáadni egy olyan szakasz *Web.config* kétszer kívánatosnál hiba.
* Indítási feladatok sikertelen, ha egy nem nulla értékű kilépési kóddal tér vissza, vagy **errorlevel**. Például, hogy amikor *AppCmd.exe* hibát jelez.

Egy célszerű ellenőrizni a **errorlevel** hívása után *AppCmd.exe*, könnyen teendő, ha a hívást burkolása *AppCmd.exe* együtt egy *.cmd* fájlt. Ha azt észleli, hogy egy ismert **errorlevel** választ, akkor figyelmen kívül hagyása, vagy adja át azt vissza.

Az által visszaadott errorlevel *AppCmd.exe* szerepelnek a winerror.h fájlt, és az is látható [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>A hiba szintje kezelése – példa
Ebben a példában hozzáad egy tömörítési fejezetet, és a egy tömörítési bejegyzést JSON-a *Web.config* fájllal hiba- és naplózás.

A vonatkozó részt a [ServiceDefinition.csdef] fájl látható itt, többek között a beállítás a [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) attribútumot `elevated` biztosíthat *AppCmd.exe* módosítása a megfelelő engedélyekkel a *Web.config* fájlt:

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

A *Startup.cmd* batch-fájlt használ *AppCmd.exe* JSON-tömörítés szakasz és a egy tömörítési bejegyzés hozzáadása a *Web.config* fájlt. A várt **errorlevel** 183, értéke nulla használatával az győződjön meg arról. EXE parancssori program. Váratlan errorlevels StartupErrorLog.txt naplózása.

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
Az Azure-ban a rendszer lényegében két tűzfal. Az első tűzfal azt szabályozza, hogy a virtuális gép és a külvilág közötti kapcsolatokat. Ez a tűzfal vezérli a [végpontok] eleme a [ServiceDefinition.csdef] fájlt.

A második tűzfal azt szabályozza, hogy a virtuális gép és a virtuális gépen belül a folyamatok közötti kapcsolatok. Ez a tűzfal szabályozhatja a `netsh advfirewall firewall` parancssori eszköz.

Az Azure létrehozza a tűzfalszabályok a folyamatok, a szerepkörök belüli használatába. Például amikor egy szolgáltatás vagy program, az Azure automatikusan létrehozza a szükséges tűzfalszabályok, hogy a szolgáltatás az internettel való kommunikációhoz. Azonban ha létrehoz egy szolgáltatást, amely a szerepkör (például egy COM + szolgáltatás vagy a Windows ütemezett feladat) kívül a folyamat elindul, meg kell manuálisan hozzon létre egy tűzfalszabályt, hogy a szolgáltatáshoz való hozzáférés engedélyezése. Ezek a tűzfal-szabályok egy indítási feladat segítségével hozható létre.

Rendelkeznie kell egy indítási feladat, amely létrehoz egy tűzfalszabályt egy [executionContext][tevékenység] , **emelt szintű**. Adja hozzá a következő indítási feladat a [ServiceDefinition.csdef] fájlt.

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

A tűzfalszabály hozzáadásához kell használnia a megfelelő `netsh advfirewall firewall` parancsok a indítási parancsfájlba. Ebben a példában az indítási feladat 80-as porton biztonság és titkosítás szükséges.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Egy adott IP-cím blokkolása
Korlátozhatja az Azure webes szerepkör hozzáférési megadott IP-címek egy készletének az IIS módosításával **web.config** fájlt. Is kell használnia, amely feloldja a parancsfájl a **ipSecurity** szakaszában a **ApplicationHost.config** fájlt.

Feloldásához a **ipSecurity** szakaszában a **ApplicationHost.config** hozzon létre egy parancsfájlt, amely a szerepkör indítása. Hozzon létre egy mappát a webes szerepkör nevű gyökérszinten **indítási** és belül ebben a mappában hozzon létre egy kötegfájlt, nevű **startup.cmd**. Ez a fájl hozzáadása a Visual Studio-projektben, és állítsa be a tulajdonságokat **mindig Másolás** annak érdekében, hogy a csomag tartalmazza.

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

Ez a feladat futtatásakor a **startup.cmd** batch fájl futtatását minden alkalommal, amikor a webes szerepkör inicializálva, biztosítva, hogy a szükséges **ipSecurity** szakasz fel oldva.

Végül módosítsa úgy a [system.webServer szakasz](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) a webes szerepkör **web.config** fájlját, hogy hozzáféréssel, IP-címek listáját az alábbi példában látható módon:

Ez a minta-konfiguráció **lehetővé teszi, hogy** a kiszolgálón, kivéve a két meghatározott összes IP-címek

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

Ez a minta-konfiguráció **megtagadja** nem férhet hozzá a kiszolgáló kivételével a két meghatározott összes IP-címek.

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
Windows PowerShell-parancsfájlok nem hívható meg közvetlenül a [ServiceDefinition.csdef] fájlt, de ezek is elindítható az indítási parancsfájlban.

PowerShell (alapértelmezés szerint) nem működik az aláíratlan parancsfájlok. Hacsak nem jelentkezik a parancsfájlt, a nem aláírt parancsfájlok futtatásához a PowerShell konfigurálása kell. A nem aláírt parancsfájlok futtatásához a **ExecutionPolicy** értékre kell állítani **Unrestricted**. A **ExecutionPolicy** beállításhoz tartozó használat alapján a Windows PowerShell-verzió.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Ha a vendég operációs rendszer, amely futtatja a PowerShell 2.0 vagy kényszerítheti, hogy futtassa a 2-es 1.0-t használ, és nem érhető el, használja az 1-es verzió.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Fájlok létrehozása a helyi tárolóban egy indítási feladat
Az indítási feladat, amely később érhető el az alkalmazás által létrehozott fájlok tárolására használhatja a helyi tároló egyik erőforrásához.

A helyi tároló-erőforrás létrehozásához adja hozzá a [LocalResources] részt a [ServiceDefinition.csdef] fájlt, majd adja hozzá a [LocalStorage] gyermekelemet. A helyi erőforrást adjon egy egyedi nevet és egy megfelelő méretet az indítási tevékenység.

Az indítási feladat a helyi tároló egyik erőforrásához használatához szeretne létrehozni egy környezeti változót, a helyi tároló-erőforrás helye hivatkozhat. Ezután az indítási feladat és az alkalmazás is olvashat és írhat fájlokat, a helyi erőforrást.

A vonatkozó részt a **ServiceDefinition.csdef** fájl itt látható:

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

Tegyük fel ez **Startup.cmd** batch-fájlt használ a **PathToStartupStorage** környezeti változót a fájl létrehozásához **MyTest.txt** helyi tároló helye határozza meg.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

A keresztül elérhető helyi tároló mappát az Azure SDK használatával a [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metódust.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Futtassa az emulátor, vagy a felhőben
Az indítási feladat eltérő lépésekkel, ha a felhőben, amikor a compute emulator képest működik is rendelkezhet. Például előfordulhat, hogy használni kívánt SQL-adatok friss másolatát csak akkor, ha az emulátorban fut. Vagy előfordulhat, hogy szeretne tenni bizonyos teljesítményoptimalizálás segíti, hogy nem kell tennie, ha az emulátorban futtatja a felhőben.

Hozzon létre egy környezeti változóhoz a képességgel, amellyel különböző műveleteket hajthat végre a compute emulator és a felhő valósítható meg a [ServiceDefinition.csdef] fájlt. Az indítási feladat egy értéket a környezeti változó tesztelésére.

A környezeti változó létrehozásához adja hozzá a [A változó]/[RoleInstanceValue] elemet, és hozzon létre egy XPath értékét `/RoleEnvironment/Deployment/@emulated`. Értékét a **ComputeEmulatorRunning %** környezeti változó `true` használatakor a compute emulator és `false` használatakor a felhő.

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

A feladat most ellenőrizheti a **ComputeEmulatorRunning %** környezeti változó különböző műveleteket hajt végre alapján a szerepkör fut-e a felhőben vagy az emulátorban. Íme egy .cmd héjparancsfájlt, amely ellenőrzi, hogy környezeti változót.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Észleli, hogy a feladat már futott
A szerepkör-újra futtatni az indítási feladatok okozó újraindítás nélkül előfordulhat, hogy újraindítás. Nincs Nincs jelölő jelzi, hogy a feladat már futott a üzemeltető virtuális gépen. Szükség lehet néhány feladatot, nem számít, hogy futnak-e több alkalommal. Felmerülhet azonban olyan helyzet, ahol meg kell akadályoznia a feladat egynél többször.

Észleli, hogy a feladat már futott a legegyszerűbb módja az, hogy hozzon létre egy fájlt a **% TEMP %** mappát, ha a feladat sikeres volt, és keresse meg azt a feladat elején. Íme egy példa cmd héjparancsfájlt, amely végez, amely az Ön számára.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

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

## <a name="task-best-practices"></a>A feladat ajánlott eljárások
Az alábbiakban néhány ajánlott eljárást kell követni a feladatot a webes és feldolgozói szerepkörök konfigurálásakor.

### <a name="always-log-startup-activities"></a>Mindig az indítási tevékenység naplózása
A Visual Studio nem biztosít hibakeresőt kötegfájlok elvégezhető, így hasznos a kötegelt fájlok működésére, a lehető legtöbb adatot beolvasása. A kimenet, kötegelt fájlok naplózása mindkét **stdout** és **stderr**, adhat meg fontos információkat hibakeresést, és javítsa ki a batch-fájlok tett kísérlet során. Való bejelentkezéshez mindkettőt **stdout** és **stderr** a StartupLog.txt a fájl a könyvtárban által hivatkozott a **% TEMP %** környezeti változót, adja hozzá a szöveget `>>  "%TEMP%\\StartupLog.txt" 2>&1` végére adott sorok bejelentkezik. Ha például a setup.exe végrehajtásához a **PathToApp1Install %** könyvtár:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Leegyszerűsítheti az XML-t, létrehozhat egy burkoló *cmd* , amely meghívja ezt az indítási összes fájl és naplózási feladatok, és biztosítja, hogy minden egyes gyermek-tevékenység megosztja az azonos környezeti változókat.

Előfordulhat, hogy találja zavaró, ha használandó `>> "%TEMP%\StartupLog.txt" 2>&1` indítási feladatokon végén. Hozzon létre egy burkoló, amely kezeli az Ön számára a naplózási tevékenység naplózása kényszeríthető. Ez a burkoló meghívja a futtatni kívánt valós kötegfájlt futtat. A célfájl batch bármely olyan kimenete átirányítjuk a *Startuplog.txt* fájlt.

Az alábbi példa bemutatja, hogyan indítási parancsfájlt minden kimenetét. Ebben a példában a ServerDefinition.csdef fájlt hoz létre egy indítási feladat, amely meghívja ezt *logwrap.cmd*. *logwrap.cmd* hívások *Startup2.cmd*, az összes parancskimenet **% TEMP %\\StartupLog.txt**.

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

A kimeneti mintát a **StartupLog.txt** fájlt:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> A **StartupLog.txt** fájl található, a *C:\Resources\temp\\{szerepkör azonosító} \RoleTemp* mappát.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Állítsa be megfelelően az indítási feladatok executionContext
Állítsa be az indítási feladat a megfelelő jogosultságokat. Néha az indítási feladatok kell futtassa megemelt jogosultságokkal annak ellenére, hogy a szerepkör normál jogosultságokkal futtatja.

A [executionContext][tevékenység] attribútum állítja be a jogosultsági szintet, az indítási feladat. Használatával `executionContext="limited"` azt jelenti, hogy az indítási feladat a szerepkör azonos jogosultsági szintre. Használatával `executionContext="elevated"` azt jelenti, hogy az indítási feladat rendszergazdai jogosultságokkal rendelkezik, amely lehetővé teszi, hogy a rendszergazdai feladatok elvégzéséhez rendszergazdai jogosultságokkal a szerepköre anélkül indítási feladat.

Egy indítási feladat emelt szintű jogosultságok szükségesek, például a egy indítási feladat által használt **AppCmd.exe** kívánja konfigurálni az IIS. **AppCmd.exe** igényel `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>A megfelelő taskType használata
A [taskType][tevékenység] attribútum meghatározza, hogy az indítási feladat úgy hajtja végre. Három értékek: **egyszerű**, **háttér**, és **előtérbeli**. A háttér-információkért és előtérben futó feladatok aszinkron módon futnak, és ezután az egyszerű feladatok végrehajtásának szinkron módon egyenként.

A **egyszerű** indítási feladatok, beállíthatja a sorrend, amelyben a feladatokat a ServiceDefinition.csdef fájlban felsorolt szerint a feladatok futásának sorrendje. Ha egy **egyszerű** feladat nullától eltérő kilépési kódot végződik, akkor az indítási eljárás leáll, és a szerepkör nem indul el.

A különbség a között **háttér** indítási feladatok és **előtér** indítási feladatok, hogy **előtérbeli** feladatok tartsa a szerepkört, amíg nem fut a  **előtérbeli** feladat véget ér. Ez azt is jelenti, hogy ha a **előtérbeli** feladat válaszol vagy összeomlik, a szerepkör lesz indul-ig a **előtérbeli** feladat kényszerítetten zárva. Ebből kifolyólag **háttér** feladatok ajánlott aszinkron indítási feladatok, ha nincs szükség az adott szolgáltatást a **előtérbeli** feladat.

### <a name="end-batch-files-with-exit-b-0"></a>A 0 kilépési /B záró kötegfájlok
A szerepkör csak fog elindulni, ha a **errorlevel** az egyes az egyszerű indítási feladat értéke nulla. Nem minden program állítsa be a **errorlevel** (kilépési kód) megfelelően, így a batch-fájlt kell végződnie egy `EXIT /B 0` Ha mindent megfelelően futott-e.

Egy hiányzó `EXIT /B 0` indítási köteg végén található fájl általában az okozza, hogy ne indítsa el a szerepkörök.

> [!NOTE]
> Már észrevette, hogy a beágyazott batch fájlok néha lefagy használatakor a `/B` paraméter. Győződjön meg arról, hogy lefagy a probléma nem fordulhat elő, ha egy másik köteg fájlt az aktuális kötegfájlt, például ha használja, érdemes a [log burkoló](#always-log-startup-activities). Kihagyhatja a `/B` ebben az esetben a paraméter.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Indítási feladatok futtatásának egynél többször várható
Nem minden szerepkör-újrahasznosítást tartalmazzák a számítógép újraindítása, de az összes szerepkör-újrahasznosítást közé tartozik az összes indítási feladatok futtatása. Ez azt jelenti, hogy az indítási feladatok futtatható többször gond nélkül újraindítások között kell lennie. Ez a következő cikkben a [előző fejezet](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Használja a helyi tároló tárolja a fájlokat kell elérni a szerepkörben
Ha meg szeretné másolni, vagy hozzon létre egy fájlt az indítási feladat, amely ezután elérhető a szerepköre alatt, majd a helyi tároló kell elhelyezni ezt a fájlt. Tekintse meg a [előző fejezet](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>További lépések
Tekintse át a felhőbe [szolgáltatási modell és -csomag](cloud-services-model-and-package.md)

Tudjon meg többet [feladatok](cloud-services-startup-tasks.md) működik.

[Létrehozása és üzembe helyezése](cloud-services-how-to-create-deploy-portal.md) a felhőszolgáltatás-csomagok.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tevékenység]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Környezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[A változó]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Végpontok]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
