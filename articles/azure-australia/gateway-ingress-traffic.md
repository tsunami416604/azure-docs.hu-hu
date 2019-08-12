---
title: Bejövő forgalom szabályozása az Azure Ausztráliában
description: Útmutató a bejövő adatforgalom vezérléséhez az Azure Ausztráliában, hogy megfeleljen az ausztráliai kormányzati követelményeknek a biztonságos internetes átjárók számára
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 3885b9fa346047a50a49c7b2f9b96b6a8f95e51f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779356"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Bejövő forgalom szabályozása az Azure Ausztráliában

Az IKT-rendszerek biztonságossá tételének alapvető eleme a hálózati forgalom szabályozása. A forgalmat csak akkor kell korlátozni, ha a rendszer működéséhez szükséges, hogy csökkentse az esetleges kompromisszumot.

Ez az útmutató részletesen ismerteti, hogyan működik a bejövő (bejövő) hálózati forgalom az Azure-ban, és javaslatokat tesz az internethez csatlakoztatott rendszer hálózati biztonsági vezérlőinek megvalósítására.

A hálózati vezérlők az Australian Cyber Security Center (ASCS) fogyasztói útmutatásával és a ASCS információs biztonsági manuális (ISM) szándékával összhangban vannak.

## <a name="requirements"></a>Követelmények

