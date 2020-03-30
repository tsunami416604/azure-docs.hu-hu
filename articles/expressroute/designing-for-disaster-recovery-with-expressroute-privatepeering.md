---
title: 'Azure ExpressRoute: Tervezés vészhelyreállításhoz'
description: Ez a lap az Azure ExpressRoute használata során a vész-helyreállításra vonatkozó architekturális javaslatokat tartalmaz.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076704"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Tervezés vészhelyreállításhoz az ExpressRoute privát társviszony-létesítésével

Az ExpressRoute magas rendelkezésre állásra van tervezve, hogy mobilszolgáltatói szintű magánhálózati kapcsolatot biztosítson a Microsoft erőforrásaihoz. Más szóval a Microsoft-hálózaton belül nincs egyetlen meghibásodási pont az ExpressRoute elérési útban. Az ExpressRoute-áramkörök rendelkezésre állásának maximalizálásához szükséges tervezési szempontokért [lásd: Tervezés az ExpressRoute magas rendelkezésre állásának tervezése című témakörben.][HA]

Azonban figyelembe véve Murphy népszerű példabeszéd -*ha valami elromlathat, akkor*-- figyelembe kell venni, ebben a cikkben hadd összpontosítson megoldásokat, amelyek túlmutatnak hibák, hogy lehet kezelni egyetlen ExpressRoute áramkör. Más szóval ebben a cikkben nézzük meg a hálózati architektúra szempontok robusztus háttérhálózati kapcsolat létrehozása a vész-helyreállítási georedundáns ExpressRoute-áramkörök használatával.

## <a name="need-for-redundant-connectivity-solution"></a>Redundáns kapcsolódási megoldás szükségessége

Vannak olyan lehetőségek és esetek, amikor egy teljes regionális szolgáltatás (legyen az microsoftos, hálózati szolgáltató, ügyfél vagy más felhőszolgáltató) leromlott. Az ilyen regionális szintű szolgáltatási hatás kiváltó oka a természeti csapás. Ezért az üzletmenet folytonossága és a kritikus fontosságú alkalmazások fontos, hogy tervezze meg a katasztrófa-helyreállítási.   

Függetlenül attól, hogy az Azure-régióban vagy a helyszínen vagy bárhol máshol futtatja-e a kritikus fontosságú alkalmazásokat, feladatátvételi helyként használhat egy másik Azure-régiót. A következő cikkek az alkalmazások és az előtér-hozzáférési perspektívák vészhelyreállítási tervével foglalkoznak:

- [Nagyvállalati szintű vészhelyreállítás][Enterprise DR]
- [SMB-vészhelyreállítás az Azure Site Recoveryvel][SMB DR]

Ha a helyszíni hálózat és a Microsoft közötti ExpressRoute-kapcsolatra támaszkodik a kritikus fontosságú műveletekhez, a vész-helyreállítási tervnek georedundáns hálózati kapcsolatot is tartalmaznia kell. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Több ExpressRoute-kapcsolat használatának kihívásai

Ha ugyanazt a hálózatkészletet több kapcsolathasználatával kapcsolja össze, párhuzamos elérési utakat vezet be a hálózatok között. A párhuzamos elérési utak nem megfelelően tervezve aszimmetrikus útválasztást eredményezhetnek. Ha állapotalapú entitások (például NAT, tűzfal) vannak az elérési úton, az aszimmetrikus útválasztás blokkolhatja a forgalmat.  Általában az ExpressRoute privát társviszony-létesítési útvonalon nem találkozik állapotalapú entitások, például a NAT vagy a tűzfalak. Ezért az ExpressRoute privát társviszony-létesítésén való aszimmetrikus útválasztás nem feltétlenül blokkolja a forgalmat.
 
Ha azonban a georedundáns párhuzamos útvonalak közötti terheléselosztási forgalmat, függetlenül attól, hogy rendelkezik-e állapotalapú entitásokkal, inkonzisztens hálózati teljesítményt tapasztal. Ebben a cikkben beszéljük meg, hogyan lehet kezelni ezeket a kihívásokat.

