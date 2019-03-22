---
title: Diagnosztika és hibaelhárítás az Azure Cosmos DB Java aszinkron SDK
description: Szolgáltatások használata, mint az ügyféloldali naplózás és más külső eszközök, diagnosztika, problémák azonosítása és elhárítása az Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 0a2bbb33182fcdef3cc6ed7ff213557f90be4544
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880075"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Hibák elhárításához, ha az aszinkron Java SDK használata Azure Cosmos DB SQL API-fiókok
Ez a cikk ismerteti gyakori problémák, megoldások, diagnosztikai lépések és eszközök használata esetén a [Java SDK-val aszinkron](sql-api-sdk-async-java.md) az Azure Cosmos DB SQL API-fiókok.
Az aszinkron Java SDK biztosít ügyféloldali logikai leképezést az Azure Cosmos DB SQL API eléréséhez. Ez a cikk ismerteti az eszközökre és megközelítési segítséget, ha bármilyen problémába.

Indítsa el ezt a listát:

* Vessen egy pillantást a [gyakori problémák és megoldások] szakasz ebben a cikkben.
* Tekintse meg az SDK-t elérhető [nyílt forráskód a Githubon](https://github.com/Azure/azure-cosmosdb-java). Rendelkezik egy [szakasz problémák](https://github.com/Azure/azure-cosmosdb-java/issues) , amely aktív figyelés alatt. Ellenőrizze, hogy ha bármilyen hasonló probléma áthidaló már be.
* Tekintse át a [teljesítménnyel kapcsolatos tippek](performance-tips-async-java.md), és kövesse az ajánlott eljárásokat.
* Ez a cikk további részének olvasása, ha nem talált megoldást. Majd a fájl egy [GitHub-problémát](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Gyakori problémák és megoldások

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Hálózati problémák, Netty időtúllépési hiba, az alacsony átviteli sebesség, a nagy késleltetésű olvasása

#### <a name="general-suggestions"></a>Általános javaslatok
* Győződjön meg arról, hogy az alkalmazás fut, és az Azure Cosmos DB-fiók ugyanabban a régióban. 
* Ellenőrizze a gazdagép, ahol az alkalmazás fut, a CPU-használat. Ha CPU-használata 90 %-os vagy annál több, az alkalmazás futtatása egy magasabb konfiguráció gazdagépeken. Vagy juttathatja el további gépeket érő terhelést.

#### <a name="connection-throttling"></a>Kapcsolat-szabályozás
Kapcsolat szabályozás feltehetően vagy egy [Egy gazdagépen a kapcsolathoz megadott korlátot] vagy [Az Azure SNAT (PAT) portfogyás].

##### <a name="connection-limit-on-host"></a>Egy gazdagépen a kapcsolathoz megadott korlátot
Egyes Linux rendszerek, például a Red Hat, megnyitott fájlok teljes száma a felső korlát rendelkezik. Sockets a Linux-fájlok vannak megvalósítva, ez a szám túl korlátozza a kapcsolatokat, teljes számát.
Futtassa a következő parancsot.

```bash
ulimit -a
```
A szám maximális engedélyezett megnyitott fájlok, amelyek "nofile" eszközként van azonosítva, kell lennie a double legalább a kapcsolatkészlet mérete. További információkért lásd: [teljesítménnyel kapcsolatos tippek](performance-tips-async-java.md).

##### <a name="snat"></a>Az Azure SNAT (PAT) portfogyás

Ha az alkalmazás van üzembe helyezve az Azure Virtual machines szolgáltatásban egy nyilvános IP-cím nélkül alapértelmezés szerint [Azure SNAT portok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) minden végpontot a virtuális Gépen kívüli kapcsolatokat hozhat létre. A virtuális gépről az Azure Cosmos DB végpont engedélyezett kapcsolatainak számát korlátozza a [Azure SNAT konfigurációs](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Az Azure SNAT portok szolgálnak, csak akkor lehetséges, ha a virtuális gép magánhálózati IP-címmel rendelkezik, és a egy folyamatot, a virtuális gépről megpróbál csatlakozni a nyilvános IP-címet. Nincsenek Azure SNAT korlátozás elkerülése érdekében a két lehetséges megoldások:

* Az Azure Cosmos DB-szolgáltatásvégpont hozzáadása az Azure Virtual Machines virtuális hálózat alhálózatának. További információkért lásd: [Azure virtuális hálózati Szolgáltatásvégpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Ha a szolgáltatásvégpont engedélyezve van, a már nem küld a nyilvános IP-cím az Azure Cosmos DB. Ehelyett a virtuális hálózat és alhálózat identitást kapnak. Ez a változás tűzfal csepp eredményezhet, ha csak nyilvános IP-címek használata engedélyezett. Ha a szolgáltatásvégpont engedélyezésekor, tűzfalat használ, adjon hozzá egy alhálózatot a tűzfal használatával [virtuális hálózati hozzáférés-vezérlési listák](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Az Azure virtuális gép nyilvános IP-cím hozzárendelése.

#### <a name="http-proxy"></a>A HTTP-proxy

Ha egy HTTP-proxyt használ, ellenőrizze, hogy tudja támogatni az SDK-ban konfigurált kapcsolatok száma `ConnectionPolicy`.
Ellenkező esetben között kapcsolódási problémák.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Érvénytelen kódolási minta: Netty i/o-szál blokkolása

Az SDK-t használ a [Netty](https://netty.io/) kommunikálni az Azure Cosmos DB i/o-könyvtár. Az SDK aszinkron API-kkal rendelkezik, és használja a nem blokkoló Netty API-k i/o. Az SDK-i/o munkahelyi i/o-Netty szálak történik. I/o-Netty szálak számának lett konfigurálva ugyanaz, mint az alkalmazás gép CPU-magok számát. 

Az Netty i/o-szálak úgy van kialakítva, hogy használható csak a nem blokkoló Netty i/o-munka. Az SDK-t adja vissza, az alkalmazás az API-hívási eredmény egy Netty i/o-szál. Ha az alkalmazás tartós műveletet hajt végre, a Netty szálon eredmények fogadása után, az SDK nem feltétlenül elég i/o-szál belső i/o-feladatának teljesítéséhez. Ilyen alkalmazás kódolási azt eredményezheti, hogy alacsony átviteli sebesség, a magas késleltetés és `io.netty.handler.timeout.ReadTimeoutException` hibák. A megoldás, hogy váltson a hozzászólásláncot, ha tudja, hogy a művelet időt vesz igénybe.

Például tekintse meg a következő kódrészletet. Előfordulhat, hogy végezhet tartós munka, amely több mint néhány ezredmásodperc alatt a Netty szálon. Ha igen, végül kaphat olyan állapotba, ahol nincs Netty i/o-szál nem található az i/o-feldolgozás. Ennek eredményeképpen ReadTimeoutException hibát kap.
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
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
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
   A megoldás, hogy módosítsa a hozzászólásláncot, amelyre Ön hajtanak végre munkát, amely időt vesz igénybe. A Scheduler az alkalmazás egy singleton-példány meghatározása.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Szüksége lehet, hogy idő szükséges idő, például nagy számítási (nagy erőforrásigényű) munkahelyi vagy blokkoló IO működnek. Ebben az esetben a szál váltson egy feldolgozó által biztosított a `customScheduler` használatával a `.observeOn(customScheduler)` API-t.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Használatával `observeOn(customScheduler)`, a Netty i/o-szál kiadás, és váltson át a saját egyéni szálat az egyéni ütemező által biztosított. Ez a módosítás megoldja a problémát. Nem kap egy `io.netty.handler.timeout.ReadTimeoutException` többé hiba.

### <a name="connection-pool-exhausted-issue"></a>Kapcsolati probléma kimerült

`PoolExhaustedException` ügyféloldali hiba történik. Ez a hiba azt jelzi, hogy az alkalmazás alkalmazások és szolgáltatások magasabb, mint az SDK-kapcsolatkészletben szolgálhat. Növelje a kapcsolódási készlet méretét, vagy a terhelés több alkalmazásra.

### <a name="request-rate-too-large"></a>Kérelmek száma túl nagy
Ez a hiba a kiszolgálóoldali hiba történik. Azt jelzi, hogy a a kiosztott átviteli sebesség felhasznált. Próbálkozzon újra később. Ha a hiba gyakran kap, fontolja meg a gyűjtemény átviteli sebesség növekedését.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Nem sikerült kapcsolódni az Azure Cosmos DB emulator

Az Azure Cosmos DB emulator HTTPS-tanúsítvány önaláírt. Az SDK az emulátorral működjön az emulátor tanúsítvány importálása egy Java TrustStore. További információkért lásd: [emulátortanúsítványok exportálása az Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Függőségi ütközés kapcsolatos problémák

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

A fenti kivétel javasol függőség egy régebbi verziója RxJava lib (pl. 1.2.2) rendelkezik. Az SDK támaszkodik RxJava 1.3.8, amely rendelkezik az API-k RxJava korábbi verzióiban nem érhető el. 

Az ilyen issuses, hogy mely más függőségek azonosításához az RxJava-1.2.2 biztosítható a megkerülő megoldás, és kizárja a RxJava-1.2.2 tranzitív függőség, és engedélyezze a cosmos DB SDK használata az újabb verzióra.

Azonosíthatja, hogy melyik könyvtár elérhetővé teszi az RxJava 1.2.2 mellett a projekt pom.xml fájlt a következő parancs futtatásával:
```bash
mvn dependency:tree
```
További információkért lásd: a [maven-függőségi fa útmutató](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Ha azonosította a RxJava-1.2.2 tranzitív függőség mely más függőség a projekt, módosíthatja a függőséget a lib pom-fájljába és kizárási RxJava tranzitív függőség a következő:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

További információkért lásd: a [tranzitív függőségi útmutató kizárása](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sice-logging"></a>Ügyfél-SDK naplózásának engedélyezése

A naplózás a adapterréteget a Java SDK-val aszinkron használja SLF4j támogató elterjedt naplózási keretrendszer például log4j, a logback bejelentkezik.

Például ha azt szeretné, log4j használandó naplózási keretrendszer, adja hozzá a következő libs az Java osztályútvonalába.

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

A log4j config is hozzáadhat.
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

További információkért lásd: a [sfl4j naplózási manuális](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Az operációs rendszer hálózati statisztika
Megtapasztalhatja, létesített kapcsolatok száma is Államokban például a netstat parancs futtatásával `ESTABLISHED` és `CLOSE_WAIT`.

A Linux a következő parancsot futtathatja.
```bash
netstat -nap
```
A csak az Azure Cosmos DB végpont kapcsolatokat, az eredmények szűréséhez.

Az Azure Cosmos DB-végpontra a kapcsolatok száma a `ESTABLISHED` állapota nem lehet nagyobb, mint a konfigurált kapcsolati készlet méretét.

Lehet, az Azure Cosmos DB végpont létesített kapcsolatok száma a `CLOSE_WAIT` állapota. Előfordulhat, hogy több mint 1000. Egy szám, nagy azt jelzi, hogy a kapcsolat létrejött és gyorsan bontva. Ez a helyzet lehetséges problémákat okoz. További információkért lásd: a [gyakori problémák és megoldások] szakaszban.

 <!--Anchors-->
[Gyakori problémák és megoldások]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Egy gazdagépen a kapcsolathoz megadott korlátot]: #connection-limit-on-host
[Az Azure SNAT (PAT) portfogyás]: #snat


