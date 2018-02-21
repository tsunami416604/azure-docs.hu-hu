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
ms.date: 1/5/2018
ms.author: subramar
ms.openlocfilehash: b39a5790e5a2ef5483428842b537e0189f5f2b32
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>A fejlesztőkörnyezet előkészítése Linuxon
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Az [Azure Service Fabric-alkalmazásoknak](service-fabric-application-model.md) a linuxos fejlesztői gépen való üzembe helyezéséhez és futtatásához telepítse a futtatókörnyezetet és az általános SDK-t. A Javához és a .NET Core-fejlesztéshez készült opcionális SDK-kat is telepítheti.

## <a name="prerequisites"></a>Előfeltételek

A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="installation-methods"></a>Telepítési módok

### <a name="1-script-installation"></a>1. Telepítés szkripttel

Az egyszerű használat érdekében megadunk egy szkriptet a Service Fabric-futtatókörnyezet és a Service Fabric általános SDK az **sfctl** CLI felülettel együtt történő telepítéséhez. Kövesse a következő szakaszban található manuális telepítési lépéseket, ha el szeretné dönteni, mi legyen telepítve és mely licenceket fogadja el. A szkript a futtatáskor azt feltételezi, hogy Ön átolvasta és elfogadja a telepített szoftverek licencfeltételeit. 

