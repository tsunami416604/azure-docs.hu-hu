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
ms.date: 09/26/2017
ms.author: saysa
ms.openlocfilehash: 0fae5fe35c25f97a9eb2c0d648cfb0f66b7f0725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>A fejlesztési környezet beállítása Mac OS X-en
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux-fürtökön futó Service Fabric-alkalmazásokat hozhat létre a Mac OS X-en. Ez a cikk bemutatja, hogyan állíthatja be Mac gépét a fejlesztéshez.

## <a name="prerequisites"></a>Előfeltételek
A Service Fabric nem fut natív módon az OS X-en. A helyi Service Fabric-fürt futtatásához egy Vagrant és VirtualBox alkalmazásokat használó, előre konfigurált Ubuntu-virtuális gépet biztosítunk. A kezdés előtt a következőkre lesz szüksége:

* [Vagrant (1.8.4-es vagy újabb verzió)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> A Vagrant és a VirtualBox kölcsönösen támogatott verzióját kell használnia. A Vagrant kiszámíthatatlan módon viselkedhet egy nem támogatott VirtualBox-verzión.
>

## <a name="create-the-local-vm"></a>A helyi virtuális gép létrehozása
Egy 5 csomópontos Service Fabric-fürtöt tartalmazó virtuális gép létrehozásához hajtsa végre a következő lépéseket:

1. A `Vagrantfile` adattár klónozása

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Ezzel a lépéssel letölti a virtuálisgép-konfigurációt tartalmazó `Vagrantfile` fájlt, valamint lekéri azt a helyet, ahonnan a virtuális gép le lett töltve.  A fájl egy tárolt Ubuntu-képre mutat.

2. Navigáljon az adattár helyi klónjához

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Nem kötelező) Módosítsa a virtuális gép alapértelmezett beállításait

    Alapértelmezés szerint a helyi virtuális gép konfigurációja a következő:

   * 3 GB lefoglalt memória
   * A 192.168.50.50 IP-címre konfigurált privát gazdagép-hálózat, amely átengedi a Mac-gazdagépről érkező forgalmat

     A `Vagrantfile`-lal bármelyik beállítást módosíthatja, és más konfigurációkat adhat a virtuális géphez. A konfigurációs beállítások teljes listájáért tekintse meg a [Vagrant dokumentációját](http://www.vagrantup.com/docs).
4. Virtuális gép létrehozása

    ```bash
    vagrant up
    ```


5. Jelentkezzen be a virtuális gépre, és telepítse a Service Fabric SDK-t.

    ```bash
    vagrant ssh
    ```

   Telepítse az SDK-t az [SDK telepítését](service-fabric-get-started-linux.md) ismertető részben foglaltaknak megfelelően.  Az egyszerű használat érdekében alább megadunk egy szkriptet a Service Fabric-futtatókörnyezet és a Service Fabric általános SDK az sfctl CLI felülettel együtt történő telepítéséhez. A szkript a futtatáskor azt feltételezi, hogy Ön átolvasta és elfogadja a telepített szoftverek licencfeltételeit.

    ```bash
    sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
    ```

5.  Indítsa el a Service Fabric-fürtöt

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    >[!TIP]
    > Ha a virtuális gép letöltése hosszú ideig tart, letöltheti a wget vagy a curl használatával, vagy egy böngészőből a `Vagrantfile` fájlban található **config.vm.box_url** által megadott hivatkozásra lépve. Miután letöltötte helyileg, szerkessze a `Vagrantfile`-t, hogy arra a helyi elérési útra mutasson, ahová a rendszerképet letöltötte. Ha a rendszerképet például a /home/users/test/azureservicefabric.tp8.box helyre töltötte le, állítsa a **config.vm.box_url** címet erre az elérési útra.
    >

5. Keresse fel a Service Fabric Explorert a http://192.168.50.50:19080/Explorer címen (feltéve, hogy megtartotta az alapértelmezett magánhálózati IP-címet) a fürt megfelelő beállításának teszteléséhez.

    ![A Service Fabric Explorer a Mac gazdagépről megtekintve][sfx-mac]

## <a name="install-the-necessary-java-artifacts-on-vagrant-to-use-service-fabric-java-programming-model"></a>A Service Fabric Java programozási modelljének használatához telepítse a szükséges Java-összetevőket a Vagrantra

A Service Fabric-szolgáltatások Java használatával történő létrehozásához telepítve kell lennie a JDK 1.8 készletnek és a Gradle-nek, amely az összeállítási feladatok futtatásához használható. Az Open JDK 1.8 és a Gradle az alábbi kódrészlettel telepíthető. A Service Fabric Java-kódtárakat a Mavenből kéri le a rendszer.

  ```bash
  vagrant ssh
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
```

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>A Service Fabric parancssori felület (sfctl) beállítása Mac gépen

A Service Fabric parancssori felület (`sfctl`) Mac gépre való telepítéséhez kövesse a [Service Fabric parancssori felület](service-fabric-cli.md#cli-mac) utasításait.
A parancssori felület a Service Fabric-entitásokkal, többek között fürtökkel, alkalmazásokkal és szolgáltatásokkal folytatott interakciók elvégzésére szolgáló parancsokat is tartalmaz.

## <a name="create-application-on-you-mac-using-yeoman"></a>Alkalmazás létrehozása Mac gépen a Yeoman használatával

A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyek segítségével Service Fabric-alkalmazásokat hozhat létre a terminálról a Yeoman sablongenerátor használatával. Az alábbi lépések végrehajtásával ellenőrizze, hogy a Service Fabric Yeoman sablongenerátor működik-e a gépen.

1. A Node.js-nek és az NPM-nek telepítve kell lennie Mac gépén. Ha a Node.js és az NPM nincs telepítve, a következők használatával telepítheti azokat a Homebrew-val. A Mac gépén telepített Node.js és NPM verzióinak ellenőrzéséhez használhatja a ``-v`` beállítást.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. A [Yeoman](http://yeoman.io/) sablongenerátor telepítése a gépre az NPM-ből

  ```bash
  npm install -g yo
  ```
3. Telepítse a használni kívánt Yeoman generátort az első lépéseket ismertető [dokumentációban](service-fabric-get-started-linux.md) leírt lépések végrehajtásával. Service Fabric-alkalmazások a Yeoman használatával való létrehozásához kövesse az alábbi lépéseket –

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Service Fabric Java-alkalmazások Mac gépen való létrehozásához a JDK 1.8-nak és a Gradle-nek telepítve kell lennie a gépen.

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 fejlesztői környezet beállítása

Telepítse az [Mac rendszerre készült .NET Core 2.0 SDK-t](https://www.microsoft.com/net/core#macos) [a C# Service Fabric-alkalmazások létrehozásának](service-fabric-create-your-first-linux-application-with-csharp.md) első lépéseként. A .NET Core 2.0 Service Fabric-alkalmazások csomagjai a NuGet.org webhelyen érhetők el, jelenleg előzetes verzióban.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Az Eclipse Neonhoz készült Service Fabric beépülő modul telepítése

A Service Fabric egy beépülő modult biztosít a **Java Eclipse Neon IDE-hez**, amely leegyszerűsítheti a Java-szolgáltatások összeállításának, létrehozásának és üzembe helyezésének folyamatát. A Service Fabric Eclipse beépülő modul telepítéséhez és frissítéséhez érdemes az ebben az általános [dokumentációban](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) szereplő telepítési lépéseket követnie.

>[!TIP]
> Alapértelmezés szerint támogatjuk az alapértelmezett IP-címet a létrehozott alkalmazás ``Local.json`` fájljában lévő ``Vagrantfile``-ban leírtak szerint. Amennyiben ezt módosítja, és a Vagrant telepítéséhez más IP-címet használ, frissítse a megfelelő IP-címet az alkalmazás ``Local.json`` fájljában is.

## <a name="next-steps"></a>Következő lépések
<!-- Links -->
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Service Fabric-fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md)
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)
* [A Service Fabric parancssori felület használata az alkalmazások kezeléséhez](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
