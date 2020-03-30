---
title: Az Azure Functions hálózatkezelési lehetőségei
description: Az Azure Functions ben elérhető összes hálózati beállítás áttekintése.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: d8c3357325eadefec7bb97faba5d600e9c6793a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276711"
---
# <a name="azure-functions-networking-options"></a>Az Azure Functions hálózatkezelési lehetőségei

Ez a cikk ismerteti a hálózati funkciók érhetők el az Azure Functions üzemeltetési beállításaiközött. Az alábbi hálózati beállítások mindegyike lehetővé teszi az erőforrások elérését az internetirányítható címek használata nélkül, vagy az internet-hozzáférés korlátozását egy függvényalkalmazáshoz.

A gazdamodellek különböző szintű hálózati elkülönítéssel rendelkeznek. A megfelelő kiválasztása segít a hálózati elkülönítési követelmények teljesítésében.

A függvényalkalmazásokat többféleképpen is üzemeltetheti:

* Több-bérlős infrastruktúrán futó csomagbeállítások, amelyek különböző szintű virtuális hálózati kapcsolattal és méretezési beállításokkal rendelkeznek:
    * A [Felhasználási terv](functions-scale.md#consumption-plan), amely dinamikusan méretezhető a terhelésre reagálva, és minimális hálózati elkülönítési lehetőségeket kínál.
    * A [prémium csomag](functions-scale.md#premium-plan), amely dinamikusan skálázódik, miközben átfogóbb hálózati elkülönítést kínál.
    * Az Azure [App Service-csomag](functions-scale.md#app-service-plan), amely rögzített méretekben működik, és hasonló hálózati elkülönítést kínál, mint a Prémium csomag.
* A függvények egy [App Service-környezetben](../app-service/environment/intro.md)futtathatók. Ez a módszer telepíti a funkciót a virtuális hálózatba, és teljes hálózati vezérlést és elkülönítést kínál.

## <a name="matrix-of-networking-features"></a>Hálózati funkciók mátrixa

|                |[Felhasználási terv](functions-scale.md#consumption-plan)|[Prémium szintű csomag](functions-scale.md#premium-plan)|[App Szolgáltatási csomag](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Bejövő IP-korlátozások & privát webhely-hozzáféréshez](#inbound-ip-restrictions)|✅Igen|✅Igen|✅Igen|✅Igen|
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
    * További információt a virtuális hálózati szolgáltatás végpontjai című [témakörben talál.](../virtual-network/virtual-network-service-endpoints-overview.md)
    * Ne feledje, hogy a szolgáltatás végpontok, a függvény továbbra is teljes kimenő hozzáférést biztosít az internethez, még a virtuális hálózati integráció konfigurálva.
* A privát helyhozzáférés egy belső terheléselosztóval (ILB) konfigurált App Service-környezetben is elérhető. További információt a [Belső terheléselosztó létrehozása és használata App Service-környezettel című témakörben talál.](../app-service/environment/create-ilb-ase.md)

A privát webhely-hozzáférés beállításáról az [Azure Functions privát webhely-hozzáférés létrehozása .Learn](functions-create-private-site-access.md)how to set up private site access, in see Establish Azure Functions private site access .

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

A virtuális hálózati integráció lehetővé teszi, hogy a függvényalkalmazás hozzáférjen a virtuális hálózaton belüli erőforrásokhoz. Az Azure Functions kétféle virtuális hálózati integrációt támogat:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Az Azure Functions virtuális hálózati integrációja megosztott infrastruktúrát használ az App Service-webalkalmazásokkal. A virtuális hálózatok integrációjának két típusáról az:

* [Regionális virtuális hálózati integráció](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Az átjáró szükséges virtuális hálózati integrációra](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

A virtuális hálózati integráció beállításáról a [Függvényalkalmazás integrálása Azure virtuális hálózattal (Integrálása)](functions-create-vnet.md)témakörben olvashat.

## <a name="regional-virtual-network-integration"></a>Regionális virtuális hálózati integráció

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>Csatlakozás szolgáltatásvégpontbiztonságos erőforrásaihoz

Magasabb szintű biztonság érdekében számos Azure-szolgáltatást korlátozhatja egy virtuális hálózatra a szolgáltatásvégpontok használatával. Ezután integrálnia kell a függvényalkalmazást a virtuális hálózattal az erőforrás eléréséhez. Ez a konfiguráció minden olyan tervben támogatott, amely támogatja a virtuális hálózati integrációt.

[További információ a virtuális hálózati szolgáltatás végpontjairól.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>A tárfiók korlátozása virtuális hálózatra

Amikor létrehoz egy függvényalkalmazást, létre kell hoznia, vagy egy általános célú Azure Storage-fiókot kell létrehoznia, amely támogatja a Blob, a Queue és a Table storage-t. Jelenleg nem használhat virtuális hálózati korlátozásokat ezen a fiókon. Ha egy virtuális hálózati szolgáltatás végpontját konfigurálja a függvényalkalmazáshoz használt tárfiókban, az megszakítja az alkalmazást.

[További információ a tárfiók követelményeiről.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>A Key Vault-hivatkozások használata 

A Key Vault-hivatkozások lehetővé teszik az Azure Key Vault titkos kulcsainak használatát az Azure Functions alkalmazásban anélkül, hogy kódmódosításokat kellene végrehajtania. Az Azure Key Vault egy olyan szolgáltatás, amely központosított titkos kulcsok kezelését biztosítja, teljes hozzáférés-házirendek és naplózási előzmények teljes körű vezérlésével.

Jelenleg [a Key Vault-hivatkozások](../app-service/app-service-key-vault-references.md) nem fognak működni, ha a Key Vault szolgáltatásvégpontokkal van biztosítva. Ha virtuális hálózati integrációval szeretne csatlakozni egy Key Vaulthoz, meg kell hívnia a key vaultot az alkalmazáskódban.

## <a name="virtual-network-triggers-non-http"></a>Virtuális hálózati eseményindítók (nem HTTP)

Jelenleg a nem HTTP-s eseményindító függvényeket a virtuális hálózaton belül kétféleképpen használhatja: 
+ Futtassa a függvényalkalmazást egy prémium csomagban, és engedélyezze a virtuális hálózat eseményindítói nak támogatását.
+ Futtassa a függvényalkalmazást egy App Service-csomagban vagy az App Service-környezetben.

### <a name="premium-plan-with-virtual-network-triggers"></a>Prémium csomag virtuális hálózati eseményindítókkal

Prémium csomag futtatásakor nem HTTP-trigger függvényeket csatlakoztathat a virtuális hálózaton futó szolgáltatásokhoz. Ehhez engedélyeznie kell a virtuális hálózati eseményindító konklisen-támogatását a függvényalkalmazáshoz. A **virtuális hálózat eseményindító támogatási** beállítása az Azure Portal [on](https://portal.azure.com) Function **alkalmazás beállításai**csoportban található.

![VNETToggle között](media/functions-networking-options/virtual-network-trigger-toggle.png)

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
> Virtuális hálózati eseményindító támogatás engedélyezésekor csak az eseményindító-típusok felett dinamikusan skálázódik az alkalmazással. Továbbra is használhatja a fent nem felsorolt eseményindítókat, de azok nem vannak az előre felmelegített példányszámon túlméretezve. Tekintse meg az [eseményindítók és kötések](./functions-triggers-bindings.md#supported-bindings) a teljes listát az eseményindítók.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service-csomag és App Service-környezet virtuális hálózati eseményindítókkal

Ha a függvényalkalmazás egy App Service-csomagban vagy egy App Service-környezetben fut, nem HTTP-trigger függvényeket használhat. Ahhoz, hogy a függvények megfelelően aktiválódjanak, csatlakoznia kell egy virtuális hálózathoz, amely hozzáfér az eseményindító-kapcsolatban meghatározott erőforráshoz. 

Tegyük fel például, hogy az Azure Cosmos DB-t úgy szeretné konfigurálni, hogy csak egy virtuális hálózatról fogadja a forgalmat. Ebben az esetben telepítenie kell a függvényalkalmazást egy App Service-csomagban, amely virtuális hálózati integrációt biztosít az adott virtuális hálózattal. Ez lehetővé teszi, hogy az Azure Cosmos DB-erőforrás egy függvényt indítson el. 

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[A hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) az Azure Relay egyik szolgáltatása, amely segítségével más hálózatok alkalmazáserőforrásaihoz férhet hozzá. Hozzáférést biztosít az alkalmazásból egy alkalmazás végpontjához. Nem használhatja az alkalmazás eléréséhez. A hibrid kapcsolatok a Felhasználási terv kivételével a Windows rendszeren futó függvények számára érhetők el.

Az Azure Functions ben használt, minden hibrid kapcsolat egyetlen TCP-állomás és port kombináció korrelál. Ez azt jelenti, hogy a hibrid kapcsolat végpontja bármely operációs rendszeren és bármely alkalmazásban elérhető lehet, amíg egy TCP-figyelő porthoz fér hozzá. A hibrid kapcsolatok szolgáltatás nem tudja, és nem érdekli, mi az alkalmazás protokoll, vagy mit ér el. Ez csak biztosít hálózati hozzáférést.

További információ: [Az App Service dokumentációja hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md). Ugyanezek a konfigurációs lépések támogatják az Azure Functions.

>[!IMPORTANT]
> A hibrid kapcsolatok csak Windows-csomagok esetén támogatottak. A Linux nem támogatott

## <a name="outbound-ip-restrictions"></a>Kimenő IP-korlátozások

A kimenő IP-korlátozások prémium csomagban, App Service-csomagban vagy App Service-környezetben érhetők el. Beállíthatja a kimenő korlátozásokat a virtuális hálózat, ahol az App Service-környezet telepítve van.

Ha egy funkcióalkalmazást integrál egy Prémium csomagba vagy egy App Service-csomagba egy virtuális hálózattal, az alkalmazás alapértelmezés szerint továbbra is kezdeményezhet kimenő hívásokat az internetre. Alkalmazásbeállítás `WEBSITE_VNET_ROUTE_ALL=1`hozzáadásával az összes kimenő forgalom elküldésre kerül a virtuális hálózatba, ahol a hálózati biztonsági csoport szabályai a forgalom korlátozására használhatók.

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
