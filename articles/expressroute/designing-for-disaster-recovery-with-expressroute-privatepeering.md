---
title: Az Azure expressroute-tal vész-helyreállítási tervezése |} A Microsoft Docs
description: Ezen a lapon ajánlásokkal architekturális vész-helyreállítási Azure ExpressRoute használata során.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466073"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Az ExpressRoute privát társviszony-létesítés vész-helyreállítási tervezése

Magas rendelkezésre állású szolgáltató Microsoft erőforrások szintű magánhálózati kapcsolatot biztosít ExpressRoute tervezték. Más szóval nincs nem az ExpressRoute elérési úton belül a Microsoft hálózati meghibásodási pont. Kialakítási szempontok az ExpressRoute-Kapcsolatcsoportok rendelkezésre állásának maximalizálását, lásd: [magas rendelkezésre állás az expressroute-tal tervezése][HA].

Azonban véve Murphy a népszerű adage--*Ha bármi is hiba lép fel, akkor*--figyelembe veszi, ebben a cikkben ossza meg velünk összpontosíthat solutions, amely a hibák, amelyek egyetlen ExpressRoute-kapcsolatcsoportot is kezelhetők. Más szóval ez a cikk a ossza meg velünk megvizsgáljuk a hálózati architektúra kapcsolatos szempontok vész-helyreállítási georedundáns ExpressRoute-Kapcsolatcsoportok használatával robusztus háttérbeli hálózati kapcsolat létrehozásához.

## <a name="need-for-redundant-connectivity-solution"></a>Redundáns megoldás szükség

Nincsenek lehetőségeket és a példányok, ahol egy teljes regionális szolgáltatás (lehet, amely a Microsoft, a hálózati szolgáltatók, az ügyfél vagy a más felhőszolgáltatók) teljesítményűre lekérdezi. Ilyen regionális széles szolgáltatás hatást kiváltó természetes calamity tartalmazza. Ezért az üzleti folytonosság és a működés szempontjából kritikus fontosságú alkalmazásai fontos vész-helyreállítási terv.   

Függetlenül attól, hogy fusson-e az üzletmenet szempontjából kritikus alkalmazásokat az Azure-régióba vagy a helyszínen, vagy bármely más használhatja a feladatátvételi helyet egy másik Azure-régióban. A következő cikkek címek vész-helyreállítási kérelmek és előtérbeli hozzáférés perspektívák:

- [A nagyvállalati szintű vészhelyreállítás][Enterprise DR]
- [SMB-vészhelyreállítás az Azure Site Recoveryvel][SMB DR]

Ha használ, az ExpressRoute-kapcsolat a helyszíni hálózat és a Microsoft között a működés szempontjából kritikus tevékenységekhez, a vész-helyreállítási terv is tartalmaznia kell georedundáns hálózati kapcsolat. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Több ExpressRoute-Kapcsolatcsoportok használatával kihívásai

Ha ugyanazokat a hálózatokhoz egynél több kapcsolattal teljesítményt követelő vezeti be a hálózatok közötti párhuzamos elérési utak. Párhuzamos elérési utakat, amikor nem megfelelően lett tervezve, aszimmetrikus útválasztás vezethet. Ha az elérési út (például a NAT, a tűzfal) állapot-nyilvántartó entitások, aszimmetrikus útválasztás forgalmat letilthatják.  Általában az ExpressRoute privát társviszony-létesítési útvonal keresztül nem bővítményeként állapot-nyilvántartó entitások, például a NAT vagy tűzfal. Ezért aszimmetrikus útválasztás az ExpressRoute privát társviszony-létesítésen keresztül nem feltétlenül blokkolja a forgalmat.
 
Adatforgalom elosztását georedundáns párhuzamos elérési utak, függetlenül attól, hogy van-e állapot-nyilvántartó entitások vagy sem, ha inkonzisztens a hálózati teljesítmény lenne tapasztalható. Ebben a cikkben vizsgáljuk meg ezek a kihívások megoldása.

