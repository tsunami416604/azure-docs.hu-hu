---
title: Az Azure App Service hozzáférési korlátozásai
description: Ismerje meg, hogyan biztosíthatja alkalmazását az Azure App Service-ben a hozzáférési korlátozások megadásával.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639269"
---
# <a name="azure-app-service-access-restrictions"></a>Az Azure App Service hozzáférési korlátozásai

A hozzáférési korlátozások lehetővé teszik az alkalmazás hálózati hozzáférését szabályozó prioritású engedélyezési/megtagadási lista meghatározását. A lista tartalmazhat IP-címeket vagy Az Azure virtuális hálózat alhálózatait. Ha egy vagy több bejegyzés van, akkor a lista végén található implicit "mindent letagadni" van.

A hozzáférési korlátozások képesség működik az összes App Service üzemeltetett munka terhelések, beleértve a következőket: webalkalmazások, API-alkalmazások, Linux-alkalmazások, Linux-tárolóalkalmazások és Funkciók.

Amikor kérelmet küld az alkalmazáshoz, a FROM-címet a rendszer a hozzáférési korlátozások listájában szereplő IP-címszabályok alapján értékeli ki. Ha a FROM cím a Microsoft.Web szolgáltatásvégpontjaival konfigurált alhálózatban van, akkor a rendszer összehasonlítja a forrásalhálózatot a hozzáférési korlátozások listájában szereplő virtuális hálózati szabályokkal. Ha a cím nem érhető el a listában szereplő szabályok alapján, a szolgáltatás [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkóddal válaszol.

A hozzáférési korlátozások funkció az App Service előtér-szerepkörökben van megvalósítva, amelyek a feldolgozói állomások előtt vannak, ahol a kód fut. Ezért a hozzáférési korlátozások gyakorlatilag hálózati Hozzáférés-hozzáférés-hozzáférési relenek.

A webalkalmazáshoz való hozzáférés azure-beli virtuális hálózatból (VNet) való korlátozásának lehetőségét [szolgáltatásvégpontnak nevezzük.][serviceendpoints] A szolgáltatásvégpontok lehetővé teszik a több-bérlős szolgáltatáshoz való hozzáférés korlátozását a kiválasztott alhálózatokból. Mind a hálózati oldalon, mind a szolgáltatáson engedélyezni kell, amelyhez engedélyezve van. Nem működik, hogy korlátozza a forgalmat az App Service-környezetben üzemeltetett alkalmazások. Ha Egy App Service-környezetben, szabályozhatja az alkalmazáshoz való hozzáférést az IP-cím szabályokkal.

![hozzáférési korlátozások áramlása](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Hozzáférés-korlátozási szabályok hozzáadása és szerkesztése a portálon ##

Ha hozzáférési korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, a menüben nyissa meg a **hálózati**>**hozzáférési korlátozásokat,** és kattintson a **Hozzáférési korlátozások konfigurálása parancsra.**

![Az App Service hálózati beállításai](media/app-service-ip-restrictions/access-restrictions.png)  

A hozzáférési korlátozások felhasználói felületén áttekintheti az alkalmazáshoz definiált hozzáférés-korlátozási szabályok listáját.

![hozzáférési korlátozások listázása](media/app-service-ip-restrictions/access-restrictions-browse.png)

A listában megjelenik az alkalmazáson található összes aktuális korlátozás. Ha virtuális hálózatra van korlátozás az alkalmazásra vonatkozóan, a táblázat megmutatja, hogy a szolgáltatásvégpontok engedélyezve vannak-e a Microsoft.Web számára. Ha nincsenek meghatározott korlátozások az alkalmazásra vonatkozóan, az alkalmazás bárhonnan elérhető lesz.  

## <a name="adding-ip-address-rules"></a>IP-címszabályok hozzáadása

A **[+] Szabály hozzáadása** gombra kattintva új hozzáférés-korlátozási szabályt adhat hozzá. Ha hozzáad egy szabályt, az azonnal hatályba lép. A szabályok at prioritási sorrendben hajtják végre a legalacsonyabb számtól kezdve és felmennek. Van egy implicit deny minden, ami érvényben van, ha még egyetlen szabályt is hozzáad.

Szabály létrehozásakor ki kell jelölnie az engedélyezés/megtagadás lehetőséget és a szabály típusát is. Azt is meg kell adnia a prioritási értéket, és azt, hogy mihez korlátozza a hozzáférést.  A szabályhoz hozzáadhat egy nevet és egy leírást.  

![IP-hozzáférés korlátozási szabály hozzáadása](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

IP-címalapú szabály beállításához válassza ki az IPv4 vagy az IPv6 típusát. Az IP-cím jelölését CIDR-jelölésben kell megadni mind az IPv4- és Az IPv6-címekhez. A pontos cím megadásához használhat valami hasonlót, mint az 1.2.3.4/32, ahol az első négy oktett az ÖN IP-címét, a /32 pedig a maszk. Az iPv4 CIDR jelölés minden címhez 0.0.0.0/0. Ha többet szeretne megtudni a CIDR jelölésről, olvassa el [az Osztály nélküli tartományok közötti útválasztás t.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

A szolgáltatásvégpontok lehetővé teszik a kiválasztott Azure virtuális hálózati alhálózatokhoz való hozzáférés korlátozását. Egy adott alhálózathoz való hozzáférés korlátozásához hozzon létre egy korlátozási szabályt a virtuális hálózat típusával. Kiválaszthatja azt az előfizetést, virtuális hálózatot és alhálózatot, amelyhez engedélyezni vagy megtagadni szeretné a hozzáférést. Ha a szolgáltatásvégpontok még nincsenek engedélyezve a Microsoft.Web szolgáltatással a kiválasztott alhálózaton, akkor a rendszer automatikusan engedélyezi, hacsak be nem jelöli a nem erre vonatkozó jelölőnégyzetet. Az a helyzet, amelyben engedélyezni szeretné az alkalmazásban, de nem az alhálózaton, nagymértékben kapcsolódik ahhoz, hogy rendelkezik-e az alhálózaton lévő szolgáltatásvégpontok engedélyezéséhez szükséges engedélyekkel. Ha valaki mást kell beszereznie, hogy engedélyezze a szolgáltatásvégpontokat az alhálózaton, bejelölheti a jelölőnégyzetet, és konfigurálhatja az alkalmazást a szolgáltatásvégpontokhoz, előre, hogy később engedélyezve lesz az alhálózaton. 

![virtuális hálózat-hozzáférés korlátozási szabály hozzáadása](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

A szolgáltatásvégpontok nem használhatók az App Service-környezetben futó alkalmazásokhoz való hozzáférés korlátozására. Ha az alkalmazás egy App Service-környezetben van, az IP-hozzáférési szabályokkal szabályozhatja az alkalmazáshoz való hozzáférést. 

A szolgáltatás-végpontok segítségével konfigurálhatja az alkalmazást alkalmazásátjárókkal vagy más WAF-eszközökkel. A biztonságos háttérrendszereket is konfigurálhat többrétegű alkalmazásokat. A lehetőségek ről további információt a [Hálózati szolgáltatások, valamint](networking-features.md) az App Service és [az Application Gateway szolgáltatásvégekkel való integráció című részében](networking/app-gateway-with-service-endpoints.md)talál.

## <a name="managing-access-restriction-rules"></a>Hozzáférés-korlátozási szabályok kezelése

Egy meglévő hozzáférés-korlátozási szabály szerkesztéséhez bármelyik sorra kattintva szerkesztheti a sorokat. A módosítások azonnal hatályba lépnek, beleértve a prioritási sorrend változásait is.

![hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Szabály szerkesztésekénél nem módosíthatja az IP-címszabály és a virtuális hálózati szabály közötti szöveget. 

![hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Szabály törléséhez kattintson a **...** gombra a szabályon, majd az **Eltávolítás gombra.**

![hozzáférés-korlátozási szabály törlése](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Egyetlen IP-cím blokkolása ##

Az első IP-korlátozási szabály hozzáadásakor a szolgáltatás egy 2147483647 prioritású explicit **Minden megtagadása** szabályt ad hozzá. A gyakorlatban az **explicit Megtagadása minden** szabály lesz az utolsó szabály végrehajtása, és blokkolja a hozzáférést minden OLYAN IP-címet, amely nem kifejezetten engedélyezett egy **engedélyezési** szabály használatával.

Abban az esetben, ha a felhasználók szeretnék explicit módon blokkolja egy IP-cím vagy IP-cím blokk, de minden más hozzáférést, meg kell adni egy explicit **Engedélyezés minden** szabály.

![egyetlen IP-cím blokkolása](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM webhely 

Amellett, hogy szabályozhatja az alkalmazáshoz való hozzáférést, korlátozhatja az alkalmazás által használt scm webhelyhez való hozzáférést is. Az scm-webhely a webes üzembe helyezési végpont és a Kudu konzol. Az alkalmazásból külön hozzárendelhet hozzáférési korlátozásokat az scm-webhelyhez, vagy ugyanazt a készletet használhatja mind az alkalmazáshoz, mind az scm-webhelyhez. Ha bejelöli a jelölőnégyzetet, hogy ugyanazok a korlátozások legyenek érvényben, mint az alkalmazás, minden elsötétül. Ha törölje a jelet a jelölőnégyzetből, az scm webhelyen korábban megadott beállításokat alkalmazza a program. 

![hozzáférési korlátozások listázása](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>A hozzáférés korlátozására vonatkozó szabályok automatizált manipulálása ##

[Az Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) és az [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) támogatja a hozzáférési korlátozások szerkesztését. Példa hozzáférési korlátozás hozzáadására az Azure CLI használatával:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Példa hozzáférési korlátozás hozzáadására az Azure PowerShell használatával:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Az értékek manuálisan is beállíthatók egy [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT művelettel az alkalmazás konfigurációján az Erőforrás-kezelőben vagy egy Azure Resource Manager-sablon használatával. Például használhatja resources.azure.com, és szerkesztheti az ipSecurityRestrictions blokka a szükséges JSON hozzáadásához.

Az erőforrás-kezelőben az adatok helye:

management.azure.com/subscriptions/**előfizetésazonosító**/resourceGroups/**erőforráscsoportok**/providers/Microsoft.Web/sites/**webapp neve**/config/web?api-version=2018-02-01

A JSON szintaxisa a korábbi példa:
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Az Azure Functions hozzáférési korlátozásai

A hozzáférési korlátozások az App Service-csomagokkal azonos funkciókkal rendelkező függvényalkalmazásokhoz is elérhetők. A hozzáférési korlátozások engedélyezése letiltja a portálkódszerkesztőt a nem engedélyezett IP-címekhez.

## <a name="next-steps"></a>További lépések
[Hozzáférési korlátozások az Azure Functionshez](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Alkalmazásátjáró integrációja szolgáltatásvégekkel](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
