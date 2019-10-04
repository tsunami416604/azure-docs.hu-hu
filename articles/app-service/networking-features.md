---
title: Hálózati telepítési funkciók – Azure App Service | Microsoft Docs
description: A különböző App Service hálózatkezelési funkciók használata
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 950818d08cb654bad969deaede24231cab9bcbe2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098557"
---
# <a name="app-service-networking-features"></a>Hálózati szolgáltatások App Service

A Azure App Service alkalmazásai több módon is üzembe helyezhetők. Alapértelmezés szerint App Service üzemeltetett alkalmazások közvetlenül az internetről elérhetők, és csak az internetről futtatott végpontok elérésére képesek. Számos ügyfél-alkalmazásnak azonban a bejövő és a kimenő hálózati forgalmat is szabályoznia kell. A App Service több funkciója is rendelkezésre áll, hogy megfeleljenek az igényeknek. A kihívás az adott probléma megoldására szolgáló szolgáltatás ismerete. Ez a dokumentum arra szolgál, hogy segítsen az ügyfeleknek meghatározni, hogy milyen szolgáltatást kell használni bizonyos példa használati esetek alapján.

A Azure App Service két elsődleges központi telepítési típust tartalmaz. Létezik a több-bérlős nyilvános szolgáltatás, amely az ingyenes, közös, alapszintű, standard, prémium és Premiumv2 díjszabási SKU-ban App Service csomagokat üzemeltet. Ebben az esetben az egyetlen bérlői App Service Environment (bemutató), amely az elkülönített SKU-App Service csomagokat közvetlenül az Azure Virtual Networkban (VNet) üzemelteti. A használt funkciók attól függően változnak, hogy a több-bérlős szolgáltatásban vagy egy szolgáltatóban van. 

## <a name="multi-tenant-app-service-networking-features"></a>Több-bérlős App Service hálózatkezelési funkciók 

A Azure App Service egy elosztott rendszer. A bejövő HTTP/HTTPS-kérelmeket kezelő szerepköröket előtér-végpontoknak nevezzük. Az ügyfél munkaterhelését üzemeltető szerepköröket feldolgozónak nevezzük. Egy App Service üzemelő példány összes szerepköre több-bérlős hálózaton található. Mivel a App Service skálázási egységben sok különböző ügyfél található, a App Service hálózat nem csatlakoztatható közvetlenül a hálózathoz. A hálózatok csatlakoztatása helyett az alkalmazások kommunikációjának különböző szempontjait kezelő funkciókra van szükségünk. Az alkalmazásra irányuló kéréseket kezelő funkciók nem használhatók problémák megoldására az alkalmazásból indított hívások során. Hasonlóképpen, az alkalmazással kapcsolatos hívások problémáit megoldó funkciók nem használhatók az alkalmazással kapcsolatos problémák megoldásához.  

| Bejövő funkciók | Kimenő funkciók |
|---------------------|-------------------|
| Alkalmazáshoz rendelt címe | Hibrid kapcsolatok |
| Hozzáférési korlátozások | Átjáró szükséges VNet-integráció |
| Service Endpoints – szolgáltatásvégpont | VNet-integráció (előzetes verzió) |

Ha másként nincs megadva, az összes funkció együtt használható. A különböző problémák megoldásához kombinálhatja a szolgáltatásokat.

## <a name="use-case-and-features"></a>Használati eset és funkciók

A probléma megoldásához bizonyos használati eseteket is használhat.  A megfelelő szolgáltatást esetenként a használati eseten kívüli okok miatt is érdemes használni. A következő bejövő használati esetek azt sugallják, hogyan használhatók a App Service hálózatkezelési funkciói az alkalmazásra irányuló forgalom szabályozásával kapcsolatos problémák megoldásához. 
 
