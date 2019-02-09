---
title: A Windows Azure Service Fabric Linux-fürt beállítása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan állítsa be a Windows fejlesztői gépeken futó Service Fabric Linux-fürtöket. Ez különösen hasznos a platformfüggetlen fejlesztői platform.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 60fd4a3ba83370327b6be775d7b2723fbb26de64
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983440"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>A Windows fejlesztői gépen a Linux Service Fabric-fürt beállítása

Ez a dokumentum bemutatja, hogyan állíthat be egy helyi Linux Service Fabric Windows fejlesztői gépeken. Egy helyi Linux-fürt beállítása Linux-fürtök esetén megcélzó alkalmazások gyors teszteléséhez hasznos, de egy Windows-gépen lettek kifejlesztve.

## <a name="prerequisites"></a>Előfeltételek
Linux-alapú Service Fabric-fürtök natív módon futtatható Windows. Egy helyi Service Fabric-fürt futtatásához egy előre konfigurált Docker-tároló lemezképet biztosítunk. A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM
* A [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows) legújabb verziója

>[!TIP]
> * Követheti a hivatalos docker lépéseket [dokumentáció](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) Docker telepíthető a Windows. 
> * Ha kész a telepítéssel, az [itt](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) említett lépéseket követve ellenőrizheti, hogy sikeres volt-e a telepítés.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy service fabric-fürt rajta való futtatásához, hajtsa végre az alábbi lépéseket a PowerShellben:


1. Frissítse a Docker-démon konfigurációját a gazdagépen a következő értékekkel, és indítsa újra a Docker-démont: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A területen végrehajtani frissítés módja – a Docker ikon > Beállítások > démon > speciális a frissítést javasolt. Ezután indítsa újra a Docker-démon a módosítások érvénybe léptetéséhez. 

2. Hozzon létre egy `Dockerfile` nevű fájlt egy új könyvtárban a Service Fabric-rendszerkép létrehozásához:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
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
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Adott változatokért látogasson el a [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) oldalára

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
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. A fürt elindulása eltart egy rövid ideig. A naplók megtekintéséhez vagy az irányítópultra való ugráshoz és a fürt állapotának megtekintéséhez használja az alábbi parancsot [http://localhost:19080](http://localhost:19080):

    ```powershell 
    docker logs sftestcluster
    ```

6. 5. lépés sikeres befejezése után léphet ``http://localhost:19080`` a Windows, és elvileg látnia kell a Service Fabric Explorert. Ezen a ponton csatlakozik a fürthöz, minden olyan eszközökkel, a Windows fejlesztői gépen, és a kijelölt Linux Service Fabric-fürtök üzembe helyezése. 

    > [!NOTE]
    > Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

7. Amikor elkészült, állítsa le és tisztíthatja meg a tárolót a következő paranccsal:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Ismert korlátozások 
 
 A Mac gép tárolójában futó helyi fürt ismert korlátozásai a következők: 
 
 * A DNS-szolgáltatás nem fut, és nem támogatott – [132. probléma](https://github.com/Microsoft/service-fabric/issues/132).

## <a name="next-steps"></a>További lépések
* Ismerkedés a [eclipse-ben](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Tekintse meg az egyéb [Java-példák](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png