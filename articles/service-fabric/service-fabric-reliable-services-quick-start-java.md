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
ms.openlocfilehash: 59b58e9d9bdb044c81261fd19338c3f95bd409b3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
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

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
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

## <a name="next-steps"></a>Következő lépések

* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
