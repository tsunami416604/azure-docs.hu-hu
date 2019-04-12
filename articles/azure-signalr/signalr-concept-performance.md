---
title: Az Azure SignalR Service teljesítmény-útmutató
description: Az Azure SignalR Service teljesítmény áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502038"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Az Azure SignalR Service teljesítmény-útmutató

Az Azure SignalR Service segítségével előnyei egyik SignalR-alkalmazások könnyű. A nagy méretű forgatókönyvekben teljesítmény lesz fontos tényező. Ez az útmutató a tényezőket, amelyek hatással van a SignalR alkalmazásteljesítmény, és a különböző használati eseteit, mi az az általános teljesítmény fog bevezettünk? A végén azt is vezet be a környezetet és a teljesítményadat-jelentés létrehozásához használt eszközök.

## <a name="terms-definition"></a>Feltételek meghatározása

*ASRS*: Azure SignalR szolgáltatás

*Bejövő*: a bejövő üzenetben az Azure SignalR Service

*Kimenő*: az Azure SignalR Service kimenő üzenete

*A sávszélesség*: üzenetek mérete összesen 1 másodperc múlva

*Alapértelmezett mód*: ASRS ügyfélkapcsolatok elfogadása előtt vele kapcsolatot létesíteni az alkalmazáskiszolgáló vár. Egy ASRS létrehozásakor az alapértelmezett működő módban.

*Kiszolgáló nélküli módban*: ASRS csak ügyfélkapcsolatokat fogad el. Nincs kapcsolat a kiszolgálóval használata engedélyezett.

## <a name="overview"></a>Áttekintés

ASRS eltérő teljesítménybeli kapacitás hét Standard szint határozza meg, és ez az útmutató szándékozik a következő kérdések megválaszolásával:

-   Mi az a jellemző ASRS teljesítmény az egyes szintek?

-   ASRS felel meg a követelménynek üzeneteinek átviteli sebessége, például a 100 000, másodpercenként üzenetek küldésére?

-   Adott forgatókönyvhöz, melyik szint a megfelelő a számomra? És hogyan lehet ki a megfelelő szint?

-   Milyen típusú alkalmazások kiszolgálói (virtuális gép mérete) megfelelő a számomra, és ezek közül hány kell helyezhetek üzembe?

Válaszol az alábbi kérdésekre, a teljesítmény-útmutató először biztosít, amelyek hatással van a teljesítmény összefoglaló jellegű ismertetése, majd minden szinten a tipikus használati esetek maximális bejövő és kimenő üzenetek mutatja be: **echo**, **adás**, **küldés-csoport**, és **küldése a betöltések és kapcsolatok** (társ-társ Csevegés).

Nem lehet a jelen dokumentum ahhoz, hogy biztosítsák az összes forgatókönyveket (és különböző használati esetekhez, különböző üzenetméret vagy üzenetet küldő minta stb.). Azonban néhány kiértékelés módszerek segítséget nyújtson a felhasználóknak körülbelül kiértékelése a bejövő vagy kimenő üzenetek követelményeknek, majd keresse meg a megfelelő szint a teljesítménytáblája ellenőrzésével biztosít.

## <a name="performance-insight"></a>Terheléselemző

Ez a szakasz azt ismerteti, a teljesítmény módszertanok, majd felsorolja az összes olyan tényezőt, amely hatással van a teljesítményre. A végén a teljesítmény-követelmények felmérése segítségével módszert is biztosít.

### <a name="methodology"></a>Módszer

**Átviteli sebesség** és **késés** két tipikus szempontból teljesítményének ellenőrzése. Különböző Termékváltozat-szint ASRS, a házirend-szabályozás különböző átviteli rendelkezik. Ez a dokumentum **a maximálisan engedélyezett átviteli sebesség (bejövő és kimenő sávszélesség)** , a maximális átviteli sebesség érhető el, ha 99 %-üzenetek késéssel rendelkeznek, kevesebb mint 1 másodperc.

A késés az időtartam az üzenet küldése a válaszüzenet fogadása ASRS kapcsolatról. Vessünk **echo** tegyük fel, minden ügyfél-csatlakozási időbélyeg hozzáadja az üzenetben. Kiszolgálói alkalmazás elosztóhoz az eredeti üzenet elküldi az ügyfélnek. A propagálás késleltetés így könnyen minden ügyfélkapcsolat számolható ki. Történő küldés időbélyegzője legyen csatolva van minden üzenetet **adás**, **küldés-csoport**, és **küldése a betöltések és kapcsolatok**.