A szkript sikeres végrehajtását követően közvetlenül a [Helyi fürt beállítása](#set-up-a-local-cluster) lépéssel folytathatja.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Manuális telepítés
A Service Fabric-futtatókörnyezet és az általános SDK manuális telepítéséhez kövesse ezt az útmutatót.

## <a name="update-your-apt-sources"></a>Frissítse az APT-forrásait
Az SDK és a kapcsolódó futtatókörnyezet-csomag apt-get parancssori eszköz használatával történő telepítéséhez először frissítenie kell az Advanced Packaging Tool- (APT-) forrásait.

1. Nyisson meg egy terminált.
2. Adja hozzá a Service Fabric-adattárat a források listájához.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
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
    sudo apt-get install curl
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

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>A Service Fabric SDK telepítése és beállítása helyi fürtbeállításhoz

A források frissítése után telepítheti az SDK-t. Telepítse a Service Fabric SDK-csomagot, erősítse meg a telepítést, és fogadja el a licencszerződést.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   A következő parancsok automatizálják a Service Fabric-csomagok licenceinek elfogadását:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Helyi fürt beállítása
  Ha a telepítés befejeződött, elindíthatja a helyi fürtöt.

  1. Futtassa a fürttelepítési szkriptet.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Indítson el egy webböngészőt, és nyissa meg a [Service Fabric Explorert](http://localhost:19080/Explorer). Ha a fürt elindult, megjelenik a Service Fabric Explorer irányítópultja.

      ![Service Fabric Explorer Linuxon][sfx-linux]

  Ezen a ponton előzetesen összeállított Service Fabric-alkalmazáscsomagokat, vagy vendégtárolókon és vendég futtatható fájlokon alapuló új alkalmazáscsomagokat helyezhet üzembe. Ha új szolgáltatásokat szeretne létrehozni a Java vagy a .NET Core SDK-k használatával, kövesse az ezt követő szakaszokban megadott opcionális beállítási lépéseket.


  > [!NOTE]
  > Az önálló fürtök Linuxon nem támogatottak.
  >


>   [!TIP]
    Ha van kéznél egy SSD lemez, az igazán kiváló teljesítmény érdekében érdemes egy SSD-könyvtárútvonalat továbbítani a `--clusterdataroot` és a devclustersetup.sh használatával.

## <a name="set-up-the-service-fabric-cli"></a>A Service Fabric parancssori felület beállítása

A [Service Fabric parancssori felület](service-fabric-cli.md) a Service Fabric-entitásokkal, többek között fürtökkel és alkalmazásokkal folytatott interakcióra szolgáló parancsokat is tartalmaz.
A parancssori felület telepítéséhez kövesse a [Service Fabric parancssori felület](service-fabric-cli.md) utasításait.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Yeoman-generátorok beállítása a tárolókhoz és vendégalkalmazásokhoz
A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyek segítségével Service Fabric-alkalmazásokat hozhat létre a terminálból Yeoman-sablongenerátorok használatával. Hajtsa végre ezeket a lépéseket a Service Fabric Yeoman-sablongenerátorok beállításához:

1. A node.js és az NPM telepítése a gépre

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

2. A [Yeoman](http://yeoman.io/) sablongenerátor telepítése a gépre az NPM-ből

  ```bash
  sudo npm install -g yo
  ```
3. A Service Fabric Yeo tárológenerátor és futtatható vendégalkalmazás-generátor telepítése az NPM-ből

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

A generátorok telepítése után futtatható vendégalkalmazásokat vagy tárolószolgáltatásokat hozhat létre a `yo azuresfguest` vagy a `yo azuresfcontainer` futtatásával.

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 fejlesztői környezet beállítása

Telepítse az [Ubuntu rendszerre készült .NET Core 2.0 SDK-t](https://www.microsoft.com/net/core#linuxubuntu) [a C# Service Fabric-alkalmazások létrehozásának](service-fabric-create-your-first-linux-application-with-csharp.md) első lépéseként. A .NET Core 2.0 Service Fabric-alkalmazások csomagjai a NuGet.org webhelyen érhetők el, jelenleg előzetes verzióban.

## <a name="set-up-java-development"></a>Java fejlesztői környezet beállítása

A Service Fabric-szolgáltatások Java használatával történő létrehozásához telepítse a JDK 1.8-at és a Gradle-t az összeállítási feladatok futtatásához. Az Open JDK 1.8 és a Gradle az alábbi kódrészlettel telepíthető. A Service Fabric Java-kódtárakat a Mavenből kéri le a rendszer.

 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Az Eclipse Neon beépülő modul telepítése (nem kötelező)

A Service Fabric Eclipse beépülő modulját a Java-fejlesztőknek készült Eclipse IDE-ből telepítheti. Az Eclipse segítségével új Service Fabric futtatható vendégalkalmazásokat és tárolóalkalmazásokat, valamint Service Fabric Java-alkalmazásokat hozhat létre.

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van a legújabb Eclipse Neon és a Buildship 1.0.17-es vagy újabb verziója. A telepített összetevők verzióját a **Súgó** > **Telepítés részletei** lehetőség kiválasztásával ellenőrizheti. A Buildship frissítéséhez kövesse az [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez) című témakör utasításait.

2. A Service Fabric beépülő modul telepítéséhez válassza a **Help** > **Install New Software** (Súgó, Új szoftver telepítése) elemet.

3. A **Work with** (Használat) mezőbe írja be a **http://dl.microsoft.com/eclipse** címet.

4. Kattintson a **Hozzáadás** parancsra.

    ![Az Available Software (Elérhető szoftver) oldal][sf-eclipse-plugin]

5. Válassza ki a **ServiceFabric** beépülő modult, majd kattintson a **Next** (Tovább) gombra.

6. Végezze el a telepítés lépéseit, majd fogadja el a végfelhasználói licencszerződést.

Ha a Service Fabric Eclipse beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. Ennek ellenőrzéséhez válassza a **Súgó** > **Telepítés részletei** elemet, majd keresse meg a Service Fabricet a telepített beépülő modulok listájában. Válassza a **Frissítés** lehetőséget, ha újabb verzió érhető el.

További információ: [Service Fabric beépülő modul az Eclipse-alapú Java-alkalmazásfejlesztéshez](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Az SDK és a futtatókörnyezet frissítése

Az SDK és a futtatókörnyezet legújabb verziójára történő frissítéshez futtassa a következő parancsokat:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
A Java SDK bináris fájljainak a Mavenből való frissítéséhez frissítenie kell a megfelelő bináris fájl verzióadatait a ``build.gradle`` fájlban, hogy azok a legfrissebb verzióra mutassanak. A verzió frissítésének pontos helyét bármelyik ``build.gradle`` fájlból megtudhatja a Service Fabric első lépéseit bemutató példákból [ezen a helyen](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> A csomagok frissítése miatt előfordulhat, hogy a helyi fejlesztési fürt leáll. Frissítés után az oldalon található utasításokat követve indítsa újra a helyi fürtöt.

## <a name="remove-the-sdk"></a>Az SDK eltávolítása
A Service Fabric SDK-k eltávolításához futtassa a következőket:

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## <a name="next-steps"></a>További lépések

* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Az első CSharp-alkalmazás létrehozása Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
* [A fejlesztőkörnyezet előkészítése OSX-en](service-fabric-get-started-mac.md)
* [Linux fejlesztőkörnyezet előkészítése Windowson](service-fabric-local-linux-cluster-windows.md)
* [A Service Fabric parancssori felület használata az alkalmazások kezeléséhez](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric – Különbségek Windows és Linux rendszeren](service-fabric-linux-windows-differences.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
