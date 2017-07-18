---
title: "A fejlesztési környezet beállítása Linuxon | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet és az SDK-t, majd hozzon létre egy helyi fejlesztési fürtöt Linuxon. A beállítás befejezése után készen áll az alkalmazások létrehozására."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: da6a8b4824d7215eb1db131680856ac04003f5aa
ms.contentlocale: hu-hu
ms.lasthandoff: 07/13/2017

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

A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Frissítse az APT-forrásait
Az SDK és a kapcsolódó futtatókörnyezet-csomag apt-get parancssori eszköz használatával történő telepítéséhez először frissítenie kell az Advanced Packaging Tool- (APT-) forrásait.

1. Nyisson meg egy terminált.
2. Adja hozzá a Service Fabric-adattárat a források listájához.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adja hozzá a(z) `dotnet`-adattárat a források listájához.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Adja hozzá az új Gnu Privacy Guard- (GnuPG- vagy GPG-) kulcsot az APT-kulcstárhoz.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Adja hozzá a hivatalos Docker GPG-kulcsot az APT-kulcstárhoz.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Állítsa be a Docker-tárházat.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Frissítse a csomaglistákat az újonnan hozzáadott adattárak szerint.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Az SDK telepítése és beállítása tárolókhoz és futtatható vendégalkalmazásokhoz

A források frissítése után telepítheti az SDK-t.

