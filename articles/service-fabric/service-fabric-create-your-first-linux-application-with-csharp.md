<properties
   pageTitle="Az első Service Fabric-alkalmazás létrehozása Linux rendszeren C# használatával | Microsoft Azure"
   description="Service Fabric-alkalmazás létrehozása és telepítése C használatával#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>



# Az első Azure Service Fabric-alkalmazás létrehozása

> [AZURE.SELECTOR]
- [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

A Service Fabric SDK-kat biztosít Linux-szolgáltatások létrehozásához a .NET és a Java használatával egyaránt. A jelen oktatóanyagban áttekintjük, hogyan hozhat létre alkalmazásokat a Linux rendszerre, valamint szolgáltatásokat a C# (.NET Core) használatával.

## Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy [beállította a Linux-fejlesztőkörnyezetet](service-fabric-get-started-linux.md). Amennyiben a Mac OS X rendszert használja, [beállíthat egy beépített Linux-környezetet egy virtuális gépen a Vagrant használatával](service-fabric-get-started-mac.md).

## Az alkalmazás létrehozása

A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmazhat, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. A Linux Service Fabric SDK tartalmaz egy [Yeoman](http://yeoman.io/)-generátort, amely megkönnyíti az első szolgáltatás létrehozását, és a továbbiak hozzáadását a későbbiekben. Hozzunk létre egy egyetlen szolgáltatással rendelkező alkalmazást a Yeoman használatával.

1. Írja be a terminálba az alábbi parancsot a keret létrehozásához: `yo azuresfcsharp`

2. Adjon nevet az alkalmazásnak.

3. Válassza ki az első szolgáltatása típusát, majd nevezze el. A jelen oktatóanyag esetén egy Reliable aktorszolgáltatást választunk.

  ![C nyelvhez készült Service Fabric Yeoman-generátor#][sf-yeoman]

>[AZURE.NOTE] További tudnivalók a beállításokról: [Service Fabric programming model overview](service-fabric-choose-framework.md) (A Service Fabric programozási modell áttekintése).

## Az alkalmazás létrehozása

A Service Fabric Yeoman-sablonok tartalmaznak egy felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához (miután megnyitotta az alkalmazás mappáját).

  ```bash
 cd myapp 
 ./build.sh 
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

3. Kattintson az előző lépésben megtalált csomópontra, majd válassza a **Inaktiválás (újraindítás)** elemet a Műveletek menüből. Ezzel a művelettel újraindítja a helyi fürt öt csomópontjának egyikét, és feladatátvételt kényszerít ki egy másik csomóponton futó másodlagos replikára. A művelet végrehajtása közben figyelje meg a tesztügyfél kimenetét, amelyből láthatja, hogy a számláló a feladatátvétel ellenére továbbra is növekszik.


## Következő lépések

- [További tudnivalók a Reliable Actorsről](service-fabric-reliable-actors-introduction.md)
- [Service Fabric-fürtökkel folytatott interakció az Azure parancssori felületének használatával](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png



<!--HONumber=Oct16_HO3-->