## <a name="small-to-medium-on-premises-network-considerations"></a>Kis és közepes méretű helyszíni hálózatokkal kapcsolatos szempontok

Vegyünk például a következő ábra szemlélteti a példában hálózati. A példában a Contoso egy helyszíni hely és a egy Azure-régióban a Contoso VNet közötti georedundáns az ExpressRoute-kapcsolat jön létre. Az ábrán zöld vonal jelzi az előnyben részesített elérési úton (1. ExpressRoute), és egy pontozott készenléti elérési úton (2. ExpressRoute) jelöli.

[![1]][1]

Az ExpressRoute-kapcsolat katasztrófa utáni helyreállítás tervezésekor kell figyelembe venni:

- georedundáns ExpressRoute-Kapcsolatcsoportok használatával
- változatos szolgáltatás-szolgáltató hálózat használatával különböző ExpressRoute-kapcsolatcsoport
- az ExpressRoute-kapcsolatcsoport esetében minden egyes tervezése [magas rendelkezésre állás][HA]
- az ügyfél hálózati másik helyen lévő másik ExpressRoute-kapcsolatcsoport leállítása

Alapértelmezés szerint ha Ön haladó útvonalak meghirdetéséhez azonos módon összes ExpressRoute útvonal, az Azure lesz helyszíni kötött forgalom terheléselosztásához között ExpressRoute elérési utakban azonos költségű több elérési út (ECMP) útválasztás használatával.

Azonban a georedundáns ExpressRoute-Kapcsolatcsoportok a kell figyelembe veszik figyelembe különböző hálózati teljesítmény különböző hálózati elérési úttal (különösen a hálózati késés). Egységes hálózati teljesítmény eléréséhez normál működés során, érdemes inkább az ExpressRoute-kapcsolatcsoport, amely a minimális késést biztosít.

Azure-ra egy ExpressRoute-kapcsolatcsoportot előnyben részesítése (hatékonyságát a listán) a következő módszerek egyikével egy másik befolyásolhatja:

- az előnyben részesített ExpressRoute-kapcsolatcsoport más ExpressRoute-kapcsolatcsoport képest keresztül konkrétabb útvonal hirdeti
- a kapcsolat a virtuális hálózaton, ExpressRoute-kapcsolatcsoportot előnyben részesített mutató kapcsolati nagyobb súlyt konfigurálása
- az Útvonalak meghirdetése kevesebb előnyben részesített ExpressRoute-kapcsolatcsoport a hosszabb AS Path (az elérési út illesztenie) keresztül

### <a name="more-specific-route"></a>Pontosabban meghatározott útvonal

A következő ábra szemlélteti a használatával pontosabban meghatározott útvonal hirdetmény befolyásoló ExpressRoute elérési út kiválasztása. Az ábrázolt példa helyszíni Contoso/24 IP-címtartomány két /25 címtartományok elérési útnak (1. ExpressRoute) keresztül és a készenléti 2 elérési útjának (ExpressRoute) keresztül /24 hirdetménnyel telepítették.

[![2]][2]

Mivel /25 pontosabb, /24, képest Azure elküldése 10.1.11.0/24 ExpressRoute 1 a normál állapotban keresztül menő forgalmat. A kapcsolatok ExpressRoute 1 leáll, majd a virtuális hálózat jelennének meg a 10.1.11.0/24 útvonal hirdetés csak a 2. ExpressRoute; használatával és ezért a készenléti kapcsolatcsoport ez sikertelen állapotba.

### <a name="connection-weight"></a>Kapcsolat súlyozása

Az alábbi képernyőképen látható konfigurálása az Azure Portalon egy ExpressRoute-kapcsolat súlyát.

[![3]][3]

A következő ábra szemlélteti a kapcsolat súly használatával befolyásoló ExpressRoute elérési út kiválasztása. Kapcsolat súlyát alapértelmezett értéke 0. Az alábbi példában az 1. ExpressRoute-kapcsolat súlyát 100 van konfigurálva. Amikor egy VNet-útvonal előtagja egynél több ExpressRoute-kapcsolatcsoporton keresztül hirdeti meg kap, a virtuális hálózat a nagyobb súlyú kapcsolatot részesíti előnyben.