## <a name="small-to-medium-on-premises-network-considerations"></a>Kis és közepes helyszíni hálózati szempontok

Vegyük figyelembe a példa hálózat illusztrált az alábbi ábrán. A példában georedundáns ExpressRoute-kapcsolat jön létre a Contoso helyszíni helye és a Contoso virtuális hálózata egy Azure-régióban között létrejön. A diagramon a folytonos zöld vonal az előnyben részesített útvonalat jelöli (expressroute 1-en keresztül), a pontozott pedig a készenléti útvonalat jelöli (expressroute 2-n keresztül).

[![1]][1]

Amikor az ExpressRoute-kapcsolatot vész-helyreállítási célokra tervezi, figyelembe kell vennie a következőket:

- georedundáns ExpressRoute-áramkörök használata
- különböző szolgáltatói hálózat(ok) használata különböző ExpressRoute-áramkörökhöz
- az ExpressRoute-áramkörök tervezése [magas rendelkezésre állás érdekében][HA]
- a különböző ExpressRoute-áramkör leválasztása az ügyfélhálózat különböző helyén

Alapértelmezés szerint ha az útvonalakat azonos módon hirdeti az összes ExpressRoute-útvonalon, az Azure a helyszíni kötött forgalmat az összes ExpressRoute-útvonalon egyenlő költségű többelérési (ECMP) útválasztás használatával tölti be.

A georedundáns ExpressRoute-áramkörök esetében azonban figyelembe kell vennünk a különböző hálózati teljesítményeket különböző hálózati elérési utakkal (különösen a hálózati késés hez). A normál működés során a konzisztensebb hálózati teljesítmény érdekében célszerű az ExpressRoute-áramkört, amely a minimális késést kínálja.

Az Azure-t az alábbi technikák egyikének használatával az egyik ExpressRoute-kapcsolat előnyben részesítésére is beállíthatja (a hatékonyság sorrendjében felsorolva):

- konkrétabb útvonal hirdetése az előnyben részesített ExpressRoute-áramkörön keresztül más ExpressRoute-áramkörökhöz képest
- nagyobb kapcsolatsúly konfigurálása azon a kapcsolaton, amely a virtuális hálózatot az előnyben részesített ExpressRoute-áramkörhöz kapcsolja
- az útvonalak hirdetése a kevésbé preferált ExpressRoute-áramkörön hosszabb AS elérési úttal (AS Path prepend)

### <a name="more-specific-route"></a>Konkrétabb útvonal

Az alábbi ábra az ExpressRoute-útvonal kiválasztásának befolyásolását mutatja be konkrétabb útvonalhirdetés sel. Az illusztrált példában a Contoso helyszíni /24 IP-tartománya két /25 címtartományként van meghirdetve az előnyben részesített útvonalon (ExpressRoute 1) keresztül, és /24-ként a készenléti útvonalon (ExpressRoute 2) keresztül.

[![2]][2]

Mivel a /25 pontosabb, a /24-hez képest, az Azure a 10.1.11.0/24-es expresszroute 1-en keresztül normál állapotba kerülő forgalmat küld. Ha az ExpressRoute 1 mindkét kapcsolata megszakad, akkor a virtuális hálózat csak az ExpressRoute 2-n keresztül látja a 10.1.11.0/24 útvonalhirdetést; ezért a készenléti áramkört ebben a hibaállapotban használja.

### <a name="connection-weight"></a>A kapcsolat súlya

Az alábbi képernyőkép bemutatja az ExpressRoute-kapcsolat súlyát az Azure Portalon keresztül.

[![3]][3]

Az alábbi ábra az ExpressRoute-útvonal kiválasztásának a kapcsolat súlyával történő befolyásolását mutatja be. A kapcsolat alapértelmezett vastagsága 0. Az alábbi példában az ExpressRoute 1 kapcsolatának súlya 100-ként van konfigurálva. Ha egy virtuális hálózat egynél több ExpressRoute-kapcsolaton keresztül hirdetett útvonal-előtagot kap, a virtuális hálózat a legnagyobb súllyal rendelkező kapcsolatot részesíti előnyben.

