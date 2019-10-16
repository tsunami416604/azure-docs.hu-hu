---
title: Bejövő/kimenő IP-címek – Azure App Service | Microsoft Docs
description: Leírja, hogyan használják a bejövő és a kimenő IP-címeket a App Serviceban, és hogyan találhatják meg az alkalmazással kapcsolatos információkat.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: de98f972a43f3845d2a01b928d90283732ef4843
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329776"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Bejövő és kimenő IP-címek Azure App Service

[Azure app Service](overview.md) egy több-bérlős szolgáltatás, a [app Service-környezetek](environment/intro.md)kivételével. Azok az alkalmazások, amelyek nem App Service-környezetben (az [elkülönített](https://azure.microsoft.com/pricing/details/app-service/)szinten nem) osztoznak a hálózati infrastruktúrában más alkalmazásokkal. Ennek eredményeképpen az alkalmazás bejövő és kimenő IP-címei eltérőek lehetnek, és bizonyos helyzetekben is változhatnak. 

[App Service környezetek](environment/intro.md) dedikált hálózati infrastruktúrát használnak, így a app Service környezetben futó alkalmazások statikus, dedikált IP-címeket kapnak mind a bejövő, mind a kimenő kapcsolatokhoz.

## <a name="when-inbound-ip-changes"></a>Bejövő IP-változások esetén

A kibővített példányok számától függetlenül minden alkalmazás egyetlen bejövő IP-címmel rendelkezik. A bejövő IP-cím a következő műveletek valamelyikének végrehajtásakor változhat:

- Töröljön egy alkalmazást, és hozza létre újra egy másik erőforráscsoporthoz.
- Törölje az erőforráscsoport _és_ a régió kombinációjában az utolsó alkalmazást, és hozza létre újból.
- Töröljön egy meglévő SSL-kötést, például a tanúsítvány megújítása során (lásd: [tanúsítványok megújítása](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="find-the-inbound-ip"></a>A bejövő IP-cím keresése

Csak futtassa a következő parancsot egy helyi terminálon:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Statikus bejövő IP-cím beszerzése

Előfordulhat, hogy az alkalmazáshoz dedikált, statikus IP-címet szeretne használni. Statikus bejövő IP-cím beszerzéséhez konfigurálnia kell egy [IP-alapú SSL-kötést](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain). Ha valójában nem igényel SSL-funkciót az alkalmazás biztonságossá tételéhez, akkor akár önaláírt tanúsítványt is feltölthet ehhez a kötéshez. Az IP-alapú SSL-kötésekben a tanúsítvány magához az IP-címhez van kötve, ezért a App Service statikus IP-címet tesz elérhetővé. 

## <a name="when-outbound-ips-change"></a>A kimenő IP-címek változásakor

A kibővített példányok számától függetlenül az egyes alkalmazások megadott számú kimenő IP-címmel rendelkeznek. A App Service alkalmazásból, például egy háttér-adatbázisba irányuló kimenő kapcsolatok a kimenő IP-címek egyikét használják a forrás IP-címként. Nem tudja előre, hogy egy adott alkalmazás példánya milyen IP-címet fog használni a kimenő kapcsolat létrehozásához, így a háttér-szolgáltatásnak meg kell nyitnia a tűzfalát az alkalmazás összes kimenő IP-címére.

Az alkalmazáshoz tartozó kimenő IP-címek készlete megváltozik, amikor az alsó**szint (Alapszintű**, **standard**és **prémium**) és a **prémium v2** szint között méretezi az alkalmazást.

Megtalálhatja az alkalmazás által használható összes lehetséges kimenő IP-címet, az árképzési szintektől függetlenül, ha a `possibleOutboundIpAddresses` tulajdonságot, vagy a Azure Portal **Tulajdonságok** paneljének **további kimenő IP-címek** mezőjében keresi meg. Lásd: [kimenő IP](#find-outbound-ips)-címek keresése.

## <a name="find-outbound-ips"></a>Kimenő IP-címek keresése

Az alkalmazás által az Azure Portalban jelenleg használt kimenő IP-címek megkereséséhez kattintson a **Tulajdonságok** elemre az alkalmazás bal oldali navigációs sávján. Ezek a **kimenő IP-címek** mezőben vannak felsorolva.

Ugyanezeket az információkat a következő parancs futtatásával is megtalálhatja a [Cloud Shellban](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Az alkalmazás _összes_ lehetséges kimenő IP-címének megkereséséhez, az árképzési szintektől függetlenül, kattintson a **Tulajdonságok** elemre az alkalmazás bal oldali navigációs sávján. Ezek a **további kimenő IP-címek** mezőben vannak felsorolva.

Ugyanezeket az információkat a következő parancs futtatásával is megtalálhatja a [Cloud Shellban](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan korlátozhatja a bejövő forgalmat forrás IP-címek alapján.

> [!div class="nextstepaction"]
> [Statikus IP-korlátozások](app-service-ip-restrictions.md)
