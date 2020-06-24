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
ms.openlocfilehash: 0d16823f920695f84db74122c9a2ac07de0abdb2
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907074"
---
# <a name="outbound-connections-in-azure"></a>Kimenő kapcsolatok az Azure-ban

A Azure Load Balancer több különböző mechanizmuson keresztül biztosít kimenő kapcsolatot az ügyfelek központi telepítéséhez. Ez a cikk a forgatókönyvek, a működésük és a kezelésük módját ismerteti. Ha a kimenő kapcsolattal kapcsolatos problémát tapasztal egy Azure Load Balanceron keresztül, tekintse meg a [Kimenő kapcsolatok hibaelhárítási útmutatóját](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>Ez a cikk csak a Resource Manager-alapú üzemelő példányokat ismerteti. Tekintse át az Azure-beli klasszikus üzembe helyezési forgatókönyvek [kimenő kapcsolatait (klasszikus)](load-balancer-outbound-connections-classic.md) .

Az Azure-beli üzemelő példányok az Azure-on kívüli végpontokkal kommunikálhatnak a nyilvános IP-címtartomány használatával. Ha egy példány kimenő folyamatot kezdeményez a nyilvános IP-címtartomány egy céljára, az Azure dinamikusan leképezi a magánhálózati IP-címet egy nyilvános IP-címhez. Ennek a leképezésnek a létrehozása után a kimenő forgalomtól érkező forgalom visszaadott értéke elérheti azt a magánhálózati IP-címet is, ahol a folyamat származik.

Az Azure a forrás hálózati címfordítás (SNAT) használatával hajtja végre ezt a funkciót. Ha egy nyilvános IP-cím mögé több magánhálózati IP-cím is fel van használva, akkor az Azure a címfordítás [(Pat)](#pat) használatával álcázza a magánhálózati IP-címeket. A rendszer ideiglenes portokat használ a PAT számára, és a készlet mérete alapján van [előfoglalva](#preallocatedports) .

Több [kimenő forgatókönyv](#scenarios)is létezik. Ezeket a forgatókönyveket igény szerint kombinálhatja. Alaposan tekintse át ezeket a képességeket, megkötéseket és mintákat, ahogyan azok az üzemi modellre és az alkalmazásokra vonatkoznak. Tekintse át az [ilyen forgatókönyvek kezelésével](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)kapcsolatos útmutatást.

>[!IMPORTANT] 
>A standard Load Balancer és a standard nyilvános IP-címek új képességeket és különböző viselkedéseket vezetnek be a kimenő kapcsolatokhoz.  Ezek nem azonosak az alapszintű SKU-kal.  Ha standard SKU-kal dolgozik a kimenő kapcsolaton, explicit módon meg kell határoznia a standard nyilvános IP-címeket vagy a standard nyilvános Load Balancer.  Ez magában foglalja a kimenő kapcsolatok létrehozását belső standard Load Balancer használata esetén.  Javasoljuk, hogy mindig használjon kimenő szabályokat egy standard nyilvános Load Balanceron.  A [3. forgatókönyv](#defaultsnat) nem érhető el a standard SKU-val.  Ez azt jelenti, hogy ha belső standard Load Balancer használ, meg kell tennie a kimenő kapcsolatok létrehozásához szükséges lépéseket a háttér-készletben lévő virtuális gépekhez, ha kimenő kapcsolatra van szükség.  A kimenő kapcsolat kontextusában egyetlen önálló virtuális gép, a rendelkezésre állási csoportban lévő összes virtuális gép a VMSS összes példánya csoportként viselkedik. Ez azt jelenti, hogy ha egy rendelkezésre állási csoport egyetlen virtuális gépe egy szabványos SKU-hoz van társítva, akkor a rendelkezésre állási csoportba tartozó összes virtuálisgép-példány ugyanúgy viselkedik, mintha a standard SKU-hoz társítva van, még akkor is, ha egy adott példány nincs közvetlenül társítva. Ez a viselkedés abban az esetben is megfigyelhető, ha egy önálló virtuális gép több hálózati adapterrel rendelkezik, amelyek egy terheléselosztó számára vannak csatlakoztatva. Ha egy hálózati adaptert önállóként adnak hozzá, akkor ugyanaz lesz a viselkedése. Körültekintően tekintse át a teljes dokumentumot, hogy megismerje az általános fogalmakat, tekintse át [standard Load Balancer](load-balancer-standard-overview.md) az SKU-ket és a [kimenő szabályokat](load-balancer-outbound-rules-overview.md).  A kimenő szabályok használata lehetővé teszi a kimenő kapcsolatok összes aspektusának részletes szabályozását.

## <a name="scenario-overview"></a><a name="scenarios"></a>Forgatókönyv áttekintése

A Azure Load Balancer és a kapcsolódó erőforrások explicit módon vannak meghatározva a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)használatakor.  Az Azure jelenleg három különböző módszert biztosít a kimenő kapcsolatok eléréséhez Azure Resource Manager erőforrásokhoz. 

| Termékváltozatok | Forgatókönyv | Metódus | IP-protokollok | Leírás |
| --- | --- | --- | --- | --- |
| Standard, alapszintű | [1. a példány szintű nyilvános IP-címmel rendelkező virtuális gép (Load Balancer) vagy anélkül](#ilpip) | SNAT, nem használt port | TCP, UDP, ICMP, ESP | Az Azure a példány hálózati adapterének IP-konfigurációjához hozzárendelt nyilvános IP-címet használja. A példányhoz minden elérhető ideiglenes port tartozik. Standard Load Balancer használata esetén a [Kimenő szabályok](load-balancer-outbound-rules-overview.md) nem támogatottak, ha egy nyilvános IP-cím van hozzárendelve a virtuális géphez. |
| Standard, alapszintű | [2. a virtuális géphez társított nyilvános Load Balancer (nincs nyilvános IP-cím a példányon)](#lb) | SNAT a Load Balancer előtérrel (PAT) rendelkező portokkal | TCP, UDP |Az Azure megosztja a nyilvános Load Balancer a több magánhálózati IP-címmel rendelkező előtér nyilvános IP-címét. Az Azure a frontendek ideiglenes portjait használja a PAT számára. A standard Load Balancer használatakor a kimenő kapcsolatok explicit módon történő definiálásához a [kimenő szabályokat](load-balancer-outbound-rules-overview.md) kell használni. |
| none vagy alapszintű | [3. önálló virtuális gép (nincs Load Balancer, nincs nyilvános IP-cím)](#defaultsnat) | SNAT a port maszkolásával (PAT) | TCP, UDP | Az Azure automatikusan kijelöl egy nyilvános IP-címet a SNAT számára, megosztja ezt a nyilvános IP-címet a rendelkezésre állási csoport több magánhálózati IP-címével, és a nyilvános IP-cím ideiglenes portjait használja. Ez a forgatókönyv az előző forgatókönyvek tartaléka. Ha láthatóságra és vezérlésre van szüksége, nem ajánlott. |

Ha nem szeretné, hogy a virtuális gép kommunikáljon az Azure-on kívüli végpontokkal a nyilvános IP-címtartomány területén, a hálózati biztonsági csoportok (NSG) használatával letilthatja a hozzáférést igény szerint. A [Kimenő kapcsolatok megakadályozása](#preventoutbound) című szakasz részletesebben tárgyalja a NSG. A virtuális hálózatok kimenő hozzáférés nélküli kialakításával, megvalósításával és kezelésével kapcsolatos útmutató a jelen cikk hatókörén kívül esik.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>1. forgatókönyv: nyilvános IP-címmel rendelkező virtuális gép

Ebben az esetben a virtuális gépnek hozzá van rendelve egy nyilvános IP-címe. A kimenő kapcsolatok tekintetében nem számít, hogy a virtuális gép terheléselosztás alatt áll-e. Ez a forgatókönyv elsőbbséget élvez a többiekkel szemben. Nyilvános IP-cím használata esetén a virtuális gép az összes kimenő folyamathoz a nyilvános IP-címet használja.  

Egy virtuális géphez hozzárendelt nyilvános IP-cím 1:1-kapcsolat (nem 1: sok), és állapot nélküli 1:1 NAT-ként lett megvalósítva.  A rendszer nem használja az álcázott portot (PAT), és a virtuális gép számára elérhető minden ideiglenes port.

Ha az alkalmazás sok kimenő folyamatot kezdeményez, és SNAT-portok kimerülését tapasztalja, érdemes lehet egy [nyilvános IP-címet hozzárendelni a SNAT-megkötések enyhítéséhez](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Tekintse át a [SNAT-kimerültség kezelését](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) teljes egészében.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>2. forgatókönyv: elosztott terhelésű virtuális gép nyilvános IP-cím nélkül

Ebben az esetben a virtuális gép egy nyilvános Load Balancer backend-készlet része. A virtuális gépnek nincs hozzárendelve nyilvános IP-címe. A Load Balancer erőforrást egy terheléselosztó-szabállyal kell konfigurálni, hogy kapcsolatot hozzon létre a nyilvános IP-frontend és a háttér-készlet között.

Ha nem fejezi be ezt a szabályt, a viselkedést a [nyilvános IP-cím nélküli önálló virtuális gép](#defaultsnat)forgatókönyve írja le. Nincs szükség ahhoz, hogy a szabály a háttérrendszer munkafolyamati készletében működő figyelővel rendelkezzen a sikeres állapothoz.

Ha a terheléselosztásos virtuális gép kimenő folyamatot hoz létre, az Azure lefordítja a kimenő folyamat privát forrás IP-címét a nyilvános Load Balancer előtér nyilvános IP-címére. Az Azure a SNAT használatával hajtja végre ezt a funkciót. Az Azure a [Pat](#pat) használatával több magánhálózati IP-címet is maszkolást használ egy nyilvános IP-cím mögé. 

A terheléselosztó nyilvános IP-címének ideiglenes portjai a virtuális gép által kezdeményezett egyes folyamatok megkülönböztetésére szolgálnak. A SNAT dinamikusan használja az [előlefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. Ebben a kontextusban a SNAT használt ideiglenes portok neve SNAT-portok.

A SNAT-portok előre le vannak foglalva a [SNAT és a Pat](#snat) című szakaszban leírtak szerint. Egy véges erőforrás, amely kimeríthető. Fontos [megérteni a használatuk módját.](#pat) Ha szeretné megtudni, hogyan tervezze meg ezt a felhasználást, és szükség esetén enyhítse a megoldást, tekintse át a [SNAT-kimerülés kezelését](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)ismertető

Ha az [Alapszintű Load Balancer több nyilvános IP-cím van társítva](load-balancer-multivip-overview.md), akkor ezek közül bármelyik nyilvános IP-cím a kimenő forgalomra jelölt, az egyik pedig véletlenszerűen van kiválasztva.  

Load Balancer alapszintű kimenő kapcsolatok állapotának figyeléséhez [Azure monitor naplókat használhat Load Balancer](load-balancer-monitor-log.md) és [riasztási eseménynaplók](load-balancer-monitor-log.md#alert-event-log) számára a SNAT-portok kimerülési üzeneteinek figyeléséhez.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>3. forgatókönyv: nyilvános IP-cím nélküli önálló virtuális gép

Ebben az esetben a virtuális gép nem része egy nyilvános Load Balancer-készletnek (és nem része egy belső standard Load Balancer készletnek), és nincs hozzárendelve nyilvános IP-cím. Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a kimenő folyamat privát forrás IP-címét egy nyilvános forrás IP-címére. Az ehhez a kimenő folyamathoz használt nyilvános IP-cím nem konfigurálható, és nem számít bele az előfizetés nyilvános IP-erőforrásának korlátba. Ez a nyilvános IP-cím nem tartozik Önnek, és nem foglalható le. Ha újra telepíti a virtuális gépet vagy a rendelkezésre állási készletet vagy a virtuálisgép-méretezési készletet, akkor ez a nyilvános IP-cím fel lesz szabadítva, és egy új nyilvános IP-címet igényel. Ne használja ezt a forgatókönyvet az IP-címek engedélyezési listájának engedélyezéséhez. Ehelyett használja a másik két forgatókönyv egyikét, ha explicit módon deklarálja a kimenő és a kimenő kapcsolathoz használandó nyilvános IP-címet.

>[!IMPORTANT] 
>Ez a forgatókönyv akkor is érvényes, ha __csak__ belső alapszintű Load Balancer van csatolva. A 3. forgatókönyv __nem érhető el__ , ha egy belső standard Load Balancer egy virtuális géphez van csatolva.  Belső standard Load Balancer használata mellett explicit módon létre kell hoznia az [1](#ilpip) . vagy a [2](#lb) . forgatókönyvet.

Az Azure a SNAT-t használja a port maszkolásával ([Pat](#pat)) a függvény végrehajtásához. Ez a forgatókönyv hasonló a [2. forgatókönyvhöz](#lb), de a használt IP-cím nem szabályozható. Ez egy tartalék forgatókönyv, ha az 1. és a 2. forgatókönyvek nem léteznek. Ezt a forgatókönyvet nem javasoljuk, ha a kimenő címet szeretné szabályozni. Ha a kimenő kapcsolatok az alkalmazás kritikus részét képezik, válasszon másik forgatókönyvet.

Az SNAT-portok a [SNAT és a Pat](#snat) című szakaszban leírtak szerint vannak kiosztva.  A rendelkezésre állási készletet megosztó virtuális gépek száma határozza meg, hogy melyik előfoglalási szintet alkalmazza a rendszer.  A rendelkezésre állási csoport nélküli önálló virtuális gépek gyakorlatilag 1-es készletet hoznak létre az előfoglalások (1024 SNAT-portok) meghatározása céljából. A SNAT-portok olyan véges erőforrás, amely kimeríthető. Fontos [megérteni a használatuk módját.](#pat) Ha szeretné megtudni, hogyan tervezze meg ezt a felhasználást, és szükség esetén enyhítse a megoldást, tekintse át a [SNAT-kimerülés kezelését](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)ismertető

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Több, kombinált forgatókönyv

Az előző szakaszokban leírt forgatókönyvek egy adott eredmény eléréséhez kombinálhatók. Ha több forgatókönyv is létezik, a rendszer elsőbbségi sorrendet alkalmaz: az [1. forgatókönyv](#ilpip) elsőbbséget élvez a 2. és a [3](#defaultsnat). [forgatókönyvvel](#lb) szemben. [2. forgatókönyv](#lb) felülbírálja a [3. forgatókönyvet](#defaultsnat).

Ilyen például egy Azure Resource Manager üzemelő példány, amelyben az alkalmazás nagymértékben támaszkodik a kimenő kapcsolatokra korlátozott számú célhelyre, de a bejövő folyamatokat is fogadja egy Load Balancer-előtérben. Ebben az esetben az 1. és a 2. forgatókönyvet kombinálhatja a támogatáshoz. További mintákért tekintse át a [SNAT-kimerültség kezelését](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)ismertetőt.

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

Ha egy nyilvános Load Balancer erőforrás olyan virtuálisgép-példányokhoz van társítva, amelyek nem rendelkeznek dedikált nyilvános IP-címekkel, minden kimenő kapcsolódási forrás újraírásra kerül. A forrás a virtuális hálózat magánhálózati IP-címéről a terheléselosztó nyilvános IP-címére íródik újra. A nyilvános IP-címtartomány területen a folyamat 5 rekordjának (forrás IP-címe, forrásport, IP-átviteli protokoll, cél IP-címe, célport) egyedinek kell lennie. A maszkolási SNAT TCP-vagy UDP-protokollal is használható.

Az ideiglenes portok (SNAT-portok) a magánhálózati forrás IP-címének újraírása után érhetők el, mert több folyamat egyetlen nyilvános IP-címről származik. A SNAT algoritmusú port az UDP és a TCP protokollal eltérő SNAT-portokat foglal le.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT portok

A rendszer egy SNAT-portot használ egy adott cél IP-címhez, porthoz. Ha több TCP-folyamat ugyanarra a cél IP-címére, portra és protokollra vonatkozik, az egyes TCP-folyamatok egyetlen SNAT-portot használnak. Ez biztosítja, hogy a folyamatok egyediek legyenek, amikor ugyanabból a nyilvános IP-címről származnak, és ugyanarra a cél IP-címére, portra és protokollra mutatnak. 

Több folyamat, amelyek mindegyike egy másik cél IP-címére, portra és protokollra vonatkozik, egyetlen SNAT-portot kell megosztania. A cél IP-címe, portja és protokollja egyedivé teszi a folyamatokat anélkül, hogy további forrásport szükségesek a nyilvános IP-címtartomány forgalmának megkülönböztetéséhez.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT portok

Az UDP-SNAT portjait egy másik algoritmus kezeli, mint a TCP SNAT-portok.  A Load Balancer az UDP protokollhoz "Port-korlátozott kúp NAT" néven ismert algoritmust használ.  A rendszer egy SNAT-portot használ minden egyes folyamathoz, a cél IP-címétől, a porttól függetlenül.

#### <a name="snat-port-reuse"></a>SNAT-port újrafelhasználása

A portok felszabadítása után a port igény szerint újra felhasználható.  Egy adott forgatókönyv esetében úgy gondolhatja, hogy a SNAT-portok a legalacsonyabb és a legmagasabb rendelkezésre állási sorba kerülnek, és az első elérhető SNAT-portot használja az új kapcsolatokhoz. 
 
#### <a name="exhaustion"></a>Fogyási

A SNAT-portok erőforrásainak kimerítése esetén a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok SNAT-portokat nem szabadítanak fel. Load Balancer visszaállítja a SNAT-portokat, amikor a folyamat lezárult, és [4 perces üresjárati időkorlátot](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) használ a SNAT-portok üresjárati forgalomból való visszaigényléséhez.

Az UDP-SNAT portok általában sokkal gyorsabbak, mint a TCP SNAT-portok, a használt algoritmus különbsége miatt. Ezeket a különbségeket figyelembe véve kell megterveznie és méreteznie a tesztet.

A SNAT-portok kimerüléséhez gyakran vezető feltételek enyhítéséhez tekintse át a [SNAT kezelése](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) szakaszt.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Ideiglenes port előfoglalása a port maszkolása SNAT (PAT)

Az Azure egy algoritmus használatával határozza meg, hogy hány előre lefoglalt SNAT-port áll rendelkezésre a háttérrendszer-készlet mérete alapján a SNAT ([Pat](#pat)) port használatakor. A SNAT-portok egy adott nyilvános IP-forráscím számára elérhető ideiglenes portok. A terheléselosztó minden egyes nyilvános IP-címéhez 64 000-as port érhető el SNAT-portként minden IP-átviteli protokollhoz.

Az UDP és a TCP számára a SNAT-portok száma azonos, és az IP-átviteli protokolltól függetlenül használatos.  A SNAT-port használata azonban eltérő attól függően, hogy a folyamat UDP vagy TCP.

>[!IMPORTANT]
>A standard SKU SNAT programozása IP-átviteli protokollon alapul, és a terheléselosztási szabályból származik.  Ha csak egy TCP-terheléselosztási szabály létezik, a SNAT csak a TCP protokollhoz érhető el. Ha csak TCP-terheléselosztási szabályra van szüksége, és kimenő SNAT van szükség az UDP-hez, hozzon létre egy UDP-terheléselosztási szabályt ugyanabból a előtér-készletből ugyanahhoz a háttér-készlethez.  Ez elindítja az UDP SNAT-programozását.  Nincs szükség munkaszabályra vagy állapot-mintavételi módszerre.  Az alapszintű SKU-SNAT a terheléselosztási szabályban megadott átviteli protokolltól függetlenül mindig programok SNAT mind az IP átviteli protokollhoz.

Az Azure az egyes virtuális gépek hálózati adapterének IP-konfigurációjához SNAT-portokat szabadít fel. Ha egy IP-konfigurációt ad hozzá a készlethez, a rendszer az IP-konfigurációhoz az SNAT-portokat a háttérrendszer-készlet mérete alapján osztja ki. A kimenő folyamatok létrehozásakor a [Pat](#pat) dinamikusan felhasználja (az előlefoglalt korlátig), és felszabadítja ezeket a portokat, amikor a folyamat lezárul vagy [üresjáratban időtúllépés](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) történik.

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

Ne feledje, hogy az elérhető SNAT-portok száma nem közvetlenül a folyamatok számára van lefordítva. Egyetlen SNAT-port többször is felhasználható több egyedi célhelyre. A portok csak akkor lesznek felhasználva, ha a folyamatokat egyedivé kell tenni. A tervezéssel és a mérsékléssel kapcsolatos útmutatásért tekintse meg a [kimerített erőforrás kezelésével](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) és a [Pat](#pat)leírásával foglalkozó szakaszt.

Előfordulhat, hogy a háttérbeli készlet méretének módosítása hatással lehet a már meglévő folyamatokra. Ha a háttérbeli készlet mérete nő, és a következő szintjére vált, az előlefoglalt SNAT-portok fele a következő nagyobb háttér-készletre való áttérés során visszaigényelve lesz. A visszaigényelt SNAT-porthoz társított folyamatok időtúllépést okoznak, és újra kell létrehozni. Ha új folyamatra van kísérlet, a folyamat azonnal sikeres lesz, amíg az előlefoglalt portok elérhetők lesznek.

Ha a háttérbeli készlet mérete csökken, és az átmenetek alacsonyabb szinten vannak, a rendelkezésre álló SNAT-portok száma növekszik. Ebben az esetben a meglévő lefoglalt SNAT-portok és a hozzájuk tartozó folyamatok nem érintettek.

A SNAT portok kiosztása IP-átviteli protokoll specifikus (a TCP és az UDP külön van karbantartva), és a következő feltételekkel szabadítható fel:

### <a name="tcp-snat-port-release"></a>TCP SNAT-port kiadása

- Ha bármelyik kiszolgáló/ügyfél elküldi a FINACK, a SNAT-port 240 másodperc után lesz felszabadítva.
- Ha az első látható, a SNAT-port 15 másodperc elteltével fog megjelenni.
- Ha elérte az üresjárati időtúllépést, a rendszer a portot szabadítja fel.

### <a name="udp-snat-port-release"></a>UDP SNAT-port kiadása

- Ha elérte az üresjárati időtúllépést, a rendszer a portot szabadítja fel.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>A virtuális gép által használt nyilvános IP-cím feltárása
A kimenő kapcsolatok nyilvános forrás IP-címének meghatározása számos módon megtörténik. A nyit egy olyan szolgáltatást biztosít, amely a virtuális gép nyilvános IP-címét jeleníti meg. 

Az nslookup parancs használatával DNS-lekérdezést küldhet a myip.opendns.com név számára a nyit feloldójának. A szolgáltatás visszaadja a lekérdezés küldéséhez használt forrás IP-címet. Ha a következő lekérdezést futtatja a virtuális gépről, a válasz az adott virtuális géphez használt nyilvános IP-cím:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Kimenő kapcsolat megakadályozása
Időnként előfordulhat, hogy a virtuális gép számára engedélyezni kell a kimenő folyamat létrehozását. Vagy előfordulhat, hogy meg kell adni egy követelményt, amely azt a célt szolgálhatja, hogy mely célhelyek érhetők el a kimenő folyamatokkal, vagy hogy mely célhelyek kezdhetik meg Ebben az esetben [hálózati biztonsági csoportok](../virtual-network/security-overview.md) használatával felügyelheti a virtuális gép által elérhető célhelyeket. A NSG használatával is kezelheti, hogy mely nyilvános célhelyek indíthatnak bejövő folyamatokat.

Ha NSG alkalmaz egy elosztott terhelésű virtuális gépre, ügyeljen a [szolgáltatás-címkékre](../virtual-network/security-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra](../virtual-network/security-overview.md#default-security-rules). Gondoskodnia kell arról, hogy a virtuális gép Azure Load Balancertól kapjon állapot-mintavételi kérelmeket. 

Ha egy NSG letiltja az állapot-mintavételi kérelmeket a AZURE_LOADBALANCER alapértelmezett címkétől, a virtuális gép állapotának mintavétele meghiúsul, és a virtuális gép meg van jelölve. Load Balancer leállítja az új folyamatok küldését a virtuális gépre.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Kapcsolatok az Azure Storage-ban ugyanabban a régióban

A fenti forgatókönyvekben a kimenő kapcsolat nem szükséges a virtuális géppel azonos régióban lévő tárolóhoz való csatlakozáshoz. Ha ezt nem szeretné, a fentiekben leírtak szerint használjon hálózati biztonsági csoportokat (NSG). A más régiókban található tároláshoz való kapcsolódáshoz kimenő kapcsolat szükséges. Vegye figyelembe, hogy amikor egy adott régióban található virtuális gépről csatlakozik a tárolóhoz, a tároló diagnosztikai naplóiban található forrás IP-cím egy belső szolgáltatói cím lesz, nem pedig a virtuális gép nyilvános IP-címe. Ha szeretné korlátozni a Storage-fiókhoz való hozzáférést egy vagy több Virtual Network alhálózatban ugyanabban a régióban, akkor a Storage-fiók tűzfalának konfigurálásakor használja [Virtual Network szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) , és ne a nyilvános IP-címét. A szolgáltatási végpontok konfigurálása után a rendszer a tároló diagnosztikai naplóiban fogja látni a Virtual Network magánhálózati IP-címét, nem pedig a belső szolgáltató címét.

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Azure Load Balancer kimenő szabályok

Azure Load Balancer kimenő kapcsolatot biztosít egy virtuális hálózattól a bejövő adatok mellett.  A kimenő szabályok egyszerűvé teszik a nyilvános [standard Load Balancer](load-balancer-standard-overview.md)kimenő hálózati címfordításának konfigurálását.  A kimenő kapcsolat teljes deklaratív vezérlése lehetővé teszi az adott igényeknek megfelelő méretezést és finomhangolást.

![Load Balancer kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok használata esetén a Load Balancer a következőket teheti: 
- adja meg a kimenő NAT-t a semmiből.
- a meglévő kimenő NAT működésének skálázása és finomhangolása. 

A kimenő szabályok segítségével a következőket vezérelheti:
- mely virtuális gépeket kell lefordítani a nyilvános IP-címekre. 
- a [kimenő SNAT portok](load-balancer-outbound-connections.md#snat) kiosztása.
- a kimenő fordítását biztosító protokollok.
- a kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.
- meg kell-e küldeni a TCP alaphelyzetbe állítását üresjárati időkorláton

A kimenő szabályok kibővítik a [2. forgatókönyvet](load-balancer-outbound-connections.md#lb) a [Kimenő kapcsolatok](load-balancer-outbound-connections.md) című cikkben leírtak szerint, és a forgatókönyv elsőbbsége változatlan marad.

### <a name="outbound-rule"></a>Kimenő szabály

Az összes Load Balancer-szabályhoz hasonlóan a kimenő szabályok ugyanazt a szintaxist követik, mint a terheléselosztás és a bejövő NAT-szabályok:

előtér- **felület**  +  **Paraméterek**  +  **háttér-készlet**

Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  A és a _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

Az "2018-07-01" API-verzió lehetővé teszi, hogy a kimenő szabályok definíciója a következőképpen legyen strukturálva:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>A tényleges kimenő NAT-konfiguráció az összes kimenő szabály és a terheléselosztási szabályok összetett változata. A kimenő szabályok növekményes terheléselosztási szabályok. Tekintse át a [kimenő NAT letiltását egy terheléselosztási szabályhoz](#disablesnat) a kimenő NAT tényleges fordításának kezeléséhez, ha több szabály vonatkozik egy virtuális gépre. A [kimenő SNAT le kell tiltania](#disablesnat) egy olyan Kimenő szabály definiálásakor, amely ugyanazt a nyilvános IP-címet használja terheléselosztási szabályként.

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Kimenő NAT méretezése több IP-címmel

Míg egy kimenő szabály csak egyetlen nyilvános IP-címmel használható, a kimenő szabályok megkönnyítik a kimenő NAT skálázásának konfigurációs terhelését. Több IP-címet is használhat a nagy léptékű forgatókönyvek tervezéséhez, és a kimenő szabályok használatával csökkentheti a [SNAT kimerülésének](troubleshoot-outbound-connection.md#snatexhaust) hajlamos mintázatát.  

A frontend által biztosított minden további IP-cím 64 000 ideiglenes portot biztosít Load Balancer számára, SNAT-portként való használatra. Míg a terheléselosztás vagy a bejövő NAT-szabályok egyetlen előtérrel rendelkeznek, a Kimenő szabály kibővíti a előtér-fogalmat, és lehetővé teszi a több előtér-szabály használatát.  A több előtérbeli felülettel rendelkező SNAT a rendelkezésre álló portok mennyisége megszorozva minden nyilvános IP-címmel, és a nagyméretű forgatókönyvek is támogatottak.

Emellett a [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül is használhatja egy kimenő szabállyal.  A nyilvános IP-előtag használatával egyszerűbben méretezhető és egyszerűsített, az Azure-környezetből származó folyamatokat tartalmazó lista is elérhető. A Load Balancer erőforráson belül konfigurálhat egy előtérbeli IP-konfigurációt, amely közvetlenül a nyilvános IP-cím előtagra hivatkozik.  Ez lehetővé teszi a nyilvános IP-előtag Load Balancer kizárólagos felügyeletét, és a kimenő kapcsolatok esetében a rendszer automatikusan a nyilvános IP-előtagon belül található összes nyilvános IP-címet fogja használni a kimenő kapcsolatokhoz.  A nyilvános IP-előtag tartományán belüli IP-címek mindegyike IP-címenként 64 000 ideiglenes portot biztosít Load Balancer SNAT-portként való használatra.   

Ha ezt a beállítást használja, nem rendelkezhet a nyilvános IP-előtagból létrehozott egyedi nyilvános IP-cím-erőforrásokkal, mert a kimenő szabálynak teljes mértékben ellenőriznie kell a nyilvános IP-előtagot.  Ha részletesebb szabályozásra van szüksége, hozzon létre egyedi nyilvános IP-cím-erőforrást a nyilvános IP-címről, és rendeljen hozzá több nyilvános IP-címet egy kimenő szabály előtérbeli felületéhez.

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>SNAT-portok kiosztásának hangolása

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet mérete alapján](load-balancer-outbound-connections.md#preallocatedports) , és lefoglalhatja az automatikus SNAT-portok kiosztását.

A következő paraméter használatával 10 000 SNAT-portot foglalhat le virtuális gépenként (hálózati adapter IP-konfigurációja).
 

          "allocatedOutboundPorts": 10000

A kimenő szabályok összes felületének minden nyilvános IP-címe akár 64 000 ideiglenes portot is felhasználhat SNAT-portokként való használatra.  Load Balancer SNAT-portokat foglal le a 8 többszörösében. Ha a 8 értékkel nem osztható értéket ad meg, a rendszer elutasítja a konfigurációs műveletet.  Ha a nyilvános IP-címek száma alapján több SNAT-portot próbál kiosztani, akkor a rendszer elutasítja a konfigurációs műveletet.  Ha például egy virtuális gépenként 10 000 portot foglal le, és a háttérbeli készlet 7 virtuális gépe egyetlen nyilvános IP-címet oszt meg, akkor a rendszer elutasítja a konfigurációt (7 x 10 000 SNAT-port > 64 000 SNAT-portok).  A forgatókönyv engedélyezéséhez több nyilvános IP-címet is hozzáadhat a Kimenő szabály előtérbeli felületéhez.

Az [automatikus SNAT-portok kiosztására a háttérbeli készlet mérete alapján](load-balancer-outbound-connections.md#preallocatedports) , a portok számának 0 értékének megadásával térhet vissza. Ebben az esetben az első 50-es virtuálisgép-példány 1024 portot kap, a 51-100-as virtuálisgép-példányok pedig 512-et kapnak, így a táblázat szerint.

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>A kimenő forgalom üresjárati időkorlátjának szabályozása

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időkorlátjának szabályozására, és az alkalmazás igényeinek megfelelően illeszkednek.  A kimenő üresjárati időtúllépések alapértelmezett értéke 4 perc.  A paraméter 4 és 120 közötti értéket fogad el az adott szabálynak megfelelő folyamatok üresjárati időkorlátja számára.

A következő paraméterrel állíthatja be a kimenő üresjárati időkorlátot 1 órára:

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>A TCP alaphelyzetbe állításának engedélyezése üresjárati időkorláton

Load Balancer alapértelmezett viselkedése a folyamat csendes eldobása a kimenő Üresjárati időkorlát elérésekor.  A enableTCPReset paraméterrel előre jelezhető alkalmazások viselkedését engedélyezheti, és szabályozhatja, hogy a kimenő Üresjárati időkorlát időtúllépése esetén a kétirányú TCP-visszaállítást (TCP-t) kell-e küldeni. 

A következő paraméter használatával engedélyezheti a TCP-visszaállítást egy kimenő szabályon:

           "enableTcpReset": true

Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](https://aka.ms/lbtcpreset) , beleértve a régió rendelkezésre állását.

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>A TCP-és UDP-átviteli protokollok támogatása egyetlen szabállyal

Valószínű, hogy az "összes" lehetőséget szeretné használni a Kimenő szabály átviteli protokollján, de a kimenő szabályt egy adott átviteli protokollra is alkalmazhatja, valamint ha erre van szükség.

A TCP és az UDP protokoll beállításához használja a következő paramétert:

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Kimenő NAT letiltása terheléselosztási szabályhoz

Ahogy korábban már említettük, a terheléselosztási szabályok biztosítják a kimenő NAT automatikus programozását. Bizonyos esetekben azonban a terheléselosztási szabály segítségével letilthatja a kimenő NAT automatikus programozását, hogy lehetővé tegye a viselkedés szabályozását vagy pontosítását.  A kimenő szabályok olyan forgatókönyvekkel rendelkeznek, amelyek fontosak az automatikus kimenő NAT-programozás leállításához.

Ezt a paramétert kétféleképpen is használhatja:
- A kimenő NAT bejövő IP-címének használata nem kötelező.  A kimenő szabályok növekményes terheléselosztási szabályokkal és ezzel a paraméterrel állíthatók be, a Kimenő szabály a vezérlőben van.
  
- A bejövő és kimenő IP-címek kimenő NAT-paramétereinek hangolása párhuzamosan.  Az automatikus kimenő NAT-programozást le kell tiltani ahhoz, hogy a kimenő szabályok szabályozni tudják a vezérlést.  Ha például meg szeretné változtatni egy SNAT-port lefoglalását is, akkor ezt a paramétert igaz értékre kell állítani.  Ha egy kimenő szabályt próbál használni a bejövő IP-cím paramétereinek újradefiniálásához, és nem adta meg a terheléselosztási szabály kimenő NAT-programozását, akkor a kimenő szabályok konfigurálásának művelete sikertelen lesz.

>[!IMPORTANT]
> A virtuális gép nem rendelkezik kimenő kapcsolattal, ha a paraméter igaz értékre van állítva, és nem rendelkezik kimenő (vagy [példány-szintű nyilvános IP](load-balancer-outbound-connections.md#ilpip) -címmel) a kimenő kapcsolat definiálásához.  A virtuális gép vagy az alkalmazás néhány művelete attól függ, hogy a kimenő kapcsolat elérhető-e. Győződjön meg arról, hogy tisztában van a forgatókönyv függőségeivel, és befolyásolta ennek a változásnak a hatását.

A kimenő SNAT a terheléselosztási szabályban a következő konfigurációs paraméterrel tilthatja le:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

A új választható disableoutboundsnat paraméter értéke false (hamis), ami azt jelenti, hogy a terheléselosztási szabály a terheléselosztási szabály konfigurációjának tükörképe **biztosítja az** automatikus kimenő NAT-t.  

Ha a terheléselosztási szabályban a új választható disableoutboundsnat igaz értékre állítja, a terheléselosztási szabály az egyébként automatikusan kimenő NAT-programozás vezérlését is felszabadítja.  A terheléselosztási szabály eredményeképpen a kimenő SNAT le van tiltva.

#### <a name="reuse-existing-or-define-new-backend-pools"></a>Meglévő újbóli felhasználása vagy új háttérbeli készletek definiálása

A kimenő szabályok nem vezetnek be új fogalmat azon virtuális gépek csoportjának definiálásához, amelyekre a szabály vonatkozik.  Ehelyett a rendszer újrahasznosítja a háttér-készlet fogalmát, amely a terheléselosztási szabályokhoz is használatos. Ezzel leegyszerűsítheti a konfigurációt egy meglévő háttérbeli készlet definíciójának újbóli használatával, vagy egy konkrét Kimenő szabály létrehozásával.

### <a name="scenarios"></a>Forgatókönyvek

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>A kifelé irányuló kimenő kapcsolatok egy adott nyilvános IP-címhez

A kifelé irányuló kimenő kapcsolatok esetében egy kimenő szabályt is használhat, hogy a rendszer egy adott nyilvános IP-címről hozzon létre, hogy megkönnyítse az engedélyezési forgatókönyvek használatát.  Ez a forrás nyilvános IP-cím ugyanaz lehet, mint a terheléselosztási szabály által használt terheléselosztási szabályok vagy a nyilvános IP-címek különböző készletei.  

1. [Nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) létrehozása (vagy nyilvános IP-címek nyilvános IP-előtagból)
2. Nyilvános Standard Load Balancer létrehozása
3. Hozza létre a használni kívánt nyilvános IP-előtagra (vagy nyilvános IP-címekre) hivatkozó előtérbeli felületet
4. Felhasználhatja a háttér-készletet, vagy létrehozhat egy háttér-készletet, és elhelyezheti a virtuális gépeket a nyilvános Load Balancer háttérbeli készletéből.
5. Egy kimenő szabály konfigurálása a nyilvános Load Balancer a virtuális gépek kimenő NAT-ának programjának kiprogramozásához a frontendek használatával
   
Ha nem szeretné, hogy a terheléselosztási szabályt a kimenő forgalomhoz használja, [le kell tiltania a kimenő SNAT](#disablesnat) a terheléselosztási szabályban.

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>SNAT-port foglalásának módosítása

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet méretétől függően](load-balancer-outbound-connections.md#preallocatedports).

Ha például két virtuális gép egyetlen nyilvános IP-címet oszt ki a kimenő NAT-hoz, akkor érdemes lehet megnövelni az alapértelmezett 1024-as portokból lefoglalt SNAT-portok számát, ha SNAT-kimerülést tapasztal. Minden nyilvános IP-cím legfeljebb 64 000 ideiglenes portot tud elősegíteni.  Ha egyetlen nyilvános IP-címmel rendelkező kimenő szabályt konfigurál, a háttér-készletben lévő virtuális gépekhez összesen 64 000 SNAT-portot oszthat ki.  Két virtuális gép esetében legfeljebb 32 000 SNAT-portot lehet lefoglalni egy kimenő szabállyal (2x 32 000 = 64 000).

Tekintse át a [kimenő kapcsolatokat](load-balancer-outbound-connections.md) és a [SNAT](load-balancer-outbound-connections.md#snat) portok kiosztásának és felhasználásának részleteit.

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Csak kimenő engedélyezése

Egy nyilvános standard Load Balancer használatával biztosíthatja a kimenő NAT-t a virtuális gépek csoportjai számára. Ebben a forgatókönyvben saját maga is használhat kimenő szabályt anélkül, hogy további szabályokat kellene megadnia.

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>Kimenő NAT csak virtuális gépekhez (nincs bejövő)

Definiáljon egy nyilvános standard Load Balancer, helyezze a virtuális gépeket a háttér-készletbe, és állítson be egy kimenő szabályt a kimenő NAT programba, és a kimenő kapcsolatok a megadott nyilvános IP-címről származnak. A nyilvános IP-előtagot is használhatja, amely leegyszerűsíti a kimenő kapcsolatok forrásának listázását.

1. Hozzon létre egy nyilvános standard Load Balancer.
2. Hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer háttér-készletéből.
3. Konfiguráljon egy kimenő szabályt a nyilvános Load Balanceron a kimenő NAT programhoz a virtuális gépek számára.

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Kimenő NAT belső standard Load Balancer-forgatókönyvekhez

Belső standard Load Balancer használata esetén a kimenő NAT nem érhető el, amíg explicit módon be nem jelenti a kimenő kapcsolatot. A kimenő kapcsolatokat egy kimenő szabállyal definiálhatja a belső standard Load Balancer mögötti virtuális gépek kimenő kapcsolatának létrehozásához a következő lépésekkel:

1. Hozzon létre egy nyilvános standard Load Balancer.
2. Hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer háttér-készletéből a belső Load Balancer mellett.
3. Konfiguráljon egy kimenő szabályt a nyilvános Load Balanceron a kimenő NAT programhoz a virtuális gépek számára.

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>TCP-& UDP-protokollok engedélyezése a kimenő NAT nyilvános standard Load Balancer

- Nyilvános standard Load Balancer használata esetén a megadott automatikus kimenő NAT-programozás megfelel a terheléselosztási szabály átviteli protokolljának.  

   1. Tiltsa le a kimenő SNAT a terheléselosztási szabályban.
   2. Konfiguráljon egy kimenő szabályt ugyanarra a Load Balancer.
   3. Használja újra a virtuális gépek által már használt háttér-készletet.
   4. A Kimenő szabály részeként a "protokoll": "all" érték adható meg.

- Ha csak a bejövő NAT-szabályok vannak használatban, a rendszer nem biztosít kimenő NAT-szabályokat.

   1. Helyezze a virtuális gépeket egy háttérbeli készletbe.
   2. Adjon meg egy vagy több előtér-IP-konfigurációt nyilvános IP-címmel vagy nyilvános IP-előtaggal.
   3. Konfiguráljon egy kimenő szabályt ugyanarra a Load Balancer.
   4. A "protokoll": "all" érték meghatározása a Kimenő szabály részeként


## <a name="limitations"></a>Korlátozások
- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- A Load Balancer nem támogatja az ICMP protokollt a kimenő NAT-hoz.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Több hálózati adapter is támogatott.
- A VNet és más Microsoft-platformokat nem tartalmazó webes feldolgozói szerepkörök csak akkor érhetők el, ha csak belső standard Load Balancer van használatban, mivel a VNet szolgáltatások és egyéb platform-szolgáltatások funkciójának mellékhatása. Ne támaszkodjon erre a mellékhatásra, mert maga a saját szolgáltatás, vagy az alapul szolgáló platform értesítés nélkül változhat. Mindig feltételezni kell, hogy a kimenő kapcsolatot explicit módon kell létrehoznia, ha csak belső standard Load Balancer használata esetén szükséges. A cikkben ismertetett [alapértelmezett SNAT](#defaultsnat) -forgatókönyv 3. esete nem érhető el.

## <a name="next-steps"></a>További lépések

- További információ a [standard Load Balancerról](load-balancer-standard-overview.md).
- További információ a szabványos nyilvános Load Balancer [kimenő szabályairól](load-balancer-outbound-rules-overview.md) .
- További információ a [Load Balancerról](load-balancer-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
- Ismerje meg az Azure egyéb fontos [hálózati funkcióit](../networking/networking-overview.md) .
