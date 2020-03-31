---
title: A lekérdezésnyelv megismerése
description: Az Azure Resource Graph segítségével használható Erőforrásgráf-táblák, valamint a rendelkezésre álló Kusto-adattípusok, operátorok és funkciók ismertetése.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927494"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure Resource Graph lekérdezési nyelvének ismertetése

Az Azure Resource Graph lekérdezési nyelve számos operátort és függvényt támogat. Minden munka és működik alapján [Kusto Query Language (KQL)](/azure/kusto/query/index). Ha többet szeretne megtudni a Resource Graph által használt lekérdezési nyelvről, kezdje a [KQL oktatóanyagával.](/azure/kusto/query/tutorial)

Ez a cikk az Erőforrás-grafikon által támogatott nyelvi összetevőket ismerteti:

- [Erőforrásgráf-táblák](#resource-graph-tables)
- [Támogatott KQL nyelvi elemek](#supported-kql-language-elements)
- [Escape karakterek](#escape-characters)

## <a name="resource-graph-tables"></a>Erőforrásgráf-táblák

Az Erőforrásgráf számos táblát biztosít az Erőforrás-kezelő erőforrástípusokkal és azok tulajdonságaival kapcsolatos adatokhoz. Ezek a táblák `join` a `union` kapcsolódó erőforrástípusok tulajdonságainak leéséhez használhatók vagy operátorokkal. Az Erőforrásgrafikonban elérhető táblák listája:

|Erőforrásgráf-táblák |Leírás |
|---|---|
|Források |Az alapértelmezett tábla, ha nincs definiálva a lekérdezésben. A legtöbb Erőforrás-kezelő erőforrástípus és -tulajdonság itt található. |
|Erőforrástárolók |Tartalmazza az előfizetést `Microsoft.Resources/subscriptions`(előzetes verzióban`Microsoft.Resources/subscriptions/resourcegroups`-- ) és az erőforráscsoport ( ) erőforrástípusokat és adatokat. |
|Tanácsadóforrások |A _hoz_ `Microsoft.Advisor`kapcsolódó erőforrásokat tartalmazza. |
|FigyelmeztetésekManagementErőforrások |A _hoz_ `Microsoft.AlertsManagement`kapcsolódó erőforrásokat tartalmazza. |
|Karbantartási erőforrások |A _hoz_ `Microsoft.Maintenance`kapcsolódó erőforrásokat tartalmazza. |
|Biztonsági erőforrások |A _hoz_ `Microsoft.Security`kapcsolódó erőforrásokat tartalmazza. |

Az erőforrástípusokat tartalmazó teljes listát a [Hivatkozás: Támogatott táblák és erőforrástípusok című témakörben található.](../reference/supported-tables-resources.md)

> [!NOTE]
> _Az erőforrások_ az alapértelmezett tábla. Az _Erőforrások_ tábla lekérdezése közben nem szükséges megadni a `join` `union` tábla nevét, kivéve, ha az az erőforrások at használja. Az ajánlott eljárás azonban az, hogy mindig a kezdeti táblát tartalmazza a lekérdezésben.

A portálon található Erőforrásgráf-kezelővel megtudhatja, hogy az egyes táblákban milyen erőforrástípusok érhetők el. Másik lehetőségként használjon egy `<tableName> | distinct type` lekérdezést, például a megadott Erőforrásgráf tábla által támogatott erőforrástípusok listáját.

A következő lekérdezés `join`egy egyszerű . A lekérdezés eredménye keveri az oszlopokat, és az összekapcsolt tábla ismétlődő oszlopneveit, a _ResourceContainers-t_ ebben a példában a program **az 1-hez**fűzi hozzá. Mivel _a ResourceContainers_ tábla előfizetésekhez és erőforráscsoportokhoz is tartalmaz típusokat, bármelyik típus használható az _erőforráshoz_ való csatlakozáshoz az erőforrástáblából.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A következő lekérdezés a összetettebb `join`használatát mutatja. A lekérdezés az összekapcsolt táblát előfizetési `project` erőforrásokra korlátozza, és csak az eredeti _mező-előfizetésazonosítót_ és az _Alnév_névre átnevezett _névmezőt_ tartalmazza. A mező átnevezése nem `join` adja hozzá _név1-nek,_ mivel a mező már létezik az Erőforrások _mezőben._ Az eredeti táblázat szűrése a `project` következővel `where` történik, és mindkét tábla oszlopait tartalmazza. A lekérdezés eredménye egy egy kulcstartó titulus, a kulcstartó neve, és az előfizetés neve, amelyben van.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ha az `join` `project`eredményeket a lehetőséggel korlátozza, a két tábla `join` összefüggésére használt tulajdonságot, a fenti példában szereplő _subscriptionId azonosítót_ a részét a rendszernek kell tartalmaznia. `project`

## <a name="supported-kql-language-elements"></a>Támogatott KQL nyelvi elemek

A Resource Graph támogatja az összes KQL [adattípust](/azure/kusto/query/scalar-data-types/), [skalárfüggvényt](/azure/kusto/query/scalarfunctions), [skalároperátort](/azure/kusto/query/binoperators)és [összesítési függvényt](/azure/kusto/query/any-aggfunction). A konkrét [táblázatos operátorokat a](/azure/kusto/query/queries) Resource Graph támogatja, amelyek közül néhány eltérő viselkedéssel rendelkezik.

### <a name="supported-tabulartop-level-operators"></a>Támogatott táblázatos/legfelső szintű operátorok

Itt van a Resource Graph által támogatott KQL táblázatos operátorok listája, konkrét mintákkal:

|KQL |Erőforrásgráf mintalekérdezése |Megjegyzések |
|---|---|---|
|[Számít](/azure/kusto/query/countoperator) |[Kulcstartók megszámlálása](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Adott alias különböző értékeinek megjelenítése](../samples/starter.md#distinct-alias-values) | |
|[Kiterjesztése](/azure/kusto/query/extendoperator) |[A virtuális gépek száma az operációs rendszer típusa szerint](../samples/starter.md#count-os) | |
|[csatlakozás](/azure/kusto/query/joinoperator) |[Kulcstartó előfizetésnévvel](../samples/advanced.md#join) |Csatlakoztassa ízek támogatott: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [belső](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Legfeljebb `join` 3 egy lekérdezésben. Egyéni illesztési stratégiák, például a szórásos illesztés, nem engedélyezettek. Egyetlen táblában vagy az _Erőforrások_ és _a ResourceContainers_ táblák között használható. |
|[Korlátoz](/azure/kusto/query/limitoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |Szinonimája`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Örökölt operátor, `mv-expand` használja helyette. _RowLimit_ max. Az alapértelmezett érték 128. |
|[mv-kibontás](/azure/kusto/query/mvexpandoperator) |[Cosmos DB listázása adott írási helyekkel](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max. Az alapértelmezett érték 128. |
|[Rendelés](/azure/kusto/query/orderoperator) |[Név szerint rendezett erőforrások listázása](../samples/starter.md#list-resources) |Szinonimája`sort` |
|[Projekt](/azure/kusto/query/projectoperator) |[Név szerint rendezett erőforrások listázása](../samples/starter.md#list-resources) | |
|[projekt-el](/azure/kusto/query/projectawayoperator) |[Oszlopok eltávolítása az eredményekből](../samples/advanced.md#remove-column) | |
|[Rendezés](/azure/kusto/query/sortoperator) |[Név szerint rendezett erőforrások listázása](../samples/starter.md#list-resources) |Szinonimája`order` |
|[Összefoglalni](/azure/kusto/query/summarizeoperator) |[Az Azure-erőforrások száma](../samples/starter.md#count-resources) |Csak egyszerűsített első oldal |
|[venni](/azure/kusto/query/takeoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |Szinonimája`limit` |
|[Top](/azure/kusto/query/topoperator) |[Az első öt virtuális gép megjelenítése név és operációs rendszer típusa szerint](../samples/starter.md#show-sorted) | |
|[Unió](/azure/kusto/query/unionoperator) |[Két lekérdezés eredményeinek egyetlen eredménybe való egyesítése](../samples/advanced.md#unionresults) |Egytábla engedélyezett: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \] _Table_ColumnName tábla . _ColumnName_ \[ `withsource=` Legfeljebb `union` 3 láb egyetlen lekérdezésben. A lábasztalok fuzzy felbontása `union` nem megengedett. Egyetlen táblában vagy az _Erőforrások_ és _a ResourceContainers_ táblák között használható. |
|[Ahol](/azure/kusto/query/whereoperator) |[Tárolót tartalmazó erőforrások megjelenítése](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape karakterek

Egyes tulajdonságneveket, például `.` azokat, amelyek egy vagyt `$`tartalmaznak, a lekérdezésben be kell csomagolni vagy meg kell kerülni, vagy a tulajdonság nevét helytelenül értelmezi a rendszer, és nem adja meg a várt eredményt.

- `.`- A tulajdonság nevét a következőképpen csomagolja be:`['propertyname.withaperiod']`
  
  Példa lekérdezésre, amely az _odata.type_tulajdonságot tördeli:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Escape a karakter a tulajdonság nevét. A használt escape karakter attól függ, hogy a rendszerhéj Resource Graph fut.

  - **Bash** - `\`

    Példa lekérdezésre, amely _ \$_ elkerüli a tulajdonság típusát a bash-ben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Ne meneküljön a `$` karakter.

  - **Powershell** - ``` ` ```

    Példa lekérdezésre, amely elkerüli a _ \$tulajdonságtípusát_ a PowerShellben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelvet az [Indítólekérdezésekben.](../samples/starter.md)
- Lásd: Speciális használat a [Speciális lekérdezésekben.](../samples/advanced.md)
- További információ az [erőforrások felfedezéséről.](explore-resources.md)