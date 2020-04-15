---
title: Az Azure Service Fabric Linux-fürt beállítása Windows rendszeren
description: Ez a cikk ismerteti, hogyan állíthatja be a Service Fabric Linux-fürtök windowsos fejlesztői gépeken futó. Ez különösen hasznos a platformok közötti fejlesztéshez.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 9f60b9d9b919a72250038ede2a2bd53278df79cb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309003"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Linux Service Fabric-fürt beállítása a Windows fejlesztői gépen

Ez a dokumentum ismerteti, hogyan lehet beállítani egy helyi Linux Service Fabric windowsos fejlesztői gépeken. A helyi Linux-fürt beállítása hasznos a Linux-fürtökre szánt alkalmazások gyors teszteléséhez, de Windows-gépen fejlesztették ki őket.

## <a name="prerequisites"></a>Előfeltételek
A Linux-alapú Service Fabric-fürtök nem futnak natívan a Windows rendszeren. Egy helyi Service Fabric-fürt futtatásához egy előre konfigurált Docker-tárolórendszerkép áll rendelkezésre. A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM
* A [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows) legújabb verziója
* A Dockernek Linux módban kell futnia

>[!TIP]
> * A Docker windowsos telepítéséhez kövesse a docker telepítéséhez a hivatalos [Docker-dokumentációban](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) említett lépéseket. 
> * Ha kész a telepítéssel, az [itt](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) említett lépéseket követve ellenőrizheti, hogy sikeres volt-e a telepítés.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy szolgáltatásháló-fürt futtatásához hajtsa végre a következő lépéseket a PowerShellben:


1. Frissítse a Docker-démon konfigurációját a gazdagépen a következő értékekkel, és indítsa újra a Docker-démont: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A javasolt módja annak, hogy frissítse - menjen a Docker Icon > Beállítások > Daemon > Advanced és frissítse ott. Ezután indítsa újra a Docker démont a módosítások érvénybe léptetéséhez. 

2. Hozzon létre egy `Dockerfile` nevű fájlt egy új könyvtárban a Service Fabric-rendszerkép létrehozásához:

    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:latest
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Módosíthatja úgy a fájlt, hogy az további programokat vagy függőségeket adjon hozzá a tárolóhoz.
    >A `RUN apt-get install nodejs -y` részlet hozzáadása például lehetővé teszi a `nodejs`-alkalmazások vendég végrehajtható fájlokként való támogatását.
    
    >[!TIP]
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Bizonyos módosításokért látogasson el a [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) oldalra

3. Az újrafelhasználható rendszerképnek a `Dockerfile` fájlból történő létrehozásához nyissa meg a terminált, és használja a `cd` parancsot a `Dockerfile` fájlt tartalmazó könyvtáron, majd futtassa az alábbi parancsot:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ez a művelet eltarthat egy ideig, de csak egyszer kell végrehajtani.

4. Most már gyorsan hozzáadhatja a Service Fabric egy helyi másolatát, amikor csak szükség van rá, az alábbi parancs futtatásával:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Ha megad egy nevet a tárolópéldányhoz, átláthatóbb módon kezelheti. 
    >
    >Ha az alkalmazás bizonyos portokon figyel, a portokat további `-p` címkékkel kell meghatározni. Ha például az alkalmazás a 8080-as porton figyel, adja hozzá az alábbi `-p` címkét:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest`
    >

5. A fürt indítása rövid időt vesz igénybe, a naplókat a következő paranccsal tekintheti [http://localhost:19080](http://localhost:19080)meg, vagy az irányítópultra ugorhat a fürtök állapotának megtekintéséhez:

    ```powershell 
    docker logs sftestcluster
    ```

6. Az 5. ``http://localhost:19080`` Ezen a ponton csatlakozhat ehhez a fürthöz a Windows fejlesztői gép bármely eszközével, és linuxos Service Fabric-fürtökre irányuló alkalmazások at helyezhet üzembe. 

    > [!NOTE]
    > Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

7. Ha elkészült, állítsa le és tisztítsa meg a tárolót ezzel a paranccsal:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Ismert korlátozások 
 
 A Mac gép tárolójában futó helyi fürt ismert korlátozásai a következők: 
 
 * A DNS-szolgáltatás nem fut, és nem támogatott – [132. probléma](https://github.com/Microsoft/service-fabric/issues/132).

## <a name="next-steps"></a>További lépések
* Az [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse) – ismerkedés
* További [Java-minták kijelentkezése](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
