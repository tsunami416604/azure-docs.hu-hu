---
title: Az Azure Service Fabric Linux-fürt beállítása Windows rendszeren
description: Ez a cikk bemutatja, hogyan állíthat be Service Fabric Linux rendszerű fürtöket Windows-fejlesztői gépeken. Ez különösen hasznos a platformok közötti fejlesztéshez.
ms.topic: conceptual
ms.date: 11/20/2017
ms.openlocfilehash: 83d494d777a4a1e1586707c8848056ca8fe9780a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537072"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Linux Service Fabric-fürt beállítása a Windows fejlesztői gépen

Ez a dokumentum bemutatja, hogyan állíthat be helyi linuxos Service Fabric a Windows fejlesztői gépeken. A helyi Linux-fürtök beállítása hasznos lehet a Linux-fürtöket megcélzó alkalmazások gyors tesztelésére, de a Windows rendszerű gépeken való fejlesztésre.

## <a name="prerequisites"></a>Előfeltételek
A Linux-alapú Service Fabric fürtök nem futnak natív módon Windows rendszeren. Helyi Service Fabric-fürt futtatásához egy előre konfigurált Docker-tároló képe van megadva. A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM
* A [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows) legújabb verziója
* A Docker Linux-módban kell futnia

>[!TIP]
> * A Docker Windows rendszeren való telepítéséhez kövesse a hivatalos Docker [dokumentációjában](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) megemlített lépéseket. 
> * Ha kész a telepítéssel, az [itt](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) említett lépéseket követve ellenőrizheti, hogy sikeres volt-e a telepítés.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy Service Fabric-fürt futtatásához hajtsa végre a következő lépéseket a PowerShellben:


1. Frissítse a Docker-démon konfigurációját a gazdagépen a következő értékekkel, és indítsa újra a Docker-démont: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A frissítés javasolt módja: ugrás a Docker ikonjára > beállítások > Daemon > Advanced, és frissítsen ott. Ezután indítsa újra a Docker-démont, hogy a módosítások életbe lépnek. 

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
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Adott változatokért látogasson el a [Docker hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) oldalára.

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

5. A fürt elindulása eltart egy rövid ideig. A naplók megtekintéséhez vagy az irányítópultra való ugráshoz és a fürt állapotának megtekintéséhez használja az alábbi parancsot `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

6. Az 5. lépés sikeres befejezése után ``http://localhost:19080`` megtekintheti a Windowst, és láthatja a Service Fabric Explorert. Ezen a ponton a Windows fejlesztői gépről származó bármely eszközzel csatlakozhat ehhez a fürthöz, és telepítheti a Linux Service Fabric-fürtökre irányuló alkalmazást is. 

    > [!NOTE]
    > Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

7. Ha elkészült, állítsa le és törölje a tárolót a következő paranccsal:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Ismert korlátozások 
 
 A Mac gép tárolójában futó helyi fürt ismert korlátozásai a következők: 
 
 * A DNS-szolgáltatás nem fut, és nem támogatott – [132. probléma](https://github.com/Microsoft/service-fabric/issues/132).

## <a name="next-steps"></a>További lépések
* Ismerkedés az [Eclipse](./service-fabric-get-started-eclipse.md) -szel
* Tekintse meg a [Java-mintákat](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
