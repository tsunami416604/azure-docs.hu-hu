---
title: Diagnosztika és hibaelhárítás az Azure Cosmos DB Java aszinkron SDK |} A Microsoft Docs
description: Szolgáltatások használata, mint az ügyféloldali naplózás, és más külső eszközök azonosításához, diagnosztizálása és elhárítása az Azure Cosmos DB-hez.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ee92a5dd474cdf4f32ed2c7327d732a2cfbbbf79
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632934"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Java aszinkron SDK használatával az Azure Cosmos DB SQL API-fiókokkal kapcsolatos hibák elhárítása
Ez a cikk ismerteti gyakori problémákat, megoldások, diagnosztikai lépések és eszközök használata esetén [Java aszinkron ADK](sql-api-sdk-async-java.md) az Azure Cosmos DB SQL API-fiókok.
Java SDK-val aszinkron ügyféloldali logikai leképezést el az Azure Cosmos DB SQL API-t biztosít. Ez a cikk ismerteti az eszközök és a megközelítés segít, ha bármilyen problémába.

Indítsa el ezt a listát:
    * Vessen egy pillantást a [gyakori problémák és megoldások] szakasz ebben a cikkben.
    * Az SDK [nyílt forráskódú, a githubon](https://github.com/Azure/azure-cosmosdb-java) és [szakasz problémák](https://github.com/Azure/azure-cosmosdb-java/issues) , amely aktívan figyeljük. Ellenőrizze, hogy ha bármilyen probléma látja a megoldás már be.
    * Felülvizsgálat [teljesítménnyel kapcsolatos tippek](performance-tips-async-java.md) és kövesse az ajánlott eljárásokat.
    * Kövesse az ebben a cikkben a többi megoldás nem találta meg, ha a fájl egy [GitHub-problémát](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Gyakori problémák és megoldások

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Hálózati problémák, Netty időtúllépési hiba, az alacsony átviteli sebesség, a nagy késleltetésű olvasása

#### <a name="general-suggestions"></a>Általános javaslatok
* Győződjön meg arról, hogy az alkalmazás fut, és a Cosmos DB-fiók ugyanabban a régióban. 
* Ellenőrizze a gazdagép, ahol az alkalmazás fut, a CPU-használat. Ha CPU-használata 90 %-os vagy annál több, fontolja meg az alkalmazás magasabb konfigurációval gazdagépen futnak, vagy további gépek terhelésének elosztása.

#### <a name="connection-throttling"></a>Kapcsolat-szabályozás
Kapcsolat szabályozás fordulhat elő, okozhatták [Gazdagépen kapcsolathoz megadott korlátot], vagy [Az Azure SNAT (PAT) portfogyás]:

##### <a name="connection-limit-on-host"></a>Gazdagépen kapcsolathoz megadott korlátot
Egyes Linux rendszerek (például a "Red Hat") rendelkezik egy felső korlátot megnyitott fájlok teljes száma. Sockets a Linux-fájlok vannak megvalósítva, ez a szám korlátozza a kapcsolatok száma túl.
Futtassa az alábbi parancsot:

```bash
ulimit -a
```
A fájlok ("nofile") kell lennie (a legkevésbé dupla, a kapcsolatkészlet mérete) elég nagy a nyílt száma. Olvassa el részletes [teljesítménnyel kapcsolatos tippek](performance-tips-async-java.md).

##### <a name="snat"></a>Az Azure SNAT (PAT) portfogyás

Ha az alkalmazás telepíthető Azure virtuális gép nyilvános IP-cím nélkül, alapértelmezés szerint [Azure SNAT portok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) használatával hoz létre a kapcsolatot a virtuális Gépen kívüli bármely végpont. A virtuális gépről a Cosmos DB végpont engedélyezett kapcsolatainak számát korlátozza a [Azure SNAT konfigurációs](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

A rendszer az Azure SNAT portokat használja, csak akkor lehetséges, ha az Azure virtuális gép magánhálózati IP-címmel rendelkezik, és a virtuális gépről egy folyamatot próbál egy kapcsolatot egy nyilvános IP-címet. Tehát van két lehetséges megoldások Azure SNAT korlátozás elkerülése érdekében:
    * Az Azure Cosmos DB-szolgáltatásvégpont hozzáadása az Azure virtuális gép virtuális hálózat alhálózatának leírtak [szolgáltatásvégpont engedélyezése](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Szolgáltatásvégpont engedélyezésekor a a már nem küld egy nyilvános IP-címet a cosmos DB inkább a virtuális hálózathoz, és a rendszer alhálózati identitást küldi el. Ez a változás tűzfal csepp eredményezhet, ha csak nyilvános IP-címek használata engedélyezett. Ha a tűzfal, amikor a szolgáltatásvégpont engedélyezése használ, a tűzfal használatával alhálózat hozzáadása [virtuális hálózati hozzáférés-vezérlési listák](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Az Azure virtuális gép nyilvános IP-cím hozzárendelése.

#### <a name="http-proxy"></a>A HTTP-proxy

Ha egy HTTP használata esetén ellenőrizze, hogy a HTTP támogathatja az SDK-ban konfigurált kapcsolatok száma `ConnectionPolicy`.
Ellenkező esetben között kapcsolódási problémák.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Érvénytelen kódolási minta: Netty i/o-szál blokkolása

Az SDK az [Netty](https://netty.io/) való kommunikációhoz, az Azure Cosmos DB szolgáltatás i/o-könyvtár. Aszinkron API van, és nem blokkoló i/o-API-k, netty használjuk. Az SDK-i/o munkahelyi i/o-netty szálak történik. I/o-netty szálak számának lett konfigurálva ugyanaz, mint az alkalmazás gép CPU-magok számát. A netty i/o-hozzászólásláncok csak van kialakítva, hogy használható nem blokkoló netty i/o-munka. Az SDK-t adja vissza, az alkalmazások kódot az API-hívási eredmény egy netty i/o-szál. Ha az alkalmazás megjelenését a netty szálon eredmények netty szál, SDK-t nem rendelkezik elegendő belső i/o-feladatának végrehajtásához IO szálak száma eredményezhet hosszú tartós műveletet hajt végre. Ilyen alkalmazás kódolási eredményezhet alacsony átviteli sebesség, a magas késleltetés és `io.netty.handler.timeout.ReadTimeoutException` hibák. A megoldás, hogy a szál váltson, ha tudja, hogy a művelet időt vesz igénybe.

   Például a következő kódrészletet bemutatja, hogy mennyi ideig tartós munka, több mint néhány ezredmásodperc alatt, a netty szál hajt végre, ha végül megtekintheti egy állapotba kerül, ahol nincs netty i/o-szál nem található az i/o-feldolgozás, és ennek eredményeképpen ReadTimeou kap tException:
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   A megoldás, hogy módosítsa a hozzászólásláncot, amelyre a idő véve a munka elvégzéséhez. Adja meg az alkalmazás a Scheduler egy egyszeri példányát:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Idő véve munkahelyi (például a nagy számítási nehéz munkahelyi blokkolása az I/O) van szüksége, váltson a szál egy feldolgozó által biztosított a `customScheduler` használatával `.observeOn(customScheduler)` API-t.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
Használatával `observeOn(customScheduler)`, kiadási a netty i/o-szálat, és váltson át a saját egyéni szál customScheduler által biztosított. Ez a módosítás fogja megoldani a problémát, és nem kap `io.netty.handler.timeout.ReadTimeoutException` többé hiba.

### <a name="connection-pool-exhausted-issue"></a>Kapcsolati probléma kimerült

`PoolExhaustedException` ügyféloldali hiba történik. Ha a hiba gyakran kap, ez jelzi, hogy az alkalmazás számítási feladatok nagyobb tudjon kiszolgálni, mely az SDK-kapcsolatkészletben. Előfordulhat, hogy növelve kapcsolatkészlet mérete, vagy több alkalmazás terhelésének elosztása érdekében.

### <a name="request-rate-too-large"></a>Kérelmek száma túl nagy
Ezt a hibát jelzi, hogy a kiosztott átviteli sebesség felhasznált, és később kell próbálkoznia. kiszolgálóoldali hiba történik. Ha a hiba gyakran kap, fontolja meg a gyűjtemény kapacitásának növelése.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Nem sikerült kapcsolódni az Azure Cosmos DB emulator

Cosmos DB emulator HTTPS-tanúsítvány önaláírt. SDK emulator dolgozni kell importálja az emulátor tanúsítvány Java TrustStore. Leírtak [Itt](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Ügyfél-SDK naplózásának engedélyezése

A naplózás a adapterréteget a Java SDK-val aszinkron használja SLF4j támogató elterjedt naplózási keretrendszer például log4j, a logback bejelentkezik.

Például ha azt szeretné, log4j használandó naplózási keretrendszer, adja hozzá a következő libs a Java osztályútvonal:

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Is hozzáadhat egy log4j config:
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Felülvizsgálat [sfl4j naplózási manuális](https://www.slf4j.org/manual.html) további információt.

## <a name="netstats"></a>Az operációs rendszer hálózati statisztika
Futtassa a netstat parancsot megtapasztalhatja, a rendszer hány kapcsolatok `Established` állapot, `CLOSE_WAIT` állapotban van, stb.

Linux rendszeren futtathatja a következő parancsot:
```bash
netstat -nap
```
A Cosmos DB végpont csak kapcsolatokat, az eredmények szűréséhez.

Kártevőnek, a Cosmos DB-végpontra a kapcsolatok száma `Established` állapota nem lehet nagyobb, mint a konfigurált kapcsolati készlet méretét.

Van-e a Cosmos DB végpont létesített kapcsolatok száma `CLOSE_WAIT` állapotba, például több mint 1000 kapcsolatot, ami arra utalhat, hogy a kapcsolat létrejött és bontva, amely potenciálisan problémákat okozhat. Felülvizsgálat [gyakori problémák és megoldások] részletesebben a következő szakaszban.

 <!--Anchors-->
[Gyakori problémák és megoldások]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Gazdagépen kapcsolathoz megadott korlátot]: #connection-limit-on-host
[Az Azure SNAT (PAT) portfogyás]: #snat


