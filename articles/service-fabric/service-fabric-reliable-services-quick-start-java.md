---
title: "Hozzon létre az első megbízható Azure mikroszolgáltatási Java nyelven |} Microsoft Docs"
description: "Bevezetés a Microsoft Azure Service Fabric-alkalmazás létrehozása az állapotmentes és állapotalapú szolgáltatással."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: e885a482edcba48c18e425c54f4acc28ee650ddd
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="get-started-with-reliable-services"></a>Ismerkedés a Reliable Services használatával
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
>
>

Ez a cikk Azure Service Fabric Reliable Services használatának alapjait ismerteti, és végigvezeti a létrehozott és telepített egy egyszerű Java nyelven írt megbízható szolgáltatásalkalmazást. A Microsoft Virtual Academy videó is bemutatja, hogyan hozzon létre egy megbízható állapotmentes szolgáltatások:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Telepítés és beállítás
Mielőtt elkezdené, győződjön meg arról, hogy a Service Fabric fejlesztőkörnyezet, állítsa be a számítógépre.
Ha szeretné beállítani, lépjen [Mac bevezetés](service-fabric-get-started-mac.md) vagy [Linux első lépések](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Alapfogalmak
Bevezetés a Reliable Services használatába, hogy meg kell ismernie néhány főbb fogalmait és kifejezéseit:

* **Szolgáltatás típusa**: Ez a szolgáltatás megvalósítása. Azt határozza meg a osztályt írt `StatelessService` és bármely más kód vagy függőségek használt, valamint nevét és verziószámát.
* **Szolgáltatáspéldány nevű**: a szolgáltatás futtatásához hoz létre a szolgáltatástípus elnevezett példányok sokkal például létrehozhat egy osztálytípus objektum példánya. Szolgáltatáspéldány valóban objektum példányának a szolgáltatásosztály írást.
* **Szolgáltatásgazda**: az elnevezett szolgáltatáspéldány létrehozása egy gazdagépen belül futtatnia kell. A szolgáltatásgazda csak az egyik folyamat, ahol a szolgáltatás példányának futtatható.
* **Eszközregisztrációs szolgáltatás**: regisztráció egyesíti mindent. A szolgáltatás típusának regisztrálva kell lennie a Service Fabric-futtatókörnyezet engedélyezése az osztályt létrehozni a Service Fabric-szolgáltatás gazdán futtatásához.  

## <a name="create-a-stateless-service"></a>Állapot nélküli szolgáltatás létrehozása
Először hozzon létre egy Service Fabric-alkalmazás. A Service Fabric SDK Linux tartalmaz egy Yeoman generátor a állványok a Service Fabric-alkalmazás az állapotmentes szolgáltatások biztosításához. Indítsa el a következő Yeoman futtassa a parancsot:

```bash
$ yo azuresfjava
```

Kövesse az útmutatást követve hozzon létre egy **megbízható állapotmentes szolgáltatások**. Ebben az oktatóanyagban nevezze el az alkalmazást "HelloWorldApplication" és a "HelloWorld" szolgáltatás. Az eredmény tartalmazza a könyvtárak a `HelloWorldApplication` és `HelloWorld`.

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
### <a name="service-registration"></a>Eszközregisztrációs szolgáltatás
Szolgáltatástípusok a Service Fabric-futtatókörnyezet regisztrálva kell lennie. A szolgáltatás típus definiálva van a `ServiceManifest.xml` és a szolgáltatás osztály, amely megvalósítja az `StatelessService`. A folyamat a fő belépési pont szolgáltatás regisztrációs történik. Ebben a példában a folyamat fő belépési pont van `HelloWorldServiceHost.java`:

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

Nyissa meg **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Ez az osztály a szolgáltatás típusa határozza meg, és semmilyen kódot futtathat. A service API két belépési pontok biztosít a kódot:

* Egy nyílt belépési pont metódus hívása `runAsync()`, ahol el lehet kezdeni a munkaterheléseket, ideértve a hosszan futó számítási feladatok végrehajtása.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Egy kommunikációs belépési pontot, amelyen csatlakoztathatja a kommunikációs verem választott. Ez azért, ahol elindíthatja kéréseket fogad a felhasználók és más szolgáltatások védelmét.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Az oktatóanyag azt összpontosítani a `runAsync()` belépési pont metódusa. Ez azért, ahol azonnal elindíthatja a kódja.

### <a name="runasync"></a>RunAsync
A platform ezt a módszert hívja, ha a szolgáltatás egy példánya elhelyezett és a végrehajtásra kész. Az állapotmentes szolgáltatások, amely egyszerűen jelenti, hogy a service-példány már meg van nyitva. A megszakítási jogkivonat koordinálására, amikor a szolgáltatáspéldány kell lezárni van megadva. A Service Fabric a megnyitása/bezárása ciklus egy szolgáltatáspéldány akkor fordulhat elő, a szolgáltatás életciklusa alatt számos alkalommal egész. Ez akkor fordulhat elő, beleértve a különböző okokból:

* A rendszer áthelyezi a szolgáltatáspéldány terheléselosztás erőforrás.
* Hibák merülnek fel, a kódban.
* Az alkalmazás- vagy frissítése.
* Az alapul szolgáló hardverben során kimaradás lép fel.

A vezénylési kezeli a Service Fabric tartani a szolgáltatás magas rendelkezésre állású és megfelelően kiegyensúlyozott által.

`runAsync()`meg nem blokkolják szinkron módon történik. RunAsync megvalósítását kell visszaadnia egy CompletableFuture folytatja a futtatókörnyezet engedélyezéséhez. Ha a számítási feladatok kell-e a CompletableFuture belül el kell végezni hosszú ideig futó feladat végrehajtásához.

#### <a name="cancellation"></a>Megszakítása
A számítási feladatok megszakítását egy együttműködési elérhető a megadott cancellation jogkivonat által összehangolva. A rendszer vár a feladat befejezéséhez (által sikeres befejezése, megszakítása vagy hiba), mielőtt az átvitel során. Fontos, hogy a megszakítási token tiszteletben, Befejezés munka és kilépés `runAsync()` lehető leggyorsabban tegye, amikor a rendszer törlését kéri. A következő példa bemutatja, hogyan kezelje a megszakítási esemény:

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

A állapotmentes szolgáltatások példában a count lokális változó tárolja. Azonban mivel ez egy állapotmentes szolgáltatások, a tárolt érték csak az aktuális a szolgáltatáspéldány-életciklus létezik. A szolgáltatás helyezi át, vagy újraindul, az érték elvész.

## <a name="create-a-stateful-service"></a>Az állapotalapú szolgáltatás létrehozása
A Service Fabric egy olyan szolgáltatás, amely állapotalapú új típusú vezet be. Az állapotalapú szolgáltatás megbízhatóan belül a szolgáltatást, a kód által használt közösen elhelyezett is állapotban. Állapot köszönhetően magas rendelkezésre állású Service Fabric nélkül megőrizni a külső áruházban állapotra.

Átalakítása a teljesítményszámláló értéke állapot nélküli magas rendelkezésre állású és állandó, akkor is, ha a szolgáltatás áthelyezi vagy újraindul, szükség van egy állapotalapú szolgáltatásra.

A HelloWorld alkalmazás könyvtárába, adhat hozzá egy új szolgáltatás futtatásával a `yo azuresfjava:AddService` parancsot. Válassza ki a "megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás" a keretrendszer, és a szolgáltatás "HelloWorldStateful". 

Az alkalmazás most már rendelkeznie kell a két szolgáltatás: az állapotmentes szolgáltatások HelloWorld és az állapotalapú szolgáltatási HelloWorldStateful.

Állapotalapú szolgáltatási állapot nélküli szolgáltatásként ugyanazon belépési pontokat rendelkezik. A fő különbség a rendelkezésre állási állapot szolgáltatóra állapot megbízhatóan tárolására is. A Service Fabric tartalmaz egy állapot szolgáltató általi megvalósítása megbízható gyűjteménynek, amely lehetővé teszi a megbízható állapotkezelője keresztül replikált adatstruktúrák létrehozását. Egy állapot-nyilvántartó megbízható szolgáltatás alapértelmezés szerint az állapotszolgáltató használja.

Nyissa meg a HelloWorldStateful.java **HelloWorldStateful src ->**, amely tartalmazza a következő RunAsync metódusában:

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
`RunAsync()`az állapot nélküli és állapotalapú alkalmazások és szolgáltatások hasonlóan működnek. Azonban az állapotalapú service, a platform hajt végre további feladata az Ön nevében végrehajtása előtt `RunAsync()`. Ez a munkahelyi tartalmazhatnak, ezzel biztosítható, hogy a megbízható állapotkezelője és megbízható gyűjtemények rendszer készen áll a használatra.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Megbízható gyűjtemények és a megbízható állapot-kezelő
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) a szótár megvalósítása, amely megbízható adattárolóban állapotokat a szolgáltatás segítségével. A Service Fabric és a megbízható Hashmaps tárolható adatok közvetlenül a szolgáltatás egy külső állandó tároló szükségessége nélkül. Megbízható Hashmaps magas rendelkezésre állásúvá adatait. A Service Fabric ezt a feladatot el létrehozásával és kezelésével több *replikák* meg a szolgáltatás. Az API-k, amelyek számítógépnél kivonatolja a bonyolultságára ezeket a replikák és a Állapotváltások kezelése is tartalmazza.

