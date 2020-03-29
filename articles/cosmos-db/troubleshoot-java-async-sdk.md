---
title: Az Azure Cosmos DB Java Async SDK diagnosztizálása és hibaelhárítása
description: Az Azure Cosmos DB-vel kapcsolatos problémák azonosításához, diagnosztizálásához és elhárításához olyan funkciókat használhat, mint az ügyféloldali naplózás és más külső eszközök.
author: moderakh
ms.service: cosmos-db
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 572139743c66546622450cef8f8a0fa264d24779
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65519982"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>A Java Async SDK Azure Cosmos DB SQL API-fiókokkal való használatakor felmerülő hibák elhárítása
Ez a cikk a [Java Async SDK](sql-api-sdk-async-java.md) Azure Cosmos DB SQL API-fiókokkal való használatakor ismerteti a gyakori problémákat, a kerülő megoldásokat, a diagnosztikai lépéseket és az eszközöket.
A Java Async SDK ügyféloldali logikai leképezést biztosít az Azure Cosmos DB SQL API eléréséhez. Ez a cikk azokat az eszközöket és módszereket ismerteti, amelyek segítenek megoldani a problémákat.

Kezdje ezzel a listával:

* Tekintse meg a cikk [Gyakori problémák és kerülő megoldások] című szakaszát.
* Nézd meg az SDK-t, amely nyílt forráskódú a [GitHubon.](https://github.com/Azure/azure-cosmosdb-java) Van egy [probléma szakasz,](https://github.com/Azure/azure-cosmosdb-java/issues) amely aktívan figyeli. Ellenőrizze, hogy van-e már benyújtani hasonló probléma a kerülő megoldással kapcsolatban.
* Tekintse át a [teljesítményre vonatkozó tippeket,](performance-tips-async-java.md)és kövesse a javasolt eljárásokat.
* Olvassa el a cikk többi részét, ha nem talált megoldást. Ezután fájl [egy GitHub probléma](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Gyakori hibák és áthidaló megoldásaik

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Hálózati problémák, Netty olvasási időtúltöltési hiba, alacsony átviteli képesség, nagy késleltetés

#### <a name="general-suggestions"></a>Általános javaslatok
* Győződjön meg arról, hogy az alkalmazás ugyanazon a régión fut, mint az Azure Cosmos DB-fiók. 
* Ellenőrizze a CPU-használat a gazdagép, ahol az alkalmazás fut. Ha a processzorhasználat 90 százalék vagy annál nagyobb, futtassa az alkalmazást egy magasabb konfigurációval rendelkező gazdagépen. Vagy több gépen is terjesztheti a terhelést.

#### <a name="connection-throttling"></a>Kapcsolat szabályozása
A kapcsolat szabályozása a [gazdagép] en vagy az [Azure SNAT (PAT) port kimerülése]miatt fordulhat elő.

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Kapcsolati korlát a gazdagépen
Egyes Linux rendszerek, például a Red Hat, a megnyitott fájlok teljes számára vonatkozó felső korláttal rendelkeznek. A Linux szoftvercsatornái fájlokként vannak megvalósítva, így ez a szám korlátozza a kapcsolatok teljes számát is.
Futtassa a következő parancsot.

```bash
ulimit -a
```
A "nofile" néven azonosított maximálisan engedélyezett nyitott fájlok számának legalább a kapcsolatkészlet méretének kétszeresének kell lennie. További információt a [Teljesítménytippek című témakörben talál.](performance-tips-async-java.md)

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Az Azure SNAT (PAT) port kimerülése

Ha az alkalmazás nyilvános IP-cím nélküli Azure virtuális gépeken van telepítve, alapértelmezés szerint [az Azure SNAT-portok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) a virtuális gépen kívüli bármely végponthoz létesítenek kapcsolatokat. A virtuális gép és az Azure Cosmos DB-végpont között engedélyezett kapcsolatok számát az [Azure SNAT-konfiguráció](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)korlátozza.

 Az Azure SNAT-portok csak akkor használatosak, ha a virtuális gép privát IP-címmel rendelkezik, és a virtuális gép egy folyamat próbál csatlakozni egy nyilvános IP-címet. Az Azure SNAT-korlátozás elkerülése érdekében két kerülő megoldás létezik:

* Adja hozzá az Azure Cosmos DB szolgáltatásvégpontját az Azure virtuális gépek virtuális hálózatának alhálózatához. További információ: [Azure Virtual Network service endpoints](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Ha a szolgáltatás végpontja engedélyezve van, a kérelmek et már nem küldi el a nyilvános IP-cím ről az Azure Cosmos DB. Ehelyett a virtuális hálózat és az alhálózati identitás küldése. Ez a módosítás tűzfal-leejtést eredményezhet, ha csak nyilvános IP-k engedélyezettek. Ha tűzfalat használ, a szolgáltatásvégpont engedélyezésekor vegyen fel egy alhálózatot a tűzfalhoz a [virtuális hálózati abeil-ek](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl)használatával.
* Rendeljen hozzá egy nyilvános IP-címet az Azure-beli virtuális géphez.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Nem érhető el a szolgáltatás - tűzfal
``ConnectTimeoutException``azt jelzi, hogy az SDK nem tudja elérni a szolgáltatást.
A közvetlen mód használatakor az alábbihoz hasonló hiba léphet fel:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Ha az alkalmazásgépen tűzfal fut, nyissa meg a 10 000 és 20 000 közötti porttartományt, amelyet a közvetlen mód használ.
Kövesse a [gazdagép kapcsolati korlátját](#connection-limit-on-host)is .

#### <a name="http-proxy"></a>HTTP-proxy

Http-proxy használata esetén győződjön meg arról, hogy az támogatja `ConnectionPolicy`az SDK-ban konfigurált kapcsolatok számát.
Ellenkező esetben csatlakozási problémákkal kell szembenéznie.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Érvénytelen kódolási minta: A Netty IO-szál blokkolása

Az SDK a [Netty](https://netty.io/) I/o-kódtár használatával kommunikál az Azure Cosmos DB-vel. Az SDK Async API-kkal rendelkezik, és a Netty nem blokkoló IO API-jait használja. Az SDK IO-munkája IO Netty szálakon történik. Az IO Netty-szálak száma úgy van beállítva, hogy megegyezik az alkalmazásgép PROCESSZORmagjainak számával. 

A Netty IO-szálak csak nem blokkoló Netty IO-munkákhoz használhatók. Az SDK visszaadja az API-meghívási eredményt az egyik Netty IO-szálon az alkalmazás kódjára. Ha az alkalmazás hosszú távú műveletet hajt végre, miután eredményeket kap a Netty szálon, az SDK nem rendelkezik elegendő IO-szálakkal a belső IO-munka elvégzéséhez. Az ilyen alkalmazáskódolás alacsony átviteli, nagy késést és `io.netty.handler.timeout.ReadTimeoutException` hibákat eredményezhet. A megoldás az, hogy váltani a szál, ha tudja, hogy a művelet időt vesz igénybe.

Tekintse meg például a következő kódrészletet. Előfordulhat, hogy a Netty szálon néhány ezredmásodpercnél hosszabb ideig tartó munkát végez. Ha igen, akkor végül olyan állapotba léphet, amelyben nincs Netty IO-szál az IO-munka feldolgozásához. Ennek eredményeképpen readtimeoutexception hibát kap.
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
   A megoldás az, hogy módosítja a szálat, amelyen a munkát, hogy időt vesz igénybe. Adja meg az alkalmazás ütemezőjének egypéldányát.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Előfordulhat, hogy olyan munkát kell végeznie, amely időt vesz igénybe, például számításilag nehéz munkát végez, vagy blokkolja az IO-t. Ebben az esetben kapcsolja be a szálat egy, az API használatával az `customScheduler` `.observeOn(customScheduler)` Ön által biztosított dolgozóra.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
A `observeOn(customScheduler)`használatával felszabadítja a Netty IO-szálat, és átvált az egyéni ütemező által biztosított saját egyéni szálra. Ez a módosítás megoldja a problémát. Többé nem lesz `io.netty.handler.timeout.ReadTimeoutException` csődtömeg.

### <a name="connection-pool-exhausted-issue"></a>A kapcsolatkészlet kimerült problémája

`PoolExhaustedException`ügyféloldali hiba. Ez a hiba azt jelzi, hogy az alkalmazás munkaterhelése magasabb, mint amit az SDK-kapcsolatkészlet szolgálhat. Növelje a kapcsolatkészlet méretét, vagy ossza el a terhelést több alkalmazáson.

### <a name="request-rate-too-large"></a>A kérelem aránya túl nagy
Ez a hiba kiszolgálóoldali hiba. Azt jelzi, hogy felhasználta a kiosztott átviteli. Próbálkozzon később. Ha gyakran kap ilyen hibát, fontolja meg a gyűjtemény átviteli sorának növelését.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Hiba az Azure Cosmos DB emulátorhoz való csatlakozáskor

Az Azure Cosmos DB emulátor HTTPS-tanúsítvány önaláírt. Ahhoz, hogy az SDK együttműködjön az emulátorral, importálja az emulátor tanúsítványt egy Java TrustStore-ba. További információ: [Export Azure Cosmos DB emulátor tanúsítványok](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Függőségi problémák

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

A fenti kivétel azt sugallja, hogy az RxJava lib egy régebbi verziójától függ (pl. 1.2.2). Az SDK az RxJava 1.3.8-ra támaszkodik, amely api-kat nem érhető el az RxJava korábbi verziójában. 

Az ilyen issuses megoldásaz, hogy meghatározza, hogy mely más függőség hozza az RxJava-1.2.2-t, és kizárja az RxJava-1.2.2 tranzitív függőségét, és lehetővé teszi a CosmosDB SDK számára az újabb verzió t.

Annak megállapításához, hogy melyik tár hozza be az RxJava-1.2.2-t, futtassa a következő parancsot a projekt pom.xml fájlja mellett:
```bash
mvn dependency:tree
```
További információt a [maven függőségi fa útmutatóban talál.](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)

Miután azonosította az RxJava-1.2.2-t, tranzitív függősége, amelytől a projekt egyéb függősége, módosíthatja a rendszerfájlban lévő lib függőséget, és kizárhatja az RxJava tranzitív függőséget:

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

További információt a [tranzitív függőségi útmutató kizárása című](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)témakörben talál.


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Ügyfél SDK-naplózásának engedélyezése

A Java Async SDK az SLF4j naplózási homlokzatot használja, amely támogatja a népszerű naplózási keretrendszerekbe, például a log4j-be és a logback-be való bejelentkezést.

Ha például a log4j naplózási keretrendszerként való használatát szeretné használni, adja hozzá a következő libeket a Java-osztályhoz.

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

Is hozzá egy log4j config.
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

További információ: [sfl4j logging manual](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Operációs rendszer hálózati statisztikái
Futtassa a netstat parancsot, hogy megtudja, `ESTABLISHED` `CLOSE_WAIT`hány kapcsolat van az olyan állapotokban, mint a és a .

Linuxon futtathatja a következő parancsot.
```bash
netstat -nap
```
Az eredményt csak az Azure Cosmos DB-végponthoz való kapcsolatokra szűrje.

Az Azure Cosmos DB-végponthoz való `ESTABLISHED` kapcsolatok száma az állapot ban nem lehet nagyobb, mint a beállított kapcsolatkészlet mérete.

Az Azure Cosmos DB-végpont számos kapcsolata lehet az `CLOSE_WAIT` állapot. Több lehet, mint 1000. Egy magas szám azt jelzi, hogy a kapcsolatok létrejönnek és gyorsan lebontódnak. Ez a helyzet problémákat okozhat. További információt a [Gyakori problémák és kerülő megoldások] című szakaszban talál.

 <!--Anchors-->
[Gyakori hibák és áthidaló megoldásaik]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Kapcsolati korlát a gazdagépen]: #connection-limit-on-host
[Az Azure SNAT (PAT) port kimerülése]: #snat


