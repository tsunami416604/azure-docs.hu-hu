---
title: A lekérdezésnyelv megismerése
description: Az Azure Resource Graph-ban használható Resource Graph-táblákat, valamint az elérhető Kusto adattípusokat, operátorokat és függvényeket ismerteti.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78927494"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure Resource Graph lekérdezési nyelvének megismerése

Az Azure Resource Graph lekérdezési nyelve számos operátort és funkciót támogat. Minden munka és működés a [Kusto lekérdezési nyelvén (KQL)](/azure/kusto/query/index)alapul. Az erőforrás-gráf által használt lekérdezési nyelv megismeréséhez Kezdje a [KQL oktatóanyagával](/azure/kusto/query/tutorial).

Ez a cikk az erőforrás-gráf által támogatott nyelvi összetevőket ismerteti:

- [Resource Graph-táblák](#resource-graph-tables)
- [Támogatott KQL nyelvi elemek](#supported-kql-language-elements)
- [Escape-karakterek](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph-táblák

Az erőforrás-diagram több táblázatot is biztosít a Resource Manager-erőforrástípusok és azok tulajdonságairól tárolt adattároláshoz. Ezek a táblák a (z `join` ) `union` és a (z) operátorokkal használhatók a kapcsolódó erőforrástípusok tulajdonságainak lekéréséhez. Itt látható az erőforrás-gráfban elérhető táblák listája:

|Resource Graph-táblák |Leírás |
|---|---|
|További források |Az alapértelmezett tábla, ha nincs megadva a lekérdezésben. A legtöbb Resource Manager-erőforrás típusa és tulajdonsága itt található. |
|ResourceContainers |A tartalmazza az előfizetést ( `Microsoft.Resources/subscriptions`előzetes verzióban) és`Microsoft.Resources/subscriptions/resourcegroups`az erőforráscsoport () típusú erőforrásokat és az adattípusokat. |
|AdvisorResources |A `Microsoft.Advisor`következőhöz _kapcsolódó_ erőforrásokat tartalmazza:. |
|AlertsManagementResources |A `Microsoft.AlertsManagement`következőhöz _kapcsolódó_ erőforrásokat tartalmazza:. |
|MaintenanceResources |A `Microsoft.Maintenance`következőhöz _kapcsolódó_ erőforrásokat tartalmazza:. |
|SecurityResources |A `Microsoft.Security`következőhöz _kapcsolódó_ erőforrásokat tartalmazza:. |

A teljes listát, például az erőforrástípusok listáját a következő témakörben tekintheti meg [: hivatkozás: támogatott táblák és erőforrástípusok](../reference/supported-tables-resources.md).

> [!NOTE]
> Az _erőforrások_ az alapértelmezett tábla. A _Resources (erőforrások_ ) tábla lekérdezése során nem kötelező megadni a táblanév `join` nevét `union` , hacsak vagy nem használja őket. Azonban az ajánlott eljárás az, hogy mindig tartalmazza a kezdeti táblát a lekérdezésben.

A portálon a Resource Graph Explorer segítségével derítheti ki, hogy milyen típusú erőforrások érhetők el az egyes táblákban. Alternatív megoldásként használjon egy lekérdezést, például `<tableName> | distinct type` hogy lekérje a megadott Resource Graph-táblázat által támogatott erőforrástípusok listáját.

A következő lekérdezés egy egyszerűt `join`mutat be. A lekérdezés eredménye összekeveri az oszlopokat és az illesztett táblázat ismétlődő oszlopainak nevét, az ebben a példában szereplő _ResourceContainers_ pedig **1**. Mivel a _ResourceContainers_ tábla mindkét előfizetéshez és az erőforráscsoporthoz is rendelkezik, a Type ( _erőforrások_ ) tábla erőforrásokhoz való csatlakoztatására is használható.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A következő lekérdezés a összetettebb használatát mutatja be `join`. A lekérdezés korlátozza az összekapcsolt táblát az előfizetések erőforrásaira, és a `project` alkalmazásban csak az eredeti mezőt _subscriptionId_ , a _név_ mezőt pedig átnevezi a _alnévre_. A mező átnevezése elkerüli `join` a _name1_ hozzáadását, mivel a mező már létezik az _erőforrásokban_. Az eredeti tábla a következővel `where` van szűrve `project` , és a következők tartoznak mindkét táblázat oszlopaihoz. A lekérdezés eredménye egyetlen kulcstároló-megjelenítési típus, a kulcstartó neve és az előfizetés neve.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `join` Az eredmények `project`a alkalmazással való korlátozásakor a által `join` használt tulajdonságnak szerepelnie kell a következő `project`példában szereplő két tábla _subscriptionId_ .

## <a name="supported-kql-language-elements"></a>Támogatott KQL nyelvi elemek

Az erőforrás-gráf támogatja az összes KQL [adattípust](/azure/kusto/query/scalar-data-types/), [skaláris függvényt](/azure/kusto/query/scalarfunctions), [skaláris operátort](/azure/kusto/query/binoperators)és [összesítési függvényt](/azure/kusto/query/any-aggfunction). Az erőforrás-gráf bizonyos [táblázatos operátorokat](/azure/kusto/query/queries) támogat, amelyek némelyike eltérő viselkedéssel rendelkezik.

### <a name="supported-tabulartop-level-operators"></a>Támogatott táblázatos/legfelső szintű operátorok

Itt látható a KQL táblázatos operátorok listája, amelyeket az erőforrás-gráf adott mintákkal támogat:

|KQL |Resource Graph-minta lekérdezése |Megjegyzések |
|---|---|---|
|[száma](/azure/kusto/query/countoperator) |[Kulcstartók száma](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Egy adott alias különböző értékeinek megjelenítése](../samples/starter.md#distinct-alias-values) | |
|[kiterjesztése](/azure/kusto/query/extendoperator) |[A virtuális gépek száma az operációs rendszer típusa szerint](../samples/starter.md#count-os) | |
|[csatlakozás](/azure/kusto/query/joinoperator) |[Key Vault előfizetés neve](../samples/advanced.md#join) |A JOIN Flavors támogatott: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Legfeljebb 3 `join` egyetlen lekérdezésben. Az egyéni csatlakoztatási stratégiák, például a szórásos csatlakozás, nem engedélyezettek. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[korlátot](/azure/kusto/query/limitoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |Szinonimája`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Örökölt operátor, `mv-expand` használja helyette. _ROWLIMIT_ Max 400. Az alapértelmezett érték a 128. |
|[MV – Kibontás](/azure/kusto/query/mvexpandoperator) |[Adott írási hellyel rendelkező Cosmos DB listázása](../samples/advanced.md#mvexpand-cosmosdb) |_ROWLIMIT_ Max 400. Az alapértelmezett érték a 128. |
|[sorrendben](/azure/kusto/query/orderoperator) |[Erőforrások listázása név szerint rendezve](../samples/starter.md#list-resources) |Szinonimája`sort` |
|[projekt](/azure/kusto/query/projectoperator) |[Erőforrások listázása név szerint rendezve](../samples/starter.md#list-resources) | |
|[projekt – vendég](/azure/kusto/query/projectawayoperator) |[Oszlopok eltávolítása az eredményekből](../samples/advanced.md#remove-column) | |
|[Rendezés](/azure/kusto/query/sortoperator) |[Erőforrások listázása név szerint rendezve](../samples/starter.md#list-resources) |Szinonimája`order` |
|[Összegzés](/azure/kusto/query/summarizeoperator) |[Az Azure-erőforrások száma](../samples/starter.md#count-resources) |Csak egyszerűsített első oldal |
|[eltarthat](/azure/kusto/query/takeoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |Szinonimája`limit` |
|[Top](/azure/kusto/query/topoperator) |[Első öt virtuális gép megjelenítése név és operációsrendszer-típus szerint](../samples/starter.md#show-sorted) | |
|[Union](/azure/kusto/query/unionoperator) |[Két lekérdezés eredményeinek egyetlen eredménybe való egyesítése](../samples/advanced.md#unionresults) |Egyetlen tábla engedélyezett: _T_ `| union` \[ `kind=` `inner` \| T `outer` _Table_ _ColumnName_ ColumnName tábla. \] \[ `withsource=`\] Egyetlen lekérdezésben `union` legfeljebb 3 láb megengedett. A láb típusú `union` táblák fuzzy feloldása nem engedélyezett. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[ahol](/azure/kusto/query/whereoperator) |[Tárolót tartalmazó erőforrások megjelenítése](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-karakterek

Egyes tulajdonságokat, például a `.` vagy `$`a karaktert tartalmazó neveket be kell csomagolni, vagy el kell menekülni a lekérdezésben, vagy a tulajdonság nevét nem megfelelően értelmezi a rendszer, és nem biztosítja a várt eredményeket.

- `.`– A tulajdonság nevét a következőképpen csomagolja be:`['propertyname.withaperiod']`
  
  Példa lekérdezésre, amely a OData tulajdonságot csomagolja _. írja be a következőt_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape a tulajdonság nevében szereplő karakter. A használatban lévő escape-karakter a rendszerhéj-erőforrás Gráftól függ.

  - **bash** - `\`

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság _ \$típusa_ a bashben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – ne elkerülje a `$` karaktert.

  - **PowerShell** - ``` ` ```

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság _ \$típusa_ a PowerShellben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>További lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- További információ az [erőforrások feltárásáról](explore-resources.md).