---
title: Az Azure SignalR Service teljesítmény-útmutató
description: A teljesítmény az Azure SignalR Service áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579240"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Az Azure SignalR Service teljesítmény-útmutató

A legfontosabb előnyei az Azure SignalR Service egyik, a SignalR-alkalmazások méretezése könnyű. Egy nagyméretű esetben teljesítményét, egy fontos tényező. 

Ebben az útmutatóban a SignalR alkalmazásteljesítményt befolyásoló tényezők nyomán. Tipikus teljesítmény, a különböző használati esetek alapján ismertetjük. A végén nyomán a környezetet és eszközöket, amelyek segítségével a teljesítmény-jelentés létrehozásához.

## <a name="term-definitions"></a>Kifejezés definíciók

*Bejövő*: A bejövő üzenetben az Azure SignalR Service.

*Kimenő*: A kimenő üzenetet, az Azure SignalR Service-ben.

*Bandwidth*: 1 másodperc üzenetek teljes mérete.

*Alapértelmezett mód*: Az alapértelmezett működő mód, ha az Azure SignalR Service-példány létrehozása. Az Azure SignalR Service vár az alkalmazáskiszolgáló vele kapcsolatot létesíteni, mielőtt bármilyen ügyfélkapcsolatokat fogad el.

*Kiszolgáló nélküli módban*: Egy módot, amelyben az Azure SignalR Service csak-ügyfélkapcsolatokat fogad el. Nincs kapcsolat a kiszolgálóval használata engedélyezett.

## <a name="overview"></a>Áttekintés

Az Azure SignalR Service eltérő teljesítménybeli kapacitás hét Standard szint határozza meg. Ez az útmutató az alábbi kérdéseket:

-   Mi az a tipikus Azure SignalR Service teljesítmény az egyes szintek?

-   Az Azure SignalR Service felel meg a saját üzeneteinek átviteli sebessége (például küldő 100 000 üzenetek / másodperc) vonatkozó követelmények?

-   Adott forgatókönyvhöz, melyik szint a megfelelő a számomra? És hogyan lehet ki a megfelelő szint?

-   Milyen típusú alkalmazások kiszolgálói (virtuális gép mérete) az megfelelő a számomra? Ezek közül hány kell helyezhetek üzembe?

Ezek a kérdések megválaszolásához Ez az útmutató első biztosít egy magas szintű teljesítményt befolyásoló tényezők ismertetése. Ezután minden réteget a tipikus használati esetek maximális bejövő és kimenő üzenetek mutatja be: **echo**, **adás**, **Küldés csoportba**, és **küldése kapcsolat** (társ-társ Csevegés).

A jelen útmutató nem tartalmazza az összes forgatókönyv (és különböző használati helyzetekhez, az üzenet mérete, üzenetet küldő mintákat, és így tovább). De egyes metódusok segítségével biztosít:

- Értékelje ki a bejövő vagy kimenő üzenetek hozzávetőleges követelmény.
- Keresse meg a megfelelő szint a teljesítménytáblája ellenőrzésével.

## <a name="performance-insight"></a>Terheléselemző

Ez a szakasz azt ismerteti, a teljesítmény módszertanok, és ezután kilistázza az összes olyan tényezőt, a teljesítményre. A végén teljesítmény-követelmények kiértékeléséhez is módszert is biztosít.

### <a name="methodology"></a>Módszer

*Átviteli sebesség* és *késés* két tipikus szempontból teljesítményének ellenőrzése. Az Azure SignalR Service-ben minden egyes Termékváltozat-szint rendelkezik a saját kapacitása a házirend-szabályozás. A szabályzat határozza meg *a maximálisan engedélyezett átviteli sebesség (bejövő és kimenő sávszélesség)* , a maximális átviteli sebesség érhető el, ha 99 %-os üzenetet, amely kisebb, mint 1 másodperc késéssel rendelkeznek.

Késései időtartomány a kapcsolatból megpróbáltuk elküldeni az üzenetet a válaszüzenet fogadása az Azure SignalR Service-ben. Vessünk **echo** példaként. Minden ügyfél-csatlakozási időbélyeg hozzáadja az üzenetben. Az alkalmazáskiszolgáló hub az eredeti üzenet elküldi az ügyfélnek. A propagálás késleltetés így könnyen minden ügyfélkapcsolat számolható ki. Az időbélyeg csatolt minden üzenetet **adás**, **Küldés csoportba**, és **küldeni a kapcsolat**.

