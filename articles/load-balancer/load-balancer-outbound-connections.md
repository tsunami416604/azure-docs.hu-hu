---
title: Kimenő kapcsolatok az Azure-ban
titleSuffix: Azure Load Balancer
description: Ez a cikk azt ismerteti, hogyan teszi lehetővé az Azure a virtuális gépek számára a nyilvános internetes szolgáltatásokkal való kommunikációt.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: acf49c4247c8084a3afd3c2046003ee1b20d2f67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393107"
---
# <a name="outbound-connections-in-azure"></a>Kimenő kapcsolatok az Azure-ban

Az Azure számos különböző mechanizmuson keresztül biztosít kimenő kapcsolatot az ügyfelek központi telepítéséhez. Ez a cikk a forgatókönyvek, a működésük és a kezelésük módját ismerteti.

>[!NOTE] 
>Ez a cikk csak a Resource Manager-alapú üzemelő példányokat ismerteti. Tekintse át az Azure-beli klasszikus üzembe helyezési forgatókönyvek [kimenő kapcsolatait (klasszikus)](load-balancer-outbound-connections-classic.md) .

Az Azure-beli üzemelő példányok az Azure-on kívüli végpontokkal kommunikálhatnak a nyilvános IP-címtartomány használatával. Ha egy példány kimenő folyamatot kezdeményez a nyilvános IP-címtartomány egy céljára, az Azure dinamikusan leképezi a magánhálózati IP-címet egy nyilvános IP-címhez. Ennek a leképezésnek a létrehozása után a kimenő forgalomtól érkező forgalom visszaadott értéke elérheti azt a magánhálózati IP-címet is, ahol a folyamat származik.

