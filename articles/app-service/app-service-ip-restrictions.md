---
title: Hozzáférési korlátozások Azure App Service
description: Megtudhatja, hogyan védheti meg alkalmazásait Azure App Service a hozzáférési korlátozások megadásával.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 42f25c1b66261ac644f015290bed2c7473acbdaa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280351"
---
# <a name="azure-app-service-access-restrictions"></a>Hozzáférési korlátozások Azure App Service #

A hozzáférési korlátozások lehetővé teszik olyan prioritású engedélyezési/megtagadási lista definiálását, amely az alkalmazáshoz való hálózati hozzáférést vezérli. A lista tartalmazhat IP-címeket vagy Azure Virtual Network alhálózatokat. Ha egy vagy több bejegyzés van, akkor a lista végén egy implicit "megtagadás" érték szerepel.

A hozzáférési korlátozások funkció az összes App Service üzemeltetett munkaterheléssel működik, beleértve a következőket:; webalkalmazások, API-alkalmazások, Linux-alkalmazások, Linux-tárolós alkalmazások és függvények.

Amikor kérelmet küld az alkalmazására, a rendszer kiértékeli a címet a hozzáférési korlátozások listájában szereplő IP-címek szabályai alapján. Ha a feladó címe olyan alhálózatban található, amely szolgáltatási végpontokkal van konfigurálva a Microsoft. Web számára, akkor a rendszer összehasonlítja a forrás alhálózatot a hozzáférési korlátozások listájában szereplő virtuális hálózati szabályokkal. Ha a címek nem engedélyezettek a listán szereplő szabályok alapján, a szolgáltatás a [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot válaszol.

A hozzáférés-korlátozási képesség a App Service előtér-szerepkörökben valósul meg, amelyek felsőbb rétegben találhatók a feldolgozó gazdagépeken, ahol a kód fut. Ezért a hozzáférési korlátozások gyakorlatilag hálózati ACL-ek.

A webalkalmazáshoz való hozzáférés korlátozása Azure-Virtual Network (VNet) nevű [szolgáltatás-végpontok][serviceendpoints]. A szolgáltatási végpontok lehetővé teszik a több-bérlős szolgáltatásokhoz való hozzáférés korlátozását a kiválasztott alhálózatokból. A hálózati oldalon és a szolgáltatásban is engedélyezve kell lennie. Nem működik a App Service Environmentban üzemeltetett alkalmazások forgalmának korlátozására. Ha App Service Environment, akkor az alkalmazáshoz való hozzáférést az IP-cím szabályaival szabályozhatja.

![hozzáférési korlátozások folyamata](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Hozzáférés-korlátozási szabályok hozzáadása és szerkesztése a portálon ##

Ha hozzáférési korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, a menüben nyissa meg a **hálózati**>**hozzáférési korlátozásokat** , és kattintson a **hozzáférési korlátozások konfigurálása** elemre.

![App Service hálózati beállítások](media/app-service-ip-restrictions/access-restrictions.png)  

A hozzáférési korlátozások felhasználói felületén áttekintheti az alkalmazáshoz definiált hozzáférési korlátozási szabályok listáját.

![hozzáférési korlátozások listázása](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista megjeleníti az alkalmazásra vonatkozó összes jelenlegi korlátozást. Ha az alkalmazás VNet korlátozást is tartalmaz, akkor a táblázat azt mutatja, hogy a szolgáltatási végpontok engedélyezve vannak-e a Microsoft. Web számára. Ha nincs definiált korlátozás az alkalmazáshoz, az alkalmazás bárhonnan elérhető lesz.  

## <a name="adding-ip-address-rules"></a>IP-cím szabályok hozzáadása

Új hozzáférés-korlátozási szabály hozzáadásához kattintson a **[+] Hozzáadás** lehetőségre. A szabály hozzáadása után azonnal érvénybe lép. A szabályok érvényesítése prioritási sorrendben történik a legalacsonyabb számtól kezdődően. Minden olyan implicit megtagadás létezik, amely akkor is érvényben van, ha még egyetlen szabályt adott hozzá.

Szabály létrehozásakor ki kell választani az engedélyezés/megtagadás lehetőséget, valamint a szabály típusát is. Emellett meg kell adnia a prioritási értéket, és azt, hogy mire korlátozza a hozzáférést.  Igény szerint hozzáadhat egy nevet és egy leírást a szabályhoz.  

![IP-hozzáférési korlátozási szabály hozzáadása](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

IP-cím alapú szabály beállításához válasszon ki egy IPv4-vagy IPv6-típust. Az IP-cím jelölését CIDR jelöléssel kell megadni az IPv4-és IPv6-címek esetében. Pontos cím megadásához használhatja a 1.2.3.4/32-et, ahol az első négy oktett az IP-címét és/32 a maszkot jelöli. Az összes cím IPv4-CIDR jelölése 0.0.0.0/0. Ha többet szeretne megtudni a CIDR jelöléséről, olvassa el az [osztály nélküli tartományok közötti útválasztást](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

A szolgáltatási végpontok lehetővé teszik a kiválasztott Azure-beli virtuális hálózati alhálózatokhoz való hozzáférés korlátozását. Egy adott alhálózathoz való hozzáférés korlátozásához hozzon létre egy korlátozási szabályt Virtual Network típussal. Kiválaszthatja azt az előfizetést, VNet és alhálózatot, amely számára engedélyezni vagy megtagadni kívánja a hozzáférést. Ha a szolgáltatási végpontok még nincsenek engedélyezve a Microsoft. Web használatával a kiválasztott alhálózathoz, akkor automatikusan engedélyezve lesz, hacsak nem jelöli be ezt a jelölőnégyzetet. Az az eset, amikor engedélyezni szeretné az alkalmazást az alkalmazásban, de az alhálózat nem a nagy mértékben kapcsolódik, ha rendelkezik a szolgáltatási végpontok engedélyezéséhez szükséges engedélyekkel az alhálózaton. Ha valaki másnak szeretné engedélyezni a szolgáltatási végpontok engedélyezését az alhálózaton, jelölje be a jelölőnégyzetet, és állítsa be úgy az alkalmazást, hogy a szolgáltatás-végpontok számára a későbbiekben az alhálózaton való engedélyezésre legyen szükség. 

![VNet-hozzáférési korlátozási szabály hozzáadása](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

A szolgáltatási végpontok nem használhatók a App Service Environmenton futó alkalmazásokhoz való hozzáférés korlátozására. Ha az alkalmazás egy App Service Environmentban van, az IP-hozzáférési szabályok segítségével szabályozhatja az alkalmazáshoz való hozzáférést. 

A szolgáltatás-végpontokkal konfigurálhatja az alkalmazást Application Gateway vagy más WAF-eszközök használatával. A többrétegű alkalmazásokat is konfigurálhatja biztonságos háttérrendszer használatával. Az egyes lehetőségekkel kapcsolatos további részletekért olvassa el a [hálózatkezelési szolgáltatások és a app Service](networking-features.md) és [Application Gateway a szolgáltatás-végpontokkal való integráció](networking/app-gateway-with-service-endpoints.md)című részben.

## <a name="managing-access-restriction-rules"></a>Hozzáférés-korlátozási szabályok kezelése

Bármelyik sorra rákattinthat egy meglévő hozzáférés-korlátozási szabály szerkesztéséhez. A módosítások azonnal érvénybe lépnek, beleértve a prioritási sorrend változásait is.

![hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Egy szabály szerkesztésekor a típus nem módosítható egy IP-cím szabály és egy Virtual Network szabály között. 

![hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Egy szabály törléséhez kattintson a **...** elemre a szabályon, majd kattintson az **Eltávolítás**gombra.

![hozzáférés-korlátozási szabály törlése](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Egyetlen IP-cím blokkolása ##

Az első IP-korlátozási szabály hozzáadásakor a szolgáltatás hozzáad egy explicit **megtagadási** szabályt, amelynek prioritása 2147483647. A gyakorlatban az összes szabály explicit **megtagadása** szabály lesz végrehajtva, és letiltja a hozzáférést minden olyan IP-címhez, amely explicit módon nem engedélyezett **engedélyezési** szabály használatával.

Ahhoz a forgatókönyvhöz, amelyben a felhasználók explicit módon blokkolják egyetlen IP-cím vagy IP-cím blokkot, de minden más hozzáférést lehetővé tesznek, hozzá kell adnia egy explicit **engedélyezése az összes** szabályt.

![egyetlen IP-cím letiltása](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM-hely 

Az alkalmazáshoz való hozzáférés szabályozása mellett az alkalmazás által használt SCM-webhelyhez is korlátozhatja a hozzáférést. Az SCM-hely a web Deploy végpontja és a kudu-konzol is. Külön rendelhet hozzáférési korlátozásokat az SCM-webhelyhez az alkalmazásból, vagy használhatja ugyanazt a készletet az alkalmazáshoz és az SCM-helyhez is. Ha bejelöli a négyzetet, hogy ugyanazokat a korlátozásokat adja meg, mint az alkalmazás, akkor minden kimarad. Ha törli a jelet a jelölőnégyzetből, a rendszer az SCM-hely korábbi beállításait is alkalmazza. 

![hozzáférési korlátozások listázása](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Hozzáférés-korlátozási szabályok programozott manipulációja ##

Az [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) és a [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) támogatja a hozzáférési korlátozások szerkesztését. Példa hozzáférési korlátozás hozzáadására az Azure CLI használatával:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Példa hozzáférési korlátozás hozzáadására a Azure PowerShell használatával:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Az értékek manuálisan is megadhatók [Azure REST API](https://docs.microsoft.com/rest/api/azure/) Put művelettel a Resource Manager alkalmazás konfigurációjában, vagy egy Azure Resource Manager sablon használatával. Például használhatja a resources.azure.com, és szerkesztheti a ipSecurityRestrictions-blokkot a szükséges JSON hozzáadásához.

Az információ helye a Resource Managerben:

management.azure.com/subscriptions/**előfizetés**-azonosító **/resourceGroups//Providers/Microsoft.Web/Sites/** **webalkalmazás neve**/config/web? API-Version = 2018-02-01

A korábbi példa JSON-szintaxisa a következő:
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

## <a name="azure-function-app-access-restrictions"></a>Azure függvényalkalmazás hozzáférési korlátozások

A hozzáférési korlátozások a App Service csomagokkal megegyező funkcióval rendelkező Function apps-alkalmazásokhoz is elérhetők. A hozzáférési korlátozások engedélyezése letiltja a portál Kódszerkesztő használatát a nem engedélyezett IP-címekhez.

## <a name="next-steps"></a>További lépések
[Az Azure Function apps hozzáférési korlátozásai](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Application Gateway integráció a szolgáltatási végpontokkal](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
