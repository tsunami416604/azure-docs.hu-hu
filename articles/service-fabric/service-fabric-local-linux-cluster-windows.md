---
title: "A Windows Azure Service Fabric Linux-fürt beállítása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan állíthatja be a Windows fejlesztői gépeken futó Service Fabric Linux-fürtök. Ez különösen fontos a platform fejlesztési közötti."
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: f21561269e90e3643ef5d8d48ee28712ee7f611c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>A Windows fejlesztői gépen Linux Service Fabric-fürt beállítása

Ez a dokumentum bemutatja, hogyan állíthat be egy helyi Linux Service Fabric Windows fejlesztői gépen. Egy helyi Linux-fürt beállítása hasznos a Linux-fürtök megcélzó alkalmazások gyors teszteléséhez, de fejlesztett Windows-gépen.

## <a name="prerequisites"></a>Előfeltételek
Service Fabric-fürtök Linux-alapú Windows natív módon nem futnak. Egy helyi Service Fabric-fürt futtatására a előre konfigurált Docker-tároló lemezkép valósul meg. A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM
* A [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows) legújabb verziója
* Hozzáférés a Service Fabric Docker-tároló beépített [rendszerképéhez](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)

>[!TIP]
> * A hivatalos Docker szerepel a lépések követésével [dokumentáció](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) Docker telepíthető a Windows. 
> * Ha kész a telepítéssel, az [itt](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) említett lépéseket követve ellenőrizheti, hogy sikeres volt-e a telepítés.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy Service Fabric-fürt rajta való futtatásához hajtsa végre a következő lépéseket:

1. A rendszerkép lekérése a Docker Hub adattárából:

    ```powershell
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Frissítse a Docker-démon konfigurációját a gazdagépen a következő értékekkel, és indítsa újra a Docker-démont: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A elkeverni frissítése módja - Ugrás a Docker ikon > Beállítások > démon > speciális és az ott a frissítést. Ezután indítsa újra a Docker démon a módosítások életbe léptetéséhez. 

3. Indítson el egy Service Fabric beépített tárolópéldányt a rendszerképpel:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Ha megad egy nevet a tárolópéldányhoz, átláthatóbb módon kezelheti. 
    > * Ha az alkalmazás bizonyos portokon figyel, ezt további -p címkékkel kell meghatározni. Ha például az alkalmazás a 8080-as porton figyel, futtassa a következőt: docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. Jelentkezzen be a Docker-tárolóba interaktív SSH-módban:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Futtassa a beállítási szkriptet, amely lehívja a szükséges függőségeket, és ezután elindítja a fürtöt a tárolón.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 5. lépés sikeres befejezését követően elvégezheti ``http://localhost:19080`` a Windows, és képes lenne a Service Fabric Explorerben talál. Ezen a ponton csatlakozzon a fürthöz, bármely számítógépről a fejlesztői eszközök segítségével, és a Linux Service Fabric-fürtök szánt alkalmazás központi telepítése. 

    > [!NOTE]
    > Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

## <a name="next-steps"></a>Következő lépések
* Ismerkedés a [eclipse-ben](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Tekintse meg az egyéb [Java-minták](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png