A több ezer egyidejű kapcsolatok szimulálásához több virtuális gép létrehozása az Azure-beli virtuális magánhálózati. Az összes ilyen virtuális gépek csatlakozni ASRS ugyanezen példányában.

ASRS alapértelmezett módban app server virtuális gépek üzembe helyezett virtuális gépek ügyfélként ugyanazon virtuális privát hálózaton.

Az összes ügyfél virtuális gépek és alkalmazások kiszolgálói közötti terület késés elkerülése érdekében ugyanabban a régióban, ugyanabban a hálózatban a virtuális gépek üzembe.

### <a name="performance-factors"></a>Teljesítmény tényezők

Elméletileg ASRS kapacitás korlátozott számítási erőforrásai által: CPU, memória és hálózati. Ha például ASRS további kapcsolatokat, több memória ASRS használja fel. A nagyobb méretű üzenet forgalmat például minden üzenet mérete nagyobb 2048 bájt, ASRS további processzorciklusok feldolgozni, valamint a szükséges. Az Azure hálózati sávszélességet, is maximális forgalom korlátot ír elő.

Az átviteli típust [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [kiszolgáló küldött események](https://en.wikipedia.org/wiki/Server-sent_events), vagy [hosszú lekérdezéseket](https://en.wikipedia.org/wiki/Push_technology), egy másik tényező befolyásolja teljesítmény. WebSocket egyetlen TCP-kapcsolaton keresztül kétirányú és kétirányú kommunikációs protokoll. Azonban amelyen sincs elküldött esemény az egyirányú protokoll leküldéses üzenet a kiszolgáló. Hosszú lekérdezéseket igényel az ügyfelek és rendszeres időközönként lekérdezi a kiszolgáló HTTP-kérés adatait. Ugyanez a feltétel alapján, az azonos API WebSocket rendelkezik a lehető legjobb teljesítményt, amelyen sincs elküldött esemény lassabb lesz, és hosszú lekérdezéseket a leglassabb. ASRS WebSocket alapértelmezés szerint használatát javasolja.

Emellett az üzenet-útválasztási költség is korlátozza a teljesítményt. ASRS szerepet játszik az olyan, egy üzenet útválasztó, amely az ügyfelek vagy kiszolgálók közül az üzenet más ügyfelek vagy kiszolgálók irányítja. Más-más forgatókönyvet vagy API-t igényel a különböző útválasztási házirend. A **echo**, az ügyfél egy üzenetet küld saját maga és az útválasztási cél önmagában is. Ehhez a mintához a legkisebb útválasztási költséget. De a **adás**, **küldés-csoport**, **küldése a betöltések és kapcsolatok**, ASRS van szüksége, keresse ki a cél-kapcsolatok keresztül a belső elosztott adatszerkezet, amely több Processzor, memória és még a hálózati sávszélességet használ fel. Ennek eredményeképpen a teljesítmény lassabb, mint a **echo**.

Az alapértelmezett módban alkalmazáskiszolgáló is válhat a szűk keresztmetszet, bizonyos forgatókönyvek esetén, mert az Azure SignalR SDK meghívni a Hub meanwhile karbantartott minden ügyfél-szívverés jelek keresztül élő kapcsolattal rendelkezik.

Kiszolgáló nélküli módban az ügyfél által a HTTP post, amely nem annyira hatékony, mint a WebSocket üzenetet küld.

Egy másik tényező, protokoll: JSON és [MessagePack](https://msgpack.org/index.html). MessagePack JSON-kal gyorsabbak, kisebb méretű és kézbesített. Intuitív MessagePack kiaknázhatják a teljesítmény, de ASRS teljesítménye nem érzékeny protokollokkal óta nem dekódolható az üzenet hasznos adattartalmából kiszolgálók vagy fordítva ügyfelektől érkező üzenetek továbbítása során.

Összefoglalva az alábbi tényezők hatással van a bejövő és kimenő kapacitása:

-   Termékváltozat-szint (CPU/memória)

-   Kapcsolatok száma

-   Üzenet mérete

-   üzenet küldések

-   transport type (WebSocket/Sever-Sent-Event/Long-Polling)

-   használatieset-forgatókönyvek (útválasztási költsége)

-   alkalmazás-kiszolgáló és a szolgáltatás kapcsolatok (a kiszolgáló mód)


### <a name="find-a-proper-sku"></a>Keresse meg a megfelelő Termékváltozat

A bejövő/kimenő kapacitás kiértékelése vagy hogyan találhatja meg, melyik szint egy meghatározott alkalmazási helyzet ideális?

Feltételezzük, hogy az alkalmazás-kiszolgáló a megfelelő teljesítmény, és nem a teljesítménybeli szűk keresztmetszetek. Ezután ellenőrizheti, hogy minden szinthez tartozó maximális bejövő és kimenő sávszélesség.

#### <a name="quick-evaluation"></a>Gyors kipróbálási

Nézzük egyszerűsítheti az értékelés először úgy vesszük, hogy néhány alapértelmezett beállításokat: WebSocket szolgál, mérete 2048 bájt 1 másodpercenként üzenetet küld, és az alapértelmezett módban.

Minden szint tartalmaz, a saját maximális bejövő és kimenő sávszélesség. Zökkenőmentes felhasználói élmény nem garantált, ha a bejövő vagy kimenő meghaladja a korlátot.

**Echo** biztosítja a legnagyobb bejövő sávszélesség, a legkisebb útválasztási költségeket, mert. **Szórási** határozza meg a kimenő üzenetek maximális sávszélesség.

Tegye **nem** haladhatja meg az alábbi két táblázatban a kijelölt értékeket.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| **Bejövő sávszélesség (bájt/s)** | **2M**    | **4M**    | **10M**   | **20M**    | **40M**    | **100M**   | **200M**    |
| Kimenő sávszélesség (bájt/s) | 2M    | 4M    | 10M   | 20M    | 40M    | 100M   | 200M    |


|     Szórás             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő sávszélesség (bájt/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Kimenő sávszélesség (bájt/s)** | **4M**    | **8M**    | **20M**    | **40M**    | **80M**    | **200M**    | **400M**   |

A bejövő sávszélesség és a kimenő sávszélesség képletek:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: üzenet küldésekor kapcsolatok száma

*outboundConnections*: üzenet fogadása kapcsolatok száma

*messageSize*: egy adott üzenet (átlag) méretét. Kis méretű üzenet, amelynek mérete legalább 1024 bájt hasonló teljesítményre gyakorolt mint 1024 bájt méretű üzenet rendelkezik.

*sendInterval*: egy üzenetet küld az ideje, általában egy üzenet, ami azt jelenti, egy üzenetet küld, másodpercenként 1 másodperc. Kisebb sendInterval azt jelenti, hogy több üzenetet küld adott időszakban. Például egy üzenet 0,5 második azt jelenti, hogy két üzenetküldésre másodpercenként.

*Kapcsolatok* minden szinthez ASRS lefoglalt maximális küszöbértéke van. A kapcsolat a szám tovább növekszik, ha a kapcsolat szabályozás romlik.

*Bejövő sávszélesség* és *kimenő sávszélesség* vannak a teljes üzenetmérete másodpercenként. Itt vagyok "azt jelenti, hogy megabájtban kifejezett az egyszerűség kedvéért.

#### <a name="evaluation-for-complex-use-cases"></a>Kiértékelés a komplex használati esetek

##### <a name="bigger-message-size-or-different-sending-rate"></a>Nagyobb méretű üzenetek mérete vagy a különböző küldési gyakorisága

Valós használati eset jóval összetettebb. Nagyobb, mint 2048 bájt továbbíthatja üzenet, vagy a küldő üzenetküldési gyakoriság nem nem egy üzenet / másodperc. Vegyük unit100 a közvetítés példaként, hogyan lehet a teljesítmény kiértékelése.

Az alábbi táblázat egy valódi esetet **adás**, de az üzenet mérete a, kapcsolatok száma és sebessége üzenetküldést különböznek a mi azt feltételezi, az előző szakaszban. A kérdés, hogy hogyan tudjuk is kikövetkeztetni bármelyik azok az elemek (üzenet mérete, kapcsolatszáma vagy üzenet küldés sebességét) Ha tudjuk, hogy csak azokat az 2.

| Szórás  | Üzenet mérete (bájt) | Bejövő (üzenet/mp) | Kapcsolatok | Időközönként (második) küldése |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100 000     | 5                       |
| 2 | 256 K                | 1                        | 8,000       | 5                       |

Nelze odvodit meglévő képlet alapján a következő képlet egyszerűen:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

A unit100, tudjuk, hogy a 400 millió a maximális kimenő sávszélesség az előző táblázatban, majd 20-K üzenet mérete, a kimenő kapcsolatok maximális kell 400 millió \* 5 és 20 K = 100 000, amely a tényleges érték.

##### <a name="mixed-use-cases"></a>Vegyes alkalmazási helyzetek

Valós használati eset általában az együtt a négy alapvető használati esetek eredményét: **echo**, **adás**, **Küldés csoportba**, vagy **küldeni a kapcsolat**. A kapacitás kiértékeléséhez használt módszer az, hogy osztani a vegyes használati esetek négy alapvető használati esetek, **a bejövő és kimenő üzenetek maximális sávszélesség kiszámításához** a fenti képletek külön-külön használ, és a számukra, hogy a teljes összeg legnagyobb bejövő/kimenő sávszélesség. Ezután vegyen fel a megfelelő szint a legnagyobb bejövő/kimenő sávszélesség táblából.

Eközben üzenetet küld vagy kisebb csoportok több ezer, akár több százezer üzenetet küld az egyes ügyfelek, az útválasztási költség lesz domináns. A hatás figyelembe kell venni. További részleteket a következő szakaszokban "Esettanulmány" terjed ki.

Az üzenetet küld az ügyfelek használati esetekhez, ellenőrizze, hogy az alkalmazás-kiszolgáló **nem** a szűk keresztmetszetet. "Esettanulmány" szakaszban biztosít a arról, hány alkalmazás-kiszolgálókkal kapcsolatos van szüksége, és hány kiszolgálókapcsolatok kell konfigurálni.

## <a name="case-study"></a>Esettanulmány

A következő szakaszok haladjon végig a WebSocket átviteli a tipikus használati esetek négy: **echo**, **adás**, **küldés-csoport**, és **küldése a betöltések és kapcsolatok** . A minden esetben felsorolja az aktuális ASRS bejövő és kimenő kapacitás csapattagok pedig nyugodtabban aludhatnak elmagyarázza, mit főbb tényezőket a teljesítményre.

Az alapértelmezett módban alkalmazáskiszolgáló keresztül az Azure SignalR Service SDK alapértelmezés szerint hoz létre öt kiszolgálókapcsolatok ASRS. A teljesítmény teszteléséhez eredmény az alábbi, 15 (vagy több üzenetküldéssel és big Data típusú csoport üzenet küldése) kapcsolatok megnöveltük kiszolgáló.

Különböző használati helyzetekhez különböző követelmény alkalmazáskiszolgálókra rendelkezik. **Szórási** kis számú alkalmazást kiszolgálóra van szüksége. **Echo** vagy **küldése a betöltések és kapcsolatok** sok alkalmazás kiszolgáló szükséges.

Az összes használati esetek, az alapértelmezett üzenet mérete 2048 bájt, és üzenet küldési időköze 1 másodperc.

## <a name="default-mode"></a>Alapértelmezett mód

Ügyfelek, a webalkalmazás-kiszolgálók és a ASRS ebben a módban is érint. Minden ügyfél egyetlen kapcsolaton jelöli.

### <a name="echo"></a>echo

Először is a webalkalmazások a ASRS csatlakozni. Másodszor, sok ügyfél csatlakozik webalkalmazást, amely ASRS a hozzáférési jogkivonatot, és a végpont az ügyfelek átirányítása. Ezt követően az ügyfelek WebSocket kapcsolatot létesíteni a ASRS.

Miután az összes ügyfél kapcsolatokat hozhat létre, üzenet, amely tartalmazza az adott hub időbélyeg másodpercenként küld indítása. A Hub ad vissza az eredeti kliensgép üzenet. Minden ügyfél számítja ki a késést, ha vissza az echo üzenetet kap.

A lépések 5\~8 (piros kiemelt forgalom) vannak hurokba került, amely egy alapértelmezett időtartama (5 perc) futtatása, és minden üzenetet késés a statisztika lekérése.
A teljesítmény az útmutató ismerteti a maximális ügyfél kapcsolatainak száma.

![echo](./media/signalr-concept-performance/echo.png)

**Echo**a viselkedés határozza meg, hogy a legnagyobb bejövő sávszélesség egyenlő maximális kimenő sávszélesség. A következő táblázatban találja.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Bejövő/Kimenő (üzenet/mp) | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Bejövő/kimenő sávszélesség (bájt/s) | 2M    | 4M    | 10M   | 20M    | 40M    | 100M   | 200M    |

Az ezt a használati esetet minden ügyfél az alkalmazáskiszolgáló meghatározott hub hív meg. A hub csak az eredeti ügyféloldali meghatározott metódus meghívja. A legtöbb világos lemért központot is **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Még esetében ez az egyszerű központ, a forgalom nyomás alkalmazás kiszolgálón is neves, mint a **echo** bejövő üzenet növekszik. Számos alkalmazás kiszolgálók ezért nagy Termékváltozat-szint esetében szükséges. Az alábbi táblázat az app-kiszolgálók száma minden szinthez.


|    echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Az ügyfél csatlakozási szám, üzenet mérete, üzenetküldést arány, a Termékváltozat-szint és a alkalmazáskiszolgáló CPU/memória hatással általános teljesítménye **echo**.

### <a name="broadcast"></a>Szórás

A **adás**, ha a webes alkalmazás fogadja az üzenetet, közzéteszi az összes ügyfélre. A további ügyfelek is szórás számára, a további üzenet forgalom minden ügyfélnek. Az alábbi ábrában.

![Szórás](./media/signalr-concept-performance/broadcast.png)

A szórás jellemzője, hogy egy kis mennyiségű ügyfelet teszi közzé, ami azt jelenti, hogy a bejövő üzenet sávszélessége kis, de a kimenő sávszélesség hatalmas. A kimenő üzenetek sávszélesség növeli az ügyfél kapcsolatként, vagy növeli a szórási arány.

Az ügyfélkapcsolatok maximális száma, a bejövő/kimenő üzenetek száma és a sávszélesség az alábbi táblázat foglalja össze.

|     Szórás             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő (üzenet/mp)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő (üzenet/mp) | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Bejövő sávszélesség (bájt/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Kimenő sávszélesség (bájt/s) | 4M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

Olyan hozzászólást szórásos ügyfél legfeljebb 4, képest kevesebb alkalmazáskiszolgálókra megköveteli **echo** óta a bejövő üzenet kis tárterülete. Két alkalmazás kiszolgáló vonatkoznak a szolgáltatásiszint-szerződés és a teljesítmény kitöltötte. De az alapértelmezett kiszolgáló-kapcsolatok érdemes növelni, különösen a Unit50 és Unit100 egyenetlen probléma elkerülése érdekében.

|   Szórás      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Az alapértelmezett kiszolgálókapcsolatok 5 ASRS lehetséges egyenetlen kiszolgálókapcsolatok elkerülése érdekében minden alkalmazás kiszolgálón 40-re növeli.
>
> Az ügyfél kapcsolatainak száma, mérete, üzenet küldés sebességét és Termékváltozat-szint hatással az általános teljesítmény **szórása**

### <a name="send-to-group"></a>Küldés csoportba

**Küldés-csoport** hasonló forgalmi minták rendelkezik azzal a különbséggel, hogy az ügyfelek ASRS WebSocket kapcsolatok létrehozásáról, miután csatlakoznia kell csoportokat egy adott csoport üzenetet küldhet. A forgalom áramlását mutatja be az alábbi ábra szerint.

![Csoport küldése](./media/signalr-concept-performance/sendtogroup.png)

Csoport tagja és a csoportok száma is a két tényező teljesítményére hatással van. Az elemzés leegyszerűsítése két típusú csoportok meghatározzuk: kis és nagy csoport.

- `small group`: minden csoportban 10 kapcsolatot. (A kapcsolat maximális szám) megegyezik a csoportazonosító / 10. Például 1 egység, ha 1000 kapcsolatot számát, majd rendelkezünk 1000 / 10 = 100 csoportok.

- `Big group`: Csoportazonosító, mindig 10. (A kapcsolat maximális szám) megegyezik a csoport tagjainak száma / 10. Például 1 egység, ha 1000 kapcsolatot számát, majd minden csoport rendelkezik 1000 / 10 = 100 tag.

**Küldés-csoport** útválasztási költség biztosítható az ASRS, mert a cél-kapcsolatok keresztül elosztott adatstruktúra található. Növelje a küldő kapcsolatok, a költség is növekszik.

#### <a name="small-group"></a>Kis csoport

Az útválasztási költsége jelentős üzenetet küld a sok kis csoport számára. Jelenleg a ASRS megvalósítás eléri unit50 útválasztási költség a korlátot. Több Processzor és memória hozzáadása érdekében, nem szándékosan további így unit100 javítása nem. Ha azt szeretné több bejövő sávszélesség, a testreszabás érdekében forduljon az ügyfélszolgálathoz.

|   Kis csoport küldése     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000 | 100 000
| Csoport tagjainak száma        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Csoportok száma               | 100   | 200   | 500    | 1,000  | 2,000  | 5000  | 10,000 
| Bejövő (üzenet/mp)  | 200   | 400   | 1,000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Bejövő sávszélesség (bájt/s)  | 400 K  | 800 K  | 2M     | 5M     | 8M     | 14M    | 14M     |
| Kimenő (üzenet/mp) | 2,000 | 4,000 | 10,000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Kimenő sávszélesség (bájt/s) | 4M    | 8M    | 20M    | 50M     | 80M    | 140M   | 140M    |

A hub hívása számos ügyfél kapcsolatot, ezért alkalmazás kiszolgáló száma is a teljesítmény kritikus fontosságú. A javasolt app server száma az alábbi táblázatban szerepel.

|  Kis csoport küldése   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Az ügyfél kapcsolatainak száma, mérete, üzenet küldés sebességét, útválasztási költség, Termékváltozat-szint és alkalmazáskiszolgáló CPU/memória hatást gyakorolnak általános teljesítménye **Küldés a kis csoport**.

#### <a name="big-group"></a>Nagy csoport

A **Küldés-az-big-csoport**, a kimenő sávszélesség a szűk keresztmetszetté válik, előtt szerezze meg az Útválasztás költség korlátot. A következő táblázat felsorolja a maximális kimenő sávszélesség, amely szinte teljesen megegyezik, **adás**.

|    Nagy csoport küldése      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000
| Csoport tagjainak száma        | 100   | 200   | 500    | 1,000  | 2,000  | 5000   | 10,000 
| Csoportok száma               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Bejövő (üzenet/mp)  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Bejövő sávszélesség (bájt/s)  | 80 K   | 40 K   | 40 K    | 20 K    | 40 K    | 40 K     | 40 K     |
| Kimenő (üzenet/mp) | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Kimenő sávszélesség (bájt/s) | 8M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

Az küldő kapcsolat jelenleg legfeljebb 40, alkalmazáskiszolgáló nehezedő kicsi, így a javasolt webes alkalmazások számát is kisebb.

|  Nagy csoport küldése  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Az alapértelmezett kiszolgálókapcsolatok 5 ASRS lehetséges egyenetlen kiszolgálókapcsolatok elkerülése érdekében minden alkalmazás kiszolgálón 40-re növeli.
> 
> Az ügyfél kapcsolatainak száma, mérete, üzenet küldés sebességét, útválasztási költség és Termékváltozat-szint hatást gyakorolnak általános teljesítménye **Küldés-az-big-csoport**.

### <a name="send-to-connection"></a>Kapcsolat küldése

Az ezt a használati esetet amikor az ügyfelek a ASRS, kapcsolatokat hozhat létre minden ügyfél meghívja egy speciális hubot az első saját kapcsolatazonosító. A teljesítmény a teljesítményteszt feladata gyűjtése az összes kapcsolat-azonosítót, shuffle őket, és rendeljen hozzájuk az összes ügyfélre küldő célként. Az ügyfelek megtartása üzenet küldése a cél-kapcsolatot a vizsgálat befejezéséig.

![Küldése az ügyfélnek](./media/signalr-concept-performance/sendtoclient.png)

Az Útválasztás költségét **küldése a betöltések és kapcsolatok** hasonló, mint **Küldés a kis csoport**.

Ahogy a kapcsolatok száma növekszik, az általános teljesítmény útválasztási költség korlátozza. 50 egység elérte a határértéket. Ennek eredményeképpen egység 100 további nem javítása.

Az alábbi táblázat egy összefoglaló statisztika, miután futtatásának számos kerekít **küldése a betöltések és kapcsolatok** teljesítményteszt

|   Kapcsolat küldése   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Kapcsolatok                        | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000          | 100 000         |
| Bejövő / Kimenő (üzenet/mp) | 1,000 | 2,000 | 5000 | 8,000  | 9,000  | 20,000 | 20,000 |
| Bejövő / kimenő sávszélesség (bájt/s) | 2M    | 4M    | 10M   | 16M    | 18M    | 40M       | 40M       |

Ezt a használati esetet nagy terhelés a kiszolgálóoldali alkalmazás szükséges. Tekintse meg a javasolt alkalmazások kiszolgálói száma az alábbi táblázatban.

|  Kapcsolat küldése  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Az ügyfél kapcsolatainak száma, mérete, üzenet küldés sebességét, útválasztási költség, Termékváltozat-szint és alkalmazáskiszolgáló CPU/memória hatást gyakorolnak általános teljesítménye **küldése a betöltések és kapcsolatok**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>ASP.NET SignalR echo/broadcast/send-to-connection

ASRS biztosít az ASP.NET SignalR teljesítmény kapacitáson. Ez a szakasz a javasolt webes alkalmazások számának biztosító ASP.NET SignalR **echo**, **adás**, és **Küldés a kis csoport**.

A teljesítményteszt használja az Azure Web App [Standard csomag S3](https://azure.microsoft.com/pricing/details/app-service/windows/) az ASP.NET SignalR.

- `echo`

|   echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Szórás       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Kis csoport küldése     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2,000 | 5000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Kiszolgáló nélküli módban

Ebben a módban az ügyfelek és ASRS is érint. Minden ügyfél egyetlen kapcsolaton jelöli. Az ügyfél küld egy másik ügyfél vagy a szórás üzeneteket az összes üzenetek REST API-n keresztül.

Nagy sűrűségű üzenetküldésre REST API-val nem áll hatékonyabb WebSocket, mert hozhat létre egy új HTTP-kapcsolat minden alkalommal - többlet költség a kiszolgáló nélküli módban van szüksége.

### <a name="broadcast-through-rest-api"></a>Szórási REST API-n keresztül
Összes ügyfél WebSocket kapcsolatot létesíteni a ASRS. Egyes ügyfelek elindítása szórásos REST API-n keresztül. Az üzenet küldése (bejövő) olyan sokoldalúsága HTTP Post, amely nem hatékony WebSocket képest.

|   Szórási REST API-n keresztül     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő (üzenet/mp)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő (üzenet/mp) | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Bejövő sávszélesség (bájt/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Kimenő sávszélesség (bájt/s) | 4M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

### <a name="send-to-user-through-rest-api"></a>Felhasználói REST API-n keresztül történő küldése
A teljesítményteszt rendel a felhasználónevek összes ügyfél csatlakozik a ASRS indítása előtt. Az ügyfelek WebSocket kapcsolatok ASRS a létrehozása után elindítják a üzenetek küldése mások HTTP Post használatával.

|   Felhasználói REST API-n keresztül történő küldése | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2,000 | 5000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Bejövő (üzenet/mp)  | 300   | 600   | 900    | 1,300  | 2,000  | 10,000  | 18,000  |
| Kimenő (üzenet/mp) | 300   | 600   | 900    | 1,300  | 2,000  | 10,000  | 18,000 |
| Bejövő sávszélesség (bájt/s)  | 600 K  | 1.2-ES M  | 1.8-AS M   | 2.6M   | 4M     | 10M     | 36M    |
| Kimenő sávszélesség (bájt/s) | 600 K  | 1.2-ES M  | 1.8-AS M   | 2.6M   | 4M     | 10M     | 36M    |

## <a name="performance-test-environments"></a>Teljesítmény-tesztelési környezetek

A vizsgálat a fent felsorolt összes használati esetek végezték-Azure-környezetben. A legtöbb 50 ügyfél virtuális gépek és a 20 app server virtuális gépek használhatók.

Ügyfél-Virtuálisgép mérete: StandardDS2V2 (2 vCPU, memória 7G)

Alkalmazás-kiszolgáló virtuális gép mérete: StandardF4sV2 (4 vCPU, memória 8G)

Az Azure SignalR SDK kiszolgálókapcsolatok: 15

## <a name="performance-tools"></a>Teljesítménynövelő eszközök

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>További lépések

Ebben a cikkben, áttekintheti a SignalR Service teljesítményének tipikus használatieset-forgatókönyvekhez.

További részleteket a SignalR Service és a SignalR Service skálázása elemei, olvassa el az alábbi útmutatóban.

* [Azure SignalR Service Internals](signalr-concept-internals.md)
* [Azure SignalR Service Scaling](signalr-howto-scale-multi-instances.md)