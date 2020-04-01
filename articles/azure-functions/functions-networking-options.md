---
title: Az Azure Functions hálózatkezelési lehetőségei
description: Az Azure Functions ben elérhető összes hálózati beállítás áttekintése.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419543"
---
# <a name="azure-functions-networking-options"></a>Az Azure Functions hálózatkezelési lehetőségei

Ez a cikk ismerteti a hálózati funkciók érhetők el az Azure Functions üzemeltetési beállításaiközött. Az alábbi hálózati beállítások mindegyike lehetővé teszi az erőforrások elérését az internet-irányítható címek használata nélkül, vagy az internet-hozzáférés korlátozását egy függvényalkalmazáshoz.

A gazdamodellek különböző szintű hálózati elkülönítéssel rendelkeznek. A megfelelő beállítás segít a hálózati elkülönítési követelmények teljesítésében.

A függvényalkalmazásokat többféleképpen is üzemeltetheti:

* A több-bérlős infrastruktúrán futó, több-bérlős infrastruktúrán futó csomagbeállítások közül választhat, a virtuális hálózati kapcsolat és a méretezési lehetőségek különböző szintjeivel:
    * A [felhasználási terv](functions-scale.md#consumption-plan) dinamikusan méretezhető a terhelésre reagálva, és minimális hálózati elkülönítési lehetőségeket kínál.
    * A [Prémium csomag](functions-scale.md#premium-plan) dinamikusan is méretezhető, és átfogóbb hálózati elkülönítést kínál.
    * Az Azure [App Service-csomag](functions-scale.md#app-service-plan) rögzített méretekben működik, és a Prémium csomaghoz hasonló hálózati elkülönítést kínál.
* A függvények egy [App Service-környezetben](../app-service/environment/intro.md)futtathatók. Ez a módszer telepíti a funkciót a virtuális hálózatba, és teljes hálózati vezérlést és elkülönítést kínál.

## <a name="matrix-of-networking-features"></a>Hálózati funkciók mátrixa

|                |[Felhasználási terv](functions-scale.md#consumption-plan)|[Prémium szintű csomag](functions-scale.md#premium-plan)|[App Szolgáltatási csomag](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](#inbound-ip-restrictions)|✅Igen|✅Igen|✅Igen|✅Igen|
|[Virtuális hálózati integráció](#virtual-network-integration)|❌nem|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅Igen|
|[Virtuális hálózati eseményindítók (nem HTTP)](#virtual-network-triggers-non-http)|❌nem| ✅Igen |✅Igen|✅Igen|
|[Hibrid kapcsolatok](#hybrid-connections) (csak Windows)|❌nem|✅Igen|✅Igen|✅Igen|
|[Kimenő IP-korlátozások](#outbound-ip-restrictions)|❌nem| ✅Igen|✅Igen|✅Igen|

## <a name="inbound-ip-restrictions"></a>Bejövő IP-korlátozások

Az IP-korlátozások segítségével megadhatja az alkalmazáshoz való hozzáférést engedélyezett vagy megtagadott IP-címek prioritást rendelő listáját. A lista tartalmazhat IPv4- és IPv6-címeket. Ha egy vagy több bejegyzés van, a lista végén létezik egy implicit "mindent tagadó" szó. Az IP-korlátozások az összes funkcióüzemeltetési beállítással működnek.

> [!NOTE]
> A hálózati korlátozások életbe, használhatja a portál szerkesztő csak a virtuális hálózaton belül, vagy ha már fel az IP-címét a gép, amelyet az Azure Portal eléréséhez a biztonságos címzettek listán. A **Platform funkciói** lapon azonban bármely gépről hozzáférhet bármely funkcióhoz.

További információ: [Azure App Service statikus hozzáférési korlátozások.](../app-service/app-service-ip-restrictions.md)

## <a name="private-site-access"></a>Hozzáférés személyes oldalakhoz

A privát webhely-hozzáférés azt a számítógépet illeti, hogy az alkalmazást csak magánhálózatról, például egy Azure virtuális hálózatról teszi elérhetővé.

* A privát webhely-hozzáférés a [prémium](./functions-premium-plan.md), [a felhasználás](functions-scale.md#consumption-plan)és az [App Service-csomagokban](functions-scale.md#app-service-plan) érhető el, ha a szolgáltatásvégpontok konfigurálva vannak.
    * A szolgáltatásvégpontok alkalmazásonként konfigurálhatók a **Platform szolgáltatások** > **hálózati** > hozzáférési korlátozások > **hozzáadása**szabály**a**csoportban. A virtuális hálózatok mostantól szabálytípusként is kijelölhetők.
    * További információ: [Virtual network service endpoints](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Ne feledje, hogy a szolgáltatás végpontok, a függvény továbbra is teljes kimenő hozzáférést biztosít az internethez, még a virtuális hálózati integráció konfigurálva.
* A privát helyhozzáférés egy belső terheléselosztóval (ILB) konfigurált App Service-környezetben is elérhető. További információt a [Belső terheléselosztó létrehozása és használata App Service-környezettel című témakörben talál.](../app-service/environment/create-ilb-ase.md)

A privát webhely-hozzáférés beállításáról az [Azure Functions privát webhely-hozzáférés létrehozása .Learn](functions-create-private-site-access.md)how to set up private site access, in see Establish Azure Functions private site access .

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

A virtuális hálózati integráció lehetővé teszi, hogy a függvényalkalmazás hozzáférjen a virtuális hálózaton belüli erőforrásokhoz.
Az Azure Functions kétféle virtuális hálózati integrációt támogat:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Az Azure Functions virtuális hálózati integrációja megosztott infrastruktúrát használ az App Service-webalkalmazásokkal. A virtuális hálózatok integrációjának két típusáról az:

* [Regionális virtuális hálózati integráció](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Átjáró által igényelt virtuális hálózati integráció](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

A virtuális hálózati integráció beállításáról a [Függvényalkalmazás integrálása Azure virtuális hálózattal (Integrálása)](functions-create-vnet.md)témakörben olvashat.

## <a name="regional-virtual-network-integration"></a>Regionális virtuális hálózati integráció

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Csatlakozás szolgáltatásvégponthoz biztonságos erőforrásokhoz

Magasabb szintű biztonság érdekében számos Azure-szolgáltatást korlátozhatja egy virtuális hálózatra a szolgáltatásvégpontok használatával. Ezután integrálnia kell a függvényalkalmazást a virtuális hálózattal az erőforrás eléréséhez. Ez a konfiguráció minden olyan tervben támogatott, amely támogatja a virtuális hálózati integrációt.

További információ: [Virtuális hálózati szolgáltatás végpontjai](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>A tárfiók korlátozása virtuális hálózatra

Amikor létrehoz egy függvényalkalmazást, létre kell hoznia, vagy egy általános célú Azure Storage-fiókot kell létrehoznia, amely támogatja a Blob, a Queue és a Table storage-t. Jelenleg nem használhat virtuális hálózati korlátozásokat ezen a fiókon. Ha egy virtuális hálózati szolgáltatás végpontját konfigurálja a függvényalkalmazáshoz használt tárfiókban, az adott konfiguráció megszakítja az alkalmazást.

További információ: [Tárfiók követelményei.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="use-key-vault-references"></a>Kulcstartó-hivatkozások használata

Az Azure Key Vault-hivatkozások használatával az Azure Functions-alkalmazásban az Azure Key Vault titkos kulcsait használhatja anélkül, hogy kódmódosításokat kellene végrehajtania. Az Azure Key Vault egy olyan szolgáltatás, amely központosított titkos kulcsok kezelését biztosítja, teljes hozzáférés-házirendek és naplózási előzmények teljes körű vezérlésével.

Jelenleg [a Key Vault-hivatkozások](../app-service/app-service-key-vault-references.md) nem fog működni, ha a kulcstartó szolgáltatás végpontokkal védett. Ha virtuális hálózati integrációhasználatával szeretne csatlakozni egy kulcstartóhoz, meg kell hívnia a Key Vaultot az alkalmazáskódban.

## <a name="virtual-network-triggers-non-http"></a>Virtuális hálózati eseményindítók (nem HTTP)

Jelenleg a nem HTTP-s eseményindító függvényeket a virtuális hálózaton belül kétféleképpen használhatja:

+ Futtassa a függvényalkalmazást egy Prémium csomagban, és engedélyezze a virtuális hálózati eseményindítók támogatását.
+ Futtassa a függvényalkalmazást egy App Service-csomagban vagy az App Service-környezetben.

### <a name="premium-plan-with-virtual-network-triggers"></a>Prémium csomag virtuális hálózati eseményindítókkal

Prémium csomag futtatásakor nem HTTP-trigger függvényeket csatlakoztathat a virtuális hálózaton futó szolgáltatásokhoz. Ehhez engedélyeznie kell a virtuális hálózati eseményindító konklisen-támogatását a függvényalkalmazáshoz. A **virtuális hálózat eseményindító támogatási** beállítása az Azure [Portalon](https://portal.azure.com) található a **Függvényalkalmazás beállításai**csoportban.

![Virtuális hálózat váltása](media/functions-networking-options/virtual-network-trigger-toggle.png)

A virtuális hálózati eseményindítókat a következő Azure CLI-paranccsal is engedélyezheti:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

A virtuális hálózati eseményindítókat a Functions futásidejű 2.x-es és újabb verziója támogatja. A következő nem HTTP-eseményindító-típusok támogatottak.

| Mellék | Minimális verzió |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 vagy újabb |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 vagy újabb|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 vagy újabb|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 vagy újabb|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 vagy újabb|

> [!IMPORTANT]
> Ha engedélyezi a virtuális hálózati eseményindítók támogatását, csak az előző táblázatban látható eseményindító-típusok skálázhatók dinamikusan az alkalmazással. Továbbra is használhatja az eseményindítók, amelyek nem szerepelnek a táblázatban, de nem vannak méretezve túl az előre felmelegített példányszáma. Az eseményindítók teljes listáját az [Eseményindítók és kötések](./functions-triggers-bindings.md#supported-bindings)című témakörben található.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service-csomag és App Service-környezet virtuális hálózati eseményindítókkal

Ha a függvényalkalmazás egy App Service-csomagban vagy egy App Service-környezetben fut, nem HTTP-trigger függvényeket használhat. Ahhoz, hogy a függvények megfelelően aktiválódjanak, csatlakoznia kell egy virtuális hálózathoz, amely hozzáfér az eseményindító-kapcsolatban meghatározott erőforráshoz.

Tegyük fel például, hogy az Azure Cosmos DB-t úgy szeretné konfigurálni, hogy csak egy virtuális hálózatról fogadja a forgalmat. Ebben az esetben telepítenie kell a függvényalkalmazást egy App Service-csomagban, amely virtuális hálózati integrációt biztosít az adott virtuális hálózattal. Integráció lehetővé teszi, hogy egy függvényt az Azure Cosmos DB erőforrás aktivál.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[A hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) az Azure Relay egyik szolgáltatása, amely segítségével más hálózatok alkalmazáserőforrásaihoz férhet hozzá. Hozzáférést biztosít az alkalmazásból egy alkalmazás végpontjához. Nem használhatja az alkalmazás eléréséhez. A hibrid kapcsolatok olyan funkciók számára érhetők el, amelyek a Felhasználási terv kivételével a Windows rendszeren futnak.

Az Azure Functions ben használt, minden hibrid kapcsolat egyetlen TCP-állomás és port kombináció korrelál. Ez azt jelenti, hogy a hibrid kapcsolat végpontja bármely operációs rendszeren és bármely alkalmazásban elérhető lehet, amíg egy TCP-figyelő porthoz fér hozzá. A hibrid kapcsolatok szolgáltatás nem tudja, és nem érdekli, mi az alkalmazás protokoll, vagy mit ér el. Ez csak biztosít hálózati hozzáférést.

További információ: [Az App Service dokumentációja hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md). Ugyanezek a konfigurációs lépések támogatják az Azure Functions.

>[!IMPORTANT]
> A hibrid kapcsolatok csak Windows-csomagok esetén támogatottak. A Linux nem támogatott.

## <a name="outbound-ip-restrictions"></a>Kimenő IP-korlátozások

A kimenő IP-korlátozások prémium csomagban, App Service-csomagban vagy App Service-környezetben érhetők el. Beállíthatja a kimenő korlátozásokat a virtuális hálózat, ahol az App Service-környezet telepítve van.

Ha egy funkcióalkalmazást integrál egy Prémium csomagba vagy egy App Service-csomagba egy virtuális hálózattal, az alkalmazás alapértelmezés szerint továbbra is kezdeményezhet kimenő hívásokat az internetre. Az alkalmazásbeállítás `WEBSITE_VNET_ROUTE_ALL=1`hozzáadásával az összes kimenő forgalom elküldésre kerül a virtuális hálózatba, ahol a hálózati biztonsági csoport szabályai a forgalom korlátozására használhatók.

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>További lépések

További információ a hálózatépítésről és az Azure Functionsről:

* [Kövesse a virtuális hálózati integráció első lépéseiről szóló oktatóanyagot](./functions-create-vnet.md)
* [A Functions hálózatkezelés – gyakori kérdések olvasása](./functions-networking-faq.md)
* [További információ az App Service/Functions virtuális hálózati integrációjáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózati funkciók és vezérlés engedélyezése az App Service-környezetekkel](../app-service/environment/intro.md)
* [Csatlakozás különálló helyszíni erőforrásokhoz tűzfal-módosítások nélkül a hibrid kapcsolatok használatával](../app-service/app-service-hybrid-connections.md)
