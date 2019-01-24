---
title: Bejövő/kimenő IP-címek – az Azure App Service |} A Microsoft Docs
description: Ismerteti, hogyan bejövő és kimenő IP címeket használnak az App Service-ben, és hogyan találhatja meg az alkalmazás a rájuk vonatkozó információk.
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
ms.custom: seodec18
ms.openlocfilehash: 56ca87f318aa5f1843a3b28480be834df1669c71
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811009"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Bejövő és kimenő IP-címek az Azure App Service-ben

[Az Azure App Service](overview.md) egy több-bérlős szolgáltatás, akkor az alábbiakat kivéve [App Service Environment-környezetek](environment/intro.md). Alkalmazások, amelyek nem találhatók App Service environment (nem a [izolált csomag](https://azure.microsoft.com/pricing/details/app-service/)) megosztás hálózati infrastruktúra egyéb alkalmazásokkal. Ennek eredményeképpen a bejövő és kimenő IP-címeket, alkalmazás eltérő lehet, és bizonyos esetekben is módosíthatja. 

[App Service Environment-környezetek](environment/intro.md) használja a dedikált hálózati infrastruktúrákban üzemelő, így App Service environment-környezetben futó alkalmazások statikus, dedikált IP-címek mind a bejövő és kimenő kapcsolatokat.

## <a name="when-inbound-ip-changes"></a>Amikor módosítja a bejövő IP

Horizontálisan felskálázott példányok számától függetlenül minden alkalmazás rendelkezik egy bejövő IP-címet. A bejövő IP-cím változhat, amikor hajt végre az alábbi műveletek egyikét:

- Alkalmazás törlése, és hozza létre újra egy másik erőforráscsoportban található.
- Az utolsó alkalmazás az erőforráscsoport törlése _és_ régió kombináció és hozza létre újra.
- Törli a létező SSL-kötést, például tanúsítványának megújítása közben (lásd: [tanúsítványok megújítása](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Bejövő statikus IP-cím kérése

Néha érdemes egy dedikált, statikus IP-címet az alkalmazás számára. Egy bejövő statikus IP-címének lekéréséhez, konfigurálni kell egy [IP-alapú SSL-kötés](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Ha valójában nincs szüksége az alkalmazás biztonságossá tétele SSL funkció, még akkor is tölthet fel egy önaláírt tanúsítványt ehhez a kötéshez. IP-alapú SSL-kötést, a tanúsítvány van kötve az IP-cím, így az App Service rendelkezések egy statikus IP-címet már meg is valósult. 

## <a name="when-outbound-ips-change"></a>Ha a kimenő IP-címek módosítása

Horizontálisan felskálázott példányok számától függetlenül minden alkalmazás rendelkezik beállítása a kimenő IP-címek száma egy adott időpontban. Minden kimenő adatforgalmat az App Service-alkalmazás, például egy háttér-adatbázis, a kimenő IP-címek valamelyikét használja, mint a forrás IP-cím. Nem tudja, hogy korábban melyik IP-címet egy adott példányt fogja használni, hogy a kimenő kapcsolatot, így a háttérszolgáltatás kell nyitnia a tűzfalat, hogy az összes kimenő IP-címet az alkalmazás.

A készletet, kimenő IP-címek esetében az alkalmazás szükségleteinek változásával az alacsonyabb rétegek közötti az alkalmazás horizontális (**alapszintű**, **Standard**, és **prémium**) és a  **A prémium V2** szint.

Megtalálhatja az összes lehetséges kimenő IP-címet az alkalmazás használhatja, függetlenül tarifacsomagok felismerésével készletét a `possibleOutboundIPAddresses` tulajdonság. Lásd: [keresse meg a kimenő IP-címek](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Kimenő IP-címek keresése

Az alkalmazás az Azure Portal által jelenleg használt kimenő IP-címek megkereséséhez kattintson **tulajdonságok** az alkalmazás bal oldali navigációs panelen. 

Ugyanazokat az információkat talál a következő parancs futtatásával a [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Található összes lehetséges kimenő IP-címeket az alkalmazás tarifacsomagjaival, függetlenül a következő parancsot a [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>További lépések

Útmutató a bejövő forgalom korlátozására a forrás IP-cím alapján.

> [!div class="nextstepaction"]
> [Statikus IP-korlátozások](app-service-ip-restrictions.md)
