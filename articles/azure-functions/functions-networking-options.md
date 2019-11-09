---
title: Azure Functions hálózati beállítások
description: A Azure Functionsben elérhető összes hálózati beállítás áttekintése.
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 4e55932d47389e09b135d571d0e000b9795e6edc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884964"
---
# <a name="azure-functions-networking-options"></a>Azure Functions hálózati beállítások

Ez a cikk a Azure Functions üzemeltetési lehetőségei között elérhető hálózati szolgáltatásokat ismerteti. A következő hálózatkezelési lehetőségek lehetővé teszi az erőforrások elérését internetről átirányítható címek használata nélkül, vagy a Function app-hozzáférés korlátozására.

Az üzemeltetési modellek különböző szintű hálózati elkülönítéssel rendelkeznek. A megfelelő kiválasztásával a hálózat elkülönítési követelményeinek megfelelőnek kell lennie.

A Function apps több módon is üzemeltethető:

* Több-bérlős infrastruktúrán futó, különböző szintű virtuális hálózati kapcsolattal és méretezési lehetőségekkel rendelkező tervezési lehetőségek állnak rendelkezésre:
    * A [fogyasztási terv](functions-scale.md#consumption-plan), amely dinamikusan méretezi a terhelést, és minimális hálózati elkülönítési lehetőségeket kínál.
    * A [Prémium csomag](functions-scale.md#premium-plan), amely szintén dinamikusan méretezi a hálózatot, miközben átfogóbb hálózati elkülönítést kínál.
    * Az Azure [app Service terv](functions-scale.md#app-service-plan), amely rögzített skálán működik, és a prémium csomaghoz hasonló hálózati elkülönítést biztosít.
* A függvényeket futtathatja egy [app Service Environmentban](../app-service/environment/intro.md). Ez a metódus üzembe helyezi a függvényt a virtuális hálózatban, és teljes hálózati vezérlést és elkülönítést biztosít.

## <a name="matrix-of-networking-features"></a>Hálózati szolgáltatások mátrixa

|                |[Felhasználási terv](functions-scale.md#consumption-plan)|[Prémium csomag](functions-scale.md#premium-plan)|[App Service-csomag](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Bejövő IP-korlátozások & Private site Access](#inbound-ip-restrictions)|✅igen|✅igen|✅igen|✅igen|
|[Virtuális hálózat integrációja](#virtual-network-integration)|❌nem|✅igen (regionális)|✅igen (regionális és átjáró)|✅igen|
|[Virtuális hálózati eseményindítók (nem HTTP)](#virtual-network-triggers-non-http)|❌nem| ❌nem|✅igen|✅igen|
|[Hibrid kapcsolatok](#hybrid-connections)|❌nem|✅igen|✅igen|✅igen|
|[Kimenő IP-korlátozások](#outbound-ip-restrictions)|❌nem| ❌nem|❌nem|✅igen|

## <a name="inbound-ip-restrictions"></a>Bejövő IP-korlátozások

Az IP-korlátozások segítségével megadhatja az alkalmazáshoz hozzáférést engedélyező vagy megtagadott IP-címek prioritással rendezett listáját. A lista IPv4-és IPv6-címeket is tartalmazhat. Ha egy vagy több bejegyzés van, akkor a lista végén egy implicit "elutasítás all" érték szerepel. Az IP-korlátozások az összes funkció-üzemeltetési lehetőséggel működnek.

> [!NOTE]
> Ha hálózati korlátozásokkal rendelkezik, a portál szerkesztőt csak a virtuális hálózatán belül használhatja, vagy ha az Ön által használt gép IP-címét a biztonságos címzettek listáján lévő Azure Portal eléréséhez használja. Azonban továbbra is hozzáférhet bármely szolgáltatáshoz a **platform szolgáltatások** lapján bármely gépről.

További információ: [Azure app Service statikus hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Hozzáférés személyes oldalakhoz

A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak magánhálózat, például egy Azure virtuális hálózat számára legyen elérhető.

* A privát helyhez való hozzáférés a szolgáltatási végpontok konfigurálásakor a [prémium](./functions-premium-plan.md), a [fogyasztási](functions-scale.md#consumption-plan)és a [app Servicei](functions-scale.md#app-service-plan) csomagban érhető el.
    * A szolgáltatási végpontok a **platform funkciói** > **hálózatkezelés** > a **hozzáférési korlátozások konfigurálása** > a **szabály hozzáadása**lehetőséggel konfigurálhatók. A virtuális hálózatok mostantól szabálytípusként is kiválaszthatók.
    * További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Ne feledje, hogy a szolgáltatási végpontok esetében a függvény továbbra is teljes kimenő hozzáférést biztosít az internethez, még a konfigurált Virtual Network Integration is.
* A privát hely elérését egy belső terheléselosztó (ILB) használatára konfigurált App Service Environmenton belül is elérhető. További információ: [belső terheléselosztó létrehozása és használata app Service Environmentsal](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

A virtuális hálózat integrációja lehetővé teszi, hogy a Function alkalmazás hozzáférjen a virtuális hálózaton belüli erőforrásokhoz. Ez a funkció a prémium csomagban és a App Service csomagban is elérhető. Ha az alkalmazás egy App Service Environmentban van, akkor már egy virtuális hálózatban van, és nem igényel virtuális hálózati integrációt az erőforrások eléréséhez ugyanabban a virtuális hálózaton.

A Virtual Network Integration használatával lehetővé teheti az alkalmazásokból való hozzáférést a virtuális hálózatban futó adatbázisokhoz és webszolgáltatásokhoz. A Virtual Network Integration szolgáltatással nem kell nyilvános végpontot közzétennie az alkalmazások számára a virtuális GÉPEN. Ehelyett használhat privát, nem internetes irányítható címeket.

A virtuális hálózatok integrálásának két formája létezik:

+ **Regionális Virtual Network Integration (előzetes verzió)** : lehetővé teszi, hogy az azonos régióban lévő virtuális hálózatokkal is integrálható legyen. Az ilyen típusú integrációhoz ugyanahhoz a régióhoz tartozó virtuális hálózat alhálózata szükséges. Ez a funkció még mindig előzetes verzióban érhető el, de a Windows rendszeren futó Function apps esetében is támogatott, a következő probléma/megoldás tábla után leírt kikötésekkel.
+ **Átjáró szükséges virtuális hálózat integrációja**: lehetővé teszi a távoli régiókban lévő virtuális hálózatok és a klasszikus virtuális hálózatok integrálását. Az ilyen típusú integrációhoz szükség van egy virtuális hálózati átjáró üzembe helyezésére a VNet. Ez egy pont – hely típusú VPN-alapú szolgáltatás, amely csak Windows rendszeren futó Function apps esetén támogatott.

Egy alkalmazás egyszerre csak egy típusú virtuális hálózati integrációs funkciót használhat. Bár mindkettő számos esetben hasznos lehet, az alábbi táblázat az egyes használatban lévőket mutatja be:

| Probléma  | Megoldás |
|----------|----------|
| Egy adott régióban szeretné elérni az RFC 1918-es címeket (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). | Regionális virtuális hálózat integrációja |
| A klasszikus virtuális hálózatban vagy egy másik régióban lévő virtuális hálózaton szeretné elérni az erőforrásokat | Átjáró szükséges virtuális hálózati integráció |
| Az RFC 1918-végpontokat szeretné elérni az Azure ExpressRoute | Regionális virtuális hálózat integrációja |
| Erőforrásokat szeretne elérni a szolgáltatási végpontok között | Regionális virtuális hálózat integrációja |

Egyik funkció sem teszi lehetővé a nem RFC 1918-címek elérését a ExpressRoute között. Ehhez jelenleg egy App Service Environment kell használnia.

A regionális virtuális hálózati integráció használata nem kapcsolja össze a virtuális hálózatot a helyszíni végpontokkal, vagy konfigurálja a szolgáltatási végpontokat. Ez egy különálló hálózati konfiguráció. A regionális virtuális hálózatok integrációja egyszerűen lehetővé teszi, hogy az alkalmazás hívásokat hajtson végre a kapcsolati típusok között.

A használt verziótól függetlenül a Virtual Network Integration lehetővé teszi, hogy az alkalmazás hozzáférjen a virtuális hálózatban található erőforrásokhoz, de a virtuális hálózatról nem biztosít privát helyet a Function alkalmazáshoz. A privát webhelyek hozzáférése azt jelenti, hogy az alkalmazás csak olyan magánhálózathoz érhető el, mint egy Azure Virtual Network. a virtuális hálózat integrációja csak az alkalmazásból a virtuális hálózatra irányuló kimenő hívásokat teszi.

A Virtual Network integrációs funkciója:

* Standard, prémium vagy PremiumV2 App Service csomagra van szükség
* A TCP és az UDP támogatása
* Együttműködik App Service alkalmazásokkal és a Function-alkalmazásokkal

Néhány dolog, amit a virtuális hálózat integrációja nem támogat, beleértve a következőket:

* Meghajtó csatlakoztatása
* Active Directory-integráció
* NetBIOS

A Azure Functions virtuális hálózati integrációja a megosztott infrastruktúrát App Service Web Apps használatával használja. Ha többet szeretne megtudni a virtuális hálózatok integrációjának két típusáról, tekintse meg a következőt:

* [Regionális virtuális hálózat integrációja](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Átjáró szükséges virtuális hálózati integráció](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

A Virtual Network Integration használatával kapcsolatos további tudnivalókért lásd: [Function App-alkalmazás integrálása Azure-beli virtuális hálózattal](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Kapcsolódás a szolgáltatás végpontjának biztonságos erőforrásaihoz

> [!NOTE]
> Egyelőre akár 12 órát is igénybe vehet, amíg az új szolgáltatási végpontok elérhetővé válnak a Function alkalmazás számára, miután konfigurálta a hozzáférési korlátozásokat az alárendelt erőforráson. Ebben az időszakban az erőforrás teljes mértékben elérhetetlenné válik az alkalmazás számára.

A magasabb szintű biztonság érdekében a szolgáltatás-végpontok segítségével számos Azure-szolgáltatást korlátozhat egy virtuális hálózatra. Ezután integrálnia kell a Function alkalmazást az adott virtuális hálózattal az erőforrás eléréséhez. Ezt a konfigurációt minden olyan csomag támogatja, amely támogatja a virtuális hálózatok integrálását.

[További információ a Virtual Network szolgáltatásbeli végpontokról.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>A Storage-fiók korlátozása egy virtuális hálózatra

Egy Function-alkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, amely támogatja a blobot, a várólistát és a Table Storage-t. Jelenleg nem használhat virtuális hálózati korlátozásokat ezen a fiókon. Ha egy virtuális hálózati szolgáltatás végpontját konfigurálja a Function alkalmazáshoz használt Storage-fiókon, akkor az alkalmazás megszakítja az alkalmazást.

[További információ a Storage-fiókra vonatkozó követelményekről.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Key Vault hivatkozások használata 

A Key Vault referenciák lehetővé teszik, hogy programkód módosítása nélkül használja a Azure Functions alkalmazásban található Azure Key Vault titkokat. A Azure Key Vault egy olyan szolgáltatás, amely központosított titkok felügyeletét teszi lehetővé a hozzáférési házirendek és a naplózási előzmények teljes körű szabályozásával.

A [Key Vault-hivatkozások](../app-service/app-service-key-vault-references.md) jelenleg nem működnek, ha a Key Vault a szolgáltatási végpontok biztosítják. Ha virtuális hálózati integráció használatával szeretne csatlakozni egy Key Vaulthoz, a Key vaultot meg kell hívnia az alkalmazás kódjában.

## <a name="virtual-network-triggers-non-http"></a>Virtuális hálózati eseményindítók (nem HTTP)

Ha a HTTP-n kívül más függvényeket is használni szeretne egy virtuális hálózaton belül, a Function alkalmazást egy App Service csomagban vagy egy App Service Environment kell futtatnia.

Tegyük fel például, hogy úgy szeretné konfigurálni a Azure Cosmos DBt, hogy csak egy virtuális hálózatról fogadja a forgalmat. A Function alkalmazást olyan app Service-csomagban kell telepíteni, amely virtuális hálózati integrációt biztosít a virtuális hálózattal az adott erőforrásból Azure Cosmos DB eseményindítók konfigurálásához. Az előzetes verzióban a virtuális hálózati integráció konfigurálása nem teszi lehetővé, hogy a Prémium csomag kikapcsolja az Azure Cosmos DB erőforrást.

Tekintse meg [ezt a listát az összes nem http-eseményindítóról](./functions-triggers-bindings.md#supported-bindings) , ha duplán szeretné megtekinteni a támogatottak listáját.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[Hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) az Azure Relay szolgáltatása, amellyel más hálózatokban is elérheti az alkalmazás erőforrásait. Hozzáférést biztosít az alkalmazástól az alkalmazások végpontja számára. Nem használhatja az alkalmazást az alkalmazás elérésére. A Hibrid kapcsolatok az összes, de a felhasználási tervben futó függvények számára elérhető.

A Azure Functionsban használt módon minden hibrid csatlakozás egyetlen TCP-gazdagéphez és porthoz kapcsolódik. Ez azt jelenti, hogy a hibrid kapcsolatok végpontja bármely operációs rendszeren és alkalmazáson is lehet, ha a TCP-figyelési porthoz fér hozzá. A Hibrid kapcsolatok funkció nem tudja, vagy érdekli az alkalmazás protokollja, vagy hogy mi az, amit elér. Csak hálózati hozzáférést biztosít.

További információt a [Hibrid kapcsolatok app Service dokumentációjában](../app-service/app-service-hybrid-connections.md)talál. Ugyanezek a konfigurációs lépések támogatják a Azure Functions.

## <a name="outbound-ip-restrictions"></a>Kimenő IP-korlátozások

A kimenő IP-korlátozások csak egy App Service Environment központilag telepített függvények esetén érhetők el. Konfigurálhatja a kimenő korlátozásokat arra a virtuális hálózatra, amelyen a App Service Environment telepítve van.

Ha egy prémium szintű csomagban vagy egy virtuális hálózattal rendelkező App Service tervben integrál egy függvényt, az alkalmazás továbbra is kezdeményezheti a kimenő hívásokat az interneten.

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelésről és a Azure Functions:

* [Kövesse a virtuális hálózatok integrálásának első lépéseit ismertető oktatóanyagot](./functions-create-vnet.md)
* [A functions hálózatkezeléssel kapcsolatos gyakori kérdések](./functions-networking-faq.md)
* [További információ a App Service/functions Virtual Network Integration szolgáltatásáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózatkezelési funkciók és vezérlés App Service környezetekkel](../app-service/environment/intro.md)
* [Helyi erőforrásokhoz való kapcsolódás tűzfal módosítása nélkül Hibrid kapcsolatok használatával](../app-service/app-service-hybrid-connections.md)
