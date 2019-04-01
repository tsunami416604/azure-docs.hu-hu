---
title: Hozzon létre az első Azure Service Fabric reliable Services Java nyelven |} A Microsoft Docs
description: Bevezetés a Microsoft Azure Service Fabric-alkalmazás létrehozása állapot nélküli és állapotalapú szolgáltatásokkal.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 6bf8c632a7513d018745bc74aa0a1db95a39af8b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663190"
---
# <a name="get-started-with-reliable-services"></a>Ismerkedés a Reliable Services használatával
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
>
>

Ez a cikk ismerteti az Azure Service Fabric Reliable Services alapjait, és végigvezeti a létrehozása és telepítése Java nyelven írt egyszerű Reliable Services-alkalmazás. 

## <a name="installation-and-setup"></a>Telepítés és beállítás
Mielőtt elkezdené, győződjön meg arról, hogy telepítette a Service Fabric fejlesztési környezet beállítása a gépen.
Ha azt szeretné, lépjen a [– az első lépések Mac](service-fabric-get-started-mac.md) vagy [Ismerkedés a Linuxos](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Alapfogalmak
Ismerkedés a Reliable Services szolgáltatással, akkor csak kell néhány alapvető fogalmak megismerése:

* **Szolgáltatás típusa**: Ez a szolgáltatás megvalósítása. Azt határozza meg az osztályt, írása, amely kibővíti `StatelessService` és más kód vagy függőségeket, egy nevet és a egy verziószámot együtt használni.
* **Szolgáltatáspéldány nevű**: A szolgáltatás futtatásához hoz létre a típusú szolgáltatás elnevezett példányok sokkal például osztálytípus figyelőobjektum-példányok létrehozása. Szolgáltatás példányai valójában az írást szolgáltatásosztály megvalósításokkal objektum.
* **Szolgáltatásgazda**: A nevesített szolgáltatáspéldány létrehozása egy gazdagépen belül futtatnia kell. A szolgáltatásgazda az csak az egy folyamatot, ahol a szolgáltatás példányát futtathatja.
* **Eszközregisztrációs szolgáltatás**: Regisztrációs magában foglal minden. A szolgáltatás típusának futtatásához egy gazdagépet a szolgáltatás példányainak, Service Fabric lehetővé teszi a Service Fabric-futtatókörnyezet regisztrálva kell lennie.  

## <a name="create-a-stateless-service"></a>Állapotmentes szolgáltatás létrehozása
Először hozzon létre egy Service Fabric-alkalmazás. A Linuxhoz készült Service Fabric SDK tartalmaz egy Yeoman generátor át az állapotmentes szolgáltatás a Service Fabric-alkalmazás a keret létrehozásához adjon meg. Indítsa el a következő Yeoman futtatásával parancsot:

```bash
$ yo azuresfjava
```

Kövesse az utasításokat, hozzon létre egy **Reliable Stateless Services**. Ebben az oktatóanyagban nevezze el az alkalmazást "HelloWorldApplication" és a "HelloWorld" szolgáltatást. Az eredmény tartalmazza a könyvtárak a `HelloWorldApplication` és `HelloWorld`.

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
### <a name="service-registration"></a>Szolgáltatás regisztrációs
Szolgáltatástípusok a Service Fabric-futtatókörnyezet regisztrálva kell lennie. A szolgáltatás típusának van definiálva a `ServiceManifest.xml` és a szolgáltatás osztály, amely megvalósítja `StatelessService`. A folyamat hlavní vstupní BOD szolgáltatás regisztrációs történik. Ebben a példában a folyamat hlavní vstupní BOD van `HelloWorldServiceHost.java`:

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

Open **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Ez az osztály a szolgáltatás típusa határozza meg, és bármely kódot futtathatja. A szolgáltatás API-t biztosít a kód két belépési pontok:

* Nevű egy nyílt belépési pont metódust `runAsync()`, megkezdheti a számítási feladatokat, beleértve a hosszú ideig futó számítási feladatok végrehajtása.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Egy kommunikációs belépési ponthoz csatlakoztatható, ahol a választott kommunikációs verem. Ez az, ahol elkezdheti kérések fogadása a felhasználók és más szolgáltatások védelmét.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ebben az oktatóanyagban összpontosít a `runAsync()` belépési pont metódust. Ez az, ahol azonnal elkezdheti a kódja fut.

### <a name="runasync"></a>RunAsync
A platform meghívja ezt a módszert, ha egy szolgáltatás egy példányának elhelyezett, és készen áll a hajtsa végre. Egy állapotmentes szolgáltatás, amely azt jelenti, hogy a szolgáltatáspéldány megnyitásakor. A megszakítás jogkivonat van megadva, a koordinációt, amikor a service-példányt kell zárni. A Service Fabric-példányok a megnyitása vagy bezárása ciklus fordulhat elő, a szolgáltatás életciklusa alatt számos alkalommal egészére. Ez akkor fordulhat elő, beleértve a különböző okok miatt:

* A rendszer a szolgáltatáspéldányok terheléselosztási erőforrás helyezi.
* A kód fordulnak elő hibák.
* Az alkalmazás- vagy frissül.
* Az alapul szolgáló hardver során kimaradás lép fel.

Ennek vezénylését felügyelt, hogy a szolgáltatás magas rendelkezésre állású és megfelelően kiegyensúlyozott Service Fabric által.

`runAsync()` érdemes blokkolja a szinkron módon történik. RunAsync megvalósítását, hogy a modul folytatja a CompletableFuture adja vissza. Ha a számítási feladatok szüksége van egy adott hosszú ideig futó feladat, amely a CompletableFuture belül kell elvégezni.

#### <a name="cancellation"></a>Megszakítás
A számítási feladat megszakításának célja egy olyan együttműködési vezényli a megadott megszakítás jogkivonatot. A rendszer megvárja, amíg a feladat befejezéséhez (a sikeres befejezése, megszakítás vagy hiba), mielőtt áthelyezi azt. Fontos, hogy tartsa tiszteletben a megszakítás jogkivonat, Befejezés munka és a kilépéshez `runAsync()` ismerhető meg leggyorsabban, amikor a rendszer kéri a megszakítás. Az alábbi példa bemutatja, hogyan kezeli a törlés eseményt:

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

Az állapotmentes szolgáltatás ebben a példában egy helyi változó tárolja a száma. De mivel ez egy állapotmentes szolgáltatás, a tárolt érték csak az aktuális élettartama a szolgáltatáspéldány létezik-e. A szolgáltatás áthelyezi vagy újraindul, ha az érték elvész.

## <a name="create-a-stateful-service"></a>Állapotalapú szolgáltatás létrehozása
A Service Fabric állapotalapú szolgáltatás egy új adatraktározási mutatja be. Az állapotalapú szolgáltatások megbízható a szolgáltatásban, a kód által használt közös elhelyezésű állapot fenntartására. Állapot magas rendelkezésre állású legyen a Service Fabric által állapotban ahhoz, hogy egy külső tároló megőrzése nélkül.

Konvertálható egy számláló értékét állapot nélküli, magas rendelkezésre állású, tartós, akkor is, ha a szolgáltatás áthelyezi vagy újraindul, az állapotalapú szolgáltatások van szükség.

Ugyanabban a könyvtárban az HelloWorld alkalmazásként, hozzáadhat egy új szolgáltatás fut a `yo azuresfjava:AddService` parancsot. Válassza ki a "megbízható állapotalapú szolgáltatás" a keretrendszer, és nevezze el a szolgáltatást "HelloWorldStateful". 

Az alkalmazás most már két szolgáltatást: a HelloWorld állapotmentes szolgáltatás és az állapotalapú szolgáltatás HelloWorldStateful.

Az állapotalapú szolgáltatások a ugyanazon belépési pontok állapotmentes szolgáltatás rendelkezik. A fő különbség a rendelkezésre állási állapot-szolgáltató állapota megbízható tárolását is. A Service Fabric egy állam adatbázisszolgáltatói implementációt, amely a Reliable State Manager keresztül replikált adatok struktúrák létrehozását teszi lehetővé a Reliable Collections nevű tartalmaz. Egy állapotalapú Reliable Services alapértelmezés szerint ez szolgáltatóját használja.

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
`RunAsync()` állapotalapú és állapotmentes szolgáltatások hasonlóan működik. Azonban egy állapotalapú szolgáltatásban, a platform további munkát végez az Ön nevében hajtja végre a rendszer `RunAsync()`. Ezt a munkát ezzel biztosítható, hogy a Reliable State Manager és a Reliable Collections készen áll a használatára is tartalmazhatnak.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>A Reliable Collections és a Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) szótár megvalósítása, amely segítségével megbízhatóan tárolja az állapot a szolgáltatásban. A Service Fabric és a megbízható HashMaps adatokat közvetlenül a szolgáltatásban egy külső állandó tároló nélkül tárolhatja. Megbízható HashMaps legyen az adatok magas rendelkezésre állású. A Service Fabric úgy hajtja ezt végre létrehozása és kezelése több *replikák* a szolgáltatás az Ön számára. Egy API, amely a kezelésével járó összetettséget, e replikák és az állapotváltozási adat áramlik kódbázis is tartalmazza.

