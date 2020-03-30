---
title: Teljesítménnyel kapcsolatos útmutatás az Azure SignalR Service-hez
description: Az Azure SignalR-szolgáltatás teljesítményének és teljesítményteljesítményének áttekintése. A kapacitás tervezésekor figyelembe vehető legfontosabb mérőszámok.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157664"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Teljesítménnyel kapcsolatos útmutatás az Azure SignalR Service-hez

Az Azure SignalR-szolgáltatás használatának egyik legfontosabb előnye a SignalR-alkalmazások egyszerű skálázása. Egy nagyszabású forgatókönyv, a teljesítmény fontos tényező. 

Ebben az útmutatóban bemutatjuk azokat a tényezőket, amelyek befolyásolják a SignalR alkalmazás teljesítményét. A jellemző teljesítményt különböző használati esetekben ismertetjük. Végül bemutatjuk a környezetet és a teljesítményjelentés létrehozásához használható eszközöket.

## <a name="term-definitions"></a>Kifejezésdefiníciók

*Bejövő:* A bejövő üzenet az Azure SignalR szolgáltatás.

*Kimenő*: Az Azure SignalR szolgáltatás kimenő üzenete.

*Sávszélesség*: Az összes üzenet teljes mérete 1 másodperc alatt.

*Alapértelmezett mód:* Az azure SignalR service-példány létrehozásakor az alapértelmezett munkamód. Az Azure SignalR-szolgáltatás elvárja, hogy az alkalmazáskiszolgáló kapcsolatot létesítsen vele, mielőtt bármilyen ügyfélkapcsolatot elfogadna.

*Kiszolgáló nélküli mód:* Olyan mód, amelyben az Azure SignalR Service csak ügyfélkapcsolatokat fogad el. Nem engedélyezett a kiszolgálókapcsolat.

## <a name="overview"></a>Áttekintés

Az Azure SignalR Service hét standard szintet határoz meg a különböző teljesítménykapacitásokhoz. Ez az útmutató a következő kérdésekre ad választ:

-   Mi az egyes szintek hez tartozó azure SignalR-szolgáltatás jellemző teljesítménye?

-   Megfelel az Azure SignalR-szolgáltatás az üzenetátviteli követelményeknek (például másodpercenként 100 000 üzenet küldése)?

-   Az én konkrét forgatókönyv, melyik szint alkalmas számomra? Vagy hogyan választhatom ki a megfelelő szintet?

-   Milyen alkalmazásszerver (VM méret) alkalmas számomra? Hányat kell bevetnem?

Ezeknek a kérdéseknek a megválaszolása érdekében ez az útmutató először a teljesítményt befolyásoló tényezők magas szintű magyarázatát ismerteti. Ezután bemutatja a maximális bejövő és kimenő üzeneteket minden réteghez a tipikus használati esetekhez: **echo**, **broadcast**, send **to group**, and send **to connection** (peer-to-peer chat).

Ez az útmutató nem terjed ki az összes forgatókönyvre (és a különböző használati esetekre, az üzenetek méretére, az üzenetküldési mintákra és így tovább). De ez szolgáltat némely módszer segíteni neki ön:

- Értékelje ki a bejövő vagy kimenő üzenetek hozzávetőleges követelményét.
- Keresse meg a megfelelő szinteket a teljesítménytábla ellenőrzésével.

## <a name="performance-insight"></a>Teljesítménybetekintés

Ez a szakasz ismerteti a teljesítményértékelési módszereket, majd felsorolja a teljesítményt befolyásoló összes tényezőt. Végül olyan módszereket biztosít, amelyek segítenek a teljesítménykövetelmények kiértékelésében.

### <a name="methodology"></a>Módszertan

*Átviteli és* *késés* két tipikus szempontból a teljesítmény-ellenőrzés. Az Azure SignalR-szolgáltatás, minden termékváltozat-szint saját átviteli sávszélesség-szabályozási szabályzattal rendelkezik. A házirend határozza meg *a maximálisan engedélyezett átviteli (bejövő és kimenő sávszélesség)* a maximálisan elért átviteli teljesítmény, ha az üzenetek 99 százaléka késés, amely kevesebb, mint 1 másodperc.

