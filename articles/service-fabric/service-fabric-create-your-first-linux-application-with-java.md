---
title: "Azure Service Fabric Reliable Actors Java-alkalmazás létrehozása Linux rendszeren | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre és helyezhet üzembe egy Java Service Fabric Reliable Actors-alkalmazást öt perc alatt."
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 254f38a600ea4026120bc411368eeb01310e56b2
ms.contentlocale: hu-hu
ms.lasthandoff: 07/15/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Az első Java Service Fabric Reliable Actors-alkalmazás létrehozása Linuxon

A rövid útmutató segítségével létrehozhatja első Azure Service Fabric Java-alkalmazását egy Linux-fejlesztőkörnyezetben csupán néhány perc alatt.  Az oktatóanyag végére egy egyszerű Java egyszolgáltatásos alkalmazás lesz elérhető a helyi fejlesztési fürtön.  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzáfogna, telepítse a Service Fabric SDK-t és az Azure CLI-t, és állítson be egy fejlesztési fürtöt a saját [Linux fejlesztőkörnyezetében](service-fabric-get-started-linux.md). Amennyiben a Mac OS X rendszert használja, [beállíthat egy Linux-fejlesztőkörnyezetet egy virtuális gépen a Vagrant használatával](service-fabric-get-started-mac.md).

Az alkalmazás üzembe helyezéséhez érdemes konfigurálni az [Azure CLI 2.0](service-fabric-azure-cli-2-0.md)-t (ajánlott) vagy az [XPlat CLI](service-fabric-azure-cli.md)-t.

## <a name="create-the-application"></a>Az alkalmazás létrehozása
A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmaz, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. A Linux Service Fabric SDK tartalmaz egy [Yeoman](http://yeoman.io/)-generátort, amely megkönnyíti az első szolgáltatás létrehozását, és a továbbiak hozzáadását a későbbiekben.  Service Fabric Java-alkalmazásokat Eclipse beépülő modul használatával is létrehozhat, kiépíthet és telepíthet. Lásd: [Az első Java-alkalmazás létrehozása és telepítése az Eclipse használatával](service-fabric-get-started-eclipse.md). Ennek az útmutatónak a céljaira a Yeoman használatával hoz létre egy egyetlen szolgáltatást kezelő alkalmazást, amely egy számláló értékét olvassa be és tárolja.

1. Írja be a terminálba a következőt: ``yo azuresfjava``.
2. Adjon nevet az alkalmazásnak. 
3. Válassza ki az első szolgáltatása típusát, majd nevezze el. A jelen oktatóanyag céljaira válasszon egy Reliable Actor-szolgáltatást. A többi szolgáltatástípusról az [A Service Fabric programozási modell áttekintése](service-fabric-choose-framework.md) című fejezet nyújt bővebb tájékoztatást.
   ![Javához készült Service Fabric Yeoman-generátor][sf-yeoman]

## <a name="build-the-application"></a>Az alkalmazás létrehozása
A Service Fabric Yeoman-sablonok tartalmaznak egy [Gradle](https://gradle.org/) felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához. Az alkalmazás felépítéséhez és becsomagolásához futtassa a következő parancsot:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Az alkalmazást a létrehozása után telepítheti a helyi fürtben.

### <a name="using-xplat-cli"></a>Az XPlat CLI használatával

1. Csatlakozzon a helyi Service Fabric-fürthöz.

    ```bash
    azure servicefabric cluster connect
    ```

2. Futtassa a sablonban megadott telepítési szkriptet az alkalmazáscsomagnak a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Az Azure CLI 2.0 használatával

A kész alkalmazás a többi Service Fabric-alkalmazással azonos módon telepíthető. Részletesebb útmutatást talál a [Service Fabric-alkalmazás kezelése az Azure CLI-vel](service-fabric-application-lifecycle-azure-cli-2-0.md) című dokumentációban.

Ezen parancsok paraméterezése megtalálható az alkalmazáscsomagon belül, a generált jegyzékfájlokban.

Az alkalmazás telepítése után nyisson meg egy böngészőt, és keresse fel a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)-t a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) URL-címen.
Bontsa ki az **Alkalmazások** csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazás típusához, és egy másik a típus első példányához.

## <a name="start-the-test-client-and-perform-a-failover"></a>Tesztügyfél elindítása és feladatátvétel végrehajtása
Egy aktor semmit sem tesz önmagában. Egy másik szolgáltatást vagy alkalmazást igényel, amely üzeneteket küld a számára. Az aktorsablon egy egyszerű tesztszkriptet tartalmaz, amelyet az aktorszolgáltatással való kommunikációra használhat.

1. Futtassa a szkriptet a figyelési segédprogram használatával az aktorszolgáltatás kimenetének megtekintéséhez.  A teszt-szkript a(z) `setCountAsync()` metódust hívja meg az aktorhoz a számláló léptetéséhez és a(z) `getCountAsync()` metódust a számláló új értékének beolvasásához, majd megjeleníti ezt az értéket a konzolon.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Keresse meg az aktorszolgáltatás elsődleges replikáját futtató csomópontot a Service Fabric Explorerben. Az alábbi képernyőképen ez a 3. csomópont. A szolgáltatás elsődleges replikája kezeli az olvasási és írási műveleteket.  A szolgáltatás állapotváltozásai replikálódnak a lenti képernyőképen a 0 és 1 csomóponton futó másodlagos replikákon.

    ![Az elsődleges replika megkeresése a Service Fabric Explorerben][sfx-primary]

3. A **Csomópontok** alatt kattintson az előző lépésben megtalált csomópontra, majd válassza a **Inaktiválás (újraindítás)** elemet a Műveletek menüből. Ezzel a művelettel újraindítja a szolgáltatás elsődleges replikáját futtató csomópontot, és feladatátvételt kényszerít ki egy másik csomóponton futó másodlagos replikára.  Ez a másodlagos replika előlép elsődlegessé, egy másik csomóponton pedig létrejön egy újabb másodlagos replika, az elsődleges replika pedig megkezdi az olvasási/írási műveleteket. Amíg a csomópont újraindul, figyelje meg a tesztügyfél kimenetét, amelyből láthatja, hogy a számláló a feladatátvétel ellenére továbbra is növekszik.

## <a name="remove-the-application"></a>Alkalmazás eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldány törléséhez, az alkalmazáscsomag regisztrációjának megszüntetéséhez, valamint az alkalmazáscsomag a fürt rendszerképtárolójából történő eltávolításához.

```bash
./uninstall.sh
```

A Service Fabric Explorerben látni fogja, hogy az alkalmazás és az alkalmazástípus már nem jelenik meg az **Alkalmazások** csomópont alatt.

## <a name="next-steps"></a>Következő lépések
* [Az első Service Fabric Java-alkalmazás létrehozása Linuxra Eclipse használatával](service-fabric-get-started-eclipse.md)
* [További tudnivalók a Reliable Actorsről](service-fabric-reliable-actors-introduction.md)
* [Service Fabric-fürtökkel folytatott interakció az Azure parancssori felületének használatával](service-fabric-azure-cli.md)
* [Üzembe helyezés hibaelhárítása](service-fabric-azure-cli.md#troubleshooting)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

## <a name="related-articles"></a>Kapcsolódó cikkek

* [A Service Fabric első lépései az Azure CLI 2.0 használatával](service-fabric-azure-cli-2-0.md)
* [Első lépések a Service Fabric XPlat CLI használatával](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

