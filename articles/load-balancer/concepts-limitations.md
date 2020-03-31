---
title: Összetevők és korlátozások
titleSuffix: Azure Load Balancer
description: Az Azure Load Balancer összetevőinek és korlátainak áttekintése.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2020
ms.author: allensu
ms.openlocfilehash: a94b51e49951948974b8f42f6c89cd3c84f95d65
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064283"
---
# <a name="load-balancer-components-and-limitations"></a>Terheléselosztó alkatrészek és korlátozások
Az Azure Load Balancer számos kulcsfontosságú összetevőt tartalmaz a működéséhez.  Ezek az összetevők konfigurálhatók az előfizetésben az Azure Portalon, az Azure CLI-n vagy az Azure PowerShellen keresztül.  

## <a name="load-balancer-components"></a>Terheléselosztó-összetevők

* **Előtéthálózati IP-konfigurációk**: A terheléselosztó IP-címe. Ez a kapcsolattartási pont az ügyfelek számára. Ezek a címek lehetnek: 

    - **[Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privát IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Háttérkészlet:** A virtuális gépek vagy a virtuálisgép-méretezési csoport azon példányainak csoportja, amelyek a bejövő kérelmet fogják kiszolgálni. A nagy mennyiségű bejövő forgalom teljesítéséhez költséghatékony méretezéshez a számítási irányelvek általában azt javasolják, hogy további példányokat adjanak hozzá a háttérkészlethez. A terheléselosztó azonnal újrakonfigurálja magát az automatikus újrakonfigurálás révén, amikor fel- vagy leskálázja a példányokat. Virtuális gépek hozzáadása vagy eltávolítása a háttérkészletből további műveletek nélkül újrakonfigurálja a terheléselosztót. A háttérkészlet hatóköre a virtuális hálózat bármely virtuális gépe. Egy háttérkészlet legfeljebb 1000 háttérpéldánysal vagy IP-konfigurációval rendelkezhet.
Az alapszintű terheléselosztók korlátozott hatókörrel rendelkeznek (rendelkezésre állási csoport) csak 300 IP-konfigurációig skálázhatók. A korlátokról a [Terheléselosztó-korlátok című](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)témakörben talál további információt. A háttérkészlet tervezésének mérlegelésekor a legkevesebb egyes háttérkészlet-erőforrásokat tervezheti meg a felügyeleti műveletek időtartamának további optimalizálása érdekében. Nincs különbség az adatsík teljesítménye vagy léptéke között.
* **Állapot-mintavételek**: Az **[állapotminta](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** a háttérkészletben lévő példányok állapotának meghatározására szolgál. Megadhatja a nem megfelelő küszöbértéket az állapot-mintavételek. Ha egy mintavételező nem válaszol, a Load Balancer nem küld új kapcsolatokat a nem megfelelő állapotú példányra. A mintavételi hiba nincs hatással a meglévő kapcsolatokra. 
    
    A kapcsolat addig folytatódik, amíg az alkalmazás: 
    - Befejezi az áramlást
    - Tétlen időkiás időkitöltés történik
    - A virtuális gép leáll

    A terheléselosztó különböző állapotmintatípusokat biztosít a végpontokhoz:
    - TCP
    - HTTP
    - HTTPS (HTTP-szonda átviteli rétegbiztonsággal (TLS) burkoló)
     
     Az egyszerű terheléselosztó nem támogatja a HTTPS-mintavételeket. Ezenkívül az alapszintű terheléselosztó leállítja az összes TCP-kapcsolatot (beleértve a létrehozott kapcsolatokat is). 
    További információ: [Probe types](load-balancer-custom-probe-overview.md#types).

* **Terheléselosztási szabályok**: A terheléselosztási szabályok azok, amelyek megmondják a terheléselosztónak, hogy mit kell tenni, amikor. 
* **Bejövő NAT-szabályok**: Egy bejövő NAT-szabály továbbítja a forgalmat egy adott előtér-IP-cím adott portjáról egy adott háttérpéldány adott portjára a virtuális hálózaton belül. **[A porttovábbítást](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** ugyanaz a kivonatalapú elosztás végzi, mint a terheléselosztás. A funkció gyakori forgatókönyvei a Távoli asztali protokoll (RDP) vagy a Secure Shell (SSH) munkamenetek az Azure virtuális hálózaton belüli egyes virtuális géppéldányokhoz. Több belső végpontot is leképezhet ugyanazon az előtér-IP-címen lévő portokhoz. Az előtér-IP-címek segítségével távolról felügyelheti a virtuális gépeket egy további ugródoboz nélkül.
* **Kimenő szabályok:** A **[kimenő szabály](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfigurálja a kimenő hálózati címfordítás (NAT) az összes virtuális gépek vagy példányok által azonosított háttérkészlet a standard terheléselosztó kell lefordítani az előtérre.

  Az alapszintű terheléselosztó nem támogatja a kimenő szabályokat.

  ![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)
* **Átviteli protokollok**: A terheléselosztó nem támogatja az ICMP-t; ICMP pingelések egy nyilvános fekvésű terheléselosztó időtúltöltés. A nyilvános felé néző terheléselosztó pingeléséhez használja a TCP Ping

## <a name="load-balancer-concepts"></a><a name = "load-balancer-concepts"></a>A terheléselosztó fogalmai

A Load Balancer az alábbi alapvető képességeket biztosítja a TCP- és UDP-alkalmazások számára:

* **Terheléselosztási algoritmus:** Az Azure Load Balancer, hozhat létre egy terheléselosztási szabályt az előtér-háttérkészlet-példányok hoz létre forgalmat. A terheléselosztó kivonatoló algoritmust használ a bejövő folyamatok (nem bájtok) elosztásához, és átírja a folyamatok fejléceit háttérkészlet-példányokká. A kiszolgáló új folyamatok fogadására érhető el, ha egy állapotminta kifogástalan háttérvégpontot jelez.
Alapértelmezés szerint a terheléselosztó 5-tüzű kivonatot használ. 

   A kivonat a következőket tartalmazza: 

   - **Forrás IP-címe**
   - **Forrásport**
   - **Cél IP-cím**
   - **Célport**
   - **IP-protokollszám a folyamatok leképezéséhez az elérhető kiszolgálókhoz** 

A forrás IP-címhez affinitást hozhat létre egy adott szabály 2- vagy 3-tappontos kivonathasználatával. Az adott csomagadatfolyam minden csomagja ugyanarra a példányra érkezik az elosztott terhelésű előtér mögött. Amikor az ügyfél új folyamatot indít el ugyanabból a forrás IP-címről, a forrásport megváltozik. Ennek eredményeképpen az 5-tuple kivonat okozhat a forgalom egy másik háttérvégpont.
További információ: [A terjesztési mód konfigurálása az Azure Load Balancer hez.](./load-balancer-distribution-mode.md) 

A következő kép a kivonatalapú elosztást szemlélteti:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Kivonatalapú elosztás">
</p>

  *Ábra: Kivonatalapú elosztás*

* **Alkalmazásfüggetlenség és -áttekintő:** A terheléselosztó nem lép közvetlenül a TCP-vel, az UDP-vel vagy az alkalmazásréteggel. Bármely TCP- vagy UDP-alkalmazási forgatókönyv támogatható. A terheléselosztó nem szünteti meg vagy nem hoz létre folyamatokat, nem lép kölcsönhatásba a folyamat hasznos terhelésével, és nem biztosít alkalmazásréteg-átjáró funkciót. A protokoll-kézfogások mindig közvetlenül az ügyfél és a háttérkészlet-példány között fordulnak elő. Egy beérkező folyamatra mindig egy virtuális géptől érkezik a válasz. Amikor a folyamat megérkezik a virtuális gépre, az eredeti IP-cím is megmarad.
  * Minden végpontnak csak egy virtuális gép felel. Például egy TCP-kézfogás mindig történik az ügyfél és a kiválasztott háttér-virtuális gép között. Az előtér-kérésre adott válasz egy háttérvirtuális gép által generált válasz. Ha sikeresen érvényesíti az előtér-kapcsolatot, legalább egy háttérvirtuális virtuális géphez érvényesíti a végpontok közötti kapcsolatot.
  * Az alkalmazás hasznos terhelései transzparensek a terheléselosztó számára. Bármely UDP- vagy TCP-alkalmazás támogatott.
  * Mivel a terheléselosztó nem lép kölcsönhatásba a TCP-tartalommal, és nem biztosít TLS-kiszervezést, végpontok közötti titkosított forgatókönyveket hozhat létre. A terheléselosztó használatával a TLS-alkalmazások nagy mennyiségben történő bővítését eredményezi a TLS-kapcsolat megszüntetésével a virtuális gépen. A TLS-munkamenet-kulcsolási kapacitást például csak a háttérkészlethez hozzáadott virtuális gépek típusa és száma korlátozza.

* **Kimenő kapcsolatok:** A virtuális hálózaton belüli privát IP-címekről az interneten lévő nyilvános IP-címekre irányuló összes kimenő adatfolyam lefordítható a terheléselosztó előtér-IP-címére. Ha egy nyilvános előtér-rendszer egy terheléselosztási szabály segítségével egy háttér-virtuális géphez van kötve, az Azure lefordítja a kimenő kapcsolatokat a nyilvános előtér-IP-címre. Ennek a konfigurációnak a következő előnyei vannak:
  * A szolgáltatások egyszerű frissítése és vészhelyreállítása, mivel az előtér dinamikusan leképezhető a szolgáltatás egy másik példányára.
  * Könnyebb hozzáférés-vezérlési lista (ACL) kezelése. Az előtér-IP-kben kifejezett AC-k nem változnak, mivel a szolgáltatások felfelé vagy lefelé skáláznak, vagy újraüzembe helyeznek. A kimenő kapcsolatok gépeknél kisebb számú IP-címre történő fordítása csökkenti a biztonságos címzettlisták megvalósításának terhét.

  A Standard Load Balancer [robusztus, skálázható és kiszámítható SNAT algoritmust](load-balancer-outbound-connections.md#snat)használ. Ezek a legfontosabb alapkések, amelyeket a Standard Load Balancer-rel való munka során meg kell emlékezni:

    - a terheléselosztási szabályok arra következtenek, hogy a SNAT hogyan van beprogramozva. A terheléselosztási szabályok protokollspecifikusak. Az SNAT protokoll-specifikus, és a konfigurációnak ezt kell tükröznie, nem pedig mellékhatást létrehoznia.

    - **Több előtér** Ha több előtér-rendszer érhető el, az összes előtér-rendszer használatos, és minden előtér megszorozza a rendelkezésre álló SNAT-portok számát. Ha több SNAT-portot szeretne, mert nagy a kimenő kapcsolatok iránti igény, vagy már nagy az igény a kimenő kapcsolatokra, hozzáadhat növekményes SNAT-portkészletet további előtér-, szabály- és háttérkészletek konfigurálásával ugyanahhoz a virtuális géphez. Erőforrások.

    - **Annak szabályozása, hogy melyik előtér van használatban a kimenő** Választhat, és szabályozhatja, ha nem szeretné, hogy egy adott előtér-t használja a kimenő kapcsolatokhoz. Ha azt szeretné, hogy a kimenő kapcsolatok csak egy adott előtér-IP-címről származnak, tetszés szerint letilthatja a kimenő SNAT-t a kimenő leképezést kifejező szabályon.

    - **A kimenő kapcsolat** kimenő forgatókönyvei explicitek, és a kimenő kapcsolat nem létezik, amíg meg nem adta. A standard terheléselosztó a virtuális hálózat környezetében létezik.  A virtuális hálózat egy elszigetelt, magánhálózat.  Ha létezik nyilvános IP-címmel rendelkező társítás, a nyilvános kapcsolat nem engedélyezett.  A [virtuális hálózati szolgáltatás végpontjait](../virtual-network/virtual-network-service-endpoints-overview.md) azért érheti el, mert azok a virtuális hálózaton belül és a helyi hálózatokon belül vannak.  Ha kimenő kapcsolatot szeretne létesíteni a virtuális hálózaton kívüli célhoz, két lehetősége van:
        - szabványos termékváltozat nyilvános IP-címét példányszintű nyilvános IP-címként rendelje hozzá a virtuálisgép-erőforráshoz, vagy
        - helyezze a virtuálisgép-erőforrást egy nyilvános standard terheléselosztó háttérkészletébe.

        Mindkettő lehetővé teszi a kimenő kapcsolatot a virtuális hálózatról a virtuális hálózaton kívülre. 

        Ha _csak_ egy belső standard terheléselosztó van társítva ahhoz a háttérkészlethez, amelyben a virtuálisgép-erőforrás található, a virtuális gép csak a virtuális hálózati erőforrásokat és a [virtuális hálózati szolgáltatás végpontjait](../virtual-network/virtual-network-service-endpoints-overview.md)érheti el.  Az előző bekezdésben ismertetett lépéseket követve kimenő kapcsolatot hozhat létre.

        A standard skus-okkal nem társított virtuálisgép-erőforrások kimenő kapcsolata a korábban is megmarad.

        Tekintse át [a Kimenő kapcsolatok részletes ismertetését.](load-balancer-outbound-connections.md)

* **Rendelkezésre állási zónák:** A standard terheléselosztó további képességeket támogat azokban a régiókban, ahol rendelkezésre állási zónák érhetők el. Ezek a funkciók növekményesek az összes standard terheléselosztó számára.  A rendelkezésre állási zónák konfigurációi mindkét típushoz elérhetők, nyilvános és belső standard terheléselosztóhoz.
 A zónaredundáns előtér túléli a zónahibát, és az összes zónában egyidejűleg dedikált infrastruktúra szolgálja ki. 
Emellett garantálhatja, hogy egy adott zónához előtér áll. A zónaszintű előtér megosztja a sorsot az adott zónával, és csak egyetlen zónában lévő dedikált infrastruktúra szolgálja ki.
A zónaközi terheléselosztás elérhető a háttérkészlethez, és a virtuális hálózat ban lévő virtuális gépi erőforrások egy háttérkészlet részét kaphatják.
Az egyszerű terheléselosztó nem támogatja a zónákat.
További információkért tekintse át [a rendelkezésre állási zónákkal kapcsolatos képességek](load-balancer-standard-availability-zones.md) és a [rendelkezésre állási zónák áttekintésének](../availability-zones/az-overview.md) részletes ismertetése.

* **HA portok:** Beállíthatja a terheléselosztási szabályokat, hogy az alkalmazás méretezése és rendkívül megbízható legyen. Ha HA portterhelés-elosztási szabályt használ, a standard terheléselosztó áramlási terheléselosztást biztosít a belső standard terheléselosztó előtér IP-címének minden ideiglenes portján.  A szolgáltatás más olyan esetekben hasznos, ahol nem célszerű vagy nem kívánatos az egyes portok megadása. A HA portok terheléselosztási szabály lehetővé teszi, hogy aktív-passzív vagy aktív-aktív n+1 forgatókönyvek hálózati virtuális készülékek és minden olyan alkalmazás, amely megköveteli a nagy számú bejövő portok.  Az állapotminta segítségével meghatározható, hogy mely háttérrendszereknek kell új folyamatokat fogadniuk.  A hálózati biztonsági csoport segítségével emulálhatja a porttartomány-forgatókönyvet. Az egyszerű terheléselosztó nem támogatja a HA portokat.
A [HA-portok részletes megbeszélésének áttekintése](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Ha hálózati virtuális berendezés használatát tervezi, érdeklődjön a forgalmazónál, hogy a terméküket tesztelték-e a HA-portokkal, és kövesse a megvalósításra vonatkozó konkrét útmutatást. 

* **Több előtér:** A terheléselosztó több, több előtér-rendszerű szabályt támogat.  A Standard Load Balancer ezt kibővíti a kimenő forgatókönyvekre.  A kimenő forgatókönyvek lényegében egy bejövő terheléselosztási szabály inverzét képezik.  A bejövő terheléselosztási szabály is létrehoz egy társítást a kimenő kapcsolatokhoz. A standard terheléselosztó a virtuálisgép-erőforráshoz társított összes előhívót használja egy terheléselosztási szabályon keresztül.  Emellett a terheléselosztási szabály egy paraméterét, és lehetővé teszi, hogy letiltsa a terheléselosztási szabályt a kimenő kapcsolat céljából, amely lehetővé teszi az adott előtér-rendszerek kiválasztását, beleértve a nincs is.

Összehasonlításképpen az alapszintű terheléselosztó véletlenszerűen kiválaszt egyetlen előtétet, és nem lehet szabályozni, hogy melyik et jelölte ki.
## <a name="load-balancer-types"></a>Terheléselosztó típusok

### <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Nyilvános Load Balancer

A nyilvános terheléselosztó leképezi a nyilvános IP-címet és a bejövő forgalom portját a virtuális gép privát IP-címére és portjára. A terheléselosztó a virtuális gép válaszforgalmának másik fordítva leképezi a forgalmat. Adott típusú forgalmat több virtuális gép vagy szolgáltatás között terjesztheti terheléselosztási szabályok alkalmazásával. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.

>[!NOTE]
>Rendelkezésre állási csoportonként csak egy nyilvános terheléselosztót és egy belső terheléselosztót valósíthat meg.

Az alábbi ábrán egy terheléselosztásos végpont webes forgalom, amely megosztott a három virtuális gépek a nyilvános és a TCP-port 80. Ez a három virtuális gép egy elosztott terhelésű készlet részeit képezi.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Nyilvános terheléselosztó">
</p>

*Ábra: A webes forgalom kiegyensúlyozása nyilvános terheléselosztó használatával*

Az internetes ügyfelek weblapkérelmeket küldenek egy webalkalmazás nyilvános IP-címére a 80-as TCP-porton. Az Azure Load Balancer a kérelmeket a három virtuális gép között osztja el a terheléselosztási készletben. A terheléselosztó algoritmusokról a [Terheléselosztó fogalmai](concepts-limitations.md#load-balancer-concepts)című témakörben talál további információt.

Az Azure Load Balancer alapértelmezés szerint egyenlően osztja el a hálózati forgalmat több virtuálisgép-példány között. A munkamenet-affinitást is beállíthatja. További információ: [A terjesztési mód konfigurálása az Azure Load Balancer hez.](load-balancer-distribution-mode.md)

### <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a> Belső Load Balancer

A belső terheléselosztó csak olyan erőforrásokra irányítja a forgalmat, amelyek egy virtuális hálózaton belül vannak, vagy amelyek VPN-t használnak az Azure-infrastruktúra eléréséhez, ellentétben a nyilvános terheléselosztóval. Az Azure-infrastruktúra korlátozza a hozzáférést a terheléselosztásos előtér-IP-címek egy virtuális hálózat. Az előtér-IP-címek és a virtuális hálózatok soha nem vannak közvetlenül kitéve egy internetes végpontnak. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el.

Egy belső Load Balancer a következő típusú terheléselosztásokat teszi lehetővé:

* **Virtuális hálózaton belül:** Terheléselosztás a virtuális hálózat virtuális gépeitől az azonos virtuális hálózatban lévő virtuális gépek készletébe.
* **A létesítmények közötti virtuális hálózat:** Terheléselosztás a helyszíni számítógépek egy sor virtuális gépek, amelyek ugyanabban a virtuális hálózatban.
* **Többrétegű alkalmazások**esetén: Terheléselosztás olyan internetes többrétegű alkalmazások esetén, ahol a háttérszintek nem internetre néznek. A háttérszintek hez az internetre néző szint forgalomterhelés-elosztása szükséges. Lásd a következő ábrát.
* **Üzletági alkalmazásoknál**: Terheléselosztás üzletági alkalmazásokhoz, amelyek további terheléselosztó hardverek vagy szoftverek nélkül üzemelnek az Azure-ban. Ez a forgatókönyv olyan helyszíni kiszolgálókat tartalmaz, amelyek olyan számítógépek készletében vannak, amelyek forgalma kiegyensúlyozatlan.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Nyilvános terheléselosztó">
</p>

*Ábra: A többrétegű alkalmazások kiegyensúlyozása nyilvános és belső terheléselosztó használatával*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Load Balancer-termékváltozatok összehasonlítása

A terheléselosztó támogatja az alapszintű és a standard termékkészletet is. Ezek a sin-ek különböznek a forgatókönyv-skálán, a funkciókban és az árakban. Az alapszintű terheléselosztóval lehetséges forgatókönyvek a standard terheléselosztóval hozhatók létre. Mindkét termékváltozat API-ja hasonló, és egy termékváltozat specifikációja által hívható. A terheléselosztó és a nyilvános IP-cím támogatásához szükséges `2017-08-01` API-k az API-val kezdve érhetők el. Mindkét SVILLA ugyanazt az általános API-t és struktúrát osztja.

A teljes forgatókönyv konfigurációja némileg eltérhet a termékváltozattól függően. A terheléselosztó dokumentációja akkor kiált, ha egy cikk csak egy adott termékváltozatra vonatkozik. A különbségek összehasonlításához és megértéséhez lásd az alábbi táblázatot. További információ: [Azure Standard Load Balancer overview](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft standard terheléselosztót ajánl.
Az önálló virtuális gépeket, a rendelkezésre állási csoportokat és a Virtual Machine Scale-készleteket csak egy termékváltozathoz csatlakoztathatja, sosem kettőhöz egyszerre. A terheléselosztónak és a nyilvános IP-cím termékváltozatának meg kell egyeznie, ha nyilvános IP-címekkel használja őket. A terheléselosztó és a nyilvános IP-sus-ok nem módosíthatók.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

További információ: [Load balancer limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). A Standard Load Balancerről további részleteket az [áttekintés](load-balancer-standard-overview.md), a [díjszabás](https://aka.ms/lbpricing) és az [SLA](https://aka.ms/lbsla) szakaszban talál.

## <a name="limitations"></a><a name = "limitations"></a>Korlátozások

- A ska-k nem módosíthatók. Előfordulhat, hogy nem módosítja egy meglévő erőforrás termékváltozatát.
- Egy önálló virtuálisgép-erőforrás, rendelkezésre állási készlet erőforrás vagy a virtuálisgép méretezési készlet erőforrás hivatkozhat egy termékváltozat, soha nem mindkettő.
- A terheléselosztó szabály nem terjedhet ki két virtuális hálózatra.  Az előtér-rendszereknek és a kapcsolódó háttérpéldányoknak ugyanabban a virtuális hálózatban kell lenniük.  
- [Az előfizetési műveletek áthelyezése](../azure-resource-manager/management/move-resource-group-and-subscription.md) nem támogatott a standard LB és nyilvános IP-erőforrások esetében.
- Web Feldolgozó szerepkörök virtuális hálózat nélkül, és más Microsoft platformszolgáltatások érhetők el a példányok mögött csak egy belső standard terheléselosztó. Nem támaszkodhat erre, mivel maga az adott szolgáltatás vagy az alapul szolgáló platform értesítés nélkül változhat. Mindig azt kell feltételeznie, hogy explicit módon létre kell [hoznia a kimenő kapcsolatot,](load-balancer-outbound-connections.md) ha csak belső standard terheléselosztót használ.

- A terheléselosztó terheléselosztást és porttovábbítást biztosít adott TCP- vagy UDP-protokollokhoz. A terheléselosztási szabályok és a bejövő NAT-szabályok támogatják a TCP- és UDP protokollt, de más IP-protokollokat, beleértve az ICMP protokollt sem.

  A terheléselosztó nem szakítja meg, nem válaszol, és nem lép kölcsönhatásba az UDP- vagy TCP-folyamat hasznos terhelésével. Ez nem egy helyettes. Az előtér-kapcsolat sikeres érvényesítésének sávon ként kell zajlania, ugyanazzal a protokollal, amelyet a terheléselosztásvagy a bejövő NAT-szabály használ. Legalább egy virtuális gépnek választ kell generálnia az ügyfél számára, hogy az előtérből érkező választ láthassa.

  Ha nem kap sávon kénti választ a terheléselosztó előtér-kezelője azt jelzi, hogy egyetlen virtuális gép sem tudott válaszolni. Semmi sem kommunikálhat a terheléselosztó előtérrel anélkül, hogy egy virtuális gép képes lenne válaszolni. Ez az elv azokra a kimenő kapcsolatokra is vonatkozik, ahol a portálarcos SNAT csak a TCP és az UDP esetén támogatott. Minden más IP-protokoll, beleértve az ICMP protokollt is, sikertelen. A probléma enyhítése érdekében rendeljen hozzá egy példányszintű nyilvános IP-címet. További [információ: SNAT és PAT ismertetése.](load-balancer-outbound-connections.md#snat)

- A belső terheléselosztók nem fordítják le a kimenő kapcsolatokat a belső terheléselosztó előelejére, mivel mindkettő privát IP-címtérben található. A nyilvános terheléselosztók [kimenő kapcsolatokat](load-balancer-outbound-connections.md) biztosítanak a virtuális hálózaton belüli privát IP-címekről a nyilvános IP-címekre. A belső terheléselosztók esetében ez a megközelítés elkerüli a potenciális SNAT-port kimerülését egy egyedi belső IP-címterületen belül, ahol nincs szükség fordításra.

  A mellékhatása az, hogy ha egy kimenő folyamat egy virtuális gép a háttér-készlet megkísérli a folyamat ot a belső terheléselosztó a készletben, _és_ le van képezve vissza a saját, a két lába a folyamat nem egyezik. Mivel nem egyeznek, az áramlás nem sikerül. A folyamat akkor sikerül, ha a folyamat nem felel meg ugyanahhoz a virtuális géphez a háttérkészletben, amely létrehozta a folyamatot az előtérhez.

  Amikor a folyamat leképezi vissza magát, a kimenő folyamat úgy tűnik, hogy származik a virtuális gép az előtér és a megfelelő bejövő folyamat úgy tűnik, hogy származik a virtuális gép önmagára. A vendég operációs rendszer szempontjából az azonos folyamat bejövő és kimenő részei nem egyeznek meg a virtuális gépen belül. A TCP-verem nem ismeri fel az azonos folyamat feleit, mivel ugyanannak a folyamatnak a része. A forrás és a cél nem egyezik. Amikor a folyamat leképezi bármely más virtuális gép a háttér-készlet, a folyamat felei egyeznek, és a virtuális gép reagálhat a folyamatra.

  A jelenség ebben a forgatókönyvben időszakos kapcsolat időtúllépés, amikor a folyamat visszatér ugyanahhoz a háttérrendszerhez, amely a folyamat származik. A gyakori megoldások közé tartozik egy proxyréteg beillesztése a belső terheléselosztó mögé, és a Direct Server Return (DSR) stílusszabályok használata. További információ: [Több előtér-vége az Azure Load Balancer.](load-balancer-multivip-overview.md)

  A belső terheléselosztót bármely külső proxyval kombinálhatja, vagy belső [application gateway-t](../application-gateway/application-gateway-introduction.md) használhat a HTTP/HTTPS-alapú proxyforgatókönyvekhez. Bár a probléma enyhítésére nyilvános terheléselosztót használhat, az eredményül kapott forgatókönyv hajlamos az [SNAT-kimerültségre.](load-balancer-outbound-connections.md#snat) Kerülje el ezt a második megközelítést, ha nem gondosan kezelik.

- Általában az IP-töredékek továbbítása nem támogatott a terheléselosztási szabályok. Az UDP- és TCP-csomagok IP-töredezettsége nem támogatott a terheléselosztási szabályokban. A magas rendelkezésre állású portok terheléselosztási szabályai meglévő IP-töredékek továbbítására használhatók. További információt a [Magas rendelkezésre állású portok – áttekintés című témakörben talál.](load-balancer-ha-ports-overview.md)

## <a name="next-steps"></a>További lépések

- Lásd: [Nyilvános standard terheléselosztó létrehozása](quickstart-load-balancer-standard-public-portal.md) a terheléselosztó használatának megkezdéséhez: hozzon létre egyet, hozzon létre virtuális gépeket egy telepített egyéni IIS-bővítményt, és töltse fel a webalkalmazást a virtuális gépek között.
- További információ az [Azure Load Balancer](load-balancer-overview.md)szolgáltatásról.
- További információ a [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md)használatáról.
- További információ [az egészségügyi szondákról.](load-balancer-custom-probe-overview.md)
- További információ a [standard terheléselosztó diagnosztikáról.](load-balancer-standard-diagnostics.md)
- További információ a terheléselosztó használatáról [a kimenő kapcsolatokhoz.](load-balancer-outbound-connections.md)
- További információ [a kimenő szabályokról.](load-balancer-outbound-rules-overview.md)
- További információ a [TCP alaphelyzetbe állításáról az idle-n.](load-balancer-tcp-reset.md)
- Ismerje meg [a Standard Load Balancer ha portterhelés-elosztási szabályokkal.](load-balancer-ha-ports-overview.md)
- További információ a [több előtér-elosztó val történő](load-balancer-multivip-overview.md)használatáról.
- További információ a [hálózati biztonsági csoportokról.](../virtual-network/security-overview.md)
