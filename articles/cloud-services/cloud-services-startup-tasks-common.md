---
title: A Felhőszolgáltatások gyakori indítási feladatai | Microsoft dokumentumok
description: Néhány példa a felhőszolgáltatások webes szerepkörében vagy feldolgozói szerepkörében végrehajtandó gyakori indítási feladatokra.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 4fe1ee3ccf2849943959889838ba0f22fb64bb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273058"
---
# <a name="common-cloud-service-startup-tasks"></a>Gyakori felhőszolgáltatás indítási feladatok
Ez a cikk néhány példát mutat be a felhőszolgáltatásban esetleg végrehajtani kívánt gyakori indítási feladatokra. Az indítási feladatok segítségével műveleteket hajthat végre a szerepkör indítása előtt. A végrehajtandó műveletek közé tartozik egy összetevő telepítése, COM-összetevők regisztrálása, beállítási kulcsok beállítása vagy egy hosszú ideig futó folyamat elindítása. 

Ebből [a cikkből](cloud-services-startup-tasks.md) megtudhatja, hogyan működnek az indítási feladatok, és hogyan hozhat létre indítási feladatot meghatározó bejegyzéseket.

> [!NOTE]
> Az indítási feladatok nem alkalmazhatók a virtuális gépekre, csak a Cloud Service Web és a Worker szerepkörökre.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Környezeti változók definiálása a szerepkör indítása előtt
Ha egy adott feladathoz definiált környezeti változókra van szüksége, használja a [Környezeti] elemet a [Feladat] elemen belül.

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

Változók is használhat érvényes [Azure XPath-érték](cloud-services-role-config-xpath.md) hivatkozik valamit a központi telepítést. Az `value` attribútum használata helyett adjon meg egy [RoleInstanceValue] gyermekelemet.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Az IIS indításának konfigurálása az AppCmd.exe programmal
Az [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) parancssori eszköz segítségével kezelheti az IIS-beállításokat indításkor az Azure-ban. *Az AppCmd.exe* kényelmes, parancssori hozzáférést biztosít az Azure indítási feladataihoz használható konfigurációs beállításokhoz. Az *AppCmd.exe*használatával a webhely beállításai hozzáadhatók, módosíthatók vagy eltávolíthatók az alkalmazásokhoz és webhelyekhez.

Van azonban néhány dolog, amire figyelni kell az *AppCmd.exe* indítási feladatként való használata során:

* Az indítási feladatok többször is futtathatók az újraindítások között. Például, ha egy szerepkör újrahasznosítja.
* Ha egy *AppCmd.exe* műveletet többször hajt végre, az hibát okozhat. Ha például megpróbál kétszer hozzáadni egy szakaszt a *Web.config* fájlhoz, az hibát okozhat.
* Az indítási feladatok sikertelenek, ha nem nulla kilépési kódot vagy **hibaszintet**adnak vissza. Ha például *az AppCmd.exe* hibát generál.

Az *AppCmd.exe hívása*után célszerű ellenőrizni a **hibaszintet,** ami könnyen ellátható, ha az *AppCmd.exe* fájlba csomagolja a hívást. *.cmd* Ha ismert **hibaszintű** választ észlel, figyelmen kívül hagyhatja, vagy visszaadhatja.

Az *AppCmd.exe* által visszaadott hibaszint szerepel a winerror.h fájlban, és az [MSDN](/windows/desktop/Debug/system-error-codes--0-499-)-en is látható.

### <a name="example-of-managing-the-error-level"></a>Példa a hibaszint kezelésére
Ez a példa egy tömörítési szakaszt és egy JSON-tömörítési bejegyzést ad a *Web.config* fájlhoz, hibakezeléssel és naplózással.

A [ServiceDefinition.csdef] fájl vonatkozó szakaszai itt láthatók, amelyek közé tartozik `elevated` a [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) attribútum beállítása, hogy az *AppCmd.exe* megfelelő engedélyekkel módosítsa a *web.config* fájl beállításait:

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

A *Startup.cmd* kötegfájl *az AppCmd.exe* programot használja tömörítési szakasz és a JSON tömörítési bejegyzésének hozzáadásához a *Web.config* fájlhoz. A várt **183-as hibaszint** nullára van állítva a VERIFY használatával. EXE parancssori program. A startuperrorlog.txt fájl nem várt hibaszinteket naplóz.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

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

## <a name="add-firewall-rules"></a>Tűzfalszabályok hozzáadása
Az Azure-ban gyakorlatilag két tűzfal van. Az első tűzfal vezérli a virtuális gép és a külvilág közötti kapcsolatokat. Ezt a tűzfalat a [ServiceDefinition.csdef] fájl [Végpontok] eleme vezérli.

