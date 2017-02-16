---
title: "Ismerkedés a Pythonnal és az Azure Cloud Serviceszel | Microsoft Docs"
description: "Azure felhőszolgáltatások, például webes és feldolgozói szerepkörök létrehozása a Python Tools for Visual Studio eszközzel."
services: cloud-services
documentationcenter: python
author: thraka
manager: timlt
editor: 
ms.assetid: 5489405d-6fa9-4b11-a161-609103cbdc18
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 712c62ed3d5ff21847e2431df3530e7d39c6aae2
ms.openlocfilehash: 53f7c17d8404bb02ab8bb2be9018ba2b179f1b08


---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Python webes és feldolgozói szerepkörök a Visual Studio eszközzel

> [!div class="op_single_selector"]
> * [.NET](cloud-services-dotnet-get-started.md)
> * [Node.js](cloud-services-nodejs-develop-deploy-app.md)
> * [Python](cloud-services-python-ptvs.md)

Ez a cikk a Python webes és feldolgozói szerepkörök [Python Tools for Visual Studio][Python Tools for Visual Studio] eszközben történő használatát ismerteti. Megtudhatja, hogyan hozhat létre és telepíthet egy, a Pythont használó alapszintű felhőszolgáltatást a Visual Studióval.

## <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2013 vagy 2015
* [Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK Tools for VS 2013][Azure SDK Tools for VS 2013] vagy [Azure SDK Tools for VS 2015][Azure SDK Tools for VS 2015]
* [Python 2.7 32 bites][Python 2.7 32 bites] vagy [Python 3.5 32 bites][Python 3.5 32 bites]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Mik a Python webes és feldolgozói szerepkörök?
Az Azure három számítási modellt biztosít az alkalmazások futtatásához: [Web Apps szolgáltatás az Azure App Service portálon][futtatási modell-webhelyek], [Azure virtuális gépek][végrehajtási modell virtuális gépek] és [Azure Cloud Services][végrehajtási modell felhőszolgáltatások]. Mindhárom modell támogatja a Python eszközt. A webes és feldolgozói szerepköröket is tartalmazó Cloud Services *platformszolgáltatást (PaaS)* kínál. A felhőszolgáltatásban a webes szerepkör egy külön Internet Information Services (IIS) webkiszolgálót biztosít az előtéri webalkalmazásokhoz, míg a feldolgozói szerepkör aszinkron, hosszan futó vagy folyamatos feladatokat futtat függetlenül a felhasználói interakcióktól vagy bemenettől.

További információ: [Mi az a Cloud Service?].

> [!NOTE]
> *Egyszerű webhelyet szeretne készíteni?*
> Ha egy egyszerű webhely előterét kívánja futtatni, fontolja meg az Azure App Service egyszerűsített Web Apps szolgáltatásának használatát. Könnyedén frissíthet Cloud Service szolgáltatásra, ha a webhely növekszik és a követelmények változnak. A <a href="/develop/python/">Python fejlesztői központban</a> találhat az Azure App Service Web Apps szolgáltatásának fejlesztését ismertető cikkeket.
> <br />
> 
> 

## <a name="project-creation"></a>Projekt létrehozása
A Visual Studio eszközben válassza az **Azure Cloud Service** lehetőséget az **Új projekt** párbeszédpanelen a **Python** területen.

