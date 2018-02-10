---
title: "Az Azure-ban kimenő kapcsolatok ismertetése |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan Azure lehetővé teszi a virtuális gépek nyilvános internetes szolgáltatásokkal kommunikálni."
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Kimenő kapcsolatok áttekintése az Azure-ban

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure felhasználói telepítés a kimenő kapcsolatot biztosít számos különböző módon.  A cikkből megtudhatja, Mik azok a helyzetek, amikor vonatkoznak, leírás és kezelésük módjával.

A központi telepítés az Azure-végpontok nyilvános IP-címtér az Azure-on kívüli kommunikációt. Ha egy példány egy kimenő folyam lévő nyilvános IP-címtér célra, Azure dinamikusan rendeli a magánhálózati IP-cím egy nyilvános IP-cím.  Ez a leképezés létrehozása után a kimenő kezdeményezésű áramlás forgalom is elérhető a magánhálózati IP-cím a folyamat származási helyének.

Azure forrás hálózati címfordítás (SNAT) használja ezt a funkciót.  Több privát IP-címek vannak színleg mögött egyetlen nyilvános IP-címnek, az Azure használja [címfordítást (PAT) port](#pat) helyettesítő magánhálózati IP-címek számára.  Elmúló port a PAT használja, és [előzetesen lefoglalt](#preallocatedports) készlet mérete alapján.

Egyszerre több [kimenő forgatókönyvek](#scenarios). Ezek a forgatókönyvek is használható együtt, igény szerint. Tekintse át alaposan, hogy a képességek, korlátozások és minták tájékozódjon a telepítési modell vonatkoznak és a forgatókönyvet.  Tekintse át az útmutató a [forgatókönyvekben kezelése](#snatexhaust).

## <a name="scenarios"></a>Forgatókönyv áttekintése

Azure két fő üzembe helyezési modellel rendelkezik: Azure Resource Manager és klasszikus. Terheléselosztó és a kapcsolódó erőforrások vannak explicit módon definiálva használata [Azure Resource Manager erőforrások](#arm).  Klasszikus üzembe helyezés terheléselosztó fogalma absztrakt, és egy hasonló funkciót végpontjai definíciója express egy [felhőalapú szolgáltatás](#classic). Az alkalmazandó [forgatókönyvek](#scenarios) a központi telepítés függő melyik központi telepítési modellt használja.

### <a name="arm"></a>Az Azure Resource Manager

Azure jelenleg három különböző módszert biztosít eléréséhez kimenő kapcsolódás az Azure Resource Manager erőforrások.  [Klasszikus](#classic) központi telepítések rendelkezik ezekkel az esetekkel egy részét.

| Forgatókönyv | Módszer | Leírás |
| --- | --- | --- |
| [1. Virtuális gép példány szint nyilvános IP-cím (vagy anélkül terheléselosztó)](#ilpip) | SNAT, színleg nem használt port |Azure használ a nyilvános IP-címet hozzárendelni az IP-konfiguráció, a példány hálózati adaptert.  A példány érhető el az összes rövid élettartamú port tartozik. |
| [2. Nyilvános terheléselosztóhoz társított virtuális gép (nem példány szint nyilvános IP-cím példányon)](#lb) | A port színleg (PAT) SNAT használ a Load Balancer frontend(s) |Azure osztja meg a nyilvános IP-cím a nyilvános terheléselosztó frontend(s) több privát IP-címek és a PAT frontend(s) elmúló portját használja. |
| [3. Önálló virtuális gép (nem a terheléselosztóhoz, példány szint nyilvános IP-cím)](#defaultsnat) | A port színleg (PAT) SNAT | Azure automatikusan meg, míg a nyilvános IP-címnek a SNAT, a nyilvános IP-címet oszt meg a rendelkezésre állási csoport több magánhálózati IP-címét, és a nyilvános IP-cím elmúló port használ.  Ebben a forgatókönyvben egy tartalék forgatókönyv a portáladatbázis előző forgatókönyvek esetén 1 és 2, és nem ajánlott, ha szüksége figyelemmel kísérelhetik és vezérelhetik. |

Ha nem szeretné, hogy a virtuális gépek végpontok nyilvános IP-címtér az Azure-on kívüli kommunikálni, a hálózati biztonsági csoportok (NSG) segítségével letiltja a hozzáférést, igény szerint.  NSG-k használatával részletesen tárgyalt [megakadályozza a kimenő kapcsolat](#preventoutbound).  Tervezési útmutatást és megvalósítási adatai tervezése és kezeléséhez minden kimenő-hozzáférés nélküli virtuális hálózat Ez a cikk hatókörén kívül esik.

### <a name="classic"></a>Klasszikus (felhőszolgáltatások)

A klasszikus központi telepítéseknél használható forgatókönyvek a következők rendelkezésre forgatókönyvek részhalmazát [Azure Resource Manager](#arm) Load Balancer alapszintű és a központi telepítései.

A klasszikus virtuális gépnek a azonos három alapvető forgatókönyv az Azure Resource Manager erőforrások leírtak ([1](#ilpip), [2](#lb), [3](#defaultsnat)). A klasszikus webes feldolgozói szerepkör csak akkor van két olyan eset ([2](#lb), [3](#defaultsnat)).  [Megoldás stratégiák](#snatexhaust) is azonos eltéréseket.

A felhasznált algoritmus [előzetes lefoglalása elmúló port](#ephemeralprots) klasszikus telepítések PAT ugyanúgy Azure Resource Manager erőforrás üzembe helyezések érték.  

### <a name="ilpip"></a>1. forgatókönyv: Virtuális gép példány szint nyilvános IP-címmel

Ebben a forgatókönyvben a virtuális Gépnek legyen egy példány szint nyilvános IP (ILPIP) rendelve. Kimenő kapcsolatok illeti nem számít, hogy a virtuális gép elosztott terhelésű vagy nem.  Ebben a forgatókönyvben élvez a többi. Egy ILPIP használata esetén a virtuális Gépet a ILPIP minden kimenő forgalom használ.  

A virtuális Gépnek legyen használható elmúló port, és nem használja a port színleg (PAT).

Ha az alkalmazás számos kimenő forgalom kezdeményez, és SNAT port Erőforrásfogyás tapasztal, érdemes lehet rendelni egy [SNAT megkötések mérséklése ILPIP](#assignilpip). Felülvizsgálati [kezelése SNAT Erőforrásfogyás](#snatexhaust) azt teljes egészében.

### <a name="lb"></a>2. forgatókönyv: Elosztott terhelésű virtuális gép nélküli példány szint nyilvános IP-cím

Ebben a forgatókönyvben a VM része egy nyilvános terheléselosztó háttérkészletének.  A virtuális gép nem rendelkezik a nyilvános IP-cím hozzárendelve. A terheléselosztó erőforrás olyan terheléselosztó szabályhoz a nyilvános IP-előtérbeli a háttérkészlet közötti kapcsolat létrehozásához kell konfigurálni. Ha nem tölti ki a szabály konfigurálását, a rendszer nem a forgatókönyvhöz, amelyben a [önálló virtuális gép, és nem példány szint nyilvános IP-cím](#defaultsnat).  Nincs szükség a szabály sikeres háttérkészlet vagy a állapotmintáihoz működő figyelőt.

A terheléselosztással rendelkező virtuális Gépet egy kimenő folyam hoz létre, amikor az Azure eszköz a személyes forrás IP-címét a kimenő folyam a nyilvános terheléselosztó előtérbeli nyilvános IP-címére. Azure forrás hálózati cím fordítási (SNAT) használja ezt a funkciót, valamint [címfordítást (PAT) port](#pat) a helyettesítő több privát IP-cím, egy nyilvános IP-cím mögött. A Load Balancer nyilvános IP-cím előtér-rövid élettartamú port segítségével különböztetheti meg egymástól a virtuális gép által az egyes adatfolyamok. SNAT dinamikusan használ [előzetesen lefoglalt elmúló port](#preallocatedports) kimenő adatfolyamok létrehozásakor. Ebben a környezetben használt SNAT porttartomány nevezzük SNAT portok.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakaszt, és, amelyek segítségével nagyon gyorsan kimerítették véges forrást. Fontos tudni, hogyan vannak [felhasznált](#pat). Felülvizsgálati [kezelése SNAT Erőforrásfogyás](#snatexhaust) annak megértése, hogyan tervezhet a és szükség szerint.

Ha [több (nyilvános) IP-címek társítva a Load Balancer alapvető](load-balancer-multivip-overview.md), bármely, a nyilvános IP-címek vannak egy [kimenő tranzakciók és egy jelölt ki van jelölve](#multivipsnat).  

Használhat [Naplóelemzési terheléselosztóhoz](load-balancer-monitor-log.md) és [riasztási eseménynaplójában, figyelje a SNAT port Erőforrásfogyás üzenetek](load-balancer-monitor-log.md#alert-event-log) figyelheti a Load Balancer alapvető kimenő kapcsolatok állapotát.

### <a name="defaultsnat"></a>3. forgatókönyv: Önálló virtuális gép példány szint nyilvános IP-cím nélküli

A virtuális gép nem része egy Azure Load Balancer címkészletet, és nincs hozzárendelve példány szint nyilvános IP (ILPIP) címe. Ha a virtuális Gépet hoz létre egy kimenő folyam, Azure lefordítja a nyilvános IP-forráscím kimenő folyam titkos forrás IP-címét. A nyilvános IP-cím, a kimenő folyam használt nem konfigurálható, és nem tartoznak bele az előfizetés nyilvános IP-erőforrás korlátját ellen. Azure használ forrás hálózati cím fordítási (SNAT) port színleg ([PAT](#pat)) Ez a függvény végrehajtásához. Ebben a forgatókönyvben hasonlít [2. forgatókönyv](#lb), azonban nem szabályozhatják a használt IP-cím van.  Ez az a tartalék forgatókönyv amikor forgatókönyvek 1 és 2. forgatókönyv nem léteznek.  Ebben a forgatókönyvben nem ajánlott, ha ellenőrzése alatt tartja a kimenő címet van szükség.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakaszt, és, amelyek segítségével nagyon gyorsan kimerítették véges forrást. Fontos tudni, hogyan vannak [felhasznált](#pat). Felülvizsgálati [kezelése SNAT Erőforrásfogyás](#snatexhaust) annak megértése, hogyan tervezhet a és szükség szerint.

### <a name="combinations"></a>Több, kombinált forgatókönyv

A forgatókönyvek az portáladatbázis előző szakaszokban ismertetett kombinálható egy adott eredmény elérése érdekében.  Ha több forgatókönyv jelen, egy rangsora vonatkozik: [1. forgatókönyv](#ilpip) elsőbbséget élvez [2. forgatókönyv](#lb) és [3](#defaultsnat) (Azure Resource Manager esetén), és [2. forgatókönyv](#lb) felülbírálások [3. forgatókönyv](#defaultsnat) (Azure Resource Manager és klasszikus).

Példa: az Azure Resource Manager központi telepítése, amikor az alkalmazás erősen támaszkodik a célhelyekre csak korlátozott számú kimenő kapcsolatokat, de is fogad a bejövő forgalom a terheléselosztó előtérbeli keresztül. Ebben az esetben volt kombinálásával forgatókönyvek 1 & mentességre 2.  Felülvizsgálati [kezelése SNAT Erőforrásfogyás](#snatexhaust) további mintára.

### <a name="multivipsnat"></a>A kimenő forgalom több frontends

Load Balancer alapszintű lesz kiválasztott egyetlen előtérbeli használt kimenő forgalom amikor [több (nyilvános) IP-frontends](load-balancer-multivip-overview.md) kimenő forgalom vár.  Ez a választás nem konfigurálható, a kijelölés algoritmus kell tekinteni véletlenszerű.  Kijelölhet egy adott IP-címet a kimenő [forgatókönyvek kombinált](#combinations).

## <a name="snat"></a>SNAT és a PAT ismertetése

### <a name="pat"></a>Port színleg SNAT (PAT)

Egy nyilvános terheléselosztó erőforrás társított Virtuálisgép-példányok esetén a rendszer írni minden kimenő kapcsolat forrása. A forrás a terheléselosztó előtérbeli nyilvános IP-cím a virtuális hálózat privát IP-címtér van írni. A nyilvános IP-címtér 5 rekordos a folyamat (forrás IP-cím, forrásport, IP-átviteli protokoll, cél IP-címe, Célport) egyedinek kell lennie.  Rövid élettartamú port használata ennek elérése után írja át a magánhálózati IP-forráscím, mivel több adatfolyamok egyetlen nyilvános IP-címnek származik.  Ezek elmúló port SNAT portok nevezzük. 

Egy SNAT port felhasznált ne egy egyetlen IP-címre, port és protokoll. Több adatfolyamok ugyanahhoz a cél IP-cím, port és protokoll, az egyes SNAT egyetlen portot használ fel. Ez biztosítja, hogy az adatfolyamok egyedi, amikor a azonos cél IP-cím, port és protokoll azonos nyilvános IP-címét származik. 

Több adatfolyamok, egy másik cél IP-cím, port és protokoll, hogy mindegyik egyetlen SNAT port megosztja. A cél IP-cím, port és protokoll egyedivé adatfolyamok nincs szükség további Forráshelyek segítségével különböztetheti meg egymástól a nyilvános IP-címtér adatfolyamok használni kívánt portokat.

SNAT port erőforrások elfogytak, amikor a kimenő forgalom sikertelen, amíg a meglévő forgalom által kiadott SNAT portok. Terheléselosztó úgy szabadít fel SNAT portok az adatfolyam bezárása, és használja a [4 perces időtúllépést](#idletimeout) az üresjárati folyamok SNAT portok újraigénylési funkcióját.

Tekintse át a [kezelése SNAT](#snatexhaust) szakasz mintára feltételek, amelyek gyakran SNAT port Erőforrásfogyás mérsékelni.

### <a name="preallocatedports"></a>Rövid élettartamú port előzetes lefoglalás port színleg SNAT (PAT)

Használatakor a háttérkészlet mérete alapján az algoritmus meghatározására száma előzetesen lefoglalt SNAT elérhető portok az Azure által használt port színleg SNAT ([PAT](#pat)).  SNAT portjait elmúló port egy adott nyilvános IP-forráscím érhető el.

Azure preallocates SNAT portok minden egyes VM hálózati adapter IP-konfigurációhoz. Amikor IP-konfigurációt ad hozzá a készlethez, a SNAT portok vannak előzetesen lefoglalt az IP-konfiguráció, a háttér-készlet mérete alapján. A foglalási klasszikus webes feldolgozói szerepkörök, szerepkör-példányonként esetén.  Kimenő forgalom létrehozásakor [PAT](#pat) dinamikusan használ fel (de előzetesen lefoglalt), majd felenged ezeket a portokat, a folyamat befejeződésekor vagy [az üresjárati időkorlát](#ideltimeout).

Az alábbi táblázat a SNAT port preallocations háttér-készlet méretű rétege számára:

| Készlet mérete (Virtuálisgép-példány) | Előzetesen lefoglalt SNAT portok száma IP-konfiguráció|
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

Fontos megjegyezni, hogy SNAT elérhető portok száma nem fordíthatók le, közvetlenül a kapcsolatok száma. Egyetlen SNAT port a több célhoz rendelt egyedi felhasználhatók. Ha szükséges, hogy adatfolyamok egyedi portok csak felhasználni. Tekintse meg [kezelése a kimeríthető erőforrás](#snatexhaust) tervezési és a megoldás útmutatást, valamint a szakasz leíró [PAT](#pat).

A háttérkészlet méretének módosítása hatással lehet a meglévő adatfolyamok némelyike. A háttér-készlet mérete növekszik, és be a következő réteggel közeledik, ha a előzetesen lefoglalt SNAT portok felének visszaigényelt vannak a következő nagyobb háttér címkészletet réteg történő áttelepítés során. Folyamatok, amelyek regenerált SNAT portot időtúllépés lesz, és létre kell hozni. Új kapcsolódási kísérletek sikeres azonnal mindaddig, amíg érhetők el előzetesen lefoglalt portok.

Ha csökkenti a háttér-készlet méretét, és az alacsonyabb szint transitions lehetőségnél SNAT elérhető portok száma növekszik. Ebben az esetben meglévő lefoglalt SNAT portok, és a megfelelő adatfolyamok, nem érintettek.

## <a name="snatexhaust"></a>SNAT (PAT) port Erőforrásfogyás kezelése

[Elmúló port](#preallocatedports) használt [PAT](#pat) egy kimeríthető erőforrás van, a [önálló virtuális gép példány szint nyilvános IP-cím nélkül](#defaultsnat) és [virtuális gép elosztott terhelésű nélkül Példány szint nyilvános IP-cím](#lb).

Ha sok kimenő TCP vagy UDP-kapcsolatok az azonos cél IP-cím és port, amelyek kezdeményezése, és figyelje a kimenő kapcsolatok sikertelen vagy az alábbi intézkedéseket javasoljuk a támogatási tudja skálázását SNAT portok áll (előzetesen lefoglalt [elmúló port](#preallocatedports)által használt [PAT](#pat)), több lehetőség közül választhat általános megoldás.  Tekintse át ezeket a beállításokat, és határozza meg az elérhető és ajánlott a forgatókönyvéhez.  Lehetséges egy vagy több segítséget nyújtanak ebben a forgatókönyvben.

Ha problémája van a kimenő kapcsolat viselkedésének megértése, IP-verem statisztika (netstat) is használhatja, vagy hasznos, és figyelje meg a kapcsolat viselkedések használatával csomag rögzíti.  Hajtsa végre a csomag rögzíti a vendég operációs rendszer a példány, vagy használjon [csomag rögzítési hálózati figyelőt](../network-watcher/network-watcher-packet-capture-manage-portal.md).

### <a name="connectionreuse"></a>Alkalmazás kapcsolatok újból módosítása 
Igény szerinti SNAT újból felhasználja a kapcsolatot az alkalmazás által használt rövid élettartamú port csökkentése érdekében.  Ez különösen igaz, protokollok, mint a HTTP 1.1, ahol kapcsolat felhasználhatja az alapértelmezett beállítás.  És egyéb protokollok HTTP-t (azaz REST) használó pedig előnyei.  Újbóli mindig jobb, mint az egyes kérelmek egyedi, atomi TCP-kapcsolatok, és további performant, nagyon hatékony TCP tranzakciók eredményez.

### <a name="connection pooling"></a>Használja a kapcsolatkészletezést módosítása
A rendszer az alkalmazásban, ahol kérelmek belső elosztott kapcsolatokat (egyes újból felhasználja a ahol csak lehetséges) készletét kapcsolatkészlet is alkalmazhat.  A megkötések a elmúló használt portok és környezetet hoz létre a megfelelő.  Ez is növelheti a kapacitásának több egyidejű művelet tételével, amikor egyetlen kapcsolatból blokkolja-e a művelet a válasz.  Az alkalmazás vagy a konfigurációs beállítások, az alkalmazás fejlesztéséhez használ keretében kapcsolatkészletezést már létezik.  Kombinálhatja a kapcsolat újbóli, és a több kérelmek rögzített, előre jelezhető több portok az azonos IP-címre és a port felhasználását is élvező csökkenti a késést és erőforrás TCP tranzakciók nagyon hatékony használata során kihasználtság.  UDP-tranzakciók is élvezheti kezelése a számát, az UDP adatfolyamok pedig kipufogógáz feltételek elkerülése és beállíthatja a SNAT port kihasználtságának.

### <a name="retry logic"></a>Módosítsa az alkalmazást, hogy az kisebb agresszív újrapróbálkozási logika
Ha [előzetesen lefoglalt elmúló port](#preallocatedports) használt [PAT](#pat) vannak kimerült vagy alkalmazás hiba történik, túl szigorú vagy találgatásos kényszerített újrapróbálja decay nevű és leállítási logika miatt Erőforrásfogyás fordulhat elő, vagy megőrzése nélkül. Igény szerinti rövid élettartamú port egy kisebb agresszív újrapróbálkozási logika használatával csökkentése érdekében.   Elmúló port (nem állítható) 4 perces üresjárati időtúllépés rendelkezik, és ha az újrapróbálkozások túl agresszív, az Erőforrásfogyás rendelkezik-e nincs lehetőség arra, hogy a saját törlődnek.  Annak eldöntéséhez, hogy milyen gyakran és hogyan lehet az alkalmazás újrapróbálja tranzakciók ezért fontos szempont a kialakítás.

### <a name="assignilpip"></a>Egy példányszintű nyilvános IP-cím hozzárendelése az egyes virtuális gépek
A forgatókönyvhöz a értékre változik [példányszintű nyilvános IP-cím egy virtuális géphez](#ilpip).  A nyilvános IP-címet használja az egyes virtuális gépek összes porttartomány érhetők el a virtuális gép (nem forgatókönyvek, ahol a nyilvános IP-elmúló port megosztott módon működik a VM megfelelő háttérkészlet társítva).  Nincsenek kell figyelembe venni, például a nyilvános IP-címek további költség- és nagyszámú egyedi IP-címek engedélyezése gyakorolt lehetséges hatásának kompromisszumot.

>[!NOTE] 
>Ez a beállítás nem érhető el webes feldolgozói szerepköröket.

### <a name="idletimeout"></a>Keep Alive csomagok küldését segítségével kimenő üresjárati időtúllépés

Kimenő kapcsolatok 4 perces üresjárati időtúllépés rendelkezik. Ez nem módosítható. Azonban transport (vagyis TCP keep-alive) is használhatja, vagy alkalmazás réteg Keep Alive csomagok küldését az üresjárati folyamat frissítése, és alaphelyzetbe állítja az üresjárati időtúllépés, ha szükséges.

## <a name="discoveroutbound"></a>A nyilvános IP-címet használja egy adott virtuális gép felderítése
Számos módon határozza meg a nyilvános IP-forráscím egy kimenő kapcsolat. OpenDNS láthatja a nyilvános IP-címet a virtuális gép egy szolgáltatást biztosít. Az nslookup parancs használata, elküldheti a név myip.opendns.com DNS-lekérdezést a OpenDNS feloldó. A szolgáltatás az IP-forráscím kérés küldése használt adja vissza. A virtuális gépről a következő lekérdezés végrehajtásakor, a rendszer a választ a nyilvános IP-címet használja ezt a virtuális gépet.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Megakadályozza a kimenő kapcsolat
Néha a virtuális gépek számára hozzon létre egy kimenő folyam engedélyezett nemkívánatos is, vagy előfordulhat, hogy mely célok elérhető a kimenő forgalom kezeléséhez követelmény, vagy mely célok megkezdheti a bejövő forgalom. Ebben az esetben [hálózati biztonsági csoportok (NSG)](../virtual-network/virtual-networks-nsg.md) kezelheti a célhoz, amely a virtuális gép is el lehet érni mely nyilvános célként is kezdeményezhető a bejövő forgalom. Amikor egy NSG-t alkalmaz egy elosztott terhelésű virtuális gép, szeretné-e nagy figyelmet fordítani az a [alapértelmezett címkék](../virtual-network/virtual-networks-nsg.md#default-tags) és [alapértelmezett szabályok](../virtual-network/virtual-networks-nsg.md#default-rules).

Győződjön meg arról, hogy a virtuális gép állapotának mintavételi kérések fogadhat Azure Load Balancer. Ha egy NSG blokkolja az egészségügyi mintavételi kérelmeit a AZURE_LOADBALANCER az alapértelmezett címke, a virtuális gép állapotmintáihoz sikertelen lesz, és a virtuális gép van megjelölve. Terheléselosztó leállítja az új forgalom küldése ezt a virtuális Gépet.

## <a name="next-steps"></a>További lépések

- További információ [Load Balancer alapvető](load-balancer-overview.md).
- További információ [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).
- Ismerje meg, azzal kapcsolatban, a másik kulccsal [hálózati lehetőségeket](../networking/networking-overview.md) az Azure-ban.
