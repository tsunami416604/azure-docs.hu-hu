---
title: Azure Application Insights OpenCensus elosztott nyomkövetés helyi továbbítója (előzetes verzió) | Microsoft docs
description: Továbbítsa a OpenCensus elosztott nyomkövetéseit, és a Pythonhoz hasonló nyelveket és az Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: aa64755b636005f4ed8ea5c074ffaada51fb8dd9
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348152"
---
# <a name="local-forwarder-preview"></a>Helyi továbbító (előzetes verzió)

A helyi továbbító egy olyan ügynök, amely különböző SDK-k Application Insights-vagy [OpenCensus](https://opencensus.io/) -telemetria gyűjt, és a Application Insightsba irányítja. Windows és Linux rendszeren is futtatható. Azt is megteheti, hogy a macOS alatt is futtatható, de jelenleg nem támogatott.

## <a name="running-local-forwarder"></a>Helyi továbbító futtatása

A helyi továbbító egy [nyílt forráskódú projekt a githubon](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Számos különböző módon futtathat helyi továbbítót több platformon.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows-szolgáltatás

A helyi továbbító Windows rendszeren való futtatásának legegyszerűbb módja, ha Windows-szolgáltatásként telepíti. A kiadáshoz tartozik egy Windows-szolgáltatás végrehajtható fájlja (*WindowsServiceHost/Microsoft. LocalForwarder. WindowsServiceHost. exe*), amely könnyen regisztrálható az operációs rendszerben.

> [!NOTE]
> A helyi továbbító szolgáltatáshoz legalább a .NET-keretrendszer 4,7-es verziójának kell tartoznia. Ha nincs telepítve a .NET-keretrendszer 4,7-es szolgáltatása, a szolgáltatás nem fog elindulni. A .NET-keretrendszer legújabb verzióját eléréséhez **[a .NET-keretrendszer letöltési oldalán](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Töltse le a TT-t. A WindowsServiceHost. zip fájl a GitHubon a [helyi továbbító kiadás oldaláról](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) .

    ![Képernyőkép a helyi továbbító kiadásának letöltési oldaláról](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Ebben a példában a könnyű bemutatóhoz csak a. zip fájlt fogjuk kinyerni az elérési útra `C:\LF-WindowsServiceHost`.

    A szolgáltatás regisztrálásához és a rendszerindítási indításhoz való konfigurálásához futtassa a következő parancsot a parancssorból rendszergazdaként:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    A válasza a következőket kapja:
    
    `[SC] CreateService SUCCESS`
    
    Az új szolgáltatás megvizsgálása a szolgáltatások grafikus felhasználói felületének típusán keresztül``services.msc``
        
     ![Képernyőkép a helyi továbbító szolgáltatásról](./media/opencensus-local-forwarder/002-services.png)

3. **Kattintson a jobb gombbal** az új helyi továbbítóra, és válassza az **Indítás**lehetőséget. A szolgáltatás mostantól egy futó állapotot fog megadni.

4. Alapértelmezés szerint a szolgáltatás helyreállítási műveletek nélkül jön létre. Kattintson a **jobb gombbal** , és válassza a **Tulajdonságok** > **helyreállítása** lehetőséget a szolgáltatási hibákra vonatkozó automatikus válaszok konfigurálásához.

    Ha a hibák előfordulásakor programozottan szeretné beállítani az automatikus helyreállítási beállításokat, a következőt használhatja:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. A ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` fájl ugyanazon a helyen található, amely ebben a ``C:\LF-WindowsServiceHost`` példában egy nevű ``LocalForwarder.config``fájl. Ez egy XML-alapú fájl, amelynek segítségével módosíthatja a localforwader konfigurációját, és megadhatja annak a Application Insights erőforrásnak a rendszerállapot-kulcsát, amelyet az elosztott nyomkövetési adatai továbbítottak. 

    Miután módosította ``LocalForwarder.config`` a fájlt a kialakítási kulcs hozzáadásához, indítsa újra a **helyi továbbító szolgáltatást** , hogy a módosítások életbe lépnek.
    
6. Annak ellenőrzéséhez, hogy a kívánt beállítások teljesülnek-e, és hogy a helyi továbbító a várt módon figyeli-e ``LocalForwarder.log`` a nyomkövetési adatait, tekintse meg a fájlt. A fájl alján az alábbi képhez hasonló eredményeknek kell megjelennie:

    ![Képernyőkép a LocalForwarder. log fájlról](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Konzol alkalmazás

Bizonyos használati esetekben hasznos lehet a helyi továbbító futtatása konzolos alkalmazásként. A kiadás a konzol gazdagépének következő végrehajtható verzióihoz tartozik:
* egy keretrendszertől függő .NET Core bináris */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. A bináris fájl futtatásához telepíteni kell egy .NET Core-futtatókörnyezetet; További részletekért tekintse meg ezt a letöltési [oldalt](https://www.microsoft.com/net/download/dotnet-core/2.1) .
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* egy önálló .NET Core-készlet bináris x86-és x64-alapú platformokhoz. Ezek nem igénylik a .NET Core futtatókörnyezet futtatását. */ConsoleHost/Win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/Win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

A Windowshoz hasonlóan a kiadás a konzol gazdagépének következő végrehajtható verzióiban érhető el:
* egy keretrendszertől függő .NET Core bináris */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. A bináris fájl futtatásához telepíteni kell egy .NET Core-futtatókörnyezetet; További részletekért tekintse meg ezt a letöltési [oldalt](https://www.microsoft.com/net/download/dotnet-core/2.1) .

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* egy önálló .NET Core-készlet a Linux-64-hez. Ehhez nem szükséges, hogy a .NET Core futtatókörnyezet fusson. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Számos Linux-felhasználó szeretné futtatni a helyi továbbítót démonként. A linuxos rendszerek számos különböző szolgáltatást biztosítanak a szolgáltatások kezeléséhez, például az indításhoz, a SysV vagy a rendszerhez. Az adott verziótól függetlenül használhatja azt a helyi továbbító futtatására úgy, hogy a legmegfelelőbb a forgatókönyvhöz.

Tegyük fel például, hogy létrehozunk egy Daemon-szolgáltatást a rendszerszintű használatával. A keretrendszer függő verzióját fogjuk használni, de ugyanezt is elvégezheti egy önálló is.

* hozza létre a következő *localforwarder. Service* nevű szolgáltatásfiókot, és helyezze a */lib/systemd/System*-be.
Ez a példa feltételezi, hogy a felhasználónevét SAMPLE_USER, és a helyi továbbító Framework-függő bináris fájljait ( */ConsoleHost/publish*) a */Home/SAMPLE_USER/LOCALFORWARDER_DIR*-be másolta.

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

* A következő parancs futtatásával utasítsa a rendszert a helyi továbbító indítására minden rendszerindításkor

```
systemctl enable localforwarder
```

* Futtassa a következő parancsot, hogy a rendszer azonnal elindítsa a helyi továbbítót

```
systemctl start localforwarder
```

* A szolgáltatás figyeléséhez vizsgálja meg a * *. log* fájlokat a/Home/SAMPLE_USER/LOCALFORWARDER_DIR könyvtárban.

### <a name="mac"></a>Mac
A helyi továbbító a macOS-mel is működhet, de jelenleg nem támogatott.

### <a name="self-hosting"></a>Self-hosting
A helyi továbbítót .NET Standard NuGet-csomagként is terjesztik, amely lehetővé teszi, hogy a saját .NET-alkalmazásán belül tárolja azt.

```csharp
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

* A helyi továbbító saját gazdagépei (a konzol gazdagépe vagy a Windows-szolgáltatás gazdagépe) egyikének futtatásakor a **LocalForwarder. config** fájlt fogja megtalálni a bináris fájl mellett.
* A helyi továbbító NuGet önálló üzemeltetése esetén az azonos formátumú konfigurációt a kódban kell megadni (lásd: szakasz az önkiszolgáló eszközön). A konfigurációs szintaxisban keresse meg a [LocalForwarder. config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) fájlt a GitHub-tárházban. 

> [!NOTE]
> A konfiguráció a kiadástól a kiadásig változhat, ezért ügyeljen arra, hogy melyik verziót használja.

## <a name="monitoring-local-forwarder"></a>Helyi továbbító figyelése

A nyomkövetéseket a helyi továbbítót futtató végrehajtható fájl (a * *. log* fájlok) melletti fájlrendszerbe írja a rendszer. A végrehajtható fájl neve mellett a *NLog. config* nevű fájlt is elhelyezheti, hogy az alapértelmezett beállítás helyett saját konfigurációt adjon meg. A [](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) formátum leírását a dokumentációban találja.

Ha nincs megadva konfigurációs fájl (ez az alapértelmezett beállítás), a helyi továbbító az alapértelmezett konfigurációt fogja használni, amely [itt](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config)található.

## <a name="next-steps"></a>További lépések

* [Az összeírás megnyitása](https://opencensus.io/)
