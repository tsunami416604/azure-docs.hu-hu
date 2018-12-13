---
title: Az Azure kimenő kapcsolatainak
titlesuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan biztosítja az Azure virtuális gépek nyilvános internetről szolgáltatásokkal kommunikálni.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2018
ms.author: kumud
ms.openlocfilehash: 09de0a3aa0303e169d0b90690016909b29dc4a9b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190968"
---
# <a name="outbound-connections-in-azure"></a>Az Azure kimenő kapcsolatainak

Az Azure számos különféle mechanizmus útján kimenő kapcsolat az ügyfél központi telepítések biztosít. A cikkből megtudhatja, Mik azok az esetek, amikor azok a alkalmazni, hogyan működnek és hogyan kezelhetők.

>[!NOTE] 
>Ez a cikk csak a Resource Manager üzembe helyezések ismerteti. Felülvizsgálat [kimenő kapcsolatok (klasszikus)](load-balancer-outbound-connections-classic.md) összes klasszikus üzembe helyezési forgatókönyvek az Azure-ban.

Központi telepítés az Azure-ban a nyilvános IP-címtér az Azure-on kívülről végpontok kommunikálhat. Ha egy példány egy kimenő folyam lévő nyilvános IP-címterület célra kezdeményezi, az Azure dinamikusan rendeli a magánhálózati IP-cím nyilvános IP-címre. Ez a leképezés létrehozása után a kimenő származó folyamat a visszatérő forgalom is elérheti a magánhálózati IP-címet, adja meg a folyamat.