[![4]][4]

A kapcsolatok ExpressRoute 1 leáll, majd a virtuális hálózat jelennének meg a 10.1.11.0/24 útvonal hirdetés csak a 2. ExpressRoute; használatával és ezért a készenléti kapcsolatcsoport ez sikertelen állapotba.

### <a name="as-path-prepend"></a>ELÉRÉSI jogosultságokat

A következő ábra szemlélteti a befolyásoló ExpressRoute elérési út kiválasztása illesztenie elérési út használatával. Az ábrán az útvonal hirdetmény keresztül ExpressRoute 1 azt jelzi, hogy eBGP alapértelmezett viselkedését. Az útvonal hirdetmény ExpressRoute 2 keresztül, az a helyszíni hálózat ASN van kiegészített emellett az útvonal elérési ÚTJAKÉNT. Több ExpressRoute-Kapcsolatcsoportok kiszolgálónként eBGP útvonal tanúsítványkiválasztási folyamat segítségével az ugyanazon útvonal fogadásakor a VNet inkább a legrövidebb ELÉRÉSI útvonala. 

[![5]][5]

A kapcsolatok ExpressRoute 1 leáll, majd a virtuális hálózat jelennének meg a 10.1.11.0/24 útvonal hirdetés csak a 2. ExpressRoute használatával. Árrombolása annál hosszabb ELÉRÉSI válnak irreleváns. Ezért a készenléti kapcsolatcsoport lesz felhasználva a következőben ez sikertelen állapotba.

Használja a módszerek valamelyikét, ha befolyásolhatja az Azure-bA előnyben részesítése az ExpressRoute egyik mások, meg kell, hogy a helyszíni hálózathoz is érdemes előnyben részesíteni ugyanazt az ExpressRoute-útvonalat az Azure traffic aszimmetrikus folyamatok elkerülése érdekében van kötve. Általában a helyi preferenciaértéket befolyásolhatja a helyi hálózaton keresztül mások egy ExpressRoute-kapcsolatcsoportot előnyben szolgál. Helyi beállítás: egy belső (iBGP) BGP-metrikát. A BGP-útvonalat a legnagyobb értékű helyi előnyben részesíti előnyben.

> [!IMPORTANT]
> Egyes ExpressRoute-Kapcsolatcsoportok, készenléti használatakor meg kell aktívan kezelni és időről időre tesztelheti a feladatátvételi művelet. 
> 

## <a name="large-distributed-enterprise-network"></a>Nagy méretű elosztott vállalati hálózat

Ha egy nagy méretű elosztott vállalati hálózaton, Ön valószínűleg több ExpressRoute-kapcsolatcsoporttal rendelkezik. Ebben a szakaszban nézzük meg, hogyan tervezhet az aktív-aktív ExpressRoute-Kapcsolatcsoportok használatával anélkül, hogy kapcsolatcsoportokat további készenléti vész-helyreállítási. 

Vegyünk például a példában a következő ábra mutatja be. A példában a Contoso két tartalmaz két Contoso IaaS telepítési két különböző régióban az Azure két különböző társviszony-létesítési helyszínek az ExpressRoute-Kapcsolatcsoportok keresztül csatlakozik a helyszíni helyeken. 

[![6]][6]

Hogyan tudjuk tervezhet a vész-helyreállítási közötti keresztszűrést területi helye (Tartomány1/region2 való location2/location1) adatforgalmat hatással van. Vegyünk például két különböző vészhelyreállítási architektúrák, amely közötti terület-location forgalmat különbözőképpen irányítja.

### <a name="scenario-1"></a>1\. forgatókönyv

Az első forgatókönyv nézzük alakítja ki vész-helyreállítási úgy, hogy az Azure-régióban és a helyszíni hálózat közötti összes forgalom áramlása stabil állapotban lévő helyi ExpressRoute-kapcsolatcsoportot. Ha a helyi ExpressRoute-kapcsolatcsoport meghibásodik, majd a távoli ExpressRoute-kapcsolatcsoport használja az Azure közötti forgalom adatfolyamait, és a helyszíni hálózat.

