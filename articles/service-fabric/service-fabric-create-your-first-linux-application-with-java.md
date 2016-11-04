---
title: Az első Service Fabric-alkalmazás létrehozása Linux rendszeren Java használatával | Microsoft Docs
description: Service Fabric-alkalmazás létrehozása és telepítése Java használatával
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: seanmck

---
# Az első Azure Service Fabric-alkalmazás létrehozása
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

A Service Fabric SDK-kat biztosít Linux-szolgáltatások létrehozásához a .NET és a Java használatával egyaránt. A jelen oktatóanyagban áttekintjük, hogyan hozhat létre alkalmazásokat a Linux rendszerre, valamint szolgáltatásokat a Java használatával.

## Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy [beállította a Linux-fejlesztőkörnyezetet](service-fabric-get-started-linux.md). Amennyiben a Mac OS X rendszert használja, [beállíthat egy beépített Linux-környezetet egy virtuális gépen a Vagrant használatával](service-fabric-get-started-mac.md).

## Az alkalmazás létrehozása
A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmazhat, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. A Linux Service Fabric SDK tartalmaz egy [Yeoman](http://yeoman.io/)-generátort, amely megkönnyíti az első szolgáltatás létrehozását, és a továbbiak hozzáadását a későbbiekben. Hozzunk létre egy egyetlen szolgáltatással rendelkező új alkalmazást a Yeoman használatával.

1. Írja be a terminálba a következőt: **yo azuresfjava**.
2. Adjon nevet az alkalmazásnak.
3. Válassza ki az első szolgáltatása típusát, majd nevezze el. A jelen oktatóanyag esetén egy Reliable aktorszolgáltatást fogunk választani.
   
   ![Javához készült Service Fabric Yeoman-generátor][sf-yeoman]

> [!NOTE]
> További tudnivalók a beállításokról: [Service Fabric programming model overview](service-fabric-choose-framework.md) (A Service Fabric programozási modell áttekintése).
> 
> 

## Az alkalmazás létrehozása
A Service Fabric Yeoman-sablonok tartalmaznak egy [Gradle](https://gradle.org/) felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához.

  ```bash
  cd myapp
  gradle
  ```

## Az alkalmazás központi telepítése
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

## Tesztügyfél elindítása és feladatátvétel végrehajtása
Az aktorprojektek önmagukban nem csinálnak semmit. Egy másik szolgáltatást vagy alkalmazást igényelnek, amely üzeneteket küld a számukra. Az aktorsablon egy egyszerű tesztszkriptet tartalmaz, amelyet az aktorszolgáltatással való kommunikációra használhat.

1. Futtassa a szkriptet a figyelési segédprogram használatával az aktorszolgáltatás kimenetének megtekintéséhez.
   
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Keresse meg az aktorszolgáltatás elsődleges replikáját futtató csomópontot a Service Fabric Explorerben. Az alábbi képernyőképen ez a 3. csomópont.
   
    ![Az elsődleges replika megkeresése a Service Fabric Explorerben][sfx-primary]
3. Kattintson az előző lépésben megtalált csomópontra, majd válassza a **Inaktiválás (újraindítás)** elemet a Műveletek menüből. Ezzel újraindítja a helyi fürt öt csomópontjának egyikét, és feladatátvételt kényszerít ki egy másik csomóponton futó másodlagos replikára. Eközben figyelje meg a tesztügyfél kimenetét, amelyből láthatja, hogy a számláló a feladatátvétel ellenére továbbra is növekszik.

## Alkalmazás létrehozása és központi telepítése az Eclipse Neon beépülő modul használatával
Amennyiben telepítette az Eclipse Neon szolgáltatási beépülő modult, a segítségével létrehozhat, felépíthet és telepíthet Java használatával készült Service Fabric-alkalmazásokat.  Az Eclipse telepítésekor válassza az **Eclipse IDE JAVA-fejlesztőknek** lehetőséget.

### Az alkalmazás létrehozása
A Service Fabric beépülő modul az Eclipse bővítményeként érhető el.

1. Az Eclipse-ben válassza a **File > Other > Service Fabric** (Fájl > Egyéb > Service Fabric) elemet. Több beállítás jelenik meg, köztük az Actors (Aktorok) és a Containers (Tárolók).
   
    ![Service Fabric-sablonok az Eclipse-ben][sf-eclipse-templates]
2. Ebben az esetben válassza a Stateless Service (Állapotmentes szolgáltatás) elemet.
3. Meg kell erősítenie a Service Fabric-perspektíva használatát, amely a Service Fabric-projektekkel való használatra optimalizálja az Eclipse-t. Válassza a Yes (Igen) lehetőséget.

### Az alkalmazás központi telepítése
A Service Fabric-sablonok tartalmaznak egy Gradle-feladatkészletet az alkalmazások létrehozásához és telepítéséhez, amelyeket az Eclipse-en keresztül aktiválhat.

1. Válassza a **Run > Run Configurations** (Futtatás > Konfigurációk futtatása) elemet.
2. Bontsa ki a **Gradle Project** (Gradle-projekt) elemet, és válassza a **ServiceFabricDeployer** elemet.
3. Kattintson a **Run** (Futtatás) parancsra.

Néhány másodpercen belül megtörténik az alkalmazás létrehozása és telepítése. Az állapotát megfigyelheti a Service Fabric Explorerből.

## Következő lépések
* [További tudnivalók a Reliable Actorsről](service-fabric-reliable-actors-introduction.md)
* [Service Fabric-fürtök kezelése az Azure parancssori felület használatával](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Oct16_HO3-->


