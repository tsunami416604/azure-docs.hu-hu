---
title: Kimenő kapcsolatok az Azure-ban
titleSuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogy az Azure hogyan teszi lehetővé a virtuális gépek számára a nyilvános internetes szolgáltatásokkal való kommunikációt.
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
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393107"
---
# <a name="outbound-connections-in-azure"></a>Kimenő kapcsolatok az Azure-ban

Az Azure kimenő kapcsolatot biztosít az ügyfelek üzembe helyezéséhez számos különböző mechanizmuson keresztül. Ez a cikk ismerteti, hogy mik a forgatókönyvek, mikor alkalmazzák, hogyan működnek, és hogyan kell kezelni őket.

>[!NOTE] 
>Ez a cikk csak az Erőforrás-kezelő központi telepítéseit ismerteti. Tekintse át [a kimenő kapcsolatokat (klasszikus)](load-balancer-outbound-connections-classic.md) az Azure összes klasszikus üzembe helyezési forgatókönyvéhez.

Az Azure-ban egy üzembe helyezés kommunikálhat az Azure-on kívüli végpontokkal a nyilvános IP-címtérben. Amikor egy példány kimenő folyamatot kezdeményez a nyilvános IP-címtérben lévő célhoz, az Azure dinamikusan leképezi a privát IP-címet egy nyilvános IP-címre. A leképezés létrehozása után a kimenő származó folyamat visszáruforgalma is elérheti azt a magán-IP-címet, ahonnan a folyamat származik.