1\. forgatókönyv a következő ábra mutatja be. A diagramban zöld sorok jelzik elérési utak a VNet1 és a helyszíni hálózat közötti adatforgalmat. A kék vonal jelzi a VNet2 és a helyszíni hálózat közötti forgalom elérési utakat. Folytonos vonal jelzi az egyenletes állapotban kívánt elérési úthoz, és a szaggatott vonal jelzi a forgalmat útvonal az egyenletes adatforgalmat végrehajtó megfelelő ExpressRoute-kapcsolatcsoport meghibásodása. 

[![7]][7]

A forgatókönyv kapcsolat súly használatával befolyásolhatja a virtuális hálózatok előnye a kapcsolatot a helyszíni hálózat helyi társviszony-létesítési helyszínen ExpressRoute kötött forgalom is tervezhet. Végezze el a megoldás, győződjön meg, hogy a szimmetrikus fordított irányú adatforgalmat kell. Preferenciaértékével helyi az iBGP-munkamenetben (, amelyen az ExpressRoute-Kapcsolatcsoportok megszűnik a helyszíni oldalon) a BGP-útválasztó között egy ExpressRoute-kapcsolatcsoportot előnyben. A megoldás a következő ábra mutatja be. 

[![8]][8]

### <a name="scenario-2"></a>2\. forgatókönyv

A 2. forgatókönyv esetében a következő ábra mutatja be. A diagramban zöld sorok jelzik elérési utak a VNet1 és a helyszíni hálózat közötti adatforgalmat. A kék vonal jelzi a VNet2 és a helyszíni hálózat közötti forgalom elérési utakat. Az egyenletes (folytonos vonal a diagram), az összes virtuális hálózatok és helyszíni helyek között a forgalmat, a flow a Microsoft gerinchálózatán keresztül, és keresztül összekapcsolása a helyszínek csak a hiba állapotú (pontozott sorokat a diagram) az expressroute.

[![9]][9]

A megoldás a következő ábra mutatja be. Amint, vagy is tervezhet a forgatókönyv (2. lehetőség) használatával pontosabban meghatározott útvonal (1. lehetőség) vagy AS-path illesztenie befolyásolhatja a virtuális hálózati elérési út kiválasztása. Befolyásolhatja a helyszíni hálózat útvonal kiválasztása az Azure-kötött forgalmat, a helyszíni hely összekapcsolása kell konfigurálnia, kevesebb mint előnyben részesíteni. Konfigurált összekapcsolási hivatkozásra előnyösebb Howe attól függ, hogy a helyszíni hálózaton belüli használt útválasztási protokollt. Használhatja a helyi beállításokat szabályozó iBGP vagy metrika IGP (OSPF vagy IS – IS).

[![10]][10]


## <a name="next-steps"></a>További lépések

Ebben a cikkben már volt szó, az ExpressRoute kapcsolatcsoport privát társviszony-létesítési kapcsolat vész-helyreállítási tervezéséről. A következő cikkek címek vész-helyreállítási kérelmek és előtérbeli hozzáférés perspektívák:

- [A nagyvállalati szintű vészhelyreállítás][Enterprise DR]
- [SMB-vészhelyreállítás az Azure Site Recoveryvel][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "kis és közepes méretű helyszíni hálózatokkal kapcsolatos szempontok"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "befolyásoló használatával pontosabb útvonalak elérési út kiválasztása"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "konfigurálása kapcsolat súly Azure-portálon"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "befolyásoló használatával kapcsolat súly elérési út kiválasztása"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "illesztenie befolyásoló elérési út használatával elérési út kiválasztása"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "nagy méretű elosztott helyszíni hálózati megfontolások"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "1. forgatókönyv"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktív-aktív ExpressRoute-Kapcsolatcsoportok 1 megoldás"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "2. forgatókönyv"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktív-aktív ExpressRoute-Kapcsolatcsoportok megoldás 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





