---
title: A lekérdezés nyelvének megismerése
description: Az Azure Resource Graph-ban használható Resource Graph-táblákat, valamint az elérhető Kusto adattípusokat, operátorokat és függvényeket ismerteti.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927494"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure Resource Graph lekérdezési nyelvének megismerése

Az Azure Resource Graph lekérdezési nyelve számos operátort és funkciót támogat. Minden munka és működés a [Kusto lekérdezési nyelvén (KQL)](/azure/kusto/query/index)alapul. Az erőforrás-gráf által használt lekérdezési nyelv megismeréséhez Kezdje a [KQL oktatóanyagával](/azure/kusto/query/tutorial).

Ez a cikk az erőforrás-gráf által támogatott nyelvi összetevőket ismerteti:

- [Resource Graph-táblák](#resource-graph-tables)
- [Támogatott KQL nyelvi elemek](#supported-kql-language-elements)
- [Escape-karakterek](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph-táblák

Az erőforrás-diagram több táblázatot is biztosít a Resource Manager-erőforrástípusok és azok tulajdonságairól tárolt adattároláshoz. Ezek a táblázatok `join` vagy `union` operátorral használhatók a tulajdonságok a kapcsolódó erőforrástípusokből való lekéréséhez. Itt látható az erőforrás-gráfban elérhető táblák listája:

|Resource Graph-táblák |Leírás |
|---|---|
|További források |Az alapértelmezett tábla, ha nincs megadva a lekérdezésben. A legtöbb Resource Manager-erőforrás típusa és tulajdonsága itt található. |
|ResourceContainers |Az előfizetést (előzetes verzió – `Microsoft.Resources/subscriptions`) és az erőforráscsoport (`Microsoft.Resources/subscriptions/resourcegroups`) típusú erőforrásokat és az adattípusokat tartalmazza. |
|AdvisorResources |`Microsoft.Advisor`hoz _kapcsolódó_ erőforrásokat tartalmaz. |
|AlertsManagementResources |`Microsoft.AlertsManagement`hoz _kapcsolódó_ erőforrásokat tartalmaz. |
|MaintenanceResources |`Microsoft.Maintenance`hoz _kapcsolódó_ erőforrásokat tartalmaz. |
|SecurityResources |`Microsoft.Security`hoz _kapcsolódó_ erőforrásokat tartalmaz. |

A teljes listát, például az erőforrástípusok listáját a következő témakörben tekintheti meg [: hivatkozás: támogatott táblák és erőforrástípusok](../reference/supported-tables-resources.md).

> [!NOTE]
> Az _erőforrások_ az alapértelmezett tábla. A _Resources (erőforrások_ ) tábla lekérdezésekor nem kell megadnia a tábla nevét, kivéve `join` vagy `union` használata esetén. Azonban az ajánlott eljárás az, hogy mindig tartalmazza a kezdeti táblát a lekérdezésben.

A portálon a Resource Graph Explorer segítségével derítheti ki, hogy milyen típusú erőforrások érhetők el az egyes táblákban. Alternatív megoldásként használjon egy lekérdezést, például az `<tableName> | distinct type`-t, hogy lekérje a megadott Resource Graph-táblázat által támogatott erőforrástípusok listáját.

A következő lekérdezés egy egyszerű `join`mutat be. A lekérdezés eredménye összekeveri az oszlopokat és az illesztett táblázat ismétlődő oszlopainak nevét, az ebben a példában szereplő _ResourceContainers_ pedig **1**. Mivel a _ResourceContainers_ tábla mindkét előfizetéshez és az erőforráscsoporthoz is rendelkezik, a Type ( _erőforrások_ ) tábla erőforrásokhoz való csatlakoztatására is használható.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Az alábbi lekérdezés a `join`összetettebb használatát mutatja be. A lekérdezés korlátozza az összekapcsolt táblát az előfizetések erőforrásaira, és a `project`, hogy csak az eredeti mezőt _subscriptionId_ , a _név_ mezőt pedig nevezze át a _alnévre_. A mező átnevezése elkerüli, hogy `join` hozzáadja a _name1_ , mert a mező már létezik az _erőforrásokban_. Az eredeti tábla `where` van szűrve, és az alábbi `project` mindkét táblából származó oszlopokat tartalmaz. A lekérdezés eredménye egyetlen kulcstároló-megjelenítési típus, a kulcstartó neve és az előfizetés neve.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ha a `join` eredményeket `project`re korlátozza, a `join` által használt tulajdonságot a fenti példában szereplő két tábla _subscriptionId_ kell `project`.

## <a name="supported-kql-language-elements"></a>Támogatott KQL nyelvi elemek

Az erőforrás-gráf támogatja az összes KQL [adattípust](/azure/kusto/query/scalar-data-types/), [skaláris függvényt](/azure/kusto/query/scalarfunctions), [skaláris operátort](/azure/kusto/query/binoperators)és [összesítési függvényt](/azure/kusto/query/any-aggfunction). Az erőforrás-gráf bizonyos [táblázatos operátorokat](/azure/kusto/query/queries) támogat, amelyek némelyike eltérő viselkedéssel rendelkezik.

### <a name="supported-tabulartop-level-operators"></a>Támogatott táblázatos/legfelső szintű operátorok

Itt látható a KQL táblázatos operátorok listája, amelyeket az erőforrás-gráf adott mintákkal támogat:

|KQL |Resource Graph-minta lekérdezése |Megjegyzések |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Kulcstartók száma](../samples/starter.md#count-keyvaults) | |
|[különböző](/azure/kusto/query/distinctoperator) |[Egy adott alias különböző értékeinek megjelenítése](../samples/starter.md#distinct-alias-values) | |
|[kiterjesztése](/azure/kusto/query/extendoperator) |[A virtuális gépek száma az operációs rendszer típusa szerint](../samples/starter.md#count-os) | |
|[csatlakozás](/azure/kusto/query/joinoperator) |[Key Vault előfizetés neve](../samples/advanced.md#join) |A JOIN Flavors támogatott: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Legfeljebb 3 `join` egyetlen lekérdezésben. Az egyéni csatlakoztatási stratégiák, például a szórásos csatlakozás, nem engedélyezettek. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[korlátot](/azure/kusto/query/limitoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |`take` szinonimája |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Örökölt operátor, használjon `mv-expand` helyette. _ROWLIMIT_ Max 400. Az alapértelmezett érték a 128. |
|[MV – Kibontás](/azure/kusto/query/mvexpandoperator) |[Adott írási hellyel rendelkező Cosmos DB listázása](../samples/advanced.md#mvexpand-cosmosdb) |_ROWLIMIT_ Max 400. Az alapértelmezett érték a 128. |
|[sorrendben](/azure/kusto/query/orderoperator) |[Az erőforrások listája név szerint rendezve](../samples/starter.md#list-resources) |`sort` szinonimája |
|[projekt](/azure/kusto/query/projectoperator) |[Az erőforrások listája név szerint rendezve](../samples/starter.md#list-resources) | |
|[projekt – vendég](/azure/kusto/query/projectawayoperator) |[Oszlopok eltávolítása az eredményekből](../samples/advanced.md#remove-column) | |
|[Rendezés](/azure/kusto/query/sortoperator) |[Az erőforrások listája név szerint rendezve](../samples/starter.md#list-resources) |`order` szinonimája |
|[Összegzés](/azure/kusto/query/summarizeoperator) |[Az Azure-erőforrások száma](../samples/starter.md#count-resources) |Csak egyszerűsített első oldal |
|[eltarthat](/azure/kusto/query/takeoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |`limit` szinonimája |
|[Top](/azure/kusto/query/topoperator) |[Az első öt virtuális gép megjelenítése a nevük és az operációs rendszerük típusa szerint](../samples/starter.md#show-sorted) | |
|[Union](/azure/kusto/query/unionoperator) |[Két lekérdezés eredményeinek egyetlen eredménybe való egyesítése](../samples/advanced.md#unionresults) |Önálló tábla engedélyezve: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _tábla_. Legfeljebb 3 `union` lábát egyetlen lekérdezésben. `union` lábát tartalmazó táblák fuzzy feloldása nem engedélyezett. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[ahol](/azure/kusto/query/whereoperator) |[A tárolót tartalmazó erőforrások megjelenítése](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-karakterek

Egyes tulajdonságnév, például `.` vagy `$`belefoglalása szükséges, a lekérdezésben kell becsomagolni vagy megszökni, vagy a tulajdonság neve helytelenül van értelmezve, és nem biztosítja a várt eredményeket.

- `.` – a tulajdonság nevét csomagolja be: `['propertyname.withaperiod']`
  
  Példa lekérdezésre, amely a OData tulajdonságot csomagolja _. írja be a következőt_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` – escape a tulajdonság nevében szereplő karakter. A használatban lévő escape-karakter a rendszerhéj-erőforrás Gráftól függ.

  - **bash** - `\`

    Példa olyan lekérdezésre, amely megmenekül a _\$típus_ a bash-ben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – ne elkerülje a `$` karaktert.

  - **PowerShell** - - ``` ` ```

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság _\$típus_ a PowerShellben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- További információ az [erőforrások feltárásáról](explore-resources.md).