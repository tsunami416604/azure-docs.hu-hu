---
title: IP-címek az Azure Functionsben
description: Megtudhatja, hogyan keresheti meg a függvényalkalmazások bejövő és kimenő IP-címeit, és mi okozza azok módosítását.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a1c4174b8f1f2349cbd35c32cbee468ee5b4cd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276490"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-címek az Azure Functionsben

Ez a cikk a függvényalkalmazások IP-címével kapcsolatos alábbi témaköröket ismerteti:

* A függvényalkalmazás által jelenleg használt IP-címek megkeresése.
* Mi okozza a függvényalkalmazás IP-címének módosítását?
* A függvényalkalmazások elérésére képes IP-címek korlátozása.
* Hogyan juthat be egy függvényalkalmazás dedikált IP-címeihez.

Az IP-címek a függvényalkalmazásokhoz vannak társítva, nem az egyes függvényekhez. A bejövő HTTP-kérelmek nem használhatják a bejövő IP-címet az egyes függvények hívására; az alapértelmezett tartománynevet (functionappname.azurewebsites.net) vagy egyéni tartománynevet kell használniuk.

## <a name="function-app-inbound-ip-address"></a>Függvényalkalmazás bejövő IP-címe

Minden függvényalkalmazás egyetlen bejövő IP-címmel rendelkezik. Az IP-cím megkeresése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg a függvényalkalmazást.
3. Válassza a **Platformfunkciók** lehetőséget.
4. Válassza a **Tulajdonságok**lehetőséget, és a bejövő IP-cím a **Virtuális IP-cím**csoportban jelenik meg.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Függvényalkalmazás kimenő IP-címei

Minden függvényalkalmazás rendelkezik egy készlet a rendelkezésre álló kimenő IP-címek. Bármely függvényből, például egy háttéradatbázisból származó kimenő kapcsolat a rendelkezésre álló kimenő IP-címek egyikét használja forrás IP-címként. Nem lehet előre tudni, hogy egy adott kapcsolat melyik IP-címet fogja használni. Emiatt a háttérszolgáltatásnak meg kell nyitnia a tűzfalat a függvényalkalmazás összes kimenő IP-címére.

A függvényalkalmazás számára elérhető kimenő IP-címek megkeresése:

1. Jelentkezzen be az [Azure Resource Explorer](https://resources.azure.com)be.
2. Válassza ki **az előfizetéseket > {előfizetése} > a Microsoft.Web > webhelyein >.**
3. A JSON panelen keresse meg `id` a helyet egy tulajdonsággal, amely a függvényalkalmazás nevében végződik.
4. Lásd `outboundIpAddresses` `possibleOutboundIpAddresses`és. 

A készlet `outboundIpAddresses` jelenleg elérhető a függvényalkalmazás. A készlet `possibleOutboundIpAddresses` tartalmazza az IP-címeket, amelyek csak akkor lesznek elérhetők, ha a függvényalkalmazás [más tarifacsomagokra méretez.](#outbound-ip-address-changes)

A rendelkezésre álló kimenő IP-címek megkeresésének másik módja a [Cloud Shell](../cloud-shell/quickstart.md)használata:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Ha egy függvényalkalmazás, amely fut a [felhasználási terv](functions-scale.md#consumption-plan) van méretezve, egy új sor kimenő IP-címek lehet hozzárendelni. Ha a felhasználási terven fut, előfordulhat, hogy a teljes adatközpontot engedélyezési listára kell tenni.

## <a name="data-center-outbound-ip-addresses"></a>Adatközpont kimenő IP-címei

Ha a függvényalkalmazások által használt kimenő IP-címeket kell engedélyezési listára tenni, egy másik lehetőség a függvényalkalmazások adatközpontjának (Azure-régió) engedélyezési listázása. [Letölthet egy JSON-fájlt, amely felsorolja az összes Azure-adatközpont IP-címét.](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Ezután keresse meg a JSON-töredéket, amely a függvényalkalmazás által futtatott régióra vonatkozik.

Például így nézhet ki a nyugat-európai JSON-töredék:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Ha további információra van szüksége arról, hogy mikor frissül a fájl, és mikor változnak az IP-címek, **bontsa** ki a [Letöltőközpont lap](https://www.microsoft.com/en-us/download/details.aspx?id=56519)Részletek szakaszát.

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Bejövő IP-cím módosításai

A bejövő **IP-cím** változhat, ha:

- Töröljön egy függvényalkalmazást, és hozza létre újra egy másik erőforráscsoportban.
- Törölje az erőforráscsoport és a régió utolsó függvényalkalmazását, és hozza létre újra.
- Töröljön egy SSL-kötést, például [a tanúsítvány megújítása](../app-service/configure-ssl-certificate.md#renew-certificate)során.

Amikor a függvényalkalmazás [egy felhasználási tervben](functions-scale.md#consumption-plan)fut, a bejövő IP-cím akkor is változhat, ha nem végzett műveleteket, például a fent felsoroltakat. [listed above](#inbound-ip-address-changes)

## <a name="outbound-ip-address-changes"></a>Kimenő IP-cím módosításai

A függvényalkalmazás elérhető kimenő IP-címeinek készlete a következő kbanadékban változhat:

* Minden olyan műveletet végrehajthat, amely megváltoztathatja a bejövő IP-címet.
* Módosítsa az App Service-csomag tarifacsomagját. Az összes lehetséges kimenő IP-cím listája, amelyet az alkalmazás az `possibleOutboundIPAddresses` összes tarifacsomaghoz használhat, a tulajdonságban található. Lásd: [Kimenő IP-k keresése.](#find-outbound-ip-addresses)

Amikor a függvényalkalmazás [egy felhasználási tervben](functions-scale.md#consumption-plan)fut, a kimenő IP-cím akkor is változhat, ha nem hajtott végre műveleteket, például a fent felsoroltakat. [listed above](#inbound-ip-address-changes)

Kimenő IP-címmódosítás szándékos kényszerítése:

1. Az App Service-csomag fel- vagy leskálázható a Standard és a Premium v2 tarifacsomagok között.
2. Várjon 10 percet.
3. Skálázás vissza oda, ahonnan elindult.

## <a name="ip-address-restrictions"></a>IP-címekre vonatkozó korlátozások

Konfigurálhatja azoknak az IP-címeknek a listáját, amelyeket engedélyezni vagy megtagadni szeretne egy függvényalkalmazáshoz való hozzáféréshez. További információ: [Azure App Service static IP restrictions](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedikált IP-címek

Ha statikus, dedikált IP-címekre van szüksége, javasoljuk, hogy [az App Service-környezetek](../app-service/environment/intro.md) (az App Service-csomagok [elkülönített szintje).](https://azure.microsoft.com/pricing/details/app-service/) További információt az [App Service-környezet IP-címei](../app-service/environment/network-info.md#ase-ip-addresses) és [az App Service-környezetbe irányuló bejövő forgalom szabályozása című témakörben talál.](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)

Annak kiderítése, hogy a függvényalkalmazás fut-e egy App Service-környezetben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg a függvényalkalmazást.
3. Válassza az **Áttekintés** lapot.
4. Az App Service-csomag csomag az **App Service-csomag/tarifacsomag**alatt jelenik meg. Az App Service-környezet tarifacsomag **elkülönített.**
 
Alternatív megoldásként használhatja a [Cloud Shellt:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Az App `sku` Service-környezet `Isolated`.

## <a name="next-steps"></a>További lépések

Az IP-változások gyakori oka a függvényalkalmazás-méretezés módosítása. [További információ a függvényalkalmazás-méretezésről.](functions-scale.md)
