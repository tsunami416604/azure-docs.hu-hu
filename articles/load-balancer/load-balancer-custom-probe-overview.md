---
title: Egészségügyi szondák méretezése és a HA a szolgáltatás
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan használhatja az állapotminta az Azure Load Balancer mögötti példányok figyelésére
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ec1507e09a183f8d466a456b70151861f5f0e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159438"
---
# <a name="load-balancer-health-probes"></a>A Load Balancer állapotmintái

Ha az Azure Load Balancer terheléselosztási szabályok használata, meg kell adnia állapotminta, hogy a terheléselosztó a háttérvégpont állapotának észleléséhez.  Az állapotminta és a mintavételi válaszok konfigurációja határozza meg, hogy mely háttérkészlet-példányok kapnak új folyamatokat. Az állapotmintak segítségével észlelheti egy alkalmazás egy háttérvégponton hiba észlelése. Egyéni választ is létrehozhat egy állapotminta, és használja az állapotminta folyamatvezérlés kezelésére terhelés vagy a tervezett állásidő. Ha egy állapotminta meghibásodik, a terheléselosztó leállítja az új folyamatok küldését a megfelelő nem megfelelő állapotú példányra. A kimenő kapcsolat nem érintett, csak a bejövő kapcsolat érinti.

Az állapotvizsgálók több protokollt is támogatnak. Egy adott állapotminta-protokoll rendelkezésre állása terheléselosztó termékváltozatonként változik.  Emellett a szolgáltatás viselkedése a terheléselosztó termékváltozattól függően változik, ahogy az ebben a táblázatban látható:

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| [Mintavételi típusok](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [A mintavételi viselkedés](#probedown) | Minden mintavétel le, minden TCP-folyamat folytatódik. | Minden mintavétel leállt, minden TCP-folyamat lejár. | 


>[!IMPORTANT]
>Tekintse át ezt a dokumentumot teljes egészében, beleértve az alábbi fontos [tervezési útmutatót](#design) a megbízható szolgáltatás létrehozásához.

>[!IMPORTANT]
>A terheléselosztó állapotmintái a 168.63.129.16 IP-címről származnak, és nem blokkolhatók a mintavételek számára a példány megjelöléséhez.  Tekintse át [a mintavételforrás IP-címét](#probesource) a részletekért.

>[!IMPORTANT]
>A http(S) terheléselosztó állapotmintái a konfigurált időtúllépési küszöbértéktől függetlenül automatikusan megvizsgálnak egy példányt, ha a kiszolgáló olyan állapotkódot ad vissza, amely nem HTTP 200 OK, vagy ha a kapcsolat a TCP alaphelyzetbe állításával megszakad.

## <a name="probe-configuration"></a><a name="probes"></a>Mintavétel konfigurációja

Az állapotminta konfigurációja a következő elemekből áll:

- Az egyes szondák közötti intervallum időtartama
- A mintavételi válaszok száma, amelyeket meg kell figyelni, mielőtt a szonda másik állapotba kerül
- A szonda protokollja
- A szonda portja
- HTTP GET-hez használandó HTTP-elérési út HTTP(S) mintavételek használataesetén

>[!NOTE]
>A mintavételi definíció nem kötelező, vagy ellenőrzött Az Azure PowerShell, az Azure CLI, sablonok vagy API használatakor. A mintavételezési érvényesítési tesztek csak az Azure Portal használatakor végezhető el.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Az alkalmazás jelének, a jel észlelésének és a platform reakciójának megértése

A mintavételi válaszok száma mindkét

- azon sikeres mintavételek száma, amelyek lehetővé teszik egy példány feljelölését, és
- azon időbeli kimenő mintavételek száma, amelyek miatt egy példány lefelé van jelölve.

A megadott idő- és intervallumértékek határozzák meg, hogy egy példány fel- vagy leveged.The timeout and interval values specified determine whether a instance will be marked as up or down.  Az intervallum és a mintavételi válaszok számának szorzata határozza meg azt az időtartamot, amely alatt a mintavételi válaszokat észlelni kell.  És a szolgáltatás reagálni fog, miután a szükséges szondák at értek el.

A viselkedést egy példával tovább illusztrálhatjuk. Ha a mintavételi válaszok számát 2-re, az intervallumot pedig 5 másodpercre állította, ez azt jelenti, hogy 10 másodperces időintervallumon belül 2 mintavételi időtúltöltési hibát kell figyelni.  Mivel a mintavétel elküldésének időpontja nem lesz szinkronizálva, amikor az alkalmazás állapota megváltozhat, két forgatókönyv vel köthetjük az időt:

1. Ha az alkalmazás közvetlenül az első mintavétel érkezése előtt kezdi meg az idő-megtérési mintavételi válasz előállítását, az események észlelése 10 másodpercet (2 x 5 másodperces időközöket) vesz igénybe, valamint az alkalmazás időtartamát, amely az első időelhagyást jelzi. szonda megérkezik.  Feltételezheti, hogy ez az észlelés valamivel több mint 10 másodpercet vesz igénybe.
2. Ha az alkalmazás elindul egy időtúllépések mintavételi válasz közvetlenül az első mintavétel érkezése után, ezek az események észlelése nem kezdődik el, amíg a következő mintavétel megérkezik (és időtúllépések) plusz további 10 másodperc (2 x 5 másodperces időközönként).  Feltételezheti, hogy ez az észlelés alig 15 másodpercet vesz igénybe.

Ebben a példában, ha észlelés történt, a platform majd egy kis időt vesz igénybe, hogy reagáljon erre a változásra.  Ez azt jelenti, hogy a 

1. amikor az alkalmazás elkezdi megváltoztatni az állapotot, és
2. ha ezt a változást észlelik, és megfelelnek a szükséges kritériumoknak (a megadott időközönként küldött szondák száma), és
3. amikor az észlelést a platformon keresztül közölték 

feltételezheti, hogy az időtúllépési szonda válaszára adott reakció legalább több mint 10 másodpercet és legfeljebb 15 másodpercet vesz igénybe, hogy reagáljon az alkalmazás ból érkező jel változására.  Ez a példa szemlélteti, hogy mi történik, azonban nem lehet előre jelezni a pontos időtartamot a fenti durva útmutatást ebben a példában bemutatott.
 
## <a name="probe-types"></a><a name="types"></a>Mintavételi típusok

Az állapotminta által használt protokoll a következők valamelyikére konfigurálható:

- [TCP-hallgatók](#tcpprobe)
- [HTTP-végpontok](#httpprobe)
- [HTTPS-végpontok](#httpsprobe)

A rendelkezésre álló protokollok a használt terheléselosztó termékváltozattól függenek:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard termékváltozat |    &#9989; |   &#9989; |   &#9989; |
| Alapszintű termékváltozat |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>TCP-szonda

A TCP-mintavételek háromutas nyitott TCP-kézfogással kezdeményeznek kapcsolatot a megadott porttal.  A TCP-mintavételek megszakítják a kapcsolatot egy négyutas, szoros TCP-kézfogással.

A mintavételi időköz 5 másodperc, a nem megfelelő állapotú válaszok minimális száma pedig 2.  Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A TCP-mintavétel sikertelen, ha:
* A TCP-figyelő a példány egyáltalán nem válaszol az időbeli elállási időszakban.  A mintavétel az időkorláttal kapcsolatos mintavételi kérelmek száma alapján van megjelölve, amelyek úgy vannak konfigurálva, hogy megválaszolatlanul maradjanak, mielőtt megjelölnék a mintavételt.
* A mintavétel tcp-alaphelyzetbe állítást kap a példánytól.

Az alábbiakban bemutatjuk, hogyan fejezheti ki ezt a fajta mintavételi konfigurációt egy Erőforrás-kezelő sablonban:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a> HTTP / HTTPS szonda

>[!NOTE]
>A HTTPS-mintavétel csak a [Standard Load Balancer](load-balancer-standard-overview.md)esetében érhető el.

A HTTP- és HTTPS-mintavételek a TCP-mintavételre épülnek, és a megadott elérési úttal http GET-t adnak ki. Mindkét mintavétel támogatja a HTTP GET relatív elérési útjait. A HTTPS-mintavételek megegyeznek a HTTP-mintavételekkel, amelyek egy Transport Layer Security (TLS, korábbi nevén SSL) burkoló hozzáadásával azonosak. Az állapotminta van megjelölve, amikor a példány válaszol egy HTTP-állapot 200 az időbeli adottidőn belül.  Az állapotminta alapértelmezés szerint 15 másodpercenként megkísérli ellenőrizni a konfigurált állapotmintaportot. A minimális mintavételi időköz 5 másodperc. Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A HTTP / HTTPS-mintavételek is hasznoslehet a saját logika megvalósításához példányok eltávolítása a terheléselosztó elforgatásából, ha a mintavételi port is a figyelő a szolgáltatás maga. Például dönthet úgy, hogy eltávolít egy példányt, ha az 90% feletti processzor, és nem 200 HTTP-állapotot ad vissza. 

> [!NOTE] 
> A HTTPS-mintavétel olyan tanúsítványok használatát igényli, amelyek minimális sHA256-os aláírási kivonatával rendelkeznek a teljes láncban.

Ha a Cloud Services szolgáltatást használja, és a w3wp.exe programot használó webes szerepkörökkel rendelkezik, akkor a webhely automatikus figyelését is elérheti. A webhelykód hibái nem 200-as állapotot adnak vissza a terheléselosztó mintavételnek.

A HTTP / HTTPS mintavétel sikertelen, ha:
* A mintavételi végpont 200-tól eltérő HTTP-válaszkódot ad vissza (például 403, 404 vagy 500). Ez azonnal megjelöli az állapotérzékelőt. 
* A mintavételi végpont egyáltalán nem reagál a mintavételi időköz és a 30 másodperces időtúladási időszak alatt. Több mintavételi kérelmek lehet, hogy megválaszolatlan marad, mielőtt a mintavétel lesz megjelölve nem fut, és amíg az összeg az összes időtúllépési időközök elérte.
* A mintavételi végpont tcp-alaphelyzetbe állítással zárja le a kapcsolatot.

Az alábbiakban bemutatjuk, hogyan fejezheti ki ezt a fajta mintavételi konfigurációt egy Erőforrás-kezelő sablonban:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Vendégügynök-mintavétel (csak klasszikus)

A felhőalapú szolgáltatásszerepkörök (feldolgozói szerepkörök és webes szerepkörök) alapértelmezés szerint vendégügynököt használnak a mintavételfigyeléshez.  A vendégügynök-mintavétel egy végső megoldásként ikonfiguráció.  Mindig használjon állapotminta explicit tcp vagy HTTP-mintavétellel. A vendégügynök-mintavétel nem olyan hatékony, mint a legtöbb alkalmazásforgatókönyv explicit módon definiált mintavételek.

A vendégügynök-mintavétel a virtuális gépen belüli vendégügynök ellenőrzése. Ezután csak akkor figyel és válaszol egy HTTP 200 OK válaszával, ha a példány Ready állapotban van. (Más állapotok foglaltak, újrahasznosítás vagy leállítás.)

További információ: [A szolgáltatásdefiníciós fájl konfigurálása (csdef) állapotmintavételezéshez](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [Az első lépések a felhőszolgáltatások nyilvános terheléselosztójának létrehozásával](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services)című témakörben található.

Ha a vendégügynök nem válaszol a HTTP 200 OK protokollal, a terheléselosztó a példányt nem válaszolóként jelöli meg. Ezután leállítja a folyamatok küldését az adott példányra. A terheléselosztó továbbra is ellenőrzi a példányt. 

Ha a vendégügynök http 200-as kódhasználatával válaszol, a terheléselosztó ismét új folyamatokat küld az adott példánynak.

Webes szerepkör használata esetén a webhelykód általában a w3wp.exe webhelyen fut, amelyet az Azure-háló vagy a vendégügynök nem figyel. A w3wp.exe hibái (például HTTP 500 válaszok) nem jelennek meg a vendégügynöknek. Következésképpen a terheléselosztó nem veszi ki ezt a példányt a rotációból.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>A viselkedés vizsgálata

A TCP-, HTTP- és HTTPS-állapotmintás a rendszer kifogástalannak tekinti, és a háttérvégpontot kifogástalannak jelöli meg, ha:

* Az állapotvizsgálat sikeres egyszer a virtuális gép indítása után.
* A háttérvégpont kifogástalanként való jelöléséhez szükséges mintavételek megadott száma teljesült.

Minden olyan háttérvégpont, amely elérte a kifogástalan állapotú, jogosult új folyamatok fogadására.  

> [!NOTE]
> Ha az állapotminta ingadozik, a terheléselosztó tovább vár, mielőtt a háttérvégpontot kifogástalan állapotba helyezi. Ez az extra várakozási idő védi a felhasználót és az infrastruktúrát, és egy szándékos házirend.

## <a name="probe-down-behavior"></a><a name="probedown"></a>A mintavételi viselkedés

### <a name="tcp-connections"></a>TCP-kapcsolatok

Az új TCP-kapcsolatok sikeresen megmaradnak a háttérvégpont okán.

Ha egy háttérvégpont állapotminta sikertelen, a háttérvégponthoz létrehozott TCP-kapcsolatok továbbra is folytatódnak.

Ha a háttérkészlet összes példányának minden mintavétele sikertelen lesz, a rendszer nem küld új folyamatokat a háttérkészletbe. A standard terheléselosztó lehetővé teszi a létrehozott TCP-folyamatok folytatását.  Az alapszintű terheléselosztó megszünteti a háttérkészletbe irányuló összes meglévő TCP-folyamatot.
 
A terheléselosztó egy áthaladási szolgáltatás (nem szünteti meg a TCP-kapcsolatokat), és a folyamat mindig az ügyfél és a virtuális gép vendég operációs rendszer és alkalmazás között. A készlet az összes mintavételek le hatására az előtér nem válaszol a TCP-kapcsolat megnyitási kísérletek (SYN), mivel nincs kifogástalan háttérvégpont a folyamat fogadására, és válaszoljon egy SYN-ACK.

### <a name="udp-datagrams"></a>UDP-datagramok

AZ UDP-datagramok kifogástalan háttérvégpontokba kerülnek.

Az UDP kapcsolat nélküli, és nincs folyamatállapot nyomon követve az UDP-hez. Ha bármely háttérvégpont állapotminta sikertelen, a meglévő UDP-folyamatok egy másik kifogástalan állapotú példánya a háttérkészletben.

Ha a háttérkészlet összes példányának összes mintavétele sikertelen lesz, a meglévő UDP-folyamatok megszakadnak az alapszintű és a standard terheléselosztók számára.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Mintavételi forrás IP-címe

A terheléselosztó egy elosztott probing szolgáltatást használ a belső állapotmodelljéhez. A szondázási szolgáltatás minden állomáson található, ahol a virtuális gépek, és igény szerint programozható az ügyfél konfigurációja szerint állapotminta létrehozásához. Az állapotminta-forgalom közvetlenül a szondázási szolgáltatás, amely létrehozza az állapotminta és az ügyfél virtuális gép között. Minden terheléselosztó állapotminta a 168.63.129.16 IP-címről származik forrásként.  A virtuális hálózaton belül olyan IP-címteret is használhat, amely nem RFC1918 terület.  Globálisan fenntartott ip-cím használatával a Microsoft tulajdonában lévő IP-cím csökkenti annak esélyét, hogy az IP-cím ütközik a virtuális hálózaton belül használt IP-címterülettel.  Ez az IP-cím minden régióban azonos, és nem változik, és nem jelent biztonsági kockázatot, mert csak a belső Azure platform-összetevő tud csomagot beszerezni erről az IP-címről. 

Az AzureLoadBalancer szolgáltatáscímke azonosítja ezt a forrás IP-címet a [hálózati biztonsági csoportokban,](../virtual-network/security-overview.md) és alapértelmezés szerint engedélyezi az állapotmintavételi forgalmat.

A terheléselosztó állapotmintáin kívül a következő műveletek ezt az [IP-címet is használják:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Lehetővé teszi, hogy a virtuálisgép-ügynök kommunikáljon a platformmal, jelezve, hogy "Kész" állapotban van
- Lehetővé teszi a virtuális DNS-kiszolgálóval való kommunikációt, hogy szűrt névfeloldást biztosítson azoknak az ügyfeleknek, akik nem határoznak meg egyéni DNS-kiszolgálókat.  Ez a szűrés biztosítja, hogy az ügyfelek csak a központi telepítés állomásneveit tudják feloldani.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be az Azure-beli DHCP-szolgáltatásból.

## <a name="design-guidance"></a><a name="design"></a>Tervezési útmutató

Az állapotpróbák rugalmassá teszik a szolgáltatást, és lehetővé teszik a méretezést. A helytelen konfiguráció vagy a hibás tervezési minta hatással lehet a szolgáltatás rendelkezésre állása és méretezhetősége. Tekintse át ezt a teljes dokumentumot, és fontolja meg, milyen hatással van a forgatókönyvre, ha ez a mintavételi válasz van megjelölve, vagy megjelölve, és hogyan befolyásolja az alkalmazás forgatókönyv rendelkezésre állását.

Az alkalmazás állapotmodelljének tervezésekor meg kell vizsgálnia egy olyan háttérvégpont-portot, amely az adott példány __és__ a nyújtott alkalmazásszolgáltatás állapotát tükrözi.  Az alkalmazásportnak és a mintavételi portnak nem kell azonosnak lennie.  Bizonyos esetekben kívánatos lehet, hogy a mintavételi port eltérő legyen a porttól, amelyen az alkalmazás szolgáltatást nyújt.  

Néha hasznos lehet az alkalmazás hoz létre egy állapotminta választ nem csak az alkalmazás állapotának észlelése, hanem közvetlenül a terheléselosztó, hogy a példány kell kapnia, vagy nem kap új folyamatokat.  Módosíthatja a mintavételi választ, hogy az alkalmazás új folyamatok ellennyomását és szabályozását hozza létre egy példányra az állapotminta sikertelen, vagy előkészíti az alkalmazás karbantartását, és kezdeményezze a forgatókönyv kiürítését.  A Standard Load Balancer használatakor a [mintavételi jel](#probedown) mindig lehetővé teszi, hogy a TCP-folyamatok az alapjárati időtúllépésig vagy a kapcsolat lezárásáig folytatódjanak. 

UdP terheléselosztáshoz létre kell hoznia egy egyéni állapotminta-jelet a háttérvégpontról, és használja a TCP, HTTP vagy HTTPS állapotminta a megfelelő figyelőt, hogy tükrözze az UDP-alkalmazás állapotát.

Ha [ha portok terheléselosztási szabályok](load-balancer-ha-ports-overview.md) [standard terheléselosztó](load-balancer-standard-overview.md)használatakor minden port terheléselosztásos, és egyetlen állapotminta válasz tükröznie kell az állapotát a teljes példány.

Ne fordítson le vagy proxy egy állapotminta a példányon keresztül, amely megkapja az állapotminta egy másik példánya a virtuális hálózat, mivel ez a konfiguráció vezethet lépcsőzetes hibák a forgatókönyvben.  Vegye figyelembe a következő forgatókönyvet: egy harmadik féltől származó készülékek készlete van telepítve a terheléselosztó erőforrás háttérkészletében, hogy skálázást és redundanciát biztosítson az eszközök számára, és az állapotminta úgy van konfigurálva, hogy megvizsgálja a külső gyártótól származó készülék proxyk vagy a készülék mögötti más virtuális gépekre.  Ha ugyanazt a portot vizsgálja, amelyet a készülék mögötti többi virtuális gépre történő fordításhoz vagy proxykérelmek lefordításához használ, a készülék mögött imaslógép minden mintavételi válasza halottnak fogja jelölni. Ez a konfiguráció a teljes alkalmazásforgatókönyv kaszkádolt hibájához vezethet a készülék mögötti egyetlen háttérvégpont miatt.  Az eseményindító lehet egy szakaszos mintavételi hiba, amely hatására a terheléselosztó megjelöli az eredeti célt (a készülékpéldányt), és ezzel letilthatja a teljes alkalmazásforgatókönyvet. Ehelyett vizsgálja meg magának a készüléknek az állapotát. A mintavétel kiválasztása az állapotjelző meghatározásához fontos szempont a hálózati virtuális készülékek (NVA) forgatókönyvek, és konzultálnia kell az alkalmazás szállítójával, hogy mi a megfelelő állapotjelző az ilyen forgatókönyvek.

Ha nem engedélyezi a mintavétel [forrás IP-címét](#probesource) a tűzfalházirendekben, az állapotminta sikertelen lesz, mivel nem tudja elérni a példányt.  Viszont a terheléselosztó megjelöli a példányt az állapotminta meghibásodása miatt.  Ez a helytelen konfiguráció a terheléselosztási alkalmazásforgatókönyv sikertelensülését okozhatja.

A terheléselosztó állapotminta a példány megjelöléséhez engedélyeznie **kell** ezt az IP-címet bármely Azure [hálózati biztonsági csoportok](../virtual-network/security-overview.md) és a helyi tűzfal-házirendek.  Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza a [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer lehetővé teszi az állapotmintavételi forgalom engedélyezéséhez.

Ha egy állapotminta-hibát szeretne tesztelni, vagy egy adott példányt jelöl meg, [hálózati biztonsági csoportok](../virtual-network/security-overview.md) segítségével explicit módon letilthatja az állapotmintát (célport vagy forrás [IP)](#probesource)és szimulálhatja a mintavétel meghibásodását.

Ne konfigurálja a virtuális hálózatot a Microsoft tulajdonában lévő, 168.63.129.16-ot tartalmazó IP-címtartománysal.  Az ilyen konfigurációk ütköznek az állapotminta IP-címével, és a forgatókönyv sikertelensedéséhez vezethetnek.

Ha több felületet a virtuális gép, biztosítania kell, hogy válaszoljon a mintavétel az on a felületen, amelyen megkapta azt.  Előfordulhat, hogy a hálózati cím forrásfordítása ezt a címet a virtuális gép egy kapcsolat onként.

[TCP-időbélyegek nem engedélyezése](https://tools.ietf.org/html/rfc1323).  A TCP-időbélyegek engedélyezése az állapotminták sikertelenségéhez vezethet, mivel a virtuális gép vendég operációsrendszer-tcp veremje eldobta a TCP-csomagokat, ami azt eredményezi, hogy a terheléselosztó a megfelelő végpontot jelöli.  A TCP-időbélyegek alapértelmezés szerint engedélyezve vannak a biztonsági edzett virtuálisgép-lemezképeken, és le kell tiltani őket.

## <a name="monitoring"></a>Figyelés

A nyilvános és a belső [standard terheléselosztó](load-balancer-standard-overview.md) végpontonként és a háttérvégpont állapotminta állapotát az Azure Monitoron keresztül többdimenziós metrikákként teszi elérhetővé. Ezeket a mutatókat más Azure-szolgáltatások vagy partneralkalmazások is felhasználhatják. 

Az alapszintű nyilvános terheléselosztó az Azure Monitor-naplókon keresztül háttérkészletenként összesített állapotmintaállapotát teszi elérhetővé.  Az Azure Monitor naplók nem érhetők el a belső alapszintű terheléselosztók.  [Az Azure Monitor naplók](load-balancer-monitor-log.md) segítségével ellenőrizheti a nyilvános terheléselosztó mintavétel állapota és a mintavétel száma. A naplózás a Power BI-val vagy az Azure Operational Insights szolgáltatással használható a terheléselosztó állapotával kapcsolatos statisztikák biztosításához.

## <a name="limitations"></a>Korlátozások

- A HTTPS-mintavételek nem támogatják az ügyféltanúsítvánnyal való kölcsönös hitelesítést.
- Azt kell feltételezni, állapot mintavételek sikertelen lesz, ha TCP időbélyegek engedélyezve vannak.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- [Nyilvános terheléselosztó létrehozása az Erőforrás-kezelőben a PowerShell használatával](quickstart-create-standard-load-balancer-powershell.md)
- [REST API állapotminta](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Új állapotminta-képességek kérése [a terheléselosztó Uservoice-jával](https://aka.ms/lbuservoice)
