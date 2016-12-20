---
title: "A fejlesztési környezet beállítása Linuxon | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet és az SDK-t, majd hozzon létre egy helyi fejlesztési fürtöt Linuxon. A beállítás befejezése után készen áll az alkalmazások létrehozására."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 7e33e00a676f4aa7143cede3380adb58ba1d11e4
ms.openlocfilehash: c27b92d4a1a1bd96ad0bdc46363b8066abc72edd


---
# <a name="prepare-your-development-environment-on-linux"></a>A fejlesztőkörnyezet előkészítése Linuxon
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
>  

 Az [Azure Service Fabric-alkalmazásoknak](service-fabric-application-model.md) a linuxos fejlesztői gépen való üzembe helyezéséhez és futtatásához telepítse a futtatókörnyezetet és az általános SDK-t. A Javához és a .NET Core-hoz készült opcionális SDK-kat is telepítheti.

## <a name="prerequisites"></a>Előfeltételek
### <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók
A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Frissítse az apt-forrásait
Az SDK és a kapcsolódó futtatókörnyezet-csomag apt-get használatával történő telepítéséhez először frissítenie kell az apt-forrásait.

1. Nyisson meg egy terminált.
2. Adja hozzá a Service Fabric-adattárat a források listájához.
   
    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Adja hozzá az új GPG-kulcsot az apt-kulcstárhoz.
   
    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
4. Frissítse a csomaglistákat az újonnan hozzáadott adattárak szerint.
   
    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Az SDK telepítése és beállítása
A források frissítése után telepítheti az SDK-t.

1. Telepítse a Service Fabric SDK-csomagot. A rendszer fel fogja kérni, hogy erősítse meg a telepítést, és fogadjon el egy licencszerződést.
   
    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Futtassa az SDK telepítőszkriptjét.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>A platformfüggetlen Azure parancssori felület beállítása
A [platformfüggetlen Azure parancssori felület][azure-xplat-cli-github] a Service Fabric-entitásokkal, többek között fürtökkel és alkalmazásokkal folytatott interakcióra szolgáló parancsokat is tartalmaz. Node.js-alapú, ezért az alábbi utasítások végrehajtása előtt [ellenőrizze, hogy telepítve van-e a Node][install-node].

1. Klónozza a github-adattárat a fejlesztői gépére.
   
    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Váltson át a klónozott adattárra, és telepítse a parancssori felület függőségeit a Node Package Managerrel (npm).
   
    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Hozzon létre szimbolikus csatolást a klónozott adattár bin/azure mappájából az /usr/bin/azure mappába, hogy bekerüljön az elérési útjába, és a parancsok bármelyik könyvtárból elérhetők legyenek.
   
    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Végezetül engedélyezze a Service Fabric-parancsok automatikus kiegészítését.
   
    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Helyi fürt beállítása
Ha mindent sikeresen telepített, elindíthatja a helyi fürtöt.

1. Futtassa a fürttelepítési szkriptet.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: http://localhost:19080/Explorer. Ha a fürt elindult, megjelenik a Service Fabric Explorer irányítópultja.
   
    ![Service Fabric Explorer Linuxon][sfx-linux]

Ezen a ponton előzetesen összeállított Service Fabric-alkalmazáscsomagokat, vagy vendégtárolókon és vendég futtatható fájlokon alapuló új alkalmazáscsomagokat helyezhet üzembe. Ha új szolgáltatásokat kíván létrehozni a Java vagy .NET Core SDK-k használatával, kövesse az alábbi opcionális lépéseket.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>A Java SDK és az Eclipse Neon beépülő modul telepítése (nem kötelező)
A Java SDK biztosítja a Java-t használó Service Fabric-szolgáltatások létrehozásához szükséges kódtárakat és sablonokat.

1. Telepítse a Java SDK-csomagot.
   
    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Futtassa az SDK telepítőszkriptjét.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

A Service Fabric Eclipse beépülő modulját az Eclipse Neon IDE-ből is telepítheti.

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van a Buildship 1.0.17-es vagy újabb verziója. A telepített összetevők verzióját a **Help > Installation Details** (Súgó > Telepítés részletei) lehetőség kiválasztásával ellenőrizheti. Az [itt][buildship-update] található utasítások alapján frissítheti a Buildshipet.
2. A Service Fabric beépülő modul telepítéséhez válassza a **Help > Install New Software...** (Súgó > Új szoftver telepítése) lehetőséget.
3. A Work with (Használat) szövegbeviteli mezőbe írja be a következőt: http://dl.windowsazure.com/eclipse/servicefabric
4. Kattintson az Add (Hozzáadás) parancsra.
   
    ![Eclipse beépülő modul][sf-eclipse-plugin]
5. Válassza ki a Service Fabric beépülő modult, majd kattintson a Next (Tovább) gombra.
6. Folytassa a telepítést, és fogadja el a végfelhasználói licencszerződést.

## <a name="install-the-net-core-sdk-optional"></a>A .NET Core SDK telepítése (opcionális)
A .NET Core SDK biztosítja a platformfüggetlen .NET Core-t használó Service Fabric-szolgáltatások létrehozásához szükséges kódtárakat és sablonokat.

1. Telepítse a .NET Core SDK-csomagot.
   
    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```
2. Futtassa az SDK telepítőszkriptjét.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Következő lépések
* [Az első Java-alkalmazás létrehozása Linuxon](service-fabric-create-your-first-linux-application-with-java.md)
* [A fejlesztőkörnyezet előkészítése OSX-en](service-fabric-get-started-mac.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png



<!--HONumber=Nov16_HO4-->


