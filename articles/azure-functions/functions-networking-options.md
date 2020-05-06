---
title: Az Azure Functions hálózatkezelési lehetőségei
description: A Azure Functionsben elérhető összes hálózati beállítás áttekintése.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419543"
---
# <a name="azure-functions-networking-options"></a>Az Azure Functions hálózatkezelési lehetőségei

Ez a cikk a Azure Functions üzemeltetési lehetőségei között elérhető hálózati szolgáltatásokat ismerteti. A következő hálózatkezelési lehetőségek lehetővé teszi az erőforrások elérését internetről átirányítható címek használata nélkül, vagy a Function app-hozzáférés korlátozására.

Az üzemeltetési modellek különböző szintű hálózati elkülönítéssel rendelkeznek. A megfelelő beállítás kiválasztásával teljesítheti a hálózat elkülönítési követelményeit.

A Function apps több módon is üzemeltethető:

* Több-bérlős infrastruktúrán futó tervezési lehetőségek közül választhat, a virtuális hálózati kapcsolat és a méretezési lehetőségek különböző szintjeivel:
    * A [fogyasztási terv](functions-scale.md#consumption-plan) dinamikusan méretezi a terhelést, és minimális hálózati elkülönítési lehetőségeket kínál.
    * A [Prémium csomag](functions-scale.md#premium-plan) emellett dinamikusan méretezi a hálózatot, és átfogóbb hálózati elkülönítést biztosít.
    * Az Azure [app Service-csomag](functions-scale.md#app-service-plan) rögzített skálán működik, és a prémium csomaghoz hasonló hálózati elkülönítést biztosít.
* A függvényeket futtathatja egy [app Service Environmentban](../app-service/environment/intro.md). Ez a metódus üzembe helyezi a függvényt a virtuális hálózatban, és teljes hálózati vezérlést és elkülönítést biztosít.

## <a name="matrix-of-networking-features"></a>Hálózati szolgáltatások mátrixa

|                |[Felhasználási terv](functions-scale.md#consumption-plan)|[Prémium szintű csomag](functions-scale.md#premium-plan)|[App Service terv](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](#inbound-ip-restrictions)|✅igen|✅igen|✅igen|✅igen|
|[Virtuális hálózat integrációja](#virtual-network-integration)|❌No|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅igen|
|[Virtuális hálózati eseményindítók (nem HTTP)](#virtual-network-triggers-non-http)|❌No| ✅igen |✅igen|✅igen|
|[Hibrid kapcsolatok](#hybrid-connections) (csak Windows)|❌No|✅igen|✅igen|✅igen|
|[Kimenő IP-korlátozások](#outbound-ip-restrictions)|❌No| ✅igen|✅igen|✅igen|

## <a name="inbound-ip-restrictions"></a>Bejövő IP-korlátozások

Az IP-korlátozások segítségével megadhatja az alkalmazáshoz hozzáférést engedélyező vagy megtagadott IP-címek prioritással rendezett listáját. A lista IPv4-és IPv6-címeket is tartalmazhat. Ha egy vagy több bejegyzés van, akkor a lista végén egy implicit "elutasítás all" érték szerepel. Az IP-korlátozások az összes funkció-üzemeltetési lehetőséggel működnek.

> [!NOTE]
> Ha hálózati korlátozásokkal rendelkezik, a portál szerkesztőt csak a virtuális hálózatán belül használhatja, vagy ha az Ön által használt gép IP-címét a biztonságos címzettek listáján lévő Azure Portal eléréséhez használja. Azonban továbbra is hozzáférhet bármely szolgáltatáshoz a **platform szolgáltatások** lapján bármely gépről.

További információ: [Azure app Service statikus hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Hozzáférés személyes oldalakhoz

A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak magánhálózat, például egy Azure virtuális hálózat számára legyen elérhető.

* A privát helyhez való hozzáférés a szolgáltatási végpontok konfigurálásakor a [prémium](./functions-premium-plan.md), a [fogyasztási](functions-scale.md#consumption-plan)és a [app Servicei](functions-scale.md#app-service-plan) csomagban érhető el.
    * A szolgáltatási végpontok a **platform szolgáltatásai** > **hálózatkezelés** > **konfigurálása hozzáférési korlátozások** > megadása**szabály hozzáadása**alkalmazás alapján konfigurálhatók. A virtuális hálózatok mostantól szabálytípusként is kiválaszthatók.
    * További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Ne feledje, hogy a szolgáltatási végpontok esetében a függvény továbbra is teljes kimenő hozzáférést biztosít az internethez, még a konfigurált Virtual Network Integration is.
* A privát hely elérését egy belső terheléselosztó (ILB) használatára konfigurált App Service Environmenton belül is elérhető. További információ: [belső terheléselosztó létrehozása és használata app Service Environmentsal](../app-service/environment/create-ilb-ase.md).

A privát helyek hozzáférésének beállításával kapcsolatos további információkért lásd: [Azure functions Private site Access létrehozása](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

A virtuális hálózat integrációja lehetővé teszi, hogy a Function alkalmazás hozzáférjen a virtuális hálózaton belüli erőforrásokhoz.
A Azure Functions kétféle virtuális hálózati integrációt támogat:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

A Azure Functions virtuális hálózati integrációja a megosztott infrastruktúrát App Service Web Apps használatával használja. Ha többet szeretne megtudni a virtuális hálózatok integrációjának két típusáról, tekintse meg a következőt:

* [Regionális virtuális hálózat integrációja](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Átjáró – szükséges virtuális hálózati integráció](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

A virtuális hálózati integráció beállításával kapcsolatos további információkért lásd: [Function App-alkalmazás integrálása Azure-beli virtuális hálózattal](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regionális virtuális hálózat integrációja

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Kapcsolódás a szolgáltatás végpontjának biztonságos erőforrásaihoz

A magasabb szintű biztonság érdekében a szolgáltatás-végpontok segítségével számos Azure-szolgáltatást korlátozhat egy virtuális hálózatra. Ezután integrálnia kell a Function alkalmazást az adott virtuális hálózattal az erőforrás eléréséhez. Ezt a konfigurációt minden olyan csomag támogatja, amely támogatja a virtuális hálózatok integrálását.

További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>A Storage-fiók korlátozása virtuális hálózatra

Egy Function-alkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, amely támogatja a blobot, a várólistát és a Table Storage-t. Jelenleg nem használhat virtuális hálózati korlátozásokat ezen a fiókon. Ha egy virtuális hálózati szolgáltatás végpontját konfigurálja a Function alkalmazáshoz használt Storage-fiókon, akkor a konfiguráció megszakítja az alkalmazást.

További információ: a [Storage-fiókra vonatkozó követelmények](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Key Vault referenciák használata

Azure Key Vault hivatkozásokat használhat a Azure Functions alkalmazásban található Azure Key Vault titkok használatára a kód módosítása nélkül. A Azure Key Vault egy olyan szolgáltatás, amely központosított titkok felügyeletét teszi lehetővé a hozzáférési házirendek és a naplózási előzmények teljes körű szabályozásával.

Jelenleg [Key Vault hivatkozások](../app-service/app-service-key-vault-references.md) nem fognak működni, ha a kulcstartó biztonságos a szolgáltatási végpontokkal. A Virtual Network Integration használatával a Key vaulthoz való csatlakozáshoz meg kell hívnia Key Vault az alkalmazás kódjában.

## <a name="virtual-network-triggers-non-http"></a>Virtuális hálózati eseményindítók (nem HTTP)

Jelenleg a nem HTTP-trigger függvények a következő két módszer egyikével használhatók a virtuális hálózaton belül:

+ Futtassa a Function alkalmazást egy prémium szintű csomagban, és engedélyezze a virtuális hálózatok triggerének támogatását.
+ A Function alkalmazást App Service csomagban vagy App Service Environment futtathatja.

### <a name="premium-plan-with-virtual-network-triggers"></a>Prémium csomag virtuális hálózati eseményindítókkal

A Prémium csomag futtatásakor a nem HTTP-trigger függvények a virtuális hálózaton belül futó szolgáltatásokhoz is csatlakoztathatók. Ehhez engedélyeznie kell a Virtual Network trigger támogatását a Function alkalmazás számára. A **virtuális hálózat triggerének támogatása** beállítás a [Azure Portal](https://portal.azure.com) a **Function app Settings**elem alatt található.

![Virtuális hálózat váltógomb](media/functions-networking-options/virtual-network-trigger-toggle.png)

A virtuális hálózati eseményindítókat a következő Azure CLI-parancs használatával is engedélyezheti:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

A virtuális hálózati eseményindítók a functions futtatókörnyezet 2. x vagy újabb verziójában támogatottak. A következő nem HTTP típusú triggerek támogatottak:

| Mellék | Minimális verzió |
|-----------|---------| 
|[Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 vagy újabb |
|[Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 vagy újabb|
|[Microsoft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 vagy újabb verzió|
|[Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 vagy újabb|
|[Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 vagy újabb|

> [!IMPORTANT]
> Ha engedélyezi a virtuális hálózat triggerének támogatását, csak az előző táblázatban látható trigger-típusok dinamikusan méretezhetők az alkalmazással. Továbbra is használhatja azokat az eseményindítókat, amelyek nem szerepelnek a táblázatban, de nem méretezhetők az előre bemelegítő példányok száma után. Az eseményindítók teljes listájáért lásd: [triggerek és kötések](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service csomag és App Service Environment virtuális hálózati eseményindítókkal

Ha a Function alkalmazás egy App Service vagy egy App Service Environment fut, akkor a nem HTTP-trigger függvényeket is használhatja. Ahhoz, hogy a függvények helyesen legyenek kiváltva, csatlakoznia kell egy olyan virtuális hálózathoz, amely hozzáfér az trigger-kapcsolatban definiált erőforráshoz.

Tegyük fel például, hogy úgy szeretné konfigurálni a Azure Cosmos DBt, hogy csak egy virtuális hálózatról fogadja a forgalmat. Ebben az esetben telepítenie kell a Function alkalmazást egy olyan App Service tervbe, amely virtuális hálózati integrációt biztosít a virtuális hálózattal. Az integráció lehetővé teszi, hogy az adott Azure Cosmos DB erőforrás aktiválja a függvényt.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[Hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) az Azure Relay szolgáltatása, amellyel más hálózatokban is elérheti az alkalmazás erőforrásait. Hozzáférést biztosít az alkalmazástól az alkalmazások végpontja számára. Nem használhatja az alkalmazást az alkalmazás elérésére. A Hibrid kapcsolatok a Windows rendszeren futó függvények számára is elérhető a használati tervben.

A Azure Functionsban használt módon minden hibrid csatlakozás egyetlen TCP-gazdagéphez és porthoz kapcsolódik. Ez azt jelenti, hogy a hibrid kapcsolatok végpontja bármely operációs rendszeren és alkalmazáson is lehet, ha a TCP-figyelési porthoz fér hozzá. A Hibrid kapcsolatok funkció nem tudja, vagy érdekli az alkalmazás protokollja, vagy hogy mi az, amit elér. Csak hálózati hozzáférést biztosít.

További információt a [Hibrid kapcsolatok app Service dokumentációjában](../app-service/app-service-hybrid-connections.md)talál. Ugyanezek a konfigurációs lépések támogatják a Azure Functions.

>[!IMPORTANT]
> Hibrid kapcsolatok csak Windows-csomagok esetén támogatott. A Linux nem támogatott.

## <a name="outbound-ip-restrictions"></a>Kimenő IP-korlátozások

A kimenő IP-korlátozások prémium csomagokban, App Service csomagban vagy App Service Environment érhetők el. Konfigurálhatja a kimenő korlátozásokat arra a virtuális hálózatra, amelyen a App Service Environment telepítve van.

Ha egy prémium szintű csomagban vagy egy virtuális hálózattal rendelkező App Service tervben integrál egy függvényt, az alkalmazás alapértelmezés szerint továbbra is elvégezheti a kimenő hívásokat az internetre. Az Alkalmazásbeállítás `WEBSITE_VNET_ROUTE_ALL=1`hozzáadásával kényszeríti az összes kimenő forgalom küldését a virtuális hálózatba, ahol a hálózati biztonsági csoportra vonatkozó szabályok a forgalom korlátozására használhatók.

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelésről és a Azure Functions:

* [Kövesse a virtuális hálózatok integrálásának első lépéseit ismertető oktatóanyagot](./functions-create-vnet.md)
* [A functions hálózatkezeléssel kapcsolatos gyakori kérdések](./functions-networking-faq.md)
* [További információ a App Service/functions Virtual Network Integration szolgáltatásáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózatkezelési funkciók és vezérlés App Service környezetekkel](../app-service/environment/intro.md)
* [Helyi erőforrásokhoz való kapcsolódás tűzfal módosítása nélkül Hibrid kapcsolatok használatával](../app-service/app-service-hybrid-connections.md)
