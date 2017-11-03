---
title: "Docker ügyfél hibákat a Windows hibaelhárítása a Visual Studio használatával |} Microsoft Docs"
description: "Hozzon létre és telepíthet webalkalmazásokat az Docker Windows rendszeren Visual Studio használatával Visual Studio használata során felmerülő problémák megoldásához."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>A Visual Studio Docker fejlesztési hibaelhárítása

Ha a Visual Studio eszközök a Docker Preview dolgozik, néhány problémákat tapasztalhat a előzetes jellege miatt.
Az alábbiakban néhány gyakori problémák és megoldások.  

## <a name="visual-studio-2017-rc"></a>A Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux-tárolók**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Build hiba történik, amikor a .NET Core web vagy konzol alkalmazásban  

Ezzel kapcsolatos lehet a meghajtót, amelyen a projektben található nem megosztása Docker Windows.  A következőhöz hasonló hiba jelenhet meg:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
A probléma megoldásához:

1. Kattintson a jobb gombbal **Docker for Windows** az értesítési területen, és válassza a **beállítások**.  
2. Válassza ki **megosztott meghajtók** és a meghajtó, ahol a projektben található.

### <a name="windows-containers"></a>**Windows-tárolók**

.NET-keretrendszer webes és a konzol alkalmazások Windows-tárolókban lévő hibakeresés a következő problémák vonatkoznak.

#### <a name="prerequisites"></a>Előfeltételek

1. A Visual Studio 2017 RC (vagy újabb) a .NET Core és a Docker Preview munkaterhelést kell telepíteni.
2. Windows 10 évforduló frissítése a legújabb Windows frissítéshez javításokkal. Kifejezetten [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) kell telepíteni. 
3. [A Windows docker](https://docs.docker.com/docker-for-windows/) (build 1.13.0 vagy újabb) kell telepíteni.
4. **Váltás a Windows-tárolók** ki kell jelölni. Az értesítési területen kattintson a **Docker for Windows**, majd válassza ki **Windows tárolók váltani**. Miután a számítógép újraindul, győződjön meg arról, hogy ez a beállítás megőrzi.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>Konzol kimeneti nem jelenik meg a Visual Studio kimeneti ablakában a Konzolalkalmazás-hibakeresés során

Ez az egy ismert hiba, mely a Visual Studio hibakereső (msvsmon.exe), amely jelenleg nem az ebben a forgatókönyvben. Ez a forgatókönyv támogatása egy későbbi kiadásban szereplő. A konzolalkalmazást a Visual Studio eredményének megtekintéséhez használja **Docker: Start projekt**, amely **indítása nélkül hibakeresés**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>A kiadás configuration meghiúsul (403-as) tiltott alkalmazások hibakeresés

A probléma megoldásához nyissa meg a web.release.config a megoldás és a kimenő megjegyzés, vagy törölje a következő sorokat:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux-tárolók**

#### <a name="unable-to-validate-volume-mapping"></a>Nem sikerült a kötet leképezés érvényesítése
A Forráskód és az alkalmazás a bináris fájlok megosztása a tároló alkalmazás mappájában kötet hozzárendelése szükséges.  Adott kötet hozzárendelések docker-compose.dev.debug.yml és a docker-compose.dev.release.yml belül találhatók. Fájlok módosulnak, a gazdagépen, mert a tárolók változásoknak hasonló gyökérmappa-szerkezetében lévő.

Ahhoz, hogy a kötet hozzárendelése:

1. Kattintson a **Moby** az értesítési területről, és válassza ki a **beállítások**.
2. Válassza ki **megosztott meghajtók**.
3. Válassza ki a meghajtót, amelyen a projekt és a meghajtó, ahol a % USERPROFILE % található.
4. Kattintson az **Alkalmaz** gombra.

Tesztelése, ha a kötet leképezési működik, építse újra, és válassza ki az F5 billentyűt a Visual Studio, után egy vagy több meghajtóra lett megosztva, vagy a parancssorból futtassa a következő kódot.

> [!NOTE]
> Ez a példa feltételezi, hogy a felhasználók mappában található a C meghajtó, és hogy azt meg lett osztva.
> Szükség esetén, ha egy másik meghajtót megosztott módosításához.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Futtassa a következő kódot a Linux-tárolóban.

```
/ # ls
```

Meg kell jelennie egy könyvtárlistát, a felhasználók vagy nyilvános mappából. Ha nem jelenik meg, és a /c/Users/Public mappa nem üres, kötet leképezési nincs megfelelően konfigurálva.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

A féreglyuk könyvtár tartalmának megtekintését. Módosítsa a `/c/Users/Public` könyvtár:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Dolgozunk a Linux virtuális gépek, a tároló fájlrendszer esetén kis-és nagybetűket.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Build: "PrepareForBuild" feladat váratlanul leállt

Microsoft.DotNet.Docker.CommandLine.ClientException: Hiba történt a kapcsolódás.

Győződjön meg arról, hogy fut-e az alapértelmezett Docker-állomás. Nyisson meg egy parancssort, és hajtsa végre:

```
docker info
```

Ha ezt a hibát ad vissza, majd próbálja meg elindítani a **Docker for Windows** egy asztali alkalmazás. Ha egy asztali alkalmazás fut, majd **Moby** az értesítési területen látható kell lennie. Kattintson a jobb gombbal **Moby** , és nyissa meg **beállítások**. Kattintson a **alaphelyzetbe**, majd indítsa újra a Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Hiba-párbeszédpanel akkor fordul elő, a Docker támogatását, vagy a hibakeresési (F5) az ASP.NET Core alkalmazás tárolója

Eltávolítása és bővítmények telepítése után a felügyelt bővítési keretrendszer (MEF) gyorsítótár, a Visual Studio is megsérülhet. Ha ez történik, azt okozhatnak különböző hibaüzenetek, amikor éppen Docker-támogatás hozzáadása, illetve próbál futtatni, vagy az ASP.NET Core alkalmazás (F5) hibakeresését. Ideiglenes megoldásként az alábbi lépések segítségével törölje, majd újra létrehozza a MEF gyorsítótár.

1. Zárja be a Visual Studio.
1. Nyissa meg a % USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. A következő mappák törlése:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Nyissa meg a Visual Studiót.
1. Próbálja meg újra a forgatókönyvet.
