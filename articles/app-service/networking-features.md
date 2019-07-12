---
title: Központi telepítési szolgáltatások – az Azure App Service hálózatkezelési |} A Microsoft Docs
description: A különféle App Service-ben a hálózati funkciók használata
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: db29d0761084e32d601dc9c6d94082cd09bc5d18
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655468"
---
# <a name="app-service-networking-features"></a>Az App Service hálózatkezelési szolgáltatások

Az Azure App Service-alkalmazások többféleképpen is telepíthető. Alapértelmezés szerint az App Service-ben üzemeltetett alkalmazások közvetlenül elérhető az internetről, és csak internet üzemeltetett végpontok érhető el. Számos ügyfél alkalmazás azonban kell a bejövő és kimenő adatforgalmát szabályozza. Nincsenek elérhető az ilyen igények kielégítésére az App Service-ben több funkciót. A kihívás, hogy milyen funkció egy adott probléma megoldására használandó van. Ez a dokumentum célja segítségével megállapítható, hogy melyik szolgáltatást kell használni, néhány példa használati esetek alapján.

Az Azure App Service a két fő üzembe helyezési típusa van. A több-bérlős nyilvános szolgáltatást, amely futtatja az App Service-csomagok az ingyenes, közös, alapszintű, Standard, prémium szintű és Premiumv2 van díjköteles termékváltozatok. Nincs az App Service Environment, amely közvetlenül az Azure virtuális hálózaton (VNet) Termékváltozat izolált App Service-csomagok egyetlen bérlő. Ha a több-bérlős szolgáltatás, vagy az ASE környezetben használt a függ. 

## <a name="multi-tenant-app-service-networking-features"></a>Több-bérlős App Service hálózatkezelési szolgáltatások 

Az Azure App Service egy elosztott rendszer. A szerepkörök, amelyek a bejövő HTTP/HTTPS-kérelmeket kezelik az előtér-kiszolgálókon nevezzük. A szerepkörök, amelyek az ügyfél számítási feladata feldolgozók nevezzük. A szerepkörök az App Service-környezet összes egy több-bérlős hálózati szerepel. Az azonos App Service-skálázási egység számos különböző ügyfél is van, mert az App Service-ben hálózati a hálózathoz való közvetlen nem lehet csatlakoztatni. Helyett a hálózatok kapcsolódik, meg kell kezelni a különböző aspektusait alkalmazás a kommunikációs szolgáltatásokat. A Funkciók, amelyek az alkalmazás kéréseinek kezeléséhez nem használható megoldani a problémákat, amikor az ALKALMAZÁSBÓL. Hasonlóképpen kapcsolatos problémák megoldásához a hívásokat az ALKALMAZÁSBÓL szolgáltatások megoldani a problémákat az ALKALMAZÁSBAN nem használható.  

| Bejövő funkciók | Kimenő funkciók |
|---------------------|-------------------|
| Alkalmazás-címet | Hibrid kapcsolatok |
| Korlátozza a hozzáférést | Átjáró szükséges a VNet-integráció |
| Service Endpoints – szolgáltatásvégpont | VNet-integráció (előzetes verzió) |

Ha másként nincs jelezve, az a funkciók is együtt kell használni. Kombinálhatja a szolgáltatásokat a különféle problémák megoldásában.

## <a name="use-case-and-features"></a>Használati és funkciók

Bármely adott használati esetekhez lehet megoldani a problémát néhány módszer.  A megfelelő funkció használatához okokból nem csak magát az használatieset néha okozza. A következő bejövő használati esetek javasolni használata App Service-ben a hálózati funkciók szabályozása a forgalmat az alkalmazás körül problémák megoldásához. 
 