| Bejövő használati esetek | Funkció |
|---------------------|-------------------|
| Az alkalmazás IP-alapú SSL-igényeinek támogatása | alkalmazáshoz rendelt címe |
| Nem megosztott, dedikált bejövő címe az alkalmazáshoz | alkalmazáshoz rendelt címe |
| Az alkalmazáshoz való hozzáférés korlátozása jól meghatározott címekből | Hozzáférési korlátozások |
| Saját alkalmazás közzététele saját IP-VNet | ILB </br> Application Gateway szolgáltatás-végpontokkal |
| Az alkalmazáshoz való hozzáférés korlátozása VNet lévő erőforrásokkal | Service Endpoints – szolgáltatásvégpont </br> ILB |
| Saját alkalmazás közzététele privát IP-VNet | ILB </br> magánhálózati IP-cím bejövő Application Gateway szolgáltatási végpontokkal |
| Az alkalmazás védetté WAF | Application Gateway + ILB </br> Application Gateway szolgáltatás-végpontokkal </br> Azure bejárati ajtó hozzáférési korlátozásokkal |
| A különböző régiókban lévő alkalmazások forgalmának elosztása | Azure bejárati ajtó hozzáférési korlátozásokkal | 
| Azonos régióban lévő forgalom terheléselosztása | Application Gateway szolgáltatás-végpontokkal | 

A következő kimenő használati esetek azt mutatják be, hogyan használhatók a App Service hálózatkezelési funkciói az alkalmazás kimenő hozzáférési igényeinek megoldásához. 

| Kimenő használati esetek | Funkció |
|---------------------|-------------------|
| Egy adott régióban található Azure-Virtual Network erőforrásainak elérése | VNet-integráció </br> ASE |
| Erőforrásokhoz való hozzáférés egy másik régióban lévő Azure-Virtual Network | Átjáró szükséges VNet-integráció </br> VNet-társítás |
| A szolgáltatási végpontokkal védett erőforrások elérése | VNet-integráció </br> ASE |
| Hozzáférés az Azure-hoz nem csatlakozó magánhálózat erőforrásaihoz | Hibrid kapcsolatok |
| Erőforrások elérése ExpressRoute-áramkörök között | VNet-integráció (a jelenleg RFC 1918-címekre korlátozódik) </br> ASE | 


### <a name="default-networking-behavior"></a>Alapértelmezett hálózatkezelési viselkedés

A Azure App Service skálázási egységek számos ügyfelet támogatnak az egyes telepítések során. Az ingyenes és a közös SKU-csomagok a több-bérlős feldolgozók esetében az ügyfelek munkaterheléseit üzemeltetik. Az alapszintű és a fenti csomagok olyan felhasználói munkaterheléseket üzemeltetnek, amelyek csak egy App Service csomagra (ASP) vannak kijelölve. Ha standard App Service csomaggal rendelkezett, akkor a tervben szereplő összes alkalmazás ugyanazon a munkavégzőn fog futni. Ha kibővíti a munkavégzőt, akkor az adott ASP-ben lévő összes alkalmazás replikálva lesz egy új feldolgozón az ASP minden példánya számára. A Premiumv2 használt munkavégzők eltérnek a többi csomaghoz használt munkatársaitól. Minden App Service központi telepítésnek van egy IP-címe, amelyet a rendszer az adott App Service üzemelő alkalmazásokhoz tartozó összes bejövő forgalomhoz használ. A kimenő hívások végrehajtásához azonban 4 – 11 cím van használatban. Ezeket a címeket a App Service üzemelő példány összes alkalmazása osztja meg. A kimenő címek különböző munkavégző típusok alapján különböznek. Ez azt jelenti, hogy az ingyenes, a megosztott, az alapszintű, a standard és a prémium ASP által használt címek eltérnek a Premiumv2 ASP kimenő hívásai által használt címektől. Ha megtekinti az alkalmazás tulajdonságait, láthatja az alkalmazás által használt bejövő és kimenő címeket. Ha egy IP ACL-sel kell lezárnia egy függőséget, használja a possibleOutboundAddresses. 

![Alkalmazás tulajdonságai](media/networking-features/app-properties.png)

App Service a szolgáltatás felügyeletéhez használt végpontok száma.  Ezeket a címeket külön dokumentumban tesszük közzé, és a AppServiceManagement IP-szolgáltatás címkéjén is szerepelnek. A AppServiceManagement címkét csak olyan App Service Environment (bemutató) használja, ahol engedélyezni kell az ilyen forgalmat. A App Service bejövő címeket a AppService IP-szolgáltatás címkéjén követjük nyomon. Nincs olyan IP-szolgáltatás címkéje, amely az App Service által használt kimenő címeket tartalmazza. 

![App Service bejövő és kimenő diagram](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Alkalmazáshoz rendelt címe 

Az alkalmazáshoz rendelt cím funkció az IP-alapú SSL-képesség indaja, és az SSL az alkalmazással való beállításával érhető el. Ez a funkció IP-alapú SSL-hívásokhoz használható, de az alkalmazás csak azt a címet használhatja, amely csak az adott. 

![Alkalmazáshoz rendelt címek diagramja](media/networking-features/app-assigned-address.png)

Ha egy alkalmazáshoz rendelt címeket használ, a forgalom továbbra is ugyanazokat az előtér-szerepköröket használja, amelyek az összes bejövő forgalmat a App Service skálázási egységben kezelik. Az alkalmazáshoz rendelt címeket azonban csak az alkalmazás használja. A szolgáltatás használati esetei a következők:

* Az alkalmazás IP-alapú SSL-igényeinek támogatása
* Olyan dedikált IP-címek beállítása az alkalmazáshoz, amely nem osztható meg semmi mással

Megtudhatja, hogyan állíthat be egy címet az alkalmazásban az [IP-alapú SSL konfigurálására][appassignedaddress]vonatkozó oktatóanyag használatával. 

### <a name="access-restrictions"></a>Hozzáférési korlátozások 

A hozzáférési korlátozások funkció lehetővé teszi a **bejövő** kérések szűrését a forrás IP-címe alapján. A szűrési művelet azon előtér-szerepkörökön történik, amelyek az alkalmazások futtatásához használt munkavégző tekercsből származnak. Mivel az előtér-szerepkörök a feldolgozóktól származnak, a hozzáférési korlátozások funkció az alkalmazások hálózati szintű védelmének tekinthető. A funkció lehetővé teszi a prioritási sorrendben kiértékelt engedélyezési és megtagadási címek listájának összeállítását. Ehhez hasonló az Azure Networking hálózati biztonsági csoport (NSG) szolgáltatásához.  Ezt a funkciót a több-bérlős szolgáltatásban is használhatja. A ILB-beadási szolgáltatással való használat esetén korlátozhatja a hozzáférést a privát címekről.

![Hozzáférési korlátozások](media/networking-features/access-restrictions.png)

A hozzáférési korlátozások szolgáltatás olyan helyzetekben nyújt segítséget, amelyekben korlátozni szeretné az alkalmazás eléréséhez használható IP-címeket. A szolgáltatás használati esetei közé a következők tartoznak:

* Az alkalmazáshoz való hozzáférés korlátozása jól meghatározott címekből 
* Korlátozza a hozzáférést a terheléselosztási szolgáltatáson keresztül, például az Azure bejárati ajtón keresztül. Ha azt szeretné, hogy a bejövő forgalom az Azure bejárati ajtón legyen zárolva, hozzon létre olyan szabályokat, amelyek engedélyezik a forgalmat a 147.243.0.0/16 és a 2a01:111:2050::/44. 

![Hozzáférési korlátozások a bejárati ajtónál](media/networking-features/access-restrictions-afd.png)

Ha le szeretné zárni az alkalmazáshoz való hozzáférést úgy, hogy az csak az Azure-Virtual Network (VNet) erőforrásaiból legyen elérhető, akkor a forráshoz tartozó VNet statikus nyilvános címnek kell lennie. Ha az erőforrások nem rendelkeznek nyilvános címen, használja helyette a szolgáltatás-végpontok funkciót. Ebből a funkcióból megtudhatja, hogyan engedélyezheti ezt a funkciót a [hozzáférési korlátozások konfigurálásával][iprestrictions]kapcsolatos oktatóanyagban.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A szolgáltatási végpontok lehetővé teszik az alkalmazáshoz való **bejövő** hozzáférés zárolását úgy, hogy a forrás címének a kiválasztott alhálózatokból kell származnia. Ez a funkció az IP-hozzáférési korlátozásokkal együtt működik. A szolgáltatási végpontok az IP-hozzáférési korlátozásokkal megegyező felhasználói élményben vannak beállítva. Létrehozhat olyan hozzáférési szabályok engedélyezési/megtagadási listáját, amelyek nyilvános címeket és alhálózatokat is tartalmaznak a virtuális hálózatok. Ez a funkció olyan forgatókönyveket támogat, mint például a következők:

![szolgáltatási végpontok](media/networking-features/service-endpoints.png)

* Application Gateway beállítása az alkalmazással a bejövő forgalom zárolására az alkalmazáshoz
* Az alkalmazáshoz való hozzáférés korlátozása a VNet lévő erőforrásokra. Ebbe beletartozhatnak a virtuális gépek, ASE vagy más olyan alkalmazások is, amelyek VNet-integrációt használnak 

![szolgáltatási végpontok az Application Gateway használatával](media/networking-features/service-endpoints-appgw.png)

A szolgáltatási végpontok az alkalmazással való konfigurálásával kapcsolatos további információkért tekintse meg a [szolgáltatási végpontok hozzáférési korlátozásainak konfigurálását][serviceendpoints] ismertető oktatóanyagot.
 
### <a name="hybrid-connections"></a>Hibrid kapcsolatok

App Service Hibrid kapcsolatok lehetővé teszi, hogy az alkalmazások **kimenő** hívásokat hajtanak végre a megadott TCP-végpontokon. A végpont lehet helyszíni, VNet vagy bárhonnan, amely lehetővé teszi az Azure-ba irányuló kimenő forgalmat az 443-es porton. A szolgáltatáshoz egy hibridkapcsolat-kezelő (HCM) nevű továbbító ügynök telepítése szükséges a Windows Server 2012-es vagy újabb gazdagépén. Az HCM-nek képesnek kell lennie az 443-as porton való Azure Relay elérésére. A HCM letölthető a portál App Service Hibrid kapcsolatok felhasználói felületéről. 

![Hibrid kapcsolatok hálózati folyamat](media/networking-features/hybrid-connections.png)

A App Service Hibrid kapcsolatok funkció az Azure Relay Hibrid kapcsolatok képességre épül. A App Service a szolgáltatás egy speciális formáját használja, amely csak a kimenő hívásokat támogatja az alkalmazásból egy TCP-gazdagépre és-portra. Ezt a gazdagépet és portot csak azon a gazdagépen kell feloldani, amelyen a HCM telepítve van. Ha az alkalmazás a App Serviceban a DNS-lekérdezést hajtja végre a hibrid kapcsolatban megadott gazdagépen és porton, a rendszer automatikusan átirányítja a forgalmat, hogy átugorjon a hibrid és a hibridkapcsolat-kezelő. Ha többet szeretne megtudni a Hibrid kapcsolatokről, olvassa el [app Service][hybridconn] dokumentációját hibrid kapcsolatok

Ez a funkció általában a következőket használja:

* Hozzáférés az Azure-hoz VPN-vagy ExpressRoute-kapcsolattal nem rendelkező magánhálózatok erőforrásaihoz
* A helyszíni alkalmazások App Serviceának és átváltásának támogatása a támogatási adatbázisok áthelyezésének szükségessége nélkül  
* Biztonságos hozzáférést biztosít egy gazdagéphez és egy hibrid kapcsolaton keresztüli porthoz. A legtöbb hálózati szolgáltatás nyitott hozzáférést biztosít egy hálózathoz, és Hibrid kapcsolatok csak egyetlen gazdagép és port érhető el.
* Más kimenő kapcsolati módszerek által nem tárgyalt forgatókönyvek
* Fejlesztés végrehajtása App Serviceban, ahol az alkalmazások egyszerűen használhatják a helyszíni erőforrásokat 

Mivel a funkció lehetővé teszi a helyszíni erőforrásokhoz való hozzáférést bejövő tűzfal nélküli furat nélkül, népszerű a fejlesztők számára. A többi kimenő App Service hálózatkezelési funkció nagyon Azure virtuális hálózatkezeléssel kapcsolatos. Hibrid kapcsolatok nem függ a VNet, és több hálózati igényt is igénybe vehet. Fontos megjegyezni, hogy a App Service Hibrid kapcsolatok funkció nem érdekli, és nem tudja, mit csinál. Ez azt jelenti, hogy használhatja egy adatbázis, egy webszolgáltatás vagy egy tetszőleges TCP-szoftvercsatorna elérésére a nagyszámítógépeken. A szolgáltatás lényegében a TCP-csomagokat alagutakra irányítja. 

Habár a Hibrid kapcsolatok népszerű a fejlesztéshez, számos éles alkalmazásban is használható. Kiválóan alkalmas webszolgáltatások vagy adatbázisok elérésére, de a sok kapcsolat létrehozásával kapcsolatos helyzetekben nem megfelelő. 

### <a name="gateway-required-vnet-integration"></a>Átjáró szükséges VNet-integráció 

Az átjáró szükséges App Service VNet integrációs funkciója lehetővé teszi, hogy az alkalmazás **kimenő** kéréseket hozzon egy Azure-Virtual Networkba. A szolgáltatás úgy működik, hogy csatlakoztatja a gazdagépet, amely egy pont – hely típusú VPN-kapcsolattal rendelkező VNet Virtual Network átjáróján fut. A szolgáltatás konfigurálásakor az alkalmazás megkapja az egyes példányokhoz rendelt pont – hely címek egyikét. Ez a funkció lehetővé teszi az erőforrások elérését bármely régióban a klasszikus vagy a Resource Manager-virtuális hálózatok. 

![Átjáró szükséges VNet-integráció](media/networking-features/gw-vnet-integration.png)

Ez a funkció megoldja a más virtuális hálózatok lévő erőforrások elérésének problémáját, és akár egy VNet keresztül csatlakozhat más virtuális hálózatok, vagy akár a helyszínen is. Nem működik a ExpressRoute csatlakoztatott virtuális hálózatok, hanem a helyek közötti VPN-kapcsolattal rendelkező hálózatokkal. Általában nem célszerű ezt a funkciót egy App Service Environment (betanító) alkalmazásból használni, mert a betanító már szerepel a VNet. A funkció által megoldott használati esetek a következők:

* A magánhálózati IP-címek erőforrásainak elérése Azure-beli virtuális hálózatokban 
* Helyszíni erőforrásokhoz való hozzáférés, ha helyek közötti VPN van 
* Erőforrások elérése a társ virtuális hálózatok 

Ha ez a funkció engedélyezve van, az alkalmazás azt a DNS-kiszolgálót fogja használni, amelyhez a cél VNet konfigurálva van. Erről a szolgáltatásról a [app Service VNet-integráció][vnetintegrationp2s]dokumentációjában olvashat bővebben. 

### <a name="vnet-integration"></a>VNet-integráció

Az átjáró szükséges VNet-integrációs funkciója nagyon hasznos, de továbbra sem oldja meg az erőforrások elérését a ExpressRoute között. Ahhoz, hogy az alkalmazások ExpressRoute-kapcsolatokon keresztül is elérhetők legyenek, az alkalmazásoknak meg kell tudniuk hívni a végpontok által biztosított szolgáltatásait. A további igények megoldásához egy másik VNet integrációs képesség lett hozzáadva. Az új VNet-integrációs funkció lehetővé teszi, hogy az alkalmazás hátterét egy ugyanabban a régióban található Resource Manager-VNet egy alhálózatán helyezze el. Ez a funkció nem érhető el egy olyan App Service Environmentból, amely már egy VNet van. A funkció az alábbi lehetőségeket kínálja:

* Erőforrások elérése erőforrás-kezelő virtuális hálózatok ugyanabban a régióban
* A szolgáltatás-végpontokkal védett erőforrások elérése 
* ExpressRoute-vagy VPN-kapcsolaton keresztül elérhető erőforrások elérése

![VNet-integráció](media/networking-features/vnet-integration.png)

Ez a funkció előzetes verzióban érhető el, és nem használható éles munkaterhelésekhez. Ha többet szeretne megtudni erről a szolgáltatásról, olvassa el [app Service VNet integrációjának][vnetintegration]dokumentációját.

## <a name="app-service-environment"></a>App Service Environment-környezet 

Az App Service Environment (bevezetési) a VNet futó Azure App Service egyetlen bérlős példánya. A kiegészítő szolgáltatás a következőket teszi lehetővé:

* Erőforrások elérése a VNet
* Erőforrások elérése ExpressRoute között
* Tegye elérhetővé alkalmazásait privát címként a VNet 
* Erőforrások elérése szolgáltatási végpontok között 

A bevezetési szolgáltatással nem kell olyan szolgáltatásokat használnia, mint a VNet-integráció vagy a szolgáltatási végpontok, mert a szolgáltató már szerepel a VNet. Ha olyan erőforrásokat szeretne elérni, mint például az SQL vagy a Storage szolgáltatásbeli végpontok, engedélyezze a szolgáltatási végpontokat a szolgáltatói alhálózaton. Ha a VNet erőforrásaihoz szeretne hozzáférni, nincs szükség további konfigurálásra.  Ha a ExpressRoute-on keresztül szeretné elérni az erőforrásokat, akkor már szerepel a VNet, és nem kell semmit konfigurálnia a betanító vagy a benne lévő alkalmazásokhoz. 

Mivel a ILB-beadásban lévő alkalmazások elérhetővé tehetők egy magánhálózati IP-címen, egyszerűen hozzáadhat WAF-eszközöket az internethez használni kívánt alkalmazások és a REST biztonság fenntartása érdekében. A többrétegű alkalmazások könnyű fejlesztéséhez is alkalmas. 

Vannak olyan dolgok, amelyek még nem lehetségesek a bemutató több-bérlős szolgáltatásból. Ilyenek például a következők:

* Alkalmazások közzététele privát IP-címen
* Az összes kimenő forgalom biztonságossá tétele olyan hálózati vezérlőkkel, amelyek nem részei az alkalmazásnak 
* Alkalmazások üzemeltetése egyetlen bérlős szolgáltatásban 
* Vertikális felskálázás több példányra, mint amennyi lehetséges a több-bérlős szolgáltatásban 
* Privát HITELESÍTÉSSZOLGÁLTATÓI Ügyféltanúsítványok betöltése az alkalmazások számára privát HITELESÍTÉSSZOLGÁLTATÓI biztonságos végpontokkal való használatra 
* A TLS 1,1 kényszerítése a rendszerben üzemeltetett összes alkalmazáson anélkül, hogy le kellene tiltani az alkalmazás szintjén. 
* Adjon meg egy dedikált kimenő IP-címeket a beadásban lévő összes olyan alkalmazáshoz, amely nincs megosztva az ügyfelekkel 

![VNet](media/networking-features/app-service-environment.png)

A benyújtó a legjobb történet az elkülönített és a dedikált alkalmazások üzemeltetése mellett, de bizonyos felügyeleti problémákkal is jár. Az operatív kiegészítő szolgáltatás használata előtt megfontolandó szempontok a következők:
 
 * A VNet-en belül, de a VNet kívüli függőségekkel is rendelkezik. Ezeket a függőségeket engedélyezni kell. További információ a [hálózatkezelési megfontolásokról app Service Environment][networkinfo]
 * A kiegészítő szolgáltatások nem méretezhetők azonnal, mint a több-bérlős szolgáltatás. A skálázási igényeket a reaktív méretezés helyett előre kell mérni. 
 * A benyújtó bekerülési díja magasabb a hozzá tartozó előfizetéssel. Annak érdekében, hogy a lehető legtöbbet hozza ki a bevezetésből, érdemes nagy mennyiségű számítási feladatot üzembe helyezést végeznie, ahelyett, hogy kis erőfeszítésekhez kellene használni
 * A kiszolgált alkalmazásokban lévő alkalmazások nem korlátozhatják az egyes alkalmazásokhoz való hozzáférést egy központon belül, másokat nem.
 * A Bekapcsolási pont alhálózatban található, és minden hálózati szabály az adott központba irányuló és onnan érkező összes forgalomra érvényes. Ha csak egy alkalmazás bejövő forgalmi szabályait szeretné hozzárendelni, használja a hozzáférési korlátozásokat. 

## <a name="combining-features"></a>Szolgáltatások egyesítése 

A több-bérlős szolgáltatáshoz feljegyzett funkciók a bonyolultabb használati esetek megoldásához is használhatók. A leggyakoribb használati esetek közül kettőt itt ismertetünk, de ezek csak példák. A különféle funkciók megismerésével szinte minden rendszerarchitektúra-szükségletet megtalálhat.

### <a name="inject-app-into-a-vnet"></a>Alkalmazás behelyezése egy VNet

Gyakori kérés, hogy hogyan helyezheti üzembe az alkalmazást egy VNet. Az alkalmazás VNet való elhelyezése azt jelenti, hogy az alkalmazás bejövő és kimenő végpontjai egy VNet belül találhatók. A benyújtó a legjobb megoldás a probléma megoldására, de a funkciók összevonásával a több-bérlős szolgáltatásban szükségesek többsége is elérhető. Például csak intranetes, privát bejövő és kimenő címmel rendelkező alkalmazásokat tárolhat a következő módon:

* Privát bejövő és kimenő IP-címekkel rendelkező Application Gateway létrehozása
* Az alkalmazás felé irányuló bejövő adatforgalom biztonságossá tétele szolgáltatási végpontokkal 
* Használja az új VNet-integrációt, hogy az alkalmazás háttere a VNet legyen 

Ez a telepítési stílus nem biztosít dedikált IP-címeket az internetre irányuló kimenő forgalom számára, vagy lehetővé teszi az alkalmazásból érkező összes kimenő forgalom zárolását.  Ez az üzembe helyezési stílus nagyban kipróbálhatja, hogy a szolgáltatáshoz csak a beszerzést kéri. 

### <a name="create-multi-tier-applications"></a>Többrétegű alkalmazások létrehozása

A többrétegű alkalmazások olyan alkalmazások, amelyek esetében az API-háttérbeli alkalmazások csak az előtér-rétegből érhetők el. Többrétegű alkalmazások létrehozásához a következőket teheti:

* VNet-integráció használata az előtér-webalkalmazás hátterének egy VNet lévő alhálózattal való összekapcsolásához
* A szolgáltatás-végpontok használatával biztosíthatja, hogy az API-alkalmazás bejövő forgalma csak az előtér-webalkalmazás által használt alhálózatról legyen elérhető

![többrétegű alkalmazás](media/networking-features/multi-tier-app.png)

Több előtér-alkalmazás is használhatja ugyanazt az API-alkalmazást az API-alkalmazás és az alhálózatok közötti VNet integráció használatával.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