[![4]][4]

Ha az ExpressRoute 1 mindkét kapcsolata megszakad, akkor a virtuális hálózat csak az ExpressRoute 2-n keresztül látja a 10.1.11.0/24 útvonalhirdetést; ezért a készenléti áramkört ebben a hibaállapotban használja.

### <a name="as-path-prepend"></a>AS elérési út

Az alábbi ábra az ExpressRoute-útvonal kiválasztásának befolyásoló, AS elérési út-előkészületet. A diagramon az ExpressRoute 1-en keresztüli útvonalhirdetés az eBGP alapértelmezett viselkedését jelzi. Az ExpressRoute 2-en keresztüli útvonalhirdetésen a helyszíni hálózat ASN-je is előkészül az útvonal AS elérési útján. Ha ugyanazt az útvonalat több ExpressRoute-körön keresztül is érkezik, az eBGP-útvonal kiválasztási folyamata szerint a virtuális hálózat a legrövidebb AS-útvonallal rendelkező útvonalat részesíti előnyben. 

[![5]][5]

Ha az ExpressRoute 1 mindkét kapcsolata megszakad, akkor a virtuális hálózat csak az ExpressRoute 2-n keresztül látja a 10.1.11.0/24 útvonalhirdetést. Következésképpen a hosszabb AS-út lényegtelenné válna. Ezért a készenléti áramkör t használja ebben a hiba állapotban.

Bármely technikával, ha befolyásolja az Azure-t, hogy az ExpressRoute egyikét részesítse előnyben másokkal szemben, azt is biztosítania kell, hogy a helyszíni hálózat is ugyanazt az ExpressRoute-elérési utat részesítse előnyben az Azure-hoz kötött forgalom számára az aszimmetrikus folyamatok elkerülése érdekében. A helyi preferencia-érték általában a helyszíni hálózat befolyásolására szolgál, hogy egy ExpressRoute-áramkört részesítsen előnyben a többivel szemben. A helyi preferencia egy belső BGP (iBGP) metrika. A legmagasabb helyi preferencia-értékkel rendelkező BGP-útvonal at részesíti előnyben.

> [!IMPORTANT]
> Bizonyos ExpressRoute-áramkörök készenléti állapotban való használataesetén aktívan kell kezelnie őket, és rendszeres időközönként tesztelnie kell a feladatátvételi műveletet. 
> 

## <a name="large-distributed-enterprise-network"></a>Nagy méretű elosztott vállalati hálózat

Ha nagy elosztott vállalati hálózattal rendelkezik, valószínűleg több ExpressRoute-kapcsolattal is rendelkezik. Ebben a szakaszban tekintse meg, hogyan tervezhet vész-helyreállítási az aktív-aktív ExpressRoute-áramkörök használatával, további készenléti áramkörök nélkül. 

Vegyük figyelembe az alábbi ábrán bemutatott példát. A példában a Contoso két helyszíni helyet rendelkezik két Contoso IaaS-üzembe helyezéshez két különböző Azure-régióban ExpressRoute-kapcsolati kapcsolati kapcsolati kapcsolati helyeken keresztül két különböző társviszony-létesítési helyen. 

[![6]][6]

Hogyan tervezzük a vész-helyreállítási hatással van arra, hogy a határokon átnyúló regionális helyközi (region1/region2 a location2/location1) forgalom van irányítva. Vegyünk két különböző katasztrófa-architektúrák, amelyek útvonalak útvonalai régió-helyforgalmát eltérően.

### <a name="scenario-1"></a>1. példa

Az első forgatókönyvben tervezzünk vészhelyreállítást úgy, hogy az Azure-régió és a helyszíni hálózat közötti összes forgalom a helyi ExpressRoute-kapcsolaton keresztül az állandó állapotban folyjon. Ha a helyi ExpressRoute-kapcsolatcsoport meghibásodik, majd a távoli ExpressRoute-kapcsolatcsoport az Azure és a helyszíni hálózat közötti összes forgalomhoz használatos.

