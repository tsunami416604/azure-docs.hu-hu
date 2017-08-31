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
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 8b4fc0ab9034263418cac42ced203035e0a8fcad
ms.contentlocale: hu-hu
ms.lasthandoff: 08/23/2017

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
    Ezzel a lépéssel lekéri a virtuálisgép-konfigurációt tartalmazó `Vagrantfile` fájlt valamint azt a helyet, ahonnan a virtuális gép le lett töltve.

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

   Ebben a lépésben megtörténik az előre konfigurált virtuálisgép-rendszerkép letöltése, helyi indítása, és végül egy helyi Service Fabric-fürt beállítása. Ez eltarthat néhány percig. Ha a beállítás sikeres, a kimenetben megjelenik egy üzenet, amely jelzi, hogy a fürt indítása folyamatban van.

    ![A fürt beállításának megkezdése a virtuális gép kiépítése után][cluster-setup-script]

    >[!TIP]
    > Ha a virtuális gép letöltése hosszú ideig tart, letöltheti a wget vagy a curl használatával, vagy egy böngészőből a `Vagrantfile` fájlban található **config.vm.box_url** által megadott hivatkozásra lépve. Miután letöltötte helyileg, szerkessze a `Vagrantfile`-t, hogy arra a helyi elérési útra mutasson, ahová a rendszerképet letöltötte. Ha a rendszerképet például a /home/users/test/azureservicefabric.tp8.box helyre töltötte le, állítsa a **config.vm.box_url** címet erre az elérési útra.
    >

5. Keresse fel a Service Fabric Explorert a http://192.168.50.50:19080/Explorer címen (feltéve, hogy megtartotta az alapértelmezett magánhálózati IP-címet) a fürt megfelelő beállításának teszteléséhez.

    ![A Service Fabric Explorer a Mac gazdagépről megtekintve][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>Alkalmazás létrehozása Mac gépen a Yeoman használatával
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

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