A második tűzfal szabályozza a virtuális gép és a virtuális gépen belüli folyamatok közötti kapcsolatokat. Ezt a tűzfalat a `netsh advfirewall firewall` parancssori eszköz vezérelheti.

Az Azure tűzfalszabályokat hoz létre a szerepkörökön belül elindított folyamatokhoz. Például amikor elindít egy szolgáltatást vagy programot, az Azure automatikusan létrehozza a szükséges tűzfalszabályokat, hogy a szolgáltatás kommunikáljon az internettel. Ha azonban olyan szolgáltatást hoz létre, amelyet a szerepkörén kívüli folyamat (például egy COM+ szolgáltatás vagy egy Windows ütemezett feladat) indít el, manuálisan kell létrehoznia egy tűzfalszabályt a szolgáltatás hozadékának engedélyezéséhez. Ezek a tűzfalszabályok indítási feladattal hozhatók létre.

A tűzfalszabályt létrehozó indítási feladatnak **emelt**szintű [executionContext][feladattal] kell rendelkeznie. Adja hozzá a következő indítási feladatot a [ServiceDefinition.csdef] fájlhoz.

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

A tűzfalszabály hozzáadásához az indítókötegfájlban a megfelelő `netsh advfirewall firewall` parancsokat kell használnia. Ebben a példában az indítási feladat a 80-as TCP-port biztonságát és titkosítását igényli.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Adott IP-cím letiltása
Az IIS **web.config** fájljának módosításával korlátozhatja az Azure-webes szerepkör-hozzáférést egy megadott IP-címek készletéhez. Olyan parancsfájlt is használnia kell, amely feloldja az **ApplicationHost.config** fájl **ipSecurity** szakaszát.

Az **ApplicationHost.config** fájl **ipSecurity** szakaszának feloldásához hozzon létre egy parancsfájlt, amely a szerepkör indításakor fut. Hozzon létre egy **mappát** a webes szerepkör gyökérszintjén, az úgynevezett indításkor, és ebben a mappában hozzon létre egy **startup.cmd**nevű kötegfájlt. Adja hozzá ezt a fájlt a Visual Studio-projekthez, és állítsa a tulajdonságokat **Másolás mindig** beállításra, hogy biztosan szerepeljen a csomagban.

Adja hozzá a következő indítási feladatot a [ServiceDefinition.csdef] fájlhoz.

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

Adja hozzá ezt a parancsot az **indítási.cmd** fájlhoz:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Ez a feladat hatására az **indítási.cmd** kötegfájl minden alkalommal, amikor a webes szerepkör inicializálása, biztosítva, hogy a szükséges **ipSecurity** szakasz fel van oldva.

Végül módosítsa a [system.webServer szakaszt](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) a webes szerepkör **web.config** fájljában, hogy hozzáadjon egy hozzáférést kapó IP-címeket, ahogy az a következő példában látható:

Ez a minta konfiguráció **lehetővé teszi,** hogy az összes IP-k hozzáférjenek a szerverhez, kivéve a két

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

Ez a minta **konfigurációs megtagadja** az összes IP-kiszolgáló eléréséhez, kivéve a két definiált.

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

## <a name="create-a-powershell-startup-task"></a>PowerShell-indítási feladat létrehozása
A Windows PowerShell-parancsfájlok nem hívhatók meg közvetlenül a [ServiceDefinition.csdef] fájlból, de indítási kötegfájlból is meghívhatók.

A PowerShell (alapértelmezés szerint) nem futtat aláíratlan parancsfájlokat. Ha nem írja alá a parancsfájlt, konfigurálnia kell a PowerShellt az aláíratlan parancsfájlok futtatásához. Aláíratlan parancsfájlok futtatásához a **ExecutionPolicy** beállításnak Korlátlan beállításra kell **állítania.** A **használt ExecutionPolicy** beállítás a Windows PowerShell verzióján alapul.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Ha egy Olyan vendég operációs rendszert használ, amely a PowerShell 2.0-s vagy 1.0-s verzióját futtatja, kényszerítheti a 2-es verziót, és ha nem érhető el, használja az 1-es verziót.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Fájlok létrehozása a helyi tárolóban indítási feladatból
A helyi tárolási erőforrás segítségével tárolhatja az indítási feladat által létrehozott fájlokat, amelyeket később az alkalmazás elért.

A helyi tárolási erőforrás létrehozásához vegyen fel egy [LocalResources] szakaszt a [ServiceDefinition.csdef] fájlba, majd adja hozzá a [LocalStorage] gyermekelemet. Adjon a helyi tárolóerőforrásnak egyedi nevet és megfelelő méretet az indítási feladathoz.

Ha helyi tárolási erőforrást szeretne használni az indítási feladatban, létre kell hoznia egy környezeti változót, amely a helyi tárolási erőforrás helyére hivatkozik. Ezután az indítási feladat és az alkalmazás képes olvasni és írni a fájlokat a helyi tárolási erőforrás.

