---
title: Az Azure Application Insights OpenCensus elosztott nyomkövetési helyi továbbító |} A Microsoft docs
description: Elosztott előre OpenCensus-nyomkövetéseket, és az Azure Application Insights például a Python és a Go nyelvről kiterjedő
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: ddfcb90090d82d8fe947292737163a81c715b32d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972827"
---
# <a name="local-forwarder"></a>Helyi továbbító

Helyi továbbító egy olyan ügynök, amely gyűjti az Application Insights vagy [OpenCensus](https://opencensus.io/) használati adatok gyűjtése a különböző SDK-k és az Application insights-bA továbbítja azt. Windows és Linux alatt alkalmas állapotban. Bizonyos is futtatható a macOS, de ez nem hivatalosan támogatott jelenleg.

## <a name="running-local-forwarder"></a>Helyi továbbítója fut

-Továbbító helyi egy [nyílt forráskódú projekt a Githubon](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Számos különböző módon futtathatja a helyi továbbító több platformon.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows-szolgáltatás

Helyi továbbító Windows alatt fut, a legegyszerűbb módja van: telepítse a Windows szolgáltatásként. A kiadás egy Windows-szolgáltatás futtatható fájlja tartalmaz (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) amely egyszerűen regisztrálni lehet az operációs rendszerrel.

> [!NOTE]
> A helyi továbbító szolgáltatás a .NET-keretrendszer 4.7 legalább igényel. Ha nem rendelkezik .NET-keretrendszer 4.7 a szolgáltatás fogja telepítése, de nem indul el. A .NET-keretrendszer legújabb verzióját eléréséhez ** [a .NET-keretrendszer letöltési oldalán](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Töltse le a LF Karakterrel. WindowsServiceHost.zip fájlt a [helyi továbbító kiadás oldalon](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) a Githubon.

    ![Helyi továbbító kiadási letöltési oldalát bemutató képernyőkép](.\media\opencensus-local-forwarder\001-local-forwarder-windows-service-host-zip.png)

2. Ebben a példában a könnyű bemutató azt fogja csak bontsa ki a zip-fájlt az elérési út `C:\LF-WindowsServiceHost`.

    Regisztrálja a szolgáltatást, és konfigurálja úgy, hogy a parancssor rendszergazdaként futtassa a következő rendszerindítás Kezdőpont:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Választ kell kapnia:
    
    `[SC] CreateService SUCCESS`
    
    Az új szolgáltatást a szolgáltatások grafikus típusú vizsgálata ``services.msc``
        
     ![Képernyőkép a helyi továbbító szolgáltatás](.\media\opencensus-local-forwarder\002-services.png)

3. **Kattintson a jobb gombbal** az új helyi továbbítót, és válassza ki **Start**. A szolgáltatás fut. írja.

4. Alapértelmezés szerint a szolgáltatás a helyreállítási műveletek nélkül jön létre. Is **kattintson a jobb gombbal** válassza **tulajdonságok** > **helyreállítási** konfigurálása az automatikus válaszok a szolgáltatás meghibásodása.

    Vagy ha inkább a programozott módon az automatikus helyreállítási beállításainak megadása, ha hiba történik, akkor használhatja:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Ugyanazon a helyen a ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` fájlt, amely ebben a példában ``C:\LF-WindowsServiceHost`` nevű fájl ``LocalForwarder.config``. Ez a alapú xml-fájlba, amely lehetővé teszi, hogy a localforwader beállításainak módosítására, és adja meg a kialakítási kulcsot, az elosztott nyomkövetési adatokat továbbítani kívánt Application Insights-erőforrás. 

    A Szerkesztés után a ``LocalForwarder.config`` fájl hozzáadása a kialakítási kulcsot, ügyeljen arra, hogy indítsa újra a **helyi továbbító szolgáltatásának** , hogy a módosítások érvénybe léptetéséhez.
    
6. Győződjön meg arról, hogy a kívánt beállításokat, és, hogy a helyi továbbító figyeli a nyomkövetési adatok várt ellenőrizze, hogy a ``LocalForwarder.log`` fájlt. A fájl alján alábbi képen láthatóhoz hasonló eredményt kell megjelennie:

    ![Képernyőkép a LocalForwarder.log fájl](.\media\opencensus-local-forwarder\003-log-file.png)

#### <a name="console-application"></a>Konzolalkalmazás

Az egyes használati esetek, érdemes lehet helyi továbbító futtathat egy konzolalkalmazást. A kiadás tartalma a konzol gazdakörnyezetét végrehajtható következő verziói:
* .NET Core keretrendszert-függő bináris */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Egy .NET Core runtime kell telepíteni; a bináris futtatásához szükség van. Tekintse meg a letöltési [oldal](https://www.microsoft.com/net/download/dotnet-core/2.1) részleteiről.
```batchfile
E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```
* bináris fájlok x86 és x64 platformokon önálló .NET Core készlete. Ezek nem igénylik a .NET Core runtime futtatásához. */ConsoleHost/Win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
```batchfile
E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
```

### <a name="linux"></a>Linux

Csakúgy, mint a Windows, a kiadás tartalma a konzol gazdakörnyezetét végrehajtható következő verziói:
* .NET Core keretrendszert-függő bináris */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Egy .NET Core runtime kell telepíteni; a bináris futtatásához szükség van. Tekintse meg a letöltési [oldal](https://www.microsoft.com/net/download/dotnet-core/2.1) részleteiről.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* egy önálló .NET Core for linux-64 bináris fájlok készlete. Ez egy .NET Core runtime futtatásához nincs szükség. */ConsoleHost/Linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Számos Linux-felhasználók érdemes helyi továbbítót futtató démon. Linux rendszerek Szolgáltatáskezelési, mint a Upstart, sysv vagy systemd megoldások széles kapható. Akármilyen területen is az adott verziója van, használhatja azt a forgatókönyvhöz leginkább megfelelő módon futtathat helyi továbbító.

Tegyük fel hozzunk létre egy démon szolgáltatás systemd használatával. A keretrendszer-függő verziót fogjuk használni, de azonos teheti meg egy önálló is.

* Hozzon létre a következő szolgáltatás fájlt *localforwarder.service* , és helyezze be */lib/systemd/system*.
Ez a példa feltételezi, a felhasználónév SAMPLE_USER és másolta helyi továbbító keretrendszer-függő bináris fájlokat (a */ConsoleHost és közzétételi*) való */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* A következő paranccsal utasíthatja az systemd helyi továbbító elindítani a naplózásra kerül minden rendszerindításkor

```
systemctl enable localforwarder
```

* A következő paranccsal utasíthatja az systemd helyi továbbító azonnal elindul

```
systemctl start localforwarder
```

* A szolgáltatás figyelésére vizsgálatával **.log* a /home/SAMPLE_USER/LOCALFORWARDER_DIR könyvtárban található fájlok.

### <a name="mac"></a>Mac
Helyi továbbító a MacOS is működhet, azonban jelenleg nem hivatalosan támogatott.

### <a name="self-hosting"></a>Self-hosting
Helyi továbbítót is NuGet-csomagként .NET Standard szintű, lehetővé teszi, hogy üzemeltesse belül a saját .NET-alkalmazás terjesztése.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Helyi továbbító konfigurálása

* Helyi továbbító saját hosts (konzol-Gazdakörnyezethez vagy Windows-szolgáltatást futtató) egyike fut, amikor megtalálja **LocalForwarder.config** a bináris mellé helyezi.
* Helyi tárolásához a helyi továbbító NuGet, ugyanazt a formátumot konfigurációjának meg kell adni a code-ban (lásd a helyi üzemeltetés). A konfigurációs szintaxist, ellenőrizze a [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) a GitHub-adattárában. 

> [!NOTE]
> Konfigurációs kiadási kiadás módosításához, ezért a figyelmet, melyik verziót használja.

## <a name="monitoring-local-forwarder"></a>Helyi továbbító figyelése

Nyomok írt a fájlrendszer mellett a végrehajtható fájl, amely a helyi továbbítója fut (keressen **.log* fájlok). Elhelyezhet egy fájlt nevű *NLog.config* mellett a végrehajtható fájlt, adja meg az alapértelmezett helyett a saját konfigurációjával. Lásd: [dokumentáció](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) formátumban leírását.

Ha nincsenek konfigurációs fájl áll rendelkezésre (Ez az alapértelmezett beállítás), a helyi továbbító fogja használni az alapértelmezett konfiguráció, amely található [Itt](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>További lépések

* [Nyissa meg népszámlálási](https://opencensus.io/)
