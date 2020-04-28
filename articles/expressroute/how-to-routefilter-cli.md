---
title: 'ExpressRoute: útvonal-szűrők – Microsoft-partnerek: Azure CLI'
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Microsoft társközi útválasztási szűrői az Azure CLI használatával
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74033416"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Útválasztási szűrők konfigurálása a Microsoft-partnerek számára: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a ExpressRoute-áramkörök útválasztási szűrőinek konfigurálásához és kezeléséhez.

Az Office 365-szolgáltatások (például az Exchange Online, a SharePoint Online és a Skype vállalati verzió) a Microsoft-társon keresztül érhetők el. Ha a Microsoft-társítás egy ExpressRoute-áramkörben van konfigurálva, a szolgáltatásokhoz kapcsolódó összes előtagot a létrehozott BGP-munkamenetek hirdetik ki. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP közösségi értékek és a hozzájuk kapcsolódó szolgáltatások listáját itt tekintheti meg: [BGP-Közösségek](expressroute-routing.md#bgp).

Ha minden szolgáltatáshoz csatlakoznia kell, nagy számú előtagokat a BGP-n keresztül hirdetünk meg. Ez jelentősen növeli a hálózaton belüli útválasztók által karbantartott útválasztási táblázatok méretét. Ha azt tervezi, hogy csak a Microsoft-partnerek által kínált szolgáltatások egy részhalmazát szeretné használni, az útválasztási táblázatok méretét kétféleképpen csökkentheti. A következőket teheti:

* Kiszűri a nemkívánatos előtagokat az útválasztási szűrők alkalmazásával a BGP-közösségeken. Ez egy szabványos hálózatkezelési gyakorlat, és általában számos hálózaton belül használatos.

* Adja meg az útvonal-szűrőket, és alkalmazza őket a ExpressRoute-áramkörre. Az útválasztási szűrő egy új erőforrás, amellyel kiválaszthatja a Microsoft-társon keresztül használni kívánt szolgáltatások listáját. A ExpressRoute-útválasztók csak az útválasztási szűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

### <a name="about-route-filters"></a><a name="about"></a>Az útválasztási szűrők ismertetése

Ha a Microsoft-társak konfigurálása a ExpressRoute-áramkörön történik, a Microsoft Edge útválasztók egy pár BGP-munkamenetet létesítenek a peremhálózati útválasztókkal (a tiéd vagy a kapcsolat szolgáltatója). Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez tulajdonképpen az összes BGP-közösségérték engedélyezési listája. Miután meghatározott és egy ExpressRoute-kapcsolatcsoporthoz csatolt egy útvonalszűrő erőforrást, a BGP-közösségértékekhez rendelt összes előtag meg van hirdetve a hálózaton.

Ahhoz, hogy az Office 365-szolgáltatásokkal csatolja az útválasztási szűrőket, rendelkeznie kell az Office 365-szolgáltatások ExpressRoute-en keresztüli használatára vonatkozó engedéllyel. Ha nincs engedélye arra, hogy az Office 365-szolgáltatásokat ExpressRoute-en keresztül használja, az útválasztási szűrők csatlakoztatásának művelete meghiúsul. Az engedélyezési folyamattal kapcsolatos további információkért lásd: [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ahhoz, hogy sikeresen tudjon csatlakozni a szolgáltatásokhoz a Microsoft-társon keresztül, el kell végeznie az alábbi konfigurációs lépéseket:

* Rendelkeznie kell egy aktív ExpressRoute-áramkörrel, amelyhez Microsoft-társítás van kiépítve. A következő utasításokat követve hajthatja végre ezeket a feladatokat:
  * A folytatás előtt [hozzon létre egy ExpressRoute áramkört](howto-circuit-cli.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.
  * [Hozzon létre Microsoft-társat](howto-routing-cli.md) , ha közvetlenül a BGP-munkamenetet kezeli. Vagy a kapcsolat szolgáltatója kiépíti a Microsoft-társat az áramkörhöz.

* Létre kell hoznia és konfigurálnia kell egy útvonal-szűrőt.
  * Azonosítsa a Microsoft-társon keresztül használni kívánt szolgáltatásokat
  * A szolgáltatásokhoz társított BGP-közösségi értékek listájának azonosítása
  * Szabály létrehozása a BGP-közösségi értékeknek megfelelő előtag-lista engedélyezéséhez

* Az ExpressRoute áramkörhöz csatolni kell az útvonal-szűrőt.

## <a name="before-you-begin"></a>Előkészületek

A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.

* Aktív Microsoft-társat kell biztosítania. Kövesse a társítási [konfiguráció létrehozása és módosítása](howto-routing-cli.md) című témakör utasításait

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését

A konfiguráció megkezdéséhez jelentkezzen be az Azure-fiókjába. Ha a "kipróbálás" lehetőséget használja, automatikusan bejelentkezett, és kihagyhatja a bejelentkezési lépést. Az alábbi példák segítséget nyújtanak a kapcsolódáshoz:

```azurecli
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli-interactive
az account list
```

Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört kíván létrehozni.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>1. lépés: az előtagok és a BGP közösségi értékek listájának lekérése

### <a name="1-get-a-list-of-bgp-community-values"></a>1. a BGP-közösségi értékek listájának beolvasása

A következő parancsmaggal kérheti le a Microsoft-társon keresztül elérhető szolgáltatásokhoz kapcsolódó BGP-közösségi értékek listáját, valamint a hozzájuk rendelt előtagok listáját:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. a használni kívánt értékek listájának létrehozása

Készítse el az útválasztási szűrőben használni kívánt BGP-közösségi értékek listáját.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>2. lépés: útvonal-szűrő és szűrő-szabály létrehozása

Egy útvonal-szűrőnek csak egy szabálya lehet, és a szabálynak "Allow" típusúnak kell lennie. Ez a szabály tartalmazhatja a BGP-közösségi értékek listáját.

### <a name="1-create-a-route-filter"></a>1. útvonal-szűrő létrehozása

Először hozza létre az útvonal-szűrőt. A parancs `az network route-filter create` csak egy útvonal-szűrő erőforrást hoz létre. Az erőforrás létrehozása után létre kell hoznia egy szabályt, és csatolni kell az útválasztási szűrő objektumhoz. Futtassa az alábbi parancsot egy útvonal-szűrő erőforrás létrehozásához:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. szűrési szabály létrehozása

Új szabály létrehozásához futtassa a következő parancsot:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>3. lépés: az útvonal-szűrő csatolása egy ExpressRoute-áramkörhöz

Futtassa az alábbi parancsot az útvonal-szűrő ExpressRoute-áramkörhöz való csatolásához:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Egy útvonal szűrő tulajdonságainak beolvasása

Az útválasztási szűrők tulajdonságainak beszerzéséhez használja a következő parancsot:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Egy útvonal szűrő tulajdonságainak frissítése

Ha az útválasztási szűrő már csatolva van egy áramkörhöz, a BGP-közösségi lista frissítései automatikusan propagálják a megfelelő előtag-hirdetményeket a létrejött BGP-munkameneteken keresztül. A következő paranccsal frissítheti az útválasztási szűrő BGP közösségi listáját:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonal-szűrő leválasztása egy ExpressRoute-áramkörből

Ha egy útvonal-szűrőt leválasztanak a ExpressRoute áramkörről, a BGP-munkameneten belül nem történik meg az előtagok meghirdetése. A következő parancs használatával leválaszthat egy ExpressRoute áramkörből:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Útvonal-szűrő törlése

Csak akkor törölhet egy útvonal-szűrőt, ha az nincs csatlakoztatva egyetlen áramkörhöz sem. A törlés megkísérlése előtt gondoskodjon arról, hogy az útválasztási szűrő ne legyen csatlakoztatva egyetlen áramkörhöz sem. Az útvonal-szűrőt a következő paranccsal törölheti:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