1. Telepítse a Service Fabric SDK-csomagot, erősítse meg a telepítést, és fogadja el a licencszerződést.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   A következő parancsok automatizálják a Service Fabric-csomagok licenceinek elfogadását:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    >   ```
    
2. Futtassa az SDK telepítőszkriptjét.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Az általános SDK-csomag telepítése után futtatható vendégalkalmazásokkal vagy tárolószolgáltatásokkal rendelkező alkalmazások hozhatók létre a `yo azuresfguest` vagy a `yo azuresfcontainer` futtatásával. Lehetséges, hogy be kell állítania a $NODE_PATH környezeti változót arra a helyre, ahol a csomópontmodulok találhatók. 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

Ha gyökérként használja a környezetet, előfordulhat, hogy a következő paranccsal kell beállítania a változót:

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> Érdemes lehet ezeket a parancsokat hozzáadni a ~/.bashrc fájlhoz, hogy ne kelljen minden bejelentkezésnél beállítania a környezeti változót.
>

## <a name="set-up-the-xplat-service-fabric-cli"></a>Az XPlat Service Fabric parancssori felület beállítása
Az [XPlat parancssori felület][azure-xplat-cli-github] a Service Fabric-entitásokkal, többek között fürtökkel és alkalmazásokkal folytatott interakcióra szolgáló parancsokat is tartalmaz. Node.js-alapú, ezért az alábbi utasítások végrehajtása előtt [ellenőrizze, hogy telepítve van-e a Node][install-node]:

1. Klónozza a GitHub-adattárat a fejlesztői gépére.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Váltson át a klónozott adattárra, és telepítse a parancssori felület függőségeit a Node Package Managerrel (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Hozzon létre szimbolikus csatolást a klónozott adattár `bin/azure` mappájából a(z) `/usr/bin/azure` mappába.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Végezetül engedélyezze a Service Fabric-parancsok automatikus kiegészítését.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

### <a name="set-up-azure-cli-20"></a>Az Azure CLI 2.0 beállítása

Az Azure CLI már egy Service Fabric-parancsmodult is tartalmaz az XPlat parancssori felület alternatívájaként.

További információ az Azure CLI 2.0 telepítéséről és a Service Fabric-parancsok használatáról: [Bevezetés a Service Fabric és az Azure CLI 2.0 használatába](service-fabric-azure-cli-2-0.md).

## <a name="set-up-a-local-cluster"></a>Helyi fürt beállítása
Ha a telepítés sikeres volt, elindíthatja a helyi fürtöt.

1. Futtassa a fürttelepítési szkriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Indítson el egy webböngészőt, és nyissa meg a [Service Fabric Explorert](http://localhost:19080/Explorer). Ha a fürt elindult, megjelenik a Service Fabric Explorer irányítópultja.

    ![Service Fabric Explorer Linuxon][sfx-linux]

Ezen a ponton előzetesen összeállított Service Fabric-alkalmazáscsomagokat, vagy vendégtárolókon és vendég futtatható fájlokon alapuló új alkalmazáscsomagokat helyezhet üzembe. Ha új szolgáltatásokat szeretne létrehozni a Java vagy a .NET Core SDK-k használatával, kövesse az ezt követő szakaszokban megadott opcionális beállítási lépéseket.


> [!NOTE]
> Az önálló fürtök Linuxon nem támogatottak. Az előzetes verzió csak a beépített és az Azure Linux többgépes fürtöket támogatja.
>

## <a name="install-the-java-sdk-optional-if-you-want-to-use-the-java-programming-models"></a>A Java SDK telepítése (nem kötelező, csak ha használni kívánja a Java programozási modelleket)
A Java SDK biztosítja a Javát használó Service Fabric-szolgáltatások létrehozásához szükséges kódtárakat és sablonokat.

1. Telepítse a Java SDK-csomagot.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Futtassa az SDK telepítőszkriptjét.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Az Eclipse Neon beépülő modul telepítése (nem kötelező)

A Service Fabric Eclipse beépülő modulját a **Java-fejlesztőknek készült Eclipse IDE-ből** is telepítheti. Az Eclipse segítségével új Service Fabric futtatható vendégalkalmazásokat és tárolóalkalmazásokat, valamint Service Fabric Java-alkalmazásokat hozhat létre.

> [!NOTE]
> Az Eclipse beépülő modul használatának előfeltétele a Java SDK még akkor is, ha csak futtatható vendégalkalmazásokhoz és tárolóalkalmazásokhoz használja.
>

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van a legújabb Eclipse Neon és a Buildship 1.0.17-es vagy újabb verziója. A telepített összetevők verzióját a **Súgó** > **Telepítés részletei** lehetőség kiválasztásával ellenőrizheti. A Buildship frissítéséhez kövesse az [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez) című témakör utasításait.

2. A Service Fabric beépülő modul telepítéséhez válassza a **Help** > **Install New Software** (Súgó, Új szoftver telepítése) elemet.

3. A **Work with** (Használat) mezőbe írja be a **http://dl.microsoft.com/eclipse** címet.

4. Kattintson az **Add** (Hozzáadás) parancsra.

    ![Az Available Software (Elérhető szoftver) oldal][sf-eclipse-plugin]

5. Válassza ki a **ServiceFabric** beépülő modult, majd kattintson a **Next** (Tovább) gombra.

6. Végezze el a telepítés lépéseit, majd fogadja el a végfelhasználói licencszerződést.

Ha a Service Fabric Eclipse beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. Ennek ellenőrzéséhez válassza a **Súgó** > **Telepítés részletei** elemet, majd keresse meg a Service Fabricet a telepített beépülő modulok listájában. Válassza a **Frissítés** lehetőséget, ha újabb verzió érhető el.

További információ: [Service Fabric beépülő modul az Eclipse-alapú Java-alkalmazásfejlesztéshez](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>A .NET Core SDK telepítése (nem kötelező, csak ha használni szeretné a .NET Core programozási modelleket)
A .NET Core SDK biztosítja a .NET Core-t használó Service Fabric-szolgáltatások létrehozásához szükséges kódtárakat és sablonokat.

1. Telepítse a .NET Core SDK-csomagot.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Futtassa az SDK telepítőszkriptjét.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="update-the-sdk-and-runtime"></a>Az SDK és a futtatókörnyezet frissítése

Az SDK és a futtatókörnyezet legújabb verziójára történő frissítéshez futtassa a következő parancsokat (távolítsa el a listából a frissíteni nem kívánt SDK-kat):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> A csomagok frissítése miatt előfordulhat, hogy a helyi fejlesztési fürt leáll. Frissítés után az oldalon található utasításokat követve indítsa újra a helyi fürtöt.

## <a name="next-steps"></a>Következő lépések
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Az első CSharp-alkalmazás létrehozása Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
* [A fejlesztőkörnyezet előkészítése OSX-en](service-fabric-get-started-mac.md)
* [Az XPlat CLI használata a Service Fabric-alkalmazások kezeléséhez](service-fabric-azure-cli.md)
* [Service Fabric – Különbségek Windows és Linux rendszeren](service-fabric-linux-windows-differences.md)

## <a name="related-articles"></a>Kapcsolódó cikkek

* [A Service Fabric és az Azure CLI 2.0 használatának első lépései](service-fabric-azure-cli-2-0.md)
* [A Service Fabric XPlat parancssori felület használatának első lépései](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

