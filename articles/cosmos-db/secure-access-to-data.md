---
title: Megtudhatja, hogyan védheti meg Azure Cosmos DB az adathozzáférését
description: Ismerje meg a Azure Cosmos DB hozzáférés-vezérlési fogalmait, beleértve az elsődleges kulcsokat, a csak olvasható kulcsokat, a felhasználókat és az engedélyeket.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 6dd95fc8fd0ab0099ac7404d4ca4e4b1851f650f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359608"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Biztonságos hozzáférés az adatokhoz az Azure Cosmos DB-ben
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk áttekintést nyújt a [Microsoft Azure Cosmos DBban](https://azure.microsoft.com/services/cosmos-db/)tárolt adathozzáférés biztonságossá tételéről.

Azure Cosmos DB kétféle kulcsot használ a felhasználók hitelesítéséhez és az adataihoz és erőforrásaihoz való hozzáférés biztosításához. 

|Kulcs típusa|További források|
|---|---|
|[Elsődleges kulcsok](#primary-keys) |Felügyeleti erőforrásokhoz használatos: adatbázis-fiókok, adatbázisok, felhasználók és engedélyek|
|[Erőforrás-tokenek](#resource-tokens)|Alkalmazás-erőforrásokhoz használatos: tárolók, dokumentumok, mellékletek, tárolt eljárások, eseményindítók és UDF|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Elsődleges kulcsok

Az elsődleges kulcsok hozzáférést biztosítanak az adatbázis-fiókhoz tartozó összes felügyeleti erőforráshoz. Minden fiók két elsődleges kulcsot tartalmaz: egy elsődleges és egy másodlagos kulcsot. A kettős kulcsok célja, hogy újragenerálja vagy leállítsa a kulcsokat, és folyamatos hozzáférést biztosítson fiókjához és adataihoz. Az elsődleges kulcsokkal kapcsolatos további tudnivalókért tekintse meg az [adatbázis biztonsági](database-security.md#primary-keys) cikkét.

### <a name="key-rotation"></a>Kulcs elforgatása<a id="key-rotation"></a>

Az elsődleges kulcs elforgatásának folyamata egyszerű. 

1. A másodlagos kulcs lekéréséhez navigáljon a Azure Portal.
2. Cserélje le az elsődleges kulcsot a másodlagos kulcsára az alkalmazásban. Győződjön meg arról, hogy az összes központi telepítés összes Cosmos DB ügyfele azonnal újraindul, és megkezdi a frissített kulcs használatát.
3. Forgassa el az elsődleges kulcsot a Azure Portal.
4. Ellenőrzi, hogy az új elsődleges kulcs az összes erőforráson működik-e. A kulcsfontosságú rotációs folyamat a Cosmos DB fiók méretétől függően akár egy perctől akár órákig is elvégezhető.
5. Cserélje le a másodlagos kulcsot az új elsődleges kulcsra.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Elsődleges kulcs rotációja a Azure Portal – a NoSQL-adatbázis biztonságának bemutatása" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Mintakód elsődleges kulcs használatára

Az alábbi mintakód bemutatja, hogyan használható egy Cosmos DB-fiók végpontja és az elsődleges kulcs egy DocumentClient létrehozásához és egy adatbázis létrehozásához:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

Az alábbi mintakód azt szemlélteti, hogyan használható a Azure Cosmos DB fiók végpontja és az elsődleges kulcs egy objektum létrehozásához `CosmosClient` :

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Erőforrás-tokenek <a id="resource-tokens"></a>

Az erőforrás-tokenek hozzáférést biztosítanak az adatbázison belüli alkalmazás-erőforrásokhoz. Erőforrás-tokenek:

- Hozzáférés biztosítása bizonyos tárolók, partíciós kulcsok, dokumentumok, mellékletek, tárolt eljárások, eseményindítók és UDF számára.
- Akkor jön létre, [](#users) amikor egy felhasználó [engedélyt kap egy](#permissions) adott erőforráshoz.
- Akkor jön létre újra, amikor egy engedélyezési erőforrást POST, GET vagy PUT hívás után végeznek el.
- Használjon olyan kivonatoló erőforrás-tokent, amelyet kifejezetten a felhasználóhoz, erőforráshoz és engedélyhez alakítottak ki.
- Az idő egy testreszabható érvényességi időtartammal van kötve. Az alapértelmezett érvényes időtartomány egy óra. A jogkivonat élettartama azonban explicit módon megadható, legfeljebb öt órára.
- Adjon meg egy biztonságos alternatívát az elsődleges kulcs megadásához.
- Lehetővé teszi az ügyfelek számára a Cosmos DB fiók erőforrásainak olvasását, írását és törlését a megadott engedélyek alapján.

Erőforrás-tokent (Cosmos DB felhasználók és engedélyek létrehozásával) is használhat, ha a Cosmos DB-fiókban lévő erőforrásokhoz hozzáférést szeretne biztosítani egy olyan ügyfél számára, amely nem megbízható az elsődleges kulccsal.  

Cosmos DB erőforrás-tokenek olyan biztonságos alternatívát biztosítanak, amely lehetővé teszi az ügyfelek számára, hogy az Ön által megadott engedélyek alapján beolvassák, írják és töröljék a Cosmos DB-fiókban lévő erőforrásokat, és nincs szükség elsődleges vagy írásvédett kulcsra.

Íme egy tipikus kialakítási minta, amely alapján az erőforrás-jogkivonatok az ügyfelek számára igényelhetők, hozhatók létre és továbbíthatók:

1. A közepes szintű szolgáltatás úgy van beállítva, hogy kiszolgálja a felhasználók fényképeit használó mobileszköz-alkalmazásokat.
2. A középső rétegbeli szolgáltatás rendelkezik az Cosmos DB fiók elsődleges kulcsával.
3. A Photo alkalmazás telepítve van a végfelhasználói mobileszközökön.
4. Bejelentkezéskor a Photo alkalmazás létrehozza a felhasználó identitását a középső rétegbeli szolgáltatással. Ez az identitás-létesítési mechanizmus kizárólag az alkalmazásra vonatkozik.
5. Az identitás létrejötte után a középső rétegbeli szolgáltatás engedélyt kér az identitás alapján.
6. A középső rétegbeli szolgáltatás egy erőforrás-tokent küld vissza a telefonos alkalmazásnak.
7. A telefonos alkalmazás továbbra is használhatja az erőforrás-jogkivonatot, hogy közvetlenül hozzáférhessen Cosmos DB erőforrásokhoz az erőforrás-jogkivonat által meghatározott engedélyekkel és az erőforrás-jogkivonat által engedélyezett intervallummal.
8. Az erőforrás-jogkivonat lejárata után a további kérések 401 jogosulatlan kivételt kapnak.  Ezen a ponton a telefonos alkalmazás újból létrehozza az identitást, és új erőforrás-tokent kér.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB erőforrás-tokenek munkafolyamata" border="false":::

Az erőforrás-jogkivonat létrehozását és felügyeletét a natív Cosmos DB ügyféloldali kódtárak kezelik. Ha azonban a REST-t használja, a kérelem/hitelesítés fejléceket kell létrehoznia. A REST-alapú hitelesítési fejlécek létrehozásával kapcsolatos további információkért lásd: [Access Control Cosmos db erőforrásokon](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) vagy a [.net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) -hoz vagy a [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)-hoz tartozó forráskódhoz.

Az erőforrás-tokenek létrehozásához vagy közvetítéséhez használt középső rétegű szolgáltatásra például a [ResourceTokenBroker alkalmazásban](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)talál példát.

## <a name="users"></a>Felhasználók<a id="users"></a>

Azure Cosmos DB felhasználók egy Cosmos-adatbázishoz vannak társítva.  Minden adatbázis nulla vagy több Cosmos DB felhasználót tartalmazhat. Az alábbi mintakód bemutatja, hogyan hozhat létre Cosmos DB felhasználót a [Azure Cosmos db .net SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement)használatával.

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Minden Cosmos DB felhasználó rendelkezik egy ReadAsync () metódussal, amely a felhasználóhoz társított [engedélyek](#permissions) listájának lekérésére használható.

## <a name="permissions"></a>Engedélyek<a id="permissions"></a>

Az engedélyezési erőforrás egy felhasználóhoz van társítva, és a tárolóban, valamint a partíciós kulcs szintjén van hozzárendelve. Minden felhasználó nulla vagy több engedélyt is tartalmazhat. Az engedélyezési erőforrás hozzáférést biztosít egy olyan biztonsági jogkivonathoz, amelyhez a felhasználónak szüksége van egy adott partíciós kulcsban található adott tároló vagy információ elérésére tett kísérlet során. Az engedélyezési erőforrások két rendelkezésre álló hozzáférési szintet tartalmazhatnak:

- Összes: a felhasználó teljes körű engedéllyel rendelkezik az erőforráson.
- Olvasás: a felhasználó csak az erőforrás tartalmát tudja olvasni, de írási, frissítési vagy törlési műveletet nem tud végrehajtani az erőforráson.

> [!NOTE]
> A tárolt eljárások futtatásához a felhasználónak az összes engedéllyel kell rendelkeznie arra a tárolóra, amelyben a tárolt eljárás futni fog.

Ha engedélyezi a [diagnosztikai naplókat az adatsík-kérelmek](cosmosdb-monitor-resource-logs.md)esetében, az engedélyhez tartozó következő két tulajdonságot naplózza a rendszer:

* **resourceTokenPermissionId** – ez a tulajdonság a megadott erőforrás-jogkivonat engedélyének azonosítóját jelöli. 

* **resourceTokenPermissionMode** – ez a tulajdonság azt az engedélyezési módot jelzi, amelyet az erőforrás-jogkivonat létrehozásakor beállított. Az engedélyezési mód rendelkezhet olyan értékekkel, mint az "all" vagy az "READ".

### <a name="code-sample-to-create-permission"></a>Kód mintája az engedély létrehozásához

Az alábbi mintakód bemutatja, hogyan hozhat létre egy engedélyezési erőforrást, beolvashatja az engedélyezési erőforrás erőforrás-jogkivonatát, és társíthatja az engedélyeket a fent létrehozott [felhasználóhoz](#users) .

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>A felhasználó számára olvasási engedéllyel rendelkező mintakód

A következő kódrészlet azt mutatja be, hogyan kérhető le a fent létrehozott felhasználóhoz tartozó engedély, és hogyan hozható létre egy új CosmosClient a felhasználó nevében, hatóköre pedig egyetlen partíciós kulcsra terjed ki.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Felhasználók hozzáadása és szerepkörök társítása

Ha Azure Cosmos DB fiók-olvasóhoz szeretne hozzáférni a felhasználói fiókjához, a következő lépéseket kell elvégeznie a Azure Portalban.

1. Nyissa meg a Azure Portal, és válassza ki Azure Cosmos DB-fiókját.
2. Kattintson a **hozzáférés-vezérlés (iam)** fülre, majd a  **+ szerepkör-hozzárendelés hozzáadása** lehetőségre.
3. A **szerepkör-hozzárendelés hozzáadása** panelen, a **szerepkör** mezőben válassza a **Cosmos db fiók-olvasó szerepkör** lehetőséget.
4. A **hozzáférés kiosztása mezőben** válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás** lehetőséget.
5. Válassza ki a címtárban azt a felhasználót, csoportot vagy alkalmazást, amelyhez hozzáférést szeretne biztosítani.  A címtárban a megjelenítendő név, e-mail-cím vagy objektumazonosító alapján kereshet.
    A kiválasztott felhasználó, csoport vagy alkalmazás megjelenik a kijelölt tagok listájában.
6. Kattintson a **Mentés** gombra.

Az entitás mostantól képes olvasni Azure Cosmos DB erőforrásait.

## <a name="delete-or-export-user-data"></a>Felhasználói adatértékek törlése vagy exportálása

Adatbázis-szolgáltatásként a Azure Cosmos DB lehetővé teszi az adatbázisban vagy tárolókban található bármilyen adatkeresést, kijelölését, módosítását és törlését. Az Ön felelőssége, hogy a megadott API-kat használja, és szükség esetén adja meg a személyes adat megkereséséhez és törléséhez szükséges logikát. Az egyes többmodelles API-k (SQL, MongoDB, Gremlin, Cassandra, Table) különböző nyelvi SDK-kat biztosítanak, amelyek az egyéni predikátumok alapján keresik és törölhetik az adatkeresési és-törlési módszereket. Azt is engedélyezheti, hogy az élettartam [(TTL)](time-to-live.md) szolgáltatás automatikusan töröljön egy adott időszak után az adattörlést anélkül, hogy további költségeket kellene fizetnie.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Következő lépések

- További információ a Cosmos Database biztonságáról: [Cosmos db adatbázis-biztonság](database-security.md).
- A Azure Cosmos DB engedélyezési jogkivonatok létrehozásával kapcsolatos további információkért lásd: [Access Control Azure Cosmos db erőforrásokon](/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Felhasználókkal és engedélyekkel rendelkező felhasználói felügyeleti minták, [.net SDK v3 felhasználói felügyeleti minták](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
