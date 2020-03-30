---
title: PowerShell-parancsfájlok az Az.Search modul használatával
titleSuffix: Azure Cognitive Search
description: Hozzon létre és konfiguráljon egy Azure Cognitive Search szolgáltatást a PowerShell használatával. A szolgáltatások fel- vagy leskálázhatók, kezelhetik a rendszergazdai és lekérdezési API-kulcsokat, és lekérdezheti konklúziók at.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209296"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Az Azure Cognitive Search szolgáltatás kezelése a PowerShell segítségével
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

PowerShell-parancsmagokat és parancsfájlokat futtathat Windows, Linux vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) rendszeren az Azure Cognitive Search létrehozásához és konfigurálásához. Az **Az.Search** modul teljes parititussal bővíti az [Azure PowerShellt](https://docs.microsoft.com/powershell/) a [Search Management REST API-kra,](https://docs.microsoft.com/rest/api/searchmanagement) és képes elvégezni a következő feladatokat:

> [!div class="checklist"]
> * [Keresési szolgáltatások listázása előfizetésben](#list-search-services)
> * [Visszáru-szolgáltatás adatai](#get-search-service-information)
> * [Szolgáltatás létrehozása vagy törlése](#create-or-delete-a-service)
> * [Felügyeleti API-kulcsok újragenerálása](#regenerate-admin-keys)
> * [Lekérdezési API-kulcsok létrehozása vagy törlése](#create-or-delete-query-keys)
> * [Fel- és leskálázás replikákkal és partíciókkal](#scale-replicas-and-partitions)

Alkalmanként kérdéseket tesznek fel olyan feladatokról, amelyek *nem* szerepelnek a fenti listán. Jelenleg nem használhatja sem az **Az.Search** modult, sem a felügyeleti REST API-t a kiszolgáló nevének, régiójának vagy rétegének módosításához. A dedikált erőforrások foglalása a szolgáltatás létrehozásakor történik. Így az alapul szolgáló hardver (hely vagy csomópont típus) módosítása új szolgáltatást igényel. Hasonlóképpen nincsenek olyan eszközök vagy API-k, amelyek a tartalom, például az index egyik szolgáltatásból a másikba történő átvitelére szolgálnak.

A szolgáltatáson belül a tartalom létrehozása és kezelése a [Search Service REST API-n](https://docs.microsoft.com/rest/api/searchservice/) vagy a [.NET SDK-n](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)keresztül történik. Bár nincsenek dedikált PowerShell-parancsok a tartalomhoz, írhat Olyan PowerShell-parancsfájlt, amely meghívja a REST vagy a .NET API-kat az indexek létrehozásához és betöltéséhez.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verziók és betöltési modulok ellenőrzése

A jelen cikkben szereplő példák interaktívak, és emelt szintű engedélyeket igényelnek. Az Azure PowerShellt (az **Az** modult) telepíteni kell. További információ: [Telepítse az Azure PowerShellt.](/powershell/azure/overview)

### <a name="powershell-version-check-51-or-later"></a>PowerShell-verzióellenőrzés (5.1-es vagy újabb verzió)

A helyi PowerShellnek 5.1-es vagy újabb verziónak kell lennie bármely támogatott operációs rendszeren.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Az Azure PowerShell betöltése

Ha nem biztos abban, hogy az **Az** telepítve van-e, futtassa a következő parancsot ellenőrzési lépésként. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Egyes rendszerek nem töltik be automatikusan a modulokat. Ha hibaüzenetet kap az előző parancsban, próbálja meg betölteni a modult, és ha ez nem sikerül, lépjen vissza a telepítési utasításokhoz, és nézze meg, hogy kihagyott-e egy lépést.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Csatlakozás az Azure-hoz egy böngészőbejelentkezési jogkivonattal

A portál bejelentkezési hitelesítő adataival csatlakozhat egy előfizetéshez a PowerShellben. Másik lehetőségként [hitelesítheti a nem interaktív szolgáltatást egy egyszerű szolgáltatással.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

```azurepowershell-interactive
Connect-AzAccount
```

Ha több Azure-előfizetéssel rendelkezik, állítsa be az Azure-előfizetést. Az aktuális előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Az előfizetés megadásához futtassa a következő parancsot. A következő példában az `ContosoSubscription`előfizetés neve .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Szolgáltatások listázása előfizetésben

A következő parancsok az [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)szolgáltatásból származnak, és az előfizetésben már kiépített meglévő erőforrásokra és szolgáltatásokra vonatkozó információkat adják vissza. Ha nem tudja, hogy hány keresési szolgáltatás már létre, ezek a parancsok vissza ezt az információt, így egy utat a portálra.

Az első parancs az összes keresési szolgáltatást visszaadja.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

A szolgáltatások listájából adja vissza egy adott erőforrás adatait.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Az eredményeknek a következő kimenethez hasonlóan kell kinézniük.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Az.Keresés importálása

Az [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) parancsai csak a modul betöltése után érhetők el.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Az összes Az.Search parancs listázása

Ellenőrzési lépésként adja vissza a modulban biztosított parancsok listáját.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Az eredményeknek a következő kimenethez hasonlóan kell kinézniük.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Keresési szolgáltatás adatainak beszereznie

**Az.Search** importálása után, és ismeri a keresési szolgáltatást tartalmazó erőforráscsoportot, futtassa a [Get-AzSearchService szolgáltatást](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) a szolgáltatásdefiníció visszaadásához, beleértve a nevet, a régiót, a réteget, valamint a replika- és partíciószámokat.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Az eredményeknek a következő kimenethez hasonlóan kell kinézniük.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Szolgáltatás létrehozása vagy törlése

[**A New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) [új keresési szolgáltatás létrehozására szolgál.](search-create-service-portal.md)

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Az eredményeknek a következő kimenethez hasonlóan kell kinézniük.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Felügyeleti kulcsok újragenerálása

[**Az Új-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) a rendszergazdai [API-kulcsok átütemezéséhez](search-security-api-keys.md)szolgál. Minden szolgáltatáshoz két rendszergazdai kulcs jön létre a hitelesített hozzáféréshez. Minden kéréshez kulcsok szükségesek. Mindkét rendszergazdai kulcs funkcionálisan egyenértékű, teljes írási hozzáférést biztosít vaegy keresési szolgáltatáshoz, amely lehetővé teszi bármely információ lekérését, illetve bármely objektum létrehozását és törlését. Két kulcs létezik, így az egyiket használhatja a másik cseréje közben. 

Egyszerre csak egy újragenerálható, `primary` vagy `secondary` kulcsként megadva. A megszakítás nélküli szolgáltatás esetén ne felejtse el frissíteni az összes ügyfélkódot, hogy másodlagos kulcsot használjon az elsődleges kulcs átgördülése közben. Kerülje a kulcsok cseréjét, amíg a műveletek repülés közben vannak.

Ahogy az várható, ha az ügyfélkód frissítése nélkül újragenerálja a kulcsokat, a régi kulcsot használó kérések sikertelenek lesznek. Az összes új kulcs újragenerálása nem zárja ki véglegesen a szolgáltatásból, és továbbra is elérheti a szolgáltatást a portálon keresztül. Az elsődleges és másodlagos kulcsok újragenerálása után frissítheti az ügyfélkódot az új kulcsok használatához, és a műveletek ennek megfelelően folytatódnak.

Az API-kulcsok értékeit a szolgáltatás hozza létre. Nem adhat egyéni kulcsot az Azure Cognitive Search használatához. Hasonlóképpen nincs felhasználó által definiált név a rendszergazdai API-kulcsok. A kulcsra való hivatkozások rögzített `primary` karakterláncok, vagy vagy `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Az eredményeknek a következő kimenethez hasonlóan kell kinézniük. Mindkét kulcs vissza, annak ellenére, hogy csak egy alkalommal módosítja.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Lekérdezéskulcsok létrehozása és törlése

[**A New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) [lekérdezési API-kulcsok](search-security-api-keys.md) létrehozására szolgál az ügyfélalkalmazásokból az Azure Cognitive Search indexhez való írásvédett hozzáféréshez. A lekérdezési kulcsok egy adott index hitelesítésére szolgálnak a keresési eredmények beolvasása céljából. A lekérdezési kulcsok nem biztosítanak írásvédett hozzáférést a szolgáltatás más elemeihez, például indexhez, adatforráshoz vagy indexelőhöz.

Nem adhat kulcsot az Azure Cognitive Search használni. Az API-kulcsokat a szolgáltatás hozza létre.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Replikák és partíciók méretezése

[**A Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) a [replikák és partíciók növelésére vagy csökkentésére](search-capacity-planning.md) szolgál a szolgáltatáson belüli számlázható erőforrások újrajátszásához. A replikák vagy partíciók növelése növeli a számlát, amely rögzített és változó díjakat is. Ha ideiglenesen további feldolgozási teljesítményre van szüksége, növelheti a replikák at és partíciókat a számítási feladatok kezeléséhez. A figyelési terület az áttekintésportál lapon csempék lekérdezési késés, lekérdezések másodpercenként és szabályozás, jelezve, hogy az aktuális kapacitás megfelelő.

Eltarthat egy ideig, amíg hozzáadja vagy eltávolítja az erőforrásokat. A kapacitás módosítása a háttérben történik, lehetővé téve a meglévő munkaterhelések folytatását. További kapacitást használ a bejövő kérelmek, amint készen áll, további konfiguráció nélkül. 

A kapacitás eltávolítása zavaró lehet. Az összes indexelési és indexelő feladat leállítása a kapacitás csökkentése előtt ajánlott elkerülni az eldobott kérelmeket. Ha ez nem valósítható meg, érdemes lehet a kapacitás csökkentése növekményesen, egy replika és partíció egy időben, amíg az új célszintet ér el.

Miután elküldte a parancsot, nincs mód arra, hogy félúton megszüntessék. A számlálás okának felülvizsgálata előtt meg kell várnia a parancs befejezését.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Az eredményeknek a következő kimenethez hasonlóan kell kinézniük.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>További lépések

Index [létrehozása](search-what-is-an-index.md), [index lekérdezése](search-query-overview.md) a portál, REST API-k vagy a .NET SDK használatával.

* [Azure Cognitive Search index létrehozása az Azure Portalon](search-create-index-portal.md)
* [Indexelő beállítása más szolgáltatásokból származó adatok betöltéséhez](search-indexer-overview.md)
* [Azure Cognitive Search-index lekérdezése az Azure Portal on Search Explorer használatával](search-explorer.md)
* [Az Azure Cognitive Search használata a .NET-ben](search-howto-dotnet-sdk.md)