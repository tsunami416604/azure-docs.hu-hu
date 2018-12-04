---
title: IP-címek az Azure Functions szolgáltatásban
description: Ismerje meg, hogyan találhatja meg a bejövő és kimenő IP-címek alkalmazások esetében, és mi okozhatja, hogy változtassa meg.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849479"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-címek az Azure Functions szolgáltatásban

Ez a cikk bemutatja a függvényalkalmazások IP-címek kapcsolódó alábbi témaköröket:

* Hogyan találhatja meg az IP-címek jelenleg használatban lévő által az függvényalkalmazás.
* Alkalmazás IP-címek módosítható, mi okozza a függvény.
* Hogyan lehet korlátozni a függvényalkalmazás eléréséhez az IP-címek.
* Hogyan kérhet dedikált IP-címek egy függvényalkalmazáshoz.

IP-címek társítva, a függvényalkalmazások, és nem az egyes függvényekkel. A bejövő HTTP-kérelmekre a bejövő IP-cím nem használható az egyes függvényeket; az alapértelmezett tartomány nevét (functionappname.azurewebsites.net) vagy egy egyéni tartománynevet kell használniuk.

## <a name="function-app-inbound-ip-address"></a>Függvényalkalmazás bejövő IP-cím

Minden függvényalkalmazáshoz egyetlen bejövő IP-címmel rendelkezik. Az IP-cím megkeresése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a függvényalkalmazást.
3. Válassza ki **platformfunkciók**.
4. Válassza ki **tulajdonságok**, és a bejövő IP-cím alatt jelenik meg **virtuális IP-cím**.

## <a name="find-outbound-ip-addresses"></a>Függvény alkalmazás kimenő IP-címek

Minden függvényalkalmazáshoz egy elérhető kimenő IP-címekkel rendelkezik. Minden kimenő adatforgalmat egy függvényt, például egy háttér-adatbázist a rendelkezésre álló kimenő IP-címek valamelyikét használja, mint a forrás IP-cím. Nem tudja, hogy korábban melyik IP-címet egy adott kapcsolathoz fogja használni. Ebből kifolyólag a háttérszolgáltatás meg kell nyitnia a tűzfalat, hogy az összes kimenő IP-címeket a függvényalkalmazás.

A kimenő IP-cím a függvényalkalmazás megkeresése:

1. Jelentkezzen be a [Azure erőforrás-kezelő](https://resources.azure.com).
2. Válassza ki **előfizetések > {subscription} > szolgáltatók > Microsoft.Web > helyek**.
3. A JSON panelen keresse meg a helyhez egy `id` tulajdonság, amely be a függvényalkalmazás nevét.
4. Lásd: `outboundIpAddresses` és `possibleOutboundIpAddresses`. 

Készletét `outboundIpAddresses` jelenleg a függvényalkalmazás számára érhető el. Készletét `possibleOutboundIpAddresses` lesz elérhető IP-címeket tartalmazza. csak akkor, ha a függvényalkalmazás [más tarifacsomagok méretezhető](#outbound-ip-address-changes).

Keresse meg a rendelkezésre álló kimenő IP-címek egy alternatív módot a használatával a [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Ha egy futó függvényalkalmazást a [Használatalapú csomag](functions-scale.md#consumption-plan) van méretezve, egy új kimenő IP-címek tartományának rendelt. Amikor futtatja a Használatalapú csomag, szükség lehet az engedélyezési listára a teljes adatközpont.

## <a name="data-center-outbound-ip-addresses"></a>Adatközpont-kimenő IP-címek

Ha szeretné a függvény alkalmazások által használt kimenő IP-címek engedélyezési listáján, egy másik lehetőség, az engedélyezési listára a függvényalkalmazások adatközpont (Azure-régióban). Is [töltse le egy JSON-fájlt, amely felsorolja az összes Azure-beli adatközpontok IP-címek](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Majd keresse meg a JSON-töredék, amely a függvényalkalmazás futó régióra vonatkozik.

Például ez az a Nyugat-Európa JSON-töredék néz:

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

 Ez a fájl frissítésekor, és ha az IP-címek változásakor információt, bontsa ki a **részletek** szakaszában a [letöltőközpontlapon](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a>Bejövő IP-cím módosításai

A bejövő IP-cím **előfordulhat, hogy** módosítsa, ha Ön:

- Törölje a függvényalkalmazást, és hozza létre újra egy másik erőforráscsoportban található.
- Törölje a legutóbbi erőforrás csoport és a régió kombinációja függvényalkalmazást, és hozza létre újból.
- Egy SSL-kötés törlése például során [tanúsítvány-megújítási](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)).

Ha fut a függvényalkalmazás egy [Használatalapú csomag](functions-scale.md#consumption-plan), a bejövő IP-cím is előfordulhat, hogy módosítsa, ha még nem léptek a azokat a műveleteket, mint például a felsorolt.

## <a name="outbound-ip-address-changes"></a>Kimenő IP-cím módosításai

A készlet elérhető kimenő IP-címek függvényalkalmazás változhatnak, amikor Ön:

* Semmit sem, amely a bejövő IP-címet módosíthatja.
* Módosítsa az App Service-csomag tarifacsomagját. A lista összes lehetséges kimenő IP-címet az alkalmazás használhatja az összes tarifacsomag, a `possibleOutboundIPAddresses` tulajdonság. Lásd: [keresse meg a kimenő IP-címek](#find-outbound-ip-addresses).

Ha fut a függvényalkalmazás egy [Használatalapú csomag](functions-scale.md#consumption-plan), a kimenő IP-cím is előfordulhat, hogy módosítsa, ha még nem léptek a azokat a műveleteket, mint például a felsorolt.

Kimenő IP-cím módosítása szándékosan kényszerítése:

1. Az App Service-csomag vertikális fel- és lefelé Standard és prémium v2 tarifacsomagok között.
2. Várjon 10 percet.
3. Vissza a méretezhető, ahol elindult.

## <a name="ip-address-restrictions"></a>IP-címkorlátozások

Konfigurálhatja úgy, hogy engedélyezi vagy megtagadja a hozzáférést egy függvényalkalmazáshoz kívánt IP-címek listáját. További információkért lásd: [Azure App Service statikus IP-korlátozások](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedikált IP-címek

Ha statikus, dedikált IP-címeket kell, javasoljuk, hogy [App Service Environment-környezetek](../app-service/environment/intro.md) (a [izolált csomag](https://azure.microsoft.com/pricing/details/app-service/) az App Service-csomagok). További információkért lásd: [App Service-környezet IP-címek](../app-service/environment/network-info.md#ase-ip-addresses) és [az App Service-környezet bejövő forgalom szabályozása](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Annak megállapítása, hogy a függvény alkalmazás az App Service Environment-környezetben fut:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a függvényalkalmazást.
3. Válassza ki a **áttekintése** fülre.
4. Az App Service csomag szint alatt megjelenő **App Service-csomag/tarifacsomag**. Az App Service Environment tarifacsomag **elkülönített**.
 
Alternatív megoldásként használhatja a [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Az App Service Environment `sku` van `Isolated`.

## <a name="next-steps"></a>További lépések

Egy IP-módosításainak gyakori oka függvény alkalmazás a módosítások. [További tudnivalók a függvény alkalmazás méretezése](functions-scale.md).