Egyidejűleg futó ügyfélkapcsolatok ezer szimulálja, több virtuális gép létrehozása az Azure-beli virtuális magánhálózati. Összes ilyen virtuális gépet az Azure SignalR Service ugyanazon csatlakozni.

Az alapértelmezett módban, az Azure SignalR Service app-kiszolgáló virtuális gépek vannak üzembe helyezve az azonos virtuális magánhálózati ügyfél virtuális gépek. Az összes ügyfél virtuális gépek és alkalmazások kiszolgálói virtuális gépek ugyanazon a hálózaton a régiók közötti késleltetés elkerülése érdekében ugyanabban a régióban vannak üzembe helyezve.

### <a name="performance-factors"></a>Teljesítmény tényezők

Az Azure SignalR Service kapacitás elméletileg számítási erőforrások készleteként korlátozza: CPU, memória és hálózati. Például az Azure SignalR Service további kapcsolatok esetén a szolgáltatás több memóriát használjanak. A nagyobb méretű üzenet forgalmat (például összes üzenetet a nagyobb, mint 2048 bájt), az Azure SignalR Service kell további processzorciklusok forgalom feldolgozása. Az Azure hálózati sávszélességet, is maximális forgalom korlátot ír elő.

Az átviteli típus teljesítményét befolyásoló másik tényező. A három típus [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [kiszolgáló küldött események](https://en.wikipedia.org/wiki/Server-sent_events), és [hosszú lekérdezéseket](https://en.wikipedia.org/wiki/Push_technology). 

Objekt WebSocket je egy kétirányú és a kétirányú kommunikációs protokollja TCP egyetlen kapcsolaton keresztül. Kiszolgáló küldött események egy egyirányú protokoll az a kiszolgáló a leküldéses üzenetek. Hosszú lekérdezéseket igényel az ügyfelek és rendszeres időközönként lekérdezi az adatait egy HTTP-kérés a kiszolgálóról. Az azonos API számára azonos feltételek WebSocket rendelkezik a lehető legjobb teljesítményt, Server elküldött esemény lassabb lesz, és hosszú lekérdezéseket a leglassabb. Az Azure SignalR Service alapértelmezés szerint WebSocket javasolja.

Az üzenet-útválasztási költség is korlátozza a teljesítményt. Az Azure SignalR Service szerepet játszik az olyan, egy üzenet útválasztó, amely az ügyfelek vagy kiszolgálók közül az üzenet más ügyfelek vagy kiszolgálók irányítja. Egy más-más forgatókönyvet vagy egy API-t igényel a különböző útválasztási házirend. 

A **echo**, az ügyfél egy üzenetet küld saját maga és az útválasztási cél önmagában is. Ehhez a mintához a legkisebb útválasztási költséget. De a **adás**, **Küldés csoportba**, és **küldeni a kapcsolat**, az Azure SignalR Service van szüksége, keresse ki a cél-kapcsolatok keresztül a belső elosztott adatok struktúra. A további feldolgozás több Processzor, memória és a hálózati sávszélességet használ. Ennek eredményeképpen teljesítménye lassabb lesz.

Az alapértelmezett módban az alkalmazáskiszolgáló is előfordulhat, hogy váljon szűk keresztmetszetté bizonyos forgatókönyvek esetén. Az Azure SignalR SDK a hub meghívni, miközben megőrzi-e minden Szívveréses jelek ügyfelet élő kapcsolattal rendelkezik.

Kiszolgáló nélküli módban az ügyfél által a HTTP post, amely nem annyira hatékony, mint a WebSocket üzenetet küld.

Egy másik tényező, protokoll: JSON és [MessagePack](https://msgpack.org/index.html). MessagePack JSON-kal gyorsabbak, kisebb méretű és kézbesített. MessagePack nem javíthatja a teljesítményt, azonban. A teljesítmény az Azure SignalR Service azért nem protokollok érzékeny, mert azt nem az üzenet hasznos adattartalmából dekódolása kiszolgálók vagy fordítva ügyfelektől érkező üzenetek továbbítása során.

Összefoglalva az alábbi tényezők befolyásolják a bejövő és kimenő kapacitás:

-   Termékváltozat-szint (CPU/memória)

-   Kapcsolatok száma

-   Üzenet mérete

-   üzenet küldések

-   Az átviteli típust (WebSocket, Server elküldött esemény vagy hosszú lekérdezéseket)

-   Használatieset-forgatókönyvet (útválasztási költsége)

-   alkalmazás-kiszolgáló és a szolgáltatás kapcsolatok (a kiszolgáló mód)


### <a name="finding-a-proper-sku"></a>Egy megfelelő Termékváltozatot keresése

Hogyan, a bejövő/kimenő kapacitás kiértékelése vagy milyen szinten található az adott használati esetekhez megfelelő?

Tegyük fel, hogy az alkalmazás server elég erős és nem a teljesítménybeli szűk keresztmetszetek. Ezt követően ellenőrizze a maximális bejövő és kimenő sávszélesség minden szinthez.

#### <a name="quick-evaluation"></a>Gyors kipróbálási

Nézzük egyszerűsítheti az értékelés először úgy vesszük, hogy néhány alapértelmezett beállításokat: 

- Az átviteli típus WebSocket.
- Az üzenet mérete 2048 bájt.
- Egy üzenetet küld minden 1 másodperc.
- Az Azure SignalR Service az alapértelmezett módban van.

Minden szint tartalmaz, a saját maximális bejövő és kimenő sávszélesség. Zökkenőmentes felhasználói élmény nem garantált, miután a bejövő vagy kimenő kapcsolat meghaladja a korlátot.

**Echo** biztosítja a legnagyobb bejövő sávszélesség, a legkisebb útválasztási költségeket, mert. **Szórási** határozza meg a kimenő üzenetek maximális sávszélesség.

Tegye *nem* haladhatja meg az alábbi két táblázatban a kijelölt értékeket.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| **Bejövő sávszélesség** | **2 MBps**    | **4 MBps**    | **10 MB/s**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Kimenő sávszélesség | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Szórás             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő sávszélesség  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Kimenő sávszélesség** | **4 MBps**    | **8 MB/s**    | **20 MBps**    | **40 MBps**    | **80 MB/s**    | **200 MBps**    | **400 MBps**   |

*Bejövő sávszélesség* és *kimenő sávszélesség* vannak a teljes üzenetmérete másodpercenként.  Az alábbiakban a képletek a számukra:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Az üzenet elküldésekor kapcsolatok száma.

- *outboundConnections*: Az üzenetet fogadó kapcsolatok száma.

- *messageSize*: Egy adott üzenet (átlag) mérete. Kis méretű üzenet, hogy kevesebb, mint 1024 bájt 1024 bájt méretű üzenet hasonló teljesítmény hatással van.

- *sendInterval*: Az ideje egy üzenetet küld. Ez általában egy üzenet, ami azt jelenti, egy üzenetet küld, másodpercenként 1 másodperc. Egy kisebb időtartam azt jelenti, hogy több üzenetet küld egy adott időszakban. Például egy üzenet 0,5 másodperc azt jelenti, hogy két üzenetküldésre másodpercenként.

- *Kapcsolatok*: A lefoglalt maximális Ügyfélszám elérése az Azure SignalR Service minden szinthez. A kapcsolat a szám tovább növekszik, ha a kapcsolat szabályozás romlik.

#### <a name="evaluation-for-complex-use-cases"></a>Kiértékelés a komplex használati esetek

##### <a name="bigger-message-size-or-different-sending-rate"></a>Nagyobb méretű üzenetek mérete vagy a különböző küldési gyakorisága

Valós használati eset jóval összetettebb. Előfordulhat, hogy küldjön üzenetet 2048 bájtnál nagyobb, vagy a küldő üzenetküldési gyakoriság nem nem egy üzenet / másodperc. Vegyük Unit100 a közvetítés példaként, hogyan lehet a teljesítmény kiértékelése.

Az alábbi táblázat egy valódi használati esetet **adás**. De az üzenet mérete a, kapcsolatok száma és sebessége üzenetküldést különböznek a mi azt feltételezi, az előző szakaszban. A kérdés az, hogy hogyan tudjuk is kikövetkeztetni bármelyik azok az elemek (üzenet mérete, kapcsolatszáma vagy üzenet küldés sebességét) Ha tudjuk, hogy csak két.

| Szórás  | Üzenet mérete | Bejövő üzenetek / másodperc | Kapcsolatok | Időközönként küldése |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 mp                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 mp                      |

A következő képletre célszámítógéppel egyszerű képlet alapján:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

A maximális kimenő sávszélesség Unit100, származik 400 MB az előző táblázatban. A kimenő kapcsolatok maximális száma egy 20-KB a mérete legyen 400 MB \* 5 / 20 KB-os = 100 000, amely megfelel a tényleges érték.

##### <a name="mixed-use-cases"></a>Vegyes alkalmazási helyzetek

Valós használati eset általában az együtt a négy alapvető használati esetek eredményét: **echo**, **adás**, **Küldés csoportba**, és **küldeni a kapcsolat**. A kapacitás kiértékeléséhez használt módszer az, hogy:

1. Ossza a vegyes használati esetek négy alapvető használati esetek.
1. A bejövő és kimenő üzenetek maximális sávszélesség az előző képletek külön-külön használatával számítják ki.
1. A sávszélesség-számítások beolvasni a teljes legnagyobb bejövő/kimenő sávszélesség összeg. 

Ezután vegyen fel a megfelelő szint a legnagyobb bejövő/kimenő sávszélesség táblából.

> [!NOTE]
> Egy üzenetet küld el több száz vagy ezer kisebb csoportok, vagy több ezer üzenetet küld az egyes ügyfelek az útválasztási költség domináns kiemelési lesz. A hatás figyelembe vennie.

Egy üzenetet küldeni az ügyfelek használati esetekhez, győződjön meg arról, hogy van-e az alkalmazáskiszolgáló *nem* a szűk keresztmetszetet. Az alábbi "Esettanulmány" szakasz tartalmaz útmutatást kapcsolatos alkalmazás hány kiszolgálóra van szüksége, és hány kiszolgálókapcsolatok, konfigurálni kell.

## <a name="case-study"></a>Esettanulmány

A következő szakaszok haladjon végig a WebSocket átviteli a tipikus használati esetek négy: **echo**, **adás**, **Küldés csoportba**, és **kapcsolatküldése**. A minden esetben a szakasz felsorolja az Azure SignalR Service aktuális bejövő és kimenő kapacitását. Azt is bemutatja a teljesítményt érintő főbb tényezőket.

Az alapértelmezett módban alkalmazáskiszolgáló az Azure SignalR Service öt kiszolgáló kapcsolatot hoz létre. Az app-kiszolgáló alapértelmezés szerint az Azure SignalR Service SDK-t használja. A következő teljesítmény vizsgálati eredmények, a kiszolgálói kapcsolatok magas 15 (vagy több teszi közzé, és a egy üzenetet küld egy big Data típusú csoport).

Különböző használati helyzetekhez alkalmazás kiszolgálók eltérő követelmények vonatkozhatnak. **Szórási** kis számú alkalmazást kiszolgálóra van szüksége. **Echo** vagy **küldeni a kapcsolat** sok alkalmazás kiszolgáló szükséges.

Az összes használati esetek, az alapértelmezett üzenet mérete 2048 bájt, és az üzenet küldési időköze 1 másodperc.

### <a name="default-mode"></a>Alapértelmezett mód

Ügyfelek, a webalkalmazás-kiszolgálók és az Azure SignalR Service az alapértelmezett mód is érint. Minden ügyfél egyetlen kapcsolaton jelöli.

#### <a name="echo"></a>echo

Először egy webalkalmazás csatlakozik az Azure SignalR Service. A második sok ügyfél csatlakozik a webalkalmazást, amely az ügyfeleket átirányítja a felhasználókat az Azure SignalR Service a hozzáférési jogkivonatot, és a végpont. Ezt követően az ügyfelek kapcsolatot WebSocket az Azure SignalR Service segítségével.

Miután az összes ügyfél kapcsolatokat hozhat létre, elindítja, amely tartalmazza az adott hub időbélyeg másodpercenként üzenetet. A hub ad vissza az eredeti kliensgép üzenet. Minden ügyfél számítja ki a késést, ha vissza az echo üzenetet kap.

Az alábbi ábrán egy hurokba, és 5 – 8 (piros kiemelt forgalom) vannak. A hurok az alapértelmezett időtartamot (5 perc) fut, és minden üzenetet késés a statisztika lekérése.

![Az echo használatieset-adatforgalom](./media/signalr-concept-performance/echo.png)

Viselkedését **echo** megállapítja, hogy a legnagyobb bejövő sávszélesség maximális kimenő sávszélesség egyenlő. További információkért lásd az alábbi táblázatot.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Bejövő/kimenő üzenetek / másodperc | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Bejövő/kimenő sávszélesség | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Az ezt a használati esetet minden ügyfél az alkalmazáskiszolgáló meghatározott hub hív meg. A hub csak az eredeti ügyféloldali meghatározott metódus meghívja. A legtöbb egyszerűsített központot is **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Még esetében ez az egyszerű központ, a forgalom nyomás az alkalmazás kiszolgálón neves, mint a **echo** bejövő üzenet terhelés mértéke. A forgalom nyomás számos alkalmazás-kiszolgálókat igényel az nagy Termékváltozat-szint esetében. Az alábbi táblázat az app-kiszolgálók száma minden szinthez.


|    echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfél csatlakozási szám, üzenet mérete, üzenetküldést arány, a Termékváltozat-szint és a Processzor/memória az alkalmazás-kiszolgáló általános teljesítményét befolyásoló **echo**.

#### <a name="broadcast"></a>Szórás

A **adás**, amikor a webalkalmazás a következő üzenetet kap, közzéteszi az összes ügyfélre. A további ügyfelek szórás számára, a további üzenet forgalom van az összes ügyfél és a rendszer. Az alábbi ábrában.

![A szórási használatieset-adatforgalom](./media/signalr-concept-performance/broadcast.png)

Egy kis mennyiségű ügyfelet sugárzó. A bejövő üzenet sávszélessége kis, de a kimenő sávszélesség hatalmas. A kimenő üzenetek sávszélesség növeli az ügyfél kapcsolatként, vagy növeli a szórási arány.

A következő táblázat összefoglalja az ügyfélkapcsolatok maximális száma, a bejövő/kimenő üzenetek száma és a sávszélesség.

|     Szórás             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő üzenetek / másodperc  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő üzenetek / másodperc | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Bejövő sávszélesség  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Kimenő sávszélesség | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

A szórásos-ügyfelek, amelyek hozzászólást nem legfeljebb négy. Szükségük van képest kevesebb alkalmazáskiszolgálókra **echo** mert kis a bejövő üzenet összege. Két alkalmazás kiszolgáló vonatkoznak a szolgáltatásiszint-szerződés és a teljesítmény kapcsolatos szempontokat. De, növelje az alapértelmezett kiszolgáló-kapcsolatok egyenetlenségének, különösen a Unit50 és Unit100 elkerülésére.

|   Szórás      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Az alapértelmezett kiszolgáló kapcsolatok, az 5. lehetséges egyenetlen kiszolgálókapcsolatok az Azure SignalR Service elkerülése érdekében minden alkalmazás kiszolgálón 40-re növeli.
>
> Az ügyfél kapcsolatainak száma, mérete, üzenet küldés sebességét és Termékváltozat-szint esetében általános teljesítményét befolyásoló **adás**.

#### <a name="send-to-group"></a>Küldés csoportba

A **Küldés csoportba** használati eset rendelkezik a forgalom hasonló mintát **adás**. A különbség az, hogy miután ügyfelek WebSocket-kapcsolatokat az Azure SignalR Service segítségével hozhat létre, csatlakoznia kell csoportokat egy adott csoport üzenetet küldhet. A következő ábra szemlélteti a forgalom áramlását.

![A Küldés-csoport használatieset-adatforgalom](./media/signalr-concept-performance/sendtogroup.png)

Csoport tagja, és a csoportok száma is a két tényező befolyásolja a teljesítményt. Az elemzés leegyszerűsítése meghatározzuk két típusú csoportok:

- **Kis csoport**: Minden csoport rendelkezik 10 kapcsolatot. (A kapcsolat maximális szám) megegyezik a csoportazonosító / 10. Például a Unit1, ha 1000 kapcsolatot számát, majd rendelkezünk 1000 / 10 = 100 csoportok.

- **Nagy csoport**: A csoport számát, mindig 10. (A kapcsolat maximális szám) megegyezik a csoport tagjainak száma / 10. Például Unit1, ha 1000 kapcsolatot számát, majd minden csoport rendelkezik 1000 / 10 = 100 tag.

**Küldés csoportba** számos lehetőséget kínál a költségek az Azure SignalR Service, mert a cél-kapcsolatok keresztül elosztott adatstruktúra található útválasztást. Növelje a küldő kapcsolatok, a költség növekszik.

##### <a name="small-group"></a>Kis csoport

Az útválasztási költsége jelentős üzenetet küld a sok kis csoport számára. Jelenleg az Azure SignalR Service megvalósítási eléri az útválasztási költség Unit50 a korlátot. Több Processzor és memória hozzáadása nem segít, így Unit100 nem javítása további elvárt. Ha több bejövő sávszélesség van szüksége, forduljon az ügyfélszolgálathoz.

|   Kis csoport küldése     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000 | 100 000
| Csoport tagjainak száma        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Csoportok száma               | 100   | 200   | 500    | 1,000  | 2,000  | 5000  | 10,000 
| Bejövő üzenetek / másodperc  | 200   | 400   | 1,000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Bejövő sávszélesség  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Kimenő üzenetek / másodperc | 2,000 | 4,000 | 10,000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Kimenő sávszélesség | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Sok ügyfél kapcsolat hívott a hub, így az alkalmazás kiszolgáló száma is fontos a teljesítmény. A következő táblázat felsorolja a javasolt server alkalmazásszámok.

|  Kis csoport küldése   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfél csatlakozási szám, üzenet mérete, üzenetküldést sebességét, útválasztási költség, Termékváltozat-szint és CPU/memória az alkalmazás-kiszolgáló általános teljesítményét befolyásoló **kis csoport küldése**.

##### <a name="big-group"></a>Nagy csoport

A **küldeni a big Data típusú csoport**, a kimenő sávszélesség a szűk keresztmetszetté válik, előtt szerezze meg az Útválasztás költség korlátot. A következő táblázat felsorolja a maximális kimenő sávszélesség, amely szinte teljesen megegyezik, a **adás**.

|    Nagy csoport küldése      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000
| Csoport tagjainak száma        | 100   | 200   | 500    | 1,000  | 2,000  | 5000   | 10,000 
| Csoportok száma               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Bejövő üzenetek / másodperc  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Bejövő sávszélesség  | 80 KBps   | 40 KB/s   | 40 KB/s    | 20 KBps    | 40 KB/s    | 40 KB/s     | 40 KB/s     |
| Kimenő üzenetek / másodperc | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Kimenő sávszélesség | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A küldő kapcsolatok száma legfeljebb 40. A megoldás az alkalmazások kiszolgálói, kicsi, így a webalkalmazásokhoz javasolt száma kisebb.

|  Nagy csoport küldése  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Az alapértelmezett kiszolgáló kapcsolatok, az 5. lehetséges egyenetlen kiszolgálókapcsolatok az Azure SignalR Service elkerülése érdekében minden alkalmazás kiszolgálón 40-re növeli.
> 
> Az ügyfél kapcsolatainak száma, mérete, üzenet küldés sebességét, útválasztási költség és Termékváltozat-szint általános teljesítményét befolyásoló **küldeni a big Data típusú csoport**.

#### <a name="send-to-connection"></a>Kapcsolat küldése

Az a **küldeni a kapcsolat** használati eset, amikor az ügyfelek az Azure SignalR Service-ben minden ügyfél kapcsolatokat hozhat létre meghív egy speciális hubot az első saját kapcsolatazonosító. A teljesítmény a teljesítményteszt gyűjti az összes kapcsolat-azonosítót, állítja őket, és ismét kiosztja őket minden ügyfélnek küldő célként. Az ügyfelek tartani az üzenet küldése a cél-kapcsolatot a vizsgálat befejezéséig.

![Forgalom küldése-ügyfél használati](./media/signalr-concept-performance/sendtoclient.png)

Az Útválasztás költségét **küldeni a kapcsolat** költsége hasonló **kis csoport küldése**.

A kapcsolatok száma növekszik, ahogy az útválasztási költség korlátozza az általános teljesítményt. Unit50 elérte a határértéket. Ennek eredményeképpen Unit100 további nem javítása.

Az alábbi táblázat statisztikai összegzését, miután futtatásának számos kerekíti a **küldeni a kapcsolat** teljesítményteszt.

|   Kapcsolat küldése   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Kapcsolatok                        | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000          | 100 000         |
| Bejövő/kimenő üzenetek / másodperc | 1,000 | 2,000 | 5000 | 8,000  | 9,000  | 20,000 | 20,000 |
| Bejövő/kimenő sávszélesség | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Ezt a használati esetet nagy terhelés az alkalmazás kiszolgálói oldalán van szükség. Tekintse meg a javasolt alkalmazások kiszolgálói száma az alábbi táblázatban.

|  Kapcsolat küldése  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfél csatlakozási szám, üzenet mérete, üzenetküldést sebességét, útválasztási költség, Termékváltozat-szint és CPU/memória az alkalmazás-kiszolgáló általános teljesítményét befolyásoló **küldeni a kapcsolat**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>Az ASP.NET SignalR echo, szórás számára, és kis csoport küldés

Az Azure SignalR Service biztosít az ASP.NET SignalR teljesítmény kapacitáson. 

A teljesítményteszt használja az Azure Web Apps [Standard csomag S3](https://azure.microsoft.com/pricing/details/app-service/windows/) az ASP.NET SignalR.

Az alábbi táblázat tartalmazza a javasolt web app száma az ASP.NET SignalR **echo**.

|   echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Az alábbi táblázat tartalmazza a javasolt web app száma az ASP.NET SignalR **adás**.

|  Szórás       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Az alábbi táblázat tartalmazza a javasolt web app száma az ASP.NET SignalR **kis csoport küldése**.

|  Kis csoport küldése     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Kiszolgáló nélküli módban

Ügyfelek és az Azure SignalR Service kiszolgáló nélküli módban is érint. Minden ügyfél egyetlen kapcsolaton jelöli. Az ügyfél a REST API-n keresztül üzeneteket küld egy másik ügyfél vagy a szórás üzenetek minden.

Nagy sűrűségű üzenetküldésre, a REST API-val nem hatékonyabb WebSocket használatával. Hozhat létre egy új HTTP-kapcsolat minden alkalommal van szükség, és többlet költség a kiszolgáló nélküli módban.

#### <a name="broadcast-through-rest-api"></a>Szórási REST API-n keresztül
Összes ügyfél WebSocket-kapcsolatokat az Azure SignalR Service hozhat létre. Egyes ügyfelek elindítása szórásos a REST API-n keresztül. Az üzenet küldése (bejövő) van sokoldalúsága HTTP Post, amely nem hatékony WebSocket képest.

|   Szórási REST API-n keresztül     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő üzenetek / másodperc  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő üzenetek / másodperc | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Bejövő sávszélesség  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Kimenő sávszélesség | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Felhasználói REST API-n keresztül történő küldése
A teljesítményteszt rendel a felhasználónevek egyeztetéséhez összes ügyfél csatlakozik az Azure SignalR Service indítása előtt. Miután az ügyfelek a WebSocket-kapcsolatokat az Azure SignalR Service hozhat létre, üzenetek küldése mások HTTP Post használatával indítása.

|   Felhasználói REST API-n keresztül történő küldése | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő üzenetek / másodperc  | 300   | 600   | 900    | 1,300  | 2,000  | 10,000  | 18,000  |
| Kimenő üzenetek / másodperc | 300   | 600   | 900    | 1,300  | 2,000  | 10,000  | 18,000 |
| Bejövő sávszélesség  | 600 KBps  | 1.2-es MB/s  | 1.8-as MB/s   | 2.6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Kimenő sávszélesség | 600 KBps  | 1.2-es MB/s  | 1.8-as MB/s   | 2.6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Teljesítmény-tesztelési környezetek

Az összes korábban felsorolt esetekben használja, hogy végzett a teljesítménytesztek Azure-környezet. Egyszerre legfeljebb használtuk 50 ügyfél virtuális gépek és a 20 app server virtuális gépek. Az alábbiakban néhány részletei:

- Ügyfél-Virtuálisgép mérete: StandardDS2V2 (2 vCPU, memória 7G)

- Alkalmazás-kiszolgáló virtuális gép mérete: StandardF4sV2 (4 vCPU, memória 8G)

- Az Azure SignalR SDK kiszolgálókapcsolatok: 15

## <a name="performance-tools"></a>Teljesítménynövelő eszközök

Megtalálhatja teljesítménynövelő eszközök az Azure SignalR Service [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>További lépések

Ez a cikk tipikus használatieset-forgatókönyvek az Azure SignalR Service teljesítményének áttekintését kapott.

A szolgáltatás és a méretezés, elemei adatainak olvassa el a következő útmutatókat:

* [Az Azure SignalR Service belső elemei](signalr-concept-internals.md)
* [Az Azure SignalR Service skálázása](signalr-howto-scale-multi-instances.md)
