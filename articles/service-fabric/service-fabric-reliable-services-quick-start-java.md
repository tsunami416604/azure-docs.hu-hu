---
title: Az első megbízható szolgáltatás létrehozása Java-ban
description: Bevezetés a Microsoft Azure Service Fabric-alkalmazások állapotnélküli és állapotalapú szolgáltatásokkal történő létrehozásához.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: c3b301a7a9039f1fe8095950f0a5a4e23eb52a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614213"
---
# <a name="get-started-with-reliable-services-in-java"></a>Ismerkedés a Megbízható szolgáltatásokkal Java-ban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
>
>

Ez a cikk ismerteti az Azure Service Fabric megbízható szolgáltatások alapjait, és végigvezeti egy Java-ban írt egyszerű megbízható szolgáltatásalkalmazás létrehozásán és üzembe helyezésén. 

## <a name="installation-and-setup"></a>Telepítés és beállítás
Mielőtt elkezdené, győződjön meg arról, hogy a Service Fabric-fejlesztői környezet beállítása a számítógépen.
Ha be kell állítania, folytassa [a Mac-en](service-fabric-get-started-mac.md) való kezdéshez vagy [a Linuxon való kezdéshez](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Alapfogalmak
A Megbízható szolgáltatások első lépéseihez csak néhány alapvető fogalmat kell megértenie:

* **Szolgáltatás típusa**: Ez a szolgáltatás megvalósítása. Ez határozza meg az osztály írsz, hogy `StatelessService` kiterjeszti, és minden más kódot vagy függőséget használnak ott, valamint egy nevet és egy verziószámot.
* **Elnevezett szolgáltatáspéldány:** A szolgáltatás futtatásához hozzon létre a szolgáltatástípus elnevezett példányait, ugyanúgy, mint egy osztálytípusú objektumpéldányokat. A szolgáltatáspéldányok valójában az Ön által írt szolgáltatásosztály objektumpéldányai.
* **Szolgáltatásgazda**: A létrehozott szolgáltatáspéldányok létre kell futtatni egy gazdagépen belül. A szolgáltatásgazda csak egy folyamat, ahol a szolgáltatás példányai futtathatók.
* **Szolgáltatás regisztráció**: Regisztráció hozza össze mindent. A szolgáltatás típusát regisztrálni kell a Service Fabric futásidejű egy szolgáltatásgazdagépen, hogy a Service Fabric hozzon létre példányokat.  

## <a name="create-a-stateless-service"></a>Állapotmentes szolgáltatás létrehozása
Első ként hozzon létre egy Service Fabric-alkalmazást. A Service Fabric SDK Linux hoz egy Yeoman-generátort, amely biztosítja a Service Fabric-alkalmazás állapotnélküli szolgáltatásállványozását. Kezdje a következő Yeoman parancs futtatásával:

```bash
$ yo azuresfjava
```

A **megbízható állapotmentes szolgáltatás**létrehozásához kövesse az utasításokat. Ebben az oktatóanyagban nevezze el a "HelloWorldApplication" alkalmazást és a "HelloWorld" szolgáltatást. Az eredmény magában foglalja `HelloWorldApplication` `HelloWorld`a könyvtárakat a és .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Szolgáltatás regisztrációja
A szolgáltatástípusokat regisztrálni kell a Service Fabric futásidejében. A szolgáltatás típusa a `ServiceManifest.xml` implementálja `StatelessService`a és a szolgáltatásosztályban van definiálva. A szolgáltatás regisztrációja a folyamat fő belépési pontján történik. Ebben a példában a folyamat `HelloWorldServiceHost.java`fő belépési pontja:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>A szolgáltatás megvalósítása

Nyissa meg **a HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java alkalmazást.** Ez az osztály határozza meg a szolgáltatás típusát, és bármilyen kódot futtathat. A szolgáltatás API két belépési pontot biztosít a kódhoz:

* Egy nyílt végű belépési `runAsync()`pont módszer, az úgynevezett, ahol megkezdheti a számítási feladatok, beleértve a hosszú ideig futó számítási számítási feladatok.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Egy kommunikációs belépési pont, ahol csatlakoztathatja a választott kommunikációs vermét. Itt kezdheti meg a felhasználóktól és más szolgáltatásoktól érkező kérések fogadását.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ez az oktatóanyag `runAsync()` a belépési pont módszerére összpontosít. Ez az a hely, ahol azonnal elkezdheti futtatni a kódot.

### <a name="runasync"></a>RunAsync
A platform meghívja ezt a metódust, ha egy szolgáltatás egy példánya van elhelyezve, és készen áll a végrehajtásra. Állapotnélküli szolgáltatás esetén ez azt jelenti, hogy a szolgáltatáspéldány megnyitásakor. A lemondási jogkivonat ot biztosítunk a szolgáltatáspéldány lezárásának koordinálására. A Service Fabric, a szolgáltatáspéldány nyitott/záró ciklusa a szolgáltatás teljes élettartama alatt többször is előfordulhat. Ennek különböző okai lehetnek, többek között:

* A rendszer áthelyezi a szolgáltatáspéldányokat az erőforrás-kiegyenlítéshez.
* Hibák lépnek fel a kódban.
* Az alkalmazás vagy a rendszer frissül.
* Az alapul szolgáló hardver kimaradást tapasztal.

Ezt a vezénylési a Service Fabric által kezeli a szolgáltatás magas rendelkezésre állású és megfelelően kiegyensúlyozott.

`runAsync()`nem blokkolhatja szinkron módon. A runAsync megvalósításának egy CompletableFuture-t kell visszaadnia, hogy a futásidejű folytatódjon. Ha a számítási feladatoknak egy hosszú ideig futó feladatot kell végrehajtania, amelyet a CompletableFuture-en belül kell elvégezni.

#### <a name="cancellation"></a>Lemondás
A számítási feladatok törlése a megadott lemondási jogkivonat által koordinált együttműködési erőfeszítés. A rendszer megvárja, amíg a feladat befejeződik (sikeres befejezéssel, megszakítással vagy hibával), mielőtt továbblépne. Fontos, hogy tartsa tiszteletben a lemondási `runAsync()` jogkivonatot, fejezze be a munkát, és lépjen ki a lehető leggyorsabban, amikor a rendszer törlést kér. A következő példa bemutatja, hogyan kell kezelni egy lemondási eseményt:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

Ebben az állapotmentes szolgáltatás példában a számláló egy helyi változóban tárolódik. De mivel ez egy állapotmentes szolgáltatás, a tárolt érték csak a szolgáltatáspéldány aktuális életciklusára van. Amikor a szolgáltatás áthelyezi vagy újraindul, az érték elvész.

## <a name="create-a-stateful-service"></a>Állapotalapú szolgáltatás létrehozása
A Service Fabric egy új típusú szolgáltatást vezet be, amely állapotalapú. Az állapotalapú szolgáltatás megbízhatóan karbantarthatja az állapotot a szolgáltatáson belül, a használt kóddal együtt elhelyezve. Állapot magas rendelkezésre állású service Fabric anélkül, hogy egy külső tároló állapotának megőrzése nélkül.

Ha egy számlálóértékét állapot nélküliről magas rendelkezésre állásúra és állandóra szeretné konvertálni, még akkor is, ha a szolgáltatás áthelyezi vagy újraindul, állapotalapú szolgáltatásra van szükség.

Ugyanabban a könyvtárban, mint a HelloWorld alkalmazás, `yo azuresfjava:AddService` a parancs futtatásával új szolgáltatást adhat hozzá. Válassza ki a "Megbízható állapotalapú szolgáltatás" a keretrendszer és a szolgáltatás neve "HelloWorldStateful". 

Az alkalmazás most már két szolgáltatás: az állapotmentes szolgáltatás HelloWorld és az állapotalapú szolgáltatás HelloWorldStateful.

Az állapotalapú szolgáltatás ugyanazokkal a belépési pontokkal rendelkezik, mint egy állapotmentes szolgáltatás. A fő különbség az állapotmegbízhatóan tárolható állapotszolgáltató elérhetősége. A Service Fabric egy megbízható gyűjtemények nevű állapotszolgáltató-implementációval érkezik, amely lehetővé teszi replikált adatstruktúrák létrehozását a Reliable State Manager-en keresztül. Az állapotalapú megbízható szolgáltatás alapértelmezés szerint ezt az állapotszolgáltatót használja.

Nyissa meg a HelloWorldStateful.java-t a **HelloWorldStateful -> src-ben,** amely a következő RunAsync módszert tartalmazza:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()`hasonlóan működik az államos és állapotmentes szolgáltatásokban. Azonban egy állapotalapú szolgáltatás, a platform végez további munkát az `RunAsync()`Ön nevében, mielőtt végrehajtja . Ez a munka magában foglalhatja annak biztosítását, hogy a megbízható állapotkezelő és a megbízható gyűjtemények készen állnak a használatra.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Megbízható gyűjtemények és a megbízható állapotkezelő
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[A ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) egy szótárimplementáció, amely segítségével megbízhatóan tárolhatja az állapotot a szolgáltatásban. A Service Fabric és a Reliable HashMaps segítségével az adatokat közvetlenül a szolgáltatásban tárolhatja anélkül, hogy külső állandó tárolóra lenne szükség. Megbízható HashMaps, hogy az adatok magas rendelkezésre állású. A Service Fabric ezt úgy valósítja meg, hogy a szolgáltatás több *replikáját* hozza létre és kezeli. Emellett egy API-t is biztosít, amely absztrakt el a replikák kezelésének összetettségét és állapotátmeneteit.

Megbízható gyűjtemények tárolhatja bármilyen Java típusú, beleértve az egyéni típusok, egy pár kikötések:

* A Service Fabric magas rendelkezésre állásúvá teszi az *állapotot* a csomópontok közötti replikálással, és a Reliable HashMap tárolja az adatokat a helyi lemezre minden replika. Ez azt jelenti, hogy minden, ami tárolt Megbízható HashMaps kell *szerializálható*. 
* Objektumok replikálása a magas rendelkezésre állás érdekében, ha tranzakciók véglegesítése megbízható hashMaps. A Megbízható hashtérképekben tárolt objektumok a szolgáltatás helyi memóriájában vannak tárolva. Ez azt jelenti, hogy helyi hivatkozást tartalmaz az objektumra.
  
   Fontos, hogy ne mutálja az objektumok helyi példányait anélkül, hogy egy tranzakció megbízható gyűjteményén frissítési műveletet végezne. Ennek az az oka, hogy az objektumok helyi példányainak módosításai nem replikálódnak automatikusan. Újra be kell szúrnia az objektumot a szótárba, vagy a szótáregyik *frissítési* módszerét kell használnia.

A Megbízható állapotkezelő megbízható kivonatképeket kezel. Bármikor és a szolgáltatás bármely helyén kérheti a megbízható állapotkezelőtől a megbízható név szerinti gyűjteményt. A megbízható állapotkezelő biztosítja, hogy egy hivatkozást vissza. Nem javasoljuk, hogy mentse a megbízható gyűjteménypéldányokra mutató hivatkozásokat osztálytag-változókban vagy -tulajdonságokban. Különös figyelmet kell fordítani annak biztosítására, hogy a hivatkozás az üzemi életciklus során mindig egy példányra legyen beállítva. A Megbízható állapotkezelő kezeli ezt a munkát az Ön számára, és úgy van optimalizálva, ismételt látogatások.


### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és aszinkron műveletek
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

A megbízható hashtérképek műveletei aszinkronak. Ennek az az oka, hogy a Megbízható gyűjteményekkel végzett írási műveletek I/O-műveleteket hajtanak végre az adatok lemezre replikálásához és megőrzéséhez.

A megbízható HashMap-műveletek *tranzakciósak,* így több megbízható hashtérkép és művelet között is konzisztens állapotot tarthat. Például előfordulhat, hogy egy munkaelem egy megbízható szótár, végre egy műveletet rajta, és mentse az eredményt egy másik Megbízható HashMap, mindezt egyetlen tranzakción belül. Ez a rendszer atomi műveletként kezeli, és garantálja, hogy vagy a teljes művelet sikeres lesz, vagy a teljes művelet visszaáll. Ha hiba történik az elem várólistájának befejezése után, de az eredmény mentése előtt a teljes tranzakció visszalesz állítva, és az elem feldolgozásra a várólistában marad.


## <a name="build-the-application"></a>Az alkalmazás létrehozása

A Yeoman állványzat tartalmaz egy gradle-parancsfájlt az alkalmazás létrehozásához és a bash parancsfájlok üzembe helyezéséhez és eltávolításához. Az alkalmazás futtatásához először építse fel az alkalmazást gradle-lel:

```bash
$ gradle
```

Ez egy Service Fabric-alkalmazáscsomagot hoz létre, amely a Service Fabric CLI használatával telepíthető.

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

Az alkalmazás telepítése után nyisson meg egy böngészőt, és keresse fel a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)t a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) URL-címen. Bontsa ki az **Alkalmazások** csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazás típusához, és egy másik a típus első példányához.

> [!IMPORTANT]
> Az alkalmazás üzembe helyezéséhez egy biztonságos Linux-fürt az Azure-ban, konfigurálnia kell egy tanúsítványt, hogy érvényesítse az alkalmazást a Service Fabric futásidejű. Ezzel lehetővé teszi, hogy a Reliable Services-szolgáltatások kommunikáljanak az alapul szolgáló Service Fabric futásidejű API-kkal. További információ: [Reliable Services alkalmazás konfigurálása Linux-fürtökön való futtatáshoz.](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)  
>

## <a name="next-steps"></a>További lépések

* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
