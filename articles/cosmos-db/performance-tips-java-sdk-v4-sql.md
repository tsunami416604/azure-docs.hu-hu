---
title: Teljesítménnyel kapcsolatos tippek Azure Cosmos DB Java SDK v4-hez
description: Az ügyfél-konfigurációs beállítások megismerése az Azure Cosmos Database teljesítményének növeléséhez Java SDK v4 esetén
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982530"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Teljesítménnyel kapcsolatos tippek Azure Cosmos DB Java SDK v4-hez

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> A cikkben szereplő teljesítménnyel kapcsolatos tippek csak Azure Cosmos DB Java SDK v4-re vonatkoznak. További információkért tekintse meg a Azure Cosmos DB Java SDK v4 kibocsátási megjegyzéseit, a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos)és a Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatóját](troubleshoot-java-sdk-v4-sql.md) . Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás vagy egy SDK-metódus hívása. Mivel azonban a Azure Cosmos DB hálózati hívásokkal érhetők el, ügyféloldali optimalizálási lehetőségek érhetők el, ha Azure Cosmos DB Java SDK v4-t használ a maximális teljesítmény eléréséhez.

Tehát ha a "Hogyan javíthatom az adatbázis teljesítményét?" című témakört kérdezi le? vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózat

* **Csatlakoztatási mód: közvetlen mód használata**
<a id="direct-connection"></a>
    
    Az ügyfél Azure Cosmos DBhoz való csatlakozásának módja fontos hatással van a teljesítményre, különösen az ügyféloldali késések tekintetében. A *ConnectionMode* az ügyfél *ConnectionPolicy*konfigurálásához rendelkezésre álló kulcsfontosságú konfigurációs beállítás. Azure Cosmos DB Java SDK v4 esetén a két elérhető *ConnectionMode*-k a következők:  
      
    * [Átjáró (alapértelmezett)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Ezek a *ConnectionMode*-k lényegében feltétele azt az útvonalat, amelyet a kérések az ügyfélszámítógépről a Azure Cosmos db háttérbe tartozó partíciók számára igényelnek. A legjobb teljesítmény érdekében általában a közvetlen mód az előnyben részesített megoldás, amely lehetővé teszi, hogy az ügyfél közvetlenül a Azure Cosmos DB háttérbeli partíciókkal nyissa meg a TCP-kapcsolatokat, és küldje el a *Direct*ly-t, és ne legyen közvetítő. Ezzel szemben az átjáró módban az ügyfél által kért kérelmek átirányítva egy úgynevezett "átjáró" kiszolgálóra a Azure Cosmos DB előtér-kiszolgálón, amely a Azure Cosmos DB háttérbeli megfelelő partíció (k) re küldi a kéréseit. Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ. A teljesítmény-kompromisszum azonban az, hogy az átjáró mód egy további hálózati ugrást (ügyfél – átjáró és partíciós átjáró) is magában foglal minden alkalommal, amikor az összes adat beolvasása vagy írása Azure Cosmos DB. Emiatt a közvetlen mód jobb teljesítményt nyújt kevesebb hálózati ugrás miatt.

    A *ConnectionMode* az Azure Cosmos db-ügyfél példányának az *ConnectionPolicy* paraméterrel való létrehozásakor van konfigurálva:
    
   #### <a name="async"></a>[Aszinkron](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* **Az ügyfeleket ugyanabban az Azure-régióban helyezze el a jobb teljesítmény érdekében** <a id="same-region"></a>

    Ha lehetséges, helyezzen minden olyan alkalmazást, amely a Azure Cosmos DBt hívja meg ugyanabban a régióban, mint az Azure Cosmos Database. A hozzávetőleges összehasonlításhoz az azonos régióban lévő Azure Cosmos DB a 1-2 MS-on belül fejeződik be, de az USA nyugati és keleti partja közötti késés >50 MS. Ez a késés valószínűleg a kérelemtől függ attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure Datacenter-határig. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

    ![A Azure Cosmos DB-kapcsolatok házirendjének ábrája](./media/performance-tips/same-region.png)

    Egy többrégiós Azure Cosmos DB fiókkal kommunikáló alkalmazásnak az [előnyben részesített helyeket]() kell konfigurálnia, hogy a kérések közös elhelyezésű régióba lépjenek.

* **Az alacsonyabb késés érdekében engedélyezze a gyorsított hálózatkezelést az Azure-beli virtuális gépen.**

Azt javasoljuk, hogy a teljesítmény maximalizálása érdekében kövesse az utasításokat a gyorsított hálózatkezelés engedélyezéséhez a [Windowsban (kattintson az utasításokra)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) vagy a [Linux (az utasításokért)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) az Azure virtuális gép elemre.

A gyorsított hálózatkezelés nélkül az i/o-t, amely az Azure-beli virtuális gép és más Azure-erőforrások közötti áthaladást okozhatja, szükségtelenül irányítható a virtuális gép és a hálózati kártya között található gazdagépen és virtuális kapcsolón keresztül. Ha a gazdagép és a virtuális kapcsoló beágyazott a DataPath, nem csupán a késést és a jitter-t növeli a kommunikációs csatornán, akkor a virtuális gép CPU-ciklusait is ellopja. A gyorsított hálózatkezeléssel a VM-felületek közvetlenül a hálózati adapterrel rendelkeznek közvetítők nélkül; a gazdagép és a virtuális kapcsoló által kezelt hálózati házirend-részletek mostantól a hálózati adapter hardverén vannak kezelve. a gazdagép és a virtuális kapcsoló megkerülése megtörténik. Általában alacsonyabb késést és nagyobb átviteli sebességet várhat, valamint *konzisztens* késést és csökkent CPU-kihasználtságot a gyorsított hálózatkezelés engedélyezésekor.

Korlátozások: a virtuális gép operációs rendszerének támogatnia kell a gyorsított hálózatkezelést, és csak akkor engedélyezhető, ha a virtuális gép le van állítva és fel van foglalva. A virtuális gép nem telepíthető Azure Resource Managerkal.

További részletekért tekintse meg a Windows és a [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) [rendszerre](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) vonatkozó utasításokat.

## <a name="sdk-usage"></a>SDK-használat
* **A legújabb SDK telepítése**

    A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](sql-api-sdk-async-java.md) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.

