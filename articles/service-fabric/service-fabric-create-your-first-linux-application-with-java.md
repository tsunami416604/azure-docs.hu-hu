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
ms.date: 09/20/2017
ms.author: ryanwi
ms.openlocfilehash: c7625a5670aca5d105601432fedfd0d7a78bb53c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Az első Java Service Fabric Reliable Actors-alkalmazás létrehozása Linuxon
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Ezzel a rövid útmutatóval csupán néhány perc alatt létrehozhatja első Azure Service Fabric Java-alkalmazását egy Linux-fejlesztőkörnyezetben.  Az oktatóanyag végére egy egyszerű Java egyszolgáltatásos alkalmazás lesz elérhető a helyi fejlesztési fürtön.  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzáfogna, telepítse a Service Fabric SDK-t és a Service Fabric parancssori felületet, és állítson be egy fejlesztési fürtöt a saját [Linux-fejlesztőkörnyezetében](service-fabric-get-started-linux.md). Amennyiben a Mac OS X rendszert használja, [beállíthat egy Linux-fejlesztőkörnyezetet egy virtuális gépen a Vagrant használatával](service-fabric-get-started-mac.md).

Telepítse a [Service Fabric parancssori felületet](service-fabric-cli.md) is.

### <a name="install-and-set-up-the-generators-for-java"></a>A Java generátorainak telepítése és beállítása
A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyekkel Service Fabric Java-alkalmazásokat hozhat létre a terminálról a Yeoman sablongenerátor használatával. Az alábbi lépések végrehajtásával biztosíthatja, hogy a Service Fabric Yeoman sablongenerátor elérhető legyen a gépen lévő Java használatához.
1. A node.js és az NPM telepítése a gépre

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. A [Yeoman](http://yeoman.io/) sablongenerátor telepítése a gépre az NPM-ből

  ```bash
  sudo npm install -g yo
  ```
3. A Service Fabric Yeoman Java-alkalmazásgenerátor telepítése az NPM-ből

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Alapfogalmak
A Reliable Actors használatának elsajátításához csak néhány alapfogalommal kell tisztában lennie:

* **Aktorszolgáltatás**. A Reliable Actors aktorai Reliable Services-szolgáltatásokba vannak csomagolva, amelyek a Service Fabric-infrastruktúrában helyezhetők üzembe. Az aktorpéldányok elnevezett szolgáltatáspéldányokban aktiválhatók.
* **Aktorregisztráció**. Akárcsak a Reliable Services esetében, a Reliable Actor-szolgáltatásokat is regisztrálni kell a Service Fabric-futtatókörnyezetben. Emellett az aktor típusát regisztrálni kell az Actor-futtatókörnyezetben.
* **Aktorillesztő**. Az aktor illesztője határozza meg az aktor erős típusmegadású nyilvános illesztőjét. A Reliable Actor modellterminológiában az aktor illesztője határozza meg azt, hogy az aktor milyen típusú üzeneteket képes értelmezni és feldolgozni. Az aktor illesztőjét használják a további aktorok és ügyfélalkalmazások, hogy üzeneteket „küldjenek” (aszinkron módon) az aktor felé. A Reliable Actors több illesztőt is képes implementálni.
* **ActorProxy-osztály**. Az ActorProxy-osztály használatával hívják meg az ügyfélalkalmazások az aktor illesztőjén keresztül közzétett metódusokat. Az ActorProxy-osztály két fontos funkciót lát el:
  
  * Névfeloldás: Képes megállapítani az aktor helyét a fürtben (megtalálja a fürt azon csomópontját, ahol az aktor található).
  * Hibakezelés: Képes újra megpróbálkozni a metódusok meghívásával, majd újra feloldani az aktor helyét például olyan esetben, ha egy hiba miatt az aktort át kellett helyezni a fürt egy másik csomópontjára.

Az aktorok illesztőivel kapcsolatban a következő szabályokat érdemes megemlíteni:

* Az aktorok illesztőmetódusait nem lehet túlterhelni.
* Az aktorok illesztőmetódusainak nem lehetnek „out”, „ref” vagy opcionális paraméterei.
* Az általános illesztők nem támogatottak.

## <a name="create-the-application"></a>Az alkalmazás létrehozása
A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmaz, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. Az utolsó szakaszban telepített generátor megkönnyíti az első szolgáltatás létrehozását, és a továbbiak hozzáadását a későbbiekben.  Service Fabric Java-alkalmazásokat Eclipse beépülő modul használatával is létrehozhat, kiépíthet és telepíthet. Lásd: [Az első Java-alkalmazás létrehozása és telepítése az Eclipse használatával](service-fabric-get-started-eclipse.md). Ennek az útmutatónak a céljaira a Yeoman használatával hoz létre egy egyetlen szolgáltatást kezelő alkalmazást, amely egy számláló értékét olvassa be és tárolja.

1. Írja be a terminálba a következőt: ``yo azuresfjava``.
2. Adjon nevet az alkalmazásnak.
3. Válassza ki az első szolgáltatása típusát, majd nevezze el. A jelen oktatóanyag céljaira válasszon egy Reliable Actor-szolgáltatást. A többi szolgáltatástípusról az [A Service Fabric programozási modell áttekintése](service-fabric-choose-framework.md) című fejezet nyújt bővebb tájékoztatást.
   ![Javához készült Service Fabric Yeoman-generátor][sf-yeoman]

Ha az alkalmazásnak a „HelloWorldActorApplication”, az aktornak pedig a „HelloWorldActor” nevet adja, akkor a következő szerkezet jön létre:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>A Reliable Actors alapszintű építőelemei
A korábban ismertetett alapfogalmak a Reliable Actor-szolgáltatások alapszintű építőelemeire is vonatkoznak.

### <a name="actor-interface"></a>Aktorillesztő
Az aktor illesztőjének definícióját tartalmazza. Ez az illesztő határozza meg azt az aktormegállapodást, amelyen az aktor implementálása és az aktort meghívó ügyfelek osztoznak. Érdemes az illesztőt egy olyan helyen definiálni, amely elkülönül az aktor implementálásától, és több egyéb szolgáltatás vagy ügyfélalkalmazás is osztozhat rajta.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Aktorszolgáltatás
Az aktor implementálását és az aktor regisztrációs kódját tartalmazza. Az aktor osztálya határozza meg az aktor illesztőjét. Itt végzi el az aktor a feladatát.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Aktorregisztráció
Az aktorszolgáltatást regisztrálni kell egy bizonyos szolgáltatástípusként a Service Fabric-futtatókörnyezetben. Ahhoz, hogy az aktorszolgáltatás futtatni tudja az aktorpéldányokat, az aktor típusát is regisztrálni kell az aktorszolgáltatásba. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Az alkalmazás létrehozása
A Service Fabric Yeoman-sablonok tartalmaznak egy [Gradle](https://gradle.org/) felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához.
A Service Fabric Java-függőségeit a Mavenből kéri le a rendszer. A Service Fabric Java-alkalmazások létrehozásához és szerkesztéséhez mindenképp telepítenie kell a JDK-t és a Gradle-t. Ha a JDK (openjdk-8-jdk) és a Gradle még nincs telepítve, a következő kód futtatásával telepítheti őket –

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Az alkalmazás felépítéséhez és becsomagolásához futtassa a következő parancsot:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Az alkalmazást a létrehozása után telepítheti a helyi fürtben.

1. Csatlakozzon a helyi Service Fabric-fürthöz.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Futtassa a sablonban megadott telepítési szkriptet az alkalmazáscsomagnak a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

    ```bash
    ./install.sh
    ```

A kész alkalmazás a többi Service Fabric-alkalmazással azonos módon telepíthető. Részletesebb útmutatást talál a [Service Fabric-alkalmazás kezelése a Service Fabric parancssori felülettel](service-fabric-application-lifecycle-sfctl.md) című dokumentációban.

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

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric Java-kódtárak a Mavenben
A Service Fabric Java-kódtárak a Mavenben üzemelnek. A függőségeket a projektek ``pom.xml`` vagy ``build.gradle`` fájljában adhatja hozzá a **mavenCentral** Service Fabric Java-kódtárainak használatához. 

### <a name="actors"></a>Aktorok

A Service Fabric Reliable Actor támogatása az alkalmazáshoz.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Szolgáltatások

A Service Fabric Reliable Services támogatása az alkalmazáshoz.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Egyéb
#### <a name="transport"></a>Átvitel

Az átviteli réteg támogatása a Service Fabric Java-alkalmazáshoz. Ezt a függőséget nem kell kifejezetten hozzáadnia a Reliable Actor- vagy Service-alkalmazásaihoz, hacsak a programozást nem az átviteli réteg szintjén végzi.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-támogatás

A natív Service Fabric-futtatókörnyezettel kommunikáló Service Fabric rendszerszintű támogatása. Ezt a függőséget nem kell kifejezetten hozzáadnia a Reliable Actor- vagy Service-alkalmazásaihoz. A rendszer automatikusan lekéri azt a Mavenből a fent említett további függőségek hozzáadásakor.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>A Mavennel használni kívánt régi Service Fabric Java-alkalmazások migrálása
Nemrégiben áthelyeztük a Service Fabric Java-kódtárakat a Service Fabric Java SDK-ból a Mavenen futó adattárba. A Yeomannel vagy az Eclipse-szel létrehozott új alkalmazások a legfrissebb projekteket hozzák létre (amelyek képesek együttműködni a Mavennel), a meglévő állapotmentes vagy aktor Service Fabric Java-alkalmazások pedig, amelyek korábban a Service Fabric Java SDK-t használták, frissíthetők a Mavenben található Service Fabric Java-függőségek használatára. Kövesse az [itt](service-fabric-migrate-old-javaapp-to-use-maven.md) felsorolt lépéseket, ha biztosítani kívánja, hogy a régebbi alkalmazásaik együttműködjenek a Mavennel.

## <a name="next-steps"></a>Következő lépések

* [Az első Service Fabric Java-alkalmazás létrehozása Linuxra Eclipse használatával](service-fabric-get-started-eclipse.md)
* [További tudnivalók a Reliable Actorsről](service-fabric-reliable-actors-introduction.md)
* [Service Fabric-fürtök használata a Service Fabric parancssori felületén](service-fabric-cli.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