Megbízható gyűjtemények Java bármilyen, beleértve a felhasználói típusok, a figyelmeztetések néhány tudja tárolni:

* A Service Fabric lehetővé teszi a állapot magas rendelkezésre állású által *replikálása* csomópontokat, és megbízható Hashmap állapot tárolja az adatokat, hogy a helyi lemez minden replikán. Ez azt jelenti, hogy minden, a megbízható Hashmaps tárolt kell *szerializálható*. 
* Objektumok replikálása a magas rendelkezésre állású ha véglegesíti a megbízható Hashmaps tranzakciók. A megbízható Hashmaps tárolt objektumok kell tartani a szolgáltatás a helyi memóriához. Ez azt jelenti, hogy rendelkezik-e helyi hivatkozik az objektumra.
  
   Fontos, hogy Ön nem mutálódni azokat az objektumokat helyi példányát a megbízható gyűjtemény a frissítési művelet végrehajtása egy tranzakcióban nélkül. Ez az objektum helyi példányát módosításai nem lesz automatikusan replikálja. Szúrja be az objektumot újra üzembe a szótár kell vagy valamelyikével a *frissítése* metódusai a szótárban.

A megbízható állapotkezelője megbízható Hashmaps az Ön kezeli. Egyszerűen kérje meg a megbízható állapotkezelője megbízható gyűjtemény neve és bárhol, bármikor a szolgáltatásban. A megbízható állapotkezelője biztosítja, hogy vissza a hivatkozás. Nem ajánlott az, hogy menti megbízható gyűjtemény példányok hivatkozik a tag változók vagy tulajdonságok. Győződjön meg arról, hogy a hivatkozás értéke egy példányát a szolgáltatás életciklusának mindig különös gondot kell fordítani. A megbízható állapotkezelője kezeli a megfelelőek Önnek, és ismétlési látogatások van optimalizálva.


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