* **Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

    Minden Azure Cosmos DB ügyfél-példány szál-biztonságos, és hatékony kapcsolatokat végez a kapcsolatok kezeléséhez és a címek gyorsítótárazásához. Annak érdekében, hogy a Azure Cosmos DB-ügyfél hatékony legyen a kapcsolatok felügyelete és a jobb teljesítmény, ajánlott a Azure Cosmos DB-ügyfél egyetlen példányát használni alkalmazástartomány az alkalmazás élettartamára.

   <a id="max-connection"></a>

* **Az alkalmazáshoz szükséges legalacsonyabb konzisztencia-szint használata**

    *CosmosClient*létrehozásakor a rendszer az alapértelmezett konzisztenciát használja, ha nincs explicit módon beállítva a *munkamenet*. Ha az alkalmazás logikája nem igényli a *munkamenet* konzisztenciáját, állítsa a *konzisztenciát* a *végső*értékre. Megjegyzés: javasoljuk, hogy legalább a *munkamenetek* konzisztenciáját használja a Azure Cosmos db változási csatornát használó alkalmazásokban.

* **Az aszinkron API használata a kiépített átviteli sebesség max.**

    Azure Cosmos DB Java SDK v4 két API-t, szinkronizálást és aszinkron csomagokat tartalmaz. Durván szólva az aszinkron API az SDK funkcionalitását valósítja meg, míg a Sync API egy vékony burkoló, amely letiltja a hívásokat az aszinkron API-nak. Ez ellentétben áll a régebbi Azure Cosmos DB aszinkron Java SDK v2-vel, amely csak aszinkron volt, és a régebbi Azure Cosmos DB Sync Java SDK v2, amely csak szinkronizálást hajtott végre, és teljesen különálló implementációval rendelkezett. 
    
    Az API megválasztása az ügyfél inicializálása során van meghatározva; a *CosmosAsyncClient* támogatja az aszinkron API-t, míg a *CosmosClient* támogatja a szinkronizálási API-t. 
    
    Az aszinkron API megvalósítja a nem blokkoló i/o-t, és az optimális választás, ha a cél az, hogy a kérések kiküldésekor Azure Cosmos DB. 
    
    A szinkronizálási API használata a megfelelő választás, ha olyan API-t szeretne vagy igényel, amely blokkolja az egyes kérelmekre adott válaszokat, vagy ha a szinkron művelet az alkalmazás domináns paradigma. Előfordulhat például, hogy a szinkronizálási API-t szeretné használni, amikor az adatok megőrzését Azure Cosmos DB egy Service-alkalmazásban, a megadott átviteli sebesség nem kritikus.  
    
    Vegye figyelembe, hogy az API-k szinkronizációs sebességének csökkentése a kérelmek válaszideje egyre növekszik, míg az aszinkron API a hardver teljes sávszélességét felhasználhatja. 
    
    A földrajzi létrehozásakor közös elhelyezés nagyobb és konzisztens átviteli sebességet biztosíthat a szinkronizálási API használatakor (lásd: [azonos Azure-régióban lévő rézvezetékes végezhet-ügyfelek teljesítménye](#collocate-clients)), de még nem várható, hogy túllépi az aszinkron API-k számára elérhető átviteli sebességet.

    Előfordulhat, hogy egyes felhasználók nem ismerik a [Project reaktort](https://projectreactor.io/), az Azure Cosmos db Java SDK v4 aszinkron API megvalósításához használt reaktív Streams keretrendszert. Ha ez aggodalomra ad okot, javasoljuk, hogy olvassa el a bevezető [reaktor minta útmutatóját](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) , és tekintse meg a [reaktív programozás bevezetését](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) , hogy megismerkedjen. Ha már használta a Azure Cosmos DBT egy aszinkron felülettel, és a használt SDK Azure Cosmos db aszinkron Java SDK v2-ben, akkor lehet, hogy már ismeri az [újraactivex-](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava) , de nem biztos benne, hogy mi változott a projekt-reaktorban. Ebben az esetben tekintse át a [Reactor vs. RxJava útmutatót](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) , és ismerkedjen meg a következővel:.

    A következő kódrészletek bemutatják, hogyan inicializálhatja Azure Cosmos DB ügyfelet az aszinkron API-vagy szinkronizálási API-művelethez:

    #### <a name="async"></a>[Aszinkron](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **ConnectionPolicy finomhangolása**

    Alapértelmezés szerint a közvetlen üzemmódú Cosmos DB kérelmek TCP protokollon keresztül történnek Azure Cosmos DB Java SDK v4 használata esetén. Belsőleg az SDK egy speciális közvetlen üzemmódú architektúrát használ a hálózati erőforrások dinamikus kezeléséhez és a legjobb teljesítmény eléréséhez.

    Azure Cosmos DB Java SDK v4-ben a közvetlen mód a legjobb választás az adatbázis teljesítményének növelésére a legtöbb munkaterheléssel. 

    * ***Közvetlen üzemmód áttekintése***

        ![A közvetlen módú architektúra ábrája](./media/performance-tips-async-java/rntbdtransportclient.png)

        A közvetlen módban alkalmazott ügyféloldali architektúra előre jelezhető hálózati kihasználtságot és többszörös hozzáférést biztosít Azure Cosmos DB replikához. A fenti ábrán látható, hogy a Direct Mode hogyan irányítja az ügyfelek kérelmeit a Cosmos DB háttérbeli replikára. A közvetlen módú architektúra legfeljebb 10 **csatornát** foglal le az ügyféloldali replikán. A csatornák egy TCP-kapcsolatok, amely előtt egy kérelem-puffer található, amely 30 kérelem mélyét képezi. A replikához tartozó csatornák dinamikusan vannak lefoglalva a replika **szolgáltatási végpontja**által igényelt módon. Amikor a felhasználó közvetlen módban bocsát ki egy kérést, a **TransportClient** a megfelelő szolgáltatási végpontra irányítja a kérést a partíciós kulcs alapján. A kérelmek **várólistájának** pufferei a szolgáltatási végpont előtt érkeznek.

    * ***ConnectionPolicy-konfigurációs beállítások közvetlen módban***

        Ezek a konfigurációs beállítások vezérlik a RNTBD architektúra viselkedését, amely a Direct Mode SDK viselkedését szabályozza.
        
        Első lépésként használja az alábbi ajánlott konfigurációs beállításokat. Ezek a *ConnectionPolicy* beállítások olyan speciális konfigurációs beállítások, amelyek nem várt módon befolyásolhatják az SDK teljesítményét. Javasoljuk, hogy a felhasználók ne módosíthassák őket, hacsak nem érzik nagyon kényelmesnek a kompromisszumok megismerését, és elengedhetetlenek. Ha az adott témakörben problémákba ütközik, forduljon a [Azure Cosmos db csapatához](mailto:CosmosDBPerformanceSupport@service.microsoft.com) .

        Ha a Azure Cosmos DBt hivatkozási adatbázisként használja (azaz az adatbázist sok pont olvasási művelethez és kevés írási művelethez használja), akkor elfogadható lehet a *idleEndpointTimeout* 0 értékre (azaz nincs időkorlát) beállítani.


        | Konfigurációs beállítás       | Alapértelmezett    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | connectionTimeout          | "PT1M"     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | "PT5S"     |
        | requestTimeout             | "PT1M"     |
        | requestTimerResolution     | "PT 0.5 S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | "PT15S"    |

* **A particionált gyűjtemények párhuzamos lekérdezésének finomhangolása**

    Azure Cosmos DB Java SDK v4 támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: a Azure Cosmos DB Java SDK v4-sel való együttműködéshez kapcsolódó [kód-minták](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy a lekérdezési késést és az adatátvitelt a soros munkatársaik

    * ***SetMaxDegreeOfParallelism finomhangolása\:***
    
        A párhuzamos lekérdezések több partíció párhuzamos lekérdezésével működnek. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezéssel kapcsolatos sorosan kerülnek beolvasásra. Ezért a setMaxDegreeOfParallelism használatával állítsa be a legtöbb teljesítményű lekérdezés elérésének maximális esélyét biztosító partíciók számát, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism használatával magas számot állíthat be, a rendszer pedig a minimális párhuzamosságot (a partíciók számát, a felhasználó által megadott bemenetet) adja meg.

        Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyöket nyújtják, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény úgy van particionálva, hogy a lekérdezés által visszaadott összes adat többsége néhány partíción (egy partíció a legrosszabb esetben) van, akkor a lekérdezés teljesítményét a partíciók szűk keresztmetszete okozhatja.

    * ***SetMaxBufferedItemCount finomhangolása\:***
    
        A párhuzamos lekérdezés úgy lett kialakítva, hogy előzetesen beolvassa az eredményeket, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. a setMaxBufferedItemCount korlátozza az előre beolvasott eredmények számát. A setMaxBufferedItemCount beállítása a visszaadott eredmények várt számához (vagy egy magasabb szám) lehetővé teszi a lekérdezés számára, hogy a lehető legtöbbet fogadja az előzetes lekéréstől.

        Az előzetes lekérés ugyanúgy működik, mint a Maxanalyticsunits, és egyetlen puffer van az összes partícióból származó adatokhoz.

* **Az ügyfél felskálázása – munkaterhelés**

    Ha magas átviteli sebességű tesztelést végez, előfordulhat, hogy az ügyfélalkalmazás szűk keresztmetszetet okoz a processzor vagy a hálózat kihasználtsága miatt. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

    Egy jó ökölszabály nem lépi túl a >50%-os CPU-kihasználtságot az adott kiszolgálón, hogy a késés alacsony legyen.

   <a id="tune-page-size"></a>

* **A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

    A dokumentumok tömeges beolvasása a hírcsatornák olvasási funkciójának használatával (például *readItems*) vagy SQL-lekérdezés (*queryItems*) kiadása esetén az eredmények szegmentált módon lesznek visszaadva, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

    Tegyük fel, hogy az alkalmazás lekérdezi a Azure Cosmos DB, és azt feltételezi, hogy az alkalmazás a lekérdezés összes eredményét igényli a feladat elvégzéséhez. Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör-átutazások számát, az [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) kérelem fejlécének beállításával növelheti az oldalméret értékét. 

    * Egypartíciós lekérdezések esetén az [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) mező értékének beállítása a-1 értékre (az oldalméret korlátozása nélkül) maximalizálja a késést a lekérdezési válaszok oldalai számának minimalizálásával: vagy a teljes eredményhalmaz egyetlen lapon lesz visszaadva, vagy ha a lekérdezés az időtúllépési intervallumnál hosszabb időt vesz igénybe, akkor a rendszer a teljes eredményhalmazt adja vissza a lehető legkevesebb oldalon. Ez a kérelem többször is megtakarítható.
    
    * A több partíciós lekérdezések esetében állítsa az [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) mezőt a-1 értékre, és távolítsa el az oldal méretének korlátját, amely az SDK-t a nem felügyelhető oldalméret miatt túlterheli. A több partíciós megoldás esetében javasoljuk, hogy növelje az oldal méretének korlátját akár egy nagy, de véges értékű értékre (akár 1000) a késés csökkentése érdekében. 
    
    Egyes alkalmazásokban nem szükséges a lekérdezés összes eredményének teljes készlete. Olyan esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API egyszerre csak 10 találatot ad vissza, az olvasási és lekérdezési sebesség csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

    A *byPage* metódus előnyben részesített oldalméret-argumentumát is beállíthatja ahelyett, hogy közvetlenül MÓDOSÍTANÁ a REST-fejléc mezőjét. Ne feledje, hogy az [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) vagy a *byPage* előnyben részesített oldalméret argumentuma csak az oldalméret felső korlátját állítja be, nem abszolút követelmény; így számos okból láthatja, hogy Azure Cosmos DB olyan visszaadott lapokat, amelyek kisebbek, mint az Ön által preferált oldalméret. 

* **A megfelelő ütemező használata (az Event loop IO-szálak kilopásának elkerülése)**

    Azure Cosmos DB Java SDK aszinkron funkciója a nem blokkoló i/o-adatforgalomon [alapul.](https://netty.io/) Az SDK az i/o-műveletekhez rögzített számú i/o-t használ (a gép számos CPU-magja). Az API által visszaadott adatforgalom kibocsátja az eredményt az egyik megosztott IO-esemény hurok-szálon. Ezért fontos, hogy ne tiltsa le a megosztott IO-esemény hurkos szálait. Ha CPU-igényes munkahelyi vagy letiltási műveletet hajt végre az IO-esemény hurkos szál szálán, a holtpontot okozhat, vagy jelentősen csökkentheti az SDK átviteli sebességét.

    Például az alábbi kód egy CPU-igényes munkát hajt végre az Event loop IO-szálon:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    Ha az eredmény megérkezik, ha a CPU-igényes munkát szeretné elvégezni az eredményen, kerülje az Event loop IO-szálon végzett műveletét. Ehelyett a saját ütemező használatával biztosíthatja a saját szálát a munka futtatásához az alább látható módon.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    A munkája típusától függően a megfelelő meglévő reaktor-ütemező használatával kell használnia a munkát. Itt [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)olvashat.

    A Azure Cosmos DB Java SDK v4-es verziójának részletes ismertetését a [githubon futó Azure SDK for Java monorepo Cosmos db könyvtárában](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)tekintheti meg.

* **A naplózási beállítások optimalizálása az alkalmazásban**

    Számos ok miatt előfordulhat, hogy a naplózást egy olyan szálban kell felvennie, amely nagy kérések átviteli sebességét eredményezi. Ha a cél az, hogy teljes mértékben telített legyen egy tároló kiépített átviteli sebessége a szál által generált kérelmekkel, a naplózási optimalizálás nagy mértékben növelheti a teljesítményt.

    * ***Aszinkron naplózó konfigurálása***

        A szinkron naplózó késése szükségszerűen a kérés-előállító szál teljes késésének számítása. A nagy teljesítményű alkalmazási szálakból való kilépéshez olyan aszinkron naplózó használata javasolt, mint például a [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) .

    * ***A nettó naplózás letiltása***

        A további CPU-költségek elkerülése érdekében a többfunkciós kódtár naplózása beszédes, és ki kell kapcsolni (a bejelentkezés a konfigurációban nem lehet elég). Ha nem hibakeresési módban van, tiltsa le a nettó naplózást. Tehát ha a log4j használatával távolítja el a további CPU ``org.apache.log4j.Category.callAppenders()`` -költségeket a (z) rendszerből, adja hozzá a következő sort a kód számára:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **Operációs rendszer által megnyitott fájlok erőforrás-korlátja**
 
    Bizonyos linuxos rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok számától, így a kapcsolatok teljes száma. A jelenlegi korlátok megtekintéséhez futtassa a következő parancsot:

    ```bash
    ulimit -a
    ```

    A megnyitott fájlok (nem fájl) számának elég nagynak kell lennie ahhoz, hogy elegendő hely legyen a konfigurált és az operációs rendszer által megnyitott fájlok számára. Ez módosítható úgy, hogy nagyobb a kapcsolatok készletének mérete.

    Nyissa meg a Limits. conf fájlt:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    A következő sorok hozzáadása/módosítása:

    ```
    * - nofile 100000
    ```

* **A partíciós kulcs meghatározása a pont írásai között**

    A pontok írásának teljesítményének növeléséhez az alábbi ábrán látható módon írja be az elem partíciós kulcsát az írási API-hívásba:

    #### <a name="async"></a>[Aszinkron](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    Ahelyett, hogy csak az elem példányát adja meg, az alább látható módon:

    #### <a name="async"></a>[Aszinkron](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    ```java
    syncContainer.createItem(item);
    ```

    ---

    Az utóbbi támogatott, de késleltetést fog adni az alkalmazáshoz; Az SDK-nak elemezni kell az elemeket, és ki kell bontania a partíciós kulcsot.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
* **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Azure Cosmos DB indexelési házirendje lehetővé teszi annak meghatározását, hogy mely dokumentum-elérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési útvonalak (setIncludedPaths és setExcludedPaths) használatával. Az indexelési útvonalak használata javíthatja az írási teljesítményt és az alacsonyabb indexű tárolást olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték, mivel az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával vannak összekapcsolva. Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok (más néven részfa) teljes szakasza az indexelésből a "*" helyettesítő karakter használatával.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos)
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    További információ: [Azure Cosmos db indexelési házirendek](indexing-policies.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

* **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

    A Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, beleértve a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, és az adatbázis-gyűjteményen belüli összes műveletet. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

    Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt.

    A lekérdezés bonyolultsága befolyásolja, hogy hány kérelem-egységet használ a művelet. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok készletének mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

    Bármilyen művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet a műveletek által felhasznált kérelmek mennyiségének méréséhez. Az egyenértékű RequestCharge tulajdonságot a ResourceResponse\<T> vagy a FeedResponse\<t>ban is megtekintheti.

    #### <a name="async"></a>[Aszinkron](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség töredéke. Ha például 2000 RU/s van kiépítve, és ha az előző lekérdezés a 1000 1KB-dokumentumokat adja vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

    Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel a kérést a RequestRateTooLarge (429-es HTTP-állapotkód), és visszaküldi az [x-MS-újrapróbálkozás-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet, jelezve azt az időtartamot (ezredmásodpercben), ameddig a felhasználónak meg kell várnia a kérés ismételt megkísérlése előtt.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

    Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó *CosmosClientException* hoz az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható a ConnectionPolicy-példány setRetryOptions használatával. Alapértelmezés szerint a 429-as kódú *CosmosClientException* a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték.

    Míg az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását, akkor előfordulhat, hogy a teljesítményre vonatkozó teljesítménymutatók végrehajtásakor a rendszer hasznosnak bizonyul, különösen a késés mérése során. Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.

* **Kisebb dokumentumok tervezése magasabb átviteli sebesség esetén**

    Egy adott műveletre vonatkozó kérelem díja (a kérelmek feldolgozási díja) közvetlenül összefügg a dokumentum méretével. A nagyméretű dokumentumokon végzett műveletek többek között a kis méretű dokumentumok műveleteinél nagyobb mértékben járnak. Ideális esetben az alkalmazás és a munkafolyamatok tervezője, hogy az elem mérete ~ 1KB vagy hasonló sorrend vagy magnitúdó legyen. A késésre érzékeny alkalmazások esetén a nagyméretű elemeket el kell kerülni – a több MB méretű dokumentumok lelassítják az alkalmazást.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partition-data.md)című témakört.