![Új projekt párbeszédpanel](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Az Azure Cloud Service varázslóban létrehozhat új webes és feldolgozói szerepköröket.

![Azure Cloud Service párbeszédpanel](./media/cloud-services-python-ptvs/new-service-wizard.png)

A feldolgozói szerepkör bolierplate kóddal rendelkezik, hogy csatlakozhasson egy Azure-tárfiókhoz vagy Azure Service Bus szolgáltatáshoz.

![Cloud Service megoldás](./media/cloud-services-python-ptvs/worker.png)

Egy meglévő felhőszolgáltatáshoz bármikor hozzáadhat webes vagy feldolgozói szerepköröket.  Hozzáadhat a megoldásában már meglévő projekteket, vagy létrehozhat újakat.

![Szerepkör hozzáadása parancs](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

A felhőszolgáltatás tartalmazhat különböző nyelveken kialakított szerepköröket.  Például kialakíthat egy Python webes szerepkört Django, Python vagy C# feldolgozói szerepkör használatával.  A szerepkörök között egyszerűen kommunikálhat Service Bus üzenetsorok vagy tárolási sorok használatával.

## <a name="install-python-on-the-cloud-service"></a>A Python telepítése a felhőszolgáltatáson
> [!WARNING]
> A Visual Studióval települt telepítési parancsfájlok (a cikk utolsó frissítésekor) erre a célra nem használhatók. A megoldást ez a szakasz ismerteti.
> 
> 

A telepítési parancsfájlok fő problémája, hogy nem telepítik a Pythont. Először határozzon meg két [indítási feladatot](cloud-services-startup-tasks.md) a [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fájlban. Az első feladat (**PrepPython.ps1**) letölti és telepíti a Python-futtatókörnyezetet. A második feladat (**PipInstaller.ps1**) a pipet futtatja a vonatkozó függőségek telepítéséhez.

Az alábbi parancsfájlok a Python 3.5-ösre vonatkoznak. Ha a Python 2.x verzióját szeretné használni, állítsa a **PYTHON2** változófájlt **be** értékre a két indítási és a futtatókörnyezeti feladathoz: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

A **PYTHON2** és a **PYPATH** változót hozzá kell adni a feldolgozó indítási feladatához. A **PYPATH** változót csak akkor használja a rendszer, ha a **PYTHON2** változó **be** értékű.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Mintául szolgáló ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Ezután hozza létre a **PrepPython.ps1** és a **PipInstaller.ps1** fájlokat a szerepkör **./bin** mappájában.

#### <a name="preppythonps1"></a>PrepPython.ps1
Ez a parancsfájl telepíti a Pythont. Ha a **PYTHON2** környezeti változó **be** értékű, akkor a rendszer a Python 2.7-et telepíti. Egyéb esetben a Python 3.5 lesz telepítve.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Ez a parancsfájl meghívja a pipet, és telepíti a **requirements.txt** fájlban található összes függőséget. Ha a **PYTHON2** környezeti változó **be** értékű, a rendszer a Python 2.7-et használja. Egyéb esetben a Python 3.5 lesz használatban.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>A LaunchWorker.ps1 módosítása
> [!NOTE]
> **Feldolgozói szerepkörű** projekt esetében az indítófájl végrehajtásához **LauncherWorker.ps1** fájl szükséges. **Webes szerepkörű** projekt esetében az indítófájl definiálása nem ebben a fájlban, hanem a projekt tulajdonságaiban történik.
> 
> 

A **bin\LaunchWorker.ps1** eredetileg azért jött létre, hogy előkészítési munkákat végezzen, de nem igazán működik. Cserélje le a fájl tartalmát a következő parancsfájllal.

A parancsfájl meghívja a **worker.py** fájlt a Python-projektből. Ha a **PYTHON2** környezeti változó **be** értékű, a rendszer a Python 2.7-et használja. Egyéb esetben a Python 3.5 lesz használatban.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
A Visual Studio-sablonoknak létre kellett hozniuk egy **ps.cmd** fájlt a **./bin** mappában. Ez a héjparancsfájl kiemeli a fenti PowerShell burkoló parancsfájlokat, és a meghívott PowerShell burkoló neve alapján biztosít naplózást. Ha a fájl nem jött létre, itt láthatja, minek kéne benne lennie. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Helyi futtatás
Ha a felhőszolgáltatási projektet állítja be kiindulási projektnek, és megnyomja az F5 billentyűt, a felhőszolgáltatás a helyi Azure emulátorban fog futni.

Bár a PVTS támogatja az indítást az emulátorban, a hibakeresés (például töréspontok keresése) nem fog működni.

A webes és feldolgozói szerepkörök hibakereséséhez állítsa be a szerepkörprojektet kiindulási projektként, és végezzen azon hibakeresést  Beállíthat több kiindulási projektet is.  Kattintson a jobb gombbal a megoldásra, majd válassza az **Indítási projektek beállítása** lehetőséget.

![A megoldás kiindulási projektjének tulajdonságai](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Közzététel az Azure platformon
A közzétételhez kattintson a jobb gombbal a felhőszolgáltatási projektre a megoldásban, majd kattintson a **Közzététel** gombra.

![Bejelentkezés a Microsoft Azure közzétételhez](./media/cloud-services-python-ptvs/publish-sign-in.png)

Kövesse a varázsló utasításait. Szükség esetén engedélyezze a távoli asztal használatát. A távoli asztal akkor hasznos, amikor valamilyen okból hibakeresést kell végeznie.

Ha elkészült a beállítások konfigurálásával, kattintson a **Közzététel** gombra.

Egy folyamatjelző jelenik meg a kimeneti ablakban, majd megnyílik a Microsoft Azure tevékenységnapló ablak.

![Microsoft Azure tevékenységnapló ablak](./media/cloud-services-python-ptvs/publish-activity-log.png)

A telepítés néhány percet vesz igénybe, ezután a webes és/vagy feldolgozói szerepkörök el is indulnak az Azure portálon!

### <a name="investigate-logs"></a>Naplók vizsgálata
A felhőszolgáltatás virtuális gépének elindulása és a Python telepítése után áttekintheti, hogy vannak-e a naplókban hibaüzenetek. Ezek a naplók a **C:\Resources\Directory\\{szerepkör}\LogFiles** mappában találhatók. A **PrepPython.err.txt** fájlban legalább egy hiba elő fog fordulni, amely akkor keletkezik, amikor a parancsfájl megpróbálja észlelni, hogy a Python telepítve van-e. Az is előfordulhat, hogy a **PipInstaller.err.txt** jelzi a pip elavult verziójának használatát.

## <a name="next-steps"></a>Következő lépések
A PVTS dokumentációban további információkat találhat a webes és feldolgozói szerepkörök használatáról Python Tools for Visual Studio eszközben:

* [Cloud Service-projektek][Cloud Service-projektek]

Ha további részletekre kíváncsi az Azure szolgáltatások használatáról webes és feldolgozói szerepkörökből, mint az Azure Storage vagy a Service Bus, olvassa el a következő cikkeket.

* [Blob Service][Blob Service]
* [Table Service][Table Service]
* [Queue szolgáltatás][Queue szolgáltatás]
* [Service Bus-üzenetsorok][Service Bus-üzenetsorok]
* [Service Bus-témakörök][Service Bus-témakörök]

<!--Link references-->

[Mi az a Cloud Service?]: cloud-services-choose-me.md
[futtatási modell-webhelyek]: ../app-service-web/app-service-web-overview.md
[végrehajtási modell virtuális gépek]: ../virtual-machines/virtual-machines-windows-about.md
[végrehajtási modell felhőszolgáltatások]: cloud-services-choose-me.md
[Python fejlesztői központ]: /develop/python/

[Blob Service]: ../storage/storage-python-how-to-use-blob-storage.md
[Queue szolgáltatás]: ../storage/storage-python-how-to-use-queue-storage.md
[Table Service]: ../storage/storage-python-how-to-use-table-storage.md
[Service Bus-üzenetsorok]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus-témakörök]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio – dokumentáció]: http://aka.ms/ptvsdocs
[Cloud Service-projektek]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bites]: https://www.python.org/downloads/
[Python 3.5 32 bites]: https://www.python.org/downloads/



<!--HONumber=Nov16_HO3-->