| Bejövő alkalmazási helyzetek | Funkció |
|---------------------|-------------------|
| Az alkalmazás IP-alapú SSL igények támogatása | Alkalmazás-címet |
| Nem megosztott, az alkalmazás dedikált bejövő címe | Alkalmazás-címet |
| Az alkalmazás jól definiált címek készletéből elérésének korlátozása | Korlátozza a hozzáférést |
| Tegye elérhetővé a magánhálózati IP-címek a virtuális hálózatomban saját alkalmazás | ILB ASE </br> A Szolgáltatásvégpontok az Application Gateway |
| Az alkalmazás az egy virtuális hálózatban található erőforrások elérésének korlátozása | Service Endpoints – szolgáltatásvégpont </br> ILB ASE |
| Tegye elérhetővé a saját virtuális hálózaton egy privát IP-címet az alkalmazásom | ILB ASE </br> magánhálózati IP-címeként bejövő a Szolgáltatásvégpontok egy Application Gateway-en |
| A webalkalmazási Tűzfallal rendelkező alkalmazás védelme | Az Application Gateway + ILB ASE környezetben </br> A Szolgáltatásvégpontok az Application Gateway </br> Korlátozza a hozzáférést az Azure szabályokat |
| Saját alkalmazások különböző régiókban lévő forgalom terheléselosztása | Korlátozza a hozzáférést az Azure szabályokat | 
| Ugyanabban a régióban forgalom terheléselosztása | A Szolgáltatásvégpontok az Application Gateway | 

A következő kimenő használati esetek javasolni App Service-ben a hálózati funkciók használata az alkalmazás kimenő hozzáférést kell megoldani. 

| Kimenő alkalmazási helyzetek | Funkció |
|---------------------|-------------------|
| Egy Azure virtuális hálózat ugyanabban a régióban lévő erőforrások eléréséhez | VNet-integráció </br> ASE |
| Egy Azure virtuális hálózaton egy másik régióban lévő erőforrások eléréséhez | Átjáró szükséges a VNet-integráció </br> ASE és a virtuális hálózatok közötti társviszony-létesítés |
| A Szolgáltatásvégpontok védett erőforrások eléréséhez | VNet-integráció </br> ASE |
| Nincs csatlakoztatva az Azure-bA egy magánhálózaton lévő erőforrások eléréséhez | Hibrid kapcsolatok |
| Erőforrások eléréséhez ExpressRoute-Kapcsolatcsoportok között | (Egyelőre RFC 1918-címek korlátozott) VNet-integráció </br> ASE | 


### <a name="default-networking-behavior"></a>Alapértelmezett hálózati viselkedés

Az Azure App Service-skálázási egység minden környezetben számos ügyfél támogatja. Az ingyenes és közös Termékváltozat csomagok több-bérlős feldolgozók az ügyfél munkaterheléseinek üzemeltetéséhez. Az alapszintű és a fenti csomagok gazdagép ügyfél munkaterheléseinek, amelyek dedikált csak egy App Service-csomagot (ASP). Ha egy standard szintű App Service-csomagot, majd az alkalmazások csomag fog futtatása feldolgozón a azonos. Ha a feldolgozó horizontális felskálázása, majd az alkalmazások, hogy az ASP a replikálja egy új feldolgozón az ASP minden példány esetében. A dolgozók által használt Premiumv2 nem azonosak az egyéb csomagok használt feldolgozók. Minden App Service-környezet, amely minden, a bejövő forgalom az adott App Service-környezet Apps szolgál egy IP-címmel rendelkezik. Vannak azonban bárhol 4 kimenő hívások 11 címekhez. Ezek a címek vannak megosztva az alkalmazások által az adott App Service-környezet. A kimenő címek eltérnek a különböző munkavégző típusok alapján. Ez azt jelenti, hogy az ingyenes, közös, alapszintű, Standard és prémium szintű ASP által használt címek eltérnek a Premiumv2 ASP kimenő hívásait használt címek. Ha az alkalmazás megkeresni a tulajdonságai, láthatja a bejövő és kimenő címek, az alkalmazás által használt. Ha zárolását, így az IP-ACL-függőség van szüksége, használja a possibleOutboundAddresses. 

