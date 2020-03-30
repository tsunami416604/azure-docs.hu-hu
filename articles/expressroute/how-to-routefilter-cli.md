---
title: 'ExpressRoute: Útvonalszűrők – Microsoft társviszony-létesítés:Azure CLI'
description: Ez a cikk bemutatja, hogyan konfigurálhatja az útvonalszűrőket a Microsoft társviszony-létesítéséhez az Azure CLI használatával
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033416"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Útvonalszűrők konfigurálása A Microsoft társviszony-létesítés: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-áramkörök útvonalszűrőit.

Az Office 365-szolgáltatások, például az Exchange Online, a SharePoint Online és a Skype Vállalati verzió a Microsoft társviszony-létesítésén keresztül érhetők el. Ha a Microsoft-társviszony-létesítés ExpressRoute-kapcsolati kapcsolatban van konfigurálva, az ezekhez a szolgáltatásokhoz kapcsolódó összes előtagot a létrehozott BGP-munkameneteken keresztül hirdeti a rendszer. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-közösség értékeinek és a hozzáuk uk leképezett szolgáltatásoknak a listáját a [BGP-közösségek című témakörben tetszésszerint.](expressroute-routing.md#bgp)

Ha az összes szolgáltatáshoz szüksége van, a BGP-n keresztül nagy számú előtagot hirdet a szolgáltatás. Ez jelentősen megnöveli a hálózaton belüli útválasztók által fenntartott útvonaltáblák méretét. Ha a Microsoft társviszony-létesítésen keresztül kínált szolgáltatásoknak csak egy részét kívánja felhasználni, kétféleképpen csökkentheti az útvonaltáblák méretét. A következőket teheti:

* A nem kívánt előtagok kiszűrése útvonalszűrők alkalmazásával a BGP-közösségeken. Ez egy szabványos hálózati gyakorlat, és gyakran használják számos hálózaton belül.

* Definiálja az útvonalszűrőket, és alkalmazza őket az ExpressRoute-kapcsolatra. Az útvonalszűrő egy új erőforrás, amely lehetővé teszi a Microsoft-társviszony-létesítés során használni kívánt szolgáltatások listájának kiválasztását. Az ExpressRoute-útválasztók csak az útvonalszűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

### <a name="about-route-filters"></a><a name="about"></a>Útvonalszűrők –

Ha a Microsoft társviszony-létesítése konfigurálva van az ExpressRoute-kapcsolatcsoporton, a Microsoft peremhálózati útválasztói bgp-munkameneteket hoznak létre a peremhálózati útválasztókkal (az Ön ével vagy a kapcsolatszolgáltatóéval). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez tulajdonképpen az összes BGP-közösségérték engedélyezési listája. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Ahhoz, hogy útvonalszűrőket csatolhassanon az Office 365-szolgáltatásokhoz, engedéllyel kell rendelkeznie ahhoz, hogy az ExpressRoute-on keresztül office 365-szolgáltatásokat használja. Ha nincs jogosult office 365-szolgáltatások felhasználására az ExpressRoute-on keresztül, az útvonalszűrők csatolására irányuló művelet sikertelen lesz. Az engedélyezési folyamatról további információt az [Office 365-alapú Azure ExpressRoute című témakörben talál.](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)

> [!IMPORTANT]
> 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése az összes szolgáltatáselőtagot a Microsoft társviszony-létesítésén keresztül hirdeti, még akkor is, ha nincsenek meghatározva az útvonalszűrők. 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ahhoz, hogy a Microsoft társviszony-létesítésével sikeresen kapcsolódhatna a szolgáltatásokhoz, a következő konfigurációs lépéseket kell végrehajtania:

* Olyan aktív ExpressRoute-kapcsolattal kell rendelkeznie, amelyhez a Microsoft társviszony-létesítése is van. Az alábbi utasításokat használhatja a következő feladatok elvégzéséhez:
  * [Hozzon létre egy ExpressRoute-áramkört,](howto-circuit-cli.md) és a folytatás előtt engedélyezve legyen a kapcsolatszolgáltató. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie.
  * [Hozzon létre Microsoft-társviszony-létesítést,](howto-routing-cli.md) ha közvetlenül kezeli a BGP-munkamenetet. Vagy a kapcsolatszolgáltató üzembe helyezheti a Microsoft-társviszony-létesítést a kapcsolatcsoporthoz.

* Létre kell hoznia és konfigurálnia kell egy útvonalszűrőt.
  * Azonosítsa azokat a szolgáltatásokat, amelyeket a Microsoft társviszony-létesítésével szeretne igénybe adni
  * A szolgáltatásokhoz társított BGP-közösségi értékek listájának azonosítása
  * Hozzon létre egy szabályt, amely engedélyezi a BGP-közösség értékeinek megfelelő előtaglistát

* Az útvonalszűrőt csatolnia kell az ExpressRoute-kapcsolathoz.

## <a name="before-you-begin"></a>Előkészületek

A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).

* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie.

* Aktív Microsoft-társviszony-létesítésre van szüksége. Kövesse a [társviszony-létesítési konfiguráció létrehozása és módosítása című](howto-routing-cli.md) útmutató utasításait

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést

A konfiguráció megkezdéséhez jelentkezzen be az Azure-fiókjába. Ha a "Try It" kifejezést használja, automatikusan bejelentkezik, és kihagyhatja a bejelentkezési lépést. A csatlakozáshoz az alábbi példák segítségével segítséget nyújt a csatlakozáshoz:

```azurecli
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli-interactive
az account list
```

Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört szeretne létrehozni.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>1. lépés: Előtagok és BGP-közösségi értékek listájának beszereznie

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP közösségi értékek listájának beszereznie

Az alábbi parancsmag segítségével leszeretné késni a Microsoft társviszony-létesítésén keresztül elérhető szolgáltatásokhoz társított BGP-közösségi értékek listáját, valamint a hozzájuk társított előtagok listáját:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Készíts egy listát a használni kívánt értékekről

Készítse el az útvonalszűrőben használni kívánt BGP-közösségi értékek listáját.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>2. lépés: Útvonalszűrő és szűrőszabály létrehozása

Az útvonalszűrőnek csak egy szabálya lehet, és a szabálynak "Engedélyezés" típusúnak kell lennie. Ez a szabály a BGP-közösség értékeit tartalmazó listát tartalmaz.

### <a name="1-create-a-route-filter"></a>1. Útvonalszűrő létrehozása

Először hozza létre az útvonalszűrőt. A `az network route-filter create` parancs csak útvonalszűrő-erőforrást hoz létre. Az erőforrás létrehozása után létre kell hoznia egy szabályt, és csatolnia kell az útvonalszűrő objektumhoz. Útvonalszűrő-erőforrás létrehozásához futtassa a következő parancsot:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Szűrőszabály létrehozása

Új szabály létrehozásához futtassa a következő parancsot:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>3. lépés: Az útvonalszűrő csatlakoztatása ExpressRoute-áramkörhöz

Futtassa a következő parancsot az útvonalszűrő ExpressRoute-kapcsolathoz való csatlakoztatásához:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Útvonalszűrő tulajdonságainak bekésezése

Az útvonalszűrő tulajdonságainak leválasztásához használja a következő parancsot:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Útvonalszűrő tulajdonságainak frissítése

Ha az útvonalszűrő már csatlakoztatva van egy kapcsolatcsoporthoz, a BGP-közösség listájának frissítései automatikusan propagálják a megfelelő előtag-hirdetésmódosításokat a létrehozott BGP-munkameneteken keresztül. Az útvonalszűrő BGP-közösséglistáját a következő paranccsal frissítheti:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonalszűrő leválasztása ExpressRoute-áramkörről

Miután egy útvonalszűrő levan választva az ExpressRoute-kapcsolatról, a BGP-munkameneten keresztül nem hirdetnek előtagokat. Az expressroute-áramkörökről a következő paranccsal választhat le útvonalszűrőt:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Útvonalszűrő törlése

Csak akkor törölheti az útvonalszűrőt, ha az nincs csatolva egyetlen kapcsolathoz sem. A törlés megkísérlése előtt győződjön meg arról, hogy az útvonalszűrő nincs csatolva egyetlen kapcsolatonként sem. Az útvonalszűrőa következő paranccsal törölhető:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
