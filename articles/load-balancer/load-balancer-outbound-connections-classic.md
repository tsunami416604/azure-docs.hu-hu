---
title: Kimenő kapcsolatok (klasszikus) Azure-ban
titlesuffix: Azure Load Balancer
description: Ez a cikk azt ismerteti, hogyan biztosítja az Azure cloud services nyilvános internetes szolgáltatásokkal kommunikálni.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 3267d79387586f5ca8475d7ac0ed0f86d3f64f0d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876942"
---
# <a name="outbound-connections-classic"></a>Kimenő kapcsolatok (klasszikus)

Az Azure számos különféle mechanizmus útján kimenő kapcsolat az ügyfél központi telepítések biztosít. A cikkből megtudhatja, Mik azok az esetek, amikor azok a alkalmazni, hogyan működnek és hogyan kezelhetők.

>[!NOTE]
>Ez a cikk csak a klasszikus üzembe helyezés ismerteti.  Felülvizsgálat [kimenő kapcsolatok](load-balancer-outbound-connections.md) az összes Resource Manager üzembe helyezési forgatókönyv az Azure-ban.

Központi telepítés az Azure-ban a nyilvános IP-címtér az Azure-on kívülről végpontok kommunikálhat. Ha egy példány egy kimenő folyam lévő nyilvános IP-címterület célra kezdeményezi, az Azure dinamikusan rendeli a magánhálózati IP-cím nyilvános IP-címre. Ez a leképezés létrehozása után a kimenő származó folyamat a visszatérő forgalom is elérheti a magánhálózati IP-címet, adja meg a folyamat.

