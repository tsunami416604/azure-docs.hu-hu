---
title: Érvényes hálózati beállításairól az Azure Functions
description: Az Azure Functions szolgáltatásban elérhető összes hálózati lehetőségeinek áttekintése
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698201"
---
# <a name="azure-functions-networking-options"></a>Érvényes hálózati beállításairól az Azure Functions

Ez a cikk ismerteti az elérhető hálózati szolgáltatások az Azure Functions az üzemeltetési lehetőségek között. Minden, a következő hálózati beállításokat adja meg a bizonyos erőforrások eléréséhez az internetes irányítható címek használata nélkül, vagy internet-hozzáférés korlátozása egy függvényalkalmazáshoz lehetősége. 

Az üzemeltetési modellek rendelkezik a különböző szintű hálózati elkülönítés érhető el. Megfelelő választás segítséget nyújt a hálózati elkülönítés követelményeinek.

Függvényalkalmazások a többféle módon is üzemeltethet:

* Nincs több különböző szintű virtuális hálózati kapcsolat és skálázási lehetőségek egy több-bérlős infrastruktúrában futó tervet beállítás:
    * A [Használatalapú csomag](functions-scale.md#consumption-plan), amely dinamikusan méretezhető betölteni a válaszban, és minimális hálózati elkülönítési lehetőséget kínál.
    * A [prémium szintű csomag](functions-scale.md#premium-plan-public-preview), amely is méretezhető dinamikusan, miközben átfogóbb hálózatelkülönítés kínál.
    * Az Azure [App Service-csomag](functions-scale.md#app-service-plan), amely rögzített méretű, működik, és a prémium szintű csomaggal hasonló hálózati elkülönítés kínálja.
* Függvények futtathat egy [App Service Environment-környezet](../app-service/environment/intro.md). Ez a módszer üzembe helyezi a függvény a virtuális hálózatban, és teljes hálózati vezérlő és elszigeteltséget biztosít.

## <a name="matrix-of-networking-features"></a>A hálózati funkciók mátrix

|                |[Használatalapú csomag](functions-scale.md#consumption-plan)|⚠ [Prémium szintű csomag](functions-scale.md#premium-plan-public-preview)|[App Service-csomag](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Bejövő IP-korlátozások](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Virtuális hálózat integrációja](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[(Az Azure ExpressRoute- és végpontok) virtuális hálózati integráció előzetes verzió](#preview-version-of-virtual-network-integration)|❌No|⚠igen|⚠igen|✅Yes|
|[Hibrid kapcsolatok](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[privát hozzáférést](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Az előzetes verziójú funkció nem éles környezetben való használathoz tartozik.

## <a name="inbound-ip-restrictions"></a>Bejövő IP-korlátozások

Az IP-korlátozások használatával engedélyezett/elutasított érhető el az alkalmazás IP-címek prioritású rendezett listájának meghatározását. Az IPv4 és IPv6 típusú címek lehetnek. Ha egy vagy több bejegyzés, a lista végén egy implicit "összes elutasítása" létezik. Az IP-korlátozások minden függvény üzemeltetési lehetőségek működnek.

> [!NOTE]
> Az Azure portal-szerkesztő segítségével, a portálon érhetik el közvetlenül a futó függvényalkalmazást kell lennie. Az eszköz, amely elérhető a portál használata is, rendelkeznie kell az IP-engedélyezési listán. A korlátozásait a hálózati helyen, továbbra is hozzáférhet a szolgáltatásokat a a **platformfunkciók** fülre.

További tudnivalókért lásd: [Azure App Service statikus hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md).

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

Virtuális hálózat integrációja lehetővé teszi, hogy a virtuális hálózatban lévő erőforrások eléréséhez a függvényalkalmazást. Ez a funkció a prémium csomag és az App Service-csomagban érhető el. Ha az alkalmazás az App Service-környezet, a virtuális hálózatban már van, és nem igénylik a virtuális hálózati integráció az azonos virtuális hálózatban lévő erőforrások eléréséhez.

Virtuális hálózat integrációja a függvény alkalmazás hozzáférést biztosít a virtuális hálózatban lévő erőforrásokra irányuló, de nem biztosíthat [privát hozzáférést](#private-site-access) a virtuális hálózatról a függvényalkalmazáshoz.

Virtuális hálózati integráció segítségével adatbázisokat és a virtuális hálózaton futó webszolgáltatások alkalmazások elérésének lehetővé tétele. A virtuális hálózati integráció nem kell egy nyilvános végpontot a virtuális Gépen alkalmazásokat teszi közzé. Ehelyett használhatja a saját, az internet irányítható címeket.

Az általánosan elérhető verziót, a virtuális hálózati integráció függvényalkalmazások kapcsolódni egy virtuális hálózat VPN-átjáró támaszkodik. App Service-csomagban üzemeltetett funkciók érhető el. Ez a szolgáltatás konfigurálásával kapcsolatban lásd: [az alkalmazás integrálása az Azure-beli virtuális hálózathoz](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Virtuális hálózati integráció előzetes verziója

Egy új verzióját a virtuális hálózati integráció funkció előzetes verzióban van. Pont – hely VPN nem függ. Között ExpressRoute támogatja az erőforrások elérésére, vagy a Szolgáltatásvégpontok. Érhető el a prémium csomag és az App Service-csomagok PremiumV2 méretezve.

Az alábbiakban néhány ebben a verzióban jellemzői:

* Nem kell egy átjárót a használatára.
* Az erőforrásokat elérheti az ExpressRoute-kapcsolatokon bármilyen további konfiguráció nélkül. Ehhez csak integrációra van szükség az ExpressRoute-hoz csatlakoztatott virtuális hálózattal.
* Szolgáltatási végpont védett erőforrások függvények futtatását is használhatók. Ehhez engedélyezze a szolgáltatásvégpontokat a virtuális hálózati integráció használt alhálózat.
* A service-végpont védett erőforrások eseményindítók nem lehet konfigurálni. 
* A függvényalkalmazás, mind a virtuális hálózat ugyanabban a régióban kell lennie.
* Az új szolgáltatáshoz egy nem használt alhálózatot a virtuális hálózaton üzembe helyezett Azure Resource Manageren keresztül.
* Éles számítási feladatok nem támogatottak, amíg a funkció előzetes verzióban érhető el.
* Útválasztási táblázatokat és globális társviszony-létesítés még nem érhető el a szolgáltatással.
* Minden lehetséges példánya egy függvényalkalmazást egy cím használható. Alhálózat méretét a hozzárendelés után nem módosítható, mert könnyen által támogatott maximális skálázási méret alhálózatot használjon. Ha például egy prémium szintű csomag, amely a 80-példány támogatása érdekében javasoljuk, hogy egy `/25` alhálózatot, amelyet a gazdagép 126 címet biztosít.

A virtuális hálózati integráció előzetes verziójának használatával kapcsolatos további tudnivalókért lásd: [függvényalkalmazás integrálása az Azure-beli virtuális hálózathoz](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[Hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) funkció az Azure Relay, amelyek segítségével más hálózatokhoz az alkalmazás-erőforrások eléréséhez. Egy alkalmazás végpontnak az alkalmazásból való elérést biztosít. Az alkalmazás eléréséhez, nem tudja használni. Hibrid kapcsolatok futó függvények rendelkezésére áll egy [App Service-csomag](functions-scale.md#app-service-plan) és a egy [App Service Environment-környezet](../app-service/environment/intro.md).

Az Azure Functions használ, egyetlen TCP-állomás és port kombinációja utal. minden hibrid kapcsolat. Ez azt jelenti, hogy a hibrid kapcsolat végpontja lehet bármely operációs rendszer és minden alkalmazás, mindaddig, amíg egy TCP-figyelési port éri el. Hibrid kapcsolatok szolgáltatását nem ismert, és gondoskodik a protokoll van, vagy milyen éri el. Egyszerűen hálózati hozzáférést biztosít.

További tudnivalókért lásd: a [App Service – dokumentáció a hibrid kapcsolatokhoz](../app-service/app-service-hybrid-connections.md), amely Functions támogatja az App Service-csomag.

## <a name="private-site-access"></a>Hozzáférés személyes oldalakhoz

Privát hozzáférést utal, hogy az alkalmazás csak elérhetőnek magánhálózaton például az Azure virtuális hálózatban. Privát hozzáférést csak az App Service-környezet belső terheléselosztóval (ILB) konfigurált érhető el. További információkért lásd: [létrehozása és használata az App Service-környezet belső terheléselosztó](../app-service/environment/create-ilb-ase.md).

Számos módon más üzemeltetési lehetőségek a virtuális hálózati erőforrások eléréséhez. Azonban az App Service-környezet az egyetlen módja, hogy a virtuális hálózaton keresztül történjen-függvény eseményindítók.

## <a name="next-steps"></a>További lépések
További információ a hálózati és az Azure Functionsről: 

* [A virtuális hálózati integráció használatának első lépéseivel kapcsolatos oktatóanyag](./functions-create-vnet.md)
* [Olvassa el a függvények hálózat – gyakori kérdések](./functions-networking-faq.md)
* [További tudnivalók a virtuális hálózati integráció az App Service a Functions](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)
* [További hálózati funkciókat és hozzáférés-vezérlés az App Service Environment-környezetek engedélyezése](../app-service/environment/intro.md)
* [Hibrid kapcsolatok segítségével csatlakozzon az egyes helyszíni erőforrásait tűzfal módosítás nélkül](../app-service/app-service-hybrid-connections.md)