A megbízható Hashmaps műveletek aszinkron jellegűek. Ennek az az oka megbízható gyűjteményekkel írási műveletek végrehajtása i/o-műveletek történő replikálásához és megőrizni az adatokat a lemezre.

Megbízható Hashmap műveletek *tranzakciós*, így biztosítható állapot konzisztens több megbízható Hashmaps és műveletek. Például Ön lekérni a munkaelem egy megbízható szótárból, rajta egy adott művelet elvégzéséhez, és mentse az eredmény anoter megbízható Hashmap, egy tranzakción belül. Ez a rendszer egy atomi művelet, és Ez garantálja, hogy vagy a teljes műveletet sikeres lesz, vagy a teljes műveletet állítja vissza. Ha hiba lép fel, akkor created elem után, de az eredmény mentése előtt, a teljes tranzakció vissza lesz állítva, és a marad, a feldolgozási várakozási sorban.


## <a name="run-the-application"></a>Az alkalmazás futtatása

A Yeoman állványok tartozik gradle parancsfájl építenie az alkalmazást, és a bash parancsfájlok telepítéséhez, és távolítsa el az alkalmazást. Az alkalmazás futtatásához előbb építenie az alkalmazást a gradle-lel:

```bash
$ gradle
```

Ezzel létrehozza a Service Fabric alkalmazáscsomagok, amelyek a Service Fabric parancssori felület használatával is telepíthető.

### <a name="deploy-with-service-fabric-cli"></a>A Service Fabric parancssori felület telepítése

A install.sh parancsfájl tartalmazza a szükséges Service Fabric parancssori felület parancsai az alkalmazáscsomag telepítéséhez. Futtassa a install.sh Telepítse központilag az alkalmazást.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>További lépések

* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
