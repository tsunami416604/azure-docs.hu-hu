---
title: PowerShell-parancsfájlok Az.Search modul – Azure Search használatával
description: Hozzon létre, és az Azure Search szolgáltatás konfigurálása a PowerShell használatával. A szolgáltatások méretezése felfelé és lefelé, kezelheti a rendszergazdai és a lekérdezési api-kulcsok és a lekérdezés rendszer-információkat.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620597"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>A PowerShell-lel az Azure Search szolgáltatás kezelése
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

PowerShell-parancsmagok és parancsfájlok is futtathatja a Windows, Linux, vagy a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) létrehozása és konfigurálása az Azure Search. A **Az.Search** modul kiterjeszti az Azure PowerShell], a teljes paritással a [Azure Search felügyeleti REST API-k](https://docs.microsoft.com/rest/api/searchmanagement). Az Azure PowerShell-lel és **Az.Search**, a következő feladatok végrehajtására:

> [!div class="checklist"]
> * [Összes, az előfizetésben a keresési szolgáltatások](#list-search-services)
> * [Egy adott keresési szolgáltatás adatainak lekérése](#get-search-service-information)
> * [Hozzon létre vagy egy szolgáltatás törlése](#create-or-delete-a-service)
> * [Felügyeleti API-kulcsok újragenerálása](#regenerate-admin-keys)
> * [Hozzon létre vagy lekérdezési api-kulcsok törlése](#create-or-delete-query-keys)
> * [A szolgáltatások méretezése növelésével vagy csökkentésével a replikák és partíciók](#scale-replicas-and-partitions)

PowerShell nem használható a neve, régió vagy a szolgáltatási szintjének módosítása. Dedikált erőforrások vannak lefoglalva, a szolgáltatás létrehozásakor. Egy új szolgáltatás az alapul szolgáló hardver (hely vagy a csomópont típusa) módosítása szükséges. Nincsenek eszközök vagy API-k tartalmak átvitelére egy szolgáltatásból egy másikba. Minden tartalomkezelési keresztül van [REST](https://docs.microsoft.com/rest/api/searchservice/) vagy [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API-kat, és ha szeretné helyezni az indexek, szüksége lesz hozza létre újra és újra betölti őket az új szolgáltatás. 

Noha nincsenek dedikált PowerShell-parancsokat a tartalomkezeléshez, írhat a PowerShell-parancsprogram, amely meghívja a REST és .NET létrehozására és betöltésére, indexet. A **Az.Search** modul önmagában nem biztosítja ezeket a műveleteket.

Egyéb feladatok, PowerShell vagy bármely más API-t (csak portál) keresztül nem támogatott a következők:
+ [Cognitive services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) a [AI bővített indexelés](cognitive-search-concept-intro.md). A cognitive Services-szolgáltatás a képességek alkalmazási lehetőségét, nem egy előfizetés vagy szolgáltatás van csatolva.
+ [Kiegészítő figyelési megoldások](search-monitor-usage.md#add-on-monitoring-solutions) vagy [forgalmi elemzések keresése](search-traffic-analytics.md) Azure Search a figyeléshez használt.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Ellenőrizze a verziók, és a modulok betöltése

Ebben a cikkben szereplő példák interaktív és emelt szintű engedélyek szükségesek. Az Azure PowerShell (az **Az** modul) kell telepíteni. További információkért lásd: [Azure PowerShell telepítése](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>PowerShell-verziójának ellenőrzése (5.1-es vagy újabb)

Helyi PowerShell 5.1-es vagy újabb verziók esetében a bármelyik támogatott operációs rendszert kell lennie.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Az Azure PowerShell betöltése

Ha nem tudja, hogy **Az** van telepítve, a következő parancs futtatásával ellenőrzésre. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Egyes rendszerek nem betölteni modulok ne. Ha hibaüzenet jelenik az előző parancs próbálja a modul betöltése, és ha ez nem sikerül, lépjen vissza a telepítési utasításokat megtekintheti, ha a kihagyott egy lépést a.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Az Azure-bA összekapcsolása egy böngészőben jelentkezzen be jogkivonatot

Csatlakozás egy előfizetéshez a PowerShellben használhatja a portál bejelentkezési hitelesítő adatait. Másik megoldásként is [nem interaktív hitelesítéséhez a szolgáltatásnévvel](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Ha több Azure-előfizetéssel, tárolására, az Azure-előfizetés beállítása. Az aktuális előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Válassza ki az előfizetést, futtassa a következő parancsot. A következő példában, az előfizetés nevét a `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Az előfizetés összes Azure Search szolgáltatás listázása

Az alábbi parancsokat a rendszer [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), meglévő erőforrásokat és szolgáltatásokat az előfizetésben már kiépített vonatkozó információk. Ha nem tudja, hány keresési szolgáltatások már jönnek létre, ezek a parancsok információk alapján a belépőt mentése a portálon adja vissza.

Az első parancs összes keresőszolgáltatást adja vissza.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

A szolgáltatások listájában vissza az adott erőforrásra vonatkozó adatokat.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Eredmények a következő kimenet hasonlóan kell kinéznie.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Import Az.Search

A parancsok [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) nem érhetők el addig, amíg a modul betöltésére.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Az összes Az.Search parancslista

Ellenőrzési lépésként a parancsok, a modul az listáját adja vissza.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Eredmények a következő kimenet hasonlóan kell kinéznie.

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

## <a name="get-search-service-information"></a>Keresési szolgáltatás információinak lekérése

Után **Az.Search** importálása és tudja, hogy az erőforráscsoport, amely tartalmazza a search szolgáltatást, futtathatja [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) vissza a szolgáltatás definíciója, beleértve a neve, a régió, a szint és a replika és a Megszámolja a partíciót.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Eredmények a következő kimenet hasonlóan kell kinéznie.

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

## <a name="create-or-delete-a-service"></a>Hozzon létre vagy egy szolgáltatás törlése

[**Új AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) szolgál [hozzon létre egy új keresési szolgáltatás](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Eredmények a következő kimenet hasonlóan kell kinéznie.

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

## <a name="regenerate-admin-keys"></a>Az adminisztrációs kulcsok újragenerálása

[**Új AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) vihetők át a rendszergazda segítségével [API-kulcsok](search-security-api-keys.md). Két adminisztrációs kulcsot és a hitelesített hozzáférést minden szolgáltatás jönnek létre. Kulcsok szükségesek halasztása minden kérelemnél. Mindkét felügyeleti kulcsai funkcionális szempontból egyenértékű, teljes írási hozzáférést biztosít egy keresési szolgáltatás lehetővé teszi az adatok, lekérését, vagy hozzon létre, és minden objektum törlése. Két kulcs létezik, hogy közben, és cserélje le a másik használhatja. 

Csak újból létrehozhatja egyenként, mert a megadott a `primary` vagy `secondary` kulcsot. A folyamatos szolgáltatást ne felejtse el az összes ügyfél kód egy másodlagos kulcs használata során az elsődleges kulcs keresztül működés közbeni frissítése. Ne változtassa a kulcsokat, míg műveletek közötti átviteléhez.

Előfordulhat, hogy várakozásainak megfelelően, ha újragenerálja a kulcsokat Ügyfélkód frissítése nélkül, a régi kulcsot használja kérelmek sikertelenek lesznek. Minden új kulcsok újragenerálása nem végleges zárolja, az nem működik, és továbbra is elérheti a szolgáltatást a portálon keresztül. Elsődleges és másodlagos kulcsot hozza létre újra, miután frissítheti az új kulcsok használatához Ügyfélkód és műveletek ennek megfelelően fog folytatódni.

A szolgáltatás által előállított API-kulcs értékeit. Egyéni kulcs használata az Azure Search nem tud biztosítani. Ehhez hasonlóan nincs rendszergazdai API-kulcsok a felhasználó által definiált név. A kulcs mutató hivatkozásokat vagy rögzítettek karakterláncok, `primary` vagy `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Eredmények a következő kimenet hasonlóan kell kinéznie. Mindkét kulcsot a rendszer annak ellenére, hogy csak akkor módosítani, egyenként adja vissza.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Hozzon létre vagy a lekérdezési kulcsok törlése

[**Új AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) lekérdezés létrehozására szolgáló [API-kulcsok](search-security-api-keys.md) csak olvasási hozzáférés ügyfélalkalmazások az Azure Search-index. Lekérdezési kulcsok segítségével egy meghatározott index lekérdezése a keresési eredmények céljából történő hitelesítéséhez. Lekérdezési kulcsok ne adjon meg más elemek, a szolgáltatás, például egy index, az adatforrás vagy az indexelő csak olvasási hozzáférést.

A kulcs használatához az Azure Search nem tud biztosítani. A szolgáltatás API-kulcsok jönnek létre.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Méretezési csoport replikák és partíciók

[**Set-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) szolgál [növelheti vagy csökkentheti a replikák és partíciók](search-capacity-planning.md) , állítsa be újra a szolgáltatáson belül számlázható erőforrásokat. Növelje a replikákat és partíciókat ad hozzá a számlán, mindkettő szerepel rögzített és változó díjak. Ha nincs egy ideiglenes szüksége további feldolgozási teljesítmény, növelheti a replikákat és partíciókat a számítási feladatok kezelésére. A figyelési területen a portal Áttekintés lapján található csempék rendelkezik a Lekérdezések késése, egy második, és a szabályozás, amely azt jelzi, hogy jelenlegi kapacitás megfelelő lekérdezések.

Eltarthat egy ideig hozzáadása vagy eltávolítása szabályozásával. Kapacitás módosításai fordulhat elő, a háttérben, lehetővé téve a számítási feladatokat a folytatáshoz. További kapacitást szolgál a bejövő kérelmeket, amint elkészült, és nincs szükség további konfigurációra. 

Kapacitás eltávolítása zavart okozhatnak. Eldobott kérések elkerülése érdekében javasolt csökkenti a kapacitást előtt minden indexelő és az indexelő feladatok leállítása. Ha nem megvalósítható, érdemes lehet növekményes, csökkentve a kapacitást egy replika és a partíció, amíg az új cél szintet elérésekor.

Miután a parancs, nincs lehetőség leáll, középen keresztül. Várja meg, amíg a parancs befejeződik, mielőtt az érintett indítják kell.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Eredmények a következő kimenet hasonlóan kell kinéznie.

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

Hozhat létre egy [index](search-what-is-an-index.md), [-index lekérdezése](search-query-overview.md) a portal, a REST API-k vagy a .NET SDK használatával.

* [Az Azure Search-index létrehozása az Azure Portalon](search-create-index-portal.md)
* [Állítsa be az indexelő betölteni az adatokat más szolgáltatásokból](search-indexer-overview.md)
* [A keresési ablakkal az Azure Portalon az Azure Search-index lekérdezése](search-explorer.md)
* [Az Azure Search használata a .NET-ben](search-howto-dotnet-sdk.md)