A Reliable Collections képes tárolni bármilyen Java, beleértve az egyéni típusok, mindössze pár korlátozásokkal:

* A Service Fabric biztosítja az állapot szerint magas rendelkezésre állású *replikálása* csomópont, és megbízható HashMap között állapota minden egyes replikának tárolja az adatokat a helyi lemezre. Ez azt jelenti, hogy minden tárolt megbízható HashMaps kell *szerializálható*. 
* Objektumok a magas rendelkezésre állású lesznek replikálva, amikor a megbízható HashMaps tranzakció véglegesítése után. Megbízható HashMaps tárolt objektumok őrzi meg a helyi memória a szolgáltatásban. Ez azt jelenti, hogy rendelkezik-e egy helyi odkaz nA Objekt.
  
   Fontos, hogy Ön nem mutálódni azokat az objektumokat helyi példánya egy tranzakcióban a megbízható gyűjteményben frissítési műveletek végrehajtása nélkül. Ennek oka az, helyi példánya objektumok módosításai nem lesznek automatikusan replikálva. Az objektum helyezze vissza a szótár vagy valamelyikével kell a *frissítése* a szótár metody.

A Reliable State Manager megbízható HashMaps kezeli az Ön számára. Kérhet a Reliable State Manager egy megbízható gyűjteményben név szerint bármikor és bárhol a service-ben. A Reliable State Manager biztosítja, hogy vissza egy hivatkozást. Nem javasoljuk, hogy menti a megbízható gyűjtemény példányok mutató hivatkozások tagváltozók osztály vagy tulajdonság. Speciális ügyelni kell annak érdekében, hogy a hivatkozás értéke egy példánya mindig a szolgáltatás-életciklusának. A Reliable State Manager kezeli ezt a munkát, az Ön számára, és ismételje meg a műveletet látogatások van optimalizálva.


### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és az aszinkron műveletek
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

A megbízható HashMaps műveletek aszinkron jellegűek. Ennek az oka az írási műveletek a Reliable Collections replikálja, és megőrizni az adatokat a lemezre i/o-műveletek végrehajtása.

Megbízható HashMap műveletek *tranzakciós*, így megtarthatja állapot konzisztens több megbízható HashMaps és műveleteket. Például, előfordulhat, hogy egy munkaelemet beolvasni egy megbízható szótárban végrehajtania egy műveletet, és mentheti az eredményt egy másik, megbízható HashMap, egy tranzakción belül a. Ez atomi műveletnek számít, és Ez garantálja, hogy vagy a teljes művelet sikeres lesz, vagy a teljes művelet visszaállítja. Hiba bekövetkezése után, az elem eltávolítása a sorból, de még mielőtt mentené az eredményt, a teljes tranzakció vissza lesz állítva, és az elem továbbra is a feldolgozási várakozási sorban.


## <a name="build-the-application"></a>Az alkalmazás létrehozása

A Yeoman szerkezetkialakító tartalmaz egy gradle szkript hozza létre az alkalmazást, és a bash-szkriptek üzembe helyezéséhez, és távolítsa el az alkalmazást. Az alkalmazás futtatásához, hozhat létre az alkalmazás a gradle-lel:

```bash
$ gradle
```

Ez hoz létre egy Service Fabric-alkalmazáscsomagot, Service Fabric parancssori felület használatával telepíthető.

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
> Biztonságos Linux-fürt az Azure-ban az alkalmazás központi telepítése, konfigurálása a Service Fabric-futtatókörnyezet az alkalmazás érvényesítendő tanúsítvánnyal kell. Ez lehetővé teszi a Reliable Services szolgáltatások kommunikálni az alapul szolgáló Service Fabric-futtatókörnyezet API-k. További tudnivalókért lásd: [egy Reliable Services-alkalmazás Linux-fürtök konfigurálása](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>További lépések

* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
