---
title: IP-címek az Azure App Service szolgáltatásban |} Microsoft Docs
description: Ismerteti, hogyan bemeneti és kimeneti IP címeket használják az App Service és a rájuk vonatkozó információk, az alkalmazás megkeresése.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: cephalin
ms.openlocfilehash: 906a5d511615c57b6ff807ac240a838c63917e66
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Bejövő és kimenő IP-címek az Azure App Service-ben

[Az Azure App Service](app-service-web-overview.md) van egy több-bérlős szolgáltatást, kivéve a [App Service Environment-környezetek](environment/intro.md). Alkalmazásokat, amelyek nem találhatók az App Service-környezetek (nem található a [elkülönített réteg](https://azure.microsoft.com/pricing/details/app-service/)) megosztás hálózati infrastruktúra egyéb alkalmazásokkal. Ennek eredményeképpen a bemeneti és kimeneti IP-címek egy alkalmazás eltérhet, és bizonyos esetekben is módosíthatja. 

[App Service Environment-környezetek](environment/intro.md) használja a dedikált hálózati infrastruktúrákban üzemelő, így az App Service environment-környezetben futó alkalmazások statikus, dedikált IP-címek, mind a bejövő és kimenő kapcsolatok.

## <a name="when-inbound-ip-changes"></a>Amikor a bejövő IP változik

A kiterjesztett példányok száma, függetlenül minden alkalmazás rendelkezik egy egyetlen bejövő IP-címet. A bejövő IP-címet módosíthatja, ha hajt végre a következő műveletek egyikét:

- Törölje az alkalmazást, és hozza létre újra egy másik erőforráscsoportban található.
- Törölje az utolsó alkalmazást egy erőforráscsoportban _és_ régió kombinációja és hozza létre újra.
- Törölje a létező SSL-kötést, többek között tanúsítványt a megújítás során (lásd: [megújítani a tanúsítványokat](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Bejövő statikus IP-cím beszerzése

Egyes esetekben szükség lehet egy dedikált, statikus IP-címet az alkalmazás. Ahhoz, hogy egy bejövő statikus IP-címet, konfigurálni kell egy [IP-alapú SSL-kötés](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Ha az alkalmazás biztonságos SSL funkció ténylegesen nem szükséges, még akkor is feltöltheti egy önaláírt tanúsítványt ehhez a kötéshez. IP-alapú SSL-kötést, a tanúsítvány van kötve az IP-címet saját magát, így az App Service rendelkezések fordulhat elő, hogy egy statikus IP. 

## <a name="when-outbound-ips-change"></a>Ha a kimenő IP-címek módosítása

A kiterjesztett példányok száma, függetlenül minden alkalmazás rendelkezik beállítása a kimenő IP-címek száma az adott időpontban. Minden kimenő kapcsolat az App Service alkalmazás, például egy háttér-adatbázis, a kimenő IP-címek egyikére használja, mint a forrás IP-címet. Nem biztos, előzetesen melyik IP-címet egy adott alkalmazás ezen példányát fogja használni a kimenő kapcsolat létrehozásához, így a háttér-szolgáltatás meg kell nyitnia a tűzfalat, hogy minden kimenő IP-címét az alkalmazás.

A kimenő IP-készlet oldja meg a app módosítások, amikor az alacsonyabb rétegek közötti alkalmazás skálázása (**alapvető**, **szabványos**, és **prémium**) és a  **Prémium szintű V2** réteg.

Megtalálhatja az összes lehetséges kimenő IP-címek készletét az alkalmazás használatához tarifacsomagok keresi, függetlenül a `possibleOutboundIPAddresses` tulajdonság. Lásd: [kimenő IP-címek keresése](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Kimenő IP-címek keresése

A kimenő IP-címek az Azure-portálon az alkalmazás által jelenleg használt megkereséséhez kattintson **tulajdonságok** bal oldali navigációs az alkalmazást. 

Ugyanazokat az információkat talál a következő parancsot futtatja a [felhő rendszerhéj](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

Található összes lehetséges kimenő IP-címek az alkalmazás, függetlenül a tarifacsomagok, futtassa a következő parancsot a [felhő rendszerhéj](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan korlátozhatja a bejövő forgalom forrás IP-cím.

> [!div class="nextstepaction"]
> [Statikus IP-címkorlátozások](app-service-ip-addresses.md)