Az 1. A diagramon a zöld vonalak a Virtuálishálózatok 1 és a helyszíni hálózatok közötti forgalom útvonalait jelölik. A kék vonalak a Virtuálishálózat2 és a helyszíni hálózatok közötti forgalom útvonalait jelzik. A folytonos vonalak jelzik a kívánt útvonalat az állandósult állapotban, a szaggatott vonalak pedig a forgalmi útvonalat a megfelelő ExpressRoute-kapcsolat meghibásodásában, amely állandósult forgalmat hordoz. 

[![7]][7]

A forgatókönyv a kapcsolat súlyának használatával befolyásolhatja a virtuális hálózatok, hogy inkább a kapcsolatot a helyi peering hely ExpressRoute a helyszíni hálózati kötött forgalom. A megoldás befejezéséhez szimmetrikus fordított forgalom áramlását kell biztosítania. A BGP-útválasztók közötti iBGP-munkamenetben (amelyen az ExpressRoute-áramkörök a helyszíni oldalon vannak leállítva) helyi preferenciával egy ExpressRoute-áramkört részesíthet előnyben. Az alábbi ábrán a megoldás látható. 

[![8]][8]

### <a name="scenario-2"></a>2. példa

A 2. A diagramon a zöld vonalak a Virtuálishálózatok 1 és a helyszíni hálózatok közötti forgalom útvonalait jelölik. A kék vonalak a Virtuálishálózat2 és a helyszíni hálózatok közötti forgalom útvonalait jelzik. Az állandósult állapotban (szilárdvonalak a diagramon) a virtuális hálózatok és a helyszíni helyek közötti forgalom nagy részt a Microsoft gerincén keresztül áramlik, és a helyszíni helyek közötti összeköttetésen keresztül csak a hiba állapotban (pontozott vonalak egy ExpressRoute ábrája).

[![9]][9]

Az alábbi ábrán a megoldás látható. Amint azt szemléltetik, a forgatókönyvet egy adottabb útvonal (1. lehetőség) vagy AS-elérési út (2. lehetőség) használatával tervezheti meg a virtuális hálózat útvonalának kiválasztásához. A helyszíni hálózati útvonal kiválasztásának befolyásolásához az Azure-hoz kötött forgalomhoz kevésbé előnyösnek kell konfigurálnia a helyszíni hely közötti kapcsolatot. Howe konfigurálja az összekapcsolási kapcsolat, mint előnyös függ az útválasztási protokoll t a helyszíni hálózaton. Használhatja a helyi preferencia iBGP vagy metrika IGP (OSPF vagy IS-IS).

[![10]][10]


## <a name="next-steps"></a>További lépések

Ebben a cikkben azt tárgyalta, hogyan tervezhet egy ExpressRoute-kapcsolati kapcsolatot egy ExpressRoute-kapcsolati kapcsolat vészhelyreállítása érdekében. A következő cikkek az alkalmazások és az előtér-hozzáférési perspektívák vészhelyreállítási tervével foglalkoznak:

- [Nagyvállalati szintű vészhelyreállítás][Enterprise DR]
- [SMB-vészhelyreállítás az Azure Site Recoveryvel][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "kis és közepes méretű helyszíni hálózati szempontok"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "az útvonalválasztás befolyásolása konkrétabb útvonalak használatával"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "a kapcsolat súlyának konfigurálása az Azure Portalon keresztül"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "az útvonal kiválasztásának befolyásolása a kapcsolat súlyával"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "az útvonal kiválasztásának befolyásolása az AS görbe előkészületével"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "nagy, elosztott helyszíni hálózati szempont"
[7.]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "forgatókönyv"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktív-aktív ExpressRoute áramköri megoldás 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "2. forgatókönyv"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktív-aktív ExpressRoute-áramköri megoldás 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





