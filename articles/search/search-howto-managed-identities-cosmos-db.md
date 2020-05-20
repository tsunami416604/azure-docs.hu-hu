---
title: Cosmos DB-fiókhoz való kapcsolódás beállítása felügyelt identitás használatával (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan állíthat be indexelő-kapcsolatokat egy Cosmos DB-fiókhoz felügyelt identitás használatával (előzetes verzió)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 3524f55f70ff42bd5ff800fb2bd7ab7b0e732596
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664682"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Indexelő-kapcsolatok beállítása egy Cosmos DB-adatbázishoz felügyelt identitás használatával (előzetes verzió)

> [!IMPORTANT] 
> A felügyelt identitással létesített kapcsolatok egy adatforráshoz való beállításának támogatása jelenleg egy kezdeményezett nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott.
> Az előnézet elérését az [űrlap](https://aka.ms/azure-cognitive-search/mi-preview-request)kitöltésével kérheti le.

Ez a lap azt ismerteti, hogyan állítható be egy indexelő-kapcsolódás egy Azure Cosmos DB-adatbázishoz felügyelt identitás használatával ahelyett, hogy hitelesítő adatokat kellene megadnia az adatforrás-objektum kapcsolódási karakterláncában.

Mielőtt többet szeretne megtudni a szolgáltatásról, javasoljuk, hogy Ismerje meg, mi az indexelő, és hogyan állítható be egy indexelő az adatforráshoz. További információt az alábbi hivatkozásokon talál:
* [Az indexelő áttekintése](search-indexer-overview.md)
* [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>A kapcsolatok beállítása felügyelt identitás használatával

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – a rendszerhez rendelt felügyelt identitás bekapcsolása

Ha egy rendszerhez rendelt felügyelt identitás engedélyezve van, az Azure létrehoz egy identitást a keresési szolgáltatáshoz, amely az ugyanazon a bérlőn és előfizetésen belüli más Azure-szolgáltatásokban való hitelesítéshez használható. Ezt az identitást használhatja olyan szerepköralapú hozzáférés-vezérlési (RBAC) hozzárendelésekben, amelyek lehetővé teszik az adatokhoz való hozzáférést az indexelés során.

![A rendszerhez rendelt felügyelt identitás bekapcsolása](./media/search-managed-identities/turn-on-system-assigned-identity.png "A rendszerhez rendelt felügyelt identitás bekapcsolása")

A **Mentés** gombra kattintva megjelenik egy objektumazonosító, amely hozzá van rendelve a keresési szolgáltatáshoz.

![Objektumazonosító](./media/search-managed-identities/system-assigned-identity-object-id.png "Objektumazonosító")
 
### <a name="2---add-a-role-assignment"></a>2 – szerepkör-hozzárendelés hozzáadása

Ebben a lépésben az Azure Cognitive Search-szolgáltatás engedélyt ad az adatok olvasására a Cosmos DB-adatbázisból.

1. A Azure Portal navigáljon ahhoz a Cosmos DB-fiókhoz, amely az indexelni kívánt adattartalomot tartalmazza.
2. **Hozzáférés-vezérlés kiválasztása (iam)**
3. Válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**

    ![Szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Szerepkör-hozzárendelés hozzáadása")

4. **Cosmos db fiók-olvasó szerepkör** kiválasztása
5. Az **Azure ad-felhasználó,-csoport vagy-szolgáltatásnév** **hozzáférésének** elhagyása
6. Keresse meg a keresési szolgáltatást, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Olvasó-és adathozzáférési szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Olvasó-és adathozzáférési szerepkör-hozzárendelés hozzáadása")

### <a name="3---create-the-data-source"></a>3 – az adatforrás létrehozása

Az **adatforrások** az index, a hitelesítő adatok és az adatok változásainak azonosítására szolgáló szabályzatokat határozzák meg (például módosított vagy törölt dokumentumok a gyűjteményen belül). Az adatforrás független erőforrásként van definiálva, így több indexelő is használható.

Ha felügyelt identitásokat használ az adatforráshoz való hitelesítéshez, a **hitelesítő adatok** nem tartalmazzák a fiók kulcsát.

Példa Cosmos DB adatforrás-objektum létrehozására a [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)használatával:

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

A kérelem törzse tartalmazza az adatforrás definícióját, amelynek tartalmaznia kell a következő mezőket:

| Mező   | Description |
|---------|-------------|
| **név** | Kötelező. Válasszon egy tetszőleges nevet az adatforrás-objektum megjelenítéséhez. |
|**típusa**| Kötelező. Kell lennie `cosmosdb` . |
|**hitelesítő adatok** | Kötelező. <br/><br/>Felügyelt identitással való kapcsolódás esetén a **hitelesítő adatok** formátumának a következőket kell tartalmaznia: *adatbázis = [adatbázis-név]; ResourceId = [erőforrás-azonosító-string];(ApiKind = [API-Kind];)*<br/> <br/>A ResourceId formátuma: *ResourceId =/Subscriptions/**az előfizetés-azonosító**/resourceGroups/**az erőforráscsoport neve**/Providers/Microsoft.DocumentDB/databaseAccounts/**a Cosmos db-fiók neve**/;*<br/><br/>SQL-gyűjtemények esetén a ApiKind nem szükséges a kapcsolatok karakterlánca.<br/><br/>A MongoDB-gyűjtemények esetében adja hozzá a **ApiKind = MongoDB** karakterláncot a kapcsolódási sztringhez. <br/><br/>A Gremlin gráfok és a Cassandra-táblázatok esetében regisztráljon a [GateD indexelő előzetes](https://aka.ms/azure-cognitive-search/indexer-preview) verziójára, és kérjen hozzáférést az előzetes verzióhoz, és tájékozódjon a hitelesítő adatok formázásáról.<br/>|
| **tároló** | A következő elemeket tartalmazza: <br/>**név**: kötelező. Az indexelni kívánt adatbázis-gyűjtemény AZONOSÍTÓjának meghatározása.<br/>**lekérdezés**: nem kötelező. Megadhat egy lekérdezést, amely egy tetszőleges JSON-dokumentumot lelapul egy olyan egyszerű sémába, amelyet az Azure Cognitive Search tud indexelni.<br/>A MongoDB API, a Gremlin API és a Cassandra API esetében a lekérdezések nem támogatottak. |
| **dataChangeDetectionPolicy** | Ajánlott |
|**dataDeletionDetectionPolicy** | Választható |

A Azure Portal és a [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) is támogatja a felügyelt identitások összekapcsolási karakterláncát. A Azure Portal használatához meg kell adni egy Feature jelzőt, amely akkor jelenik meg, amikor az oldal tetején található hivatkozásra kattint az előzetes verzióra való regisztráláskor. 

### <a name="4---create-the-index"></a>4 – az index létrehozása

Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Ebből a témakörből megtudhatja, hogyan hozhat létre egy indexet egy kereshető `booktitle` mezővel:

```
POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

További információk az indexek létrehozásáról: [create index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – az indexelő létrehozása

Az indexelő egy adatforrást hoz létre a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít.

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására.

Példa indexelő definícióra:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Lásd még

További információ a Cosmos DB indexelő szolgáltatásról:
* [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