A késés az üzenet küldésétől az Azure SignalR-szolgáltatástól érkező válaszüzenet fogadásáig leadott időtartam. Vegyük **példának** Echo-t. Minden ügyfélkapcsolat időbélyegzőt ad hozzá az üzenethez. Az alkalmazáskiszolgáló központi központja visszaküldi az eredeti üzenetet az ügyfélnek. Így a propagálási késleltetés t könnyen kiszámítható minden ügyfél kapcsolat. Az időbélyegző a **szórásminden**üzenetéhez csatolva van, **elküldés a csoportnak**, és **elküldés a kapcsolatra.**

Több ezer egyidejű ügyfélkapcsolat szimulálása érdekében több virtuális gép jön létre egy virtuális magánhálózatban az Azure-ban. Ezek a virtuális gépek ugyanahhoz az Azure SignalR-szolgáltatáspéldányhoz csatlakoznak.

Az Azure SignalR Service alapértelmezett üzemmódjában az alkalmazáskiszolgáló im-ek ugyanabban a virtuális magánhálózatban vannak telepítve, mint az ügyfélvirtuális virtuális gépek. Az összes ügyfélvirtuális gép és az alkalmazáskiszolgáló virtuális gépe ugyanabban a régióban van telepítve, hogy elkerülje a régiók közötti késést.

### <a name="performance-factors"></a>Teljesítménytényezők

Elméletileg az Azure SignalR-szolgáltatás kapacitását számítási erőforrások korlátozzák: processzor, memória és hálózat. Például az Azure SignalR-szolgáltatással való további kapcsolatok hatására a szolgáltatás több memóriát használ. Nagyobb üzenetforgalom esetén (például minden üzenet nagyobb, mint 2048 bájt), az Azure SignalR Szolgáltatásnak több PROCESSZOR-ciklust kell költenie a forgalom feldolgozásához. Eközben az Azure hálózati sávszélessége is korlátozza a maximális forgalmat.

