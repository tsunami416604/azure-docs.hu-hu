---
title: A lekérdezés nyelvének megismerése
description: Az Azure Resource Graph-ban használható Resource Graph-táblákat, valamint az elérhető Kusto adattípusokat, operátorokat és függvényeket ismerteti.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 6189920cb03a6cf388f0b5d232c6ce97ae4f3f82
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389769"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure Resource Graph lekérdezési nyelvének megismerése

Az Azure Resource Graph lekérdezési nyelve számos operátort és funkciót támogat. Minden munka és működés a [Kusto lekérdezési nyelvén (KQL)](/azure/kusto/query/index)alapul. Az erőforrás-gráf által használt lekérdezési nyelv megismeréséhez Kezdje a [KQL oktatóanyagával](/azure/kusto/query/tutorial).

Ez a cikk az erőforrás-gráf által támogatott nyelvi összetevőket ismerteti:

- [Resource Graph-táblák](#resource-graph-tables)
- [Támogatott KQL nyelvi elemek](#supported-kql-language-elements)
- [Escape-karakterek](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph-táblák

Az erőforrás-diagram több táblázatot is biztosít a Resource Manager-erőforrástípusok és azok tulajdonságairól tárolt adattároláshoz. Ezek a táblázatok `join` vagy `union` operátorokkal használhatók a tulajdonságok a kapcsolódó erőforrástípusokből való lekéréséhez. Itt látható az erőforrás-gráfban elérhető táblák listája:

|Resource Graph-táblák |Leírás |
|---|---|
|Segédanyagok és eszközök |Az alapértelmezett tábla, ha nincs megadva a lekérdezésben. A legtöbb Resource Manager-erőforrás típusa és tulajdonsága itt található. |
|ResourceContainers |Tartalmazza az előfizetés (`Microsoft.Resources/subscriptions`) és az erőforráscsoport (`Microsoft.Resources/subscriptions/resourcegroups`) típusú erőforrásokat és az adattípusokat. |
|AlertsManagementResources |@No__t-1- _hez kapcsolódó_ erőforrásokat tartalmaz. |
|SecurityResources |@No__t-1- _hez kapcsolódó_ erőforrásokat tartalmaz. |

> [!NOTE]
> Az _erőforrások_ az alapértelmezett tábla. A _Resources (erőforrások_ ) tábla lekérdezése során nem szükséges megadnia a tábla nevét, kivéve, ha `join` vagy `union` van használatban. Azonban az ajánlott eljárás az, hogy mindig tartalmazza a kezdeti táblát a lekérdezésben.

A portálon a Resource Graph Explorer segítségével derítheti ki, hogy milyen típusú erőforrások érhetők el az egyes táblákban. Alternatív megoldásként használjon egy olyan lekérdezést, mint például a `<tableName> | distinct type`, hogy lekérje a megadott Resource Graph-táblázat által támogatott erőforrástípusok listáját.

A következő lekérdezés egy egyszerű `join` értéket mutat be. A lekérdezés eredménye összekeveri az oszlopokat és az illesztett táblázat ismétlődő oszlopainak nevét, az ebben a példában szereplő _ResourceContainers_ pedig **1**. Mivel a _ResourceContainers_ tábla mindkét előfizetéshez és az erőforráscsoporthoz is rendelkezik, a Type ( _erőforrások_ ) tábla erőforrásokhoz való csatlakoztatására is használható.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Az alábbi lekérdezés a `join` összetettebb használatát mutatja be. A lekérdezés korlátozza az összekapcsolt táblát az előfizetések erőforrásaira, és a `project` értékkel csak az eredeti mező _subscriptionId_ , a _név_ mezőt pedig átnevezi a _alnévre_. A mező átnevezése elkerüli a `join` _name1_ , mivel a mező már létezik az _erőforrásokban_. Az eredeti tábla a `where` értékkel van szűrve, és a következő `project` mindkét táblából tartalmaz oszlopokat. A lekérdezés eredménye egyetlen kulcstároló-megjelenítési típus, a kulcstartó neve és az előfizetés neve.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ha a `join` eredményt a `project` értékre korlátozza, a `join` táblához használt tulajdonságot, amely a fenti példában szereplő _subscriptionId_ vonatkozik, szerepelnie kell a következőben: `project`.

## <a name="supported-kql-language-elements"></a>Támogatott KQL nyelvi elemek

Az erőforrás-gráf támogatja az összes KQL [adattípust](/azure/kusto/query/scalar-data-types/), [skaláris függvényt](/azure/kusto/query/scalarfunctions), [skaláris operátort](/azure/kusto/query/binoperators)és [összesítési függvényt](/azure/kusto/query/any-aggfunction). Az erőforrás-gráf bizonyos [táblázatos operátorokat](/azure/kusto/query/queries) támogat, amelyek némelyike eltérő viselkedéssel rendelkezik.

### <a name="supported-tabulartop-level-operators"></a>Támogatott táblázatos/legfelső szintű operátorok

Itt látható a KQL táblázatos operátorok listája, amelyeket az erőforrás-gráf adott mintákkal támogat:

|KQL |Resource Graph-minta lekérdezése |Megjegyzések |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Kulcstartók száma](../samples/starter.md#count-keyvaults) | |
|[különböző](/azure/kusto/query/distinctoperator) |[Egy adott alias különböző értékeinek megjelenítése](../samples/starter.md#distinct-alias-values) | |
|[kiterjesztése](/azure/kusto/query/extendoperator) |[A virtuális gépek száma az operációs rendszer típusa szerint](../samples/starter.md#count-os) | |
|[csatlakozás](/azure/kusto/query/joinoperator) |[Key Vault előfizetés neve](../samples/advanced.md#join) |A JOIN Flavors támogatott: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Legfeljebb 3 @no__t – 0 egyetlen lekérdezésben. Az egyéni csatlakoztatási stratégiák, például a szórásos csatlakozás, nem engedélyezettek. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[korlátot](/azure/kusto/query/limitoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |@No__t – 0 szinonimája |
|[MV – Kibontás](/azure/kusto/query/mvexpandoperator) |[Adott írási hellyel rendelkező Cosmos DB listázása](../samples/advanced.md#mvexpand-cosmosdb) |_ROWLIMIT_ Max 400 |
|[sorrendben](/azure/kusto/query/orderoperator) |[Az erőforrások listája név szerint rendezve](../samples/starter.md#list-resources) |@No__t – 0 szinonimája |
|[projekt](/azure/kusto/query/projectoperator) |[Az erőforrások listája név szerint rendezve](../samples/starter.md#list-resources) | |
|[projekt – vendég](/azure/kusto/query/projectawayoperator) |[Oszlopok eltávolítása az eredményekből](../samples/advanced.md#remove-column) | |
|[Rendezés](/azure/kusto/query/sortoperator) |[Az erőforrások listája név szerint rendezve](../samples/starter.md#list-resources) |@No__t – 0 szinonimája |
|[Összegzés](/azure/kusto/query/summarizeoperator) |[Az Azure-erőforrások száma](../samples/starter.md#count-resources) |Csak egyszerűsített első oldal |
|[eltarthat](/azure/kusto/query/takeoperator) |[Az összes nyilvános IP-cím listázása](../samples/starter.md#list-publicip) |@No__t – 0 szinonimája |
|[Top](/azure/kusto/query/topoperator) |[Az első öt virtuális gép megjelenítése a nevük és az operációs rendszerük típusa szerint](../samples/starter.md#show-sorted) | |
|[Union](/azure/kusto/query/unionoperator) |[Két lekérdezés eredményeinek egyetlen eredménybe való egyesítése](../samples/advanced.md#unionresults) |Önálló tábla engedélyezve: _T_ `| union` \[ @ no__t-3 `inner` @ no__t-5 @ no__t-6 @ no__t-7 \[ @ no__t-9_ColumnName_1 _tábla_. Legfeljebb 3 @no__t – 0 láb egyetlen lekérdezésben. @No__t-0 láb-táblázatok fuzzy feloldása nem engedélyezett. Egy táblán belül, illetve az _erőforrások_ és a _ResourceContainers_ táblák között is felhasználható. |
|[ahol](/azure/kusto/query/whereoperator) |[A tárolót tartalmazó erőforrások megjelenítése](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-karakterek

Egyes tulajdonságnév, például a `.` vagy a `$`, be kell csomagolni vagy megszökni a lekérdezésben, vagy a tulajdonságnév helytelenül van értelmezve, és nem biztosítja a várt eredményeket.

- @no__t – 0 – a tulajdonság nevének becsomagolása: `['propertyname.withaperiod']`
  
  Példa lekérdezésre, amely a OData tulajdonságot csomagolja _. írja be a következőt_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- @no__t – 0 – a tulajdonság nevében lévő karakter elkerülhető. A használatban lévő escape-karakter a rendszerhéj-erőforrás Gráftól függ.

  - **bash** -  @ no__t-2

    Példa olyan lekérdezésre, amely a bash _\$type_ tulajdonságát megmenekül:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – ne elkerülje a `$` karaktert.

  - **PowerShell** -  @ no__t-2

    Példa a _\$type_ tulajdonságot a PowerShellben elkerülő lekérdezésre:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Következő lépések

- Megtekintheti az [alapszintű lekérdezésekben](../samples/starter.md) használt nyelvet
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md)
- Információ az [erőforrások felfedezéséről](explore-resources.md)