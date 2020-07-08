---
title: PowerShell-parancsfájlok az az. Search modul használatával
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search szolgáltatás létrehozása és konfigurálása a PowerShell-lel. A szolgáltatás fel-vagy leskálázása, a rendszergazdák és a lekérdezési API-kulcsok kezelése, valamint a rendszerinformációk lekérdezése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77209296"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Azure Cognitive Search szolgáltatás kezelése a PowerShell-lel
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Cognitive Search létrehozásához és konfigurálásához Windows, Linux vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) PowerShell-parancsmagokat és-parancsfájlokat is futtathat. Az az **. Search** modul a [keresési felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement) -k teljes paritásával bővíti a [Azure PowerShellt](https://docs.microsoft.com/powershell/) , és képes a következő feladatok végrehajtására:

> [!div class="checklist"]
> * [Előfizetés keresési szolgáltatásainak listázása](#list-search-services)
> * [Szolgáltatás adatainak visszaküldése](#get-search-service-information)
> * [Szolgáltatás létrehozása vagy törlése](#create-or-delete-a-service)
> * [Felügyeleti API-kulcsok újragenerálása](#regenerate-admin-keys)
> * [Lekérdezési API-kulcsok létrehozása vagy törlése](#create-or-delete-query-keys)
> * [Vertikális fel-vagy leskálázás replikákkal és partíciókkal](#scale-replicas-and-partitions)

Alkalmanként a fenti listán *nem* szereplő feladatokkal kapcsolatos kérdéseket is fel kell kérni. Jelenleg nem használható az az **. Search** modul vagy a felügyeleti REST API a kiszolgálónév, régió vagy rétegek módosításához. A dedikált erőforrások a szolgáltatás létrehozásakor vannak lefoglalva. Ennek megfelelően a mögöttes hardver (hely vagy csomópont típusa) módosítása új szolgáltatást igényel. Hasonlóképpen nincsenek olyan eszközök vagy API-k, amelyek a tartalmak, például indexek továbbítására szolgálnak az egyik szolgáltatásból a másikba.

A szolgáltatáson belül a tartalom létrehozása és kezelése [Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/) vagy [.net SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)-n keresztül történik. Noha nincsenek a tartalomhoz dedikált PowerShell-parancsok, olyan PowerShell-parancsfájlt írhat, amely REST vagy .NET API-kat hív meg indexek létrehozásához és betöltéséhez.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verziók és betöltési modulok keresése

A cikkben szereplő példák interaktívak, és emelt szintű engedélyeket igényelnek. Azure PowerShell **(az az modul)** telepíteni kell. További információ: [Install Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>PowerShell-verzió-ellenőrzési (5,1 vagy újabb)

A helyi PowerShellnek 5,1 vagy újabb verziójúnak kell lennie bármely támogatott operációs rendszeren.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Betöltés Azure PowerShell

Ha nem tudja biztosan, **hogy az az telepítve** van-e, futtassa a következő parancsot ellenőrzési lépésként. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Néhány rendszer nem tölti ki automatikusan a modulokat. Ha hibaüzenet jelenik meg az előző parancsban, próbálja meg betölteni a modult, és ha ez nem sikerül, lépjen vissza a telepítési utasításokba, és ellenőrizze, hogy kihagyott-e egy lépést.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Kapcsolódás az Azure-hoz egy böngésző bejelentkezési jogkivonattal

A portál bejelentkezési hitelesítő adatait használhatja a PowerShell-előfizetéshez való kapcsolódáshoz. Azt is megteheti, hogy [nem interaktív módon tud hitelesítést végezni egy egyszerű szolgáltatással](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Ha több Azure-előfizetéssel rendelkezik, állítsa be az Azure-előfizetését. A jelenlegi előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Az előfizetés megadásához futtassa a következő parancsot. A következő példában az előfizetés neve: `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Előfizetéshez tartozó szolgáltatások listázása

A következő parancsok az [**az. Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), az előfizetésben már üzembe helyezendő meglévő erőforrásokkal és szolgáltatásokkal kapcsolatos információkat adnak vissza. Ha nem tudja, hogy hány keresési szolgáltatás van már létrehozva, ezek a parancsok visszaküldik ezt az információt, és megmenti a portálon.

Az első parancs visszaadja az összes keresési szolgáltatást.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

A szolgáltatások listájából egy adott erőforrásra vonatkozó adatokat ad vissza.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importálás az. Search

Az [**az. Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) utasításai nem érhetők el, amíg be nem tölti a modult.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Az összes. keresési parancs listázása

Ellenőrzési lépésként a modulban megadott parancsok listáját kell visszaadnia.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

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

## <a name="get-search-service-information"></a>Keresési szolgáltatás adatainak beolvasása

Az **az. Search** importálása után, és ismeri a keresési szolgáltatást tartalmazó erőforráscsoportot, a [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) futtatásával adja vissza a szolgáltatás definícióját, beleértve a nevet, a régiót, a szintet, valamint a replika és a partíciók számát.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

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

A [**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) [új keresési szolgáltatás létrehozásához](search-create-service-portal.md)használatos.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

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

## <a name="regenerate-admin-keys"></a>Rendszergazdai kulcsok újragenerálása

A [**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) a felügyeleti [API-kulcsok](search-security-api-keys.md)átadására szolgál. A hitelesített hozzáféréshez a szolgáltatás két rendszergazdai kulcsot hoz létre. Minden kérelemhez kulcsokra van szükség. Mindkét rendszergazdai kulcs funkcionálisan egyenértékű, és teljes írási hozzáférést biztosít egy keresési szolgáltatáshoz bármilyen információ lekéréséhez, illetve bármely objektum létrehozásához és törléséhez. Két kulcs létezik, hogy a másikat cserélje le. 

Egyszerre csak egyszer lehet újradefiniálni, a `primary` vagy a `secondary` kulcsként megadva. A nem folytonos szolgáltatás esetében ne felejtse el frissíteni az összes állapotkódot, hogy másodlagos kulcsot használjon, miközben az elsődleges kulcsra mutat. Ne változtassa meg a kulcsokat, amíg a műveletek bekerülnek a repülésbe.

Ahogy várható, ha az ügyfél kódjának frissítése nélkül újragenerálja a kulcsokat, a régi kulcsot használó kérelmek sikertelenek lesznek. Az összes új kulcs újragenerálása nem zárja ki véglegesen a szolgáltatást, és a portálon keresztül továbbra is hozzáférhet a szolgáltatáshoz. Az elsődleges és a másodlagos kulcsok újragenerálása után frissítheti az ügyfélszoftvert az új kulcsok használatára, és ennek megfelelően folytathatja a műveletet.

Az API-kulcsok értékeit a szolgáltatás hozza létre. Nem adhat meg egyéni kulcsot az Azure Cognitive Search használatához. Hasonlóképpen, a rendszergazda API-kulcsok nem rendelkeznek felhasználó által definiált névvel. A kulcsra mutató hivatkozások rögzített karakterláncok, `primary` vagy vagy `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie. Mindkét kulcsot a rendszer akkor is visszaadja, ha egyszerre csak egyszer módosítja.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Lekérdezési kulcsok létrehozása vagy törlése

A [**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) használatával lekérdezési [API-kulcsokat](search-security-api-keys.md) hozhat létre a csak olvasási hozzáféréshez az ügyfélalkalmazások és egy Azure Cognitive Search index között. A lekérdezési kulcsok egy adott indexre való hitelesítésre szolgálnak a keresési eredmények beolvasása céljából. A lekérdezési kulcsok nem biztosítanak csak olvasási hozzáférést a szolgáltatás más elemeihez, például az indexhez, az adatforráshoz vagy az indexelő.

Nem adhat meg kulcsot az Azure Cognitive Search használatához. Az API-kulcsokat a szolgáltatás hozza létre.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Replikák és partíciók skálázása

A [**set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) használatával [megnövelhető vagy csökkenthető a replikák és partíciók](search-capacity-planning.md) a szolgáltatásban lévő számlázható erőforrások újraigazítása érdekében. Egyre több replika vagy partíció járul hozzá a számlához, amely rögzített és változó díjakat is tartalmaz. Ha átmenetileg további feldolgozási teljesítményre van szüksége, növelheti a replikákat és a partíciókat a munkaterhelés kezeléséhez. Az áttekintő portál figyelés területén található csempék a lekérdezési késéssel, a másodpercenkénti lekérdezésekkel és a szabályozással kapcsolatban, és jelzi, hogy az aktuális kapacitás megfelelő-e.

Eltarthat egy darabig a reforrások hozzáadásához vagy eltávolításához is. A kapacitás módosítása a háttérben történik, ami lehetővé teszi a meglévő munkaterhelések folytatását. A beérkező kérésekhez további kapacitást kell használni, amint az készen áll, és nincs szükség további konfigurálásra. 

A kapacitás eltávolítása zavaró lehet. Az eldobott kérések elkerülése érdekében ajánlott az összes indexelő és indexelő feladat leállítása a kapacitás csökkentése előtt. Ha ez nem lehetséges, érdemes lehet fokozatosan csökkenteni a kapacitást, egy replikát és partíciót egyszerre, amíg el nem éri az új célként megadott szintet.

A parancs elküldése után nem lehet lemondani félúton. A számok módosítása előtt várnia kell, amíg a parancs be nem fejeződik.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

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

Hozzon létre [egy indexet, és](search-what-is-an-index.md) [Kérdezzen le egy indexet](search-query-overview.md) a portál, a REST API-k vagy a .net SDK használatával.

* [Hozzon létre egy Azure Cognitive Search indexet a Azure Portal](search-create-index-portal.md)
* [Indexelő beállítása az adatok más szolgáltatásokból való betöltéséhez](search-indexer-overview.md)
* [Azure Cognitive Search-index lekérdezése a keresési ablak használatával a Azure Portal](search-explorer.md)
* [Az Azure Cognitive Search használata a .NET-ben](search-howto-dotnet-sdk.md)