A nemzetközösségi rendszerek általános biztonsági követelményei az ISM-ben vannak meghatározva. Ahhoz, hogy támogassa a nemzetközösségi entitásokat a hálózati biztonság megvalósításában [, a ASCs közzétette a ASCs védelmet: A hálózati szegmentálás és elkülönítés](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)megvalósítása, valamint a felhőalapú környezetekben a rendszerek biztonságossá tétele érdekében a ASCs közzétette a [felhőalapú számítástechnikai biztonságot a bérlők számára](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

Ezek az útmutatók körvonalazzák a hálózati biztonság megvalósításának és a forgalom szabályozásának a kontextusát, valamint a hálózati tervezésre és konfigurálásra vonatkozó gyakorlati javaslatokat is.

A Microsoft [Cloud Computing Security a Microsoft Azure útmutató bérlői számára](https://aka.ms/au-irap) a szolgáltatás-megbízhatósági portál ausztrál oldalán Kiemelt Microsoft-technológiákat mutat be, amelyek lehetővé teszik a ASCs-kiadványokban található tanácsok betartását.

A ASCS közzétett kiadványokban azonosított következő kulcsfontosságú követelmények fontosak az Azure-beli bejövő forgalom szabályozásához:

|Leírás|Source|
|---|---|
|**A hálózati szegmentálás és elkülönítés megvalósítása – például n szintű architektúra – a gazdagép-alapú tűzfalak és a CSP hálózati hozzáférés-vezérlésének használatával korlátozhatja a bejövő és kimenő virtuálisgép-hálózati kapcsolatot, hogy csak a szükséges portok/protokollok legyenek elérhetők.**| _Felhő-számítástechnika bérlők számára_|
|**Megfelelően nagy sávszélességű, kis késleltetésű és megbízható hálózati kapcsolat implementálása** a bérlő (beleértve a bérlő távoli felhasználóit) és a felhőalapú szolgáltatás között a bérlő rendelkezésre állási követelményeinek kielégítése érdekében  | _Felhő-számítástechnika bérlők számára_|
|**Technológiák alkalmazása több, mint a hálózati rétegben**. Az egyes gazdagépeket és hálózatokat a gyakorlatilag felügyelhető legalacsonyabb szinten kell szegmentálni és elkülöníteni, ahol lehetséges. A legtöbb esetben a szegmentálás és az elkülönítés az adatkapcsolati rétegből és az alkalmazás rétegével együtt érvényes. érzékeny környezetekben azonban célszerű lehet fizikai elkülönítést megadni. A gazdagép-alapú és a hálózatra kiterjedő mértékeket kiegészítő módon kell üzembe helyezni, és központilag figyelni kell. A tűzfal vagy a biztonsági berendezés csak akkor használható, ha az egyetlen biztonsági mérték nem elegendő. |_ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása_|
|**Használja a legalacsonyabb jogosultsági szintű és a szükséges**alapelveket. Ha egy gazdagépnek, szolgáltatásnak vagy hálózatnak nem kell kommunikálnia egy másik gazdagéptel, szolgáltatással vagy hálózattal, nem szabad engedélyezni a következőt:. Ha egy gazdagép, szolgáltatás vagy hálózat csak bizonyos portokkal vagy protokollokkal kommunikál egy másik gazdagéptel, szolgáltatással vagy hálózattal, akkor minden más portot vagy protokollt le kell tiltani. Ezen alapelvek hálózaton keresztüli elfogadásával kiegészíthető a felhasználói jogosultságok minimalizálása, és jelentősen növelhető a környezet általános biztonsági helyzete. |_ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása_|
|A **gazdagépek és hálózatok elkülönítése az üzleti műveleteknek való érzékenységük vagy kritikusság alapján**. Az elkülönítés a különböző biztonsági besorolások, biztonsági tartományok, illetve az egyes gazdagépek vagy hálózatok rendelkezésre állási/integritási követelményeitől függően különböző hardverek vagy platformok használatával érhető el. Külön felügyeleti hálózatok, valamint a sávon kívüli felügyeleti hálózatok fizikai elkülönítése érzékeny környezetekben. |_ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása_|
|**Azonosítsa, hitelesítse és engedélyezze az összes entitás hozzáférését az összes többi entitáshoz**. Minden felhasználónak, gazdagépnek és szolgáltatásnak csak a rájuk vonatkozó feladatai vagy funkciói számára szükséges, hogy csak a többi felhasználóra, gazdagépre és szolgáltatásra korlátozza a hozzáférését. Minden olyan örökölt vagy helyi szolgáltatást, amely az azonosítás, a hitelesítés és az engedélyezési szolgáltatások erősségének megkerülését vagy visszalépését eredményezi, le kell tiltani, és a használatukat szigorúan figyelni kell. |_ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása_|
|A letiltási **lista helyett a hálózati forgalom engedélyezési listájának implementálása**. Csak az ismert jó hálózati forgalom (azaz az azonosított, hitelesített és engedélyezett) elérésének engedélyezése, nem pedig az ismert hibás hálózati forgalomhoz való hozzáférés (például egy adott címe vagy szolgáltatás blokkolása) engedélyezése. Az engedélyezési lista kiváló biztonsági házirendet eredményez a feketelistára helyezéséhez, és jelentősen javítja a szervezet kapacitását a lehetséges hálózati behatolások észlelésére és értékelésére. |_ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása_|
|

Ez a cikk információkat és javaslatokat tartalmaz arról, hogy ezek a követelmények hogyan teljesíthetők az Azure-ban üzembe helyezett rendszerekben a szolgáltatásként (IaaS) és a platform szolgáltatásként (Pásti) való használatával. Emellett olvassa el a [kimenő forgalom szabályozása az Azure Ausztráliában](gateway-egress-traffic.md) című cikket, amely teljes mértékben megértette az Azure-on belüli hálózati forgalom szabályozását.

## <a name="architecture"></a>Architektúra

Ha részt vesz a hálózati biztonság kialakításában vagy megvalósításában, valamint a forgalom szabályozása terén, először meg kell ismernie, hogyan működik a bejövő hálózati forgalom az Azure-ban a IaaS és a Péter-n keresztül. Ez a szakasz áttekintést nyújt azokról a lehetséges belépési pontokról, amelyekben a hálózati forgalom elérheti az Azure-ban üzemeltetett erőforrásokat, valamint a forgalom korlátozására és szabályozására rendelkezésre álló biztonsági vezérlőket. Ezen összetevők mindegyikét részletesen tárgyaljuk az útmutató hátralévő fejezeteiben.

### <a name="architecture-components"></a>Architektúra-összetevők

Az itt látható építészeti diagram megjeleníti azokat a lehetséges útvonalakat, amelyekkel a hálózati forgalom kapcsolódhat az Azure-ban üzemeltetett szolgáltatásokhoz. Ezek az összetevők az Azure-ba, a IaaS bejövő forgalomra, a Pásti beérkező adatokra és a biztonsági szabályozásra vannak osztva attól függően, hogy milyen funkciót biztosítanak a bejövő forgalom számára.

![Architektúra](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure-összetevők

|Összetevő | Leírás|
|---|---|
|**DDoS Protection** | Az elosztott szolgáltatásmegtagadási (DDoS) támadások megpróbálnak kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak. Az Azure magában foglalja az Azure platformon automatikusan DDoS Protectiont, és további enyhítő képességeket biztosít, amelyek részletesebb szabályozást tesznek lehetővé az egyes alkalmazásokhoz.|
| **Traffic Manager** | Az Azure Traffic Manager egy tartománynévrendszer (DNS) alapú forgalom terheléselosztó, amely optimálisan osztja el a forgalmat az Azure-régiók között, miközben magas rendelkezésre állást és válaszadást biztosít. A Traffic Manager a DNS használatával irányítja az ügyfelek kérelmeit a legmegfelelőbb végpontra a forgalom-útválasztási módszer és a végpontok állapota alapján.|
| **ExpressRoute** | A ExpressRoute egy dedikált hálózati kapcsolatok a Microsoft Cloud Services fogyasztásához. A szolgáltatás egy kapcsolati szolgáltatón keresztül lett kiépítve, és nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot kínál, mint az interneten keresztüli szokásos kapcsolatok. A ExpressRoute áramkör a helyszíni infrastruktúra és a Microsoft Cloud Services közötti logikai kapcsolatot jelenti a kapcsolat szolgáltatóján keresztül.|
| **Privát ExpressRoute** | A ExpressRoute privát társítása a helyszíni környezet és a magán Azure-beli virtuális hálózatok közötti kapcsolat. A privát kapcsolat lehetővé teszi a virtuális hálózatokon belül üzembe helyezett Azure-szolgáltatások, például a Virtual Machines elérését. A privát kapcsolaton keresztül elért erőforrások és virtuális hálózatok a szervezet alapvető hálózatának kiterjesztését jelentik. A privát kapcsolatok a privát IP-címek használatával kétirányú kapcsolatot biztosítanak a helyszíni hálózat és az Azure-beli virtuális hálózatok között.|
| **ExpressRoute Microsoft-partnerek** | A Microsoft ExpressRoute a helyszíni környezet és a Microsoft és az Azure nyilvános szolgáltatásai közötti kapcsolat. Ez magában foglalja az Office 365, a Dynamics 365 és az Azure Pásti szolgáltatásokhoz való kapcsolódást. A társítás a szervezet vagy a kapcsolat szolgáltatója tulajdonában lévő nyilvános IP-címeken keresztül valósul meg. Alapértelmezés szerint egyetlen szolgáltatás sem érhető el a Microsoft-ExpressRoute keresztül, a szervezetnek pedig a szükséges szolgáltatásokat kell választania. Ez a folyamat ezután kapcsolatot biztosít ugyanahhoz a végpontokhoz, amelyek elérhetők az interneten.|
|

### <a name="iaas-ingress-components"></a>IaaS-bejövő összetevők

|Összetevő | Leírás|
|---|---|
|**Hálózati adapter** | A hálózati adapterek az Azure-ban található erőforrások. Csatolva van egy virtuális géphez, és a hozzá társított alhálózatból nem internetre irányítható IP-címet rendelt hozzá. Ez az IP-cím dinamikusan vagy statikusan van hozzárendelve Azure Resource Manageron keresztül.|
|**Alhálózat** | Az alhálózat egy IP-címtartomány, amely egy VNet belül jön létre. A hálózati szegmentálás VNet belül több alhálózat is létrehozható.|
| **Virtual Network (VNet)** | A VNet az Azure-ban található alapvető erőforrás, amely platformot és határt biztosít az erőforrások üzembe helyezéséhez és a kommunikáció engedélyezéséhez. A VNet egy Azure-régióban található, és meghatározza az IP-címtartomány és az útválasztási határokat a VNet integrált erőforrásai, például a Virtual Machines számára.|
| **Virtuális hálózatok közötti társviszony** | A VNet-társítás egy olyan Azure-beli konfigurációs beállítás, amely lehetővé teszi a két virtuális hálózatok közötti közvetlen kommunikációt anélkül, hogy Virtual Network átjáróra lenne szükség. Miután megtörtént a kapcsolat, a két virtuális hálózatok közvetlenül kommunikálhat, és további beállításokkal szabályozhatja Virtual Network átjárók és egyéb átviteli lehetőségek használatát.|
| **Nyilvános IP-cím** | A nyilvános IP-cím egy olyan erőforrás, amely a Microsoft tulajdonában lévő nyilvános, internetre irányítható IP-címeket foglal le a megadott régióból a virtuális hálózaton való használatra. Egy adott hálózati adapterhez társítható, amely lehetővé teszi, hogy az erőforrás elérhető legyen az internetről, a ExpressRoute és a Péter rendszerből.|
| **ExpressRoute-átjáró** | Az ExpressRoute-átjáró egy Virtual Network lévő objektum, amely kapcsolatot és útválasztást biztosít a Virtual Network a helyszíni hálózatokhoz egy ExpressRoute-áramkörön lévő privát társon keresztül.|
| **VPN Gateway** | A VPN Gateway a Virtual Network egy olyan objektuma, amely titkosított alagutat biztosít egy Virtual Network egy külső hálózatnak. A titkosított alagút lehet a helyek közötti kétirányú kommunikáció egy helyszíni környezettel, más virtuális hálózattal vagy felhőalapú környezettel, illetve pont – hely kapcsolattal egyetlen végponttal való kommunikációhoz.|
| **Pásti VNet-integráció** | Számos, a (Virtual Network) rendszerbe beépíthető, vagy azokba integrált, nagyszámú Pásti-képesség is telepíthető. Néhány Péter-képesség teljes mértékben integrálható egy VNet, és csak privát IP-címeken keresztül érhető el. Mások, például a Azure Load Balancer és az Azure Application Gateway rendelkezhetnek külső interfésszel egy nyilvános IP-címmel és egy belső interfésszel, amely a virtuális hálózaton belül magánhálózati IP-címmel rendelkezik. Ebben az esetben a forgalom a Virtual Network a Pásti funkcióval beáramlik a ba.|
|

### <a name="paas-ingress-components"></a>Péter bejövő összetevői

|Összetevő | Leírás|
|---|---|
|**Állomásnév** | Az Azure Pásti képességeit az erőforrás létrehozásakor hozzárendelt egyedi nyilvános állomásnév azonosítja. Ezt a gazdagépet ezután egy nyilvános DNS-tartományba regisztrálja, ahol egy nyilvános IP-címhez is feloldható.|
|**Nyilvános IP-cím** | A VNet integrált konfigurációban való üzembe helyezése nélkül az Azure Pásti-funkciók nyilvános, internetre irányítható IP-címen keresztül érhetők el. Ez a címe az adott erőforrásokhoz, például egy nyilvános Load Balancerhoz is hozzárendelhető, vagy egy adott képességhez társítható, amely több példányhoz (például Storage vagy SQL) megosztott belépési ponttal rendelkezik. Ez a nyilvános IP-cím elérhető az internetről, ExpressRoute vagy IaaS nyilvános IP-címekről az Azure gerinces hálózaton keresztül.|
|**Szolgáltatásvégpontok** | A szolgáltatási végpontok közvetlen, magánhálózati kapcsolatokat biztosítanak egy Virtual Networkról egy adott Pásti-képességre. A szolgáltatás-végpontok, amelyek csak a Pásti-funkciók egy részhalmaza számára érhetők el, nagyobb teljesítményt és biztonságot biztosítanak a VNet-hez hozzáférő erőforrásokhoz.|
|

### <a name="security-controls"></a>Biztonsági vezérlők

|Összetevő | Leírás|
|---|---|
|**Hálózati biztonsági csoportok (NSG)** | A NSG az Azure-beli virtuális hálózati erőforrások felé és kívülre irányítja a forgalmat. A NSG szabályokat alkalmazhat az engedélyezett vagy megtagadott adatforgalomra, amely magában foglalja az Azure-on belüli, valamint az Azure és a külső hálózatok, például a helyszíni vagy az Internet közötti adatforgalmat. A NSG a virtuális hálózaton belüli alhálózatokra vagy az egyes hálózati adapterekre vonatkoznak.|
|**Péter-tűzfal** | Számos Pásti-képesség, például a Storage és az SQL egy beépített tűzfallal rendelkezik az adott erőforrás felé irányuló hálózati forgalom szabályozásához. Szabályok hozhatók létre az adott IP-címekről és/vagy virtuális hálózatokról érkező kapcsolatok engedélyezéséhez vagy megtagadásához.|
|**Pásti hitelesítés és Access Control** | A biztonság rétegzett megközelítésének részeként a Péter-képességek több mechanizmust biztosítanak a felhasználók hitelesítéséhez és a jogosultságok és a hozzáférés szabályozásához.|
|**Azure Policy** | Azure Policy egy Azure-szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére szolgál. Ezek a házirendek szabályok segítségével szabályozzák az üzembe helyezhető erőforrások típusát és az erőforrások konfigurációját. A szabályzatok használatával kényszeríthető a megfelelőség, ha megakadályozza az erőforrások telepítését, ha azok nem felelnek meg a követelményeknek, vagy a megfelelőségi állapot jelentésének figyelésére is használható.|
|

## <a name="general-guidance"></a>Általános útmutatás

Az Azure-on belüli biztonságos megoldások kialakításához és létrehozásához kritikus fontosságú a hálózati forgalom megismerése és szabályozása, hogy csak az azonosított és engedélyezett kommunikációt lehessen megállapítani. Ennek az útmutatónak a célja, valamint az egyes összetevőkre vonatkozó útmutató a későbbi szakaszokban a _ASCs-védelemben ismertetett alapelvek alkalmazásához használható eszközöket és szolgáltatásokat ismerteti. A hálózat szegmentálásának és elkülönítésének_ megvalósítása az Azure-beli számítási feladatok között. Ez részletesen ismerteti, hogyan hozhat létre virtuális architektúrát az erőforrások biztonságossá tételéhez, ha nem lehet alkalmazni a helyszíni környezetben lehetséges hagyományos fizikai és hálózati vezérlőket.

### <a name="specific-focus-areas"></a>Konkrét fókusz területek

* A belépési pontok számának korlátozása virtuális hálózatokra
* A nyilvános IP-címek számának korlátozása
* Érdemes lehet sugaras hálózati kialakítást használni a virtuális hálózatokhoz a Microsoft Virtual adatközpont (VDC) dokumentációjában leírtaknak megfelelően.
* Beépített biztonsági képességekkel rendelkező termékek használata az internetről érkező bejövő kapcsolatokhoz (például Application Gateway, API Gateway, Network Virtual Appliances)
* A csak a rendszerfunkciókhoz szükséges kommunikációs folyamatok korlátozása a Péter-képességekre
* A Pásti üzembe helyezése a VNet integrált konfigurációjában a nagyobb elkülönítés és szabályozás érdekében
* Rendszerek konfigurálása titkosítási mechanizmusok használatára a ASCS-fogyasztói útmutató és az ISM-vel összhangban
* Identitás-alapú védelem, például hitelesítés és szerepköralapú hozzáférés-vezérlés használata a hagyományos hálózati vezérlőkön kívül
* ExpressRoute megvalósítása a helyszíni hálózatokkal való kapcsolathoz
* Virtuális magánhálózatok megvalósítása a felügyeleti forgalomhoz és a külső hálózatokkal való integrációhoz
* A Azure Policy használata a régiók és erőforrások csak azokra a korlátozására, amelyek szükségesek a rendszer működéséhez
* Az internetről elérhető erőforrások alapkonfigurációjának biztonsági beállításainak betartatása Azure Policy használatával

### <a name="additional-resources"></a>További források

|Resource | Összekapcsolás|
|---|---|
|Ausztrál szabályozási és szabályzatok megfelelőségi dokumentumai, beleértve a fogyasztói útmutatást|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure virtuális adatközpont|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|ASCS hálózati szegmentálás|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|ASCS Cloud Security bérlők számára| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ASCS-információ biztonsági kézikönyve|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Összetevő-útmutatás

Ez a szakasz további útmutatást nyújt azokról az összetevőkről, amelyek az Azure-ban üzembe helyezett rendszerek forgalmának beléptetéséhez szükségesek. Az egyes szakaszok az adott összetevő célját ismertetik, és a dokumentációra és a konfigurációs útmutatókra mutató hivatkozásokat tartalmaznak, amelyek segítséget nyújtanak a tervezési és a létrehozási tevékenységekhez.

## <a name="azure"></a>Azure

Az Azure-ban lévő erőforrásokkal folytatott minden kommunikáció a Microsoft által karbantartott hálózati infrastruktúrán keresztül történik, amely csatlakozási és biztonsági funkciókat biztosít. A Microsoft az Azure platform és a hálózati infrastruktúra védelme érdekében automatikusan helyez el számos védelmet, és további képességeket biztosít az Azure-ban elérhető szolgáltatásokként a hálózati forgalom szabályozása és a hálózati szegmentálás kialakítása érdekében. szegregáció.

### <a name="ddos-protection"></a>DDoS Protection

Az internetről elérhető erőforrások érzékenyek a DDoS-támadásokra. A támadások elleni védelem érdekében az Azure a DDoS-védelmet alapszintű és standard szinten biztosítja.

Az alapszintű szolgáltatás automatikusan engedélyezve van az Azure platform részeként, beleértve a folyamatos adatforgalom figyelését, valamint a gyakori hálózati szintű támadások valós idejű enyhítését, amely a Microsoft online szolgáltatások által használt védelmi adatokat biztosítja. Az Azure globális hálózatának teljes skálája felhasználható a különböző régiók közötti támadási forgalom elosztására és enyhítésére. A védelem az IPv4-és IPv6-alapú Azure nyilvános IP-címekhez van megadva

A standard megoldás további kockázatcsökkentő képességeket biztosít az alapszintű szolgáltatási szinten, amely kifejezetten az Azure Virtual Network-erőforrásokhoz van hangolva. A védelmi szabályzatok a dedikált forgalom monitorozásával és a gépi tanulási algoritmusokkal vannak összehangolva. A védelem az IPv4 Azure nyilvános IP-címeihez van megadva.

|Resource|Összekapcsolás|
|---|---|
|Azure DDoS Protection áttekintése|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Azure DDoS – ajánlott eljárások|[https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices)|
|DDoS Protection kezelése|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Traffic Manager

A Traffic Manager a bejövő forgalom kezeléséhez szükséges, amely szabályozza, hogy az alkalmazás mely végpontokon fogadjon kapcsolatokat. A számítógépes biztonsági támadás miatti rendszerek vagy alkalmazások rendelkezésre állásának elvesztése, illetve a szolgáltatások rendszerbiztonság utáni helyreállításának megakadályozása érdekében Traffic Manager segítségével átirányíthatja a forgalmat a működésre, az elérhető alkalmazások példányaira.

|Resource|Összekapcsolás|
|---|---|
|A Traffic Manager áttekintése | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Vész-helyreállítás Azure DNS és Traffic Manager útmutató segítségével | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

A ExpressRoute használatával saját elérési utat hozhat létre helyszíni környezetből az Azure-ban üzemeltetett rendszerekre. Ez a kapcsolat nagyobb megbízhatóságot és garantált teljesítményt biztosíthat a hálózati kommunikáció fokozott adatvédelme érdekében. Az Express Route lehetővé teszi a nemzetközösségi entitások számára a helyszíni környezettől érkező bejövő forgalom vezérlését, valamint a szervezet által a bejövő tűzfalszabályok és hozzáférés-vezérlési listák használatára vonatkozó dedikált címek meghatározását.

|Resource | Összekapcsolás|
|---|---|
|ExpressRoute – áttekintés | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|ExpressRoute-kapcsolati modellek | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Privát ExpressRoute

A privát felügyelet olyan mechanizmust biztosít, amellyel a helyszíni környezeteket az Azure-ba csak privát IP-címek használatával bővítheti. Ez lehetővé teszi, hogy a Commonwealth-entitások integrálják az Azure-beli virtuális hálózatokat és címtartományt a meglévő helyszíni rendszerekkel és szolgáltatásokkal. A privát kapcsolatok garantálják, hogy a ExpressRoute közötti kommunikáció csak a szervezet által engedélyezett virtuális hálózatokra vonatkozik. Ha privát társat használ, a Commonwealth-entitásoknak az Azure VPN Gateway helyett hálózati virtuális berendezéseket (NVA) kell alkalmazniuk, hogy a ASCS fogyasztói útmutatója megköveteli a biztonságos VPN-kommunikációt a helyszíni hálózatokhoz.

|Resource | Összekapcsolás|
|---|---|
|A ExpressRoute privát társának áttekintése | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute – útmutató | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute Microsoft-partnerek

A Microsoft-partnerek nagy sebességű, kis késleltetésű kapcsolatot biztosítanak a Microsoft nyilvános szolgáltatásaihoz anélkül, hogy az interneten keresztül kellene bejárniuk. Ez nagyobb megbízhatóságot, teljesítményt és adatvédelmet biztosít a kapcsolatok számára. Az útválasztási szűrők használatával a nemzetközösségi entitások csak a számukra szükséges Azure-régiókra korlátozhatják a kommunikációt, de ez magában foglalja más szervezetek által üzemeltetett szolgáltatásokat is, és további szűrési vagy ellenőrzési képességeket is szükségessé tehet a helyszíni környezet és a Microsoft.

A nemzetközösségi entitások használhatják a társítási kapcsolaton keresztül létrehozott dedikált nyilvános IP-címeket, hogy egyedileg azonosítsák a helyszíni környezetet a tűzfalakon és a hozzáférés-vezérlési listán a Pásti-funkciókon belül.

Másik lehetőségként a Commonwealth-entitások a ExpressRoute Microsoft-partnerként használhatják a VPN-kapcsolatot az Azure VPN Gateway használatával. Ebben a modellben nincs aktív kommunikáció a belső helyszíni hálózatról az Azure Public Services-be a ExpressRoute-on keresztül, de a magánhálózati virtuális hálózatokon keresztüli biztonságos kapcsolat a ASCS fogyasztói útmutatójának megfelelően érhető el.

|Resource | Összekapcsolás|
|---|---|
|ExpressRoute Microsoft-partnerek áttekintése | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute Microsoft-partneri útmutató | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS bejövő forgalom

Ez a szakasz a IaaS-összetevőkre irányuló bejövő forgalom szabályozásához nyújt útmutatást. A IaaS Virtual Machines és más számítási erőforrásokat is tartalmaz, amelyeket üzembe helyezhet és kezelhet az Azure-Virtual Network belül. Ahhoz, hogy a IaaS-alapú rendszereken megérkeznek a forgalom, belépési ponttal kell rendelkeznie a Virtual Networkhoz, amely nyilvános IP-címen, Virtual Network átjárón vagy Virtual Network társ-létesítési kapcsolaton keresztül hozható kapcsolatba.

### <a name="network-interface"></a>Hálózati adapter

A hálózati adapterek a virtuális gépek felé irányuló összes forgalom bejövő pontjai. A hálózati adapterek lehetővé teszik az IP-címzés konfigurálását, és a NSG alkalmazására, illetve egy hálózati virtuális berendezésen keresztüli útválasztási forgalom használatára is használhatók. A Virtual Machines hálózati adaptereit úgy kell megtervezni és konfigurálni, hogy megfeleljenek az általános hálózati szegmentálási és elkülönítési célkitűzéseknek.

|Resource | Összekapcsolás|
|---|---|
|Hálózati adapter létrehozása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|Hálózati adapter IP-címzése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

Az alhálózatok a hálózati szegmentálás és az Azure-beli elkülönítés kulcsfontosságú összetevői. Az alhálózatok hasonló módon használhatók a rendszerek közötti elkülönítés biztosításához. A NSG az alhálózatokra is alkalmazható, hogy a bejövő kommunikációs folyamatokat csak a rendszerfunkciókhoz szükséges rendszerekre korlátozza. Az alhálózatok a tűzfalszabályok és a hozzáférés-vezérlési listák forrás-és célhelyként is használhatók, és konfigurálhatók a szolgáltatás-végpontok számára a Pásti-funkciókhoz való kapcsolódás biztosításához.

|Resource | Összekapcsolás|
|---|---|
|Virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Virtuális hálózat (VNet)

A virtuális hálózatok az Azure-ban a hálózatkezelés egyik alapvető építőeleme. A virtuális hálózatok határozzák meg az IP-címtartomány és az útválasztási határokat a különböző rendszerekben való használathoz. A virtuális hálózatok alhálózatokra vannak osztva, és a Virtual Network belüli összes alhálózatnak közvetlen hálózati útvonala van egymáshoz. Virtual Network átjárók (ExpressRoute vagy VPN) használatával a Virtual Networkon belüli rendszerek elérhetővé tehetők a helyszíni és külső környezetekben. A virtuális hálózatok megismerése és a kapcsolódó konfigurációs paraméterek és útválasztás létfontosságú a bejövő hálózati forgalom megismerése és szabályozása szempontjából.

|Resource | Összekapcsolás|
|---|---|
|Virtual Networks – áttekintés | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|A virtuális hálózatok útmutatójának megtervezése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Virtual Network rövid útmutató létrehozása | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNet-társítás

A VNet-társítás a két virtuális hálózat közötti közvetlen kommunikációs útvonal biztosítására szolgál. Ha létrejött a társítás, az egyik Virtual Network gazdagépei nagy sebességű útválasztási útvonallal rendelkeznek közvetlenül egy másik Virtual Network található gazdagépekhez. A NSG továbbra is a normál és a speciális konfigurációs paraméterek használatával határozható meg, hogy Virtual Network átjárón vagy más külső rendszereken keresztül történő kommunikáció engedélyezett-e.

|Resource | Összekapcsolás|
|---|---|
|Virtual Network peering – áttekintés |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Virtuális hálózati társak létrehozása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>Nyilvános IP-cím a VNET

A nyilvános IP-címek a Virtual Networkban üzembe helyezett szolgáltatások bejövő kommunikációs útvonalának biztosítására szolgálnak. A nemzetközösségi entitásoknak körültekintően kell megtervezniük a nyilvános IP-címek kiosztását, és csak olyan erőforrásokhoz kell azokat hozzárendelni, amelyeken valódi követelmény van. Általános tervezési gyakorlatként a nyilvános IP-címeket olyan beépített biztonsági képességekkel rendelkező erőforrásokhoz kell hozzárendelni, mint a Application Gateway vagy a hálózati virtuális berendezések, amelyek biztonságos, ellenőrzött nyilvános belépési pontot biztosítanak a Virtual Network számára.

|Resource | Összekapcsolás|
|---|---|
|Nyilvános IP-címek áttekintése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Nyilvános IP-cím létrehozása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>ExpressRoute-átjáró

A ExpressRoute-átjárók a helyszíni környezetből beléptetési pontot biztosítanak, és a biztonsági, rendelkezésre állási, pénzügyi és teljesítménybeli követelmények kielégítése érdekében kell őket telepíteni. A ExpressRoute-átjárók meghatározott hálózati sávszélességet biztosítanak, és használati költségekkel járnak a telepítés után. A virtuális hálózatok csak egy ExpressRoute-átjáróval rendelkezhetnek, de ez több ExpressRoute-áramkörhöz is csatlakoztatható, és több virtuális hálózat is kihasználható a VNet-alapú kapcsolaton keresztül, így több virtuális hálózat is megoszthatja a sávszélességet és a kapcsolatot. Ügyelni kell arra, hogy az útválasztást a helyszíni környezetek és a virtuális hálózatok között a ExpressRoute-átjárók használatával konfigurálja, hogy a végpontok közötti kapcsolat az ismert, ellenőrzött hálózati beléptetési pontokon keresztül biztosítható legyen. A ExpressRoute átjárót használó Nemzetközösségi entitásoknak is telepíteniük kell a hálózati virtuális berendezéseket a VPN-kapcsolat létesítéséhez a helyszíni környezethez, hogy megfeleljenek a ASCS felhasználói útmutatójának.

|Resource | Összekapcsolás|
|---|---|
|A ExpressRoute-átjáró áttekintése | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Az ExpressRoute virtuális hálózati átjáróinak konfigurálása | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Az Azure VPN Gateway egy külső hálózatról bejövő hálózati pontot biztosít helyek közötti vagy pont – hely kapcsolatokhoz. A VPN-átjárók meghatározott hálózati sávszélességet biztosítanak, és a telepítés után használati költségekkel járnak. Az VPN Gatewayt használó Nemzetközösségi entitásoknak biztosítaniuk kell, hogy az a ASCS-használati útmutatónak megfelelően legyen konfigurálva. A virtuális hálózatok csak egy VPN Gateway rendelkezhetnek, de ez több bújtatással is konfigurálható, és több virtuális hálózat is kihasználható a VNet-társításon keresztül, így több virtuális hálózat is megoszthatja a sávszélességet és a kapcsolatot. A VPN-átjárók az interneten keresztül vagy a ExpressRoute keresztül hozhatók a Microsoft-partneri kapcsolaton keresztül.

|Resource | Összekapcsolás|
|---|---|
|VPN Gateway áttekintése | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|A VPN Gateway tervezése és kialakítása | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|VPN Gateway konfiguráció az ausztráliai kormányzati szervek számára|[Az ausztrál kormányzati szervek számára szükséges IPSEC-konfiguráció](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Pásti VNet-integráció

A Pásti kihasználása nagyobb funkcionalitást és rendelkezésre állást biztosít, és csökkentheti a felügyeleti terhelést, azonban megfelelő védelemmel kell rendelkeznie. A szabályozás növeléséhez, a hálózati szegmentálás betartatásához, illetve az alkalmazások és szolgáltatások biztonságos bejövő belépési pontjának biztosításához számos, a Virtual Network segítségével integrálható, több Péter-képesség.

A biztonságos belépési pont biztosításához a Péter-képességek, például a Application Gateway konfigurálhatók egy külső, nyilvános felülettel és egy belső, privát kezelőfelülettel az Application Services szolgáltatással való kommunikációhoz. Ez megakadályozza, hogy az alkalmazás-kiszolgálókat nyilvános IP-címekkel kell konfigurálni, és azokat külső hálózatokra lehessen telepíteni.

Ha a rendszer vagy az alkalmazás architektúrájának integrált részeként szeretné használni a Pástit, a Microsoft több mechanizmust biztosít a Pásti Virtual Networkba való telepítéséhez. Az üzembe helyezési módszer korlátozza a bejövő hozzáférést a külső hálózatokból, például az internetről, miközben a kapcsolódást és a belső rendszerekkel és alkalmazásokkal való integrációt biztosítja. Ilyenek például a App Service környezetek, az SQL felügyelt példányai és egyebek.

|Resource | Összekapcsolás|
|---|---|
|Virtuális hálózati integráció az Azure-szolgáltatásokhoz | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Az alkalmazás integrálása Azure Virtual Network útmutatóval | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>Péter bejövő forgalom

A Péter képességei lehetővé teszik a megnövekedett képességet és az egyszerűbb felügyeletet, de a hálózati szegmentálásra és elkülönítésre vonatkozó követelmények kezelése terén összetett szempontokat is bevezetnek. A Pásti-képességek általában nyilvános IP-címekkel vannak konfigurálva, és az internetről érhetők el.  A Pásti-képességeket használó rendszerek kiépítésekor ügyelni kell arra, hogy azonosítsa a rendszer és a létrehozott hálózati biztonsági szabályok közötti összes szükséges kommunikációs folyamatot, hogy csak ez a kommunikáció engedélyezzen. A biztonság védelme érdekében a Pásti-képességeket titkosítással, hitelesítéssel és megfelelő hozzáférés-vezérléssel és engedélyekkel kell konfigurálni.  

### <a name="hostname"></a>Állomásnév

A Pásti-képességeket a gazdagépek egyedileg azonosítják, hogy ugyanazon szolgáltatás több példánya is ugyanazon a nyilvános IP-címen legyen tárolva. Egyedi állomásnevek vannak megadva, amikor létrejönnek az erőforrások, és léteznek a Microsoft tulajdonában lévő DNS-tartományokon belül. A jogosult szolgáltatások egyedi állomásneve az alkalmazás szintű szűrési képességekkel rendelkező biztonsági eszközökön belül is felhasználható. Bizonyos szolgáltatások igény szerint egyéni tartományokkal is konfigurálhatók.

|Resource | Összekapcsolás|
|---|---|
|Az Azure-szolgáltatások által használt számos nyilvános névtér a Get-AzureRMEnvironment parancs futtatásával szerezhető be a Powershellen keresztül. | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Egyéni tartománynév konfigurálása Azure Cloud Service-hez | App Services és mások rendelkezhetnek egyéni tartományokkal[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>Nyilvános IP-cím: Péter

A Péter-képességekhez tartozó nyilvános IP-címek a szolgáltatás által üzemeltetett vagy üzembe helyezett régió alapján vannak lefoglalva. A hálózati szegmentáláshoz és az Azure Virtual Networks, a Pásti és a ExpressRoute, valamint az Internet-kapcsolatokhoz kapcsolódó megfelelő hálózati biztonsági szabályok és útválasztási topológia kiépítéséhez a nyilvános IP-címek kiosztásának és a régióknak a megértése szükséges. Az Azure IP-címeket foglal le az egyes Azure-régiók számára lefoglalt készletből. A Microsoft az egyes régiókban használt címeket letölti, amelyek rendszeres és ellenőrzött módon frissülnek. Az egyes régiókban elérhető szolgáltatások is gyakran változnak, mivel új szolgáltatások jelennek meg, vagy a szolgáltatások telepítése szélesebb körben történik. A nemzetközösségi entitásoknak rendszeresen át kell tekinteniük ezeket az anyagokat, és az Automation segítségével szükség szerint karbantarthatók a rendszerek. Az egyes régiókban üzemeltetett szolgáltatások adott IP-címei a Microsoft ügyfélszolgálatával szerezhetők be.

|Resource | Összekapcsolás|
|---|---|
|Microsoft Azure adatközpont IP-tartományai | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Azure-szolgáltatások régiónként | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Ausztrália – Közép-Ausztrália – Közép-2, Ausztrália – Kelet, Ausztrália – délkeleti & termékek = mind](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

Virtual Network szolgáltatási végpontok nagy sebességű, privát bejövő hálózati kapcsolattal rendelkeznek a Virtual Networkn belüli alhálózatok adott, a Pásti-képességekkel való felhasználásához. A Pásti-képesség teljes hálózati szegmentálása és elkülönítése érdekében úgy kell konfigurálni a Pásti funkciót, hogy csak a szükséges virtuális hálózatokról fogadjon kapcsolatokat. Nem minden a Pásti-funkció támogatja a szolgáltatási végpontokat és a hagyományos IP-cím alapú szabályokat tartalmazó tűzfalszabályok kombinációját, ezért ügyelni kell az alkalmazások működéséhez és felügyeletéhez szükséges kommunikációs folyamat megértéséhez annak érdekében, hogy ezeknek a biztonsági ellenőrzéseknek a megvalósítása ne befolyásolja a szolgáltatás rendelkezésre állását.

|Resource | Összekapcsolás|
|---|---|
|Szolgáltatási végpontok áttekintése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Oktatóanyag |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Biztonság

A hálózati szegmentálás és a IaaS-és a Pásti-elkülönítési vezérlők megvalósítása a képességek biztonságossá tételével, valamint a felügyelt kommunikációs útvonalak olyan rendszerekkel való megvalósításával valósul meg, amelyek kommunikálni fognak a képesség.

A megoldások az Azure-ban való tervezése és kialakítása olyan logikai architektúra létrehozására szolgál, amely a teljes Azure-beli jelenlétben a hálózati erőforrások megértéséhez, szabályozásához és figyeléséhez szükséges. Ez a logikai architektúra az Azure platformon belül definiált szoftver, amely a hagyományos hálózati környezetekben megvalósított fizikai hálózati topológia helyét veszi igénybe.

A létrehozott logikai architektúrának meg kell adnia a használhatósághoz szükséges funkciókat, azonban meg kell adnia a biztonság és integritás számára szükséges láthatóságot és szabályozást is.

Ennek az eredménynek a megvalósítása a szükséges hálózati szegmentálási és elkülönítési eszközök megvalósításán alapul, de a hálózati topológia védelméhez és érvényesítéséhez, valamint ezeknek az eszközöknek a megvalósításához is.

Az ebben az útmutatóban található információk segítségével azonosíthatók a bejövő forgalomnak engedélyezett források, valamint a forgalom további szabályozásának és korlátozásának módjai.

### <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG)

A NSG egy alhálózat vagy egy adott hálózati adapter számára engedélyezett bejövő és kimenő forgalom megadására szolgálnak. A NSG konfigurálásakor a nemzetközösségi entitásoknak engedélyezési módszert kell használniuk, amelyben a szabályok úgy vannak konfigurálva, hogy engedélyezzék a szükséges forgalmat egy olyan alapértelmezett szabállyal, amely nem felel meg az adott engedélyezési utasításnak. A NSG tervezése és konfigurálása során körültekintően kell eljárni, hogy az összes szükséges bejövő és kimenő forgalom megfelelően legyen rögzítve. Ez magában foglalja az Azure-beli virtuális hálózatokban és a helyszíni környezetben használt összes magánhálózati IP-címtartomány azonosítását és megismerését, valamint a Microsoft bizonyos szolgáltatásait, például a Azure Load Balancer és a Pásti felügyeleti követelményeket. A hálózati biztonsági csoportok kialakításában és megvalósításában részt vevő személyeknek is ismerniük kell a szolgáltatás-címkék és az alkalmazás-biztonsági csoportok használatát a részletes, szolgáltatás-és alkalmazásspecifikus biztonsági szabályok létrehozásához.

|Resource | Összekapcsolás|
|---|---|
|Hálózati biztonság áttekintése | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Hálózati biztonsági csoport létrehozása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>Péter-tűzfal

A Pásti-tűzfal egy hálózati hozzáférés-vezérlési képesség, amely bizonyos Pásti-szolgáltatásokra alkalmazható. Lehetővé teszi az IP-címek szűrését vagy az adott virtuális hálózatok szűrését úgy, hogy az adott Pásti-példányra korlátozza a bejövő forgalmat. A tűzfalat tartalmazó Pásti-funkciók esetében a hálózati hozzáférés-vezérlési házirendeket úgy kell konfigurálni, hogy csak az alkalmazásra vonatkozó követelmények alapján engedélyezzék a szükséges bejövő forgalmat.  

|Resource | Összekapcsolás|
|---|---|
|IP-tűzfalszabályok Azure SQL Database és SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Tárterület hálózati biztonsága | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>Pásti hitelesítés és hozzáférés-vezérlés

A Pásti-képességtől és céljától függően a hálózati vezérlők használatával a hozzáférés korlátozása nem lehetséges vagy nem praktikus. A Pásti számára készült rétegzett biztonsági modell részeként az Azure számos hitelesítési és hozzáférés-vezérlési mechanizmust biztosít a szolgáltatásokhoz való hozzáférés korlátozására, még akkor is, ha a hálózati forgalom engedélyezett. A Pásti-képességekre jellemző hitelesítési mechanizmusok a következők: Azure Active Directory, alkalmazás szintű hitelesítés, valamint megosztott kulcsok vagy hozzáférési aláírások. Ha a felhasználó biztonságosan azonosítható, a szerepköröket a felhasználó által végrehajtható műveletek szabályozására lehet használni. Ezek az eszközök alternatív vagy ingyenes mértékként használhatók a szolgáltatások elérésének korlátozására.

|Resource | Összekapcsolás|
|---|---|
|Adatbázis-hozzáférés szabályozása és biztosítása SQL Database és SQL Data Warehouse számára | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Az Azure Storage szolgáltatásainak engedélyezése | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy az Azure-környezet logikai architektúrájának érvényesítésére és karbantartására szolgáló kulcsfontosságú összetevő. A különböző szolgáltatások és az Azure-szolgáltatásokon keresztül elérhető hálózati forgalom elérési útjai miatt rendkívül fontos, hogy a Commonwealth-entitások tisztában legyenek a környezetében meglévő erőforrásokkal és az elérhető hálózati beléptetési pontokkal. Annak biztosítása érdekében, hogy a nem engedélyezett hálózati beléptetési pontok ne legyenek létrehozva az Azure-környezetben, a nemzetközösségi entitásoknak Azure Policy kell használniuk az üzembe helyezhető erőforrások típusának és az erőforrások konfigurálásának szabályozására. A gyakorlati példákban az erőforrások korlátozása csak a használatra engedélyezett és jóváhagyott, a HTTPS-titkosítás kikényszerítése a Storage szolgáltatásban, valamint az alhálózatokhoz való NSG megkövetelése.

|Resource | Összekapcsolás|
|---|---|
|Azure Policy áttekintése | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Engedélyezett erőforrástípusok – minta házirend | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|HTTPS Storage-fiókhoz tartozó minta házirend biztosítása|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|NSG kényszerítése az alhálózati minta házirendjében| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>További lépések

Tekintse át az [átjáró kimenő forgalmának kezelésével és ellenőrzésével](gateway-egress-traffic.md) foglalkozó cikket, amely részletesen ismerteti a forgalom Azure-környezetből más hálózatokra való kezelését az Azure-beli átjáró-összetevők használatával.
