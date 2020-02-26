---
title: Összetevők és korlátozások
titleSuffix: Azure Load Balancer
description: Azure Load Balancer összetevők és korlátozások áttekintése.
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
ms.openlocfilehash: aab6a4de7be57df1f691861533a4528a0bcae571
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605686"
---
# <a name="load-balancer-components-and-limitations"></a>Összetevők és korlátozások Load Balancer
Azure Load Balancer számos kulcsfontosságú összetevőt tartalmaz a működéséhez.  Ezek az összetevők az előfizetésben a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával konfigurálhatók.  

## <a name="load-balancer-components"></a>Összetevők Load Balancer

* Előtér **-IP-konfigurációk**: a terheléselosztó IP-címe. Ez a kapcsolódási pont az ügyfelek számára. Ezek a címek a következők lehetnek: 

    - **[Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Magánhálózati IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Háttér-készlet**: a virtuálisgép-méretezési csoportban lévő virtuális gépek vagy példányok csoportja, amelyek a bejövő kérelmet fogják kiszolgálni. Ahhoz, hogy a költséghatékonyan megfeleljen a nagy mennyiségű bejövő forgalomnak, a számítástechnikai irányelvek általában azt ajánlják, hogy további példányokat vegyen fel a háttér-készletbe. Load Balancer azonnal újrakonfigurálja magát az automatikus újrakonfigurálással, ha feljebb vagy lejjebb méretezi a példányokat. Ha virtuális gépeket ad hozzá vagy távolít el a háttér-készletből, a Load Balancer további műveletek nélkül konfigurálja újra. A háttér-készlet hatóköre a virtuális hálózatban található bármely virtuális gép. Egy háttér-készlet legfeljebb 1000 háttér-példánnyal vagy IP-konfigurációval rendelkezhet.
Az alapszintű terheléselosztó korlátozott hatókörrel rendelkezik (rendelkezésre állási csoport), amely legfeljebb 300 IP-konfigurációt képes felskálázásra. További információ a korlátozásokról: [Load Balancer korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). A háttérbeli készlet kialakításának megtervezése során a felügyeleti műveletek időtartamának további optimalizálása érdekében megtervezheti a minimális számú egyéni háttér-készlet erőforrását. Nem különbözik az adatsíkok teljesítményének vagy méretezésének.
* **Állapot**-mintavételek: a háttérbeli készletben lévő példányok állapotának meghatározásához a rendszer **[állapot](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** -mintavételt használ. Megadhatja az állapot-mintavételek nem megfelelő állapotát. Ha egy mintavételező nem válaszol, a Load Balancer nem küld új kapcsolatokat a nem megfelelő állapotú példányra. A mintavételi hiba nem érinti A meglévő kapcsolatokat. 
    
    A kapcsolatok addig folytatódnak, amíg az alkalmazás: 
    - A folyamat vége
    - Tétlen időtúllépés történik
    - A virtuális gép leáll

    Load Balancer különböző állapot-mintavételi típusokat biztosít a végpontokhoz:
    - TCP
    - HTTP
    - HTTPS (HTTP-mintavétel Transport Layer Security (TLS) burkolóval)
     
     Az alapszintű Load Balancer nem támogatja a HTTPS-próbákat. Emellett az alapszintű Load Balancer leállítja az összes TCP-kapcsolatot (beleértve a létesített kapcsolatokat is). 
    További információ: mintavételi [típusok](load-balancer-custom-probe-overview.md#types).

* **Terheléselosztási szabályok**: a terheléselosztási szabályok olyanok, amelyek közlik a Load Balancer, hogy mit kell tenni a (z) esetén. 
* **Bejövő NAT-szabályok**: a bejövő NAT-szabály egy adott előtéri IP-cím megadott portjáról továbbítja a forgalmat a virtuális hálózaton belüli adott backend-példány egy adott portjára. A **[portok továbbítása](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** a terheléselosztással megegyező kivonatoló alapú eloszlással történik. Ennek a képességnek a gyakori forgatókönyvei a RDP protokoll (RDP) vagy a Secure Shell-(SSH-) munkamenetek az Azure-Virtual Networkban lévő egyes virtuálisgép-példányokhoz. Több belső végpontot is leképezheti ugyanazon előtér-IP-címen lévő portokra. Az előtérbeli IP-címek használatával távolról is felügyelhet virtuális gépeket további Jump Box nélkül.
* **Kimenő szabályok**: egy **[Kimenő szabály](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfigurálja a kimenő hálózati címfordítást (NAT) minden olyan virtuális géphez vagy példányhoz, amelyet a standard Load Balancer háttér-készlete azonosít a felületre.
Az alapszintű Load Balancer nem támogatja a kimenő szabályokat.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name = "load-balancer-concepts"></a>Load Balancer fogalmak

A Load Balancer az alábbi alapvető képességeket biztosítja a TCP- és UDP-alkalmazások számára:

* **Terheléselosztási algoritmus**: a Azure Load Balancer használatával terheléselosztási szabályt hozhat létre, amellyel terjesztheti a frontenden a háttérrendszer-példányokra érkező forgalmat. A Load Balancer kivonatoló algoritmust használ a bejövő folyamatok (nem bájt) elosztásához, és átírja a folyamatok fejléceit a háttérbeli készlet példányaiba. A kiszolgálók új folyamatok fogadására használhatók, ha az állapot-mintavétel állapota Kifogástalan.
Alapértelmezés szerint a Load Balancer 5 rekordos kivonatot használ. 

   A kivonat a következőket tartalmazza: 

   - **Forrás IP-címe**
   - **Forrásport**
   - **Cél IP-címe**
   - **Célport**
   - **IP-protokoll száma, amellyel a folyamatokat le lehet képezni a rendelkezésre álló kiszolgálókra** 

Egy adott szabályhoz egy 2 vagy 3 rekordos kivonatot használva affinitást hozhat létre a forrás IP-címekhez. Az adott csomagadatfolyam minden csomagja ugyanarra a példányra érkezik az elosztott terhelésű előtér mögött. Amikor az ügyfél egy új folyamatot indít el ugyanabból a forrás IP-címről, a forrásport módosul. Ennek eredményeképpen az 5 rekordos kivonat azt eredményezheti, hogy a forgalom egy másik háttér-végpontra mutat.
További információ: [Azure Load Balancer elosztási módjának konfigurálása](./load-balancer-distribution-mode.md). 

A következő kép a kivonatalapú elosztást szemlélteti:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Kivonatalapú elosztás">
</p>

  *Ábra: Kivonatalapú elosztás*

* Az **alkalmazás függetlensége és átláthatósága**: a Load Balancer nem kommunikál közvetlenül a TCP vagy UDP vagy az alkalmazás rétegével. Bármely TCP-vagy UDP-alkalmazás támogatott lehet. Load Balancer nem szakítja meg vagy nem kezdeményezi a folyamatokat, interakcióba lép a folyamat hasznos adataival, vagy bármilyen Application Layer Gateway-függvényt biztosít. A protokoll-kézfogások mindig közvetlenül az ügyfél és a háttérbeli készlet példánya között történnek. Egy beérkező folyamatra mindig egy virtuális géptől érkezik a válasz. Amikor a folyamat megérkezik a virtuális gépre, az eredeti IP-cím is megmarad.
  * Minden végpontnak csak egy virtuális gép felel. A TCP-kézfogás például mindig az ügyfél és a kijelölt háttérbeli virtuális gép között fordul elő. Az előtérre irányuló kérelemre adott válasz egy háttérbeli virtuális gép által generált válasz. Ha sikeresen érvényesíti a kapcsolatot az előtérrel, a végpontok közötti kapcsolatot ellenőrzi legalább egy háttérbeli virtuális géppel.
  * Az alkalmazás hasznos adatai transzparensek Load Balancer. Bármely UDP-vagy TCP-alkalmazás támogatott lehet.
  * Mivel a Load Balancer nem kommunikál a TCP-adattartalommal, és biztosítja a TLS-kiszervezést, teljes körű, titkosított forgatókönyvek hozhatók létre. A Load Balancer használata nagy kibővíthető TLS-alkalmazások esetén, ha a TLS-kapcsolatokat maga a virtuális gépen befejezi. Például a TLS-munkamenet beírási kapacitását csak a háttérbeli készlethez hozzáadott virtuális gépek típusa és száma korlátozza.

* **Kimenő kapcsolatok**: a virtuális hálózaton belüli magánhálózati IP-címekről az interneten lévő nyilvános IP-címekre irányuló kimenő forgalom a Load Balancer előtér-IP-címére fordítható le. Ha egy nyilvános előtér egy terheléselosztási szabály útján egy háttérbeli virtuális géphez van kötve, az Azure lefordítja a kimenő kapcsolatokat a nyilvános előtérbeli IP-címére. Ennek a konfigurációnak a következő előnyei vannak:
  * A szolgáltatások egyszerű frissítése és vész-helyreállítása, mivel az előtér dinamikusan képezhető le a szolgáltatás egy másik példányára.
  * Egyszerűbb hozzáférés-vezérlési lista (ACL) kezelése. Az előtérbeli IP-címekként kifejezett ACL-ek nem változnak a szolgáltatások vertikális felskálázása vagy újbóli üzembe helyezése esetén. A kimenő kapcsolatok kisebb számú IP-címhez való lefordítása, mint a gépek, csökkenti a biztonságos címzettek listáinak megvalósításának terheit.

  A standard Load Balancer [robusztus, méretezhető és kiszámítható SNAT algoritmust](load-balancer-outbound-connections.md#snat)használ. Ezek a legfontosabb alapelvek a standard Load Balancer használatának megemlékezéséhez:

    - a terheléselosztási szabályok azt a következtetést jelentik, hogy a SNAT hogyan van programozva. A terheléselosztási szabályok a protokoll-specifikusak. A SNAT a protokoll-specifikus, és a konfigurációnak tükröznie kell ezt ahelyett, hogy mellékhatást kellene létrehoznia.

    - **Több frontend** Ha több előtér áll rendelkezésre, a rendszer az összes előtérbeli felületet felhasználja, és az egyes frontendek megszorozzák az elérhető SNAT-portok számát. Ha további SNAT-portokra van szüksége, mert várt vagy már magas keresletet tapasztal a kimenő kapcsolatok esetében, növekményes SNAT is hozzáadhat, ha további előtér-, szabály-és háttér-készleteket is konfigurál ugyanahhoz a virtuális géphez. erőforrások.

    - **A kimenő előtérbeli felület szabályozása** Kiválaszthatja és szabályozhatja, hogy nem kíván-e használni egy adott előtér-felületet a kimenő kapcsolatokhoz. Ha korlátozni szeretné a kimenő kapcsolatokat, hogy csak egy adott előtér-IP-címről származzanak, akkor letilthatja a kimenő SNAT a kimenő leképezést kifejező szabályon.

    - A kimenő kapcsolat kimenő helyzetének **vezérlése** explicit módon történik, és a kimenő kapcsolat nem létezik, amíg meg nem adta a megadott értéket. Standard Load Balancer létezik a virtuális hálózat kontextusában.  A virtuális hálózat egy elkülönített, magánhálózati hálózat.  Ha nem létezik nyilvános IP-címmel rendelkező társítás, a nyilvános kapcsolat nem engedélyezett.  Elérheti a [VNet szolgáltatás-végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) , mivel azok a virtuális hálózatban és a helyi hálózaton belül vannak.  Ha kimenő kapcsolatot szeretne létesíteni a virtuális hálózatán kívüli célhoz, két lehetőség közül választhat:
        - rendeljen hozzá egy standard SKU nyilvános IP-címet a virtuálisgép-erőforráshoz tartozó példány-szintű nyilvános IP-címnek
        - Helyezze a virtuális gép erőforrását egy nyilvános standard Load Balancer háttér-készletéből.

        Mindkettő lehetővé teszi a virtuális hálózat kimenő kapcsolatát a virtuális hálózaton kívülre. 

        Ha _csak_ belső standard Load Balancer van társítva ahhoz a háttér-készlethez, amelyben a virtuális gép erőforrása található, a virtuális gép csak virtuális hálózati erőforrásokat és [VNet-végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)érhet el.  A kimenő kapcsolatok létrehozásához kövesse az előző bekezdésben ismertetett lépéseket.

        A standard SKU-hoz nem társított virtuálisgép-erőforrások kimenő kapcsolata korábban is megmarad.

        Tekintse át [a kimenő kapcsolatok részletes megvitatását](load-balancer-outbound-connections.md).

* **Availability Zones**: standard Load Balancer támogatja a további képességeket azokban a régiókban, ahol elérhetők a Availability Zones. Ezek a funkciók minden standard Load Balancer számára növekményes.  Availability Zones konfigurációk mindkét típushoz, nyilvános és belső standard Load Balancerhoz elérhetők.
 A Zone-redundáns frontend megmarad a zóna meghibásodása esetén, és az összes zónában egyidejűleg dedikált infrastruktúra szolgálja ki. 
Emellett egy adott zónához is garantálhatja a felületet. A zónákhoz tartozó előtér-megosztások a megfelelő zónával rendelkeznek, és csak dedikált infrastruktúra szolgálnak ki egyetlen zónában.
A többzónás terheléselosztás elérhető a háttér-készlethez, és a virtuális hálózatban található bármely virtuálisgép-erőforrás egy háttér-készlet része lehet.
Az alapszintű Load Balancer nem támogatja a zónákat.
További információkért tekintse át [Availability Zones kapcsolódó képességeinek részletes megvitatását](load-balancer-standard-availability-zones.md) és [Availability Zones áttekintését](../availability-zones/az-overview.md) .

* **Ha portok**: beállíthatja a terheléselosztási szabályokat, hogy az alkalmazások méretezése és megbízhatósága megbízható legyen. Ha ha egy ha portok terheléselosztási szabályt használ, a standard Load Balancer a belső standard Load Balancer előtérbeli IP-címének minden ideiglenes portján adatfolyam-terheléselosztást fog biztosítani.  A szolgáltatás más esetekben hasznos, ha nem praktikus vagy nemkívánatos az egyes portok megadására. Egy HA portos terheléselosztási szabály lehetővé teszi, hogy aktív-passzív vagy aktív-aktív n + 1 forgatókönyvet hozzon létre a hálózati virtuális berendezésekhez és bármely olyan alkalmazáshoz, amelyhez nagy tartományú bejövő portok szükségesek.  Az állapot-mintavétel segítségével meghatározhatja, hogy mely háttérrendszer kapjon új folyamatokat.  A porttartomány-forgatókönyvek emulálása hálózati biztonsági csoporttal végezhető el. Az alapszintű Load Balancer nem támogatja a HA portokat.
[Ha portok részletes megvitatását](load-balancer-ha-ports-overview.md) tekintse át
>[!IMPORTANT]
> Ha hálózati virtuális berendezést szeretne használni, a gyártótól tájékozódhat arról, hogy a termékük a HA portokkal lett tesztelve, és követik a megvalósításra vonatkozó útmutatást. 

* **Több frontend**: Load Balancer több, több előtérrel rendelkező szabályt is támogat.  Standard Load Balancer kibontja ezt a kimenő forgatókönyvek számára.  A kimenő forgatókönyvek lényegében egy bejövő terheléselosztási szabály inverze.  A bejövő terheléselosztási szabály is létrehoz egy hozzárendelést a kimenő kapcsolatok számára. Standard Load Balancer a virtuálisgép-erőforrásokhoz társított összes előtérbeli felületet egy terheléselosztási szabályon keresztül használja.  Emellett a terheléselosztási szabály egyik paramétere, amely lehetővé teszi egy terheléselosztási szabály letiltását a kimenő kapcsolathoz, ami lehetővé teszi adott előtérbeli elemek kiválasztását, beleértve a none elemet is.

Az összehasonlításhoz az alapszintű Load Balancer véletlenszerűen választja ki az egyetlen előtérbeli felületet, és nincs lehetőség a kiválasztására.
## <a name="load-balancer-types"></a>Load Balancer típusok

### <a name = "publicloadbalancer"></a>Nyilvános Load Balancer

A nyilvános Load Balancer leképezi a nyilvános IP-címet és a bejövő forgalom portját a virtuális gép magánhálózati IP-címére és portjára. Load Balancer leképezi a forgalmat a virtuális gépről érkező válasz forgalmának másik felén. A terheléselosztási szabályok alkalmazásával adott típusú adatforgalom több virtuális gépre vagy szolgáltatásba is terjeszthető. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.

>[!NOTE]
>Rendelkezésre állási csoportonként csak egy nyilvános Load Balancer és egy belső Load Balancer valósítható meg.

Az alábbi ábra egy elosztott terhelésű végpontot mutat be a webes forgalomhoz, amely a nyilvános és a 80-as TCP-porton három virtuális gép között van megosztva. Ez a három virtuális gép egy elosztott terhelésű készlet részeit képezi.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Nyilvános Load Balancer">
</p>

*Ábra: webes forgalom kiegyensúlyozása nyilvános terheléselosztó használatával*

Az internetes ügyfelek a 80-as TCP-porton lévő webalkalmazás nyilvános IP-címére küldenek webszolgáltatási kéréseket. Azure Load Balancer elosztja a kérelmeket a három virtuális gép között a terheléselosztási készletben. Load Balancer algoritmusokkal kapcsolatos további információkért lásd: [Load Balancer fogalmak](concepts-limitations.md#load-balancer-concepts).

A Azure Load Balancer alapértelmezés szerint több virtuálisgép-példány között egyenlően osztja el a hálózati forgalmat. A munkamenet-affinitást is beállíthatja. További információ: [Azure Load Balancer elosztási módjának konfigurálása](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Belső Load Balancer

A belső terheléselosztó csak olyan erőforrásokra irányítja a forgalmat, amelyek egy virtuális hálózaton belül vannak, vagy amelyek VPN-kapcsolattal férnek hozzá az Azure-infrastruktúrához, szemben a nyilvános terheléselosztó használatával. Az Azure-infrastruktúra korlátozza a virtuális hálózatok elosztott terhelésű előtér-IP-címeinek elérését. Az előtér-IP-címeket és a virtuális hálózatokat a rendszer soha nem teszi elérhetővé közvetlenül az internetes végpontok számára. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el.

Egy belső Load Balancer a következő típusú terheléselosztásokat teszi lehetővé:

* **Virtuális hálózaton belül**: terheléselosztás a virtuális hálózatban lévő virtuális gépekről az azonos virtuális hálózatban lévő virtuális gépek készletére.
* **Létesítmények közötti virtuális hálózat esetén**: terheléselosztás a helyszíni számítógépekről az azonos virtuális hálózatban lévő virtuális gépekre.
* **Többrétegű alkalmazások esetén**: terheléselosztás az internetre irányuló, többrétegű alkalmazásokhoz, ahol a háttérbeli rétegek nem internetre néznek. A háttérrendszer a forgalmi terheléselosztást igényli az internetre irányuló rétegben. Lásd a következő ábrát.
* **Üzletági alkalmazásoknál**: Terheléselosztás üzletági alkalmazásokhoz, amelyek további terheléselosztó hardverek vagy szoftverek nélkül üzemelnek az Azure-ban. Ez a forgatókönyv olyan helyszíni kiszolgálókat tartalmaz, amelyek azon számítógépek készletében találhatók, amelyeknek a forgalma elosztott.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Nyilvános Load Balancer">
</p>

*Ábra: többrétegű alkalmazások terheléselosztása nyilvános és belső Load Balancer használatával*

## <a name="skus"></a> Load Balancer-termékváltozatok összehasonlítása

A Load Balancer az alapszintű és a standard SKU-t is támogatja. Ezek az SKU-ket a forgatókönyvek skálázása, a funkciók és a díjszabás különbözik. Az alapszintű Load Balancerekkel kapcsolatos esetleges forgatókönyvek standard Load Balancer használatával hozhatók létre. Mindkét SKU API-jai hasonlóak, és az SKU specifikációja alapján meghívja őket. A terheléselosztó és a nyilvános IP-címek támogatásához használható API a `2017-08-01` API-val kezdődik. Mindkét SKU ugyanazt az általános API-t és struktúrát használja.

A teljes forgatókönyv konfigurálása az SKU-tól függően némileg eltérő lehet. A Load Balancer dokumentációja felhívja a figyelmét arra, hogy ha egy cikk csak egy adott SKU-ra vonatkozik. A különbségek összehasonlításához és megértéséhez lásd az alábbi táblázatot. További információ: az [Azure standard Load Balancer áttekintése](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft javasolja standard Load Balancer.
Az önálló virtuális gépeket, a rendelkezésre állási csoportokat és a Virtual Machine Scale-készleteket csak egy termékváltozathoz csatlakoztathatja, sosem kettőhöz egyszerre. A Load Balancer és a nyilvános IP-cím SKU-nak egyeznie kell, ha nyilvános IP-címekkel használja őket. A Load Balancer és a nyilvános IP-címek nem változtathatók meg.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

További információ: [Load Balancer korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). A Standard Load Balancerről további részleteket az [áttekintés](load-balancer-standard-overview.md), a [díjszabás](https://aka.ms/lbpricing) és az [SLA](https://aka.ms/lbsla) szakaszban talál.

## <a name = "limitations"></a>Korlátozások

- Az SKU-ket nem változtathatók meg. A meglévő erőforrások SKU-jának módosítása nem lehetséges.
- Egy önálló virtuálisgép-erőforrás, rendelkezésre állási készlet erőforrás vagy virtuálisgép-méretezési csoport erőforrása hivatkozhat egyetlen SKU-ra, soha nem mindkettőre.
- Egy Load Balancer szabály nem terjedhet ki két virtuális hálózatra.  A frontendeknek és a hozzájuk kapcsolódó háttérbeli példányoknak ugyanabban a virtuális hálózatban kell lenniük.  
- Az [előfizetési műveletek](../azure-resource-manager/management/move-resource-group-and-subscription.md) nem támogatottak a standard LB-és a nyilvános IP-erőforrások esetében.
- A VNet és más Microsoft-Platform nélküli webes feldolgozói szerepkörök csak belső standard Load Balancer mögött lévő példányokból érhetők el. Ezt nem kell felhasználnia, mert a saját maga vagy a mögöttes platform értesítés nélkül változhat. Mindig feltételezni kell, hogy a [kimenő kapcsolatot](load-balancer-outbound-connections.md) explicit módon kell létrehoznia, ha csak belső standard Load Balancer használata esetén szükséges.

- A Load Balancer terheléselosztást és port továbbítást biztosít bizonyos TCP-vagy UDP-protokollokhoz. A terheléselosztási szabályok és a bejövő NAT-szabályok támogatják a TCP és az UDP használatát, de nem más IP-protokollok, beleértve az ICMP-t is.

  A Load Balancer nem szakítja meg, nem válaszol, vagy más módon kommunikál az UDP-vagy TCP-forgalom hasznos adataival. Nem proxy. Az előtér kapcsolatának sikeres érvényesítéséhez a terheléselosztásban vagy a bejövő NAT-szabályban használt protokollal együtt kell végrehajtani a sávon belüli kapcsolatot. A virtuális gépek közül legalább az egyiknek választ kell adnia az ügyfél számára, hogy megjelenjen a válasz az előtérből.

  Nem érkezett sávon kívüli válasz a Load Balancer előtérből azt jelzi, hogy egyetlen virtuális gép sem válaszolhat. A virtuális gép nem tud reagálni egy Load Balancer előtérre. Ez az elv a kimenő kapcsolatokra is vonatkozik, amelyekben a port maszkolási SNAT csak TCP és UDP esetén támogatott. Minden egyéb IP-protokoll, beleértve az ICMP-t, sikertelen. Rendeljen egy példány szintű nyilvános IP-címet a probléma enyhítéséhez. További információ: a [SNAT és a Pat ismertetése](load-balancer-outbound-connections.md#snat).

- A belső terheléselosztó nem fordítja le a kimenő kapcsolatokat egy belső Load Balancer elülső végére, mert mindkettő magánhálózati IP-címtartomány részét képezi. A nyilvános terheléselosztó [kimenő kapcsolatokat](load-balancer-outbound-connections.md) biztosít a virtuális hálózaton belüli magánhálózati IP-címekről a nyilvános IP-címekre. A belső terheléselosztó esetében ez a módszer elkerüli a lehetséges SNAT-portok kimerülését egy egyedi belső IP-címtartomány belsejében, ahol a fordítás nem szükséges.

  Ennek egyik mellékhatása, hogy ha a háttér-készletben lévő virtuális gépről érkező kimenő forgalom a készlet belső Load Balancerának végére kísérli meg a folyamatot, _és_ az önmagához van rendelve, a folyamat két lába nem egyezik. Mivel nem egyeznek, a folyamat meghiúsul. A folyamat abban az esetben sikeres, ha a folyamat nem képezi vissza ugyanazt a virtuális gépet a háttér-készletben, amely létrehozta a folyamatot az előtér-végpontnak.

  Ha a folyamat önmagára mutat, a kimenő folyamat úgy tűnik, hogy a virtuális gépről származik, és a megfelelő bejövő folyamat úgy tűnik, hogy a virtuális gépről származik. A vendég operációs rendszer szempontjából az ugyanazon folyamat bejövő és kimenő részei nem egyeznek meg a virtuális géppel. A TCP-verem nem ismeri fel ugyanezen folyamat azon felét, mint ugyanazon folyamat részeként. A forrás és a cél nem egyezik. Ha a folyamat a háttérbeli készlet bármely más virtuális gépe felé mutat, a folyamat felei megegyeznek, és a virtuális gép válaszolni tud a folyamatra.

  Ennek a forgatókönyvnek a tünete átmeneti kapcsolati időtúllépés, amikor a folyamat visszatér ugyanarra a háttérre, amely a folyamatból származik. Gyakori megkerülő megoldások közé tartozik a belső Load Balancer mögötti proxy réteg beszúrása, valamint a közvetlen kiszolgáló Return (DSR) stílusú szabályok használata. További információ: [Azure Load Balancer több előtér-végpontja](load-balancer-multivip-overview.md).

  A belső Load Balancer bármely harmadik féltől származó proxyval egyesítheti, vagy használhat belső [Application Gateway](../application-gateway/application-gateway-introduction.md) a proxy forgatókönyvekhez HTTP/HTTPS használatával. Habár a probléma megoldásához nyilvános Load Balancer is használható, az eredményül kapott forgatókönyv a [SNAT kimerülését](load-balancer-outbound-connections.md#snat)eredményezi. Kerülje ezt a második megközelítést, ha körültekintően nem felügyelt.

- Általánosságban elmondható, hogy a továbbítási IP-töredékek nem támogatottak a terheléselosztási szabályokban. Az UDP-és TCP-csomagok IP-töredezettsége nem támogatott a terheléselosztási szabályokban. A magas rendelkezésre állású portok terheléselosztási szabályai használhatók a meglévő IP-töredékek továbbítására. További információ: [magas rendelkezésre állású portok – áttekintés](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Következő lépések

- Lásd: [nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) a Load Balancer használatának megkezdéséhez: hozzon létre egyet, hozzon létre virtuális gépeket egy egyéni IIS-bővítménnyel, és a virtuális gépek között a webalkalmazás terheléselosztását.
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- Tudnivalók a [Kimenő kapcsolatok Load Balancer](load-balancer-outbound-connections.md)használatáról.
- További információ a [kimenő szabályokról](load-balancer-outbound-rules-overview.md).
- További információ [a TCP alaphelyzetbe állításáról üresjáratban](load-balancer-tcp-reset.md).
- További tudnivalók a HA-portok terheléselosztási [szabályaival standard Load Balancer](load-balancer-ha-ports-overview.md).
- Ismerje meg, hogyan használhatja [a Load Balancert több előtér-felülettel](load-balancer-multivip-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
