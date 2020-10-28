---
title: Hálózatkezelési funkciók
description: Ismerkedjen meg a Azure App Service hálózati szolgáltatásaival, és hogy mely funkciók szükségesek a hálózat biztonságához vagy működéséhez.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9b75df9df2e81f01543b407b019c752c77ee6807
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628830"
---
# <a name="app-service-networking-features"></a>Hálózati szolgáltatások App Service

A Azure App Service alkalmazásai több módon is üzembe helyezhetők. Alapértelmezés szerint App Service üzemeltetett alkalmazások közvetlenül az internetről elérhetők, és csak az internetről futtatott végpontok elérésére képesek. Számos ügyfél-alkalmazásnak azonban a bejövő és a kimenő hálózati forgalmat is szabályoznia kell. A App Service több funkciója is rendelkezésre áll, hogy megfeleljenek az igényeknek. A kihívás az adott probléma megoldására szolgáló szolgáltatás ismerete. Ez a dokumentum arra szolgál, hogy segítsen az ügyfeleknek meghatározni, hogy milyen szolgáltatást kell használni bizonyos példa használati esetek alapján.

A Azure App Service két elsődleges központi telepítési típust tartalmaz. Létezik a több-bérlős nyilvános szolgáltatás, amely App Service csomagokat üzemeltet az ingyenes, közös, alapszintű, standard, prémium, Premiumv2 és Premiumv3 díjszabási SKU-ban. Ebben az esetben az egyetlen bérlői App Service Environment (bemutató), amely az elkülönített SKU-App Service csomagokat közvetlenül az Azure Virtual Networkban (VNet) üzemelteti. A használt funkciók attól függően változnak, hogy a több-bérlős szolgáltatásban vagy egy szolgáltatóban van. 

## <a name="multi-tenant-app-service-networking-features"></a>Több-bérlős App Service hálózatkezelési funkciók 

A Azure App Service egy elosztott rendszer. A bejövő HTTP/HTTPS-kérelmeket kezelő szerepköröket előtér-végpontoknak nevezzük. Az ügyfél munkaterhelését üzemeltető szerepköröket feldolgozónak nevezzük. Egy App Service üzemelő példány összes szerepköre több-bérlős hálózaton található. Mivel a App Service skálázási egységben sok különböző ügyfél található, a App Service hálózat nem csatlakoztatható közvetlenül a hálózathoz. A hálózatok csatlakoztatása helyett az alkalmazások kommunikációjának különböző szempontjait kezelő funkciókra van szükségünk. Az alkalmazásra irányuló kéréseket kezelő funkciók nem használhatók problémák megoldására az alkalmazásból indított hívások során. Hasonlóképpen, az alkalmazással kapcsolatos hívások problémáit megoldó funkciók nem használhatók az alkalmazással kapcsolatos problémák megoldásához.  

| Bejövő funkciók | Kimenő funkciók |
|---------------------|-------------------|
| Alkalmazáshoz rendelt címe | Hibrid kapcsolatok |
| Hozzáférési korlátozások | Átjáró szükséges VNet-integráció |
| Szolgáltatásvégpontok | VNet-integráció |
| Privát végpontok ||

Ha másként nincs megadva, az összes funkció együtt használható. A különböző problémák megoldásához kombinálhatja a szolgáltatásokat.

## <a name="use-case-and-features"></a>Használati eset és funkciók

A probléma megoldásához bizonyos használati eseteket is használhat.  A megfelelő szolgáltatást esetenként a használati eseten kívüli okok miatt is érdemes használni. A következő bejövő használati esetek azt sugallják, hogyan használhatók a App Service hálózatkezelési funkciói az alkalmazásra irányuló forgalom szabályozásával kapcsolatos problémák megoldásához. 
 
| Bejövő használati esetek | Szolgáltatás |
|---------------------|-------------------|
| Az alkalmazás IP-alapú SSL-igényeinek támogatása | alkalmazáshoz rendelt címe |
| Nem megosztott, dedikált bejövő címe az alkalmazáshoz | alkalmazáshoz rendelt címe |
| Az alkalmazáshoz való hozzáférés korlátozása jól meghatározott címekből | Hozzáférési korlátozások |
| Az alkalmazáshoz való hozzáférés korlátozása VNet lévő erőforrásokkal | Szolgáltatásvégpontok </br> ILB </br> Privát végpontok |
| Saját alkalmazás közzététele privát IP-VNet | ILB </br> Privát végpontok </br> Magánhálózati IP-cím bejövő Application Gateway szolgáltatási végpontokkal |
| Az alkalmazás és a webalkalmazási tűzfal (WAF) elleni védelem | Application Gateway + ILB </br> Application Gateway privát végpontokkal </br> Application Gateway szolgáltatásvégpontokkal </br> Azure bejárati ajtó hozzáférési korlátozásokkal |
| A különböző régiókban lévő alkalmazások forgalmának elosztása | Azure bejárati ajtó hozzáférési korlátozásokkal | 
| Azonos régióban lévő forgalom terheléselosztása | [Application Gateway szolgáltatásvégpontokkal][appgwserviceendpoints] | 

A következő kimenő használati esetek azt mutatják be, hogyan használhatók a App Service hálózatkezelési funkciói az alkalmazás kimenő hozzáférési igényeinek megoldásához. 

| Kimenő használati esetek | Szolgáltatás |
|---------------------|-------------------|
| Egy adott régióban található Azure-Virtual Network erőforrásainak elérése | VNet-integráció </br> ASE |
| Erőforrásokhoz való hozzáférés egy másik régióban lévő Azure-Virtual Network | Átjáró szükséges VNet-integráció </br> VNet-társítás |
| A szolgáltatási végpontokkal védett erőforrások elérése | VNet-integráció </br> ASE |
| Hozzáférés az Azure-hoz nem csatlakozó magánhálózat erőforrásaihoz | Hibrid kapcsolatok |
| Erőforrások elérése ExpressRoute-áramkörök között | VNet-integráció </br> ASE | 
| Biztonságos kimenő forgalom a webalkalmazásból | VNet-integrációs és hálózati biztonsági csoportok </br> ASE | 
| A kimenő forgalom irányítása a webalkalmazásból | VNet-integráció és-útválasztási táblák </br> ASE | 


### <a name="default-networking-behavior"></a>Alapértelmezett hálózatkezelési viselkedés

A Azure App Service skálázási egységek számos ügyfelet támogatnak az egyes telepítések során. Az ingyenes és a közös SKU-csomagok a több-bérlős feldolgozók esetében az ügyfelek munkaterheléseit üzemeltetik. Az alapszintű és a fenti csomagok olyan felhasználói munkaterheléseket üzemeltetnek, amelyek csak egy App Service csomagra (ASP) vannak kijelölve. Ha standard App Service csomaggal rendelkezett, akkor a tervben szereplő összes alkalmazás ugyanazon a munkavégzőn fog futni. Ha kibővíti a munkavégzőt, akkor az adott ASP-ben lévő összes alkalmazás replikálva lesz egy új feldolgozón az ASP minden példánya számára. 

#### <a name="outbound-addresses"></a>Kimenő címek

A munkavégző virtuális gépek nagy részben vannak lebontva a App Service árképzési csomagok alapján. Az ingyenes, közös, alapszintű, standard és prémium szintű mind ugyanazt a munkavégző virtuálisgép-típust használják. A Premiumv2 egy másik virtuálisgép-típuson van. A Premiumv3 még egy másik virtuálisgép-típuson van. A virtuálisgép-család minden változása esetében a kimenő címek eltérő készlete van. Ha standard szintről Premiumv2-re méretezi, a kimenő címek megváltoznak. Ha a Premiumv2-ről Premiumv3-re méretezi, a kimenő címek megváltoznak. Néhány régebbi méretezési egység is megváltoztatja a bejövő és a kimenő címeket a standard és a Premiumv2 közötti skálázás esetén. A kimenő hívások elkészítése során több cím is használható. Az alkalmazásban a kimenő hívások elküldéséhez használt kimenő címek az alkalmazás tulajdonságainál jelennek meg. Ezeket a címeket a App Service üzemelő példány ugyanazon munkavégző virtuálisgép-családján futó összes alkalmazás megosztja. Ha szeretné megtekinteni az alkalmazás által az adott skálázási egységben használt összes lehetséges címet, egy possibleOutboundAddresses nevű másik tulajdonság jelenik meg, amely felsorolja azokat. 

![Alkalmazás tulajdonságai](media/networking-features/app-properties.png)

App Service a szolgáltatás felügyeletéhez használt végpontok száma.  Ezeket a címeket külön dokumentumban tesszük közzé, és a AppServiceManagement IP-szolgáltatás címkéjén is szerepelnek. A AppServiceManagement címke csak olyan App Service Environment használatos, amelyben engedélyezni kell a forgalmat. A App Service bejövő címeket a AppService IP-szolgáltatás címkéjén követjük nyomon. Nincs olyan IP-szolgáltatás címkéje, amely az App Service által használt kimenő címeket tartalmazza. 

![App Service bejövő és kimenő diagram](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Alkalmazáshoz rendelt címe 

Az alkalmazáshoz rendelt cím funkció az IP-alapú SSL-képesség indaja, és az SSL az alkalmazással való beállításával érhető el. Ez a funkció IP-alapú SSL-hívásokhoz használható, de az alkalmazás csak azt a címet használhatja, amely csak az adott. 

![Alkalmazáshoz rendelt címek diagramja](media/networking-features/app-assigned-address.png)

Ha egy alkalmazáshoz rendelt címeket használ, a forgalom továbbra is ugyanazokat az előtér-szerepköröket használja, amelyek az összes bejövő forgalmat a App Service skálázási egységben kezelik. Az alkalmazáshoz rendelt címeket azonban csak az alkalmazás használja. A szolgáltatás használati esetei a következők:

* Az alkalmazás IP-alapú SSL-igényeinek támogatása
* Olyan dedikált IP-címek beállítása az alkalmazáshoz, amely nem osztható meg semmi mással

Megtudhatja, hogyan állíthatja be az alkalmazáshoz tartozó címeket a [TLS/SSL-tanúsítvány hozzáadása Azure app Serviceban][appassignedaddress]című oktatóanyagban. 

### <a name="access-restrictions"></a>Hozzáférési korlátozások 

A hozzáférési korlátozások funkció lehetővé teszi a **bejövő** kérések szűrését. A szűrési művelet azon előtér-szerepkörökön történik, amelyek az alkalmazások futtatását végző feldolgozói szerepkörökből származnak. Mivel az előtér-szerepkörök a feldolgozóktól származnak, a hozzáférési korlátozások funkció az alkalmazások hálózati szintű védelmének tekinthető. A funkció lehetővé teszi a prioritási sorrendben kiértékelt engedélyezési és megtagadási szabályok listájának összeállítását. Ehhez hasonló az Azure Networking hálózati biztonsági csoport (NSG) szolgáltatásához.  Ezt a funkciót a több-bérlős szolgáltatásban is használhatja. ILB-előállítók vagy privát végpontok használata esetén korlátozhatja a hozzáférést a privát címekről.
> [!NOTE]
> Akár 512 hozzáférési korlátozási szabály is konfigurálható alkalmazásként. 

![Hozzáférési korlátozások](media/networking-features/access-restrictions.png)
#### <a name="ip-based-access-restriction-rules"></a>IP-alapú hozzáférés korlátozási szabályai

Az IP-alapú hozzáférési korlátozások szolgáltatás olyan helyzetekben nyújt segítséget, amelyekben korlátozni szeretné az alkalmazás eléréséhez használható IP-címeket. Az IPv4- és IPv6-címek egyaránt támogatottak. A szolgáltatás használati esetei közé a következők tartoznak:

* Az alkalmazáshoz való hozzáférés korlátozása jól meghatározott címekből 
* Terheléselosztási szolgáltatáson keresztüli hozzáférés korlátozása, például az Azure bejárati ajtaja

![Hozzáférési korlátozások a bejárati ajtónál](media/networking-features/access-restrictions-afd.png)

Ebből a funkcióból megtudhatja, hogyan engedélyezheti ezt a funkciót a [hozzáférési korlátozások konfigurálásával][iprestrictions]kapcsolatos oktatóanyagban.

#### <a name="service-endpoint-based-access-restriction-rules"></a>Szolgáltatás-végponton alapuló hozzáférés korlátozási szabályai

A szolgáltatási végpontok lehetővé teszik az alkalmazáshoz való **bejövő** hozzáférés zárolását úgy, hogy a forrás címének a kiválasztott alhálózatokból kell származnia. Ez a funkció az IP-hozzáférési korlátozásokkal együtt működik. A szolgáltatási végpontok nem kompatibilisek a távoli hibakereséssel. Ha a távoli hibakeresést szeretné használni az alkalmazással, az ügyfél nem lehet olyan alhálózatban, amelyen engedélyezve vannak a szolgáltatási végpontok. A szolgáltatási végpontok az IP-hozzáférési korlátozásokkal megegyező felhasználói élményben vannak beállítva. Létrehozhat olyan hozzáférési szabályok engedélyezési/megtagadási listáját, amelyek nyilvános címeket és alhálózatokat is tartalmaznak a virtuális hálózatok. Ez a funkció olyan forgatókönyveket támogat, mint például a következők:

![szolgáltatási végpontok](media/networking-features/service-endpoints.png)

* Application Gateway beállítása az alkalmazással a bejövő forgalom zárolására az alkalmazáshoz
* Az alkalmazáshoz való hozzáférés korlátozása a VNet lévő erőforrásokra. Ebbe beletartozhatnak a virtuális gépek, ASE vagy más olyan alkalmazások is, amelyek VNet-integrációt használnak 

![szolgáltatási végpontok az Application Gateway használatával](media/networking-features/service-endpoints-appgw.png)

A szolgáltatási végpontok az alkalmazással való konfigurálásával kapcsolatos további információkért tekintse meg a [szolgáltatási végpontok hozzáférési korlátozásainak konfigurálását][serviceendpoints] ismertető oktatóanyagot.

### <a name="private-endpoints"></a>Privát végpontok

A privát végpont egy olyan hálózati adapter, amely az Azure Private-kapcsolaton keresztül csatlakozik a webalkalmazáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a webalkalmazást a VNet. Ez a funkció csak a webalkalmazás **bejövő** folyamatai számára érhető el.
[Privát végpontok használata az Azure Web App-hoz][privateendpoints]

A privát végpontok olyan forgatókönyveket tesznek lehetővé, mint például:

* Az alkalmazáshoz való hozzáférés korlátozása VNet lévő erőforrásokkal 
* Saját alkalmazás közzététele privát IP-VNet 
* Az alkalmazás védetté WAF 

A privát végpontok meggátolják, hogy az kiszűrése az egyetlen dolog, amit a privát végponton keresztül érhet el. 
 
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

Mivel a funkció lehetővé teszi a helyszíni erőforrásokhoz való hozzáférést bejövő tűzfal nélküli furat nélkül, népszerű a fejlesztők számára. A többi kimenő App Service hálózati szolgáltatás az Azure virtuális hálózatkezeléssel kapcsolatos. Hibrid kapcsolatok nem függ a VNet, és több hálózati igényt is igénybe vehet. Fontos megjegyezni, hogy a App Service Hibrid kapcsolatok funkció nem érdekli, és nem tudja, mit csinál. Ez azt jelenti, hogy használhatja egy adatbázis, egy webszolgáltatás vagy egy tetszőleges TCP-szoftvercsatorna elérésére a nagyszámítógépeken. A szolgáltatás lényegében a TCP-csomagokat alagutakra irányítja. 

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

Az átjáró szükséges VNet-integrációs funkciója hasznos, de továbbra sem oldja meg az erőforrások elérését a ExpressRoute között. Ahhoz, hogy az alkalmazások ExpressRoute-kapcsolatokon keresztül is elérhetők legyenek, az alkalmazásoknak meg kell tudniuk hívni a végpontok által biztosított szolgáltatásait. A további igények megoldásához egy másik VNet integrációs képesség lett hozzáadva. Az új VNet-integrációs funkció lehetővé teszi, hogy az alkalmazás hátterét egy ugyanabban a régióban található Resource Manager-VNet egy alhálózatán helyezze el. Ez a funkció nem érhető el egy olyan App Service Environmentból, amely már egy VNet van. Ez a funkció lehetővé teszi a következőket:

* Erőforrások elérése erőforrás-kezelő virtuális hálózatok ugyanabban a régióban
* A szolgáltatás-végpontokkal védett erőforrások elérése 
* ExpressRoute-vagy VPN-kapcsolaton keresztül elérhető erőforrások elérése
* Az összes kimenő forgalom biztonságossá tétele 
* Az összes kimenő forgalom bújtatásának kényszerítése. 

![VNet-integráció](media/networking-features/vnet-integration.png)

Ha többet szeretne megtudni erről a szolgáltatásról, olvassa el [app Service VNet integrációjának][vnetintegration]dokumentációját.

## <a name="app-service-environment"></a>App Service-környezet 

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

A többrétegű alkalmazások olyan alkalmazások, amelyek esetében az API-háttérbeli alkalmazások csak az előtér-rétegből érhetők el. A többrétegű alkalmazások létrehozása kétféleképpen lehetséges. Mindkettő a VNet-integráció használatával csatlakozik az előtér-webalkalmazáshoz egy VNet lévő alhálózattal. Ez lehetővé teszi, hogy a webalkalmazás hívásokat hajtson végre a VNet. Miután az előtér-alkalmazás csatlakozott a VNet, ki kell választania, hogy hogyan zárolja az API-alkalmazáshoz való hozzáférést.  A következőket teheti:

* az előtér-és API-alkalmazást ugyanabban a ILB-előfizetésben üzemelteti, és az előtér-alkalmazást elérhetővé teszi az internethez egy Application Gateway használatával
* a több-bérlős szolgáltatásban található előtérrendszer és a háttérrendszer üzemeltetése egy ILB-ben
* az előtér-és API-alkalmazás üzemeltetése a több-bérlős szolgáltatásban

Ha az előtér-és API-alkalmazást egy többrétegű alkalmazáshoz is üzemelteti, a következőket teheti:

Saját API-alkalmazás közzététele privát végpontokkal a VNet

![privát végpontok két rétegbeli alkalmazás](media/networking-features/multi-tier-app-private-endpoint.png)

A szolgáltatás-végpontok használatával biztosíthatja, hogy az API-alkalmazás bejövő forgalma csak az előtér-webalkalmazás által használt alhálózatról legyen elérhető

![biztonságos alkalmazás szolgáltatás-végpontok](media/networking-features/multi-tier-app.png)

A két módszer közötti kompromisszumok a következők:

* a szolgáltatási végpontok esetében csak az API-alkalmazás felé irányuló forgalmat kell biztonságossá tenni az integrációs alhálózaton. Ez biztonságossá teszi az API-alkalmazást, de a kiszűrése lehetősége van arra, hogy az előtérben lévő alkalmazásból a App Service más alkalmazásai is rendelkezzenek.
* a privát végpontokkal két alhálózattal rendelkezik a lejátszásnál. Ez a bonyolultságot fokozza. Emellett a privát végpont egy legfelső szintű erőforrás, és további kezeléshez is hozzájárul. A privát végpontok használatának előnye, hogy Ön nem rendelkezik kiszűrése lehetőséggel. 

Mindkét módszer több előtér-végponttal is működik. Kis méretekben a szolgáltatási végpontok sokkal egyszerűbben használhatók, mert egyszerűen engedélyezheti a szolgáltatási végpontokat az API-alkalmazáshoz az előtér-integrációs alhálózaton. Az előtér-alkalmazások hozzáadásakor minden API-alkalmazást módosítania kell, hogy az integrációs alhálózattal rendelkezzenek a szolgáltatási végpontokkal. A privát végpontokkal összetettebb, de nem kell semmit módosítania az API-alkalmazásokban a privát végpontok beállítása után. 

### <a name="line-of-business-applications"></a>Üzletági alkalmazások

Az üzletági (LOB) alkalmazások olyan belső alkalmazások, amelyeket általában nem tesznek elérhetővé az internetről. Ezeket az alkalmazásokat a vállalati hálózatokon belül kell meghívni, ahol a hozzáférés szigorúan szabályozható. Ha ILB-t használ, könnyen üzemeltetheti az üzletági alkalmazásait. Ha a több-bérlős szolgáltatást használja, használhat privát végpontokat vagy szolgáltatási végpontokat egy Application Gateway kombinálva. A magánhálózati végpontok helyett két ok van a Application Gateway használatára a szolgáltatási végpontokkal:

* WAF-védelemmel kell rendelkeznie a LOB-alkalmazásokban
* terheléselosztást szeretne betölteni a LOB-alkalmazások több példányára

Ha egyik sem a helyzet, akkor jobb, ha privát végpontokat használ. A App Serviceban elérhető privát végpontokkal az alkalmazásait privát címekre teheti ki a VNet. A VNet elhelyezhető privát végpont ExpressRoute és VPN-kapcsolatokon keresztül is elérhető. A privát végpontok konfigurálása privát címen teszi elérhetővé az alkalmazásokat, de a DNS-t úgy kell konfigurálnia, hogy a helyszíni környezetből elérje ezt a internetcímet. Ennek a munkának a végrehajtásához továbbítania kell a privát végpontokat tartalmazó Azure DNS privát zónákat a helyszíni DNS-kiszolgálókra. Azure DNS privát zónák nem támogatják a zónák továbbítását, hanem a DNS-kiszolgáló használatát is támogathatja erre a célra. Ez a sablon, a [DNS-továbbító](https://azure.microsoft.com/resources/templates/301-dns-forwarder/)megkönnyíti a Azure DNS saját zónájának továbbítását a helyszíni DNS-kiszolgálókra.

## <a name="app-service-ports"></a>App Service portok

Ha beolvassa a App Service, a rendszer számos olyan portot talál, amelyek elérhetők a bejövő kapcsolatokhoz. A több-bérlős szolgáltatásban a portok elérését nem lehet letiltani vagy szabályozni. Az elérhető portok a következők:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Kezelés | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresése  |  4020, 4022, 4024 |
|  Web Deploy szolgáltatás | 8172 |
|  Infrastruktúra-használat | 7654, 1221 |

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
