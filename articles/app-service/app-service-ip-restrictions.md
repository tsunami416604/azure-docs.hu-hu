---
title: Hozzáférési korlátozások Azure App Service
description: Megtudhatja, hogyan védheti meg alkalmazásait Azure App Service a hozzáférési korlátozások beállításával.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576444"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Hozzáférési korlátozások beállítása Azure App Service

A hozzáférési korlátozások beállításával meghatározhatja az alkalmazáshoz való hálózati hozzáférést szabályozó, prioritással elválasztott engedélyezési/megtagadási listát. A lista tartalmazhat IP-címeket vagy Azure Virtual Network alhálózatokat. Ha egy vagy több bejegyzés van, akkor a lista végén van egy implicit *Megtagadás* .

A hozzáférés-korlátozási képesség minden Azure App Service üzemeltetett munkaterheléssel működik. A számítási feladatok közé tartozhatnak a webalkalmazások, az API-alkalmazások, a Linux-alkalmazások, a linuxos tároló-alkalmazások és a függvények.

Amikor kérelmet küld az alkalmazására, a rendszer kiértékeli a feladó címét a hozzáférés-korlátozási listán szereplő IP-címek szabályai alapján. Ha a feladó címe olyan alhálózatban található, amely szolgáltatási végpontokkal van konfigurálva a Microsoft. Web számára, a rendszer összehasonlítja a forrás alhálózatot a hozzáférés-korlátozási listán szereplő virtuális hálózati szabályokkal. Ha a címnek a listában szereplő szabályok alapján való hozzáférése nem engedélyezett, a szolgáltatás [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot választ.

A hozzáférés-korlátozási képesség a App Service előtér-szerepkörökben valósul meg, amelyek felsőbb rétegben találhatók a munkavégző gazdagépeken, ahol a kód fut. Ezért a hozzáférési korlátozások hatékony hálózati hozzáférés-vezérlési listák (ACL-ek).

A webalkalmazáshoz való hozzáférés korlátozása egy Azure-beli virtuális hálózatról [szolgáltatás-végpontok][serviceendpoints]által engedélyezett. A szolgáltatás-végpontokkal korlátozhatja a hozzáférést egy több-bérlős szolgáltatáshoz a kiválasztott alhálózatokból. Nem működik a App Service Environmentban üzemeltetett alkalmazások forgalmának korlátozására. Ha App Service Environment van, az IP-cím szabályok alkalmazásával szabályozhatja az alkalmazáshoz való hozzáférést.

> [!NOTE]
> A szolgáltatási végpontokat engedélyezni kell mind a hálózatkezelési, mind az Azure-szolgáltatás számára, amelyeken engedélyezve vannak. A szolgáltatási végpontokat támogató Azure-szolgáltatások listáját itt tekintheti meg: [Virtual Network szolgáltatási végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).
>

![A hozzáférési korlátozások folyamatábrája.](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Hozzáférés-korlátozási szabályok hozzáadása vagy szerkesztése a portálon

Hozzáférés-korlátozási szabály az alkalmazáshoz való hozzáadásához tegye a következőket:

1. Jelentkezzen be az Azure Portalra.

1. A bal oldali ablaktáblán válassza a **hálózatkezelés** lehetőséget.

1. A **hálózat** ablaktábla **hozzáférési korlátozások** területén válassza a **hozzáférési korlátozások konfigurálása** lehetőséget.

   ![Képernyőkép a Azure Portal App Service hálózatkezelési beállítások panelről.](media/app-service-ip-restrictions/access-restrictions.png)  

1. A **hozzáférési korlátozások** lapon tekintse át az alkalmazáshoz definiált hozzáférés-korlátozási szabályok listáját.

   ![Képernyőkép a Azure Portal hozzáférési korlátozások oldaláról, amely a kiválasztott alkalmazáshoz definiált hozzáférés-korlátozási szabályok listáját jeleníti meg.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   A lista megjeleníti az alkalmazásra alkalmazott összes jelenlegi korlátozást. Ha az alkalmazáshoz virtuális hálózati korlátozás vonatkozik, a táblázat azt mutatja, hogy a szolgáltatási végpontok engedélyezve vannak-e a Microsoft. Web számára. Ha az alkalmazásban nincsenek korlátozások meghatározva, az alkalmazás bárhonnan elérhető.  

### <a name="add-an-access-restriction-rule"></a>Hozzáférés-korlátozási szabály hozzáadása

Ha hozzáférési korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, a **hozzáférési korlátozások** panelen válassza a **szabály hozzáadása** lehetőséget. A szabály hozzáadása után azonnal érvénybe lép. 

A szabályok érvényesítése prioritási sorrendben történik, a **prioritás** oszlopban szereplő legalacsonyabb számtól kezdve. Egy implicit *Megtagadás minden* esetben érvényben van, miután még egyetlen szabályt adott hozzá.

Az **IP-korlátozás hozzáadása** panelen hozzon létre egy szabályt, tegye a következőket:

1. A **művelet** területen válassza az **Engedélyezés** vagy a **Megtagadás** lehetőséget.  

   ![Képernyőkép az "IP-korlátozás hozzáadása" panelről.](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. Szükség esetén megadhatja a szabály nevét és leírását is.  
1. A **típus** legördülő listában válassza ki a szabály típusát.  
1. A **Priority (prioritás** ) mezőben adjon meg egy prioritási értéket.  
1. Az **előfizetés** , **Virtual Network** és **alhálózat** legördülő listában válassza ki, hogy mit szeretne korlátozni a hozzáférését.  

### <a name="set-an-ip-address-based-rule"></a>IP-cím alapú szabály beállítása

Kövesse az előző szakaszban ismertetett eljárást, de a következő változattal:
* A 3. lépésben a **típus** legördülő listában válassza az **IPv4** vagy az **IPv6** lehetőséget. 

Az IPv4-és IPv6-címek esetében az IP-címet az osztály nélküli Inter-Domain útválasztási (CIDR) jelöléssel határozza meg. Cím megadásához használhatja a *1.2.3.4/32* -et, ahol az első négy oktett az Ön IP-címét és */32* a maszkot jelöli. Az összes cím IPv4-CIDR jelölése 0.0.0.0/0. További információ a CIDR jelöléséről: [osztály nélküli Inter-Domain útválasztás](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="use-service-endpoints"></a>Szolgáltatásvégpontok használata

A szolgáltatási végpontok használatával korlátozhatja a hozzáférést a kiválasztott Azure-beli virtuális hálózati alhálózatokhoz. Egy adott alhálózathoz való hozzáférés korlátozásához hozzon létre egy korlátozási szabályt egy **Virtual Network** típussal. Ezután kiválaszthatja azt az előfizetést, virtuális hálózatot és alhálózatot, amely számára engedélyezni vagy megtagadni kívánja a hozzáférést. 

Ha a szolgáltatási végpontok még nincsenek engedélyezve a Microsoft. Web-ben a kiválasztott alhálózathoz, akkor automatikusan engedélyezve lesznek, kivéve, ha bejelöli a **hiányzó Microsoft. Web Service-végpontok figyelmen kívül hagyása** jelölőnégyzetet. A forgatókönyv, ahol előfordulhat, hogy engedélyezni szeretné a szolgáltatási végpontokat az alkalmazáson, de az alhálózat nem függ elsősorban attól, hogy rendelkezik-e az alhálózaton való engedélyezéséhez szükséges engedélyekkel. 

Ha valaki másnak kell engedélyeznie a szolgáltatási végpontokat az alhálózaton, jelölje be a **hiányzó Microsoft. webszolgáltatás-végpontok figyelmen kívül hagyása** jelölőnégyzetet. Az alkalmazás a szolgáltatás-végpontok számára lesz konfigurálva, hogy az alhálózaton később engedélyezzék őket. 

![Képernyőfelvétel: az "IP-korlátozás hozzáadása" panel a kiválasztott Virtual Network típussal.](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

A szolgáltatási végpontok nem használhatók a App Service Environmenton futó alkalmazásokhoz való hozzáférés korlátozására. Ha az alkalmazás egy App Service Environmentban van, akkor az IP-hozzáférési szabályok alkalmazásával szabályozhatja a hozzáférését. 

A szolgáltatási végpontok segítségével az alkalmazást Application Gateway vagy más webalkalmazási tűzfal (WAF) eszközzel konfigurálhatja. A többrétegű alkalmazásokat is konfigurálhatja biztonságos háttérbeli befejezéssel. További információ: [hálózati szolgáltatások és app Service](networking-features.md) és [Application Gateway integráció a szolgáltatás-végpontokkal](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - A szolgáltatási végpontok jelenleg nem támogatottak az IP-SSL (SSL) virtuális IP-címet (VIP) használó webalkalmazások esetén.
> - Az IP-cím vagy a szolgáltatás-végpont korlátozásai legfeljebb 512 sor. Ha több mint 512 sornyi korlátozásra van szüksége, javasoljuk, hogy egy önálló biztonsági termék telepítését, például az Azure bejárati ajtót, az Azure app Gatewayt vagy egy WAF kell telepítenie.
>

## <a name="manage-access-restriction-rules"></a>Hozzáférés-korlátozási szabályok kezelése

Szerkesztheti vagy törölheti a meglévő hozzáférés-korlátozási szabályt.

### <a name="edit-a-rule"></a>Szabály szerkesztése

1. Egy meglévő hozzáférés-korlátozási szabály szerkesztésének megkezdéséhez a **hozzáférési korlátozások** lapon kattintson duplán a szerkeszteni kívánt szabályra.

1. Az **IP-korlátozás szerkesztése** panelen végezze el a módosításokat, majd válassza a **szabály frissítése** lehetőséget. A módosítások azonnal érvénybe lépnek, beleértve a prioritási sorrend változásait is.

   ![Képernyőkép: "az IP-korlátozás szerkesztése" panel a Azure Portal, amely egy meglévő hozzáférés-korlátozási szabály mezőit jeleníti meg.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > Egy szabály szerkesztésekor nem válthat egy IP-cím szabály és egy virtuális hálózati szabály között. 

   ![Képernyőkép: az "IP-korlátozás szerkesztése" panel a Azure Portalban, amely a virtuális hálózati szabály beállításait mutatja.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Szabály törlése

Egy szabály törléséhez a **hozzáférési korlátozások** lapon válassza a törölni kívánt szabály melletti három pontot ( **...** ), majd válassza az **Eltávolítás** lehetőséget.

![Képernyőkép a "hozzáférési korlátozások" oldalról, amely a törölni kívánt hozzáférés-korlátozási szabály melletti "Eltávolítás" három pontot mutatja.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Egyetlen IP-cím blokkolása

Az első IP-korlátozási szabály hozzáadásakor a szolgáltatás hozzáad egy explicit *megtagadási* szabályt, amelynek prioritása 2147483647. A gyakorlatban az összes szabály explicit *megtagadása* a végső szabály, amely letiltja az *engedélyezési* szabályok által explicit módon nem engedélyezett IP-címekhez való hozzáférést.

Olyan forgatókönyv esetén, ahol explicit módon le szeretné tiltani egyetlen IP-címet vagy IP-címtartományt, de minden más számára engedélyezi a hozzáférést, adjon hozzá egy explicit *engedélyezett minden* szabályt.

![Képernyőfelvétel: a Azure Portal "hozzáférési korlátozások" lapja, amely egyetlen letiltott IP-címet mutat be.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>SCM-helyhez való hozzáférés korlátozása 

Az alkalmazáshoz való hozzáférés szabályozása mellett korlátozhatja az alkalmazás által használt SCM-helyhez való hozzáférést is. Az SCM-hely a webes üzembe helyezési végpont és a kudu-konzol is. Az SCM-helyhez külön hozzáférési korlátozásokat rendelhet az alkalmazáshoz, vagy az alkalmazásra és az SCM-helyre vonatkozó korlátozásokat is használhat. Ha **ugyanazokat a korlátozásokat \<app name>** jelöli be, mint a jelölőnégyzet, minden kimarad. Ha törli a jelölőnégyzet jelölését, az SCM-hely beállításai újra lesznek alkalmazva. 

![Képernyőkép a Azure Portal "hozzáférési korlátozások" oldaláról, amely azt mutatja, hogy az SCM-helyhez vagy az alkalmazáshoz nincs beállítva hozzáférési korlátozás.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Hozzáférés-korlátozási szabályok kezelése programozott módon

A következő módokon adhat hozzá hozzáférési korlátozásokat a programozott módon: 

* Használja [Az Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)-t. Például:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true)használata. Például:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

Az értékeket manuálisan is megadhatja a következők valamelyikével:

* Használjon [Azure REST API](/rest/api/azure/) Put műveletet a Azure Resource Manager alkalmazás-konfigurációjában. Az információk helye Azure Resource Manager a következő:

  management.azure.com/subscriptions/ **előfizetés** -azonosító **/resourceGroups//Providers/Microsoft.Web/Sites/****webalkalmazás neve** /config/web? API-Version = 2018-02-01

* ARM-sablon használata. Például használhatja a resources.azure.com, és szerkesztheti a ipSecurityRestrictions-blokkot a szükséges JSON hozzáadásához.

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

## <a name="set-up-azure-functions-access-restrictions"></a>Hozzáférési korlátozások beállítása Azure Functions

A hozzáférési korlátozások a App Service csomagokkal megegyező funkciókkal rendelkező Function apps-alkalmazásokhoz is elérhetők. Ha engedélyezi a hozzáférési korlátozásokat, a Azure Portal Kódszerkesztő is letiltható a nem engedélyezett IP-címekhez.

## <a name="next-steps"></a>Következő lépések
[Azure Functions hozzáférési korlátozásai](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integráció a szolgáltatási végpontokkal](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
