---
title: Az Azure Cosmos DB teljesítménytippjei az Async Java-hoz
description: Az Azure Cosmos adatbázisteljesítményének javításához ügyfélkonfigurációs lehetőségek megismerése
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 89df941eb6ebaad6e078c278f1ed883db5528c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152557"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Az Azure Cosmos DB és az Async Java teljesítményével kapcsolatos tippek

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Az Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen skálázható a garantált késéssel és átviteli kapcsolattal. Nem kell jelentős architektúra-módosításokat végrehajtania, vagy összetett kódot írnia az adatbázis azure Cosmos DB-vel való méretezéséhez. Fel- és leskálázás a könnyű, mint egyetlen API-hívás vagy SDK metódus hívás. Mivel azonban az Azure Cosmos DB hálózati hívásokon keresztül érhető el, ügyféloldali optimalizálásokat tehet lehetővé a csúcsteljesítmény elérése érdekében az [SQL Async Java SDK](sql-api-sdk-async-java.md)használatakor.

Ha azt kérdezi: "Hogyan javíthatom az adatbázis teljesítményét?" fontolja meg a következő lehetőségeket:

## <a name="networking"></a>Hálózat

* **Csatlakozási mód: Közvetlen mód használata**
<a id="direct-connection"></a>
    
    Hogyan csatlakozik egy ügyfél az Azure Cosmos DB-hez fontos hatással van a teljesítményre, különösen az ügyféloldali késés szempontjából. A *ConnectionMode* egy kulcskonfigurációs beállítás, amely az ügyfél *ConnectionPolicy*konfigurálására érhető el. Az Async Java SDK esetében a két elérhető ConnectionMode a következő:  
      
    * [Átjáró (alapértelmezett)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Az átjáró mód minden SDK platformon támogatott, és alapértelmezés szerint ez a beállított beállítás. Ha az alkalmazások szigorú tűzfalkorlátozásokkal rendelkező vállalati hálózaton futnak, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használja. A teljesítmény kompromisszumot, azonban az, hogy a Gateway mód egy további hálózati ugrás minden alkalommal, amikor az adatok olvasása vagy írása az Azure Cosmos DB. Emiatt a közvetlen mód jobb teljesítményt nyújt a kevesebb hálózati ugrás nak köszönhetően.

    A *ConnectionMode* a *DocumentClient* példány létrehozása során van konfigurálva a *ConnectionPolicy* paraméterrel.
    
    ```java
        public ConnectionPolicy getConnectionPolicy() {
          ConnectionPolicy policy = new ConnectionPolicy();
          policy.setConnectionMode(ConnectionMode.Direct);
          policy.setMaxPoolSize(1000);
          return policy;
        }

        ConnectionPolicy connectionPolicy = new ConnectionPolicy();
        DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

* **Az ügyfeleket ugyanabban az Azure-régióban helyezze el a jobb teljesítmény érdekében** <a id="same-region"></a>

    Ha lehetséges, helyezzen el az Azure Cosmos DB-t hívó alkalmazásokat ugyanabban a régióban, mint az Azure Cosmos-adatbázist. A hozzávetőleges összehasonlítás érdekében az Azure Cosmos DB-hez való hívások ugyanabban a régióban 1–2 ms-on belül befejeződnek, de az USA nyugati és keleti partja közötti késés 50 ms >. Ez a késés valószínűleg kérésről kérelemre változhat a kérelem által megtett útvonaltól függően, ahogy az ügyfélről az Azure adatközpont határára kerül. A lehető legalacsonyabb késés érhető el annak biztosításával, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB-végpont. Az elérhető régiók listáját az [Azure Regions (Azure Regions) (Azure Regions) (Azure Regions) (Azure Regions) (Azure Regions) (Azure Regions) (Az Azure-](https://azure.microsoft.com/regions/#services)

    ![Az Azure Cosmos DB kapcsolati szabályzatának illusztrációja](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK-használat
* **A legújabb SDK telepítése**

    Az Azure Cosmos DB SDK-k folyamatosan fejlesztik, hogy a legjobb teljesítményt. Tekintse meg az [Azure Cosmos DB SDK-lapok](sql-api-sdk-async-java.md) a legújabb SDK meghatározásához és tekintse át a fejlesztéseket.

* **Egyetlen Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama alatt**

    Minden AsyncDocumentClient példány szálbiztos, és hatékony kapcsolatkezelést és címgyorsítótárazást hajt végre. Az AsyncDocumentClient hatékony kapcsolatkezelésének és jobb teljesítményének lehetővé tétele érdekében ajánlott az Alkalmazás teljes élettartama alatt apptartományonként az AsyncDocumentClient egyetlen példányát használni.

   <a id="max-connection"></a>

* **A Kapcsolatházirend finomhangolása**

    Alapértelmezés szerint a Közvetlen módcosmos DB kérelmek tcp-n keresztül történik az Async Java SDK használataesetén. Belsőleg az SDK egy speciális közvetlen módú architektúrát használ a hálózati erőforrások dinamikus kezelésére és a legjobb teljesítmény re.

    Az Async Java SDK-ban a közvetlen mód a legjobb választás az adatbázis teljesítményének javítására a legtöbb munkaterheléssel. 

    * ***A közvetlen mód áttekintése***

        ![A közvetlen mód architektúrájának illusztrációja](./media/performance-tips-async-java/rntbdtransportclient.png)

        A közvetlen módban alkalmazott ügyféloldali architektúra kiszámítható hálózati kihasználtságot és az Azure Cosmos DB replikák hoz való multiplexelést teszi lehetővé. A fenti ábrán látható, hogyan irányítja a Közvetlen mód az ügyfélkérelmeket a Cosmos DB-háttérrendszer replikáihoz. A közvetlen módú architektúra db-replikánként legfeljebb 10 **csatornát** foglal le az ügyféloldalon. A csatorna egy TCP-kapcsolat, amelyet egy kérelempuffer előz meg, amely 30 kérelem mély. A kópiához tartozó csatornák dinamikusan vannak lefoglalva a kópia **Szolgáltatásvégpontja**által szükség szerint. Amikor a felhasználó közvetlen módban ad ki kérelmet, a **TransportClient** a partíciókulcs alapján a megfelelő szolgáltatásvégpontra irányítja a kérelmet. A **kérelemvárólista** puffereli a kérelmeket a szolgáltatásvégpont előtt.

    * ***A ConnectionPolicy konfigurációs beállításai közvetlen módhoz***

        Első lépésként használja az alábbi ajánlott konfigurációs beállításokat. Lépjen kapcsolatba az [Azure Cosmos DB csapatával,](mailto:CosmosDBPerformanceSupport@service.microsoft.com) ha ebben a témában problémákba ütközik.

        Ha az Azure Cosmos DB-t referencia-adatbázisként használja (azaz az adatbázist számos pontolvasási művelethez és néhány írási művelethez használja), elfogadható lehet az *idleEndpointTimeout* beállítása 0-ra (azaz nincs időtúltöltés).


        | Konfigurációs beállítás       | Alapértelmezett    |
        | :------------------:       | :-----:    |
        | ütközikPageSize             | 8192       |
        | connectionTimeout kapcsolat          | "PT1M"     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | "PT5S"     |
        | requestTimeout             | "PT1M"     |
        | requestTimerResolution (időzítő feloldása)     | "PT0.5s"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | "PT15S"    |

    * ***Programozási tippek közvetlen módhoz***

        Tekintse át az Azure Cosmos DB [Async Java SDK hibaelhárítási](troubleshoot-java-async-sdk.md) cikket alapkonfigurációként az Async Java SDK-problémák megoldásához.

        Néhány fontos programozási tipp a Közvetlen mód használatakor:

        + **Használja a többszálas adattovábbítást az alkalmazásban a hatékony TCP adatátvitelérdekében** – A kérelem kérése után az alkalmazásnak elő kell fizetnie, hogy adatokat fogadjon egy másik szálon. Ha ezt nem teszi meg, a nem kívánt "félduplex" műveletet kényszeríti, és az ezt követő kérelmek blokkolva vannak az előző kérés válaszára várva.

        + **Számítási nagy munkaterhelések elvégzése egy dedikált szálon** – Az előző csúcshoz hasonló okok miatt az olyan műveletek, mint például az összetett adatfeldolgozás, a legjobban egy külön szálban találhatók. Egy olyan kérelem, amely adatokat kér le egy másik adattárból (például ha a szál egyszerre használja az Azure Cosmos DB- és Spark-adattárakat) megnövekedett késést tapasztalhat, és ajánlott egy további szálat létrehozni, amely a másik tól érkező választ várja. adattárban.

            + Az Async Java SDK-ban az alapul szolgáló hálózati IO-t a Netty kezeli, lásd az alábbi [tippeket a Netty IO-szálakat blokkoló kódolási minták elkerüléséhez.](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread)

        + **Adatmodellezés** – Az Azure Cosmos DB SLA feltételezi, hogy a dokumentum mérete kevesebb, mint 1 KB. Az adatmodell optimalizálása és a kisebb dokumentumméretre való programozás általában a késés csökkenéséhez vezet. Ha 1 KB-nál nagyobb dokumentumok tárolására és lekérésére van szüksége, az ajánlott módszer az, hogy a dokumentumok az Azure Blob Storage-ban lévő adatokhoz kapcsolódjanak.


* **Particionált lekérdezések finomhangolása particionált gyűjtemények**

    Az Azure Cosmos DB SQL Async Java SDK támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemény párhuzamos lekérdezését. További információ: Az SDK-kkal való munkához kapcsolódó [kódminták.](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) A párhuzamos lekérdezések célja, hogy javítsa a lekérdezés késését és átviteli képességét a soros megfelelője felett.

    * ***Tuning setMaxDegreeOfParallelism\:***
    
        A párhuzamos lekérdezések több partíció párhuzamos lekérdezésével működnek. Az egyes particionált gyűjteményből származó adatok azonban a lekérdezéshez képest sorozatosan lekérésre kerül. Így használja setMaxDegreeOfParallelism a partíciók számának beállításához, amely a legnagyobb eséllyel éri el a legtöbb teljesítményű lekérdezés, feltéve, hogy az összes többi rendszerfeltételek változatlanok maradnak. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism segítségével nagy számot állíthat be, és a rendszer a minimális (partíciók száma, felhasználói bevitel) a maximális párhuzamosság mértékét választja.

        Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyökkel jár, ha az adatok egyenletesen oszlanak el az összes partíciót a lekérdezés tekintetében. Ha a particionált gyűjtemény particionált oly módon, hogy az összes vagy a lekérdezés által visszaadott adatok többsége koncentrálódik néhány partíciót (egy partíció a legrosszabb esetben), majd a lekérdezés teljesítményét lenne szűk keresztmetszetű az adott partíciókat.

    * ***Hangolás készletMaxBufferedItemCount\:***
    
        A párhuzamos lekérdezés úgy van kialakítva, hogy az eredmények et előre lehívása közben az aktuális eredményköteget az ügyfél dolgozza fel. Az előzetes lekérés segít a lekérdezés általános késésének javításában. A setMaxBufferedItemCount korlátozza az előre lekért eredmények számát. Ha a setMaxBufferedItemCount értéket a várt számú eredményre (vagy nagyobb számra) állítja be, a lekérdezés maximális hasznot húzhat az előolvasásból.

        Az előolvasás ugyanúgy működik, függetlenül a MaxDegreeOfParallelism-től, és az összes partícióadataihoz egyetlen puffer van.

* **Visszalépés megvalósítása getRetryAfterInMilliseconds időközönként**

    A teljesítménytesztelés során növelnie kell a terhelést, amíg a kérelmek kis mértékű szabályozása meg nem szabályozva lesz. Ha szabályozott, az ügyfélalkalmazás nak ki kell kapcsolnia a kiszolgáló által megadott újrapróbálkozási időközt. A visszalépés tiszteletben tartása biztosítja, hogy minimális időt töltsön az újrapróbálkozások közötti várakozással.

* **Az ügyfél-munkaterhelés horizontális felskálázása**

    Ha nagy átviteli sebességű (>50 000 RU/s) sebességgel tesztel, az ügyfélalkalmazás szűk keresztmetszetté válhat a processzor vagy a hálózati kihasználtság miatt. Ha eléri ezt a pontot, továbbra is leküldéses az Azure Cosmos DB-fiók további horizontális felskálázása az ügyfélalkalmazások több kiszolgálók között.

* **Névalapú címzés használata**

    Használjon névalapú címzést, `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`ahol a hivatkozások\_formátuma a SelfLinks (self) helyett található, amelyek formátuma `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` elkerülhető a hivatkozás létrehozásához használt összes erőforrás ResourceIds beolvasása. Is, mivel ezek az erőforrások kap újra (esetleg azonos nevű), cache őket nem segít.

   <a id="tune-page-size"></a>

* **A jobb teljesítmény érdekében a lekérdezések/etetők olvasása oldalméretének finomhangolása**

    Ha olvasási hírcsatorna-funkcióval (például readDocuments) vagy SQL-lekérdezés kiadásakor tömegesen olvassa be a dokumentumokat, az eredmények szegmentált módon kerülnek visszaadásra, ha az eredményhalmaz túl nagy. Alapértelmezés szerint az eredmények 100 elemből vagy 1 MB-os adattömbökből adják vissza az eredményeket, attól függően, hogy melyik korlát ot érinti először.

    Az összes vonatkozó eredmény lekéréséhez szükséges hálózati átutazások számának csökkentése érdekében az [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) kérésfejléc használatával akár 1000-re is növelheti az oldalméretet. Azokban az esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az alkalmazás API-ja csak 10 eredményt ad vissza egy időben, az oldalméretet is csökkentheti 10-re az olvasások és lekérdezések során felhasznált átviteli érték csökkentése érdekében.

    Az oldalméretet a setMaxItemCount metódussal is beállíthatja.

* **Használja a megfelelő ütemezőt (Ne lopjon eseményhurok IO Netty szálakat)**

    Az Async Java SDK [netty-t](https://netty.io/) használ a nem blokkoló IO-hoz. Az SDK egy meghatározott számú IO netty eseményhurok-szálat használ (annyi CPU-mag, amivel a gép rendelkezik) az IO-műveletek végrehajtásához. Az API által visszaadott megfigyelhető az eredményt a megosztott IO-eseményhurok netty szálak. Ezért fontos, hogy ne blokkolja a megosztott IO esemény hurok netty szálak. Cpu-igényes munka vagy blokkolási művelet az IO eseményhurok netty szál okozhat holtpontra jutott, vagy jelentősen csökkenti az SDK átviteli sebességét.

    Például a következő kód egy cpu-igényes munkát hajt végre az eseményhurok IO netty szálon:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Miután az eredmény érkezik, ha azt szeretné, hogy a CPU-intenzív munkát az eredmény el kell kerülni ezt az esemény hurok IO netty szál. Ehelyett saját ütemezőt is biztosíthat, hogy saját szálat biztosítson a munka futtatásához.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    A munka típusától függően a megfelelő meglévő RxJava ütemező t kell használnia a munkájához. Olvassa [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)el itt .

    További információkért tekintse meg az Async Java SDK [GitHub-oldalát.](https://github.com/Azure/azure-cosmosdb-java)

* **Netty naplózásának letiltása**

    Netty könyvtár naplózás a beszédes, és ki kell kapcsolni (letiltása jel a konfiguráció nem lehet elég), hogy elkerüljék a további CPU költségeket. Ha nem hibakeresési módban van, tiltsa le a netty naplózását. Tehát, ha a log4j segítségével eltávolítja a ``org.apache.log4j.Category.callAppenders()`` netty-ből származó további CPU-költségeket, adja hozzá a következő sort a kódbázishoz:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **OS Megnyitott fájlok Erőforrás-korlát**
 
    Egyes Linux rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok számára, és így a kapcsolatok teljes számára. Futtassa az alábbi futtatást az aktuális korlátok megtekintéséhez:

    ```bash
    ulimit -a
    ```

    A megnyitott fájlok (nofile) számának elég nagynak kell lennie ahhoz, hogy elegendő hely legyen a beállított kapcsolatkészlet méretéhez és az operációs rendszer által megnyitott fájlokhoz. Módosítható, hogy nagyobb kapcsolatkészletméretet engedélyezze.

    Nyissa meg a limits.conf fájlt:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Adja hozzá/módosítsa a következő sorokat:

    ```
    * - nofile 100000
    ```

* **Natív SSL-implementáció használata a netty-hez**

    A Netty közvetlenül használhatja az OpenSSL-t az SSL implementációs veremhez a jobb teljesítmény elérése érdekében. Hiányában ez a konfiguráció netty esik vissza a Java alapértelmezett SSL megvalósítása.

    Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    és adja hozzá a következő függőséget a projekt maven függőségeihez:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Más platformok (Red Hat, Windows, Mac stb.) esetében olvassa el ezeket az utasításokathttps://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexelési szabályzat
 
* **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Az Azure Cosmos DB indexelési szabályzata lehetővé teszi, hogy meghatározza, hogy mely dokumentumelérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési elérési utak (setIncludedPaths és setExcludedPaths) használatával. Az indexelési útvonalak használata jobb írási teljesítményt és alacsonyabb indextárolási adatokat kínálhat olyan esetekben, amelyekben a lekérdezési minták előre ismertek, mivel az indexelési költségek közvetlenül korrelálnak az indexelt egyedi elérési utak számával. A következő kód például bemutatja, hogyan zárhatja ki a dokumentumok egy teljes szakaszát (más néven részfát) az indexelésből a "*" helyettesítő karakter használatával.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információ: [Azure Cosmos DB indexelési szabályzatok.](indexing-policies.md)

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

* **Kisebb kérési egységek/másodperchasználat mérése és hangolása**

    Az Azure Cosmos DB adatbázis-műveletek széles körű készletét kínálja, beleértve az UDF-ekkel, tárolt eljárásokkal és eseményindítókkal kapcsolatos relációs és hierarchikus lekérdezéseket – az adatbázis-gyűjteményben lévő dokumentumokon működő összes. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardvererőforrások átgondolása és kezelése helyett a kérelemegység (RU) egyetlen mértékként is elgondolkodhat a különböző adatbázis-műveletek végrehajtásához és az alkalmazáskérelmek kiszolgálásához szükséges erőforrásokhoz.

    Átviteli átviteli van kiépítve az egyes [tárolókhoz](request-units.md) beállított kérelemegységek száma alapján. A kérelem egységfelhasználása másodpercenkénti díjként lesz kiértékelve. Alkalmazások, amelyek meghaladják a kiosztott kérelem egységdíj a tároló korlátozott, amíg a sebesség alá csökken a kiosztott szint a tároló. Ha az alkalmazás magasabb átviteli szintet igényel, növelheti az átviteli kapacitást további kérelemegységek kiépítésével.

    A lekérdezés összetettsége hatással van arra, hogy hány kérelemegység et használ fel egy művelethez. A predikátumok száma, a predikátumok jellege, az UDF-ek száma és a forrásadatkészlet mérete mind befolyásolják a lekérdezési műveletek költségét.

    Bármely művelet (létrehozás, frissítés vagy törlés) többletterhelésének méréséhez vizsgálja meg az [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet az ilyen műveletek által felhasznált kérelemegységek számának mérésére. A ResourceResponse T> vagy a\<FeedResponse\<T> megfelelő RequestCharge tulajdonságát is megnézheti.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    A fejlécben visszaadott kérelemdíj a kiosztott átviteli igény töredéke. Ha például 2000 RU/s kivan építve, és az előző lekérdezés 1000 1 KB-os bizonylatokat ad vissza, a művelet költsége 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést tart tiszteletben, mielőtt korlátozza a későbbi kérelmek et. További információ: [Egységek kérése](request-units.md) és a [kérelemegység-kalkulátor.](https://www.documentdb.com/capacityplanner)

<a id="429"></a>
* **A sebességkorlátozás/kérelem sebességének lehívása túl nagy**

    Ha egy ügyfél megpróbálja túllépni egy fiók fenntartott átviteli teljesítményét, a kiszolgálón nincs teljesítménycsökkenés, és a fenntartott szinten túl nincs átviteli kapacitás. A kiszolgáló a requestet megelőzően a RequestRateTooLarge (429-es HTTP-állapotkód) segítségével befejezi, és visszaadja az [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet, amely azt az időt jelzi , ezredmásodpercben, amíg a felhasználónak várnia kell a kérelem újbóli megkísérlése előtt.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k implicit módon elkapják ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási fejlécet, és újrapróbálkoznak a kéréssel. Ha csak akkor éri el fiókját, hogy több ügyfél egyidejűleg fér hozzá, a következő újrapróbálkozás sikeres lesz.

    Ha egynél több ügyfél összesítve működik következetesen meghaladja a kérelem aránya, az alapértelmezett újrapróbálkozások száma jelenleg 9 belsőleg az ügyfél nem elegendő; ebben az esetben az ügyfél egy 429-es állapotkódú DocumentClientException-et dob az alkalmazásba. Az alapértelmezett újrapróbálkozások száma a ConnectionPolicy példány setRetryOptions parancsával módosítható. Alapértelmezés szerint a DocumentClientException állapotkód 429-es állapotkóddal 30 másodperces összesített várakozási idő után kerül vissza, ha a kérelem továbbra is a kérelemsebesség felett működik. Ez akkor is előfordul, ha az aktuális újrapróbálkozások száma kisebb, mint a maximális újrapróbálkozások száma, legyen az az alapértelmezett 9 vagy egy felhasználó által definiált érték.

    Bár az automatikus újrapróbálkozási viselkedés segít javítani a rugalmasságot és a használhatóságot a legtöbb alkalmazás számára, előfordulhat, hogy ellentétes a teljesítmény-referenciaértékek, különösen a késés mérése során. Az ügyfél által megfigyelt késés kiugrik, ha a kísérlet eléri a kiszolgáló szabályozási, és az ügyfél SDK-t némán újrapróbálkozik. A teljesítménykísérletek során a késés kiugrásának elkerülése érdekében mérje meg az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelemdíj alatt működnek. További információ: [Request units](request-units.md).

* **Tervezés kisebb dokumentumokhoz a nagyobb átaputlékért**

    A kérelem díj (a kérelem feldolgozási költség) egy adott művelet közvetlenül korrelál a dokumentum méretét. A nagy dokumentumokon végzett műveletek többe kerülnek, mint a kis dokumentumok műveletei.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű tervezéséről, olvassa el [a Particionálás és méretezés az Azure Cosmos DB-ben című témakört.](partition-data.md)
