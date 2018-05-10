---
title: Kimenő kapcsolatok az Azure-ban |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan Azure lehetővé teszi a virtuális gépek nyilvános internetes szolgáltatásokkal kommunikálni.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2018
ms.author: kumud
ms.openlocfilehash: 5cff443ac3bbd89a2245e7adb21458ecc62fd494
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="outbound-connections-in-azure"></a>Kimenő kapcsolatok az Azure-ban

Azure felhasználói telepítés a kimenő kapcsolatot biztosít számos különböző módon. A cikkből megtudhatja, Mik azok a helyzetek, amikor vonatkoznak, leírás és kezelésük módjával.

>[!NOTE] 
>Ez a cikk csak a Resource Manager üzembe helyezések ismerteti. Felülvizsgálati [kimenő kapcsolatok (klasszikus)](load-balancer-outbound-connections-classic.md) összes klasszikus telepítési forgatókönyvek az Azure-ban.

A központi telepítés az Azure-végpontok kívül a nyilvános IP-címtér az Azure kommunikálhatnak. Amikor egy példányát a nyilvános IP-címtér lévő célra egy kimenő folyam kezdeményez, Azure dinamikusan rendeli a magánhálózati IP-cím egy nyilvános IP-cím. Ez a leképezés létrehozása után a kimenő kezdeményezésű áramlás forgalom is elérhető a magánhálózati IP-címet a folyamat származási helyének.