A **ServiceDefinition.csdef** fájl vonatkozó szakaszai itt láthatók:

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

Ez a **Startup.cmd** kötegfájl például a **PathToStartupStorage** környezeti változót használja a **MyTest.txt** fájl helyi tárolóhelyen történő létrehozásához.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

A [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metódus használatával elérheti a helyi tárolómappát az Azure SDK-ból.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Futtatás az emulátorban vagy a felhőben
Beállíthatja, hogy az indítási feladat különböző lépéseket hajtson végre, amikor a felhőben működik, mint amikor a számítási emulátorban van. Előfordulhat például, hogy csak akkor szeretné az SQL-adatok friss másolatát használni, ha az emulátorban fut. Vagy előfordulhat, hogy szeretne néhány teljesítményoptimalizálása a felhő, amely nem kell tennie, ha fut az emulátor.

Ez a képesség, hogy különböző műveleteket hajtson végre a számítási emulátor és a felhő lehet elérni egy környezeti változó a [ServiceDefinition.csdef] fájlban. Ezután tesztelje a környezeti változót az indítási feladat egy értékére.

A környezeti változó létrehozásához adja hozzá a [Variable]/[RoleInstanceValue] elemet, és hozzon létre egy XPath `/RoleEnvironment/Deployment/@emulated`értéket. A **%ComputeEmulatorRunning%** környezeti változó `true` értéke a számítási emulátoron `false` és a felhőben való futtatáskor.

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

A feladat most már ellenőrizheti a **%ComputeEmulatorRunning% környezeti változót,** hogy különböző műveleteket hajtson végre attól függően, hogy a szerepkör a felhőben vagy az emulátorban fut-e. Itt van egy .cmd shell script, amely ellenőrzi az adott környezeti változó.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Annak észlelése, hogy a feladat már lefutott
A szerepkör újraindítás nélkül újrahasznosítható, ami az indítási feladatok ismételt futtatását okozza. Nincs jelző, amely azt jelzi, hogy egy feladat már fut a gazdagép virtuális gép. Előfordulhat, hogy vannak olyan feladatok, ahol nem számít, hogy többször is futnak. Előfordulhat azonban, hogy olyan helyzetbe kerül, amelyben meg kell akadályoznia, hogy egy feladat egynél többször fusson.

A feladat futtatásának észlelésének legegyszerűbb módja, ha a feladat sikeres végrehajtása esetén létrehoz egy fájlt a **%TEMP%** mappában, és a feladat kezdetén megkeresi azt. Itt van egy minta cmd shell script, hogy nem, hogy az Ön számára.

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

## <a name="task-best-practices"></a>Gyakorlati tanácsok a feladatokra
Az alábbiakban néhány ajánlott eljárás, amelyet követnie kell a webes vagy feldolgozói szerepkör feladatának konfigurálásakor.

### <a name="always-log-startup-activities"></a>Mindig naplózza az indítási tevékenységeket
A Visual Studio nem biztosít hibakeresőt a kötegfájlok átdolgozásához, ezért érdemes minél több adatot beszerezni a kötegfájlok működéséről. A kötegfájlok kimenetének naplózása mind **az stdout,** mind **az stderr**, fontos információkat adhat a kötegfájlok hibakeresése és javítása során. Ha a (%TEMP% ) környezeti változó által a **(%TEMP% környezeti** változó) által mutatott könyvtárban `>>  "%TEMP%\\StartupLog.txt" 2>&1` lévő StartupLog.txt fájlba szeretne naplózni az **stdout** és **stderr** fájlt, adja hozzá a szöveget a naplózni kívánt sorok végéhez. Például a setup.exe fájl végrehajtása a **%PathToApp1Install%** könyvtárban:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Az xml egyszerűsítése érdekében létrehozhat egy burkoló *cmd* fájlt, amely az összes indítási feladatot meghívja a naplózással együtt, és biztosítja, hogy minden gyermekfeladat ugyanazokat a környezeti változókat ossza meg.

Lehet, hogy bosszantó, `>> "%TEMP%\StartupLog.txt" 2>&1` bár használni a végén minden indítási feladat. A feladatnaplózást úgy kényszerítheti ki, hogy létrehoz egy burkolót, amely kezeli a naplózást. Ez a burkoló a futtatni kívánt valódi kötegfájlt hívja meg. A célkötegfájlból származó kimenetek a *Startuplog.txt* fájlba lesznek átirányítva.

A következő példa bemutatja, hogyan lehet átirányítani az összes kimenetet egy indítási kötegfájlból. Ebben a példában a ServerDefinition.csdef fájl létrehoz egy indítási feladatot, amely meghívja *a logwrap.cmd fájlt.* *logwrap.cmd* hívja *az Startup2.cmd*fájlt, az összes kimenetát átirányítja **a %TEMP%\\StartupLog.txt fájlba.**

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

**Indítás2.cmd:**

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

Mintakimenet a **StartupLog.txt** fájlban:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> A **StartupLog.txt** fájl a *C:\Resources\temp\\{role identifier}\RoleTemp* mappában található.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>A executionContext beállítása az indítási feladatokhoz
Állítsa be az indítási feladathoz megfelelő jogosultságokat. Előfordulhat, hogy az indítási feladatoknak emelt szintű jogosultságokkal kell futniuk, még akkor is, ha a szerepkör normál jogosultságokkal fut.

A [executionContext][Task] attribútum beállítja az indítási feladat jogosultsági szintjét. Azt `executionContext="limited"` jelenti, hogy az indítási feladat jogosultsági szintje megegyezik a szerepkörjogosultsági szinttel. Az `executionContext="elevated"` indítási feladat használata rendszergazdai jogosultságokkal rendelkezik, ami lehetővé teszi, hogy az indítási feladat rendszergazdai feladatokat hajtson végre anélkül, hogy rendszergazdai jogosultságokat adna a szerepkörnek.

Emelt szintű jogosultságokat igénylő indítási feladat például olyan indítási feladat, amely **az AppCmd.exe** programot használja az IIS konfigurálásához. **Az AppCmd.exe** használatához szükséges `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>A megfelelő feladattípus használata
A [taskType][Task] attribútum határozza meg az indítási feladat végrehajtásának módját. Három érték létezik: **egyszerű**, **háttér**, és **az előtérben**. A háttér- és előtérfeladatok aszinkron módon kezdődnek, majd az egyszerű feladatok végrehajtása egyesével történik.

**Egyszerű** indítási feladatokkal beállíthatja, hogy a feladatok milyen sorrendben fussanak a ServiceDefinition.csdef fájlban. Ha egy **egyszerű** feladat nem nulla kilépési kóddal végződik, akkor az indítási eljárás leáll, és a szerepkör nem indul el.

A **háttérindítási** feladatok és **az előtérindítási** feladatok közötti különbség az, hogy **az előtér-feladatok** az előtér-feladatok szerint futnak, amíg az **előtér-feladat** véget nem ér. Ez azt is jelenti, hogy ha az **előtérfeladat** lefagy vagy összeomlik, a szerepkör nem újrahasznosítja, amíg az **előtérfeladat** be nem zárul. Ezért a **háttérfeladatok** ajánlottak az aszinkron indítási feladatokhoz, kivéve, ha az **előtérben lévő** feladat nak erre a szolgáltatására van szükség.

### <a name="end-batch-files-with-exit-b-0"></a>Kötegfájlok befejezése az EXIT /B 0 kapcsolóval
A szerepkör csak akkor indul el, ha az egyszerű indítási feladatok **hibaszintje** nulla. Nem minden program állítja be helyesen a **hibaszintet** (kilépési `EXIT /B 0` kódot), így a kötegfájlnak akkor kell végződnie, ha minden megfelelően futott.

Az `EXIT /B 0` indítási kötegfájl végén hiányzó szerepkörök gyakori oka, amelyek nem indulnak el.

> [!NOTE]
> Észrevettem, hogy a beágyazott kötegfájlok néha `/B` lefagynak a paraméter használatakor. Győződjön meg arról, hogy ez a lefagyási probléma nem fordul elő, ha egy másik kötegfájl hívja meg az aktuális kötegfájlt, például ha a [naplóburkolót](#always-log-startup-activities)használja. Ebben az esetben `/B` kihagyhatja a paramétert.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Indítási feladatok többszöri futtatása várható
Nem minden szerepkör-újrahasznosítási tartalmaz egy újraindítás, de minden szerepkör újrahasznosítja az összes indítási feladatok futtatását. Ez azt jelenti, hogy az indítási feladatoknak minden gond nélkül többször is futniuk kell az újraindítások között. Ezt az előző [szakasz](#detect-that-your-task-has-already-run)tárgyalja .

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Helyi tároló használata a szerepkörben elérendő fájlok tárolására
Ha az indítási feladat során olyan fájlt szeretne másolni vagy létrehozni, amely a szerepkör számára elérhető, akkor a fájlt a helyi tárolóba kell helyezni. Lásd az [előző szakaszt](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>További lépések
Tekintse át a [felhőszolgáltatás modelljét és csomagját](cloud-services-model-and-package.md)

További információ a [Feladatok működéséről.](cloud-services-startup-tasks.md)

[Hozza létre és telepítse](cloud-services-how-to-create-deploy-portal.md) a felhőszolgáltatási csomagot.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tevékenység]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Környezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Változó]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue érték]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Végpontok]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[Localstorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[Helyi források]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue érték]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue



