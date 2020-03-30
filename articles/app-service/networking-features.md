---
title: Hálózatkezelési funkciók
description: Ismerje meg az Azure App Service hálózati szolgáltatásait, valamint azt, hogy a hálózati igényeknek megfelelően mely funkciókra van szüksége a biztonság hoz vagy a funkciókhoz.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bc3d35830d833c0223a400140c53e583d2f6ed37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475425"
---
# <a name="app-service-networking-features"></a>Az App Service hálózati szolgáltatásai

Az Azure App Service-ben az alkalmazások többféleképpen is telepíthetők. Alapértelmezés szerint az App Service által üzemeltetett alkalmazások közvetlenül elérhetők az interneten, és csak az internetes üzemeltetett végpontokat érhetik el. Számos ügyfélalkalmazásnak azonban szabályoznia kell a bejövő és kimenő hálózati forgalmat. Az App Service számos olyan funkcióval rendelkezik, amelyek kielégítik ezeket az igényeket. A kihívás az, hogy tudjuk, milyen funkciót kell használni egy adott probléma megoldására. Ez a dokumentum célja, hogy segítsen az ügyfeleknek meghatározni, hogy milyen funkciót kell használni néhány példa használati eset alapján.

Az Azure App Service két elsődleges központi telepítési típusa van. Ott van a több-bérlős közszolgáltatás, amely az Ingyenes, megosztott, alapszintű, standard, prémium és premiumv2 árképzési termékkészletben üzemeltetI az App Service-csomagokat. Ezután ott van az egybérlős App Service Environment(ASE), amely üzemelteti az elkülönített SKU App Service-csomagok közvetlenül az Azure virtuális hálózat (VNet). A használt funkciók a ttól függően változnak, ha a több-bérlős szolgáltatás vagy egy ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Több-bérlős App Service hálózati funkciók 

Az Azure App Service egy elosztott rendszer. A bejövő HTTP/HTTPS-kérelmeket kezelő szerepköröket előtérnek nevezzük. Az ügyfél-munkaterhelést fogadó szerepköröket dolgozóknak nevezzük. Az App Service-telepítés ben az összes szerepkör létezik egy több-bérlős hálózatban. Mivel ugyanabban az App Service-méretezési egységben sok különböző ügyfél van, az App Service-hálózat nem csatlakoztatható közvetlenül a hálózathoz. A hálózatok csatlakoztatása helyett olyan funkciókra van szükségünk, amelyek az alkalmazáskommunikáció különböző aspektusait kezelik. Az alkalmazásba irányuló kéréseket kezelő funkciók nem használhatók az alkalmazásból érkező hívások hívásai során felmerülő problémák megoldására. Hasonlóképpen, az alkalmazásból érkező hívások problémáit megoldó funkciók nem használhatók az alkalmazás problémáinak megoldására.  

| Bejövő funkciók | Kimenő szolgáltatások |
|---------------------|-------------------|
| Alkalmazáshoz rendelt cím | Hibrid kapcsolatok |
| Hozzáférési korlátozások | Átjáró szükséges virtuális hálózati integráció |
| Szolgáltatásvégpontok | VNet-integráció |

Eltérő rendelkezés hiányában az összes funkció együtt használható. Keverheti a funkciókat, hogy megoldja a különböző problémákat.

## <a name="use-case-and-features"></a>Használati eset és funkciók használata

Bármely adott használati esetben, nem lehet néhány módja, hogy megoldja a problémát.  A megfelelő funkció használata néha miatt okok túl csak a használati eset is. A következő bejövő használati esetek azt javasolják, hogyan használhatja az App Service hálózati funkciókat az alkalmazáshoz irányuló forgalom szabályozásával kapcsolatos problémák megoldásához. 
 
| Bejövő használati esetek | Szolgáltatás |
|---------------------|-------------------|
| Az alkalmazás IP-alapú SSL-igényeinek támogatása | alkalmazáshoz rendelt cím |
| Nem megosztott, dedikált bejövő cím az alkalmazáshoz | alkalmazáshoz rendelt cím |
| Az alkalmazáshoz való hozzáférés korlátozása jól definiált címekből | Hozzáférési korlátozások |
| Az alkalmazáshoz való hozzáférés korlátozása virtuális hálózat erőforrásaiból | Szolgáltatásvégpontok </br> ILB ASE </br> Privát végpont (előzetes verzió) |
| Az alkalmazás felfedése privát IP-címén a virtuális hálózatban | ILB ASE </br> privát IP a szolgáltatásvégpontokkal rendelkező application gateway-re érkező konszern számára </br> Szolgáltatásvégpont (előzetes verzió) |
| Az alkalmazás védelme WAF-fel | Alkalmazásátjáró + ILB ASE </br> Application Gateway szolgáltatásvégpontokkal </br> Azure bejárati ajtó hozzáférési korlátozásokkal |
| Terheléselosztási forgalom az alkalmazásaimhoz a különböző régiókban | Azure bejárati ajtó hozzáférési korlátozásokkal | 
| Terheléselosztási forgalom ugyanabban a régióban | [Application Gateway szolgáltatásvégpontokkal][appgwserviceendpoints] | 

A következő kimenő használati esetek azt javasolják, hogyan használhatja az App Service hálózati funkciókat az alkalmazás kimenő hozzáférési igényeinek megoldásához. 

| Kimenő használati esetek | Szolgáltatás |
|---------------------|-------------------|
| Erőforrások elérése egy Azure virtuális hálózatban ugyanabban a régióban | VNet-integráció </br> Ase |
| Erőforrások elérése egy másik régióban lévő Azure virtuális hálózatban | Átjáró szükséges virtuális hálózati integráció </br> ASE és virtuális hálózat társviszony-létesítése |
| Szolgáltatásvégekkel védett erőforrások elérése | VNet-integráció </br> Ase |
| Az Azure-hoz nem kapcsolódó magánhálózat erőforrásainak elérése | Hibrid kapcsolatok |
| Erőforrások elérése az ExpressRoute-áramkörök között | VNet-integráció </br> Ase | 
| A kimenő forgalom biztonságossá tétele a webalkalmazásból | Virtuális hálózat integrációs és hálózati biztonsági csoportjai </br> Ase | 
| Kimenő forgalom átirányítása a webalkalmazásból | Virtuális hálózat integrációs és útvonaltáblái </br> Ase | 


### <a name="default-networking-behavior"></a>Alapértelmezett hálózati viselkedés

Az Azure App Service méretezési egységek számos ügyfelet támogatnak az egyes telepítésekben. Az ingyenes és megosztott termékváltozat-csomagok több-bérlős dolgozók ügyfél-munkaterheléseit üzemeltetik. Az alapszintű és a fenti csomagok csak egy App Service-csomaghoz (ASP) dedikált ügyfél-munkaterheléseket üzemeltetnek. Ha standard App Service-csomaggal volt, akkor a csomagban lévő összes alkalmazás ugyanazon a dolgozón fog futni. Ha horizontális felskálázza a dolgozót, majd az adott ASP-ben lévő összes alkalmazás replikálódik egy új dolgozón az ASP minden egyes példányához. A Premiumv2-hez használt dolgozók különböznek a többi csomaghoz használt dolgozóktól. Minden App Service-telepítés rendelkezik egy IP-címet, amely az app service-telepítésben lévő alkalmazások összes bejövő forgalomhoz használatos. Vannak azonban bárhol 4-11 címeket használt kimenő hívásokat. Ezeket a címeket az adott App Service-telepítésben lévő összes alkalmazás megosztja. A kimenő címek a különböző munkavégző típusoktól függően eltérőek. Ez azt jelenti, hogy az ingyenes, megosztott, alapszintű, standard és prémium szintű aszp-k által használt címek eltérnek a Premiumv2 AP-k kimenő hívásaihoz használt címektől. Ha megnézi az alkalmazás tulajdonságait, láthatja az alkalmazás által használt bejövő és kimenő címeket. Ha egy IP-acl-lel rendelkező függőséget kell lezárnia, használja a lehetségesOutboundAddresses címet. 

![Alkalmazás tulajdonságai](media/networking-features/app-properties.png)

Az App Service számos végpontot használ a szolgáltatás kezeléséhez használt.  Ezek a címek egy külön dokumentumban vannak közzétéve, és az AppServiceManagement IP szolgáltatáscímkéjében is szerepelnek. Az AppServiceManagement címke csak egy App Service-környezet (ASE) használatával használható, ahol engedélyeznie kell az ilyen forgalmat. Az App Service bejövő címeit az AppService IP-szolgáltatáscímkéje követi nyomon. Nincs olyan IP-szolgáltatáscímke, amely az App Service által használt kimenő címeket tartalmazza. 

![Az App Service bejövő és kimenő diagramja](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Alkalmazáshoz rendelt cím 

Az alkalmazáshoz rendelt címfunkció az IP-alapú SSL-képesség utódja, és az SSL alkalmazással való beállításával érhető el. Ez a funkció használható IP-alapú SSL-hívásokhoz, de arra is használható, hogy az alkalmazásnak olyan címet adjon, amellyel csak ő rendelkezik. 

![Alkalmazáshoz rendelt címdiagram](media/networking-features/app-assigned-address.png)

Ha egy alkalmazáshoz rendelt címet használ, a forgalom továbbra is ugyanazokon az előtér-szerepkörökön megy keresztül, amelyek az App Service méretezési egységbe érkező összes bejövő forgalmat kezelik. Az alkalmazáshoz rendelt címet azonban csak az alkalmazás használja. A funkció használati esetei a következők:

* Az alkalmazás IP-alapú SSL-igényeinek támogatása
* Az alkalmazáshoz olyan dedikált cím beállítása, amely nincs megosztva másokkal

Megtudhatja, hogyan állíthat be egy címet az alkalmazáson az [IP-alapú SSL konfigurálásáról][appassignedaddress]szóló oktatóanyaggal. 

### <a name="access-restrictions"></a>Hozzáférési korlátozások 

A hozzáférési korlátozások funkció lehetővé teszi **a bejövő** kérelmek szűrését az originációs IP-cím alapján. A szűrési művelet az előtér-szerepkörökön történik, amelyek az alkalmazások at futtató feldolgozói szerepköröktől feljebb vannak. Mivel az előtér-szerepkörök a dolgozóktól feljebb vannak, a hozzáférési korlátozások funkció az alkalmazások hálózati szintű védelmének tekinthető. A szolgáltatás lehetővé teszi, hogy listát hozzon létre az engedélyezett és megtagadási címblokkokról, amelyek et prioritási sorrendben értékelik ki. Hasonló a Network Security Group (NSG) szolgáltatás, amely az Azure Networking.  Ezt a funkciót használhatja egy ASE vagy a több-bérlős szolgáltatás. Ha ILB ASE-vel használja, korlátozhatja a hozzáférést a privát címblokkokból.

![Hozzáférési korlátozások](media/networking-features/access-restrictions.png)

A Hozzáférési korlátozások szolgáltatás segítséget nyújt olyan esetekben, amikor korlátozni szeretné az alkalmazás eléréséhez használható IP-címeket. A funkció használati esetei közé tartoznak a következők:

* Az alkalmazáshoz való hozzáférés korlátozása jól definiált címekből 
* Korlátozza a hozzáférést egy terheléselosztási szolgáltatáson keresztül, például az Azure Bejárati ajtaján keresztül. Ha le szeretné zárni az Azure Bejárati ajtajába irányuló bejövő forgalmat, hozzon létre szabályokat a 147.243.0.0/16 és a 2a01:111:2050:/44 forgalom engedélyezéséhez. 

![Hozzáférési korlátozások a bejárati ajtóval](media/networking-features/access-restrictions-afd.png)

Ha szeretné lezárni az alkalmazáshoz való hozzáférést, hogy az csak az Azure virtuális hálózat (VNet) erőforrásaiból érhető el, statikus nyilvános címre van szüksége, függetlenül a forrás a virtuális hálózatban. Ha az erőforrások nem rendelkeznek nyilvános címmel, használja inkább a Szolgáltatásvégpontok szolgáltatást. Ismerje meg, hogyan engedélyezheti ezt a funkciót a [hozzáférési korlátozások konfigurálásáról][iprestrictions]szóló oktatóanyaggal.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A szolgáltatásvégpontok lehetővé teszik az alkalmazás **bejövő** hozzáférésének zárolását, így a forráscímnek a kiválasztott alhálózatok készletéből kell származnia. Ez a szolgáltatás az IP-hozzáférési korlátozásokkal együtt működik. A szolgáltatásvégpontok az IP-hozzáférési korlátozásokkal azonos felhasználói élményben vannak beállítva. Létrehozhat egy engedélyezési/megtagadási listát a hozzáférési szabályok, amely tartalmazza a nyilvános címeket, valamint a virtuális hálózatok alhálózatok. Ez a szolgáltatás támogatja a forgatókönyveket, mint például:

![szolgáltatásvégpontok](media/networking-features/service-endpoints.png)

* Alkalmazásátjáró beállítása az alkalmazással az alkalmazás bejövő forgalmának zárolására
* Az alkalmazáshoz való hozzáférés korlátozása a virtuális hálózat erőforrásaihoz. Ez magában foglalhatja a virtuális gépeket, az ASEs-eket vagy akár más alkalmazásokat, amelyek virtuális hálózati integrációt használnak 

![szolgáltatásvégpontok alkalmazásátjáróval](media/networking-features/service-endpoints-appgw.png)

A szolgáltatásvégpontok alkalmazással történő konfigurálásáról a [szolgáltatásvégpontok hozzáférési korlátozásainak konfigurálásáról][serviceendpoints] szóló oktatóanyagban olvashat bővebben.

### <a name="private-endpoint-preview"></a>Saját végpont (előzetes verzió)

A Private Endpoint egy olyan hálózati felület, amely az Azure Private Link által privát és biztonságos kapcsolatot biztosít a webalkalmazással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, így a webalkalmazás hatékonyan hozza a virtuális hálózatba. Ez a funkció csak a **webalkalmazásba irányuló bejövő** folyamatokra szolgál.
[Privát végpontok használata az Azure Web Apphoz (előzetes verzió)][privateendpoints]
 
### <a name="hybrid-connections"></a>Hibrid kapcsolatok

Az App Service hibrid kapcsolatok lehetővé teszi, hogy az alkalmazások **kimenő** hívásokat kezdeményezzenek a megadott TCP-végpontok számára. A végpont lehet a helyszínen, egy virtuális hálózatban, vagy bárhol, amely lehetővé teszi a kimenő forgalmat az Azure-ba a 443-as porton. A szolgáltatáshoz egy hibrid kapcsolatkezelő (HCM) nevű továbbítóügynök telepítésére van szükség Windows Server 2012 vagy újabb gazdagépen. A HCM-nek képesnek kell lennie az Azure Relay elérésére a 443-as porton. A HCM letölthető az App Service hibrid kapcsolatok felhasználói felületéről a portálon. 

![Hibrid kapcsolatok hálózati folyamata](media/networking-features/hybrid-connections.png)

Az App Service hibrid kapcsolatok szolgáltatás az Azure Relay hibrid kapcsolatok funkcióra épül. Az App Service a szolgáltatás egy speciális formáját használja, amely csak az alkalmazásból a TCP-állomásra és a portra irányuló kimenő hívásokat támogatja. Ezt az állomást és portot csak azon az állomáson kell feloldani, ahol a HCM telepítve van. Amikor az alkalmazás az App Service-ben DNS-kereséset végez a hibrid kapcsolatban definiált gazdagépen és porton, a rendszer automatikusan átirányítja a forgalmat a hibrid kapcsolaton keresztül, és kiakad a hibrid kapcsolatkezelőn. Ha többet szeretne megtudni a hibrid kapcsolatokról, olvassa el az [App Service hibrid kapcsolatok dokumentációját][hybridconn]

Ezt a funkciót általában a következőkre használják:

* Erőforrások elérése olyan magánhálózatokban, amelyek nem csatlakoznak az Azure-hoz VPN-nel vagy ExpressRoute-tal
* Támogatja a helyszíni alkalmazások áthelyezését az App Service szolgáltatásba anélkül, hogy a támogató adatbázisokat is át kellene helyeznie  
* Biztonságos hozzáférést biztosít egyetlen állomáshoz és porthoz hibrid kapcsolatonként. A legtöbb hálózati szolgáltatás nyílt hozzáférést biztosít a hálózathoz, és a hibrid kapcsolatok csak az egyetlen állomás és port érhető el.
* Más kimenő kapcsolódási módszerek által nem lefedett forgatókönyvek lefedése
* Fejlesztés végrehajtása az App Service-ben, ahol az alkalmazások könnyen kihasználhatják a helyszíni erőforrásokat 

Mivel a szolgáltatás lehetővé teszi a hozzáférést a helyszíni erőforrásokhoz bejövő tűzfallyuk nélkül, népszerű a fejlesztők számára. A többi kimenő App Service hálózati funkciók nagyon Azure virtuális hálózattal kapcsolatos. A hibrid kapcsolatok nem függ a virtuális hálózaton keresztül, és a hálózati igények szélesebb köréhez használható. Fontos megjegyezni, hogy az App Service hibrid kapcsolatok funkció nem érdekel, vagy tudja, mit csinál a tetején. Ez azt jelenti, hogy használhatja egy adatbázis, egy webszolgáltatás vagy egy tetszőleges TCP szoftvercsatorna eléréséhez a nagyszámítógépen. A szolgáltatás lényegében atcp-csomagokat aktasz. 

Míg a hibrid kapcsolatok népszerű a fejlesztés, azt is használják számos termelési alkalmazások is. Kiválóan alkalmas egy webszolgáltatás vagy adatbázis elérésére, de nem megfelelő olyan helyzetekben, amelyek sok kapcsolatot hoznak létre. 

### <a name="gateway-required-vnet-integration"></a>Átjáró szükséges virtuális hálózati integráció 

Az átjáró szükséges App Service VNet-integrációs funkció lehetővé teszi, hogy az alkalmazás **kimenő** kérelmeket egy Azure virtuális hálózatba. A szolgáltatás úgy működik, hogy csatlakoztatja a gazdagép az alkalmazás fut egy virtuális hálózati átjáró a virtuális hálózat egy pont-hely VPN. A szolgáltatás konfigurálásakor az alkalmazás megkapja az egyes példányokhoz rendelt helyek közötti címek egyikét. Ez a szolgáltatás lehetővé teszi, hogy a klasszikus vagy az erőforrás-kezelő virtuális hálózatai erőforrásait bármely régióban. 

![Átjáró szükséges virtuális hálózati integráció](media/networking-features/gw-vnet-integration.png)

Ez a szolgáltatás megoldja a más virtuális hálózatok erőforrásainak elérésével kapcsolatos problémát, és akár egy virtuális hálózaton keresztül más virtuális hálózatokhoz vagy akár a helyszínen is használható. Nem működik az ExpressRoute-alapú csatlakoztatott virtuális hálózatokkal, de a helyek közötti VPN-csatlakoztatott hálózatokkal igen. Általában nem megfelelő, hogy ezt a funkciót egy alkalmazásból egy App Service-környezetben (ASE), mert az ASE már a virtuális hálózatban. A szolgáltatás által megoldott használati esetek a következők:

* Erőforrások elérése privát IP-ken az Azure virtuális hálózataiban 
* Erőforrások elérése a helyszínen, ha van helyről webhelyre VPN 
* Erőforrások elérése társtársi hálózatokban 

Ha ez a szolgáltatás engedélyezve van, az alkalmazás azt a DNS-kiszolgálót fogja használni, amelyhez a célvirtuális hálózat konfigurálva van. Erről a funkcióról az [App Service virtuális hálózat integrációja][vnetintegrationp2s]című dokumentumban olvashat bővebben. 

### <a name="vnet-integration"></a>VNet-integráció

A szükséges virtuális hálózat integrációs szolgáltatás a szükséges, de még mindig nem oldja meg az erőforrások elérését az ExpressRoute-on keresztül. Az ExpressRoute-kapcsolatokon keresztüli elérés mellett az alkalmazásoknak is tudniuk kell hívásokat kezdeményezni a szolgáltatásvégpont-alapú biztonságos szolgáltatásokhoz. Mindkét további igény megoldásához egy másik virtuális hálózat integrációs képesség került hozzáadásra. Az új virtuális hálózat integrációs szolgáltatás lehetővé teszi, hogy helyezze el az alkalmazás háttér-egy alhálózat egy Erőforrás-kezelő virtuális hálózat ugyanabban a régióban. Ez a szolgáltatás nem érhető el egy App Service-környezetben, amely már a virtuális hálózatban. Ez a funkció lehetővé teszi:

* Erőforrások elérése az erőforrás-kezelő virtuális hálózataiban ugyanabban a régióban
* Szolgáltatásvégpontokkal védett erőforrások elérése 
* Az ExpressRoute- vagy VPN-kapcsolatokon keresztül elérhető erőforrások elérése
* Az összes kimenő forgalom biztonságossá tétele 
* Az összes kimenő forgalom bújtatásának kényszerítése. 

![VNet-integráció](media/networking-features/vnet-integration.png)

A funkcióról az [App Service virtuális hálózat integrációjáról][vnetintegration]szóló dokumentumokban olvashat bővebben.

## <a name="app-service-environment"></a>App Service Environment 

Az App Service-környezet (ASE) az Azure App Service egyetlen bérlői telepítése, amely a virtuális hálózaton fut. Az ASE lehetővé teszi a használati esetek, mint például:

* Erőforrások elérése a virtuális hálózatban
* Erőforrások elérése az ExpressRoute-on keresztül
* Az alkalmazások felfedése privát címmel a virtuális hálózatban 
* Erőforrások elérése a szolgáltatás végpontjai között 

Egy ASE, nem kell használni a funkciókat, például a virtuális hálózat integrációja vagy a szolgáltatás végpontok, mert az ASE már a virtuális hálózatban. Ha szolgáltatások végpontokon keresztül szeretné elérni az olyan erőforrásokat, mint az SQL vagy a Storage, engedélyezze a szolgáltatásvégpontokat az ASE-alhálózaton. Ha a virtuális hálózat erőforrásaihoz szeretne hozzáférni, nincs szükség további konfigurációra.  Ha az ExpressRoute-on keresztül szeretne erőforrásokat elérni, már a virtuális hálózatban van, és nem kell konfigurálnia semmit az ASE-n vagy a benne lévő alkalmazásokon. 

Mivel az ILB ASE-ben lévő alkalmazások privát IP-címen is elérhetők lehetnek, egyszerűen hozzáadhat WAF-eszközöket, hogy csak a kívánt alkalmazásokat tetszhet ki az internetre, és tartsa a többit biztonságban. Ez alkalmas arra, hogy könnyen fejlesztése többszintű alkalmazások. 

Vannak olyan dolgok, amelyek még nem lehetségesek a több-bérlős szolgáltatásból, amelyek egy ASE-ből származnak. Ezek közé tartozik a dolgok, mint például:

* Az alkalmazások megjelenítése privát IP-címen
* Az összes kimenő forgalom biztonságossá tétele olyan hálózati vezérlőkkel, amelyek nem részei az alkalmazásnak 
* Az alkalmazások egyetlen bérlői szolgáltatásban való üzemeltetése 
* A több-bérlős szolgáltatásban lehetségesnél sokkal több példányra skálázható 
* Privát hitelesítésszolgáltatói ügyféltanúsítványok betöltése az alkalmazások által iminált használatra a saját hitelesítésszolgáltató által védett végpontokkal 
* A TLS 1.1 kényszerítése a rendszerben tárolt összes alkalmazásra anélkül, hogy le tiltaná az alkalmazás szintjén 
* Adjon meg egy dedikált kimenő címet az ASE összes olyan alkalmazásához, amely nincs megosztva egyetlen ügyféllel sem 

![ASE egy virtuális hálózatban](media/networking-features/app-service-environment.png)

Az ASE biztosítja a legjobb történet körül elszigetelt és dedikált app hosting, de nem jön néhány kezelési kihívásokat. Néhány dolog, hogy fontolja meg, mielőtt egy működő ASE a következők:
 
 * Az ASE a virtuális hálózaton belül fut, de a virtuális hálózaton kívüli függőségekkel rendelkezik. Ezeket a függőségeket engedélyezni kell. További információ: [Az App Service-környezet hálózati szempontjai][networkinfo]
 * Az ASE nem skálázódik azonnal, mint a több-bérlős szolgáltatás. A méretezési igényeket kell előre jeleznie, nem pedig újraésen skálázás. 
 * Az ASE-nek magasabb előzetes költsége van. Annak érdekében, hogy a legtöbbet hozza ki az ASE, meg kell terveznie üzembe sok számítási feladatok egy ASE helyett azt használják a kis erőfeszítések
 * Az ASE-ben lévő alkalmazások nem korlátozhatják a hozzáférést az ASE egyes alkalmazásaihoz, és nem másokhoz.
 * Az ASE egy alhálózatban van, és a hálózati szabályok az adott ASE-be irányuló és onnan érkező összes forgalomra vonatkoznak. Ha csak egy alkalmazáshoz szeretne bejövő forgalmi szabályokat hozzárendelni, használja a hozzáférési korlátozásokat. 

## <a name="combining-features"></a>Jellemzők egyesítése 

A több-bérlős szolgáltatás megjegyezte, hogy együtt lehet megoldani bonyolultabb használati esetek. A leggyakoribb használati esetek közül kettőt itt ismertetett, de ezek csak példák. A különböző funkciók megértésével szinte az összes rendszerarchitektúrára szüksége van.

### <a name="inject-app-into-a-vnet"></a>Alkalmazás befecskendezése virtuális hálózatba

Gyakori kérés, hogyan helyezheti el az alkalmazást egy virtuális hálózatban. Az alkalmazás virtuális hálózatba helyezése azt jelenti, hogy az alkalmazás bejövő és kimenő végpontjai egy virtuális hálózaton belül vannak. Az ASE a legjobb megoldást a probléma megoldására, de a funkciók kombinálásával a legtöbb, amire szükség van a több-bérlős szolgáltatás kombinálásával. Például csak a magánbejövő és kimenő címekkel rendelkező intranetes alkalmazásokat üzemeltethet:

* Alkalmazásátjáró létrehozása privát bejövő és kimenő címmel
* Az alkalmazásba irányuló bejövő forgalom biztonságossá tétele szolgáltatásvégekkel 
* Használja az új virtuális hálózat-integrációt, hogy az alkalmazás háttérrendszere a virtuális hálózatban 

Ez a telepítési stílus nem ad dedikált címet az internetre irányuló kimenő forgalomhoz, és nem teszi lehetővé az alkalmazásból érkező összes kimenő forgalom zárolását.  Ez a telepítési stílus adna egy nagy, amit csak egyébként kap egy ASE. 

### <a name="create-multi-tier-applications"></a>Többrétegű alkalmazások létrehozása

A többrétegű alkalmazás olyan alkalmazás, ahol az API-háttéralkalmazások csak az előtér-rétegből érhetők el. Többrétegű alkalmazás létrehozásához a következőket teheti:

* A Virtuálishálózat-integráció használata az előtér-webalkalmazás háttérrendszerének csatlakoztatásához egy virtuális hálózat alhálózatához
* Szolgáltatásvégpontok használatával biztonságos sáb-mentes bejövő forgalmat az API-alkalmazás csak az előtér-webalkalmazás által használt alhálózatból érkezik

![többrétegű alkalmazás](media/networking-features/multi-tier-app.png)

Több előtér-alkalmazás is használhatja ugyanazt az API-alkalmazást a vnet-integráció használatával a többi előtér-alkalmazások és az API-alkalmazás szolgáltatásvégpontok az alhálózatok.  

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