Az Azure forrás hálózati címfordítás (SNAT) használ, ez a függvény végrehajtásához. Több magánhálózati IP-címek vannak folyamatnak álcázott mögött egyetlen nyilvános IP-címet, ha az Azure használ [címfordítás (PAT) port](#pat) a magánhálózati IP-címek révén. A rövid élettartamú port a PAT használja, és [előzetesen lefoglalt](#preallocatedports) készlet mérete alapján.

Egyszerre több [kimenő forgatókönyveket](#scenarios). Igény szerint kombinálhatja ezeket a forgatókönyveket. Tekintse át alaposan, hogy értelmezését képességek, korlátozások és minták a-alapú üzemi modellre vonatkoznak és a forgatókönyvet. Tekintse át az útmutató a [ezek a forgatókönyvek kezelése](#snatexhaust).

## <a name="scenarios"></a>Forgatókönyv áttekintése

Az Azure kimenő kapcsolatot klasszikus üzembe helyezéssel eléréséhez három különböző módszert biztosít.  Nem minden klasszikus üzembe helyezéssel rendelkezik a rendelkezésükre álló mindhárom forgatókönyv:

| Forgatókönyv | Módszer | IP-protokollok | Leírás | Webes feldolgozó szerepkör | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Virtuális gép példány szintű nyilvános IP-címmel](#ilpip) | Port folyamatnak álcázott nem használja az SNAT, | TCP, UDP, ICMP, ESP | Az Azure a hozzárendelt virtuális gép nyilvános IP-címet használ. A példány érhető el minden rövid élettartamú port rendelkezik. | Nem | Igen |
| [2. a nyilvános elosztott terhelésű végpont](#publiclbendpoint) | A port (PAT) folyamatnak álcázott nyilvános végpontjára mutató SNAT | TCP, UDP | Az Azure IP-cím nyilvános nyilvános végpontot a több, saját végponttal rendelkező fájlmegosztások. Azure rövid élettartamú port a nyilvános végpont PAT használ. | Igen | Igen |
| [3. Önálló virtuális gép](#defaultsnat) | A port (PAT) folyamatnak álcázott SNAT | TCP, UDP | Az Azure automatikusan jelöl ki egy nyilvános IP-címet az SNAT, a nyilvános IP-címet oszt meg a teljes telepítést, és PAT elmúló port a nyilvános végpont IP-cím használja. Ez az a fenti esetekben egy tartalék forgatókönyvet. Nem ajánlott, ha a szükséges átláthatóságot és irányítást. | Igen | Igen |

Ez a kimenő kapcsolat funkció érhető el az Azure Resource Manager üzembe helyezések egy részét.  

Különböző központi telepítésénél a klasszikus rendelkezik a különböző funkciókat:

| Klasszikus üzembe helyezés | Elérhető funkciók | 
| --- | --- |
| Virtuális gép | a forgatókönyv [1](#ilpip), [2](#publiclbendpoint), vagy [3](#defaultsnat) |
| Webes feldolgozó szerepkör | csak a forgatókönyv [2](#publiclbendpoint), [3](#defaultsnat) | 

[Kockázatcsökkentési stratégia](#snatexhaust) azonos különbségek is rendelkezik.

Az előzetes lefoglalása elmúló port a klasszikus üzemi modellben a PAT használt algoritmust ugyanúgy történik, mint az Azure Resource Manager-erőforrás üzembe helyezések.

### <a name="ilpip"></a>1. forgatókönyv: Virtuális gép példány szintű nyilvános IP-címmel

Ebben a forgatókönyvben a VM-példány szintű nyilvános IP (ILPIP) hozzárendelt rendelkezik. Kimenő kapcsolatok illeti nem számít, hogy a virtuális gépen az elosztott terhelésű végpontot, vagy nem. Ebben a forgatókönyvben a többitől felett élvez elsőbbséget. Egy ILPIP használata esetén a virtuális gép minden kimenő forgalom a ILPIP használ.  

Egy nyilvános egy virtuális géphez rendelt IP-1:1 kapcsolatot (nem 1:many) és megvalósítva, állapot nélküli 1:1 helyezkedik el.  Álcázásos port (PAT) nincs használatban, és a virtuális gépen elérhető összes elmúló port.

Ha az alkalmazás számos kimenő forgalom kezdeményezése és SNAT portfogyás tapasztal, vegye fontolóra hozzárendelése egy [csökkentése érdekében az SNAT megkötések ILPIP](#assignilpip). Felülvizsgálat [kezelése SNAT Erőforrásfogyás](#snatexhaust) egészében.

### <a name="publiclbendpoint"></a>2. forgatókönyv: Nyilvános elosztott terhelésű végpont

Ebben a forgatókönyvben a virtuális gép vagy a webes feldolgozói szerepkör társítva egy nyilvános IP-címet az elosztott terhelésű végpont. A virtuális gép nem rendelkezik hozzárendelt nyilvános IP-címet. 

Ha az elosztott terhelésű virtuális gép egy kimenő folyamatot hoz létre, az Azure a privát IP-forráscím a kimenő flow nyilvános IP-címet a nyilvános elosztott terhelésű végpont fordítja le. Azure SNAT használja ezt a funkciót. Az Azure is használ [PAT](#pat) való révén több magánhálózati IP-cím nyilvános IP-cím mögött. 

A terheléselosztó nyilvános IP-cím frontend-port a rövid élettartamú segítségével adja meg a virtuális gép által az egyes folyamatok megkülönböztetésére. Dinamikusan használja az SNAT [előzetesen lefoglalt elmúló port](#preallocatedports) kimenő folyamatok létrehozásakor. Ebben a környezetben a rövid élettartamú tűzfalakon SNAT SNAT portok nevezik.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakaszban. Azok, amelyek felhasználhatók, is véges erőforrás. Fontos tudni, hogyan vannak [felhasznált](#pat). Megtudhatja, hogyan tervezhet a felhasználásra és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

Amikor [több nyilvános terheléselosztásos végpontjait](load-balancer-multivip.md) létezik, a nyilvános IP-címek bármelyike kimenő forgalom csatlakozni kívánó és egy véletlenszerűen kiválasztott.  

### <a name="defaultsnat"></a>3. forgatókönyv: Hozzárendelt nyilvános IP-cím

Ebben a forgatókönyvben a virtuális gép vagy a webes feldolgozói szerepkör nem képezi részét nyilvános kiegyenlített terhelésű végpontot.  És a virtuális gép esetén nincs hozzárendelve egy ILPIP-cím. Ha a virtuális Gépet hoz létre egy kimenő folyam, Azure lefordítja a kimenő folyamat egy nyilvános IP-forráscím privát forrás IP-címét. A kimenő flow használt nyilvános IP-cím nem konfigurálható, és nem számítanak bele az előfizetéshez tartozó nyilvános IP-erőforráskorlátot.  Az Azure automatikusan foglalja le ezt a címet.

Azure-port folyamatnak álcázott használja az SNAT ([PAT](#pat)) Ez a függvény végrehajtásához. Ebben a forgatókönyvben a 2,. forgatókönyv hasonló, azzal a különbséggel ott nem használt IP-cím nem szabályozza. Ez a tartalék forgatókönyv esetében, ha 1. és 2 forgatókönyvek nem létezik. Ebben a forgatókönyvben nem ajánlott, ha azt szeretné, hogy a kimenő cím felett. Ha a kimenő kapcsolatokat az alkalmazás kritikus része, egy másik forgatókönyv kell választotta.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakaszban.  A virtuális gépek vagy a webes feldolgozói szerepkörök megosztása a nyilvános IP-cím számát előzetesen lefoglalt elmúló port számát határozza meg.   Fontos tudni, hogyan vannak [felhasznált](#pat). Megtudhatja, hogyan tervezhet a felhasználásra és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

## <a name="snat"></a>SNAT és a PAT ismertetése

### <a name="pat"></a>Port álcázásos SNAT (PAT)

Ha egy központi telepítést hajt végre egy kimenő kapcsolatot, a rendszer feladatátvitelt minden kimenő kapcsolat forrása. A forrás az üzemelő példányhoz (a fent leírt forgatókönyvek alapján) hozzárendelt nyilvános IP-címhez van feladatátvitelt a magánhálózati IP-címterét. A nyilvános IP-címtér 5 rekordos (forrás IP-cím, forrásoldali portszám, IP protokoll, cél IP-címe, Célport) a folyamat egyedinek kell lennie.  

A rövid élettartamú portok (SNAT port) segítségével ennek elérése után újraírását a magánhálózati IP-forráscím, mert több folyamatok egyetlen nyilvános IP-cím származik. 

Egy SNAT-port száma a flow számára egy egy cél IP-cím, port és protokoll használja fel. Több folyamatok, az azonos cél IP-cím, port és protokoll az egyes folyamatok SNAT egyetlen portot használ fel. Ez biztosítja, hogy a flow egyediek, amikor azok, azonos nyilvános IP-cím származnak, és nyissa meg az azonos cél IP-cím, port és protokoll. 

Több egy másik cél IP-cím, port és protokoll, a folyamatok egyetlen SNAT port megosztása. A cél IP-cím, port és protokoll egyedivé folyamatok nincs szükség további forrás különbséget tenni a nyilvános IP-címtér folyamatok portokat.

SNAT port erőforrások elfogy, ha a kimenő forgalom sikertelen, amíg a meglévő folyamatok kiadási SNAT portokat. Load Balancer SNAT portok szabadít fel, amikor a folyamat bezárul, és használja a [4 perces üresjárati időkorlátot](#idletimeout) VISSZAIGÉNYLÉSE SNAT portok az inaktív folyamatok esetében.

Minták csökkentése érdekében a feltételeket, amelyek gyakran vezetnek SNAT portfogyás, tekintse át a [kezelése SNAT](#snatexhaust) szakaszban.

### <a name="preallocatedports"></a>A rövid élettartamú port előzetes lefoglalás folyamatnak álcázott SNAT (PAT) port

Portot álcázásos SNAT használata esetén a háttérkészlet mérete alapján egy algoritmus meghatározására száma előzetesen lefoglalt SNAT elérhető portok Azure használja ([PAT](#pat)). SNAT portjait elmúló port egy adott nyilvános IP-forráscím érhető el.

Azure preallocates SNAT portok során egy példány van üzembe helyezve, hány virtuális gép vagy a webes feldolgozói szerepkör-példány osztozik az adott nyilvános IP-cím alapján.  Kimenő forgalom létrehozásakor [PAT](#pat) dinamikusan használ fel (a korlátig előzetesen lefoglalt) és kiadások ezeket a portokat, amikor a folyamat bezárása vagy üresjárati időtúllépés fordulhat elő.

Az alábbi táblázat az SNAT port preallocations készletméretek háttér-szint esetében:

| Példányok | Előzetesen lefoglalt SNAT portok példányonként |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Ne feledje, hogy a rendelkezésre álló SNAT portok száma nem fordítja le közvetlenül a folyamatok száma. Egyetlen SNAT port több egyedi célhoz felhasználhatók. Portok csak akkor, ha szükséges, hogy egyedi folyamatok használnak fel. Tervezés és kockázatcsökkentési útmutatásért tekintse meg a szakasz kapcsolatos [kezelése az kimeríthető erőforrás](#snatexhaust) és a szakasz leírja, [PAT](#pat).

Az üzemelő példány méretének módosítása hatással lehetnek egyes létrehozott folyamatok. Ha a háttérkiszolgáló-készlet méretét növeli, és be a következő réteggel értékre vált, az előzetesen lefoglalt SNAT portok fele visszaigényelt vannak az áttérés a következő nagyobb háttérkiszolgáló-készlet réteggel során. Folyamatok regenerált SNAT port társított időtúllépést okoz, és létre kell hozni. Új folyamat kísérel meg, ha a folyamat sikeres lesz azonnal, mindaddig, amíg az előzetesen lefoglalt portok érhetők el.

Ha csökkenti a központi telepítés méretét, és a egy alacsonyabb szintre szolgáltatássá SNAT elérhető portok száma növekszik. Ebben az esetben meglévő lefoglalt SNAT portok, és a saját folyamatok nem érinti.

Egy felhőalapú szolgáltatás újratelepítése vagy megváltozott, az infrastruktúra ideiglenesen feltétlenül jelentik a háttérkészlet, a tényleges akár kétszer, nagy méretű lehet, és Azure fog viszont készletméret kevesebb SNAT példányonként portok a vártnál.  Ez ideiglenesen megnövelheti az SNAT portfogyás valószínűségét. Végül a készlet méretét veszi át a tényleges méretre, és az Azure automatikusan megnöveli a fenti táblázat alapján a várt számú előzetesen lefoglalt SNAT-portokat.  Ez a viselkedés a rendszer kialakításából fakad, és nem konfigurálható.

SNAT portok hozzárendelések meghatározott IP-protokoll (TCP és UDP-karbantartása külön-külön) és jelennek meg az alábbi feltételek:

### <a name="tcp-snat-port-release"></a>TCP SNAT port kiadás

- Ha mindkét kiszolgáló vagy ügyfél küld a PÉNZÜGY/ACK, SNAT port 240 másodperc múlva elérhető lesz.
- Ha egy ÜZE látható, SNAT port 15 másodperc múlva elérhető lesz.
- elérte az üresjárat időkorlátja

### <a name="udp-snat-port-release"></a>UDP SNAT-port kiadás

- elérte az üresjárat időkorlátja

## <a name="problemsolving"></a> Problémák megoldása 

Ez a szakasz célja SNAT Erőforrásfogyás és az egyéb forgatókönyvek, melyek előfordulhatnak az Azure kimenő kapcsolatainak történésekről.

### <a name="snatexhaust"></a> Portfogyás SNAT (PAT) kezelése
[Elmúló port](#preallocatedports) használt [PAT](#pat) vannak egy kimeríthető erőforrás leírtak szerint [nincs nyilvános IP-Címmel társított](#defaultsnat) és [nyilvános elosztott terhelésű végpont](#publiclbendpoint).

Ha tudja, hogy, sok kimenő TCP vagy UDP-kapcsolatokat ugyanazon cél IP-cím és port még kezdeményezése, és figyelje meg a kimenő kapcsolatok sikertelen vagy végigvitelével támogatja, hogy Ön skálázását SNAT portok (előzetesen lefoglalt [rövid élettartamú portok](#preallocatedports) által használt [PAT](#pat)), általános megoldás több lehetősége is van. Tekintse át ezeket a beállításokat, és döntse el, mi érhető el, és a forgatókönyv esetén ajánlott használni. Akkor lehet, hogy egy vagy több segíthet kezelni az ebben a forgatókönyvben.

Ha problémába ütközik a kimenő kapcsolatot viselkedésének megértése, IP-verem statisztika (netstat) használhatja. Vagy vizsgálja meg a kapcsolati viselkedések csomagrögzítés használatával a hasznos lehet.

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
Egy ILPIP hozzárendelése módosítja, a forgatókönyv [példány szintű nyilvános IP-cím egy virtuális géphez](#ilpip). Minden rövid élettartamú port a nyilvános IP-cím az egyes virtuális Gépekhez használt érhetők el a virtuális géphez. (Ellentétben, forgatókönyvek, ahol a virtuális gépeket tartalmazó megosztott elmúló port egy nyilvános IP-cím társítva a megfelelő központi telepítési.) Nincsenek feláldozását érdemes figyelembe venni, például a nagy számú egyedi IP-címek engedélyezési lehetséges hatását.

>[!NOTE] 
>Ez a beállítás nem érhető el a webes feldolgozói szerepkörök.

### <a name="idletimeout"></a>A kimenő üresjárati időtúllépés életben használatával

Kimenő kapcsolatok rendelkezik egy 4 perces üresjárati időkorlátot. Ez az időkorlát nem állítható. Azonban segítségével átviteli (például TCP életben) vagy alkalmazásszintű életben frissítse az üresjárati folyamat, és szükség esetén alaphelyzetbe állíthatja az üresjárati időkorlát.  Ellenőrizze a nyertes bármely csomagolt szoftver ezt támogatja-e vagy engedélyezését.  Általában csak egy oldalon kell alaphelyzetbe állítja az üresjárati időkorlát életben készítése. 

## <a name="discoveroutbound"></a>A nyilvános IP-cím, amely egy virtuális gép felderítése
Számos módon határozza meg a nyilvános IP-forráscím egy kimenő kapcsolat. OpenDNS biztosít egy szolgáltatás, amely jeleníti meg, a virtuális gép nyilvános IP-címét. 

Az nslookup parancs használatával a OpenDNS feloldó elküldheti a név myip.opendns.com DNS-lekérdezést. A szolgáltatás visszaadja a forrás IP-címet, amely a lekérdezés el lett küldve. A virtuális gépről futtassa a következő lekérdezést, a válasz esetén a használt virtuális gép nyilvános IP-cím:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>További lépések

- Tudjon meg többet [terheléselosztó](load-balancer-overview.md) a Resource Manager üzembe helyezések használt.
- Mód ismertetése [kimenő kapcsolat](load-balancer-outbound-connections.md) forgatókönyvek Resource Manager üzembe helyezések érhető el.
