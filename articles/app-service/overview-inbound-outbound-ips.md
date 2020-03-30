---
title: Bejövő/kimenő IP-címek
description: Megtudhatja, hogyan használják a bejövő és kimenő IP-címeket az Azure App Service-ben, mikor változnak, és hogyan találhatja meg az alkalmazás címét.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279207"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Bejövő és kimenő IP-címek az Azure App Service-ben

[Az Azure App Service](overview.md) egy több-bérlős szolgáltatás, kivéve [az App Service-környezetek.](environment/intro.md) Azok az alkalmazások, amelyek nem App Service-környezetben vannak (nem az [Elkülönített rétegben)](https://azure.microsoft.com/pricing/details/app-service/)megosztják a hálózati infrastruktúrát más alkalmazásokkal. Ennek eredményeképpen az alkalmazás bejövő és kimenő IP-címei eltérőek lehetnek, és bizonyos helyzetekben akár változhatnak is. 

[Az App Service-környezetek](environment/intro.md) dedikált hálózati infrastruktúrákat használnak, így az App Service-környezetben futó alkalmazások statikus, dedikált IP-címeket kapnak mind a bejövő, mind a kimenő kapcsolatokhoz.

## <a name="when-inbound-ip-changes"></a>Bejövő IP-változások esetén

A kicsinyített példányok számától függetlenül minden alkalmazás egyetlen bejövő IP-címmel rendelkezik. A bejövő IP-cím az alábbi műveletek valamelyikének végrehajtásakor változhat:

- Töröljön egy alkalmazást, és hozza létre újra egy másik erőforráscsoportban.
- Törölje az erőforráscsoport _és_ a régió utolsó alkalmazását, és hozza létre újra.
- Meglévő SSL-kötés törlése, például a tanúsítvány megújítása során [(lásd: Tanúsítvány megújítása).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>A bejövő IP megkeresése

Csak futtassa a következő parancsot egy helyi terminálon:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Statikus bejövő IP-cím beszerezni

Előfordulhat, hogy egy dedikált, statikus IP-címet szeretne az alkalmazáshoz. Statikus bejövő IP-cím leszámításához be kell állítania egy [IP-alapú SSL-kötést.](configure-ssl-bindings.md#secure-a-custom-domain) Ha az alkalmazás biztonságához valójában nincs szüksége SSL-funkciókra, akár önaláírt tanúsítványt is feltölthet ehhez a kötéshez. Az IP-alapú SSL-kötésben a tanúsítvány magához az IP-címhez van kötve, ezért az App Service statikus IP-címet rendel annak megvalósításához. 

## <a name="when-outbound-ips-change"></a>Amikor a kimenő IP-k változnak

A kibővített példányok számától függetlenül minden alkalmazás egy adott időpontban meghatározott számú kimenő IP-címmel rendelkezik. Az App Service-alkalmazásból kimenő kapcsolatok, például egy háttéradatbázis, a kimenő IP-címek egyikét használja forrás IP-címként. Nem tudhatja előre, hogy egy adott alkalmazáspéldány melyik IP-címet fogja használni a kimenő kapcsolat hozására, ezért a háttérszolgáltatásnak meg kell nyitnia a tűzfalat az alkalmazás összes kimenő IP-címéhez.

Az alkalmazás kimenő IP-címeinek készlete megváltozik, amikor az alkalmazást az alacsonyabb szintek **(alapszintű,** **standard**és **prémium)** és a **prémium szintű V2** szint között méretezi.

A `possibleOutboundIpAddresses` tulajdonság keresésével vagy az Azure Portal **Tulajdonságok paneljének** További kimenő IP-címek mezőjében az alkalmazás a díjszabási szintektől függetlenül használhatja az összes lehetséges kimenő **IP-cím** készletét. Lásd: [Kimenő IP-k keresése.](#find-outbound-ips)

## <a name="find-outbound-ips"></a>Kimenő IP-k keresése

Ha meg szeretné tudni, hogy az alkalmazás jelenleg az Azure Portalon használja-e a kimenő IP-címeket, kattintson a **Tulajdonságok** elemre az alkalmazás bal oldali navigációs elemében. Ezek a Kimenő **IP-címek** mezőben jelennek meg.

Ugyanezeket az információkat a következő parancs futtatásával találhatja meg a [Cloud Shell](../cloud-shell/quickstart.md)ben.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Ha az alkalmazás _összes_ lehetséges kimenő IP-címét meg szeretné találni, függetlenül a tarifaszintektől, kattintson a **Tulajdonságok** elemre az alkalmazás bal oldali navigációs sávján. Ezek a További **kimenő IP-címek** mezőben találhatók.

Ugyanezeket az információkat a következő parancs futtatásával találhatja meg a [Cloud Shell](../cloud-shell/quickstart.md)ben.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan korlátozhatja a bejövő forgalmat forrás IP-címek szerint.

> [!div class="nextstepaction"]
> [Statikus IP-korlátozások](app-service-ip-restrictions.md)
