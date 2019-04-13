---
title: 'Microsoft - társviszony-létesítés útvonalszűrőinek konfigurálása ExpressRoute: Az Azure CLI |} A Microsoft Docs'
description: Ez a cikk ismerteti az Azure parancssori felület használatával a Microsoft Peering útvonalszűrők konfigurálása
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: cfd9f4c52d3ddddd944186a833cba48e6ca76182
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527965"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Microsoft társviszony-létesítés útvonalszűrőinek konfigurálása: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. A jelen cikkben ismertetett lépések segítségével konfigurálhatja és kezelheti az ExpressRoute-Kapcsolatcsoportok útvonalszűrőinek.

Dynamics 365-szolgáltatások és az Office 365-szolgáltatások például az Exchange Online, SharePoint Online és Skype for Business, a Microsoft társviszony-létesítésen keresztül érhetők el. Az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítés konfigurálásakor a ezekhez a szolgáltatásokhoz kapcsolódó összes előtagokat hirdet meg a BGP-munkamenetek létesített keresztül. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP-Közösség értékét, és a szolgáltatások leképezik a listáját lásd: [BGP-Közösségek](expressroute-routing.md#bgp).

Minden szolgáltatásokhoz való kapcsolódás van szükség, ha az előtagok sok BGP-n keresztüli hirdesse meg. Ez jelentősen növeli az útválasztási táblázatokat a hálózaton belüli útválasztók által fenntartott méretét. Ha azt tervezi, a Microsoft társviszony-létesítés keresztül felajánlott szolgáltatásokhoz csak egy részhalmazát felhasználását, csökkentheti az útválasztási táblázatban kétféleképpen méretét. A következőket teheti:

* A BGP-Közösségek útvonalszűrők alkalmazásával szűrése nemkívánatos előtagokat ki. Ez egy szabványos hálózatkezelési eljárás és belül túl sok hálózathoz gyakran használják.

* Útvonal szűrőket határozhat meg, és az ExpressRoute-kapcsolatcsoport alkalmazza őket. Egy útvonalszűrőhöz egy új erőforrást, amely lehetővé teszi szolgáltatások tervez használni a Microsoft társviszony-létesítésen keresztül listájában válassza ki. Csak az ExpressRoute útválasztók továbbítják az útvonalszűrőt szerepelt a szolgáltatásokhoz tartozó előtaglistát.

### <a name="about"></a>Útvonalszűrők kapcsolatban

Ha az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítés van konfigurálva, a Microsoft peremhálózati útválasztói létesíteni két BGP-munkamenetet a peremhálózati útválasztóhoz (Öné vagy a kapcsolatszolgáltató). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez tulajdonképpen az összes BGP-közösségérték engedélyezési listája. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Rendeljen hozzá útvonalszűrőket az Office 365-szolgáltatások rajtuk legyen, engedélyezési felhasználásához az Office 365 szolgáltatás expressroute-on keresztül kell rendelkeznie. Ha nem jogosult az Office 365 szolgáltatás expressroute-on keresztül felhasználásához, rendeljen hozzá útvonalszűrőket a művelet sikertelen lesz. Az engedélyezési folyamat kapcsolatos további információkért lásd: [Office 365-höz készült Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Dynamics 365-szolgáltatásokhoz való kapcsolódás nem igényel előzetes engedélyek.

> [!IMPORTANT]
> Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni. Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve.
> 
> 

### <a name="workflow"></a>A munkafolyamat

Az, hogy sikeresen csatlakozni a Microsoft társviszony-létesítés keresztül, az alábbi konfigurációs lépéseket kell elvégeznie:

* Aktív ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítést kiépített rendelkező kell rendelkeznie. Az alábbi utasítások segítségével a fenti feladatok elvégzéséhez:
  * [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) , és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt a folytatáshoz. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell lennie.
  * [A Microsoft társviszony-létesítés](howto-routing-cli.md) kezeléséhez közvetlenül a BGP-munkamenetben. Vagy a kapcsolatszolgáltató kell kiépíteni a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.

* Hozzon létre és egy útvonalszűrőhöz konfigurálnia kell.
  * A szolgáltatás azonosítására, és felhasználásához a Microsoft társviszony-létesítésen keresztül
  * Azonosítsa a BGP-Közösség értékét vett szolgáltatások listája
  * Hozzon létre egy szabályt, hogy az előtagok listáját a BGP-Közösség értékét megfelelő

* Az ExpressRoute-kapcsolatcsoport az útvonalszűrőt kell csatolnia.

## <a name="before-you-begin"></a>Előkészületek

A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell lennie.

* Rendelkeznie kell egy aktív Microsoft társviszony-létesítés. Kövesse az utasításokat, [létrehozása és a társviszony-létesítési konfigurációjának módosítása](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjával, és válassza ki az előfizetését

A konfiguráció első lépésként jelentkezzen be az Azure-fiókjával. Ha használja a "Kipróbálom", automatikusan van bejelentkezve, és a bejelentkezés a lépést kihagyhatja. Az alábbi példák segítségével segíthet a kapcsolódásban:

```azurecli
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli-interactive
az account list
```

Válassza ki az előfizetést, amelynek meg szeretné ExpressRoute-kapcsolatcsoport létrehozása.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>1. lépés: Előtagok és BGP-Közösség értékét listájának lekérése

### <a name="1-get-a-list-of-bgp-community-values"></a>1. A BGP-Közösség értékét tartalmazó lista beolvasása

A BGP-Közösség értékét társított szolgáltatások, a Microsoft társviszony-létesítésekhez listáját, és a hozzájuk társított előtaglistát beolvasásához használja a következő parancsmagot:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Győződjön meg a használni kívánt értékek listáját

Ellenőrizze a BGP-Közösség értékét az útvonalszűrőt használni kívánt listáját. Tegyük fel a Dynamics 365-szolgáltatásokhoz a BGP-közösségérték 12076:5040.

## <a name="filter"></a>2. lépés: Egy útvonalszűrőhöz és a egy Állapotszűrő szabály létrehozása

Egy útvonalszűrőhöz lehet csak egy szabályt, és a szabály "Engedélyezés" típusúnak kell lennie. Ez a szabály is van egy listája azokról a BGP-Közösség értékét társítva.

### <a name="1-create-a-route-filter"></a>1. Hozzon létre egy útvonalszűrőhöz

Először hozza létre az útvonalszűrőt. A parancs `az network route-filter create` csak az útvonal szűrő erőforrást hoz létre. Az erőforrás létrehozása után kell majd hozzon létre egy szabályt és csatlakoztassa azt az útvonalat szűrő objektum. Futtassa a következő parancsot egy útvonal-szűrő erőforrás létrehozásához:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Szűrési szabály létrehozása

Futtassa a következő parancsot egy új szabály létrehozása:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>3. lépés: Az útvonalszűrőt csatlakoztatása egy ExpressRoute-kapcsolatcsoporttal

Futtassa a következő parancsot az útvonalszűrőt csatolása az ExpressRoute-kapcsolatcsoport:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Gyakori feladatok

### <a name="getproperties"></a>Hogy egy útvonalszűrőhöz tulajdonságainak beolvasása

Egy útvonalszűrőhöz tulajdonságainak lekéréséhez használja a következő parancsot:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Egy útvonalszűrőhöz tulajdonságainak frissítése

Az útvonalszűrőt már csatolva van egy kapcsolatcsoporthoz, ha a BGP-Közösség listához frissítések automatikusan propagálása a létesített BGP-munkamenetek a megfelelő előtaggal hirdetmény változásoknak. A BGP közösségi listája az útvonalszűrőt, a következő paranccsal frissítheti:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Egy útvonalszűrőhöz az ExpressRoute-kapcsolatcsoport leválasztása

Miután egy útvonalszűrőhöz az ExpressRoute-kapcsolatcsoport le van választva, nincs előtagokat hirdet meg a BGP-munkameneten keresztül. Az ExpressRoute-kapcsolatcsoport a következő parancsot egy útvonalszűrőhöz leválaszthatja:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Egy útvonalszűrőhöz törlése

Egy útvonalszűrőhöz csak törölheti, ha bármely kapcsolatcsoporthoz nincs csatolva. Győződjön meg arról, hogy az útvonalszűrőt nincs csatolva bármely kapcsolatcsoport azt törlése megkísérlése előtt. Egy útvonalszűrőhöz, a következő paranccsal törölheti:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
