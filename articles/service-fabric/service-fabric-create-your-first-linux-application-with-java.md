---
title: "Az első Service Fabric-alkalmazás létrehozása Linux rendszeren Java használatával | Microsoft Docs"
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
ms.date: 10/04/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2cf98a0ef478a058c03122d3e027ef37e2404a09
ms.openlocfilehash: 8a7b100a531ea1dd5420451064fdfb1eb3f21782


---
# <a name="create-your-first-azure-service-fabric-application"></a>Az első Azure Service Fabric-alkalmazás létrehozása
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

A Service Fabric SDK-kat biztosít Linux-szolgáltatások létrehozásához a .NET és a Java használatával egyaránt. A jelen oktatóanyagban létrehozunk egy alkalmazást a Linux rendszerre, valamint egy szolgáltatást a Java használatával.  Az alábbi Microsoft Virtual Academy-videó végigvezeti egy Java-alkalmazás Linuxon történő létrehozásának folyamatán:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

> [!NOTE]
> A Java, mint elsőrangú beépített programozási nyelv, kizárólag a Linux előzetes verziójában támogatott (a Windows-támogatás bevezetése tervbe van véve). Bármely, Java-alkalmazásokat tartalmazó alkalmazás futtatható azonban Windows vagy Linux rendszeren vendégalkalmazásként vagy tárolókon belül. További információk a [meglévő végrehajtható fájlok Azure Service Fabric felületén történő üzembe helyezésével](service-fabric-deploy-existing-app.md) és a [meglévő tárolók Service Fabric felületén történő üzembe helyezésével](service-fabric-deploy-container.md) foglalkozó témakörökben találhatók.
> 


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

## <a name="build-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Alkalmazás létrehozása és központi telepítése az Eclipse Neon beépülő modul használatával

Amennyiben telepítette az Eclipse Neon [Service Fabric beépülő modulját](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#install-the-java-sdk-and-eclipse-neon-plugin-optional), a segítségével létrehozhat, felépíthet és telepíthet Java használatával készült Service Fabric-alkalmazásokat.  Az Eclipse telepítésekor válassza az **Eclipse IDE JAVA-fejlesztőknek** lehetőséget.

### <a name="create-the-application"></a>Az alkalmazás létrehozása

A Service Fabric beépülő modul az Eclipse bővítményeként érhető el.

1. Az Eclipse-ben válassza a **File > Other > Service Fabric** (Fájl > Egyéb > Service Fabric) elemet. Több beállítás jelenik meg, köztük az Actors (Aktorok) és a Containers (Tárolók).
   
    ![Service Fabric-sablonok az Eclipse-ben][sf-eclipse-templates]

2. Ebben az esetben válassza a Stateless Service (Állapotmentes szolgáltatás) elemet.

3. Meg kell erősítenie a Service Fabric-perspektíva használatát, amely a Service Fabric-projektekkel való használatra optimalizálja az Eclipse-t. Válassza a Yes (Igen) lehetőséget.

### <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
A Service Fabric-sablonok tartalmaznak egy Gradle-feladatkészletet az alkalmazások létrehozásához és telepítéséhez, amelyeket az Eclipse-en keresztül aktiválhat. 

1. Válassza a **Run > Run Configurations** (Futtatás > Konfigurációk futtatása) elemet.
2. Adja a **local** (helyi) vagy **cloud** (felhő) beállítást. Az alapértelmezett beállítás a **local** (helyi). Távoli fürtre történő telepítéshez válassza a **cloud** (felhő) beállítást.
3. Töltse be a megfelelő információkat a közzétételi profilokba a `local.json` vagy a `cloud.json` megfelelő módon történő szerkesztésével.
4. Kattintson a **Run** (Futtatás) parancsra.

Néhány másodpercen belül megtörténik az alkalmazás létrehozása és telepítése. Az állapotát megfigyelheti a Service Fabric Explorerből.

## <a name="adding-more-services-to-an-existing-application"></a>További szolgáltatások hozzáadása meglévő alkalmazáshoz

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket: 
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtassa a `yo azuresfjava:AddService` parancsot.


## <a name="next-steps"></a>Következő lépések
* [További tudnivalók a Reliable Actorsről](service-fabric-reliable-actors-introduction.md)
* [Service Fabric-fürtökkel folytatott interakció az Azure parancssori felületének használatával](service-fabric-azure-cli.md)
* [Üzembe helyezés hibaelhárítása](service-fabric-azure-cli.md#troubleshooting)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Jan17_HO1-->


