---
title: "Spring Boot-alkalmazás telepítése Azure Service Fabricre | Microsoft Docs"
description: "Spring Boot-alkalmazás telepítése Azure Service Fabricre a Spring Boot útmutatójának segítségével."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: d34862d96744e038d7c1890f703ead79c416ddfa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-a-spring-boot-application"></a>Spring Boot-alkalmazás telepítése
Az Azure Service Fabric egy elosztott rendszerplatform, amely mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál. 

Ez a rövid útmutató a Spring Boot-alkalmazás Service Fabricre történő telepítését mutatja be. A rövid útmutató a Spring webhelyének [első lépéseket ismertető](https://spring.io/guides/gs/spring-boot/) útmutatójában található mintát használja. Ez az ismerős parancssori eszközöket alkalmazó rövid útmutató végigvezet a Spring Boot-minta Service Fabric-alkalmazásként történő telepítésének lépésein. A folyamat végén a Spring Boot mintája működőképes állapotban lesz a Service Fabricen. 

![Képernyőkép az alkalmazásról](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)

Ezen rövid útmutató segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Spring Boot-alkalmazás telepítése Service Fabricre
> * Az alkalmazás központi telepítése a helyi fürtre 
> * Az alkalmazás központi telepítése egy fürtre az Azure-ban
> * Az alkalmazás horizontális felskálázása több csomópontra
> * Feladatátvétel elvégzése a szolgáltatáson a rendelkezésre állás korlátozása nélkül

## <a name="prerequisites"></a>Előfeltételek
A gyorsútmutató elvégzéséhez:
1. [A Service Fabric SDK és a Service Fabric parancssori felület (CLI) telepítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [A Git telepítése](https://git-scm.com/)
3. [A Yeoman telepítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [A Java-környezet beállítása](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>A minta letöltése
Egy parancssori ablakban futtassa a következő parancsot a Spring Boot mintaalkalmazásának helyi számítógépre történő klónozásához.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>A Spring Boot-alkalmazás becsomagolása 
1. A frissen klónozott `gs-spring-boot` könyvtárban futtassa a `yo azuresfguest` parancsot. 

2. Az egyes sorokban adja meg a következő adatokat. 

    ![Yeoman-bejegyzések](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. A `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` mappában hozzon létre egy `entryPoint.sh` nevű fájlt. Adja a következőket a fájlhoz. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

Mostanra más létrehozta Spring Boot mintájához tartozó Service Fabric-alkalmazást, amelyet a Service Fabricre telepíthet.

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása
1. A helyi fürt elindításához futtassa a következő parancsot:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    A helyi fürt elindítása eltarthat egy ideig. A fürt üzembe állásának ellenőrzéséhez nyissa meg a Service Fabric Explorert a **http://localhost:19080** címen. Ha öt kifogástalan csomópontot lát, a fürt megfelelően üzemel. 
    
    ![A helyi fürt kifogástalan](./media/service-fabric-quickstart-java/localclusterup.png)

2. Navigáljon a `gs-spring-boot/SpringServiceFabric` mappához.
3. A helyi fürthöz való csatlakozáshoz futtassa a következő parancsot. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Futtassa az `install.sh` szkriptet. 

    ```bash
    ./install.sh
    ```

5. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást a **http://localhost:8080** címen. 

    ![Helyi alkalmazás kezelőfelülete](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)
    
Most már elérhető a Service Fabric-fürtre telepített Spring Boot-alkalmazás.  

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás központi telepítése az Azure-ban

### <a name="set-up-your-azure-service-fabric-cluster"></a>Azure Service Fabric-fürt beállítása
Az alkalmazás Azure-fürtön történő üzembe helyezéséhez hozzon létre egy saját fürtöt.

A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök. Ezeket a Service Fabric csapata üzemelteti, és bárki üzembe helyezhet rajtuk alkalmazásokat, illetve megismerkedhet a platform használatával. A nyilvános fürt eléréséhez [kövesse az alábbi utasításokat](http://aka.ms/tryservicefabric). 

További információk saját fürtök létrehozásáról: [Service Fabric-fürt létrehozása az Azure-on](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> A Spring Boot szolgáltatás a konfigurációja szerint a 8080-as porton figyeli a bejövő forgalmat. Győződjön meg róla, hogy a port nyitva van a fürtön. Ha a nyilvános fürtöt használja, ez a port nyitva van.
>

### <a name="deploy-the-application-using-cli"></a>Az alkalmazás üzembe helyezése a parancssori felület használatával
Az alkalmazást és a fürtjét a létrehozása után közvetlenül a parancssori felületről telepítheti egy fürtre.

1. Navigáljon a `gs-spring-boot/SpringServiceFabric` mappához.
2. Az Azure-fürthöz való csatlakozáshoz futtassa a következő parancsot. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Ha a fürtöt önaláírt tanúsítvány védi, a következő parancsot kell futtatnia: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Futtassa az `install.sh` szkriptet. 

    ```bash
    ./install.sh
    ```

4. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást a **http://\<ConnectionIPOrUrl>:8080** címen. 

    ![Helyi alkalmazás kezelőfelülete](./media/service-fabric-quickstart-java-spring-boot/springsfazure.png)
    
Most már elérhető a Service Fabric-fürtre telepített Spring Boot-alkalmazás.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben
A szolgáltatások skálázhatók egy adott fürtben, hogy kövessék a szolgáltatások terhelésének változásait. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik. A szolgáltatásokat többféleképpen is méretezheti – használhat szkripteket, vagy a Service Fabric parancssori felület (sfctl) parancsait. Ebben a példában a Service Fabric Explorert használjuk.

A Service Fabric Explorer az összes Service Fabric-fürtben fut. Eléréséhez egy böngészőben navigáljon az adott fürt HTTP-kezelési portjára (19080), például: `http://demolinuxsecure.westus.cloudapp.azure.com:19080`.

A webes előtér-szolgáltatás méretezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://demolinuxsecure.westus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a fanézetben a **fabric:/SpringServiceFabric/SpringGettingStarted** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

    ![A Service Fabric Explorer méretezési szolgáltatása](./media/service-fabric-quickstart-java-spring-boot/springbootsfhowtoscale.png)

    Most már méretezheti a szolgáltatás példányainak számát.

3. Módosítsa a számot **5**-re, és kattintson a **Szolgáltatás méretezése** parancsra.

    A szolgáltatás parancssorral történő skálázása a következőképpen is végrehajtható.

    ```bash 
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 5 --stateless 
    ``` 

4. Kattintson a **fabric:/SpringServiceFabric/SpringGettingStarted** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![A Service Fabric Explorer skálázási szolgáltatása kész](./media/service-fabric-quickstart-java-spring-boot/springsfscaled.png)

    Ekkor láthatja, hogy a szolgáltatás öt példányban jelenik meg, a fanézetben pedig megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megnöveltük a Spring-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos megérteni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány kezdjen futni a fürtben.

## <a name="failover-services-in-a-cluster"></a>Feladatátvételi szolgáltatások a fürtben 
A szolgáltatás feladatátvételének bemutatásához a Service Fabric Explorerben szimulálhatjuk egy csomópont újraindítását. Győződjön meg arról, hogy a szolgáltatásnak kizárólag 1 példánya fut. 

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://demolinuxsecure.westus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a szolgáltatás példányát futtató csomópont mellett, és indítsa újra a csomópontot. 

    ![Csomópont újraindítása a Service Fabric Explorerben](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestart.png)
3. A szolgáltatás példánya egy új csomópontra kerül át anélkül, hogy az alkalmazás leállna. 

    ![A csomópont-újraindítás a Service Fabric Explorerben sikeres volt](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestartsucceed.png)

## <a name="next-steps"></a>Következő lépések
Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Spring Boot-alkalmazás telepítése Service Fabricre
> * Az alkalmazás központi telepítése a helyi fürtre 
> * Az alkalmazás központi telepítése egy fürtre az Azure-ban
> * Az alkalmazás horizontális felskálázása több csomópontra
> * Feladatátvétel elvégzése a szolgáltatáson a rendelkezésre állás korlátozása nélkül

* További információ [a Java-mikroszolgáltatások Service Fabric programozási modellekkel történő kiépítéséről](service-fabric-quickstart-java-reliable-services.md).
* További információk a [folyamatos integráció és üzembe helyezés beállításáról a Jenkins használatával](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* Egyéb [Java-minták](https://github.com/Azure-Samples/service-fabric-java-getting-started) megtekintése.