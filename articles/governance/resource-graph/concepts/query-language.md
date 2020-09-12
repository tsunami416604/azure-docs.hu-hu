---
title: A lekérdezésnyelv megismerése
description: Az Azure Resource Graph-ban használható Resource Graph-táblákat, valamint az elérhető Kusto adattípusokat, operátorokat és függvényeket ismerteti.
ms.date: 08/24/2020
ms.topic: conceptual
ms.openlocfilehash: 65304ca1241b2c8a1f9541580e7ee8434dd5b6eb
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426401"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure Resource Graph lekérdezési nyelvének megismerése

Az Azure Resource Graph lekérdezési nyelve számos operátort és funkciót támogat. Minden munka és működés a [Kusto lekérdezési nyelvén (KQL)](/azure/kusto/query/index)alapul. Az erőforrás-gráf által használt lekérdezési nyelv megismeréséhez Kezdje a [KQL oktatóanyagával](/azure/kusto/query/tutorial).

Ez a cikk az erőforrás-gráf által támogatott nyelvi összetevőket ismerteti:

- [Resource Graph-táblák](#resource-graph-tables)
- [Az erőforrás-gráf egyéni nyelvi elemei](#resource-graph-custom-language-elements)
- [Támogatott KQL nyelvi elemek](#supported-kql-language-elements)
- [A lekérdezés hatóköre](#query-scope)
- [Escape-karakterek](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph-táblák

Az erőforrás-diagram több táblázatot is biztosít a Azure Resource Manager erőforrástípusok és tulajdonságaik által tárolt adattároláshoz. Ezek a táblák a (z `join` ) és a (z `union` ) operátorokkal használhatók a kapcsolódó erőforrástípusok tulajdonságainak lekéréséhez. Itt látható az erőforrás-gráfban elérhető táblák listája:

|Resource Graph-táblák |Description |
|---|---|
|További források |Az alapértelmezett tábla, ha nincs megadva a lekérdezésben. A legtöbb Resource Manager-erőforrás típusa és tulajdonsága itt található. |
|ResourceContainers |A tartalmazza az előfizetést (előzetes verzióban `Microsoft.Resources/subscriptions` ) és az erőforráscsoport ( `Microsoft.Resources/subscriptions/resourcegroups` ) típusú erőforrásokat és az adattípusokat. |
|AdvisorResources |A következőhöz _kapcsolódó_ erőforrásokat tartalmazza: `Microsoft.Advisor` . |
|AlertsManagementResources |A következőhöz _kapcsolódó_ erőforrásokat tartalmazza: `Microsoft.AlertsManagement` . |
|GuestConfigurationResources |A következőhöz _kapcsolódó_ erőforrásokat tartalmazza: `Microsoft.GuestConfiguration` . |
|HealthResources |A következőhöz _kapcsolódó_ erőforrásokat tartalmazza: `Microsoft.ResourceHealth` . |
|MaintenanceResources |A következőhöz _kapcsolódó_ erőforrásokat tartalmazza: `Microsoft.Maintenance` . |
|SecurityResources |A következőhöz _kapcsolódó_ erőforrásokat tartalmazza: `Microsoft.Security` . |

A teljes listát, például az erőforrástípusok listáját a következő témakörben tekintheti meg [: hivatkozás: támogatott táblák és erőforrástípusok](../reference/supported-tables-resources.md).

> [!NOTE]
> Az _erőforrások_ az alapértelmezett tábla. A _Resources (erőforrások_ ) tábla lekérdezése során nem kötelező megadni a táblanév nevét, hacsak `join` vagy nem használja őket `union` . Azonban az ajánlott eljárás az, hogy mindig tartalmazza a kezdeti táblát a lekérdezésben.

A portálon a Resource Graph Explorer segítségével derítheti ki, hogy milyen típusú erőforrások érhetők el az egyes táblákban. Alternatív megoldásként használjon egy lekérdezést, például `<tableName> | distinct type` hogy lekérje a megadott Resource Graph-táblázat által támogatott erőforrástípusok listáját.

A következő lekérdezés egy egyszerűt mutat be `join` . A lekérdezés eredménye összekeveri az oszlopokat és az illesztett táblázat ismétlődő oszlopainak nevét, az ebben a példában szereplő _ResourceContainers_ pedig **1**. Mivel a _ResourceContainers_ tábla mindkét előfizetéshez és az erőforráscsoporthoz is rendelkezik, a Type ( _erőforrások_ ) tábla erőforrásokhoz való csatlakoztatására is használható.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A következő lekérdezés a összetettebb használatát mutatja be `join` . A lekérdezés korlátozza az összekapcsolt táblát az előfizetések erőforrásaira, és a alkalmazásban `project` csak az eredeti mezőt _subscriptionId_ , a _név_ mezőt pedig átnevezi a _alnévre_. A mező átnevezése elkerüli a `join` _name1_ hozzáadását, mivel a mező már létezik az _erőforrásokban_. Az eredeti tábla a következővel van szűrve, `where` és a következők `project` tartoznak mindkét táblázat oszlopaihoz. A lekérdezés eredménye egyetlen kulcstároló-megjelenítési típus, a kulcstartó neve és az előfizetés neve.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Az eredmények a alkalmazással való korlátozásakor `join` `project` a által használt tulajdonságnak `join` szerepelnie kell a következő példában szereplő két tábla _subscriptionId_ `project` .

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Kiterjesztett tulajdonságok (előzetes verzió)

_Előzetes_ verzióként az erőforrás-gráf egyes erőforrástípusok további típusokkal kapcsolatos tulajdonságokkal rendelkeznek, amelyek a Azure Resource Manager által biztosított tulajdonságokkal meghaladják a lekérdezéseket. A (z) rendszer egy támogatott erőforrástípus esetében a _kiterjesztett tulajdonságok_néven ismert értékeket tartalmaz `properties.extended` . A következő lekérdezéssel megtekintheti, hogy mely erőforrástípusok rendelkeznek _kiterjesztett tulajdonságokkal_:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Példa: a virtuális gépek számának lekérése a következővel `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Az erőforrás-gráf egyéni nyelvi elemei

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Megosztott lekérdezési szintaxis (előzetes verzió)

Előzetes verzióként egy [megosztott lekérdezés](../tutorials/create-share-query.md) közvetlenül egy Resource Graph-lekérdezésben érhető el. Ez a forgatókönyv lehetővé teszi, hogy szabványos lekérdezéseket hozzon létre megosztott lekérdezésként, és újra felhasználhassa őket. Ha egy megosztott lekérdezést szeretne meghívni egy Erőforrásgrafikon-lekérdezésen belül, használja a `{{shared-query-uri}}` szintaxist. A megosztott lekérdezés URI-ja a lekérdezés **Beállítások** lapján található megosztott lekérdezés _erőforrás-azonosítója_ . Ebben a példában a megosztott lekérdezési URI-ja a következő: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Ez az URI az előfizetés, az erőforráscsoport és a megosztott lekérdezés teljes nevére mutat, amelyet egy másik lekérdezésben szeretnénk hivatkozni. Ez a lekérdezés ugyanaz, mint az [oktatóanyagban létrehozott: lekérdezés létrehozása és megosztása](../tutorials/create-share-query.md).

> [!NOTE]
> Megosztott lekérdezésként megosztott lekérdezésre hivatkozó lekérdezések nem menthetők.

1. példa: csak a megosztott lekérdezés használata

Az erőforrás-gráf lekérdezésének eredményei megegyeznek a megosztott lekérdezésben tárolt lekérdezéssel.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

2. példa: a megosztott lekérdezés belefoglalása egy nagyobb lekérdezés részeként

A lekérdezés először a megosztott lekérdezést használja, majd a használatával `limit` tovább korlátozza az eredményeket.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Támogatott KQL nyelvi elemek

Az erőforrás-gráf a KQL [adattípusok](/azure/kusto/query/scalar-data-types/), a [skaláris függvények](/azure/kusto/query/scalarfunctions), a [skaláris operátorok](/azure/kusto/query/binoperators)és az [összesítési függvények](/azure/kusto/query/any-aggfunction)egy részhalmazát támogatja. Az erőforrás-gráf bizonyos [táblázatos operátorokat](/azure/kusto/query/queries) támogat, amelyek némelyike eltérő viselkedéssel rendelkezik.

### <a name="supported-tabulartop-level-operators"></a>Támogatott táblázatos/legfelső szintű operátorok

Itt látható a KQL táblázatos operátorok listája, amelyeket az erőforrás-gráf adott mintákkal támogat:

|KQL |Resource Graph-minta lekérdezése |Jegyzetek |
|---|---|---|
|[száma](/azure/kusto/query/countoperator) |[Kulcstartók száma](../samples/starter.md#count-keyvaults) | |
|[különböző](/azure/kusto/query/distinctoperator) |[Egy adott alias különböző értékeinek megjelenítése](../samples/starter.md#distinct-alias-values) | |
|[kiterjesztése](/azure/kusto/query/extendoperator) |[A virtuális gépek száma az operációs rendszer típusa szerint](../samples/starter.md#count-os) | |
|[csatlakozás](/azure/kusto/query/joinoperator) |[Key Vault előfizetés neve](../samples/advanced.md#join) |A JOIN Flavors támogatott: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Legfeljebb 3 `join` egyetlen lekérdezésben. Az egyéni csatlakoztatási stratégiák, például a szórásos csatlakozás, nem engedélyezettek. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[korlátot](/azure/kusto/query/limitoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |Szinonimája `take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Örökölt operátor, használja `mv-expand` helyette. _ROWLIMIT_ Max 400. Az alapértelmezett érték a 128. |
|[MV – Kibontás](/azure/kusto/query/mvexpandoperator) |[Adott írási hellyel rendelkező Cosmos DB listázása](../samples/advanced.md#mvexpand-cosmosdb) |_ROWLIMIT_ Max 400. Az alapértelmezett érték a 128. |
|[sorrendben](/azure/kusto/query/orderoperator) |[Erőforrások listázása név szerint rendezve](../samples/starter.md#list-resources) |Szinonimája `sort` |
|[projekt](/azure/kusto/query/projectoperator) |[Erőforrások listázása név szerint rendezve](../samples/starter.md#list-resources) | |
|[projekt – vendég](/azure/kusto/query/projectawayoperator) |[Oszlopok eltávolítása az eredményekből](../samples/advanced.md#remove-column) | |
|[Rendezés](/azure/kusto/query/sortoperator) |[Erőforrások listázása név szerint rendezve](../samples/starter.md#list-resources) |Szinonimája `order` |
|[Összegzés](/azure/kusto/query/summarizeoperator) |[Az Azure-erőforrások száma](../samples/starter.md#count-resources) |Csak egyszerűsített első oldal |
|[take](/azure/kusto/query/takeoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |Szinonimája `limit` |
|[top](/azure/kusto/query/topoperator) |[Első öt virtuális gép megjelenítése név és operációsrendszer-típus szerint](../samples/starter.md#show-sorted) | |
|[Union](/azure/kusto/query/unionoperator) |[Két lekérdezés eredményeinek egyetlen eredménybe való egyesítése](../samples/advanced.md#unionresults) |Egyetlen tábla engedélyezett: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] _tábla_. `union`Egyetlen lekérdezésben legfeljebb 3 láb megengedett. A láb típusú táblák fuzzy feloldása `union` nem engedélyezett. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[ahol](/azure/kusto/query/whereoperator) |[Tárolót tartalmazó erőforrások megjelenítése](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>Lekérdezési hatókör

Annak az előfizetésnek a hatóköre, amelyből a lekérdezés az erőforrásokat adja vissza, az erőforrás-gráf elérésének módjától függ. Az Azure CLI és a Azure PowerShell feltöltheti a kérelembe felvenni kívánt előfizetések listáját a jogosultsággal rendelkező felhasználó kontextusa alapján. Az előfizetések listája manuálisan is definiálható az **előfizetések** és az **előfizetési** paraméterek esetében.
A REST API és az összes többi SDK-ban a kérés részeként explicit módon meg kell határozni az előfizetések listáját.

**Előzetes**verzióként REST API verziója `2020-04-01-preview` egy tulajdonság hozzáadásával a lekérdezés hatókörét egy [felügyeleti csoportba](../../management-groups/overview.md)helyezi. Ez az előzetes verziójú API azt is lehetővé teszi, hogy az előfizetés tulajdonság nem kötelező. Ha nincs definiálva felügyeleti csoport vagy előfizetési lista, a lekérdezés hatóköre minden erőforrás, amely tartalmazza az [Azure Lighthouse](../../../lighthouse/concepts/azure-delegated-resource-management.md) által delegált erőforrásokat, amelyeket a hitelesített felhasználó elérhet. Az új `managementGroupId` tulajdonság a felügyeleti csoport azonosítóját veszi át, amely eltér a felügyeleti csoport nevétől. Ha `managementGroupId` meg van adva, a rendszer a megadott felügyeleti csoport hierarchiájának első 5000-előfizetésének erőforrásait tartalmazza. `managementGroupId` nem használható egyszerre a következővel: `subscriptions` .

Példa: a "saját felügyeleti csoport" nevű felügyeleti csoport hierarchiájában lévő összes erőforrás lekérdezése a (z) myMG AZONOSÍTÓval.

- REST API URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Kérelem törzse

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Escape-karakterek

Egyes tulajdonságokat, például a vagy a karaktert tartalmazó neveket `.` be `$` kell csomagolni, vagy el kell menekülni a lekérdezésben, vagy a tulajdonság nevét nem megfelelően értelmezi a rendszer, és nem biztosítja a várt eredményeket.

- `.` – A tulajdonság nevét a következőképpen csomagolja be: `['propertyname.withaperiod']`
  
  Példa lekérdezésre, amely a OData tulajdonságot csomagolja _. írja be a következőt_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Escape a tulajdonság nevében szereplő karakter. A használatban lévő escape-karakter a rendszerhéj-erőforrás Gráftól függ.

  - **bash** - `\`

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság _ \$ típusa_ a bashben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – ne elkerülje a `$` karaktert.

  - **PowerShell** - ``` ` ```

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság _ \$ típusa_ a PowerShellben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- További információ az [erőforrások feltárásáról](explore-resources.md).