![Alkalmazás tulajdonságai](media/networking-features/app-properties.png)

App Service számos olyan végpontok, amelyek a szolgáltatás kezelése.  Ezt a címet egy külön dokumentum közzétett, és a AppServiceManagement IP szolgáltatáscímke is megtalálhatók. A AppServiceManagement címkét csak az egy App Service Environment (ASE), engedélyeznie kell a ilyen forgalom használja. Az App Service, az App Service IP szolgáltatáscímke bejövő címek követi nyomon. Nem, amely tartalmazza a kimenő címek App Service által használt IP-szolgáltatáscímke van. 

![App Service-ben a bejövő és kimenő diagramja](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Alkalmazás-címet 

Az alkalmazás hozzárendelt cím funkciót egy offshoot az IP-alapú SSL funkció, és az SSL beállítása az alkalmazás hozzáfér. Ez a szolgáltatás IP-alapú SSL-hívásokhoz használható, de azt is használható adjon az alkalmazásnak, hogy csak egy címet. 

![Alkalmazáshoz hozzárendelt cím diagramja](media/networking-features/app-assigned-address.png)

Ha egy alkalmazás-címet használ, a forgalom továbbra is végighalad a azonos előtér-szerepköröket kezelni tudja a bejövő forgalmat, az App Service-ben skálázási egység. A cím, amely hozzá van rendelve az alkalmazás azonban csak az alkalmazás használja. Ez a szolgáltatás alkalmazási helyzeteit a következők:

* Az alkalmazás IP-alapú SSL igények támogatása
* Az alkalmazás, amelyek nincsenek megosztva, bármi más dedikált cím beállítása

Megismerheti, hogyan állíthatja be egy címet az oktatóanyag az alkalmazásában lévő is [konfigurálása IP-alapú SSL][appassignedaddress]. 

### <a name="access-restrictions"></a>Korlátozza a hozzáférést 

A hozzáférési korlátozásokat képesség lehetővé teszi a **bejövő** kérelmeket az eredeti IP-cím alapján. A szűrési művelet kerül sor az előtér-szerepkörökhöz, amelyek a feldolgozó tekercsben a felsőbb rétegbeli, ahol az alkalmazások futnak. Mivel a dolgozók a felsőbb rétegbeli az előtér-szerepkörök, a hozzáférési korlátozásokat funkció tekinthető hálózati szintű védelmet alkalmazásai számára. A funkció lehetővé teszi, hogy hozhat létre listáját engedélyezéséhez és megtagadásához-címblokkok prioritási sorrendben értékeli ki. Ez hasonlít a hálózati biztonsági csoport (NSG) szolgáltatást, amely megtalálható az Azure-hálózatot.  Ez a funkció is használhatja, az ASE környezetben vagy a több-bérlős szolgáltatás. Az ILB ASE használata esetén korlátozhatja a hozzáférést a privát címterületet.

![Korlátozza a hozzáférést](media/networking-features/access-restrictions.png)

A hozzáférési korlátozásokat szolgáltatás segítségével a forgatókönyvekben, ahol az IP-címek, amelyek az alkalmazás eléréséhez használható korlátozni szeretné. Többek között a használat esetek ennek a funkciónak a következők:

* Az alkalmazás jól definiált címek készletéből elérésének korlátozása 
* Terheléselosztási szolgáltatás, például az Azure bejárati ajtajának beérkező való hozzáférés korlátozása. Ha a bejövő forgalom az Azure bejárati ajtajának zárolását, létrehozhat olyan szabályokat, 147.243.0.0/16 és 2a01:111:2050 érkező adatforgalom engedélyezéséhez:: / 44. 

![Korlátozza a hozzáférést a bejárati ajtó](media/networking-features/access-restrictions-afd.png)

Ha szeretné, hogy ez csak érhető el az erőforrásokat az Azure virtuális hálózaton (VNet) az alkalmazáshoz való hozzáférés zárolása, kell egy statikus nyilvános cím, függetlenül a forrása a virtuális hálózatban található. Az erőforrások nem rendelkeznek a nyilvános cím, a Szolgáltatásvégpontok funkció Ehelyett használjon. Ismerje meg, hogyan engedélyezze ezt a funkciót, az oktatóanyag a [hozzáférési korlátozások konfigurálása][iprestrictions].

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A Szolgáltatásvégpontok lehetővé teszi, hogy zárolását **bejövő** hozzáférni az alkalmazáshoz úgy, hogy a forrás címe kiválasztott alhálózatok halmaza kell származnia. Ez a funkció az IP-korlátozása együtt működik. A Szolgáltatásvégpontok meg ugyanazt a felhasználói élményt, mint az IP-korlátozása. Egy engedélyezési vagy megtagadási lista a hozzáférési szabályok, amely tartalmazza a nyilvános cím, valamint alhálózatokat a virtuális hálózatok hozhat létre. Ez a szolgáltatás például a forgatókönyveket teszi lehetővé:

![Szolgáltatásvégpontok](media/networking-features/service-endpoints.png)

* Egy Application Gateway az alkalmazással való zárolását, így az alkalmazás bejövő forgalmát beállítása
* Az alkalmazás a virtuális hálózat erőforrásaihoz való hozzáférés korlátozása. Ide tartozhatnak a virtuális gépek, ASE vagy akár egyéb VNet-integráció használó alkalmazások 

![a Szolgáltatásvégpontok az application gateway segítségével](media/networking-features/service-endpoints-appgw.png)

További konfigurálásáról az alkalmazását a Szolgáltatásvégpontok az oktatóanyagban a [szolgáltatási végpont hozzáférési korlátozások konfigurálása][serviceendpoints]
 
### <a name="hybrid-connections"></a>Hibrid kapcsolatok

App Service hibrid kapcsolatai lehetővé teszi, hogy az alkalmazásokat, hogy a **kimenő** megadott TCP-végpontokra irányuló hívásokat. A végpont a helyszínen, egy virtuális hálózatban vagy bárhol, amely lehetővé teszi, hogy az Azure-hoz a 443-as porton a kimenő forgalmat. A funkcióhoz egy Windows Server 2012 vagy újabb-gazdagépen a hibrid kapcsolat Manager (HCM) nevű továbbító ügynök telepítését. A HCM kell tudni érnie az Azure Relay a 443-as porton. A HCM letölthető az App Service hibrid kapcsolatok felhasználói felülete a portálon. 

![A hibrid kapcsolatok hálózati forgalom](media/networking-features/hybrid-connections.png)

Az App Service hibrid kapcsolataira szolgáltatás az Azure Relay hibrid kapcsolatok képesség épül. Az App Service egy speciális formája, a szolgáltatás, amely csak támogatja és kimenő hívásokat az alkalmazásból, egy TCP-állomás és port használja. A gazdagép és a port csak kell hárítania a gazdagépen, amelyen a HCM telepítve van. Az alkalmazás az App Service-ben a gazdagép és a hibrid kapcsolat meghatározott portot egy DNS-címkeresés használható, amikor a rendszer automatikusan átirányítja a forgalmat a hibrid kapcsolaton keresztül, és a Hybrid Connection Manager ki ugorhat. Hibrid kapcsolatok kapcsolatos további információkért olvassa el a dokumentációt [App Service hibrid kapcsolataira][hybridconn]

Ez a szolgáltatás általában arra használják:

* Magánhálózatokon, amelyek nem csatlakoznak az Azure-bA a VPN vagy ExpressRoute-erőforrások eléréséhez
* Támogatási átemeléses a helyszíni alkalmazások App Service-ben anélkül, hogy is a támogató adatbázisok áthelyezése  
* Biztonságos hozzáférés biztosítása egy egyetlen állomás és port hibrid kapcsolatonként. A legtöbb hálózati szolgáltatások nyissa meg a hálózati hozzáférés és a hibrid kapcsolatokkal csupán a egyetlen állomás és port érhető el.
* Nem fedi le más kimenő kapcsolat módszerek forgatókönyveket fednek le
* Hajtsa végre a fejlesztés az App Service szolgáltatásban, ahol a könnyen hasznosíthatók az alkalmazásokban a helyszíni erőforrásokhoz 

A funkció lehetővé teszi, hogy egy bejövő tűzfalra watering nélkül a helyszíni erőforrásokhoz való hozzáférés, mivel, népszerű fejlesztőkkel. A más kimenő App Service hálózatkezelési szolgáltatásokat is nagyon Azure virtuális hálózatkezeléssel kapcsolatos. Hibrid kapcsolatok egy virtuális hálózaton keresztül egy függőség nem rendelkezik, és hálózati igényeinek megfelelően többféle is használható. Fontos megjegyezni, hogy az App Service hibrid kapcsolataira szolgáltatás nem gondoskodik és nem tudja, mi végeznek megkönnyítése. Tegyük fel, hogy ezzel el egy adatbázist, egy webszolgáltatás, vagy egy tetszőleges TCP szoftvercsatorna-nagyszámítógépes a is. A szolgáltatás lényegében alagutak TCP-csomagokat. 

Bár a hibrid kapcsolatok népszerű fejlesztéshez, is szolgál számos éles alkalmazásokban is. Kiválóan alkalmazható a webszolgáltatás vagy az adatbázis eléréséhez, de nem megfelelő helyzetek járó sok kapcsolat létrehozása. 

### <a name="gateway-required-vnet-integration"></a>Átjáró szükséges a VNet-integráció 

Az átjáró szükséges az App Service virtuális hálózat integrációja lehetővé teszi, hogy az alkalmazást, győződjön meg arról, hogy **kimenő** kérelmek egy Azure virtuális hálózatban. A funkció működik a gazdagépen, az alkalmazás fut, a virtuális hálózati átjárót a virtuális hálózathoz pont – hely VPN-nel a csatlakozással. A szolgáltatás konfigurálásakor az alkalmazás lekéri a pont – hely címek hozzárendelve az egyes példányok egyikére. Ez a funkció lehetővé teszi, hogy az erőforrások klasszikus vagy Resource Manager virtuális hálózatok bármelyik régióban érhető el. 

![Átjáró szükséges a VNet-integráció](media/networking-features/gw-vnet-integration.png)

Ez a funkció megoldja a problémát az erőforrások más virtuális hálózatok eléréséhez, és csatlakozni egy virtuális vagy más virtuális hálózatok, vagy akár a helyszíni hálózaton keresztül akkor is használható. Nem működik az expressroute használatával csatlakoztatott virtuális hálózatokat, de biztosítja a Site-to-site VPN csatlakoztatott hálózatokon. Általában nem helyénvaló használja ezt a szolgáltatást az alkalmazás az App Service környezet (ASE), mert a virtuális hálózat már szerepel az ASE-t. A használati esetek, hogy ez a funkció megoldást kínál a következők:

* Magánhálózati IP-címek az Azure virtuális hálózataiban lévő erőforrások eléréséhez 
* Erőforrásokhoz való hozzáférés a helyszíni van-e a site-to-site VPN 
* Társviszonyban lévő virtuális hálózatokban az erőforrások elérése 

Ha ez a funkció engedélyezve van, az alkalmazás, amelynek része a cél virtuális hálózat DNS-kiszolgáló fogja használni. Tudjon meg többet erről a szolgáltatásról a dokumentáció a [App Service VNet-integráció][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNet-integráció

Az átjáró a virtuális hálózat integrációja nagyon hasznos, de még nem oldja meg hozzáférni erőforrásokhoz között ExpressRoute szükséges. Kellene eléréséhez ExpressRoute-kapcsolatok között, felül van szükség az alkalmazások hajthatják végre a védett szolgáltatásokhoz szolgáltatásvégpont-hívások. A megoldásában mindkét további igényeinek, egy másik VNet-integráció képesség hozzá lett adva. Az új VNet-integráció funkciója lehetővé teszi az alkalmazás háttérszolgáltatásának helyezni, egy alhálózatot egy Resource Manager virtuális hálózat ugyanabban a régióban. Ez a funkció nem érhető el az App Service-környezet, amely már szerepel egy virtuális hálózathoz. A funkció az alábbi lehetőségeket kínálja:

* Resource Manager virtuális hálózatok ugyanabban a régióban lévő erőforrások eléréséhez
* A Szolgáltatásvégpontok védett erőforrásokhoz való hozzáférés 
* ExpressRoute vagy VPN-kapcsolaton keresztül elérhető erőforrásokhoz való hozzáféréshez

![VNet-integráció](media/networking-features/vnet-integration.png)

Ez a funkció előzetes verzióban érhető el, és nem használható éles számítási feladatok esetében. Ez a funkció kapcsolatos további információkért olvassa el a docs [App Service VNet-integráció][vnetintegration].

## <a name="app-service-environment"></a>App Service-környezet 

Az App Service Environment (ASE) az egyetlen új bérlő üzemelő példánya, amely a virtuális hálózatban fut az Azure App Service. Az ASE lehetővé teszi, hogy az alkalmazási helyzetek, például:

* A virtuális hálózatban található erőforrások eléréséhez
* Erőforrások eléréséhez ExpressRoute között
* Tegye elérhetővé az alkalmazások, a virtuális hálózaton egy privát címmel 
* A Szolgáltatásvégpontok között erőforrások eléréséhez 

Az ASE nem kell szolgáltatások, mint a VNet-integráció vagy Szolgáltatásvégpontok használatára, mivel a virtuális hálózat már szerepel az ASE-t. Ha szeretne hozzáférni az erőforrásokhoz, mint a Storage vagy az SQL-szolgáltatásvégpontokat keresztül, engedélyezze a szolgáltatásvégpontokat az ASE-alhálózatra. Ha azt szeretné, a virtuális hálózatban található erőforrások eléréséhez, nincs nincs szükség további konfigurációra.  Ha szeretne hozzáférni az erőforrásokhoz között ExpressRoute, a virtuális hálózatban már van, és nem kell semmit az ASE-t vagy az alkalmazásokat, konfigurálja. 

Az ILB ASE környezetben az alkalmazásokat elérhetővé tehető a magánhálózati IP-cím, mert könnyedén adhat hozzá WAF-eszközöket csak az alkalmazásokat, hogy az internetről szeretne, és a többi biztonságban elérhetővé. Ez adatmodelljeinek Többrétegű alkalmazások egyszerű fejlesztését. 

Néhány dolog, amelyek nem lehetséges a több-bérlős szolgáltatás, amely az ASE származnak. Ezek közé tartozik többek között:

* Tegye elérhetővé az alkalmazások a magánhálózati IP-cím
* A hálózati vezérlők, amelyek nem részei egy az alkalmazás minden kimenő forgalom védelme 
* Az alkalmazások egy egybérlős szolgáltatásban 
* Több példány is előfordulhatnak, a több-bérlős szolgáltatás, mint a vertikális felskálázás 
* Betöltés privát hitelesítésszolgáltató ügyfél által használt tanúsítványok az alkalmazások privát CA biztonságos végpontok 
* Összes letiltása alkalmazásszinten bármely lehetősége nélkül a rendszerben üzemeltetett alkalmazások a TLS 1.1 kényszerítése 
* Adjon meg egy dedikált kimenő címet a az alkalmazások, amelyek nincsenek megosztva egyetlen ügyfél sem, az ASE környezetben 

![ASE egy virtuális hálózaton](media/networking-features/app-service-environment.png)

Az ASE biztosítja a legjobb történetet elkülönített és dedikált alkalmazásüzemeltetés körül, de néhány felügyeleti kihívások kapható. Fontolja meg az operatív ASE használata előtt a következőkre van:
 
 * Egy ASE Környezethez a virtuális hálózaton belül fut, de a virtuális hálózaton kívül függőségekkel rendelkeznek. Ezekhez a függőségekhez engedélyezni kell. További információ: [App Service Environment hálózati szempontjai][networkinfo]
 * Az ASE nem azonnal méretezhető például a több-bérlős szolgáltatás. Előrejelezheti a skálázási igényeihez reaktív skálázás helyett kell. 
 * Az ASE magasabb szintű meghozni költség társítva van. Annak érdekében, hogy a legtöbbet az ASE ki, hogy kell tervezi, hogy hány számítási feladatok üzembe helyezése egy ASE ahelyett, hogy kis munkája alapján használt
 * Az alkalmazások az ASE környezetben nem korlátozza a hozzáférést az egyes alkalmazások az ASE másoknak pedig nem található.
 * Az ASE alhálózat és hálózati szabályok vonatkoznak, és az ASE összes forgalom. Ha szeretne hozzárendelni egy alkalmazás bejövő forgalomra vonatkozó szabályokat, használja a korlátozza a hozzáférést. 

## <a name="combining-features"></a>Szolgáltatások egyesítése 

A szolgáltatások, a több-bérlős szolgáltatás feljegyzett megoldani az összetettebb esetekhez együtt használható. A gyakori használati esetek közül kettő ebben a témakörben találhatók, de csak például szolgál. Megismerése a különféle szolgáltatások mire, meg tudja oldani szinte az összes rendszer-architektúra igényeinek.

### <a name="inject-app-into-a-vnet"></a>Alkalmazás behelyezése egy virtuális hálózathoz

Egy közös kérelmet el, hogyan legyen az alkalmazása a virtuális hálózat. Az alkalmazás üzembe helyezése egy virtuális hálózat azt jelenti, hogy egy alkalmazás bejövő és kimenő végpontjait egy virtuális hálózaton. Az ASE-t biztosít a legjobb megoldás a probléma megoldásához, de a legtöbb van szükség az a több-bérlős szolgáltatás funkciók kombinálásával kap. Például a bejövő és kimenő privát-csak intranet-alkalmazásokat is üzemeltethet:

* Privát bejövő és kimenő címmel rendelkező Application Gateway létrehozása
* A Szolgáltatásvégpontok az alkalmazáshoz a bejövő adatforgalom védelme 
* Használja az új VNet-integráció, így a háttérrendszer az alkalmazás a virtuális hálózat 

A központi telepítési stílus nem lenne dedikált címnek biztosítanak az internetre irányuló kimenő forgalom vagy lehetővé teszik, való zárolását, így az alkalmazás összes kimenő forgalmát.  Ez a központi telepítési stílus adnák vissza, az lenne ellenkező esetben csak az ASE egy jelentős. 

### <a name="create-multi-tier-applications"></a>Többrétegű alkalmazások létrehozása

Egy többrétegű alkalmazást egy alkalmazás, ahol a háttérbeli API-alkalmazások csak elérhető lesz az előtérréteg. Többrétegű alkalmazások létrehozásához, a következőket teheti:

* A háttérrendszer az előtérbeli webes alkalmazás csatlakoztatása egy virtuális hálózat alhálózatához VNet-integráció segítségével
* A Szolgáltatásvégpontok használatával csak a webes előtér-alkalmazás által használt alhálózatból érkező, az API-alkalmazás bejövő forgalmának biztonságossá tétele

![többrétegű alkalmazást](media/networking-features/multi-tier-app.png)

Használja ugyanazt az API-alkalmazás más előtér-alkalmazásokból és az API-alkalmazáshoz az alhálózatok szolgáltatásvégpontokat VNet-integráció használatával több előtér-alkalmazás is rendelkezhet.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