Az átviteli típus egy másik tényező, amely befolyásolja a teljesítményt. A három típus a [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [a Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)és a [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

A WebSocket egy kétirányú és teljes kétirányú kommunikációs protokoll egyetlen TCP-kapcsolaton keresztül. A Kiszolgáló által küldött esemény egy egyirányú protokoll, amely leküldi az üzeneteket a kiszolgálóról az ügyfélre. A Long-Polling megköveteli az ügyfelektől, hogy rendszeres időközönként lekérdezik az információkat a kiszolgálóról egy HTTP-kérelemen keresztül. Ugyanazaz API-t azonos feltételek mellett, WebSocket a legjobb teljesítményt, Server-Sent-Event lassabb, és a hosszú lekérdezési a leglassabb. Az Azure SignalR-szolgáltatás alapértelmezés szerint a WebSocket szolgáltatást ajánlja.

Az üzenetút-költség is korlátozza a teljesítményt. Az Azure SignalR szolgáltatás üzenetútválasztóként játszik szerepet, amely az ügyfelek vagy kiszolgálók egy csoportjából más ügyfelekhez vagy kiszolgálókhoz irányítja az üzenetet. Egy másik forgatókönyv vagy API más útválasztási szabályzatot igényel. 

A **echo**esetében az ügyfél üzenetet küld magának, és az útválasztási cél is maga. Ez a minta rendelkezik a legalacsonyabb útválasztási költség. De **a szórás**, **küldjön a csoportnak,** és **küldje el a kapcsolatot,** az Azure SignalR Service kell keresni a célkapcsolatokat a belső elosztott adatstruktúra. Ez az extra feldolgozás több PROCESSZOR-, memória- és hálózati sávszélességet használ. Ennek eredményeképpen a teljesítmény lassabb.

Az alapértelmezett módban az alkalmazáskiszolgáló bizonyos esetekben szűk keresztmetszetté is válhat. Az Azure SignalR SDK-nak meg kell hívnia a központot, miközben a szívverési jeleken keresztül minden ügyféllel élő kapcsolatot tart fenn.

Kiszolgáló nélküli módban az ügyfél HTTP-bejegyzéssel küld üzenetet, amely nem olyan hatékony, mint a WebSocket.

Egy másik tényező a protokoll: JSON és [MessagePack](https://msgpack.org/index.html). MessagePack kisebb méretű és szállított gyorsabb, mint a JSON. A MessagePack azonban nem javítja a teljesítményt. Az Azure SignalR-szolgáltatás teljesítménye nem érzékeny a protokollokra, mert nem dekódolja az üzenet hasznos adatátaz ügyfelekről a kiszolgálókra történő üzenettovábbítás során, vagy fordítva.

Összefoglalva, a következő tényezők befolyásolják a bejövő és kimenő kapacitást:

-   SKU-szint (CPU/memória)

-   Kapcsolatok száma

-   Üzenet mérete

-   Üzenet küldési sebessége

-   Átviteli típus (WebSocket, Kiszolgáló által küldött esemény vagy hosszú lekérdezés)

-   Használati eset (útválasztási költség)

-   Alkalmazáskiszolgáló- és szolgáltatáskapcsolatok (kiszolgáló módban)


### <a name="finding-a-proper-sku"></a>Megfelelő termékváltozat keresése

Hogyan értékelheti ki a bejövő/kimenő kapacitást, vagy hogyan találhatja meg, hogy melyik szint alkalmas egy adott használati esethez?

Tegyük fel, hogy az alkalmazáskiszolgáló elég erős, és nem a teljesítmény szűk keresztmetszete. Ezután ellenőrizze a maximális bejövő és kimenő sávszélesség minden réteg.

#### <a name="quick-evaluation"></a>Gyors értékelés

Egyszerűsítsük le a kiértékelést először néhány alapértelmezett beállítással: 

- Az átviteli típus WebSocket.
- Az üzenet mérete 2048 bájt.
- A rendszer 1 másodpercenként küld üzenetet.
- Az Azure SignalR szolgáltatás az alapértelmezett módban van.

Minden réteg saját maximális bejövő sávszélességgel és kimenő sávszélességgel rendelkezik. A zökkenőmentes felhasználói élmény nem garantált, miután a bejövő vagy kimenő kapcsolat túllépi a korlátot.

**Az Echo** a maximális bejövő sávszélességet adja meg, mivel a legalacsonyabb útválasztási költséggel rendelkezik. **A szórás** a kimenő üzenetek maximális sávszélességét határozza meg.

*Ne* lépje túl a következő két táblázatban kiemelt értékeket.

|       Echo                        | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| **Bejövő sávszélesség** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 Mb/s**    | **40 Mb/s**    | **100 Mb/s**   | **200 Mb/s**    |
| Kimenő sávszélesség | 2 MBps   | 4 MBps   | 10 MBps  | 20 Mb/s   | 40 Mb/s   | 100 Mb/s  | 200 Mb/s   |


|     Adás             | Egység1 | Egység2 | Egység5  | Egység10 | Egység20 | Egység50  | Egység100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20000 | 50 000  | 100 000 |
| Bejövő sávszélesség  | 4 Kb/s   | 4 Kb/s   | 4 Kb/s    | 4 Kb/s    | 4 Kb/s    | 4 Kb/s     | 4 Kb/s    |
| **Kimenő sávszélesség** | **4 MBps**    | **8 MBps**    | **20 Mb/s**    | **40 Mb/s**    | **80 Mb/s**    | **200 Mb/s**    | **400 Mb/s**   |

*A bejövő sávszélesség* és a *kimenő sávszélesség* az üzenet másodpercenkénti teljes mérete.  Itt vannak a képletek számukra:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Az üzenetet küldő kapcsolatok száma.

- *kimenő kapcsolatok:* Az üzenetet fogadó kapcsolatok száma.

- *messageSize*: Egyetlen üzenet mérete (átlagos érték). Egy 1024 bájtnál kisebb kis méretű üzenet teljesítményhatása hasonló az 1024 bájtos üzenethez.

- *sendInterval*: Egy üzenet elküldésének ideje. Általában ez 1 másodperc /üzenet, ami azt jelenti, hogy másodpercenként egy üzenetet küld. A kisebb időköz azt jelenti, hogy több üzenetet küld egy adott időszakban. Például 0,5 másodperc üzenetenként azt jelenti, hogy másodpercenként két üzenetet küld.

- *Kapcsolatok:* Az Azure SignalR-szolgáltatás véglegesített maximális küszöbértéke minden réteghez. Ha a kapcsolat száma tovább növekszik, akkor a kapcsolat szabályozása szenved.

#### <a name="evaluation-for-complex-use-cases"></a>Összetett használati esetek értékelése

##### <a name="bigger-message-size-or-different-sending-rate"></a>Nagyobb üzenetméret vagy eltérő küldési sebesség

Az igazi felhasználási eset bonyolultabb. Előfordulhat, hogy 2048 bájtnál nagyobb üzenetet küld, vagy a küldő üzenetek sebessége nem másodpercenként egy üzenet. Vegyük például a Unit100 közvetítését, hogy megtudjuk, hogyan értékelhetjük a teljesítményét.

Az alábbi táblázat a közvetítés valós használati esetét mutatja **be.** Az üzenetek mérete, a kapcsolatok száma és az üzenetek küldési sebessége azonban eltér az előző szakaszban feltételezetttől. A kérdés az, hogyan tudjuk következtetni bármely ilyen elem (üzenet mérete, kapcsolat száma, vagy üzenet küldési arány), ha tudjuk, csak kettő közülük.

| Adás  | Üzenet mérete | Bejövő üzenetek másodpercenként | Kapcsolatok | Időközök küldése |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 másodperc                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 másodperc                      |

A következő képlet könnyen kikövetkeztethető az előző képlet alapján:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

A Unit100 esetében a maximális kimenő sávszélesség 400 MB az előző táblához képest. 20 KB-os üzenetméret esetén a kimenő kapcsolatok maximális \* mérete 400 MB 5 / 20 KB = 100 000, amely megfelel a valós értéknek.

##### <a name="mixed-use-cases"></a>Vegyes felhasználású esetek

A valós használati eset általában a négy alapvető használati esetet keveri össze: **echo**, **broadcast**, send **to group**, and send **to connection**. A kapacitás értékelésére használt módszer a következő:

1. A vegyes használati eseteket négy alaphasználati esetre osztja fel.
1. Számítsa ki a bejövő és kimenő üzenetek maximális sávszélességét az előző képletek külön használatával.
1. Összegezze a sávszélesség-számításokat a teljes maximális bejövő/kimenő sávszélesség leszámításához. 

Ezután vegye fel a megfelelő réteget a maximális bejövő/kimenő sávszélesség-táblákból.

> [!NOTE]
> Ha több száz vagy több ezer kisebb csoportnak, vagy több ezer ügyfélnek küld üzenetet egymásnak, az útválasztási költség lesz domináns. Vegye figyelembe ezt a hatást.

Az ügyfeleknek küldött üzenet használatának esetében győződjön meg arról, hogy *nem* az alkalmazáskiszolgáló jelenti a szűk keresztmetszetet. A következő "Esettanulmány" című rész útmutatást ad arról, hogy hány alkalmazáskiszolgálóra van szüksége, és hány kiszolgálókapcsolatot kell konfigurálnia.

## <a name="case-study"></a>Esettanulmány

A következő szakaszok négy tipikus használati eseten mennek keresztül a WebSocket átvitelhez: **echo**, **broadcast**, send **to group**, and send **to connection**. Minden forgatókönyv esetén a szakasz felsorolja az Azure SignalR-szolgáltatás aktuális bejövő és kimenő kapacitását. Azt is magyarázza a fő tényezők, amelyek befolyásolják a teljesítményt.

Az alapértelmezett módban az alkalmazáskiszolgáló öt kiszolgálókapcsolatot hoz létre az Azure SignalR Szolgáltatással. Az alkalmazáskiszolgáló alapértelmezés szerint az Azure SignalR Service SDK-t használja. A következő teljesítményteszt-eredményekben a kiszolgálókapcsolatok száma 15-re nő (vagy többre a műsorszóráshoz és egy nagy csoportnak küldött üzenet küldéséhez).

A különböző használati esetek eltérő követelményeket támasztanak az alkalmazáskiszolgálókra vonatkozóan. **A szóráshoz** kis számú alkalmazáskiszolgálóra van szükség. **Echo** vagy **küldjön a kapcsolathoz** sok alkalmazásszervert igényel.

Minden használati esetben az alapértelmezett üzenetméret 2048 bájt, az üzenet küldési időköze pedig 1 másodperc.

### <a name="default-mode"></a>Alapértelmezett mód

Az ügyfelek, a webalkalmazás-kiszolgálók és az Azure SignalR-szolgáltatás az alapértelmezett módban vesznek részt. Minden ügyfél egyetlen kapcsolatot képvisel.

#### <a name="echo"></a>Echo

Először egy webalkalmazás csatlakozik az Azure SignalR szolgáltatáshoz. Másodszor, sok ügyfél csatlakozik a webalkalmazáshoz, amely átirányítja az ügyfeleket az Azure SignalR Service a hozzáférési jogkivonatot és a végpontot. Ezután az ügyfelek websocket-kapcsolatokat létesítenek az Azure SignalR Szolgáltatással.

Miután az összes ügyfél kapcsolatokat létesít, elkezdenek egy üzenetet küldeni, amely minden másodpercben időbélyegzőt tartalmaz az adott hubra. A hub visszaismétli az üzenetet az eredeti ügyfélnek. Minden ügyfél kiszámítja a késést, amikor visszakapja a visszhangüzenetet.

A következő ábrán 5-8 (piros kiemelt forgalom) van egy hurok. A hurok fut egy alapértelmezett időtartam (5 perc), és lekéri a statisztika az összes üzenet késés.

![A visszhanghasználati eset forgalma](./media/signalr-concept-performance/echo.png)

A **visszhang** viselkedése azt határozza meg, hogy a maximális bejövő sávszélesség megegyezik a maximális kimenő sávszélességgel. A részletekért lásd az alábbi táblázatot.

|       Echo                        | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Bejövő/kimenő üzenetek másodpercenként | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Bejövő/kimenő sávszélesség | 2 MBps   | 4 MBps   | 10 MBps  | 20 Mb/s   | 40 Mb/s   | 100 Mb/s  | 200 Mb/s   |

Ebben a használati esetben minden ügyfél meghívja az alkalmazáskiszolgálón definiált hubot. A hub csak meghívja az eredeti ügyféloldalon definiált metódust. Ez a hub a legkönnyebb hub **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Még ezen az egyszerű hub, a forgalom nyomása az alkalmazás szerver kiemelkedő, mint a **visszhang** bejövő üzenet terhelés növekszik. Ez a forgalmi nyomás számos alkalmazáskiszolgálót igényel a nagy Termékváltozat-szintekhez. Az alábbi táblázat felsorolja az alkalmazáskiszolgálók számát minden réteghez.


|    Echo          | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az alkalmazáskiszolgáló ügyfélkapcsolati száma, üzenetmérete, üzenetküldési sebessége, Termékváltozat-szintje és PROCESSZOR/memória hatása a **visszhang**általános teljesítményére.

#### <a name="broadcast"></a>Adás

Szórás **esetén,** amikor a webalkalmazás megkapja az üzenetet, az összes ügyfélnek sugároz. Minél több ügyfelet kell szórásra, annál több üzenetforgalom van az összes ügyfél számára. Lásd az alábbi diagramot.

![A műsorszóráshasználati eset forgalma](./media/signalr-concept-performance/broadcast.png)

Néhány ügyfél sugároz. A bejövő üzenet sávszélessége kicsi, de a kimenő sávszélesség hatalmas. A kimenő üzenet sávszélessége az ügyfélkapcsolat vagy a szórási sebesség növekedésével növekszik.

Az alábbi táblázat a maximális ügyfélkapcsolatokat, a bejövő/kimenő üzenetek számát és a sávszélességet foglalja össze.

|     Adás             | Egység1 | Egység2 | Egység5  | Egység10 | Egység20 | Egység50  | Egység100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20000 | 50 000  | 100 000 |
| Bejövő üzenetek másodpercenként  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 20000 | 40,000 | 100 000 | 200,000 |
| Bejövő sávszélesség  | 4 Kb/s   | 4 Kb/s   | 4 Kb/s    | 4 Kb/s    | 4 Kb/s    | 4 Kb/s     | 4 Kb/s     |
| Kimenő sávszélesség | 4 MBps   | 8 MBps   | 20 Mb/s   | 40 Mb/s   | 80 Mb/s   | 200 Mb/s   | 400 Mb/s   |

Az üzeneteket közzévevő műsorszóró ügyfelek legfeljebb négyek. Kevesebb alkalmazáskiszolgálóra van szükségük a **visszhanghoz képest,** mert a bejövő üzenetek mennyisége kicsi. Két alkalmazáskiszolgáló elegendő mind az SLA- és a teljesítménybeli szempontokhoz. De meg kell növelni az alapértelmezett kiszolgálói kapcsolatokat, hogy elkerülje a kiegyensúlyozatlanságot, különösen a Unit50 és unit100 esetében.

|   Adás      | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Növelje az alapértelmezett kiszolgálói kapcsolatokat 5-ről 40-re minden alkalmazáskiszolgálón, hogy elkerülje az Azure SignalR Szolgáltatással való esetleges kiegyensúlyozatlan kiszolgálói kapcsolatokat.
>
> Az ügyfélkapcsolat száma, az üzenetmérete, az üzenetküldési sebesség és a Termékváltozat-szint befolyásolja a **szórás**általános teljesítményét.

#### <a name="send-to-group"></a>Küldés a csoportnak

A **küldés a csoporthasználati** esetnek hasonló forgalmi mintája **van.** A különbség az, hogy miután az ügyfelek websocket-kapcsolatokat létesítenek az Azure SignalR Szolgáltatással, csatlakozniuk kell a csoportokhoz, mielőtt üzenetet küldhetnének egy adott csoportnak. Az alábbi ábra a forgalom áramlását mutatja be.

![A csoportba küldési használati eset forgalma](./media/signalr-concept-performance/sendtogroup.png)

A csoporttag és a csoportszám két tényező, amelyek befolyásolják a teljesítményt. Az elemzés egyszerűsítése érdekében kétféle csoportot határozunk meg:

- **Kiscsoport**: Minden csoportnak 10 kapcsolata van. A csoportszám egyenlő (maximális kapcsolatszám) / 10. Például a Unit1 esetében, ha 1000 kapcsolatszám van, akkor 1000 / 10 = 100 csoportunk van.

- **Nagy csoport**: A csoport száma mindig 10. A csoporttagok száma egyenlő (maximális kapcsolatszám) / 10. Például a Unit1 esetében, ha 1000 kapcsolat számít, akkor minden csoportnak 1000 / 10 = 100 tagja van.

**A küldés a csoportnak** útválasztási költséget hoz az Azure SignalR Szolgáltatásba, mert a célkapcsolatokat elosztott adatstruktúrán keresztül kell megtalálnia. A küldő kapcsolatok növekedésével a költségek növekednek.

##### <a name="small-group"></a>Kis csoport

Az útválasztási költség jelentős az üzenet küldéséhez sok kis csoportnak. Jelenleg az Azure SignalR service implementációja eléri az útválasztási költségkorlátot a Unit50-nél. Több processzor és memória hozzáadása nem segít, így a Unit100 nem tud tovább fejlődni. Ha nagyobb bejövő sávszélességre van szüksége, forduljon az ügyfélszolgálathoz.

|   Küldés kis csoportnak     | Egység1 | Egység2 | Egység5  | Egység10 | Egység20 | Egység50 | Egység100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20000 | 50 000 | 100 000
| Csoporttagok száma        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Csoportszám               | 100   | 200   | 500    | 1,000  | 2000  | 5000  | 10,000 
| Bejövő üzenetek másodpercenként  | 200   | 400   | 1,000  | 2500  | 4,000  | 7,000  | 7,000   |
| Bejövő sávszélesség  | 400 Kb/s  | 800 Kb/s  | 2 MBps     | 5 MBps     | 8 MBps     | 14 Mb/s    | 14 Mb/s     |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Kimenő sávszélesség | 4 MBps    | 8 MBps    | 20 Mb/s    | 50 Mb/s     | 80 Mb/s    | 140 Mb/s   | 140 Mb/s    |

Számos ügyfélkapcsolat hívja a központot, így az alkalmazáskiszolgáló száma is kritikus fontosságú a teljesítmény szempontjából. Az alábbi táblázat felsorolja a javasolt alkalmazáskiszolgálók számát.

|  Küldés kis csoportnak   | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfélkapcsolat száma, az üzenet mérete, az üzenetküldési sebesség, az útválasztási költség, a Termékváltozat szintje és az alkalmazáskiszolgáló PROCESSZOR/memória hatása befolyásolja a **kis csoportnak küldés**általános teljesítményét.

##### <a name="big-group"></a>Nagy csoport

Nagy **csoportba küldés**esetén a kimenő sávszélesség szűk keresztmetszetté válik, mielőtt elérné az útválasztási költségkorlátot. Az alábbi táblázat a maximális kimenő sávszélességet sorolja fel, amely majdnem megegyezik a **szórási**sávszélességével.

|    Küldés nagy csoportnak      | Egység1 | Egység2 | Egység5  | Egység10 | Egység20 | Egység50  | Egység100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20000 | 50 000  | 100 000
| Csoporttagok száma        | 100   | 200   | 500    | 1,000  | 2000  | 5000   | 10,000 
| Csoportszám               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Bejövő üzenetek másodpercenként  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Bejövő sávszélesség  | 80 Kb/s   | 40 Kb/s   | 40 Kb/s    | 20 Kb/s    | 40 Kb/s    | 40 Kb/s     | 40 Kb/s     |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 20000 | 40,000 | 100 000 | 200,000 |
| Kimenő sávszélesség | 8 MBps    | 8 MBps    | 20 Mb/s    | 40 Mb/s    | 80 Mb/s    | 200 Mb/s    | 400 Mb/s    |

A küldő kapcsolatok száma nem több 40-nél. Az alkalmazáskiszolgálóra nehezedő teher kicsi, így a javasolt számú webalkalmazás kicsi.

|  Küldés nagy csoportnak  | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Növelje az alapértelmezett kiszolgálói kapcsolatokat 5-ről 40-re minden alkalmazáskiszolgálón, hogy elkerülje az Azure SignalR Szolgáltatással való esetleges kiegyensúlyozatlan kiszolgálói kapcsolatokat.
> 
> Az ügyfélkapcsolat száma, az üzenet mérete, az üzenetküldési sebesség, az útválasztási költség és a Termékváltozat-szint befolyásolja a **nagy csoportnak küldés**általános teljesítményét.

#### <a name="send-to-connection"></a>Küldés a kapcsolatra

A **küldés** a kapcsolat használati esetben, amikor az ügyfelek létre hozzák a kapcsolatot az Azure SignalR Szolgáltatás, minden ügyfél hív egy speciális hub, hogy saját kapcsolatazonosítóját. A teljesítményteszt összegyűjti az összes kapcsolatazonosítót, lekeveri őket, és újra hozzárendeli őket az összes ügyfélhez küldő célként. Az ügyfelek folyamatosan küldik az üzenetet a célkapcsolatnak, amíg a teljesítményteszt be nem fejeződik.

![Az ügyfélnek küldendő használati eset forgalma](./media/signalr-concept-performance/sendtoclient.png)

A **kapcsolatra küldött útválasztási** költség hasonló a **kis csoportnak küldés**költségéhez.

A kapcsolatszám növekedésével az útválasztási költség korlátozza az általános teljesítményt. Unit50 elérte a határt. Ennek eredményeképpen a Unit100 nem tud tovább fejlődni.

A következő táblázat egy statisztikai összefoglaló a **küldés a kapcsolati referenciaértékhez** futtatása után.

|   Küldés a kapcsolatra   | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50          | Egység100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Kapcsolatok                        | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000          | 100 000         |
| Bejövő/kimenő üzenetek másodpercenként | 1,000 | 2000 | 5000 | 8,000  | 9000  | 20000 | 20000 |
| Bejövő/kimenő sávszélesség | 2 MBps    | 4 MBps    | 10 MBps   | 16 Mb/s    | 18 Mb/s    | 40 Mb/s       | 40 Mb/s       |

Ez a használati eset nagy terhelést igényel az alkalmazás kiszolgálói oldalán. Tekintse meg a javasolt alkalmazáskiszolgálók számát az alábbi táblázatban.

|  Küldés a kapcsolatra  | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az alkalmazáskiszolgáló ügyfélkapcsolati száma, üzenetmérete, üzenetküldési sebessége, útválasztási költsége, Termékváltozat-szint és CPU/memória befolyásolja a **kapcsolatra való küldés**általános teljesítményét.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR visszhang, adás, és küldje el a kis csoport

Az Azure SignalR szolgáltatás ugyanazt a teljesítménykapacitást biztosítja ASP.NET SignalR számára. 

A teljesítményteszt az Azure Web Apps standard service plan S3 for ASP.NET SignalR.The performance test uses Azure Web Apps from [Standard Service Plan S3](https://azure.microsoft.com/pricing/details/app-service/windows/) for ASP.NET SignalR.

Az alábbi táblázat a javasolt webalkalmazások számát adja ASP.NET SignalR **echo**számára.

|   Echo           | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Az alábbi táblázat a javasolt webalkalmazások számát adja meg ASP.NET **SignalR-szóráshoz.**

|  Adás       | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Az alábbi táblázat a javasolt webalkalmazások számát adja meg ASP.NET SignalR **kis csoportnak küldéséhez.**

|  Küldés kis csoportnak     | Egység1 | Egység2 | Egység5 | Egység10 | Egység20 | Egység50 | Egység100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20000 | 50 000 | 100 000 |
| Alkalmazáskiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Kiszolgáló nélküli mód

Az ügyfelek és az Azure SignalR szolgáltatás kiszolgáló nélküli módban vesznek részt. Minden ügyfél egyetlen kapcsolatot képvisel. Az ügyfél üzeneteket küld a REST API-n keresztül egy másik ügyfélnek, vagy mindenkinek küldüzeneteket.

Nagy sűrűségű üzenetek küldése a REST API-n keresztül nem olyan hatékony, mint a WebSocket használata. Ez megköveteli, hogy hozzon létre egy új HTTP-kapcsolat minden alkalommal, és ez egy extra költség kiszolgáló nélküli módban.

#### <a name="broadcast-through-rest-api"></a>Közvetítés a REST API-n keresztül
Minden ügyfél websocket-kapcsolatokat létesít az Azure SignalR szolgáltatással. Ezután egyes ügyfelek megkezdik a KÖZVETÍTÉSt a REST API-n keresztül. Az üzenetküldés (bejövő) a HTTP-bejegyzésen keresztül történik, ami nem hatékony a WebSocket hez képest.

|   Közvetítés a REST API-n keresztül     | Egység1 | Egység2 | Egység5  | Egység10 | Egység20 | Egység50  | Egység100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20000 | 50 000  | 100 000 |
| Bejövő üzenetek másodpercenként  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 20000 | 40,000 | 100 000 | 200,000 |
| Bejövő sávszélesség  | 4 Kb/s    | 4 Kb/s    | 4 Kb/s     | 4 Kb/s     | 4 Kb/s     | 4 Kb/s      | 4 Kb/s      |
| Kimenő sávszélesség | 4 MBps    | 8 MBps    | 20 Mb/s    | 40 Mb/s    | 80 Mb/s    | 200 Mb/s    | 400 Mb/s    |

#### <a name="send-to-user-through-rest-api"></a>Küldés a felhasználónak a REST API-n keresztül
A benchmark hozzárendeli a felhasználóneveket az összes ügyfélhez, mielőtt elkezdené az Azure SignalR szolgáltatáshoz való csatlakozást. Miután az ügyfelek websocket-kapcsolatokat létesítenek az Azure SignalR Szolgáltatással, http-bejegyzésen keresztül kezdenek el üzeneteket küldeni másoknak.

|   Küldés a felhasználónak a REST API-n keresztül | Egység1 | Egység2 | Egység5  | Egység10 | Egység20 | Egység50  | Egység100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20000 | 50 000  | 100 000 |
| Bejövő üzenetek másodpercenként  | 300   | 600   | 900    | 1,300  | 2000  | 10,000  | 18000  |
| Kimenő üzenetek másodpercenként | 300   | 600   | 900    | 1,300  | 2000  | 10,000  | 18000 |
| Bejövő sávszélesség  | 600 K/s  | 1,2 Mb/s  | 1,8 Mb/s   | 2,6 Mb/s   | 4 MBps     | 10 MBps     | 36 Mb/s    |
| Kimenő sávszélesség | 600 K/s  | 1,2 Mb/s  | 1,8 Mb/s   | 2,6 Mb/s   | 4 MBps     | 10 MBps     | 36 Mb/s    |

## <a name="performance-test-environments"></a>Teljesítményteszt-környezetek

A korábban felsorolt összes használati esetesetében elvégeztük a teljesítményteszteket egy Azure-környezetben. Legkorábban 50 ügyfél virtuális gépet és 20 alkalmazáskiszolgálói virtuális gépet használtunk. Íme néhány részlet:

- Ügyfél virtuális gép mérete: StandardDS2V2 (2 vCPU, 7G memória)

- Alkalmazáskiszolgáló virtuális gépmérete: StandardF4sV2 (4 vCPU, 8G memória)

- Azure SignalR SDK-kiszolgálókapcsolatok: 15

## <a name="performance-tools"></a>Teljesítményeszközök

Az Azure SignalR-szolgáltatás teljesítményeszközeit a [GitHubon](https://github.com/Azure/azure-signalr-bench/)találja.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure SignalR-szolgáltatás teljesítményét a tipikus használati esetekben.

A szolgáltatás belső részleteinek és méretezésének megismeréséhez olvassa el az alábbi útmutatókat:

* [Az Azure SignalR szolgáltatás belső elemei](signalr-concept-internals.md)
* [Az Azure SignalR szolgáltatás méretezése](signalr-howto-scale-multi-instances.md)
