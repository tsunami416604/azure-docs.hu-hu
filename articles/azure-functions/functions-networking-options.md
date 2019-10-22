---
title: Azure Functions hálózati beállítások
description: A Azure Functionsban elérhető összes hálózati beállítás áttekintése
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 9fe7147325b2e14a7ae6bb4b31aa941fb4059b11
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690827"
---
# <a name="azure-functions-networking-options"></a>Azure Functions hálózati beállítások

Ez a cikk a Azure Functions üzemeltetési lehetőségei között elérhető hálózati szolgáltatásokat ismerteti. Az alábbi hálózati beállítások mindegyike lehetővé teszi az erőforrások elérését az internetre irányítható címek használata nélkül, vagy korlátozza az internet-hozzáférést egy Function-alkalmazáshoz. 

Az üzemeltetési modellek különböző szintű hálózati elkülönítéssel rendelkeznek. A megfelelő kiválasztásával a hálózat elkülönítési követelményeinek megfelelőnek kell lennie.

A Function apps több módon is üzemeltethető:

* Több-bérlős infrastruktúrán futó, különböző szintű virtuális hálózati kapcsolattal és méretezési lehetőségekkel rendelkező tervezési lehetőségek állnak rendelkezésre:
    * A [fogyasztási terv](functions-scale.md#consumption-plan), amely dinamikusan méretezi a terhelést, és minimális hálózati elkülönítési lehetőségeket kínál.
    * A [Prémium csomag](functions-scale.md#premium-plan), amely szintén dinamikusan méretezi a hálózatot, miközben átfogóbb hálózati elkülönítést kínál.
    * Az Azure [app Service terv](functions-scale.md#app-service-plan), amely rögzített skálán működik, és hasonló hálózati elkülönítést biztosít a prémium csomaghoz.
* A függvényeket futtathatja egy [app Service Environmentban](../app-service/environment/intro.md). Ez a metódus üzembe helyezi a függvényt a virtuális hálózatban, és teljes hálózati vezérlést és elkülönítést biztosít.

## <a name="matrix-of-networking-features"></a>Hálózati szolgáltatások mátrixa

|                |[Felhasználási terv](functions-scale.md#consumption-plan)|[Prémium csomag (előzetes verzió)](functions-scale.md#premium-plan)|[App Service-csomag](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Bejövő IP-korlátozások & Private site Access](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Virtuális hálózat integrációja](#virtual-network-integration)|❌No|✅Yes (regionális)|✅Yes (regionális és átjáró)|✅Yes|
|[Virtuális hálózati eseményindítók (nem HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[Hibrid kapcsolatok](#hybrid-connections)|❌No|✅Yes|✅Yes|✅Yes|
|[Kimenő IP-korlátozások](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>Bejövő IP-korlátozások

Az IP-korlátozások segítségével megadhatja az alkalmazáshoz engedélyezett/elutasított IP-címek prioritással rendezett listáját. A lista IPv4-és IPv6-címeket is tartalmazhat. Ha van egy vagy több bejegyzés, a lista végén egy implicit "deny all" (összes megtagadás) szerepel. Az IP-korlátozások az összes funkció-üzemeltetési lehetőséggel működnek.

> [!NOTE]
> Ha hálózati korlátozásokkal rendelkezik, csak a virtuális hálózaton belüli portál-szerkesztőt használhatja, vagy ha engedélyezte annak a gépnek az IP-címét, amelyet a Azure Portal eléréséhez használ. Azonban továbbra is hozzáférhet bármely szolgáltatáshoz a **platform szolgáltatások** lapján bármely gépről.

További információ: [Azure app Service statikus hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Hozzáférés személyes oldalakhoz

A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. 
* A privát webhely elérése a [prémium](./functions-premium-plan.md), a [fogyasztási], a (functions-Scale. MD # fogyasztás-terv) és a [app Service tervben](functions-scale.md#app-service-plan) érhető el, amikor a **szolgáltatási végpontok** konfigurálva vannak. 
    * A szolgáltatási végpontok a platform funkciói > hálózatkezelés > a hozzáférési korlátozások konfigurálása > a szabály hozzáadása lehetőséggel konfigurálhatók. A virtuális hálózatok mostantól a szabály típusaként is kiválaszthatók.
    * További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
        * Ne feledje, hogy a szolgáltatási végpontok esetében a függvény továbbra is teljes kimenő hozzáférést biztosít az internethez, még a konfigurált Virtual Network Integration is.
* A privát helyhez való hozzáférés egy belső terheléselosztó (ILB) által konfigurált App Service Environment is elérhető. További információ: [belső terheléselosztó létrehozása és használata app Service Environmentsal](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

A virtuális hálózat integrációja lehetővé teszi, hogy a Function alkalmazás hozzáférjen a virtuális hálózaton belüli erőforrásokhoz. Ez a funkció a prémium csomagban és a App Service csomagban is elérhető. Ha az alkalmazás egy App Service Environmentban van, akkor már egy virtuális hálózatban van, és nem igényli a virtuális hálózati integráció használatát az azonos virtuális hálózat erőforrásainak eléréséhez.

A Virtual Network Integration használatával lehetővé teheti az alkalmazásokból való hozzáférést a virtuális hálózatban futó adatbázisokhoz és webszolgáltatásokhoz. A Virtual Network Integration szolgáltatással nem kell nyilvános végpontot közzétennie az alkalmazások számára a virtuális GÉPEN. Ehelyett használhatja a privát, nem internetes irányítható címeket is.

A Virtual Network Integration szolgáltatásnak két formája van

1. A regionális virtuális hálózati integráció lehetővé teszi az azonos régióban található virtuális hálózatokkal való integrációt. A szolgáltatás ezen formája megköveteli, hogy az azonos régióban található virtuális hálózat alhálózata legyen. Ez a szolgáltatás még előzetes verzióban érhető el, de a Windows-alkalmazások éles számítási feladataihoz az alábbiakban ismertetett kikötések használhatók.
2. Az átjáróhoz szükséges virtuális hálózati integráció lehetővé teszi a távoli régiókban lévő virtuális hálózatok és a klasszikus virtuális hálózatok integrálását. A szolgáltatás ezen verziója megköveteli a Virtual Network átjáró üzembe helyezését a VNet. Ez a pont – hely VPN-alapú szolgáltatás, és csak a Windows-alkalmazások esetében támogatott.

Egy alkalmazás egyszerre csak a VNet-integrációs szolgáltatás egyik formáját tudja használni. A kérdés az, hogy melyik szolgáltatást használja. Számos dolgot használhat. A egyértelmű differenciálók azonban a következők:

| Probléma  | Megoldás | 
|----------|----------|
| Egy adott régióban szeretné elérni az RFC 1918-es címeket (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). | regionális VNet-integráció |
| Szeretné elérni az erőforrásokat egy másik régióban található klasszikus VNet vagy VNet | átjáró szükséges VNet-integráció |
| Az RFC 1918-végpontok elérését szeretné elérni a ExpressRoute között | regionális VNet-integráció |
| Erőforrásokat szeretne elérni a szolgáltatási végpontok között | regionális VNet-integráció |

Egyik funkció sem teszi lehetővé a nem RFC 1918-címek elérését a ExpressRoute között. Ehhez a-t kell használnia.

A regionális VNet-integráció használata nem köti össze a VNet a helyszíni vagy a szolgáltatási végpontok konfigurálásával. Ez külön hálózati konfiguráció. A regionális VNet-integráció egyszerűen lehetővé teszi, hogy az alkalmazás hívásokat hajtson végre a kapcsolati típusok között.

A használt verziótól függetlenül a VNet integrációja lehetővé teszi a Function app számára a virtuális hálózat erőforrásaihoz való hozzáférést, de a virtuális hálózatról nem biztosít privát helyet a Function alkalmazáshoz. A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet. 

A VNet integrációs funkciója:

* Standard, prémium vagy PremiumV2 App Service csomagra van szükség
* a TCP és az UDP támogatása
* együttműködik App Service alkalmazásokkal és a Function apps szolgáltatással

Néhány dolog, amit a VNet-integráció nem támogat, beleértve a következőket:

* Meghajtó csatlakoztatása
* AD-integráció 
* NetBios

A functions Virtual Network Integration a App Service Web Apps szolgáltatással megosztott infrastruktúrát használ. Ha többet szeretne megtudni a virtuális hálózatok integrációjának két típusáról, tekintse meg a következőt:
* [Regionális VNET-integráció](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Átjáró szükséges VNet-integráció](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

A Virtual Network Integration használatával kapcsolatos további tudnivalókért lásd: [Function App-alkalmazás integrálása Azure-beli virtuális hálózattal](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Kapcsolódás a szolgáltatás végpontjának biztonságos erőforrásaihoz

> [!note] 
> Átmenetileg akár 12 órát is igénybe vehet, amíg az új szolgáltatási végpontok elérhetővé válnak a Function app számára, ha az alsóbb rétegbeli erőforráshoz hozzáférési korlátozásokat konfigurál. Ebben az időszakban az erőforrás teljes mértékben elérhetetlenné válik az alkalmazás számára.

Magasabb szintű biztonság biztosítása érdekében a szolgáltatás-végpontok segítségével számos Azure-szolgáltatást korlátozhat egy virtuális hálózatra. Ezután integrálnia kell a Function alkalmazást az adott virtuális hálózattal az erőforrás eléréséhez. Ezt a konfigurációt minden olyan csomag támogatja, amely támogatja a virtuális hálózatok integrálását.

[A Virtual Network szolgáltatásbeli végpontokról itt olvashat bővebben.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>A Storage-fiók korlátozása egy virtuális hálózatra
A Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. Ezen a fiókon jelenleg nem lehet virtuális hálózati korlátozásokat használni. Ha egy virtuális hálózati szolgáltatás végpontját konfigurálja a Function alkalmazáshoz használt Storage-fiókon, akkor az alkalmazás megszakítja az alkalmazást.

[A Storage-fiókra vonatkozó követelményekről itt olvashat bővebben.](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>Virtuális hálózati eseményindítók (nem HTTP)

Jelenleg a HTTP-n kívüli Function triggerek a virtuális hálózatról való használatához a Function alkalmazást egy App Service csomagban vagy egy App Service Environment kell futtatnia.

Ha például úgy kívánja konfigurálni a Azure Cosmos DBt, hogy csak egy virtuális hálózatról fogadjon forgalmat, akkor a Function app szolgáltatást egy app Service-csomagban kell telepítenie, amely virtuális hálózati integrációval rendelkezik az adott virtuális hálózattal Azure Cosmos DB triggerek konfigurálásához Ebből az erőforrásból. Az előzetes verzióban a VNET-integráció konfigurálása nem teszi lehetővé, hogy a Prémium csomag kiváltsa az adott Azure Cosmos DB erőforrást.

Tekintse meg [ezt a listát az összes nem http-eseményindítóról](./functions-triggers-bindings.md#supported-bindings) , ha duplán szeretné megtekinteni a támogatottak listáját.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[Hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) az Azure Relay szolgáltatása, amellyel más hálózatokban is elérheti az alkalmazás erőforrásait. Hozzáférést biztosít az alkalmazástól az alkalmazások végpontja számára. Nem használhatja az alkalmazást az alkalmazás elérésére. A Hibrid kapcsolatok az összes, de a felhasználási tervben futó függvények számára elérhető.

A Azure Functionsban használt módon minden hibrid csatlakozás egyetlen TCP-gazdagéphez és porthoz kapcsolódik. Ez azt jelenti, hogy a hibrid kapcsolatok végpontja bármely operációs rendszeren és alkalmazáson is lehet, ha a TCP-figyelési porthoz fér hozzá. A Hibrid kapcsolatok funkció nem tudja, vagy nem érdekli az alkalmazás protokollja, vagy hogy mi fér hozzá. Egyszerűen hálózati hozzáférést biztosít.

További információkért tekintse meg a [Hibrid kapcsolatok app Service dokumentációját](../app-service/app-service-hybrid-connections.md), amely az azonos konfigurációs lépéseken keresztül támogatja a függvényeket.

## <a name="outbound-ip-restrictions"></a>Kimenő IP-korlátozások

A kimenő IP-korlátozások csak egy App Service Environment központilag telepített függvények esetében érhetők el. Konfigurálhatja a kimenő korlátozásokat arra a virtuális hálózatra, amelyen a App Service Environment telepítve van.

Ha egy Function app-t egy prémium csomagba vagy egy virtuális hálózattal App Service tervbe integrál, az alkalmazás továbbra is képes lesz kimenő hívásokat kezdeményezni az internethez.

## <a name="next-steps"></a>Következő lépések
További információ a hálózatkezelésről és a Azure Functions: 

* [Kövesse a virtuális hálózatok integrálásának első lépéseit ismertető oktatóanyagot](./functions-create-vnet.md)
* [A functions hálózatkezeléssel kapcsolatos gyakori kérdések](./functions-networking-faq.md)
* [További információ a App Service/functions Virtual Network Integration szolgáltatásáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózatkezelési funkciók és vezérlés App Service környezetekkel](../app-service/environment/intro.md)
* [Helyi erőforrásokhoz való kapcsolódás tűzfal módosítása nélkül Hibrid kapcsolatok használatával](../app-service/app-service-hybrid-connections.md)