Az Azure forrás hálózati címfordítás (SNAT) használ, ez a függvény végrehajtásához. Több magánhálózati IP-címek vannak folyamatnak álcázott mögött egyetlen nyilvános IP-címet, ha az Azure használ [címfordítás (PAT) port](#pat) a magánhálózati IP-címek révén. A rövid élettartamú port a PAT használja, és [előzetesen lefoglalt](#preallocatedports) készlet mérete alapján.

Egyszerre több [kimenő forgatókönyveket](#scenarios). Igény szerint kombinálhatja ezeket a forgatókönyveket. Tekintse át alaposan, hogy értelmezését képességek, korlátozások és minták a-alapú üzemi modellre vonatkoznak és a forgatókönyvet. Tekintse át az útmutató a [ezek a forgatókönyvek kezelése](#snatexhaust).

>[!IMPORTANT] 
>A standard Load Balancer kimenő kapcsolat az új funkciókat vehetnek igénybe, és különböző beállításokat vezet be.   Ha például [3. forgatókönyv](#defaultsnat) nem létezik, ha jelen egy belső Standard Load Balancer és különböző lépéseket kell elvégezni.   Gondosan tekintse át ezt a teljes dokumentumot, az általános fogalmak és Termékváltozatai közötti különbségek megértése.

## <a name="scenarios"></a>Forgatókönyv áttekintése

Az Azure Load Balancer és kapcsolódó erőforrások explicit módon definiálva vannak használatakor [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Az Azure jelenleg a kimenő kapcsolat az Azure Resource Manager-erőforrások eléréséhez három különböző módszert biztosít. 

| Forgatókönyv | Módszer | IP-protokollok | Leírás |
| --- | --- | --- | --- |
| [1. Virtuális gép (vagy anélkül terheléselosztó) példány szintű nyilvános IP-címmel](#ilpip) | Port folyamatnak álcázott nem használja az SNAT, | TCP, UDP, AZ ICMP, ESP | Az Azure használ a nyilvános IP-cím rendelt IP-konfigurációja, a példány hálózati adapteren. A példány érhető el minden rövid élettartamú port rendelkezik. |
| [2. Nyilvános Load Balancer társított virtuális gép (nem példány szintű nyilvános IP-cím-példányon)](#lb) | Port (PAT) folyamatnak álcázott az SNAT használatával a terheléselosztó előtérrendszer | TCP, UDP |Az Azure több magánhálózati IP-címek osztanak meg a nyilvános terheléselosztó előtérrendszer nyilvános IP-címét. Az Azure az előtérrendszer a PAT-rövid élettartamú port használja. |
| [3. Önálló virtuális gép (nem a terheléselosztóhoz, nincs példány szintű nyilvános IP-cím)](#defaultsnat) | A port (PAT) folyamatnak álcázott SNAT | TCP, UDP | Az Azure automatikusan jelöl ki egy nyilvános IP-címet az SNAT, a nyilvános IP-címet oszt meg a rendelkezésre állási csoport több magánhálózati IP-címek, és a nyilvános IP-cím elmúló port használja. Ebben a forgatókönyvben a fenti esetekben szolgál. Nem ajánlott, ha a szükséges átláthatóságot és irányítást. |

Ha nem szeretné a virtuális gép nyilvános IP-címtér az Azure-on kívülről végpontok kommunikálni, a hálózati biztonsági csoportok (NSG-k) segítségével letiltja a hozzáférést, igény szerint. A szakasz [megakadályozza, hogy a kimenő kapcsolat](#preventoutbound) NSG-k ismerteti részletesebben. Tervezési útmutatást, végrehajtási és a egy virtuális hálózat minden kimenő hozzáférés nélkül kezelése nem ez a cikk foglalkozik.

### <a name="ilpip"></a>1. forgatókönyv: Virtuális gép példány szintű nyilvános IP-címmel

Ebben a forgatókönyvben a VM-példány szintű nyilvános IP (ILPIP) hozzárendelt rendelkezik. Kimenő kapcsolatok illeti nem számít, hogy a virtuális gép, vagy nem elosztott terhelésű. Ebben a forgatókönyvben a többitől felett élvez elsőbbséget. Egy ILPIP használata esetén a virtuális gép minden kimenő forgalom a ILPIP használ.  

Egy virtuális géphez hozzárendelt nyilvános IP-cím egy 1:1 számosságú kapcsolatok (ahelyett, hogy 1: sok) és megvalósítva, állapot nélküli 1:1 helyezkedik el.  Álcázásos port (PAT) nincs használatban, és a virtuális gépen elérhető összes elmúló port.

Ha az alkalmazás számos kimenő forgalom kezdeményezése és SNAT portfogyás tapasztal, vegye fontolóra hozzárendelése egy [csökkentése érdekében az SNAT megkötések ILPIP](#assignilpip). Felülvizsgálat [kezelése SNAT Erőforrásfogyás](#snatexhaust) egészében.

### <a name="lb"></a>2. forgatókönyv: Elosztott terhelésű Virtuálisgép-példány szintű nyilvános IP-cím nélkül

Ebben a forgatókönyvben a virtuális gép része egy nyilvános Load Balancer háttérkészlethez. A virtuális gép nem rendelkezik hozzárendelt nyilvános IP-címet. A terheléselosztó erőforrás egy terheléselosztó-szabályt létrehozni egy hivatkozást a háttérkészlet előtérbeli nyilvános IP-Címmel kell konfigurálni.

Ha nem ez a szabály konfigurálását végrehajtani, viselkedését a forgatókönyvben leírtak szerint van-e [önálló virtuális gép nincs példány szintű nyilvános IP-címmel](#defaultsnat). Nem elengedhetetlen a a szabályt, hogy rendelkezik egy működő figyelőt a háttérkészletben sikeres végrehajtásához az állapotmintához.

Az elosztott terhelésű virtuális gép egy kimenő folyamatot hoz létre, amikor Azure lefordítja a nyilvános Load Balancer előtérbeli nyilvános IP-címét a kimenő folyamat privát forrás IP-címét. Azure SNAT használja ezt a funkciót. Az Azure is használ [PAT](#pat) való révén több magánhálózati IP-cím nyilvános IP-cím mögött. 

A terheléselosztó nyilvános IP-cím frontend-port a rövid élettartamú segítségével adja meg a virtuális gép által az egyes folyamatok megkülönböztetésére. Dinamikusan használja az SNAT [előzetesen lefoglalt elmúló port](#preallocatedports) kimenő folyamatok létrehozásakor. Ebben a környezetben a rövid élettartamú tűzfalakon SNAT SNAT portok nevezik.

SNAT portok előre kiosztott leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakaszban. Azok, amelyek felhasználhatók, is véges erőforrás. Fontos tudni, hogyan vannak [felhasznált](#pat). Megtudhatja, hogyan tervezhet a felhasználásra és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

Amikor [több nyilvános IP-címek társítva a Load Balancer alapszintű](load-balancer-multivip-overview.md), bármely, a nyilvános IP-címek vannak egy [a kimenő forgalom jelölt](#multivipsnat), és a egy véletlenszerűen kiválasztott.  

Kimenő kapcsolatok a Load Balancer alapszintű állapotának monitorozásához használja [Log Analytics terheléselosztó](load-balancer-monitor-log.md) és [eseménynaplók riasztás](load-balancer-monitor-log.md#alert-event-log) SNAT port Erőforrásfogyás üzenetek figyeléséhez.

### <a name="defaultsnat"></a>3. forgatókönyv: Önálló virtuális gép példány szintű nyilvános IP-cím nélkül

Ebben a forgatókönyvben a virtuális gép nem része egy nyilvános terheléselosztó-készlet (és nem egy Standard Load Balancer belső készlet része), és nem rendelkezik egy ILPIP-cím rendelve. Ha a virtuális Gépet hoz létre egy kimenő folyam, Azure lefordítja a kimenő folyamat egy nyilvános IP-forráscím privát forrás IP-címét. A kimenő flow használt nyilvános IP-cím nem konfigurálható, és nem számítanak bele az előfizetéshez tartozó nyilvános IP-erőforráskorlátot. A nyilvános IP-cím nem tartozik, és nem lehet foglalt. Újbóli telepítése a virtuális gép vagy a rendelkezésre állási csoportban vagy virtuálisgép-méretezési csoportot, ha a nyilvános IP-cím elérhető lesz, és a egy új nyilvános IP-cím kért. Ebben a forgatókönyvben ne használja az engedélyezési IP-címeket. Ehelyett használjon egy másik két forgatókönyv ahol, explicit módon deklarálja a kimenő forgatókönyv és a kimenő kapcsolat használandó nyilvános IP-cím.

>[!IMPORTANT] 
>Ebben a forgatókönyvben azt is vonatkozik, amikor __csak__ egy belső alapszintű terheléselosztó van csatolva. 3. forgatókönyv van __nem érhető el__ amikor egy belső Standard Load Balancer egy virtuális Géphez van csatlakoztatva.  Explicit módon létre kell hoznia [1. forgatókönyv](#ilpip) vagy [2. forgatókönyv](#lb) Standard belső terheléselosztó használata mellett.

Azure-port folyamatnak álcázott használja az SNAT ([PAT](#pat)) Ez a függvény végrehajtásához. Ez a forgatókönyv hasonlít a [2. forgatókönyv](#lb), azzal a különbséggel ott nem használt IP-cím nem szabályozza. Ez a tartalék forgatókönyv esetében, ha 1. és 2 forgatókönyvek nem létezik. Ebben a forgatókönyvben nem ajánlott, ha azt szeretné, hogy a kimenő cím felett. Ha a kimenő kapcsolatokat az alkalmazás kritikus része, egy másik forgatókönyv kell választania.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakaszban.  A virtuális gépek rendelkezésre állási csoport megosztása száma határozza meg, melyik előzetes lefoglalás szint vonatkozik.  Egy különálló virtuális gép rendelkezésre állási csoport nélkül lényegében egy készlet 1 (1024 SNAT portok) előzetes lefoglalás meghatározása céljából. SNAT portjait véges erőforrás, amely is felhasználhatók. Fontos tudni, hogyan vannak [felhasznált](#pat). Megtudhatja, hogyan tervezhet a felhasználásra és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

### <a name="combinations"></a>Több, kombinált forgatókönyvek

Egy adott eredmény eléréséhez az előző szakaszokban leírt forgatókönyveket kombinálhatók. Amikor több forgatókönyvek találhatók, vonatkozik-e olyan ellentmondások: [1. forgatókönyv](#ilpip) elsőbbséget élvez [2. forgatókönyv](#lb) és [3](#defaultsnat). [2. forgatókönyv](#lb) felülbírálások [3. forgatókönyv](#defaultsnat).

Ilyen például, egy Azure Resource Manager-alapú, ahol az alkalmazás erősen támaszkodik a célhelyekre csak korlátozott számú kimenő kapcsolatokat, azonban is fogad a terheléselosztó előtérbeli keresztül bejövő forgalom. Ebben az esetben kombinálhat 1 és 2. a mentesség forgatókönyvek. További minták, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

### <a name="multife"></a> A kimenő forgalom több előtérrendszer

#### <a name="load-balancer-standard"></a>Load Balancer Standard

Load Balancer Standard használja minden jelöltek a kimenő forgalom egyszerre időpontot, amikor [több (nyilvános) IP-előtérrendszer](load-balancer-multivip-overview.md) megtalálható. Egyes előtérrendszereken összeszoroz elérhető előzetesen lefoglalt SNAT portok száma, ha a terheléselosztási szabály engedélyezve van a kimenő kapcsolatok számára.

Választhat egy előtérbeli IP-cím használatát a kimenő kapcsolatok egy új terhelés terheléselosztási szabályt kapcsolóval le:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Általában a `disableOutboundSnat` beállítást az alapértelmezett érték _false (hamis)_ , és jelzi, hogy ez a szabály a háttérkészlet, a terheléselosztási szabály, a társított virtuális gépek kimenő SNAT programokat. A `disableOutboundSnat` módosítható _igaz_ megakadályozza, hogy a terheléselosztó kimenő kapcsolatok a virtuális gépek, a háttérkészletben a terheléselosztási szabály társított előtérbeli IP-cím használatával.  És továbbra is kijelölhet egy adott IP-cím a kimenő forgalom leírtak szerint [több kombinált forgatókönyvek](#combinations) is.

#### <a name="load-balancer-basic"></a>Load Balancer alapszintű

Load Balancer alapszintű választja a kimenő forgalom használandó egyetlen frontend amikor [több (nyilvános) IP-előtérrendszer](load-balancer-multivip-overview.md) kimenő forgalom a deduplikációra. Ez a beállítás nem konfigurálható, és gondolja át a kijelölést algoritmus véletlenszerű. Kimenő forgalom a megadott IP-címet is kijelölhet a leírtak szerint [több kombinált forgatókönyvek](#combinations).

### <a name="az"></a> A rendelkezésre állási zónák

Használata esetén [rendelkezésre állási zónák a Standard Load Balancer](load-balancer-standard-availability-zones.md), a zónaredundáns előtérrendszer biztosíthat zónaredundáns kimenő SNAT-kapcsolatok és SNAT programozási survives zóna hiba.  Zónaszintű előtérrendszer használata esetén a kimenő SNAT-kapcsolatok sorsát megoszthatja a zónához tartoznak.

## <a name="snat"></a>SNAT és a PAT ismertetése

### <a name="pat"></a>Port álcázásos SNAT (PAT)

Ha egy nyilvános terheléselosztó-erőforráshoz társítva a Virtuálisgép-példányok, minden kimenő kapcsolat forrása van átírása. A forrás az előtér a terheléselosztó nyilvános IP-címet a virtuális hálózati magánhálózati IP-címtér van újraírja. A nyilvános IP-címtér 5 rekordos (forrás IP-cím, forrásoldali portszám, IP protokoll, cél IP-címe, Célport) a folyamat egyedinek kell lennie.  Álcázásos SNAT port a TCP vagy UDP IP protokollokkal használható.

A rövid élettartamú portok (SNAT port) segítségével ennek elérése után újraírását a magánhálózati IP-forráscím, mert több folyamatok egyetlen nyilvános IP-cím származik. A port álcázásos SNAT algoritmust SNAT portok eltérően lefoglalja TCP és UDP-hez.

#### <a name="tcp"></a>SNAT TCP-portok

Egy SNAT port van felhasznált folyamat egyetlen cél IP-cím, port száma. Több TCP forgalom az azonos cél IP-cím, port és protokoll, az egyes TCP folyamatok SNAT egyetlen portot használ fel. Ez biztosítja, hogy a flow egyediek, amikor azok, azonos nyilvános IP-cím származnak, és nyissa meg az azonos cél IP-cím, port és protokoll. 

Több egy másik cél IP-cím, port és protokoll, a folyamatok egyetlen SNAT port megosztása. A cél IP-cím, port és protokoll egyedivé folyamatok nincs szükség további forrás különbséget tenni a nyilvános IP-címtér folyamatok portokat.

#### <a name="udp"></a> SNAT használt UDP-portok

Egy teljesen más algoritmust, mint az TCP SNAT-portok által kezelt UDP SNAT-portok.  Load Balancer UDP-hez "port korlátozott amerikai NAT" néven ismert algoritmust használ.  Az egyes folyamatok, attól függetlenül, cél IP-cím, port egy SNAT port használja fel.

#### <a name="exhaustion"></a>Erőforrásfogyás

SNAT port erőforrások elfogy, ha a kimenő forgalom sikertelen, amíg a meglévő folyamatok kiadási SNAT portokat. Load Balancer SNAT portok szabadít fel, amikor a folyamat bezárul, és használja a [4 perces üresjárati időkorlátot](#idletimeout) VISSZAIGÉNYLÉSE SNAT portok az inaktív folyamatok esetében.

UDP SNAT portok általában sokkal gyorsabb, mint a használt algoritmus miatt TCP SNAT portok felhasználta rendelkezésére. Meg kell tervezési és a méretezési csoport teszteléséhez az ezt a különbséget szem előtt.

Minták csökkentése érdekében a feltételeket, amelyek gyakran vezetnek SNAT portfogyás, tekintse át a [kezelése SNAT](#snatexhaust) szakaszban.

### <a name="preallocatedports"></a>A rövid élettartamú port előzetes lefoglalás folyamatnak álcázott SNAT (PAT) port

Portot álcázásos SNAT használata esetén a háttérkészlet mérete alapján egy algoritmus meghatározására száma előzetesen lefoglalt SNAT elérhető portok Azure használja ([PAT](#pat)). SNAT portjait elmúló port egy adott nyilvános IP-forráscím érhető el.

SNAT portok azonos számú előzetesen lefoglalt UDP és TCP rendre és egymástól függetlenül IP protokoll / felhasznált.  Azonban az SNAT porthasználatáról eltér attól függően, hogy a folyamat UDP vagy TCP.

>[!IMPORTANT]
>Standard Termékváltozat SNAT programozási / IP-átviteli protokoll és a terheléselosztási szabály származik.  Ha csak a TCP terheléselosztási szabály létezik, SNAT lehetőség csak a TCP. Ha csak a TCP terheléselosztási szabály rendelkezik, és kimenő SNAT UDP-hez, hozzon létre UDP terheléselosztási szabály ugyanazt a frontend alhálózatból ugyanazt a háttérkészlethez.  Ezzel megkezdődik az SNAT programozási UDP-hez.  Szabály vagy egészségügyi mintavétel működő, nem szükséges.  Alapszintű Termékváltozat SNAT SNAT mindig programok, attól függetlenül, az átviteli protokoll a terheléselosztási szabály megadott mindkét IP protokoll esetében.

Azure preallocates SNAT portok az egyes virtuális gépek hálózati adapter IP-konfigurációhoz. Amikor egy IP-konfigurációt a készletet ad hozzá, az SNAT portok vannak előzetesen lefoglalt az IP-konfiguráció, a háttérkiszolgáló-készlet mérete alapján. Kimenő forgalom létrehozásakor [PAT](#pat) dinamikusan használ fel (a korlátig előzetesen lefoglalt) és kiadások ezeket a portokat, a folyamat befejeződésekor vagy [üresjárati időtúllépés](#idletimeout) fordulhat elő.

Az alábbi táblázat az SNAT port preallocations készletméretek háttér-szint esetében:

| Készletméret (Virtuálisgép-példányok) | Előzetesen lefoglalt SNAT portok száma IP-konfiguráció|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

>[!NOTE]
> A Standard Load Balancer használatakor [több előtérrendszer](load-balancer-multivip-overview.md), [minden előtérbeli IP-cím szorozza meg a rendelkezésre álló SNAT portok száma](#multivipsnat) az előző táblázatban. Például egy háttérkészletéhez 50 virtuális gép 2 terheléselosztási szabályok, minden egyes külön előtérbeli IP-cím, az IP-konfiguráció (2 x 1024) 2048 SNAT hatportos fogja használni. A részletek megtekintéséhez [több előtérrendszer](#multife).

Ne feledje, hogy a rendelkezésre álló SNAT portok száma nem fordítja le közvetlenül a folyamatok száma. Egyetlen SNAT port több egyedi célhoz felhasználhatók. Portok csak akkor, ha szükséges, hogy egyedi folyamatok használnak fel. Tervezés és kockázatcsökkentési útmutatásért tekintse meg a szakasz kapcsolatos [kezelése az kimeríthető erőforrás](#snatexhaust) és a szakasz leírja, [PAT](#pat).

A háttérkészlet méretének módosítása hatással lehetnek egyes létrehozott folyamatok. Ha a háttérkiszolgáló-készlet méretét növeli, és be a következő réteggel értékre vált, az előzetesen lefoglalt SNAT portok fele visszaigényelt vannak az áttérés a következő nagyobb háttérkiszolgáló-készlet réteggel során. Folyamatok regenerált SNAT port társított időtúllépést okoz, és létre kell hozni. Új folyamat kísérel meg, ha a folyamat sikeres lesz azonnal, mindaddig, amíg az előzetesen lefoglalt portok érhetők el.

Ha csökkenti a háttérkiszolgáló-készlet méretét, és a egy alacsonyabb szintre szolgáltatássá SNAT elérhető portok száma növekszik. Ebben az esetben meglévő lefoglalt SNAT portok, és a saját folyamatok nem érinti.

SNAT portok hozzárendelések meghatározott IP-protokoll (TCP és UDP-karbantartása külön-külön) és jelennek meg az alábbi feltételek:

### <a name="tcp-snat-port-release"></a>TCP SNAT port kiadás

- Ha mindkét kiszolgáló vagy ügyfél küld a PÉNZÜGY/ACK, SNAT port 240 másodperc múlva elérhető lesz.
- Ha egy ÜZE látható, SNAT port 15 másodperc múlva elérhető lesz.
- elérte az üresjárat időkorlátja

### <a name="udp-snat-port-release"></a>UDP SNAT-port kiadás

- elérte az üresjárat időkorlátja

## <a name="problemsolving"></a> Problémák megoldása 

Célja, hogy ez a szakasz SNAT Erőforrásfogyás történésekről, és az Azure kimenő kapcsolatainak alakulhat ki, amely.

### <a name="snatexhaust"></a> Portfogyás SNAT (PAT) kezelése
[Elmúló port](#preallocatedports) használt [PAT](#pat) vannak egy kimeríthető erőforrás leírtak szerint [önálló virtuális gép példány szintű nyilvános IP-cím nélkül](#defaultsnat) és [elosztott terhelésű virtuális gép nélkül- Példány szintű nyilvános IP-cím](#lb).

Ha tudja, hogy, sok kimenő TCP vagy UDP-kapcsolatokat ugyanazon cél IP-cím és port még kezdeményezése, és figyelje meg a kimenő kapcsolatok sikertelen vagy végigvitelével támogatja, hogy Ön skálázását SNAT portok (előzetesen lefoglalt [rövid élettartamú portok](#preallocatedports) által használt [PAT](#pat)), általános megoldás több lehetősége is van. Tekintse át ezeket a beállításokat, és döntse el, mi érhető el, és a forgatókönyv esetén ajánlott használni. Akkor lehet, hogy egy vagy több segíthet kezelni az ebben a forgatókönyvben.

Ha problémába ütközik a kimenő kapcsolatot viselkedésének megértése, IP-verem statisztika (netstat) használhatja. Vagy vizsgálja meg a kapcsolati viselkedések csomagrögzítés használatával a hasznos lehet. Hajtsa végre ezeket a csomagrögzítés a vendég operációs rendszer a példány, vagy használjon [csomagrögzítés a Network Watcher](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Módosítsa az alkalmazást újra felhasználhatja a kapcsolatok 
Csökkentheti az SNAT-kapcsolatok az alkalmazás a újrafelhasználásával használt elmúló port igény szerint. Ez különösen igaz protokollok, például a HTTP/1.1, ahol a kapcsolat újbóli az alapértelmezett érték. És egyéb protokollok, amelyek a HTTP használata (például a többi) t viszont előnyeit. 

Újbóli mindig jobb, mint az egyes kérések egyedi, atomi TCP-kapcsolatokat. Újból felhasználhatja a további nagy teljesítményű, nagyon hatékony TCP tranzakciók eredményez.

#### <a name="connection pooling"></a>Az alkalmazás használatához a kapcsolatkészletezést módosítása
Egy kapcsolatkészlet az alkalmazásban, ahol kérelmek belsőleg elosztva rögzített számú kapcsolatok (minden újbóli felhasználása ahol csak lehetséges) sémát is alkalmazhat. Ez a séma korlátozza a használatban lévő elmúló port számát, és létrehoz egy kiszámíthatóbb környezetet. Ez a séma szerint lehetővé teszi több egyidejű művelet, amikor egyetlen kapcsolaton keresztül blokkolja a válasz egy adott művelethez a is növelheti kérések átviteli sebessége.  

Kapcsolatkészletezést előfordulhat, hogy az alkalmazás vagy a konfigurációs beállításokat az alkalmazás fejlesztéséhez használt keretein belül már létezik. A kapcsolat újbóli kapcsolatkészletezést kombinálhatja. A több kérés vállalatoknál egy fix, kiszámítható portok száma: az azonos cél IP-cím és port. A kérések is élvezhetik csökkenti a késést és az erőforrás TCP tranzakciók hatékony kihasználását. UDP-tranzakciók is hasznot, mert kezelése UDP-folyamatok számát is viszont kipufogógáz feltételek elkerülése és kezelheti az SNAT port kihasználtságával.

#### <a name="retry logic"></a>Módosítsa az alkalmazást a kevésbé agresszív újrapróbálkozási logika használata
Amikor [előzetesen lefoglalt elmúló port](#preallocatedports) használt [PAT](#pat) vannak a rendszer vagy alkalmazás esetén fordulhat elő, agresszív és a találgatásos nélkül decay és leállítási logikát miatt fordulhat elő, vagy megőrizni az Erőforrásfogyás újrapróbálkozik. A kevésbé agresszív újrapróbálkozási logikát használatával csökkentheti elmúló port iránti igény. 

A rövid élettartamú port egy 4 perces üresjárati időkorlát (nem állítható) rendelkezik. Ha az újrapróbálkozásokat túl agresszív, az Erőforrásfogyás rendelkezik saját kiürítéséhez nincs lehetőség. Ezért figyelembe vétele – hogyan és milyen gyakran – az alkalmazás újrapróbálkozik a tranzakciók a Tervező egy kritikus részét képezi.

#### <a name="assignilpip"></a>Egy példány szintű nyilvános IP-cím hozzárendelése minden virtuális Géphez
Egy ILPIP hozzárendelése módosítja, a forgatókönyv [példány szintű nyilvános IP-cím egy virtuális géphez](#ilpip). Minden rövid élettartamú port a nyilvános IP-cím az egyes virtuális Gépekhez használt érhetők el a virtuális géphez. (Ellentétben, forgatókönyvek, ahol a virtuális gépeket tartalmazó megosztott elmúló port egy nyilvános IP-cím társítva a megfelelő háttérkészlet.) Nincsenek feláldozását érdemes figyelembe venni, például a nyilvános IP-címek további költségeket és a nagy számú egyedi IP-címek engedélyezési lehetséges hatását.

>[!NOTE] 
>Ez a beállítás nem érhető el a webes feldolgozói szerepkörök.

#### <a name="multifesnat"></a>Több előtérrendszer használata

Nyilvános Standard Load Balancer használatakor hozzárendelhet [több előtérbeli IP-címet a kimenő kapcsolatok számára](#multife) és [szorozza meg a rendelkezésre álló SNAT portok száma](#preallocatedports).  Hozzon létre egy előtérbeli IP-konfiguráció, a szabály és a háttérkészlet a programozás, a nyilvános IP-címét az előtér az SNAT aktiválásához.  A szabály nem kell működni, és az állapotfigyelő mintavételező nincs szükség a sikeres legyen.  Ha több előtérrendszer számára, valamint bejövő (helyett csak a kimenő), használjon egyéni állapotmintákkal biztosítják a megbízhatóság.

>[!NOTE]
>A legtöbb esetben az SNAT portok kimerülése rossz kialakítás bejelentkezési.  Ellenőrizze, hogy megismerte, hogy miért áll skálázását portok használata több előtérrendszer SNAT portok hozzáadása előtt.  Előfordulhat, hogy a problémát, ami sikertelen később maszkolás.

#### <a name="scaleout"></a>Horizontális felskálázás

[Előzetesen lefoglalt portok](#preallocatedports) háttérrendszer készlet mérete alapján, és a csoportosított leskálázáskor, amikor néhány portot kell kiosztani a következő nagyobb háttérbeli készletet méretréteg befogadásához rétegekben vannak hozzárendelve.  Előfordulhat, hogy egy adott szint maximális méretét a háttérkészlet felskálázásával megnövelje SNAT port kihasználtsága egy adott előtérbeli lehetőség.  Az alkalmazás horizontális felskálázása hatékonyan ehhez.

Például két virtuális gépet a háttérkészletben kellene álló IP-konfiguráció, így összesen 2048 SNAT portok a központi telepítés 1024 SNAT-port.  Ha az üzembe helyezés-ra, 50 virtuális gépeket, még ha száma előzetesen lefoglalt virtuális gépenként összesen 51,200 (50 x 1024-) portok marad állandó SNAT portok az üzemelő példány által használható.  Ha szeretné az üzemelő példány horizontális, ellenőrizheti a [előzetesen lefoglalt portok](#preallocatedports) / csomag győződjön meg arról, hogy a horizontális felskálázás a megfelelő szint maximális formázása.  Az előző példában ha horizontális felskálázás legfeljebb 50 példányt helyett 51 választotta volna lenne végrehajtási a következő réteg és a záró fel mint összesen kevesebb SNAT-porttal, valamint virtuális gépenként.

Horizontális felskálázás a következő nagyobb háttérrendszer készlet mérete szintre van-e az egyes ideje a kimenő kapcsolatok Ha lefoglalt portokat kell kiosztani.  Ha csak használja az SNAT portok némelyike, között a következő nagyobb háttérkiszolgáló-készlet méretét horizontális felskálázás nem léteznek.  A meglévő fél portokat fog kiosztani minden alkalommal, amikor helyez át a következő háttérkiszolgáló-készlet réteggel.  Ha nem szeretné, hogy végre lehessen hajtani ezt, a központi telepítést a méret az alakzat szeretne.  Vagy ellenőrizze, hogy az alkalmazás észlelésére, és ismételje meg igény szerint.  TCP életben segítheti az észlelés, ha az SNAT portok már nem függvény miatt újra kiosztja folyamatban van.

### <a name="idletimeout"></a>A kimenő üresjárati időtúllépés életben használatával

Kimenő kapcsolatok rendelkezik egy 4 perces üresjárati időkorlátot. Ez az időkorlát nem állítható. Azonban segítségével átviteli (például TCP életben) vagy alkalmazásszintű életben frissítse az üresjárati folyamat, és szükség esetén alaphelyzetbe állíthatja az üresjárati időkorlát.  

Használja a TCP életben, esetén elegendő ahhoz, hogy a kapcsolat egyik oldalán engedélyezheti őket. Például elegendő lehetővé teszi a kiszolgálói oldalon csak az a folyamat az inaktivitási időzítő alaphelyzetbe állítása, és azt nem szükséges mindkét kezdeményezett TCP életben.  Hasonló fogalmak alkalmazásréteg, beleértve az ügyfél és kiszolgáló konfigurációi is léteznek.  Ellenőrizze a kiszolgálói oldalon az alkalmazás adott életben milyen lehetőség is létezik.

## <a name="discoveroutbound"></a>A nyilvános IP-cím, amely egy virtuális gép felderítése
Számos módon határozza meg a nyilvános IP-forráscím egy kimenő kapcsolat. OpenDNS biztosít egy szolgáltatás, amely jeleníti meg, a virtuális gép nyilvános IP-címét. 

Az nslookup parancs használatával a OpenDNS feloldó elküldheti a név myip.opendns.com DNS-lekérdezést. A szolgáltatás visszaadja a forrás IP-címet, amely a lekérdezés el lett küldve. A virtuális gépről futtassa a következő lekérdezést, a válasz esetén a használt virtuális gép nyilvános IP-cím:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Megakadályozza, hogy a kimenő kapcsolatok
Néha érdemes engedélyezendő kimenő folyamat létrehozása a virtuális gép nem kívánatos. Előfordulhat, hogy mely célok elérhető, és kimenő forgalom kezeléséhez szükséges, vagy hogy mely célok megkezdheti a bejövő forgalom. Ebben az esetben használhatja [hálózati biztonsági csoportok](../virtual-network/security-overview.md) kezeléséhez, amely a virtuális gép elérheti a célhelyre. NSG-k segítségével is kezelheti, mely nyilvános cél kezdeményezheti a bejövő forgalom.

Amikor alkalmazza az NSG-KET egy elosztott terhelésű virtuális gép, figyelmet fordítani az [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags) és [alapértelmezett biztonsági szabályokat](../virtual-network/security-overview.md#default-security-rules). Biztosítania kell, hogy a virtuális gép az Azure Load Balancer állapot-mintavételi kérések fogadhat. 

Ha az NSG-KET blokkolja az egészségügyi mintavételi kérelmeit a AZURE_LOADBALANCER az alapértelmezett címke, a virtuális gép állapotadat-mintavétel meghiúsul, és a virtuális gép van megjelölve. Terheléselosztó nem küld új folyamatok a virtuális Gépre.

## <a name="limitations"></a>Korlátozások
- Új választható DisableOutboundSnat esetén nem érhető el beállítás konfigurálása egy terheléselosztási szabályt a portálon.  REST, sablon vagy ügyféloldali eszközök használata.
- Webes feldolgozói szerepkörök egy VNet és más Microsoft-platformszolgáltatások nélkül elérhető lehet, ha csak egy belső Standard Load Balancer miatt a hogyan előtti – VNet-szolgáltatások és más platform sem services függvény mellékhatása szolgál. Ne használja ezt a mellékhatást, maga a megfelelő szolgáltatás, vagy az alapul szolgáló platform értesítés nélkül változhatnak. Meg kell mindig feltételezze, hogy explicit módon létrehozása a kimenő kapcsolat, ha szükséges, ha egy belső Standard Load Balancer használata csak kell. A [SNAT alapértelmezett](#defaultsnat) ebben a cikkben leírt 3. forgatókönyv nem érhető el.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [terheléselosztó](load-balancer-overview.md).
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- Tudjon meg többet [hálózati biztonsági csoportok](../virtual-network/security-overview.md).
- A másik hívóbetűt némelyikét [hálózat képességeivel](../networking/networking-overview.md) az Azure-ban.
