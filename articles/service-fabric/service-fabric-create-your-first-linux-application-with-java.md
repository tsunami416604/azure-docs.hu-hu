---
title: "Az első Azure-alapú mikroszolgáltatás-alkalmazás létrehozása Linux rendszeren Java használatával | Microsoft Docs"
description: "Service Fabric-alkalmazás létrehozása és telepítése Java használatával"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 88b16b0b7c951ab0350649de05c00263ec76e630
ms.lasthandoff: 03/11/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>Az első Azure Service Fabric-alkalmazás létrehozása
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

A Service Fabric SDK-kat biztosít Linux-szolgáltatások létrehozásához a .NET és a Java használatával egyaránt. A jelen oktatóanyagban létrehozunk egy alkalmazást a Linux rendszerre, valamint egy szolgáltatást a Java használatával.  

> [!NOTE]
> A Java, mint elsőrangú beépített programozási nyelv, kizárólag a Linux előzetes verziójában támogatott (a Windows-támogatás bevezetése tervbe van véve). Bármely, Java-alkalmazásokat tartalmazó alkalmazás futtatható azonban Windows vagy Linux rendszeren vendégalkalmazásként vagy tárolókon belül. További információk a [meglévő végrehajtható fájlok Azure Service Fabric felületén történő üzembe helyezésével](service-fabric-deploy-existing-app.md) és a [meglévő tárolók Service Fabric felületén történő üzembe helyezésével](service-fabric-deploy-container.md) foglalkozó témakörökben találhatók.
>

## <a name="video-tutorial"></a>Oktatóvideó

Az alábbi Microsoft Virtual Academy-videó végigvezeti egy Java-alkalmazás Linuxon történő létrehozásának folyamatán:  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy [beállította a Linux-fejlesztőkörnyezetet](service-fabric-get-started-linux.md). Amennyiben a Mac OS X rendszert használja, [beállíthat egy beépített Linux-környezetet egy virtuális gépen a Vagrant használatával](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Az alkalmazás létrehozása
A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmazhat, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. A Linux Service Fabric SDK tartalmaz egy [Yeoman](http://yeoman.io/)-generátort, amely megkönnyíti az első szolgáltatás létrehozását, és a továbbiak hozzáadását a későbbiekben. Hozzunk létre egy egyetlen szolgáltatással rendelkező alkalmazást a Yeoman használatával.

1. Írja be a terminálba a következőt: ``yo azuresfjava``.
2. Adjon nevet az alkalmazásnak.
3. Válassza ki az első szolgáltatása típusát, majd nevezze el. A jelen oktatóanyag esetén egy Reliable aktorszolgáltatást választunk.

   ![Javához készült Service Fabric Yeoman-generátor][sf-yeoman]

> [!NOTE]
> További tudnivalók a beállításokról: [Service Fabric programming model overview](service-fabric-choose-framework.md) (A Service Fabric programozási modell áttekintése).
>

## <a name="build-the-application"></a>Az alkalmazás létrehozása
A Service Fabric Yeoman-sablonok tartalmaznak egy [Gradle](https://gradle.org/) felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához.

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Az alkalmazást a létrehozása után az Azure parancssori felülettel telepítheti a helyi fürtben.

1. Csatlakozzon a helyi Service Fabric-fürthöz.

    ```bash
    azure servicefabric cluster connect
    ```

2. Használja a sablonban megadott telepítési szkriptet az alkalmazáscsomag a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

    ```bash
    ./install.sh
    ```

3. Nyisson meg egy böngészőt, és keresse fel a Service Fabric Explorert a következő címen: http://localhost:19080/Explorer (a Vagrant Mac OS X rendszeren való használata esetében a localhost helyett használja a virtuális gép magánhálózati IP-címét).

4. Bontsa ki az Alkalmazások csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazása típusához, és egy másik a típus első példányához.

## <a name="start-the-test-client-and-perform-a-failover"></a>Tesztügyfél elindítása és feladatátvétel végrehajtása
Az aktorprojektek önmagukban nem csinálnak semmit. Egy másik szolgáltatást vagy alkalmazást igényelnek, amely üzeneteket küld a számukra. Az aktorsablon egy egyszerű tesztszkriptet tartalmaz, amelyet az aktorszolgáltatással való kommunikációra használhat.

1. Futtassa a szkriptet a figyelési segédprogram használatával az aktorszolgáltatás kimenetének megtekintéséhez.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Keresse meg az aktorszolgáltatás elsődleges replikáját futtató csomópontot a Service Fabric Explorerben. Az alábbi képernyőképen ez a 3. csomópont.

    ![Az elsődleges replika megkeresése a Service Fabric Explorerben][sfx-primary]

3. Kattintson az előző lépésben megtalált csomópontra, majd válassza a **Inaktiválás (újraindítás)** elemet a Műveletek menüből. Ezzel a művelettel újraindítja a helyi fürt öt csomópontjának egyikét, és feladatátvételt kényszerít ki egy másik csomóponton futó másodlagos replikára. A művelet végrehajtása közben figyelje meg a tesztügyfél kimenetét, amelyből láthatja, hogy a számláló a feladatátvétel ellenére továbbra is növekszik.

## <a name="create-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Alkalmazás létrehozása és központi telepítése az Eclipse Neon beépülő modul használatával

A Service Fabric a Service Fabric Java-alkalmazások Eclipse-szel végzett létrehozásához, kiépítéséhez és üzembe helyezéséhez is lehetőséget nyújt. Az Eclipse telepítésekor válassza az **Eclipse IDE JAVA-fejlesztőknek** lehetőséget. A Service Fabric az Eclipse **Neon** beépülő modulját is támogatja. Tekintse meg a részletes dokumentációt: [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése a Service Fabric Eclipse beépülő moduljával Linux rendszeren](service-fabric-get-started-eclipse.md)

## <a name="adding-more-services-to-an-existing-application"></a>További szolgáltatások hozzáadása meglévő alkalmazáshoz

### <a name="using-command-line-utility"></a>Parancssori segédprogram használata
Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket:
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtassa a `yo azuresfjava:AddService` parancsot.

### <a name="using-service-fabric-eclipse-plugin-for-java-on-linux"></a>Service Fabric Eclipse beépülő modul használata Javához Linux rendszeren
Ha szolgáltatást szeretne adni a Service Fabric Eclipse beépülő moduljával létrehozott meglévő alkalmazáshoz, tekintse meg [ezt](service-fabric-get-started-eclipse.md#add-new-service-fabric-service-to-your-service-fabric-application) a dokumentációt.

## <a name="next-steps"></a>Következő lépések
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése a Service Fabric Eclipse beépülő moduljával Linux rendszeren](service-fabric-get-started-eclipse.md)
* [További tudnivalók a Reliable Actorsről](service-fabric-reliable-actors-introduction.md)
* [Service Fabric-fürtökkel folytatott interakció az Azure parancssori felületének használatával](service-fabric-azure-cli.md)
* [Üzembe helyezés hibaelhárítása](service-fabric-azure-cli.md#troubleshooting)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

