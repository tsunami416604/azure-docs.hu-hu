---
title: Spring Boot-alkalmazás létrehozása a Service Fabricben az Azure-ban | Microsoft Docs
description: Ez a rövid útmutató azt ismerteti, hogyan lehet Spring Boot-alkalmazást telepíteni az Azure Service Fabricre egy Spring Boot-mintaalkalmazás használatával.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e4fde75aeaf86219518daf92b67434fe9fd63f86
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297402"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-service-fabric"></a>Gyors útmutató: A Service Fabric Java Spring Boot-alkalmazás üzembe helyezése

Az Azure Service Fabric egy elosztott rendszerplatform, amely mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

Ez a rövid útmutató a Spring Boot-alkalmazás Service Fabricre történő telepítését mutatja be. A rövid útmutató a Spring webhelyének [első lépéseket ismertető](https://spring.io/guides/gs/spring-boot/) útmutatójában található mintát használja. Ez az ismerős parancssori eszközöket alkalmazó rövid útmutató végigvezet a Spring Boot-minta Service Fabric-alkalmazásként történő telepítésének lépésein. A folyamat végén a Spring Boot mintája működőképes állapotban lesz a Service Fabricen.

![Képernyőkép az alkalmazásról](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Ezen rövid útmutató segítségével megtanulhatja a következőket:

* Spring Boot-alkalmazás telepítése Service Fabricre
* Az alkalmazás központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra
* Feladatátvétel elvégzése a szolgáltatáson a rendelkezésre állás korlátozása nélkül

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

1. A Service Fabric SDK és a Service Fabric parancssori felület (CLI) telepítése

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [A Git telepítése](https://git-scm.com/)
1. A Yeoman telepítése

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. A Java-környezet beállítása

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a Spring Boot-mintaalkalmazás helyi számítógépre történő klónozásához.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>A Spring Boot-alkalmazás létrehozása 
1. Az alkalmazás létrehozásához futtassa az alábbi parancsot a `gs-spring-boot/complete` könyvtárban: 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>A Spring Boot-alkalmazás becsomagolása 
1. A klónban lévő `gs-spring-boot` könyvtárban futtassa a `yo azuresfguest` parancsot. 

1. Az egyes sorokban adja meg a következő adatokat.

    ![Yeoman-bejegyzések](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

1. A `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` mappában hozzon létre egy `entryPoint.sh` nevű fájlt. Adja a következőket az `entryPoint.sh` fájlhoz. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Adja hozzá az **Endpoints** erőforrást a `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml` fájlban:

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    A **ServiceManifest.xml** fájlnak most így kell kinéznie: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Mostanra más létrehozta Spring Boot mintájához tartozó Service Fabric-alkalmazást, amelyet a Service Fabricre telepíthet.

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

1. A helyi fürt Ubuntu rendszerű gépeken való elindításához futtassa a következő parancsot:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Mac rendszerű gép esetében indítsa el a helyi fürtöt a Docker-rendszerképből (ehhez az [előfeltételek](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) alapján be kell állítania a helyi fürtöt Mac rendszerhez). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    A helyi fürt elindítása eltarthat egy ideig. A fürt üzembe állásának ellenőrzéséhez nyissa meg a Service Fabric Explorert a **http://localhost:19080** címen. Ha öt kifogástalan csomópontot lát, a fürt megfelelően üzemel. 
    
    ![A helyi fürt kifogástalan](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

1. Navigáljon a `gs-spring-boot/SpringServiceFabric` mappához.
1. A helyi fürthöz való csatlakozáshoz futtassa a következő parancsot.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Futtassa az `install.sh` szkriptet.

    ```bash
    ./install.sh
    ```

1. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást a **http://localhost:8080** címen.

    ![Helyi alkalmazás kezelőfelülete](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Most már elérhető a Service Fabric-fürtre telepített Spring Boot-alkalmazás.

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A szolgáltatások skálázhatók egy adott fürtben, hogy kövessék a szolgáltatások terhelésének változásait. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik. A szolgáltatásokat többféleképpen is skálázhatja, használhat például szkripteket vagy a Service Fabric parancssori felület (sfctl) parancsait. A következő lépések során a Service Fabric Explorert használjuk.

A Service Fabric Explorer az összes Service Fabric-fürtben fut. Az eléréséhez egy böngészőben navigáljon az adott fürt HTTP-kezelési portjára (19080), például: `http://localhost:19080`.

A webes előtér-szolgáltatás skálázásához tegye a következőket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://localhost:19080`.
1. Kattintson a három pontra a fanézetben a **fabric:/SpringServiceFabric/SpringGettingStarted** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

    ![A Service Fabric Explorer méretezési szolgáltatása](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Most már méretezheti a szolgáltatás példányainak számát.

1. Módosítsa a számot **3**-ra, és kattintson a **Szolgáltatás méretezése** parancsra.

    A szolgáltatás parancssorral történő skálázása a következőképpen is végrehajtható.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Kattintson a **fabric:/SpringServiceFabric/SpringGettingStarted** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![A Service Fabric Explorer skálázási szolgáltatása kész](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    A szolgáltatás három példányban jelenik meg, a fanézetben pedig látható, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="fail-over-services-in-a-cluster"></a>Feladatátvételi szolgáltatások a fürtben

A szolgáltatás feladatátvételének bemutatásához a Service Fabric Explorerben szimuláljuk egy csomópont újraindítását. Győződjön meg arról, hogy a szolgáltatásnak kizárólag egy példánya fut.

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://localhost:19080`.
1. Kattintson a három pontra a szolgáltatás példányát futtató csomópont mellett, és indítsa újra a csomópontot.

    ![Csomópont újraindítása a Service Fabric Explorerben](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
1. A szolgáltatás példánya egy új csomópontra kerül át anélkül, hogy az alkalmazás leállna.

    ![A csomópont-újraindítás a Service Fabric Explorerben sikeres volt](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Spring Boot-alkalmazás telepítése Service Fabricre
* Az alkalmazás központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra
* Feladatátvétel elvégzése a szolgáltatáson a rendelkezésre állás korlátozása nélkül

Ha bővebb információra van szüksége a Java-alkalmazások Service Fabricben való használatával kapcsolatban, lépjen tovább a Java-alkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Java-alkalmazás üzembe helyezése](./service-fabric-tutorial-create-java-app.md)