Az Azure a függvény végrehajtásához a forráshálózati címfordítást (SNAT) használja. Ha több privát IP-cím van álarcoskodik mögött egyetlen nyilvános IP-cím, az Azure [használ portcím fordítás (PAT)](#pat) a magánjellegű IP-címek álcázása. Az efemer portok a PAT-hez használatosak, és [előre lefoglaltak](#preallocatedports) a készlet mérete alapján.

Több [kimenő forgatókönyv létezik.](#scenarios) Ezeket a forgatókönyveket szükség szerint kombinálhatja. Tekintse át őket figyelmesen, hogy megismerjék a képességek, korlátozások és minták, ahogy azok a telepítési modell és az alkalmazás forgatókönyv. Tekintse át a [forgatókönyvek kezelésére vonatkozó útmutatást.](#snatexhaust)

>[!IMPORTANT] 
>A standard terheléselosztó és a szabványos nyilvános IP új képességeket és különböző viselkedéseket vezet be a kimenő kapcsolatokhoz.  Nem egyeznek meg az alapszintű termékkészletekkel.  Ha azt szeretné, kimenő kapcsolat, amikor a standard skus-ok, explicit módon meg kell határoznia azt vagy a standard nyilvános IP-címek vagy standard nyilvános terheléselosztó.  Ez magában foglalja a kimenő kapcsolat létrehozása, ha egy belső standard terheléselosztó használata.  Azt javasoljuk, hogy mindig használja a kimenő szabályokat a standard nyilvános terheléselosztó.  [3. forgatókönyv](#defaultsnat) nem érhető el a standard termékváltozat.  Ez azt jelenti, hogy ha egy belső standard terheléselosztót használ, lépéseket kell tennie a háttérkészletben lévő virtuális gépek kimenő kapcsolatának létrehozásához, ha kimenő kapcsolatra van szükség.  A kimenő kapcsolat, egyetlen önálló virtuális gép, az összes virtuális gép egy rendelkezésre állási csoportban, a VMSS-ben a vMSS-ben egy csoportként viselkednek. Ez azt jelenti, ha egy rendelkezésre állási csoport egyetlen virtuális gép egy szabványos termékváltozat társított egy szabványos termékváltozat, a rendelkezésre állási csoporton belüli összes virtuálisgép-példányok most antól ugyanazokat a szabályokat, mintha a standard termékváltozattársított, még akkor is, ha egy adott példány nincs közvetlenül társítva. Ez a viselkedés is megfigyelhető abban az esetben, ha egy önálló virtuális gép több hálózati csatoló kártyák csatolt terheléselosztó. Ha egy hálózati adapter t önálló, akkor ugyanaz a viselkedés. Gondosan tekintse át ezt a teljes dokumentumot, hogy megértse az általános fogalmakat, tekintse át a [Standard Load Balancer-t](load-balancer-standard-overview.md) a termékkódok közötti különbségekről, és tekintse át a [kimenő szabályokat.](load-balancer-outbound-rules-overview.md)  A kimenő szabályok használatával részletesen szabályozhatja a kimenő kapcsolat minden aspektusát.

## <a name="scenario-overview"></a><a name="scenarios"></a>Forgatókönyv áttekintése

Az Azure Load Balancer és a kapcsolódó erőforrások explicit módon vannak definiálva az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)használatakor.  Az Azure jelenleg három különböző módszert biztosít az Azure Resource Manager-erőforrások kimenő kapcsolatának eléréséhez. 

| Termékváltozatok | Forgatókönyv | Módszer | IP protokollok | Leírás |
| --- | --- | --- | --- | --- |
| Standard, Alapszintű | [1. Virtuális gép példányszintű nyilvános IP-címmel (terheléselosztóval vagy anélkül)](#ilpip) | SNAT, port álcázás nem használt | TCP, UDP, ICMP, ESP | Az Azure a példány hálózati adapterének IP-konfigurációjához rendelt nyilvános IP-címet használja. A példány rendelkezik az összes ideiglenes port áll rendelkezésre. A standard terheléselosztó használataesetén [a kimenő szabályok](load-balancer-outbound-rules-overview.md) nem támogatottak, ha nyilvános IP-cím van rendelve a virtuális géphez. |
| Standard, Alapszintű | [2. A virtuális géphez társított nyilvános terheléselosztó (nincs nyilvános IP-cím a példányon)](#lb) | SNAT portálcázással (PAT) a terheléselosztó előtétjeivel | TCP, UDP |Az Azure megosztja a nyilvános terheléselosztó előhívóinak nyilvános IP-címét több privát IP-címmel. Az Azure a PAT előtér-portjainak ideiglenes portjait használja. A [kimenő szabályok at](load-balancer-outbound-rules-overview.md) explicit módon definiálja a kimenő kapcsolat. |
| nincs vagy Alap | [3. Önálló virtuális gép (nincs terheléselosztó, nincs nyilvános IP-cím)](#defaultsnat) | SNAT portálcázással (PAT) | TCP, UDP | Az Azure automatikusan kijelöl egy nyilvános IP-címet az SNAT-hoz, megosztja ezt a nyilvános IP-címet a rendelkezésre állási csoport több privát IP-címével, és a nyilvános IP-cím ideiglenes portjait használja. Ez a forgatókönyv az előző forgatókönyvek tartalék. Nem javasoljuk, ha láthatóságra és vezérlésre van szüksége. |

Ha nem szeretné, hogy egy virtuális gép kommunikáljon az Azure-on kívüli végpontokkal nyilvános IP-címtérben, használhatja a hálózati biztonsági csoportok (NSG-k) a hozzáférés letiltásához szükség szerint. A [kimenő kapcsolatok megelőzése](#preventoutbound) szakasz részletesebben ismerteti az NSG-ket. A virtuális hálózat külső hozzáférés nélküli tervezésére, megvalósítására és kezelésére vonatkozó útmutatás nem tartozik a jelen cikk hatálya alá.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>1. forgatókönyv: Nyilvános IP-címmel rendelkező virtuális gép

Ebben a forgatókönyvben a virtuális gép rendelkezik egy nyilvános IP-cím hez rendelt. Ami a kimenő kapcsolatok at illeti, nem számít, hogy a virtuális gép terheléselosztásos vagy sem. Ez a forgatókönyv elsőbbséget élvez a többivel szemben. Nyilvános IP-cím használatközben a virtuális gép a nyilvános IP-címet használja az összes kimenő folyamathoz.  

A virtuális géphez rendelt nyilvános IP-cím 1:1 kapcsolat (nem pedig 1: sok), és állapotnélküli 1:1 NAT-ként valósítva meg.  Port maskading (PAT) nem használható, és a virtuális gép rendelkezik az összes ideiglenes port használható.

Ha az alkalmazás sok kimenő folyamatot kezdeményez, és az SNAT-port kimerülését tapasztalja, fontolja meg egy nyilvános IP-cím hozzárendelését [az SNAT-megkötések csökkentése érdekében.](#assignilpip) Felülvizsgálat [kezelése SNAT kimerültség](#snatexhaust) teljes egészében.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>2. forgatókönyv: Terheléskiegyensúlyozott virtuális gép nyilvános IP-cím nélkül

Ebben a forgatókönyvben a virtuális gép egy nyilvános terheléselosztó háttérkészlet része. A virtuális gép nem rendelkezik nyilvános IP-címmel hozzárendelve. A terheléselosztó erőforrást terheléselosztó szabállyal kell konfigurálni, hogy kapcsolatot hozzon létre a nyilvános IP-előtér és a háttérkészlet között.

Ha nem fejezi be ezt a szabálykonfigurációt, a viselkedés a [nyilvános IP-cím nélküli önálló virtuális gép](#defaultsnat)forgatókönyvében leírtak szerint történik. Nem szükséges, hogy a szabály egy működő figyelő a háttérkészletben az állapotminta sikeres.

Amikor a terheléselosztásos virtuális gép kimenő folyamatot hoz létre, az Azure a kimenő folyamat magánforrásIP-címét a nyilvános terheléselosztó előtér nyilvános IP-címére fordítja le. Az Azure a SNAT segítségével hajtja végre ezt a funkciót. Az Azure [is](#pat) pat segítségével álcázza több privát IP-címek mögött egy nyilvános IP-címet. 

A terheléselosztó nyilvános IP-címelőrendszerének ideiglenes portjai a virtuális gép által származó egyes folyamatok megkülönböztetésére szolgálnak. Az SNAT dinamikusan használja [az előre lefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. Ebben az összefüggésben az SNAT-hoz használt ideiglenes portokat SNAT-portnak nevezzük.

Az SNAT-portok előre levannak foglalva az [SNAT és a PAT ismertetése](#snat) című szakaszban leírtak szerint. Ők egy véges erőforrás, hogy lehet kimeríteni. Fontos megérteni, hogyan [fogyasztják](#pat)őket. Ha meg szeretné érteni, hogyan tervezhető meg erre a fogyasztásra, és szükség esetén mérsékelheti a rendszert, tekintse át [a SNAT-kimerültség kezelése](#snatexhaust).

Ha [több nyilvános IP-cím van társítva a Terheléselosztó alapszintű,](load-balancer-multivip-overview.md)ezek közül bármelyik nyilvános IP-címek jelölt a kimenő folyamatok, és egy véletlenszerűen kiválasztott.  

A terheléselosztó alapszintű kimenő kapcsolatok állapotának figyeléséhez használhatja az [Azure Monitor naplók terheléselosztó](load-balancer-monitor-log.md) és [riasztási eseménynaplók](load-balancer-monitor-log.md#alert-event-log) az SNAT-port kimerültségi üzenetek figyeléséhez.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>3. forgatókönyv: Önálló virtuális gép nyilvános IP-cím nélkül

Ebben a forgatókönyvben a virtuális gép nem része egy nyilvános terheléselosztó készlet (és nem része egy belső standard terheléselosztó készlet), és nem rendelkezik nyilvános IP-cím hozzá. Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure a kimenő folyamat privát forrás IP-címét egy nyilvános forrás IP-címére fordítja le. A kimenő folyamathoz használt nyilvános IP-cím nem konfigurálható, és nem számít bele az előfizetés nyilvános IP-erőforrás-korlátba. Ez a nyilvános IP-cím nem az Ön tulajdona, és nem foglalható le. Ha újratelepíti a virtuális gép vagy a rendelkezésre állási csoport vagy a virtuális gép méretezési készlet, ez a nyilvános IP-cím lesz felszabadítva, és egy új nyilvános IP-címet kér. Ne használja ezt a forgatókönyvet az IP-címek engedélyezési listázására. Ehelyett használja a másik két forgatókönyv, ahol explicit módon deklarálja a kimenő forgatókönyv és a nyilvános IP-címet kell használni a kimenő kapcsolat.

>[!IMPORTANT] 
>Ez a forgatókönyv akkor is érvényes, ha __csak__ egy belső alapszintű terheléselosztó van csatolva. 3. forgatókönyv __nem érhető el,__ ha egy belső standard terheléselosztó van csatolva egy virtuális géphez.  A belső standard terheléselosztó használata mellett explicit módon létre kell [hoznia](#lb) az [1.](#ilpip)

Az Azure a snat-t portos maskading[(PAT)](#pat)használatával hajtja végre a funkció valameddig. Ez a forgatókönyv hasonló a [2.](#lb) Ez egy tartalék forgatókönyv, ha az 1. Nem javasoljuk ezt a forgatókönyvet, ha azt szeretné, hogy a kimenő cím felett. Ha a kimenő kapcsolatok az alkalmazás kritikus részét képezik, válasszon egy másik forgatókönyvet.

Az SNAT-portok előre ki vannak osztva az [SNAT és a PAT ismertetése](#snat) című szakaszban leírtak szerint.  Az elérhetőségi csoportban lévő virtuális gépek száma határozza meg, hogy melyik előfoglalási szint vonatkozik.  Egy önálló virtuális gép rendelkezésre állási csoport nélkül gyakorlatilag egy készlet 1 az előfoglalás meghatározása (1024 SNAT-portok) meghatározásához. Az SNAT-portok olyan véges erőforrások, amelyek kimerülhetnek. Fontos megérteni, hogyan [fogyasztják](#pat)őket. Ha meg szeretné érteni, hogyan tervezhető meg erre a fogyasztásra, és szükség esetén mérsékelheti a rendszert, tekintse át [a SNAT-kimerültség kezelése](#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Több, kombinált forgatókönyv

Az előző szakaszokban leírt forgatókönyveket egy adott eredmény eléréséhez kombinálhatja. Ha több forgatókönyv is van jelen, egy fontossági sorrend lép [scenario 2](#lb) fel: [3](#defaultsnat) [az 1.](#ilpip) [2. forgatókönyv](#lb) felülbírálja [a 3.](#defaultsnat)

Egy példa egy Azure Resource Manager-telepítés, ahol az alkalmazás nagymértékben támaszkodik a kimenő kapcsolatok korlátozott számú cél, hanem bejövő folyamatokat fogad egy terheléselosztó előtér. Ebben az esetben kombinálhatja az 1- es és a 2-es forgatókönyveket a megkönnyebbüléshez. További minták, tekintse [kezelése SNAT kimerültség](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Több előtér-vonal a kimenő folyamatokhoz

#### <a name="standard-load-balancer"></a>Standard terheléselosztó

A standard terheléselosztó az összes jelöltet egyszerre használja a kimenő folyamatokhoz, amikor [több (nyilvános) IP-előtét van](load-balancer-multivip-overview.md) jelen. Minden előtér megszorozza a rendelkezésre álló előre lefoglalt SNAT-portok számát, ha a kimenő kapcsolatokhoz engedélyezve van egy terheléselosztási szabály.

Letilthatja, hogy egy előtér-IP-cím ne használható-e kimenő kapcsolatokhoz egy új terheléselosztási szabállyal:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Általában a `disableOutboundSnat` beállítás alapértelmezés szerint _hamis,_ és azt jelenti, hogy ez a szabály programok kimenő SNAT a társított virtuális gépek a háttérkészlet a terheléselosztási szabály. A `disableOutboundSnat` lehet változtatni, hogy _igaz,_ hogy megakadályozzák a terheléselosztó a kapcsolódó előtér-IP-cím a kimenő kapcsolatok a virtuális gépek a háttérkészlet a terheléselosztási szabály.  És továbbra is kijelölhet egy adott IP-címet a kimenő folyamatokhoz [a több, kombinált forgatókönyvben leírtak](#combinations) szerint is.

#### <a name="load-balancer-basic"></a>Terheléselosztó alapszintű

A Load Balancer Basic egyetlen előtér-folyamatot választ a kimenő folyamatokhoz, ha [több (nyilvános) IP-előtér](load-balancer-multivip-overview.md) jelölt a kimenő folyamatokhoz. Ez a beállítás nem konfigurálható, és a kiválasztási algoritmust véletlenszerűnek kell tekinteni. A kimenő folyamatokhoz egy adott IP-címet is kijelölhet a [több, kombinált forgatókönyvben leírtak szerint.](#combinations)

### <a name="availability-zones"></a><a name="az"></a>Elérhetőségi zónák

Ha [a Standard Load Balancer-t rendelkezésre állási zónákkal](load-balancer-standard-availability-zones.md)használja, a zónaredundáns előhívók zónaredundáns kimenő SNAT-kapcsolatokat biztosíthatnak, és az SNAT-programozás túléli a zónahibát.  Zónaszintű előtér-rendszerek használatközben a kimenő SNAT-kapcsolatok megosztják a sorsot azzal a zónával, amelyhez tartoznak.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Az SNAT és a PAT ismertetése

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>SNAT -bemasquerading (PAT) port

Ha egy nyilvános terheléselosztó erőforrás virtuálisgép-példányokkal van társítva, minden kimenő kapcsolatforrás újraírása. A forrás átírása a virtuális hálózat privát IP-címterület a frontend nyilvános IP-címét a terheléselosztó. A nyilvános IP-címtérben a folyamat 5-hangnak (forrás IP-cím, forrásport, IP-átviteli protokoll, cél IP-cím, célport) egyedinek kell lennie.  A Port masquerading SNAT tcp vagy UDP IP protokollokkal használható.

Az ideiglenes portok (SNAT-portok) ezt a magánforrás IP-címének átírása után használják, mivel több folyamat egyetlen nyilvános IP-címről származik. A port álcázó SNAT algoritmus lefoglalja SNAT portok eltérő UDP versus TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT-portok

Egy SNAT-port ot használ fel egy folyamat egyetlen cél IP-címhez, porthoz. Több TCP-folyamat esetén ugyanazon a cél IP-címén, portján és protokollján minden TCP-folyamat egyetlen SNAT-portot használ fel. Ez biztosítja, hogy a folyamatok egyediek legyenek, ha ugyanabból a nyilvános IP-címből származnak, és ugyanarra a cél IP-címre, portra és protokollra kerülnek. 

Több folyamat, mindegyik egy másik cél IP-cím, port és protokoll, osztozik egy SNAT-port. A cél IP-cím, port és protokoll egyedivé teszi a folyamatokat anélkül, hogy további forrásportokra lenne szükség a nyilvános IP-címtérben lévő folyamatok megkülönböztetéséhez.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT-portok

Az UDP SNAT-portokat a TCP SNAT-portoktól eltérő algoritmus kezeli.  A terheléselosztó az UDP-hez "portra korlátozott kúpos nat" néven ismert algoritmust használ.  Minden folyamathoz egy SNAT-port kerül felhasználásra, függetlenül a cél IP-címétől, a porttól.

#### <a name="snat-port-reuse"></a>SNAT-port újrafelhasználása

A port felszabadítása után a port szükség szerint újra felhasználható.  Az SNAT-portok az adott forgatókönyvhöz elérhető legalacsonyabbtól a legmagasabbig, és az első elérhető SNAT-port ot használják az új kapcsolatokhoz. 
 
#### <a name="exhaustion"></a>Kimerültség

Ha az SNAT-port erőforrásai kimerültek, a kimenő folyamatok mindaddig sikertelenek lesznek, amíg a meglévő folyamatok fel nem oldják az SNAT-portokat. A terheléselosztó visszaszerzi az SNAT-portokat, amikor a folyamat bezárul, és [4 perces tétlen időtúlot](#idletimeout) használ az SNAT-portok visszaszerzésére az írási folyamatokból.

Az UDP SNAT-portok általában sokkal gyorsabban kimerítik a TCP SNAT-portokat a használt algoritmusbeli különbségek miatt. Ezt a különbséget szem előtt tartva kell megterveznie és skáláznia a tesztet.

Az SNAT-port okának kimerüléséhez gyakran vezető feltételek csökkentésére vonatkozó mintákról tekintse át az [SNAT kezelése](#snatexhaust) című szakaszt.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Efemer port előfoglalása az SNAT-nak álcázó porthoz (PAT)

Az Azure egy algoritmussegítségével határozza meg az előre lefoglalt SNAT-portok számát a háttérkészlet mérete alapján, amikor portálarcos SNAT ([PAT)](#pat)használatával használja. Az SNAT-portok egy adott nyilvános IP-címhez elérhető ideiglenes portok.

Ugyanennyi SNAT-port van előre lefoglalva az UDP, illetve a TCP számára, és ip-átviteli protokollonként egymástól függetlenül fogyaszthatók.  Az SNAT-port használata azonban eltérő attól függően, hogy a folyamat UDP vagy TCP.

>[!IMPORTANT]
>A szabványos SKU SNAT-programozás IP-átviteli protokollonkénti, a terheléselosztási szabályból származik.  Ha csak egy TCP terheléselosztási szabály létezik, az SNAT csak a TCP-hez érhető el. Ha csak egy TCP terheléselosztási szabályt, és szüksége van kimenő SNAT UDP, hozzon létre egy UDP terheléselosztási szabályt ugyanabból az előtér-készletugyanabból a háttérkészletből.  Ez elindítja az UDP SNAT programozását.  Nem szükséges működő szabály vagy állapotminta.  Az alapvető SKU SNAT mindig mindkét IP-átviteli protokollhoz programozza az SNAT-ot, függetlenül a terheléselosztási szabályban megadott átviteli protokolltól.

Az Azure előre lefoglalja az SNAT-portokat az egyes virtuális gépek hálózati adapterének IP-konfigurációjához. Ha ip-konfigurációt ad hozzá a készlethez, az SNAT-portok a háttérkészlet mérete alapján előre le vannak foglalva ehhez az IP-konfigurációhoz. Kimenő folyamatok létrehozásakor [a PAT](#pat) dinamikusan fogyaszt (az előre lefoglalt korlátig), és felszabadítja ezeket a portokat, amikor a folyamat bezárul, vagy [tétlen időtúllépést](#idletimeout) végez.

Az alábbi táblázat a háttérkészlet-méretek rétegeinek SNAT-port-előfoglalásait mutatja be:

| Készlet mérete (VM-példányok) | Előre lefoglalt SNAT-portok IP-konfigurációnként|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Ha [több előtér-számlálót](load-balancer-multivip-overview.md)használ, minden előtér-IP-cím megszorozza az előző táblázatban elérhető SNAT-portok számát. Például egy 50 virtuális gépből álló háttérkészlet 2 terheléselosztási szabályokkal, amelyek mindegyike külön előtér-IP-címmel rendelkezik, szabályonként 2048 (2x 1024) SNAT-portot fog használni. Több [előtér-témakör](#multife)részleteinek megtekintése.

Ne feledje, hogy a rendelkezésre álló SNAT-portok száma nem fordítja közvetlenül a folyamatok számát. Egyetlen SNAT-port több egyedi célhoz is felhasználható. A portok csak akkor kerülnek felhasználásra, ha a folyamatok egyedivé állításához szükséges. A tervezési és kockázatcsökkentési útmutatásért tekintse meg a [kimeríthető erőforrás kezeléséről](#snatexhaust) szóló szakaszt, valamint a [PAT-ot](#pat)ismerő szakaszt.

A háttérkészlet méretének módosítása hatással lehet a létrehozott folyamatok egy részére. Ha a háttérkészlet mérete nő, és átvált a következő rétegbe, az előre lefoglalt SNAT-portok fele visszanyerésre kerül a következő nagyobb háttérkészlet-szintre való áttérés során. A visszanyert SNAT-porthoz társított folyamatok időout-ot kapnak, és újra létre kell hozni őket. Ha új folyamatot kísérel meg, a folyamat azonnal sikeres lesz, amíg előre lefoglalt portok rendelkezésre állnak.

Ha a háttérkészlet mérete csökken, és alacsonyabb szintre vált, a rendelkezésre álló SNAT-portok száma nő. Ebben az esetben a meglévő lefoglalt SNAT-portokat és azok folyamatait ez nem érinti.

Az SNAT-portok kiosztása IP-átviteli protokoll-specifikus (a TCP és az UDP külön-külön karbanmarad), és a következő feltételek mellett szabadul fel:

### <a name="tcp-snat-port-release"></a>TCP SNAT-port kiadása

- Ha bármelyik kiszolgáló/ügyfél FINACK-ot küld, az SNAT-port 240 másodperc elteltével felszabadul.
- Ha rst látható, az SNAT-port 15 másodperc elteltével feloldódik.
- Ha az indle időtúllépés elérte, a port fel szabadul.

### <a name="udp-snat-port-release"></a>UDP SNAT-port kiadása

- Ha az indle időtúllépés elérte, a port fel szabadul.

## <a name="problem-solving"></a><a name="problemsolving"></a>Problémamegoldás 

Ez a szakasz az SNAT-kimerültség csökkentésére szolgál, és amely az Azure-beli kimenő kapcsolatok esetén fordulhat elő.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Az SNAT (PAT) port kimerülésének kezelése
A [PAT-hez](#pat) használt [ideiglenes portok](#preallocatedports) kimeríthető erőforrások, ahogy azt az [önálló virtuális gép nyilvános IP-cím és](#defaultsnat) nyilvános [IP-cím nélküli, kiegyensúlyozott terhelésű virtuális gép](#lb)írja le. Figyelemmel kísérheti a rövid élettartamú portok használatát, és összehasonlíthatja az aktuális allokációval az SNAT kilégzés kockázatának meghatározásához vagy az SNAT exhuastion megerősítéséhez [ezzel](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) az útmutatóval.

Ha tudja, hogy számos kimenő TCP- vagy UDP-kapcsolatot kezdeményez ugyanarra a cél IP-címre és portra, és a sikertelen kimenő kapcsolatokat észleli, vagy a támogatás azt tanácsolja, hogy kimeríti az SNAT-portokat (a [PAT](#pat)által használt, előre lefoglalt [ideiglenes portokat),](#preallocatedports) számos általános kockázatcsökkentési lehetőséggel rendelkezik. Tekintse át ezeket a lehetőségeket, és döntse el, hogy mi érhető el, és mi a legjobb a forgatókönyvnek. Lehetséges, hogy egy vagy több segíthet kezelni ezt a forgatókönyvet.

Ha nem érti a kimenő kapcsolat viselkedését, használhatja az IP-verem statisztikáit (netstat). Vagy hasznos lehet a kapcsolat viselkedésének megfigyelése csomagrögzítéssel. Ezeket a csomagrögzítéseket a példány vendég operációs rendszerében hajthatja végre, vagy használhatja [a Network Watcher-t a csomagrögzítéshez.](../network-watcher/network-watcher-packet-capture-manage-portal.md) 

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Az alkalmazás módosítása a kapcsolatok újrafelhasználásához 
Az SNAT-hoz használt ideiglenes portok iránti igény csökkenthető az alkalmazás kapcsolatainak újbóli felhasználásával. Ez különösen igaz az olyan protokollokra, mint a HTTP/1.1, ahol a kapcsolat újrafelhasználása az alapértelmezett. És más protokollok, amelyek http-t használnak átvitelként (például REST) előnyösek lehetnek. 

Az újrafelhasználás mindig jobb, mint az egyes, atomi TCP-kapcsolatok minden kéréshez. Az újrafelhasználás hatékonyabb, nagyon hatékony TCP-tranzakciókat eredményez.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Az alkalmazás módosítása kapcsolatkészletezés használatára
A kapcsolatkészletezési sémát alkalmazhatja az alkalmazásban, ahol a kérelmek belsőleg vannak elosztva a kapcsolatok rögzített készletében (ahol lehetséges, mindegyik újrafelhasználása). Ez a séma korlátozza a használatban lévő ideiglenes portok számát, és kiszámíthatóbb környezetet hoz létre. Ez a séma növelheti a kérelmek átviteli mertét is, ha több egyidejű műveletet engedélyez, ha egyetlen kapcsolat blokkolja a művelet válaszát.  

Előfordulhat, hogy a kapcsolatkészletezés már létezik az alkalmazás vagy az alkalmazás konfigurációs beállításainak fejlesztéséhez használt keretrendszeren belül. A kapcsolatkészletezést kombinálhatja a kapcsolat újrafelhasználásával. A több kérelem ezután rögzített, kiszámítható számú portot használ fel ugyanarra a cél IP-címre és portra. A kérelmek is részesülnek a tcp-tranzakciók hatékony használatából, csökkentve a késést és az erőforrás-kihasználtságot. UdP-tranzakciók is előnyös, mert az UDP-folyamatok számának kezelése viszont elkerülheti a kipufogógáz-feltételeket, és kezelheti az SNAT-port kihasználtságát.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára
Ha a [PAT-hez](#pat) használt [előre lefoglalt rövid élettartamú portok](#preallocatedports) kimerültek, vagy alkalmazáshibák fordulnak elő, agresszív vagy találgatásos újrapróbálkozások bomlás nélkül és a visszalépési logika kimerültséget okoz, vagy megmarad. Kevésbé agresszív újrapróbálkozási logikával csökkentheti az ideiglenes portok iránti igényt. 

Az efemer portok 4 perces tétlen időtúlértékgel rendelkeznek (nem állítható). Ha az újrapróbálkozások túl agresszívek, a kimerültségnek nincs lehetősége arra, hogy magától tisztázza magát. Ezért figyelembe véve, hogyan - és milyen gyakran - az alkalmazás újrapróbálkozik tranzakciók kritikus része a tervezés.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Nyilvános IP hozzárendelése minden virtuális géphez
Nyilvános IP-cím hozzárendelése a [forgatókönyvet nyilvános IP-címre módosítja egy virtuális gépre.](#ilpip) A nyilvános IP-cím minden ideiglenes portja, amely az egyes virtuális gépekhez használatos, elérhető a virtuális gép számára. (Ellentétben olyan forgatókönyvekkel, ahol a nyilvános IP-cím ideiglenes portjai meg vannak osztva a megfelelő háttérkészlethez társított összes virtuális géptel.) Vannak olyan kompromisszumok, amelyeket figyelembe kell venni, például a nyilvános IP-címek többletköltsége és a nagyszámú egyedi IP-cím engedélyezési listájának lehetséges hatása.

>[!NOTE] 
>Ez a beállítás nem érhető el a webes feldolgozói szerepkörökhöz.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Több előtér használata

Nyilvános standard terheléselosztó használataesetén [több előtér-IP-címet](#multife) kell hozzárendelni e-mail kapcsolatokhoz, és [meg kell szoroznia a rendelkezésre álló SNAT-portok számát.](#preallocatedports)  Hozzon létre egy előtér-IP-konfigurációt, szabályt és háttérkészletet az SNAT programozásának az előtér nyilvános IP-címére történő aktiválásához.  A szabálynak nem kell működnie, és az állapotmintának nem kell sikeresnek lennie.  Ha több előtér-programot is használ a bejövő (nem csak a kimenő), érdemes egyéni állapotminta jól megbízhatóságának biztosítása érdekében.

>[!NOTE]
>A legtöbb esetben a SNAT-portok kimerülése a rossz kialakítás jele.  Győződjön meg róla, hogy megértette, miért meríti ki a portokat, mielőtt több előtér-portot használna az SNAT-portok hozzáadásához.  Lehet, hogy elfedi a problémát, amely később kudarchoz vezethet.

#### <a name="scale-out"></a><a name="scaleout"></a>Horizontális felskálázás

[Az előre lefoglalt portok](#preallocatedports) a háttérkészlet mérete alapján vannak hozzárendelve, és rétegekbe vannak csoportosítva, hogy minimálisra csökkentsék a fennakadást, amikor egyes portokat újra kell lefoglalni a következő nagyobb háttérkészlet-méretréteg befogadásához.  Előfordulhat, hogy egy adott előtér-készlet sNAT-portkihasználtságának intenzitását növelheti úgy, hogy a háttérkészletet egy adott szint maximális méretére skálázhatja.  Ehhez az alkalmazás hatékony horizontális felskálázása szükséges.

A háttérkészletben lévő két virtuális gép például 1024 SNAT-porttal rendelkezik IP-konfigurációnként, így összesen 2048 SNAT-port ot engedélyez a telepítéshez.  Ha a központi telepítést 50 virtuális gépre kellene növelni, annak ellenére, hogy az előre lefoglalt portok száma virtuális gépenként állandó marad, a központi telepítés összesen 51 200 (50 x 1024) SNAT-portot használhat.  Ha szeretné horizontálisfelskálázni a központi telepítést, ellenőrizze az [előre lefoglalt portok](#preallocatedports) száma rétegenként, hogy megbizonyosodjon arról, hogy a horizontális felskálázás ta-ig az adott szint re.  Az előző példában, ha úgy döntött, hogy 50 példány helyett 51-re skálázódik, akkor a következő szintre lép, és a végén kevesebb SNAT-port ot virtuális gépenként, valamint összesen.

Ha horizontális felskálázás a következő nagyobb háttérkészlet méretréteg, fennáll annak lehetősége, hogy néhány kimenő kapcsolatok időtúltöltés, ha a lefoglalt portokat újra kell foglalni.  Ha csak néhány SNAT-portot használ, a következő nagyobb háttérkészlet-méreten keresztüli horizontális felskálázás lényegtelen.  A meglévő portok fele minden alkalommal újra lelesz foglalva, amikor a következő háttérkészlet-szintre lép.  Ha nem szeretné, hogy ez megtörténik, a központi telepítést a szint méretéhez kell alakítania.  Vagy győződjön meg arról, hogy az alkalmazás képes észlelni, és próbálja meg szükség szerint újra.  A TCP keepalives segíthet észlelni, ha az SNAT-portok az újrafoglalás miatt már nem működnek.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Keepalives használatával alaphelyzetbe állíthatja a kimenő tétlen időoutot

A kimenő kapcsolatok 4 perces tétlen időhatartalmúak. Ez az időmeghosszabbítás a [Kimenő szabályokon](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout)keresztül állítható. Az átvitel (például TCP keepalives) vagy az alkalmazásszintű keepalives használatával frissítheti az alapjárati folyamatot, és szükség esetén alaphelyzetbe állíthatja az alapjárati időhalátot.  

A TCP keepalives használata esetén elegendő a kapcsolat egyik oldalán engedélyezni őket. Elegendő például, ha a kiszolgálóoldalon csak a folyamat alapjárati időzítőjét szeretnék alaphelyzetbe állítani, és nem szükséges, hogy mindkét fél kezdeményezze a TCP keepalives-t.  Hasonló fogalmak léteznek az alkalmazásréteghez, beleértve az adatbázis-ügyfél-kiszolgáló konfigurációkat is.  Ellenőrizze a kiszolgálóoldalon, hogy milyen lehetőségek vannak az alkalmazásspecifikus keepalives-ra.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>A virtuális gép által használt nyilvános IP-cím felfedezése
A kimenő kapcsolatok nyilvános forrásIP-címének számos módja van. OpenDNS nyújt olyan szolgáltatást, amely megmutatja a nyilvános IP-címét a virtuális gép. 

Az nslookup paranccsal dns-lekérdezést küldhet az OpenDNS-feloldónak a myip.opendns.com névre vonatkozóan. A szolgáltatás a lekérdezés elküldéséhez használt forrás IP-címet adja vissza. Amikor a következő lekérdezést futtatja a virtuális gépről, a válasz az adott virtuális géphez használt nyilvános IP-cím:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>A kimenő kapcsolat megakadályozása
Néha nem kívánatos, hogy egy virtuális gép számára lehetővé kell tenni, hogy hozzon létre egy kimenő folyamatot. Vagy lehet, hogy a követelmény, hogy kezelje, mely célok érhető el a kimenő folyamatok, vagy mely célállomások kezdődhetnek bejövő folyamatok. Ebben az esetben [a hálózati biztonsági csoportok](../virtual-network/security-overview.md) segítségével kezelheti a virtuális gép által elérhető célokat. NsG-k segítségével is kezelheti, hogy melyik nyilvános cél kezdeményezheti a bejövő folyamatokat.

Amikor nsg-t alkalmaz egy terheléselosztásos virtuális gépre, figyeljen a [szolgáltatáscímkékre](../virtual-network/security-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra.](../virtual-network/security-overview.md#default-security-rules) Győződjön meg arról, hogy a virtuális gép képes az Azure Load Balancer állapotmintavételi kérelmek fogadására. 

Ha egy NSG blokkolja az állapotminta kérelmeket a AZURE_LOADBALANCER alapértelmezett címke, a virtuális gép állapotminta sikertelen lesz, és a virtuális gép le van jelölve. A terheléselosztó leállítja az új folyamatok küldését a virtuális gépre.

## <a name="limitations"></a>Korlátozások
- Web Feldolgozó szerepkörök virtuális hálózat és más Microsoft platformszolgáltatások nélkül érhető el, ha csak egy belső standard terheléselosztó használata miatt mellékhatása, hogyan pre-VNet szolgáltatások és egyéb platformszolgáltatások működését. Ne hagyatkozzon erre a mellékhatásra, mivel maga az adott szolgáltatás vagy az alapul szolgáló platform értesítés nélkül változhat. Mindig azt kell feltételeznie, hogy explicit módon létre kell hoznia a kimenő kapcsolatot, ha csak belső standard terheléselosztót használ. A cikkben ismertetett [alapértelmezett SNAT-forgatókönyv](#defaultsnat) nem érhető el.

## <a name="next-steps"></a>További lépések

- További információ a [Standard Load Balancer-](load-balancer-standard-overview.md)ről.
- További információ a standard nyilvános terheléselosztó [kimenő szabályairól.](load-balancer-outbound-rules-overview.md)
- További információ a [terheléselosztóról.](load-balancer-overview.md)
- További információ a [hálózati biztonsági csoportokról.](../virtual-network/security-overview.md)
- Ismerje meg az Azure egyéb kulcsfontosságú [hálózati lehetőségeit.](../networking/networking-overview.md)