Azure forrás hálózati címfordítás (SNAT) használja ezt a funkciót. Több privát IP-címek vannak színleg mögött egyetlen nyilvános IP-címnek, az Azure használja [címfordítást (PAT) port](#pat) helyettesítő magánhálózati IP-címek számára. Elmúló port a PAT használja, és [előzetesen lefoglalt](#preallocatedports) készlet mérete alapján.

Egyszerre több [kimenő forgatókönyvek](#scenarios). Ezek a forgatókönyvek igény szerint kombinálhatja. Tekintse át alaposan, hogy a képességek, korlátozások és minták tájékozódjon a telepítési modell vonatkoznak és a forgatókönyvet. Tekintse át az útmutató a [forgatókönyvekben kezelése](#snatexhaust).

>[!IMPORTANT] 
>Standard terheléselosztó kimenő kapcsolat számára új képességekkel és különböző beállításokat vezet be.   Például [3. forgatókönyv](#defaultsnat) nem létezik, egy szabványos belső terheléselosztón jelen, és különböző lépéseket kell tenni.   Gondosan tekintse át a teljes dokumentum átfogó fogalmakat és termékváltozatok közötti különbségek megértése.

## <a name="scenarios"></a>Forgatókönyv áttekintése

Az Azure Load Balancer és a kapcsolódó erőforrások explicit módon határozzák meg használatakor [Azure Resource Manager](#arm).  Azure jelenleg három különböző módszert biztosít eléréséhez kimenő kapcsolódás az Azure Resource Manager erőforrások. 

| Forgatókönyv | Módszer | IP-protokollok | Leírás |
| --- | --- | --- | --- |
| [1. Virtuális gép példány szint nyilvános IP-címmel (vagy anélkül terheléselosztó)](#ilpip) | SNAT, színleg nem használt port | TCP, UDP, ICMP, ESP | Azure használ a nyilvános IP-címet hozzárendelni az IP-konfiguráció, a példány hálózati adaptert. A példány érhető el az összes rövid élettartamú port tartozik. |
| [2. A virtuális gép (nem példány szint nyilvános IP-cím a példányon) társított nyilvános terheléselosztó](#lb) | A port színleg (PAT) SNAT használ a Load Balancer frontends | TCP, UDP |Azure több privát IP-címek osztanak meg a nyilvános terheléselosztót frontends nyilvános IP-címét. Azure a PAT frontends elmúló portját használja. |
| [3. Önálló virtuális gép (nem a terheléselosztóhoz, példány szint nyilvános IP-cím)](#defaultsnat) | A port színleg (PAT) SNAT | TCP, UDP | Azure automatikusan meg, míg a nyilvános IP-címnek a SNAT, a nyilvános IP-címet oszt meg több privát IP-címek, a rendelkezésre állási csoport és a nyilvános IP-cím elmúló port használja. Ez egy webfarmos tartalék a fenti forgatókönyvek esetén. Nem ajánlott, ha figyelemmel kísérelhetik és vezérelhetik van szüksége. |

Ha nem szeretné a virtuális gépek kívül Azure a nyilvános IP-címterület végpontok kommunikálni, a hálózati biztonsági csoportokkal (NSG-k) segítségével letiltja a hozzáférést, igény szerint. A szakasz [megakadályozza a kimenő kapcsolat](#preventoutbound) NSG-ket ismerteti részletesen. Designing útmutatást, megvalósítás, és minden kimenő-hozzáférés nélküli virtuális hálózat kezelése van ez a cikk hatókörén kívül.

### <a name="ilpip"></a>1. forgatókönyv: Virtuális gép példány szint nyilvános IP-címmel

Ebben a forgatókönyvben a virtuális Gépnek legyen egy példány szint nyilvános IP (ILPIP) rendelve. Kimenő kapcsolatok illeti nem számít, hogy a virtuális gép elosztott terhelésű vagy nem. Ebben a forgatókönyvben élvez a többi. Egy ILPIP használata esetén a virtuális Gépet a ILPIP minden kimenő forgalom használ.  

Nyilvános IP-egy virtuális Géphez rendelt egy 1:1 kapcsolat (nem 1:many), és valósul meg állapotmentes 1:1 hálózati címfordítást.  A virtuális Gépnek legyen használható elmúló port, és nem használja a port színleg (PAT).

Ha az alkalmazás számos kimenő forgalom kezdeményez, és SNAT port Erőforrásfogyás tapasztal, érdemes lehet hozzárendelése egy [SNAT megkötések mérséklése ILPIP](#assignilpip). Felülvizsgálati [kezelése SNAT Erőforrásfogyás](#snatexhaust) egészében.

### <a name="lb"></a>2. forgatókönyv: Elosztott terhelésű virtuális gép nélküli példány szint nyilvános IP-cím

Ebben a forgatókönyvben a VM része egy nyilvános terheléselosztó háttérkészletének. A virtuális gép nem rendelkezik a nyilvános IP-cím hozzárendelve. A terheléselosztó erőforrás olyan terheléselosztó szabályhoz a nyilvános IP-előtérbeli a háttérkészlet közötti kapcsolat létrehozásához kell konfigurálni.

Ha nem tölti ki a szabály konfigurálását, a rendszer nem a forgatókönyvhöz, amelyben a [önálló virtuális gép, és nem példány szint nyilvános IP-cím](#defaultsnat). Nincs szükség a szabályhoz való egy működő figyelő sikeres állapotának mintavétel háttérkészlet.

A terheléselosztással rendelkező virtuális Gépet egy kimenő folyam hoz létre, amikor az Azure eszköz a személyes forrás IP-címét a kimenő folyam a nyilvános terheléselosztó előtérbeli nyilvános IP-címére. Azure SNAT használja ezt a funkciót. Azure is használ [PAT](#pat) a helyettesítő több privát IP-cím, egy nyilvános IP-cím mögött. 

A load balancer nyilvános IP-cím előtér-rövid élettartamú port segítségével különböztetheti meg egymástól a virtuális gép által az egyes adatfolyamok. SNAT dinamikusan használ [előzetesen lefoglalt elmúló port](#preallocatedports) kimenő adatfolyamok létrehozásakor. Ebben a környezetben használt SNAT porttartomány SNAT portok nevezzük.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakasz. A véges erőforrása is nagyon gyorsan kimerítették fontosságúak. Fontos tudni, hogyan vannak [felhasznált](#pat). Annak megértése, hogyan tervezhet a a fogyasztás és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

Ha [több (nyilvános) IP-címek társítva a Load Balancer alapvető](load-balancer-multivip-overview.md), bármely a nyilvános IP-címek vannak egy [a kimenő forgalom jelölt](#multivipsnat), és egy van választva.  

Kimenő kapcsolatok a Load Balancer alapszintű állapotának figyelésére használható [Naplóelemzési terheléselosztóhoz](load-balancer-monitor-log.md) és [eseménynaplók riasztási](load-balancer-monitor-log.md#alert-event-log) SNAT port Erőforrásfogyás üzenetek figyelésére.

### <a name="defaultsnat"></a>3. forgatókönyv: Önálló virtuális gép példány szint nyilvános IP-cím nélküli

Ebben a forgatókönyvben a virtuális gép nem része egy nyilvános terheléselosztó-készlet (és nem része egy belső szabványos terheléselosztó-készlet), és nincs hozzárendelve ILPIP címe. Ha a virtuális Gépet hoz létre egy kimenő folyam, Azure lefordítja a nyilvános IP-forráscím kimenő folyam titkos forrás IP-címét. A nyilvános IP-cím, a kimenő folyam használt nem konfigurálható, és nem tartoznak bele az előfizetés nyilvános IP-erőforrás korlátját ellen.

>[!IMPORTANT] 
>Ebben a forgatókönyvben azt is érvényes, ha __csak__ egy alapszintű belső terheléselosztón van csatolva. 3. forgatókönyv van __nem érhető el__ amikor egy belső szabványos Load Balancer egy virtuális Géphez van csatolva.  Explicit módon kell létrehoznia [1. forgatókönyv](#ilpip) vagy [2. forgatókönyv](#lb) egy szabványos belső terheléselosztó használata mellett.

Azure használ SNAT port színleg ([PAT](#pat)) Ez a függvény végrehajtásához. Ebben a forgatókönyvben hasonlít [2. forgatókönyv](#lb), azonban nem szabályozhatják a használt IP-cím van. Ez az a tartalék forgatókönyv esetén 1 és 2 nem léteznek. Ez a forgatókönyv nem ajánlott, ha azt szeretné, hogy a kimenő cím vezérelheti. Kimenő kapcsolatok az alkalmazáshoz kritikus részét képezik, ha egy másik helyzet kell választotta.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakasz.  A virtuális gépek rendelkezésre állási csoport megosztása számát határozza meg, milyen előzetes lefoglalás szinten vonatkozik.  Egy különálló virtuális gép rendelkezésre állási csoport nélküli hatékonyan 1 készletét előzetes lefoglalás (1024 SNAT portok) meghatározása céljából. SNAT portjait is nagyon gyorsan kimerítették véges erőforrás. Fontos tudni, hogyan vannak [felhasznált](#pat). Annak megértése, hogyan tervezhet a a fogyasztás és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

### <a name="combinations"></a>Több, kombinált forgatókönyv

Az egy adott eredmény elérése érdekében a fenti szakaszokban ismertetett forgatókönyvek kombinálhatja. Ha több forgatókönyv jelen, egy rangsora vonatkozik: [1. forgatókönyv](#ilpip) elsőbbséget élvez [2. forgatókönyv](#lb) és [3](#defaultsnat). [2. forgatókönyv](#lb) felülbírálások [3. forgatókönyv](#defaultsnat).

Példa: az Azure Resource Manager központi telepítése, amikor az alkalmazás erősen támaszkodik a célhelyekre csak korlátozott számú kimenő kapcsolatokat, de is fogad a bejövő forgalom a terheléselosztó előtérbeli keresztül. Ebben az esetben kombinálhatja a 1. és 2 mentességre forgatókönyvek. További kombinációját, tekintse át [kezelése SNAT Erőforrásfogyás](#snatexhaust).

### <a name="multife"></a> A kimenő forgalom több frontends

#### <a name="load-balancer-standard"></a>Load Balancer Standard

Load Balancer szabványos használja minden jelöltek kimenő forgalom ugyanazon az időpontot, amikor [több (nyilvános) IP-frontends](load-balancer-multivip-overview.md) található. Minden egyes előtér szorozza meg a rendelkezésre álló előzetesen lefoglalt SNAT portok száma egy terheléselosztási szabályt engedélyezésekor kimenő kapcsolatok.

Ha szeretné, ne jelenjen meg többé a front-end IP-címet az új terheléselosztási terheléselosztási szabály lehetőséggel kimenő kapcsolatokat használja:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Általában ez a beállítás alapértelmezett értéke _hamis_ és jelzi, hogy ez a szabály a társított virtuális gépek a terheléselosztási szabály háttérkészlethez kimenő SNAT programokat.  Ez módosítható _igaz_ megakadályozhatja, hogy a terheléselosztó kimenő kapcsolatok a virtuális géphez társított előtérbeli IP-címét a következőre a terheléselosztási szabály háttérkészlethez.  Továbbra is megjelölhetők, így egy adott IP-cím a kimenő forgalom leírtak szerint, és [több kombinált forgatókönyvek](#combinations) is.

#### <a name="load-balancer-basic"></a>Terheléselosztó Basic betöltése

Load Balancer alapvető választja a kimenő forgalom használandó egyetlen előtérbeli amikor [több (nyilvános) IP-frontends](load-balancer-multivip-overview.md) kimenő forgalom vár. Ez a beállítás nem konfigurálható, és vegye figyelembe a kijelölés algoritmust kell véletlenszerű. Egy adott IP-cím a kimenő forgalom jelölhet ki a [több kombinált forgatókönyvek](#combinations).

### <a name="az"></a> Rendelkezésre állási zónák

Használata esetén [szabványos terheléselosztót, rendelkezésre állási zónák](load-balancer-standard-availability-zones.md), zónaredundáns frontends biztosíthat SNAT zónaredundáns kimenő kapcsolatokat, és SNAT programozási survives zóna hiba.  Zonal frontends használata esetén SNAT kimenő kapcsolatok sorsáról megosztása a zónához tartozik.

## <a name="snat"></a>SNAT és a PAT ismertetése

### <a name="pat"></a>Port színleg SNAT (PAT)

Egy nyilvános terheléselosztó erőforrás társított Virtuálisgép-példányok esetén a rendszer írni minden kimenő kapcsolat forrása. A forrás az előtérbeli nyilvános IP-cím a terheléselosztó a virtuális hálózat privát IP-címtér van újraírja. A nyilvános IP-címtér 5 rekordos a folyamat (forrás IP-cím, forrásport, IP-átviteli protokoll, cél IP-címe, Célport) egyedinek kell lennie.  Port színleg SNAT TCP vagy UDP IP protokoll használható.

Ennek elérése után írja át a magánhálózati IP-forráscím, mert több adatfolyamok egy egyetlen nyilvános IP-címről elmúló port (SNAT portok) segítségével. 

Egy SNAT port felhasznált ne egy egyetlen IP-címre, port és protokoll. Több adatfolyamok ugyanahhoz a cél IP-cím, port és protokoll, az egyes SNAT egyetlen portot használ fel. Ez biztosítja, hogy az adatfolyamok egyedi legyenek-e az azonos nyilvános IP-cím oly módon, és nyissa meg az azonos cél IP-cím, port és protokoll. 

Több adatfolyamok, egy másik cél IP-cím, port és protokoll, hogy mindegyik egyetlen SNAT port megosztása. A cél IP-cím, port és protokoll egyedivé adatfolyamok nincs szükség további Forráshelyek segítségével különböztetheti meg egymástól a nyilvános IP-címtér forgalmának portok.

SNAT port erőforrások elfogytak, amikor a kimenő forgalom sikertelen, amíg a meglévő forgalom kiadási SNAT portok. Terheléselosztó úgy szabadít fel SNAT portok az adatfolyam bezárása, és használja a [4 perces időtúllépést](#idletimeout) az üresjárati folyamok SNAT portok újraigénylési funkcióját.

Feltételeket, amelyek gyakran előfordulhat, hogy SNAT port Erőforrásfogyás mérséklése kombinációját, tekintse át a [kezelése SNAT](#snatexhaust) szakasz.

### <a name="preallocatedports"></a>Rövid élettartamú port előzetes lefoglalás port színleg SNAT (PAT)

Használatakor a háttérkészlet mérete alapján az algoritmus meghatározására száma előzetesen lefoglalt SNAT elérhető portok az Azure által használt port színleg SNAT ([PAT](#pat)). SNAT portjait elmúló port egy adott nyilvános IP-forráscím érhető el.

SNAT portok azonos számú előzetesen lefoglalt az UDP- és TCP kulcsattribútumokkal és felhasznált egymástól függetlenül / IP-protokoll. 

>[!IMPORTANT]
>Standard Termékváltozat SNAT programozási / IP-átviteli protokoll és a terheléselosztási szabályt származik.  Ha csak egy TCP terheléselosztási szabályt létezik, SNAT lehetőség csak a TCP. Ha csak egy TCP terheléselosztási szabály és kimenő SNAT kell UDP-hez, UDP terheléselosztási szabály ugyanazokat az előtér-azonos háttérkészlet létrehozása.  Ez akkor indul el, az UDP programozási SNAT.  Egy működő szabály vagy a rendszerállapot mintavételt nincs szükség.  Alapszintű Termékváltozat SNAT SNAT mindig programok mindkét IP átviteli protokoll, az átviteli protokoll a terheléselosztási szabály megadott függetlenül.

Azure preallocates SNAT portok minden egyes VM hálózati adapter IP-konfigurációhoz. Amikor IP-konfigurációt ad hozzá a készlethez, a SNAT portok vannak előzetesen lefoglalt az IP-konfiguráció, a háttér-készlet mérete alapján. Kimenő forgalom létrehozásakor [PAT](#pat) dinamikusan használ fel (de előzetesen lefoglalt), majd felenged ezeket a portokat, a folyamat befejeződésekor vagy [az üresjárati időkorlát](#ideltimeout) fordulhat elő.

Az alábbi táblázat a SNAT port preallocations háttér címkészletet méretű rétege számára:

| Készlet mérete (Virtuálisgép-példány) | Előzetesen lefoglalt SNAT portok száma IP-konfiguráció|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> A szabványos terheléselosztó használatakor [több frontends](load-balancer-multivip-overview.md), [minden előtérbeli IP-cím szorozza meg a rendelkezésre álló SNAT portok száma](#multivipsnat) az előző táblázatban. Például egy háttér címkészletet 50 virtuális gép 2 terheléselosztási szabályok, külön-külön előtérbeli IP-címek egy, a 2048 (2 x 1024-es) SNAT portok száma IP-konfigurációt fogja használni. A részletek megtekintéséhez [több frontends](#multife).

Ne feledje, hogy a rendelkezésre álló SNAT portok száma nem jelenti azt, közvetlenül az adatfolyamok száma. Egyetlen SNAT port a több célhoz rendelt egyedi felhasználhatók. Portok csak akkor, ha a forgalom egyedivé kell felhasználni. Tervezési és a megoldás útmutatásért tekintse át a részt kapcsolatos [kezelése a kimeríthető erőforrás](#snatexhaust) és a következő szakasz ismerteti [PAT](#pat).

A háttérkészlet méretének módosítása hatással lehet a meglévő adatfolyamok némelyike. Ha a háttér-készlet mérete növekszik, és be a következő réteggel átmenetek, a előzetesen lefoglalt SNAT portok felének visszaigényelt vannak a következő nagyobb háttér címkészletet réteg történő áttelepítés során. Folyamatok, amelyek regenerált SNAT portot fog túllépi az időkorlátot, és létre kell hozni. Ha egy új módszer kísérlet történik, a folyamat sikeres lesz azonnal, mindaddig, amíg érhetők el előzetesen lefoglalt portok.

Ha csökkenti a háttér-készlet méretét, és az alacsonyabb szint transitions lehetőségnél SNAT elérhető portok száma növekszik. Ebben az esetben meglévő lefoglalt SNAT portok, és a megfelelő adatfolyamok, nem érintettek.

SNAT port lefoglalását meghatározott IP-protokoll (TCP és UDP külön-külön karbantarthatók) és a következő feltételek kiadott:

### <a name="tcp-snat-port-release"></a>TCP SNAT port kiadás

- Ha mindkét kiszolgáló ügyfél küld FIN/ACK, SNAT port kiadjuk 240 másodperc múlva.
- Ha egy RST látható, SNAT port kiadjuk 15 másodperc után.
- üresjárati időtúllépés el lett érve.

### <a name="udp-snat-port-release"></a>UDP-SNAT port kiadás

- üresjárati időtúllépés el lett érve.

## <a name="problemsolving"></a> A probléma megoldásához 

Ez a szakasz segítséget nyújtanak az SNAT Erőforrásfogyás és a többi olyan forgatókönyvet, amely akkor fordulhat elő, a kimenő kapcsolatok az Azure-ban készült.

### <a name="snatexhaust"></a> SNAT (PAT) port Erőforrásfogyás kezelése
[Elmúló port](#preallocatedports) használt [PAT](#pat) egy kimeríthető erőforrás van, a [önálló virtuális gép, egy példány szint nyilvános IP-cím nélküli](#defaultsnat) és [virtuális gép elosztott terhelésű nélküli egy Példány szint nyilvános IP-cím](#lb).

Ha tudja, hogy sok kimenő TCP vagy UDP-kapcsolata ugyanahhoz a cél IP-cím és port még kezdeményezése, és láthatja a kimenő kapcsolatok sikertelen vagy végigvitelével úgy, hogy Ön skálázását SNAT portok (előzetesen lefoglalt [elmúló portok](#preallocatedports) által használt [PAT](#pat)), több lehetőség közül választhat általános megoldás. Tekintse át ezeket a beállításokat, és határozza meg az elérhető és ajánlott a forgatókönyvéhez. Akkor lehet, hogy egy vagy több segítséget nyújtanak ebben a forgatókönyvben.

Ha problémája van a kimenő kapcsolat viselkedésének megértése, IP-verem statisztika (netstat) is használhatja. Vagy hasznos, és figyelje meg a kapcsolat viselkedés a csomag rögzíti. Hajtsa végre a csomag rögzíti a vendég operációs rendszer a példány, vagy használjon [csomag rögzítési hálózati figyelőt](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Az alkalmazás kapcsolatok újból módosítása 
Igény szerinti elmúló SNAT újból felhasználja a kapcsolatot az alkalmazás által használt portok csökkentése érdekében. Ez különösen igaz protokollok, például a HTTP/1.1, ahol a kapcsolat újbóli az alapértelmezett beállítás. És egyéb protokollok (például REST) t a HTTP Protokollt használó pedig előnyei. 

Újbóli mindig jobb, mint az egyes, atomi TCP-kapcsolatok az egyes kérelmek. Használja fel további performant, nagyon hatékony TCP tranzakciók eredményez.

#### <a name="connection pooling"></a>Az alkalmazás használatához a kapcsolatkészletezést módosítása
A rendszer az alkalmazásban, ahol kérelmek belső elosztott kapcsolatokat (egyes újból felhasználja a ahol csak lehetséges) készletét kapcsolatkészlet is alkalmazhat. Ez a séma használatban elmúló port száma korlátozza, és a környezetet hoz létre a megfelelő. Ez a séma is növelheti a kapacitásának több egyidejű művelet tételével, amikor egyetlen kapcsolatból blokkolja-e a művelet a válasz.  

Kapcsolatkészletezést előfordulhat, hogy az alkalmazás vagy a konfigurációs beállítások, az alkalmazás fejlesztése segítségével keretében már létezik. A kapcsolat újbóli kapcsolatkészletezést kombinálhatja. A több kérelmek felhasználni a azonos cél IP-cím és port portok rögzített, előre jelezhető száma. A kérelmek is előnyös TCP tranzakciók, így csökkentve a késést és erőforrás-használat hatékony felhasználását. UDP-tranzakciók számára is hasznos, mert a számot, UDP-forgalom kezelésére is pedig kipufogógáz feltételek elkerülése és kezelheti a SNAT port kihasználtságának.

#### <a name="retry logic"></a>Az alkalmazást, hogy az kisebb agresszív újrapróbálkozási logika módosítása
Ha [előzetesen lefoglalt elmúló port](#preallocatedports) használt [PAT](#pat) vannak kimerült vagy alkalmazás hiba történik, túl szigorú vagy találgatásos kényszerített újrapróbálja decay nevű és leállítási logika miatt Erőforrásfogyás fordulhat elő, vagy megőrzése nélkül. Igény szerinti rövid élettartamú port egy kisebb agresszív újrapróbálkozási logika használatával csökkentése érdekében. 

Elmúló port (nem állítható) 4 perces üresjárati időtúllépés rendelkezik. Ha az újrapróbálkozások túl agresszív, az Erőforrásfogyás rendelkezik nincs lehetőség arra, hogy a saját törlődnek. Ezért annak eldöntéséhez, hogy hogyan--és milyen gyakran – az alkalmazás újrapróbálja tranzakciók a Tervező kritikus részét képezi.

#### <a name="assignilpip"></a>Egy példány szintű nyilvános IP-cím hozzárendelése minden virtuális gép
A forgatókönyvhöz a hozzárendelése egy ILPIP változik [példány szint nyilvános IP-cím egy virtuális géphez](#ilpip). A virtuális gép érhetők el az egyes virtuális gépek használt rövid élettartamú a nyilvános IP-portok. (Figyelésekor forgatókönyvek, ahol a nyilvános IP-elmúló port megosztott módon működik a virtuális gépek társított megfelelő háttérkészlet.) Nincsenek kompromisszumot kell figyelembe venni, például a nyilvános IP-címek további költségeket és a célgyűjtemény nagyszámú egyedi IP-címek engedélyezése.

>[!NOTE] 
>Ez a beállítás nem érhető el webes feldolgozói szerepköröket.

#### <a name="multifesnat"></a>Több frontends használata

Ha a szabványos nyilvános terheléselosztót használ, rendeljen hozzá [kimenő kapcsolatok több előtérbeli IP-címet](#multife) és [szorozza meg a rendelkezésre álló SNAT portok számának](#preallocatedports).  Egy előtérbeli IP-konfigurációja, a szabály és a nyilvános IP-címét az előtérbeli SNAT programozását elindítható a háttérkészlet létrehozásához szükséges.  A szabály nem történő működéshez szükségük van, és egy állapotmintáihoz nem kell sikeres.  Ha több frontends a bejövő, valamint (, nem pedig csak kimenő), használjon egyéni állapot-mintavételi csomagjai megbízhatóság biztosításához is.

>[!NOTE]
>A legtöbb esetben SNAT portok kimerülése rossz tervezési bejelentkezési.  Győződjön meg arról, hogy miért skálázását portok további frontends használata SNAT portok hozzáadása előtt.  Előfordulhat, hogy a problémát, ami sikertelen később maszkolás.

#### <a name="scaleout"></a>Horizontális felskálázása

[Előzetesen lefoglalt portok](#preallocatedports) a háttér-készlet mérete alapján, és a lehető legkisebb mértékű akadályozása, ha néhány port már foglalhatók le a következő nagyobb háttér címkészletet mérete réteggel befogadásához réteg csoportosított hozzá.  Előfordulhat, hogy egy lehetőség, hogy a megadott időtúllépést SNAT port használatot intenzitásának növelése skálázással a háttérkészlet az adott réteg maximális méretét.  Ehhez az alkalmazás horizontális hatékony.

A háttérkészlet 2 virtuális gép például 1024 SNAT portok, amely egy IP-konfiguráció, így 2048 SNAT összesen portok a központi telepítés rendelkezik.  Ha a központi telepítés-ra, 50 virtuális gépek, még ha száma előzetesen lefoglalt portok marad állandó virtuális gépenként 51,200 (50 x 1024-es) összesen SNAT portok a központi telepítés által használható.  Ha a központi telepítés kiterjesztése, ellenőrizheti a [előzetesen lefoglalt portok](#preallocatedports) / módosítása a következőre győződjön meg arról, hogy a megfelelő rétegben maximális kibővítési a alakul.  A portáladatbázis előző példa, ha 51 helyett legfeljebb 50 példányt horizontálisan választotta volna a volna előrehaladás a következő réteggel és a záró mentése kisebb SNAT porttal, valamint virtuális gépenként összesen hasonlóan.

Ezzel szemben a következő nagyobb háttér címkészletet mérete réteg potenciálisan kimenő kapcsolatok Ha lefoglalt portok kibővítési kell foglalhatók le újra.  Ha nem szeretné, ez kerül sor, akkor alakul a környezetet, hogy a réteg mérete.  Vagy győződjön meg arról, hogy az alkalmazás észleléséhez, majd próbálja megismételni szükség szerint.  Segít a TCP Keep-Alive csomagok küldését a észlelés, ha SNAT portok már nem függvény alatt teljesítményigény miatt.

### <a name="idletimeout"></a>Segítségével a Keep Alive csomagok küldését a kimenő üresjárati időtúllépés

Kimenő kapcsolatok 4 perces üresjárati időtúllépés rendelkezik. Ez az időkorlát értéke nem állítható. Azonban használhatja (például TCP Keep Alive csomagok küldését) transport vagy alkalmazásréteg Keep-Alive csomagok küldését az üresjárati folyamat frissítése, és alaphelyzetbe állítja az üresjárati időtúllépés, ha szükséges.  

TCP Keep-Alive csomagok küldését használata, esetén elegendő, és lehetővé teszi a kapcsolat egyik oldalán. Például elegendő, és lehetővé teszi a kiszolgáló oldalán, csak az időzítőt, a folyamat visszaállítására, és nincs szükség mindkét oldalon kezdeményezett TCP Keep Alive csomagok küldését.  Hasonló fogalmak alkalmazásréteg, beleértve az adatbázis ügyfél-kiszolgáló konfigurációk léteznek.  Ellenőrizze a kiszolgáló oldalán, milyen lehetőségek érhetők el az alkalmazás adott Keep Alive csomagok küldését a.

## <a name="discoveroutbound"></a>A nyilvános IP-cím egy virtuális gép használ felderítése
Számos módon határozza meg a nyilvános IP-forráscím egy kimenő kapcsolat. OpenDNS láthatja a nyilvános IP-címet a virtuális gép egy szolgáltatást biztosít. 

Az nslookup parancs használatával a OpenDNS feloldó elküldheti a név myip.opendns.com DNS-lekérdezést. A szolgáltatás az IP-forráscím kérés küldése használt adja vissza. A virtuális gépről a következő lekérdezés futtatásakor a rendszer a választ a nyilvános IP-címet használja ezt a virtuális gépet:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Megakadályozza a kimenő kapcsolat
Néha nemkívánatos is a virtuális gépek számára hozzon létre egy kimenő folyam engedélyezett. Előfordulhat, hogy mely célok elérhető a kimenő forgalom kezeléséhez követelmény, vagy mely célok megkezdheti a bejövő forgalom. Ebben az esetben használhatja [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) kezeléséhez, amelyek a virtuális gép el lehet érni a célhelyre. Az NSG-k segítségével is kezelheti, mely nyilvános cél is kezdeményezhető a bejövő forgalom. 

Amikor egy NSG-t alkalmaz egy elosztott terhelésű virtuális gép, különös a [alapértelmezett címkék](../virtual-network/virtual-networks-nsg.md#default-tags) és [alapértelmezett szabályok](../virtual-network/virtual-networks-nsg.md#default-rules). Győződjön meg arról, hogy a virtuális gép állapotának mintavételi kérések fogadhat Azure Load Balancer. 

Ha egy NSG blokkolja az egészségügyi mintavételi kérelmeit a AZURE_LOADBALANCER az alapértelmezett címke, a virtuális gép állapotmintáihoz sikertelen lesz, és a virtuális gép van megjelölve. Terheléselosztó leállítja az új forgalom küldése ezt a virtuális Gépet.

## <a name="limitations"></a>Korlátozások
- DisableOutboundSnat nincs lehetőség egy terheléselosztási szabály portálon konfigurálásakor.  Ehelyett használja a többi, sablon vagy ügyfél eszközök.
- Webes feldolgozói szerepkörök nélkül egy VNet és egyéb Microsoft-platform szolgáltatás elérhető lehet, ha csak egy belső szabványos terheléselosztó használata miatt a hogyan előtti-VNet-szolgáltatások és az egyéb platform szolgáltatás függvény egyik mellékhatása. Meg kell támaszkodjon ezt a mellékhatást, a megfelelő service magát, vagy az alapul szolgáló platform értesítés nélkül változhatnak. Hogy mindig részlegnek feltételeznie kell létrehozásához szükséges kimenő kapcsolat explicit módon ha egy belső szabványos terheléselosztó csak használata esetén szükséges. A [SNAT alapértelmezett](#defaultsnat) cikkben leírt 3. forgatókönyv nem érhető el.

## <a name="next-steps"></a>További lépések

- További információ [terheléselosztó](load-balancer-overview.md).
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- További információ [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).
- Ismerje meg, azzal kapcsolatban, a másik kulccsal [hálózati lehetőségeket](../networking/networking-overview.md) az Azure-ban.