Az Azure a forrás hálózati címfordítás (SNAT) használatával hajtja végre ezt a funkciót. Ha egy nyilvános IP-cím mögé több magánhálózati IP-cím is fel van használva, akkor az Azure a címfordítás [(Pat)](#pat) használatával álcázza a magánhálózati IP-címeket. A rendszer ideiglenes portokat használ a PAT számára, és a készlet mérete alapján van [előfoglalva](#preallocatedports) .

Több [kimenő forgatókönyv](#scenarios)is létezik. Ezeket a forgatókönyveket igény szerint kombinálhatja. Alaposan tekintse át ezeket a képességeket, megkötéseket és mintákat, ahogyan azok az üzemi modellre és az alkalmazásokra vonatkoznak. Tekintse át az [ilyen forgatókönyvek kezelésével](#snatexhaust)kapcsolatos útmutatást.

>[!IMPORTANT] 
>A standard Load Balancer és a standard nyilvános IP-címek új képességeket és különböző viselkedéseket vezetnek be a kimenő kapcsolatokhoz.  Ezek nem azonosak az alapszintű SKU-kal.  Ha standard SKU-kal dolgozik a kimenő kapcsolaton, explicit módon meg kell határoznia a standard nyilvános IP-címeket vagy a standard nyilvános Load Balancer.  Ez magában foglalja a kimenő kapcsolatok létrehozását belső standard Load Balancer használata esetén.  Javasoljuk, hogy mindig használjon kimenő szabályokat egy standard nyilvános Load Balanceron.  A [3. forgatókönyv](#defaultsnat) nem érhető el a standard SKU-val.  Ez azt jelenti, hogy ha belső standard Load Balancer használ, meg kell tennie a kimenő kapcsolatok létrehozásához szükséges lépéseket a háttér-készletben lévő virtuális gépekhez, ha kimenő kapcsolatra van szükség.  A kimenő kapcsolat kontextusában egyetlen önálló virtuális gép, a rendelkezésre állási csoportban lévő összes virtuális gép a VMSS összes példánya csoportként viselkedik. Ez azt jelenti, hogy ha egy rendelkezésre állási csoport egyetlen virtuális gépe egy szabványos SKU-hoz van társítva, akkor a rendelkezésre állási csoportba tartozó összes virtuálisgép-példány ugyanúgy viselkedik, mintha a standard SKU-hoz társítva van, még akkor is, ha egy adott példány nincs közvetlenül társítva. Ez a viselkedés abban az esetben is megfigyelhető, ha egy önálló virtuális gép több hálózati adapterrel rendelkezik, amelyek egy terheléselosztó számára vannak csatlakoztatva. Ha egy hálózati adaptert önállóként adnak hozzá, akkor ugyanaz lesz a viselkedése. Körültekintően tekintse át a teljes dokumentumot, hogy megismerje az általános fogalmakat, tekintse át [standard Load Balancer](load-balancer-standard-overview.md) az SKU-ket és a [kimenő szabályokat](load-balancer-outbound-rules-overview.md).  A kimenő szabályok használata lehetővé teszi a kimenő kapcsolatok összes aspektusának részletes szabályozását.

## <a name="scenario-overview"></a><a name="scenarios"></a>Forgatókönyv áttekintése

A Azure Load Balancer és a kapcsolódó erőforrások explicit módon vannak meghatározva a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)használatakor.  Az Azure jelenleg három különböző módszert biztosít a kimenő kapcsolatok eléréséhez Azure Resource Manager erőforrásokhoz. 

| Termékváltozatok | Forgatókönyv | Módszer | IP-protokollok | Leírás |
| --- | --- | --- | --- | --- |
| Standard, alapszintű | [1. a példány szintű nyilvános IP-címmel rendelkező virtuális gép (Load Balancer) vagy anélkül](#ilpip) | SNAT, nem használt port | TCP, UDP, ICMP, ESP | Az Azure a példány hálózati adapterének IP-konfigurációjához hozzárendelt nyilvános IP-címet használja. A példányhoz minden elérhető ideiglenes port tartozik. Standard Load Balancer használata esetén a [Kimenő szabályok](load-balancer-outbound-rules-overview.md) nem támogatottak, ha egy nyilvános IP-cím van hozzárendelve a virtuális géphez. |
| Standard, alapszintű | [2. a virtuális géphez társított nyilvános Load Balancer (nincs nyilvános IP-cím a példányon)](#lb) | SNAT a Load Balancer előtérrel (PAT) rendelkező portokkal | TCP, UDP |Az Azure megosztja a nyilvános Load Balancer a több magánhálózati IP-címmel rendelkező előtér nyilvános IP-címét. Az Azure a frontendek ideiglenes portjait használja a PAT számára. A kimenő kapcsolatok explicit meghatározásához használjon [kimenő szabályokat](load-balancer-outbound-rules-overview.md) . |
| none vagy alapszintű | [3. önálló virtuális gép (nincs Load Balancer, nincs nyilvános IP-cím)](#defaultsnat) | SNAT a port maszkolásával (PAT) | TCP, UDP | Az Azure automatikusan kijelöl egy nyilvános IP-címet a SNAT számára, megosztja ezt a nyilvános IP-címet a rendelkezésre állási csoport több magánhálózati IP-címével, és a nyilvános IP-cím ideiglenes portjait használja. Ez a forgatókönyv az előző forgatókönyvek tartaléka. Ha láthatóságra és vezérlésre van szüksége, nem ajánlott. |

Ha nem szeretné, hogy a virtuális gép kommunikáljon az Azure-on kívüli végpontokkal a nyilvános IP-címtartomány területén, a hálózati biztonsági csoportok (NSG) használatával letilthatja a hozzáférést igény szerint. A [Kimenő kapcsolatok megakadályozása](#preventoutbound) című szakasz részletesebben tárgyalja a NSG. A virtuális hálózatok kimenő hozzáférés nélküli kialakításával, megvalósításával és kezelésével kapcsolatos útmutató a jelen cikk hatókörén kívül esik.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>1. forgatókönyv: nyilvános IP-címmel rendelkező virtuális gép

Ebben az esetben a virtuális gépnek hozzá van rendelve egy nyilvános IP-címe. A kimenő kapcsolatok tekintetében nem számít, hogy a virtuális gép terheléselosztás alatt áll-e. Ez a forgatókönyv elsőbbséget élvez a többiekkel szemben. Nyilvános IP-cím használata esetén a virtuális gép az összes kimenő folyamathoz a nyilvános IP-címet használja.  

Egy virtuális géphez hozzárendelt nyilvános IP-cím 1:1-kapcsolat (nem 1: sok), és állapot nélküli 1:1 NAT-ként lett megvalósítva.  A rendszer nem használja az álcázott portot (PAT), és a virtuális gép számára elérhető minden ideiglenes port.

Ha az alkalmazás sok kimenő folyamatot kezdeményez, és SNAT-portok kimerülését tapasztalja, érdemes lehet egy [nyilvános IP-címet hozzárendelni a SNAT-megkötések enyhítéséhez](#assignilpip). Tekintse át a [SNAT-kimerültség kezelését](#snatexhaust) teljes egészében.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>2. forgatókönyv: elosztott terhelésű virtuális gép nyilvános IP-cím nélkül

Ebben az esetben a virtuális gép egy nyilvános Load Balancer backend-készlet része. A virtuális gépnek nincs hozzárendelve nyilvános IP-címe. A Load Balancer erőforrást egy terheléselosztó-szabállyal kell konfigurálni, hogy kapcsolatot hozzon létre a nyilvános IP-frontend és a háttér-készlet között.

Ha nem fejezi be ezt a szabályt, a viselkedést a [nyilvános IP-cím nélküli önálló virtuális gép](#defaultsnat)forgatókönyve írja le. Nincs szükség ahhoz, hogy a szabály a háttérrendszer munkafolyamati készletében működő figyelővel rendelkezzen a sikeres állapothoz.

Ha a terheléselosztásos virtuális gép kimenő folyamatot hoz létre, az Azure lefordítja a kimenő folyamat privát forrás IP-címét a nyilvános Load Balancer előtér nyilvános IP-címére. Az Azure a SNAT használatával hajtja végre ezt a funkciót. Az Azure a [Pat](#pat) használatával több magánhálózati IP-címet is maszkolást használ egy nyilvános IP-cím mögé. 

A terheléselosztó nyilvános IP-címének ideiglenes portjai a virtuális gép által kezdeményezett egyes folyamatok megkülönböztetésére szolgálnak. A SNAT dinamikusan használja az [előlefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. Ebben a kontextusban a SNAT használt ideiglenes portok neve SNAT-portok.

A SNAT-portok előre le vannak foglalva a [SNAT és a Pat](#snat) című szakaszban leírtak szerint. Egy véges erőforrás, amely kimeríthető. Fontos [megérteni a használatuk módját.](#pat) Ha szeretné megtudni, hogyan tervezze meg ezt a felhasználást, és szükség esetén enyhítse a megoldást, tekintse át a [SNAT-kimerülés kezelését](#snatexhaust)ismertető

Ha az [Alapszintű Load Balancer több nyilvános IP-cím van társítva](load-balancer-multivip-overview.md), akkor ezek közül bármelyik nyilvános IP-cím a kimenő forgalomra jelölt, az egyik pedig véletlenszerűen van kiválasztva.  

Load Balancer alapszintű kimenő kapcsolatok állapotának figyeléséhez [Azure monitor naplókat használhat Load Balancer](load-balancer-monitor-log.md) és [riasztási eseménynaplók](load-balancer-monitor-log.md#alert-event-log) számára a SNAT-portok kimerülési üzeneteinek figyeléséhez.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>3. forgatókönyv: nyilvános IP-cím nélküli önálló virtuális gép

Ebben az esetben a virtuális gép nem része egy nyilvános Load Balancer-készletnek (és nem része egy belső standard Load Balancer készletnek), és nincs hozzárendelve nyilvános IP-cím. Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a kimenő folyamat privát forrás IP-címét egy nyilvános forrás IP-címére. Az ehhez a kimenő folyamathoz használt nyilvános IP-cím nem konfigurálható, és nem számít bele az előfizetés nyilvános IP-erőforrásának korlátba. Ez a nyilvános IP-cím nem tartozik Önnek, és nem foglalható le. Ha újra telepíti a virtuális gépet vagy a rendelkezésre állási készletet vagy a virtuálisgép-méretezési készletet, akkor ez a nyilvános IP-cím fel lesz szabadítva, és egy új nyilvános IP-címet igényel. Ne használja ezt a forgatókönyvet az IP-címek engedélyezési listájának engedélyezéséhez. Ehelyett használja a másik két forgatókönyv egyikét, ha explicit módon deklarálja a kimenő és a kimenő kapcsolathoz használandó nyilvános IP-címet.

>[!IMPORTANT] 
>Ez a forgatókönyv akkor is érvényes, ha __csak__ belső alapszintű Load Balancer van csatolva. A 3. forgatókönyv __nem érhető el__ , ha egy belső standard Load Balancer egy virtuális géphez van csatolva.  Belső standard Load Balancer használata mellett explicit módon létre kell hoznia az [1](#ilpip) . vagy a [2](#lb) . forgatókönyvet.

Az Azure a SNAT-t használja a port maszkolásával ([Pat](#pat)) a függvény végrehajtásához. Ez a forgatókönyv hasonló a [2. forgatókönyvhöz](#lb), de a használt IP-cím nem szabályozható. Ez egy tartalék forgatókönyv, ha az 1. és a 2. forgatókönyvek nem léteznek. Ezt a forgatókönyvet nem javasoljuk, ha a kimenő címet szeretné szabályozni. Ha a kimenő kapcsolatok az alkalmazás kritikus részét képezik, válasszon másik forgatókönyvet.

Az SNAT-portok a [SNAT és a Pat](#snat) című szakaszban leírtak szerint vannak kiosztva.  A rendelkezésre állási készletet megosztó virtuális gépek száma határozza meg, hogy melyik előfoglalási szintet alkalmazza a rendszer.  A rendelkezésre állási csoport nélküli önálló virtuális gépek gyakorlatilag 1-es készletet hoznak létre az előfoglalások (1024 SNAT-portok) meghatározása céljából. A SNAT-portok olyan véges erőforrás, amely kimeríthető. Fontos [megérteni a használatuk módját.](#pat) Ha szeretné megtudni, hogyan tervezze meg ezt a felhasználást, és szükség esetén enyhítse a megoldást, tekintse át a [SNAT-kimerülés kezelését](#snatexhaust)ismertető

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Több, kombinált forgatókönyv

Az előző szakaszokban leírt forgatókönyvek egy adott eredmény eléréséhez kombinálhatók. Ha több forgatókönyv is létezik, a rendszer elsőbbségi sorrendet alkalmaz: az [1. forgatókönyv](#ilpip) elsőbbséget élvez a 2. és a [3](#defaultsnat). [forgatókönyvvel](#lb) szemben. [2. forgatókönyv](#lb) felülbírálja a [3. forgatókönyvet](#defaultsnat).

Ilyen például egy Azure Resource Manager üzemelő példány, amelyben az alkalmazás nagymértékben támaszkodik a kimenő kapcsolatokra korlátozott számú célhelyre, de a bejövő folyamatokat is fogadja egy Load Balancer-előtérben. Ebben az esetben az 1. és a 2. forgatókönyvet kombinálhatja a támogatáshoz. További mintákért tekintse át a [SNAT-kimerültség kezelését](#snatexhaust)ismertetőt.

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Több előtér a kimenő folyamatokhoz

#### <a name="standard-load-balancer"></a>standard Load Balancer

Standard Load Balancer az összes jelöltet a kimenő folyamatokhoz használja egyszerre, ha [több (nyilvános) IP-frontend](load-balancer-multivip-overview.md) van jelen. Minden előtér megszorozza a rendelkezésre álló előre lefoglalt SNAT-portok számát, ha engedélyezve van a kimenő kapcsolatok terheléselosztási szabálya.

Dönthet úgy, hogy letiltja a előtér-IP-címet a kimenő kapcsolatokhoz egy új terheléselosztási szabály beállítással:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Általában a `disableOutboundSnat` beállítás alapértelmezett _értéke false (hamis_ ), és azt jelzi, hogy ez a szabály a társított virtuális gépek kimenő SNAT a terheléselosztási szabály backend-készletében található. A `disableOutboundSnat` tulajdonság _értéke TRUE (igaz_ ) értékre módosítható, hogy a rendszer Load Balancer ne használja az adott terheléselosztási szabály háttér-készletében lévő virtuális gépekhez tartozó kimenő kapcsolatok társított előtéri IP-címét.  Emellett továbbra is kijelölhet egy adott IP-címet a kimenő folyamatokhoz a [több, kombinált forgatókönyvekben](#combinations) is leírtak szerint.

#### <a name="load-balancer-basic"></a>Alapszintű Load Balancer

Load Balancer Basic (alapszintű) beállítás egyetlen előtér-felületet választ ki a kimenő folyamatokhoz, ha a kimenő folyamatok esetében [több (nyilvános) IP-frontend](load-balancer-multivip-overview.md) szerepel. Ez a kijelölés nem konfigurálható, és a kiválasztási algoritmust véletlenszerűen kell figyelembe venni. Kijelölheti a kimenő folyamatok adott IP-címét [több, kombinált forgatókönyvben](#combinations)leírtak szerint.

### <a name="availability-zones"></a><a name="az"></a>Availability Zones

[A standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használata esetén a Zone-redundáns előtérben a zóna-REDUNDÁNS kimenő SNAT-kapcsolatok biztosíthatók, és a SNAT programozása megtartja a zóna meghibásodását.  Ha a rendszer a zóna-előtérbeli felületeket használja, a kimenő SNAT-kapcsolatok megosztják a sorsot azzal a zónával, amelyhez tartoznak.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>A SNAT és a PAT ismertetése

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maszkolása SNAT (PAT)

Ha egy nyilvános Load Balancer erőforrás virtuálisgép-példányokhoz van társítva, minden kimenő kapcsolódási forrás újraírásra kerül. A forrás a virtuális hálózat magánhálózati IP-címéről a terheléselosztó nyilvános IP-címére íródik újra. A nyilvános IP-címtartomány területen a folyamat 5 rekordjának (forrás IP-címe, forrásport, IP-átviteli protokoll, cél IP-címe, célport) egyedinek kell lennie.  A maszkolási SNAT TCP-vagy UDP-protokollal is használható.

Az ideiglenes portok (SNAT-portok) a magánhálózati forrás IP-címének újraírása után érhetők el, mert több folyamat egyetlen nyilvános IP-címről származik. A SNAT algoritmusú port az UDP és a TCP protokollal eltérő SNAT-portokat foglal le.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT portok

A rendszer egy SNAT-portot használ egy adott cél IP-címhez, porthoz. Ha több TCP-folyamat ugyanarra a cél IP-címére, portra és protokollra vonatkozik, az egyes TCP-folyamatok egyetlen SNAT-portot használnak. Ez biztosítja, hogy a folyamatok egyediek legyenek, amikor ugyanabból a nyilvános IP-címről származnak, és ugyanarra a cél IP-címére, portra és protokollra mutatnak. 

Több folyamat, amelyek mindegyike egy másik cél IP-címére, portra és protokollra vonatkozik, egyetlen SNAT-portot kell megosztania. A cél IP-címe, portja és protokollja egyedivé teszi a folyamatokat anélkül, hogy további forrásport szükségesek a nyilvános IP-címtartomány forgalmának megkülönböztetéséhez.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT portok

Az UDP-SNAT portjait egy másik algoritmus kezeli, mint a TCP SNAT-portok.  A Load Balancer az UDP protokollhoz "Port-korlátozott kúp NAT" néven ismert algoritmust használ.  A rendszer egy SNAT-portot használ minden egyes folyamathoz, a cél IP-címétől, a porttól függetlenül.

#### <a name="snat-port-reuse"></a>SNAT-port újrafelhasználása

A portok felszabadítása után a port igény szerint újra felhasználható.  Egy adott forgatókönyv esetében úgy gondolhatja, hogy a SNAT-portok a legalacsonyabb és a legmagasabb rendelkezésre állási sorba kerülnek, és az első elérhető SNAT-portot használja az új kapcsolatokhoz. 
 
#### <a name="exhaustion"></a>Fogyási

A SNAT-portok erőforrásainak kimerítése esetén a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok SNAT-portokat nem szabadítanak fel. Load Balancer visszaállítja a SNAT-portokat, amikor a folyamat lezárult, és [4 perces üresjárati időkorlátot](#idletimeout) használ a SNAT-portok üresjárati forgalomból való visszaigényléséhez.

Az UDP-SNAT portok általában sokkal gyorsabbak, mint a TCP SNAT-portok, a használt algoritmus különbsége miatt. Ezeket a különbségeket figyelembe véve kell megterveznie és méreteznie a tesztet.

A SNAT-portok kimerüléséhez gyakran vezető feltételek enyhítéséhez tekintse át a [SNAT kezelése](#snatexhaust) szakaszt.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Ideiglenes port előfoglalása a port maszkolása SNAT (PAT)

Az Azure egy algoritmus használatával határozza meg, hogy hány előre lefoglalt SNAT-port áll rendelkezésre a háttérrendszer-készlet mérete alapján a SNAT ([Pat](#pat)) port használatakor. A SNAT-portok egy adott nyilvános IP-forráscím számára elérhető ideiglenes portok.

Az UDP és a TCP számára a SNAT-portok száma azonos, és az IP-átviteli protokolltól függetlenül használatos.  A SNAT-port használata azonban eltérő attól függően, hogy a folyamat UDP vagy TCP.

>[!IMPORTANT]
>A standard SKU SNAT programozása IP-átviteli protokollon alapul, és a terheléselosztási szabályból származik.  Ha csak egy TCP-terheléselosztási szabály létezik, a SNAT csak a TCP protokollhoz érhető el. Ha csak TCP-terheléselosztási szabályra van szüksége, és kimenő SNAT van szükség az UDP-hez, hozzon létre egy UDP-terheléselosztási szabályt ugyanabból a előtér-készletből ugyanahhoz a háttér-készlethez.  Ez elindítja az UDP SNAT-programozását.  Nincs szükség munkaszabályra vagy állapot-mintavételi módszerre.  Az alapszintű SKU-SNAT a terheléselosztási szabályban megadott átviteli protokolltól függetlenül mindig programok SNAT mind az IP átviteli protokollhoz.

Az Azure az egyes virtuális gépek hálózati adapterének IP-konfigurációjához SNAT-portokat szabadít fel. Ha egy IP-konfigurációt ad hozzá a készlethez, a rendszer az IP-konfigurációhoz az SNAT-portokat a háttérrendszer-készlet mérete alapján osztja ki. A kimenő folyamatok létrehozásakor a [Pat](#pat) dinamikusan felhasználja (az előlefoglalt korlátig), és felszabadítja ezeket a portokat, amikor a folyamat lezárul vagy [üresjáratban időtúllépés](#idletimeout) történik.

A következő táblázat a SNAT portok előfoglalásait mutatja be a háttérbeli készlet méreteihez:

| Készlet mérete (VM-példányok) | Az előlefoglalt SNAT-portok száma IP-konfiguráció alapján|
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> Ha [több](load-balancer-multivip-overview.md)előtérrel standard Load Balancer használ, minden ELŐTÉRI IP-cím szorozza meg a rendelkezésre álló SNAT-portok számát az előző táblázatban. Például egy 50 virtuális gép 2. terheléselosztási szabálya, amelyek mindegyike külön előtér-IP-címmel rendelkezik, a 2048 (2x 1024) SNAT-portokat fogja használni. Több előtér- [felület](#multife)részleteinek megtekintése.

Ne feledje, hogy az elérhető SNAT-portok száma nem közvetlenül a folyamatok számára van lefordítva. Egyetlen SNAT-port többször is felhasználható több egyedi célhelyre. A portok csak akkor lesznek felhasználva, ha a folyamatokat egyedivé kell tenni. A tervezéssel és a mérsékléssel kapcsolatos útmutatásért tekintse meg a [kimerített erőforrás kezelésével](#snatexhaust) és a [Pat](#pat)leírásával foglalkozó szakaszt.

Előfordulhat, hogy a háttérbeli készlet méretének módosítása hatással lehet a már meglévő folyamatokra. Ha a háttérbeli készlet mérete nő, és a következő szintjére vált, az előlefoglalt SNAT-portok fele a következő nagyobb háttér-készletre való áttérés során visszaigényelve lesz. A visszaigényelt SNAT-porthoz társított folyamatok időtúllépést okoznak, és újra kell létrehozni. Ha új folyamatra van kísérlet, a folyamat azonnal sikeres lesz, amíg az előlefoglalt portok elérhetők lesznek.

Ha a háttérbeli készlet mérete csökken, és az átmenetek alacsonyabb szinten vannak, a rendelkezésre álló SNAT-portok száma növekszik. Ebben az esetben a meglévő lefoglalt SNAT-portok és a hozzájuk tartozó folyamatok nem érintettek.

A SNAT portok kiosztása IP-átviteli protokoll specifikus (a TCP és az UDP külön van karbantartva), és a következő feltételekkel szabadítható fel:

### <a name="tcp-snat-port-release"></a>TCP SNAT-port kiadása

- Ha bármelyik kiszolgáló/ügyfél elküldi a FINACK, a SNAT-port 240 másodperc után lesz felszabadítva.
- Ha az első látható, a SNAT-port 15 másodperc elteltével fog megjelenni.
- Ha elérte az üresjárati időtúllépést, a rendszer a portot szabadítja fel.

### <a name="udp-snat-port-release"></a>UDP SNAT-port kiadása

- Ha elérte az üresjárati időtúllépést, a rendszer a portot szabadítja fel.

## <a name="problem-solving"></a><a name="problemsolving"></a>Problémamegoldás 

Ez a szakasz a SNAT-kimerültség enyhítését, valamint az Azure-beli kimenő kapcsolatok esetén felmerülő megoldásokat ismerteti.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>A SNAT (PAT) portjának kimerülésének kezelése
A [pathoz](#pat) használt [ideiglenes portok](#preallocatedports) kimeríthető erőforrások, amelyeket az [önálló virtuális gép nyilvános IP-cím nélküli](#defaultsnat) és [elosztott terhelésű virtuális gép nyilvános IP-cím](#lb)nélkül című része ismertet. Az időszakos portok használatát figyelemmel kísérheti, és összehasonlíthatja az aktuális kiosztásával, hogy meghatározza a SNAT exhuastion kockázatát vagy megerősítését [az útmutató segítségével](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) .

Ha tudja, hogy több kimenő TCP-vagy UDP-kapcsolatra van szüksége ugyanahhoz a cél IP-címhez és porthoz, és megfigyelheti a sikertelen kimenő kapcsolatokat, vagy ha támogatja a SNAT-portok kimerítését (a [Pat](#pat)által használt időszakosan lefoglalt időszakos [portok](#preallocatedports) ), számos általános kockázatcsökkentő lehetőség közül választhat. Tekintse át ezeket a beállításokat, és döntse el, hogy mi az elérhető és melyik a legmegfelelőbb a forgatókönyvhöz. Lehetséges, hogy egy vagy több segíthet a forgatókönyv kezelésében.

Ha nem sikerül megismerni a kimenő kapcsolatok viselkedését, használhatja az IP-verem statisztikáit (netstat). Vagy hasznos lehet a kapcsolatok viselkedésének figyelésére a csomagok rögzítései használatával. Ezeket a csomagokat a példány vendég operációs rendszerében hajthatja végre, vagy Network Watcher is használhatja a [csomagok rögzítéséhez](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Az alkalmazás módosítása a kapcsolatok újrafelhasználásához 
A SNAT használt ideiglenes portok igényét csökkentheti az alkalmazásban található kapcsolatok újrafelhasználásával. Ez különösen az olyan protokollok esetében igaz, mint a HTTP/1.1, ahol az alapértelmezett a kapcsolatok újrafelhasználása. Továbbá a HTTP protokollt használó más protokollok (például a REST) is hasznosak lehetnek. 

Az újrafelhasználás mindig jobb, mint az egyes kérések egyéni, atomi TCP-kapcsolatai. A több teljesítményű, nagyon hatékony TCP-tranzakció eredményeit is újra felhasználhatja.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Az alkalmazás módosítása a kapcsolatok készletezésének használatára
Az alkalmazásban létrehozhat egy kapcsolati készletezési sémát, ahol a kérések belsőleg oszlanak meg egy rögzített kapcsolaton belül (az összes újrafelhasználás, ahol lehetséges). Ez a séma korlátozza a használatban lévő ideiglenes portok számát, és egy előre jelezhető környezetet hoz létre. Ez a séma növelheti a kérések átviteli sebességét azáltal, hogy lehetővé teszi több egyidejű művelet engedélyezését, ha egyetlen kapcsolódás blokkolja a művelet válaszát.  

Előfordulhat, hogy a kapcsolatok készletezése már létezik azon a keretrendszeren belül, amelyet az alkalmazás fejlesztéséhez vagy az alkalmazás konfigurációs beállításaihoz használ. Összekapcsolhatja a kapcsolatok készletezését a kapcsolatok újbóli használatával. A többszörös kérelmek ezután egy rögzített, kiszámítható számú portot használnak ugyanahhoz a cél IP-címhez és porthoz. A kérések kihasználják a TCP-tranzakciók hatékony használatát a késés és az erőforrások kihasználtságának csökkentése érdekében. Az UDP-tranzakciók is hasznosak lehetnek, mivel az UDP-folyamatok számának kezelése a kimerülési feltételek elkerülésével és a SNAT-portok kihasználtságának kezelésével is jár.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára
Ha a [pathoz](#pat) használt, [előlefoglalt](#preallocatedports) időszakos portok kimerülése vagy az alkalmazások meghibásodása esetén az agresszív vagy találgatásos kényszerített újrapróbálkozások a romlás és a leállítási logikája miatt kimerítik vagy megtartják a fáradtságot. Az ideiglenes portok igényét kevésbé agresszív újrapróbálkozási logika használatával csökkentheti. 

Az ideiglenes portok 4 perces üresjárati időkorláttal rendelkeznek (nem állítható be). Ha az újrapróbálkozások túl agresszívek, a kimerültségnek nincs lehetősége a saját törlésére. Ezért figyelembe véve, hogy a--és milyen gyakran – az alkalmazás újrapróbálkozási tranzakciói a terv kritikus részét képezik.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Nyilvános IP-cím kiosztása minden virtuális géphez
Egy nyilvános IP-cím hozzárendelésével a forgatókönyv a [nyilvános IP-címekre változik egy virtuális gépen](#ilpip). Az egyes virtuális gépekhez használt nyilvános IP-címek minden ideiglenes portja elérhető a virtuális gép számára. (Azon forgatókönyvek esetében, amelyekben a nyilvános IP-címek ideiglenes portjai meg vannak osztva a megfelelő háttér-készlethez társított összes virtuális géppel.) Kompromisszumok merülnek fel, mint például a nyilvános IP-címek további díja, valamint a nagy számú egyedi IP-cím engedélyezési lehetséges következményei.

>[!NOTE] 
>Ez a beállítás webes feldolgozói szerepkörök esetén nem érhető el.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Több előtér-felület használata

Nyilvános standard Load Balancer használata esetén [több előtér-IP-címet kell hozzárendelni a kimenő kapcsolatokhoz](#multife) , és meg kell [szorozni a rendelkezésre álló SNAT-portok számát](#preallocatedports).  Hozzon létre egy előtérbeli IP-konfigurációt, szabályt és háttér-készletet a SNAT programozásának elindításához a frontend nyilvános IP-címére.  A szabálynak nem kell működnie, és az állapot-mintavételnek nem kell sikeresnek lennie.  Ha több előtérbeli felületet is használ a bejövő (nem csak a kimenő) előtérben, akkor a megbízhatóság biztosításához használja az egyéni állapotú mintavételt is.

>[!NOTE]
>A legtöbb esetben a SNAT-portok kimerítése hibás kialakítás jele.  Győződjön meg arról, hogy a portok kimerítésének okát érdemes megismerni, mielőtt SNAT-portok hozzáadására több felületet használ.  Előfordulhat, hogy olyan problémát takar, amely később hibát okozhat.

#### <a name="scale-out"></a><a name="scaleout"></a>Horizontális felskálázás

Az [előlefoglalt portok](#preallocatedports) a háttérrendszer-készlet mérete alapján vannak hozzárendelve, és rétegekbe vannak csoportosítva, hogy kis mennyiségű portot lehessen megszakítani, ha a portok némelyikét újra kell osztani a háttérrendszer következő méretének növeléséhez.  Lehetséges, hogy egy adott előtérben a SNAT-portok kihasználtságának intenzitását növelheti, ha a háttér-készletet egy adott réteg maximális méretére szeretné méretezni.  Ehhez az szükséges, hogy az alkalmazás hatékonyan felskálázásra kerüljön.

A háttér-készletben lévő két virtuális gép esetében például IP-konfiguráció esetén 1024 SNAT-port érhető el, ami lehetővé teszi, hogy összesen 2048 SNAT portot engedélyezzen a telepítéshez.  Ha az üzembe helyezést 50 virtuális gépre szeretné növelni, bár az előre lefoglalt portok száma virtuális gépenként állandó marad, akkor az üzemelő példány összesen 51 200 (50 x 1024) SNAT-portot használhat.  Ha ki szeretné bővíteni az üzemelő példányt, ellenőrizze az [előlefoglalt portok](#preallocatedports) számát egy szinten, hogy a méretezést a maximális értékre alakítsa ki a megfelelő szintjére.  Ha az előző példában úgy döntött, hogy 50-példány helyett 51-ra szeretné felskálázást végezni, akkor a következő szintet kell végrehajtania, és a végén a virtuális gép kevesebb SNAT-portja, valamint összesen szerepel.

Ha felskálázást végez a következő nagyobb háttérrendszer-készlet méretével, előfordulhat, hogy a kimenő kapcsolatok némelyike időtúllépést okoz, ha a lefoglalt portok újra le lesznek foglalva.  Ha csak néhány SNAT-portot használ, a következő nagyobb méretű háttér-készlet méretének horizontális felskálázása lényegtelen.  A meglévő portok felét a rendszer minden alkalommal újra lefoglalja, amikor a következő háttérbeli készletre lép.  Ha nem szeretné, hogy ez megtörténjen, az üzembe helyezést a rétegek méretére kell alakítania.  Vagy ellenőrizze, hogy az alkalmazás képes-e az észlelésre, és szükség esetén próbálkozzon újra.  A TCP-Keepalives segítséget nyújthat az észlelésben, ha a SNAT-portok már nem működnek az újrafoglalás miatt.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>A kimenő Üresjárati időkorlát alaphelyzetbe állítása a Keepalives használatával

A kimenő kapcsolatok 4 perces üresjárati időkorláttal rendelkeznek. Ez az időkorlát a [kimenő szabályokon](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout)keresztül állítható be. A Transport (például TCP Keepalives) vagy az alkalmazás-réteg Keepalives használatával is frissítheti az üresjárati folyamatokat, és szükség esetén visszaállíthatja ezt az üresjárati időkorlátot.  

A TCP-Keepalives használata esetén elegendő a csatlakozás egyik oldalán való engedélyezése. Például elegendő, ha csak a kiszolgáló oldalon engedélyezi őket, hogy alaphelyzetbe állítsa a folyamat üresjárati időzítőjét, és nem szükséges mindkét fél számára a TCP-Keepalives kezdeményezni.  Hasonló fogalmak léteznek az alkalmazási réteghez, beleértve az adatbázis-ügyfél-kiszolgáló konfigurációkat is.  Tekintse meg a kiszolgáló oldalát, hogy milyen lehetőségek léteznek az alkalmazás-specifikus Keepalives.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>A virtuális gép által használt nyilvános IP-cím feltárása
A kimenő kapcsolatok nyilvános forrás IP-címének meghatározása számos módon megtörténik. A nyit egy olyan szolgáltatást biztosít, amely a virtuális gép nyilvános IP-címét jeleníti meg. 

Az nslookup parancs használatával DNS-lekérdezést küldhet a myip.opendns.com név számára a nyit feloldójának. A szolgáltatás visszaadja a lekérdezés küldéséhez használt forrás IP-címet. Ha a következő lekérdezést futtatja a virtuális gépről, a válasz az adott virtuális géphez használt nyilvános IP-cím:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Kimenő kapcsolat megakadályozása
Időnként előfordulhat, hogy a virtuális gép számára engedélyezni kell a kimenő folyamat létrehozását. Vagy előfordulhat, hogy meg kell adni egy követelményt, amely azt a célt szolgálhatja, hogy mely célhelyek érhetők el a kimenő folyamatokkal, vagy hogy mely célhelyek kezdhetik meg Ebben az esetben [hálózati biztonsági csoportok](../virtual-network/security-overview.md) használatával felügyelheti a virtuális gép által elérhető célhelyeket. A NSG használatával is kezelheti, hogy mely nyilvános célhelyek indíthatnak bejövő folyamatokat.

Ha NSG alkalmaz egy elosztott terhelésű virtuális gépre, ügyeljen a [szolgáltatás-címkékre](../virtual-network/security-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra](../virtual-network/security-overview.md#default-security-rules). Gondoskodnia kell arról, hogy a virtuális gép Azure Load Balancertól kapjon állapot-mintavételi kérelmeket. 

Ha egy NSG letiltja az állapot-mintavételi kérelmeket a AZURE_LOADBALANCER alapértelmezett címkétől, a virtuális gép állapotának mintavétele meghiúsul, és a virtuális gép meg van jelölve. Load Balancer leállítja az új folyamatok küldését a virtuális gépre.

## <a name="limitations"></a>Korlátozások
- A VNet és más Microsoft-platformokat nem tartalmazó webes feldolgozói szerepkörök csak akkor érhetők el, ha csak belső standard Load Balancer van használatban, mivel a VNet szolgáltatások és egyéb platform-szolgáltatások funkciójának mellékhatása. Ne támaszkodjon erre a mellékhatásra, mert maga a saját szolgáltatás, vagy az alapul szolgáló platform értesítés nélkül változhat. Mindig feltételezni kell, hogy a kimenő kapcsolatot explicit módon kell létrehoznia, ha csak belső standard Load Balancer használata esetén szükséges. A cikkben ismertetett [alapértelmezett SNAT](#defaultsnat) -forgatókönyv 3. esete nem érhető el.

## <a name="next-steps"></a>További lépések

- További információ a [standard Load Balancerról](load-balancer-standard-overview.md).
- További információ a szabványos nyilvános Load Balancer [kimenő szabályairól](load-balancer-outbound-rules-overview.md) .
- További információ a [Load Balancerról](load-balancer-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
- Ismerje meg az Azure egyéb fontos [hálózati funkcióit](../networking/networking-overview.md) .
