---
title: "A fejlesztési környezet beállítása Mac OS X-en az Azure Service Fabric használatához | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások Mac OS X-en történő létrehozására."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 309fcb901a1a3878edbfbe06e12122615b74664e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>A fejlesztési környezet beállítása Mac OS X-en
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux-fürtökön futó Service Fabric-alkalmazásokat hozhat létre a Mac OS X-en. Ez a dokumentum bemutatja, hogyan állíthatja be Mac gépét a fejlesztéshez.

## <a name="prerequisites"></a>Előfeltételek
A Service Fabric nem fut natív módon az OS X-en. A helyi Service Fabric-fürt futtatásához egy előre konfigurált Docker-tároló lemezképet biztosítunk. A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM
* A [Docker](https://www.docker.com/) legújabb verziója
* Hozzáférés a Service Fabric Docker-tároló beépített [rendszerképéhez](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)

>[!TIP]
> * A Docker Mac gépen való telepítéséhez követheti a hivatalos Docker [dokumentációban](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) említett lépéseket. 
> * Ha kész a telepítéssel, az [itt](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine) említett lépéseket követve ellenőrizheti, hogy sikeres volt-e a telepítés.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy Service Fabric-fürt rajta való futtatásához hajtsa végre a következő lépéseket:

1. A rendszerkép lekérése a Docker Hub adattárából:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Frissítse a Docker-démon konfigurációját a gazdagépen a következő értékekkel, és indítsa újra a Docker-démont: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    A frissítést közvetlenül a Docker telepítési útvonalán lévő daemon.json fájlban végezheti el (amelynek helye gépenként változhat, például: ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json). A frissítést javasolt a Docker ikon > Beállítások > Démon > Speciális területen végrehajtani.

3. Indítson el egy Service Fabric beépített tárolópéldányt a rendszerképpel:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Ha megad egy nevet a tárolópéldányhoz, átláthatóbb módon kezelheti. 
    > * Ha az alkalmazás bizonyos portokon figyel, ezt további -p címkékkel kell meghatározni. Ha például az alkalmazás a 8080-as porton figyel, futtassa a következőt: docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. Jelentkezzen be a Docker-tárolóba interaktív SSH-módban:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Futtassa a beállítási szkriptet, amely lehívja a szükséges függőségeket, és ezután elindítja a fürtöt a tárolón.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Az 5. lépés sikeres befejezése után a ``http://localhost:19080`` helyre léphet a Mac gépéről, és elvileg látnia kell a Service Fabric Explorert.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>A Service Fabric parancssori felület (sfctl) beállítása Mac gépen

A Service Fabric parancssori felület (`sfctl`) Mac gépre való telepítéséhez kövesse a [Service Fabric parancssori felület](service-fabric-cli.md#cli-mac) utasításait.
A parancssori felület a Service Fabric-entitásokkal, többek között fürtökkel, alkalmazásokkal és szolgáltatásokkal folytatott interakciók elvégzésére szolgáló parancsokat is tartalmaz.

## <a name="create-application-on-your-mac-using-yeoman"></a>Alkalmazás létrehozása Mac gépen a Yeoman használatával

A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyekkel Service Fabric-alkalmazásokat hozhat létre a terminálról a Yeoman sablongenerátor használatával. Az alábbi lépések végrehajtásával ellenőrizze, hogy a Service Fabric Yeoman sablongenerátor működik-e a gépen.

1. A Node.js-nek és az NPM-nek telepítve kell lennie Mac gépén. Ha a Node.js és az NPM nincs telepítve, a következő lépésben leírt módon telepítheti őket a Homebrew-val:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. A [Yeoman](http://yeoman.io/) sablongenerátor telepítése a gépre az NPM-ből

    ```bash
    npm install -g yo
    ```
3. Telepítse a használni kívánt Yeoman generátort az első lépéseket ismertető [dokumentációban](service-fabric-get-started-linux.md) leírt lépések végrehajtásával. Service Fabric-alkalmazások Yeomannal való létrehozásához kövesse az alábbi lépéseket –

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Service Fabric Java-alkalmazások Mac gépen való létrehozásához a JDK 1.8-nak és a Gradle-nek telepítve kell lennie a gépen. Ha még nincs telepítve, a [Homebrew](https://brew.sh/)-val telepítheti. 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Alkalmazás telepítése Mac gépen terminálról

Amint létrehozza és kiépíti a Service Fabric-alkalmazását, az alábbi lépéseket követve helyezheti üzembe a [Service Fabric parancssori felület](service-fabric-cli.md#cli-mac) segítségével:

1. Csatlakozzon a Mac gépén lévő tárolópéldányon belül futó Service Fabric-fürthöz.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Lépjen be a projektkönyvtárba, és futtassa a telepítési szkriptet.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 fejlesztői környezet beállítása

Telepítse az [Mac rendszerre készült .NET Core 2.0 SDK-t](https://www.microsoft.com/net/core#macos) [a C# Service Fabric-alkalmazások létrehozásának](service-fabric-create-your-first-linux-application-with-csharp.md) első lépéseként. A .NET Core 2.0 Service Fabric-alkalmazások csomagjai a NuGet.org webhelyen érhetők el, jelenleg előzetes verzióban.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Az Eclipse Neonhoz készült Service Fabric beépülő modul telepítése Mac gépen

A Service Fabric egy beépülő modult biztosít a **Java Eclipse Neon IDE-hez**, amely leegyszerűsítheti a Java-szolgáltatások összeállításának, létrehozásának és üzembe helyezésének folyamatát. A Service Fabric Eclipse beépülő modul telepítéséhez és legújabb verzióra frissítéséhez érdemes az ebben az általános [dokumentációban](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) szereplő telepítési lépéseket követnie.

Minden más, a [Service Fabric Eclipse dokumentációjában](service-fabric-get-started-eclipse.md) az alkalmazás készítésével, szolgáltatás alkalmazáshoz adásával, alkalmazás telepítésével/eltávolításával stb. kapcsolatban említett lépés itt is érvényes.

A fenti lépéseken kívül ahhoz, hogy a Service Fabric Eclipse beépülő modul működjön a Macen lévő Docker-tárolóval, az alábbiak szerint példányosítania kell a tárolót egy, a gazdagépével megosztott útvonallal:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
ahol ``/Users/sayantan/work/workspaces/mySFWorkspace`` a Mac munkaterületének teljes elérési útja, ``/tmp/mySFWorkspace`` pedig az útvonal abban a tárolóban, ahová az le lesz képezve.

> [!NOTE]
>1. Ha munkaterülete neve/elérési útja eltérő, frissítse azokat megfelelően a fenti ``docker run`` parancsban.
>2. Ha a tárolót más, az ``sfonebox`` névtől eltérő néven indítja, frissítse a nevet a Service Fabric-aktor Java alkalmazásában lévő ``testclient.sh`` fájlban.

## <a name="next-steps"></a>Következő lépések
<!-- Links -->
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Service Fabric-fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md)
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)
* [A Service Fabric parancssori felület használata az alkalmazások kezeléséhez](service-fabric-application-lifecycle-sfctl.md)
* [Linux fejlesztőkörnyezet előkészítése Windowson](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship