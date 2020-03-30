---
title: Ismerje meg, hogyan biztosítható az adatokhoz való hozzáférés biztonságossá tétele az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB hozzáférés-vezérlési fogalmait, beleértve a főkulcsokat, az írásvédett kulcsokat, a felhasználókat és az engedélyeket.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251816"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Biztonságos hozzáférés az adatokhoz az Azure Cosmos DB-ben

Ez a cikk áttekintést nyújt a [Microsoft Azure Cosmos DB-ben](https://azure.microsoft.com/services/cosmos-db/)tárolt adatokhoz való hozzáférés biztosításáról.

Az Azure Cosmos DB kétféle kulcsot használ a felhasználók hitelesítéséhez, és hozzáférést biztosít az adatokhoz és az erőforrásokhoz. 

|Kulcs típusa|Források|
|---|---|
|[Főkulcsok](#master-keys) |Felügyeleti erőforrásokhoz használható: adatbázisfiókok, adatbázisok, felhasználók és engedélyek|
|[Erőforrás-tokenek](#resource-tokens)|Alkalmazás-erőforrásokhoz használatos: tárolók, dokumentumok, mellékletek, tárolt eljárások, eseményindítók és UDF-ek|

<a id="master-keys"></a>

## <a name="master-keys"></a>Főkulcsok

A főkulcsok hozzáférést biztosítanak az adatbázisfiók összes felügyeleti erőforrásához. Főkulcsok:

- Hozzáférést biztosítanak a fiókokhoz, adatbázisokhoz, felhasználókhoz és engedélyekhez. 
- Nem használható a tárolókhoz és dokumentumokhoz való részletes hozzáférés biztosításához.
- A fiók létrehozása során jönnek létre.
- Bármikor regenerálható.

Minden fiók két főkulcsból áll: egy elsődleges kulcsból és egy másodlagos kulcsból. A kettős kulcsok célja, hogy újragenerálhassa vagy a tekercskulcsokat, folyamatos hozzáférést biztosítva a fiókjához és az adatokhoz.

A Cosmos DB-fiók két főkulcsa mellett két írásvédett kulcs is található. Ezek az írásvédett kulcsok csak olvasási műveleteket engedélyeznek a fiókban. Az írásvédett kulcsok nem biztosítanak hozzáférést az olvasási engedélyekhez szükséges erőforrásokhoz.

Elsődleges, másodlagos, csak olvasható és írási-olvasási főkulcsok az Azure Portalon keresztül lehívhatók és újragenerálhatók. További információt a [Hozzáférési kulcsok megtekintése, másolása és újragenerálása (View, copy and regenerate) (Hozzáférési kulcsok megtekintése, másolása és újragenerálása) témakörben talál.](manage-with-cli.md#regenerate-account-key)

![Hozzáférés-vezérlés (IAM) az Azure Portalon – a NoSQL-adatbázis biztonságának bemutatása](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

A főkulcs elforgatásának folyamata egyszerű. Keresse meg az Azure Portalon a másodlagos kulcs lekéréséhez, majd cserélje le az elsődleges kulcsot az alkalmazásban a másodlagos kulcsra, majd forgassa el az elsődleges kulcsot az Azure Portalon.

![Főkulcs-rotáció az Azure Portalon – a NoSQL-adatbázis biztonságának bemutatása](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Kódminta főkulcs használatához

A következő kódminta bemutatja, hogyan használhatja a Cosmos DB-fiók végpontját és a főkulcsot a DocumentClient példányosításához és az adatbázis létrehozásához.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## <a name="resource-tokens"></a>Erőforrás-tokenek<a id="resource-tokens"></a>

Az erőforrás-jogkivonatok hozzáférést biztosítanak az alkalmazás erőforrásaihoz egy adatbázison belül. Erőforrás-jogkivonatok:

- Hozzáférés biztosítása adott tárolókhoz, partíciókulcsokhoz, dokumentumokhoz, mellékletekhez, tárolt eljárásokhoz, eseményindítókhoz és UDF-ekhez.
- Akkor jön létre, ha egy [felhasználó](#users) [engedélyt](#permissions) kap egy adott erőforráshoz.
- Akkor jönnek létre újra, ha egy engedélyerőforrást postai, GET vagy PUT hívással alkalmaznak.
- Használjon kifejezetten a felhasználóhoz, erőforráshoz és engedélyhez készített kivonaterőforrás-jogkivonatot.
- Az idő egy testreszabható érvényességi időszakkal van kötve. Az alapértelmezett érvényes időtartam egy óra. A token élettartama azonban explicit módon is megadható, legfeljebb öt óráig.
- Biztosítson biztonságos alternatívát a főkulcs kiadásával.
- Lehetővé teszi az ügyfelek számára, hogy a Cosmos DB-fiókban lévő erőforrásokat a megadott engedélyeknek megfelelően olvassák, írják és töröljék.

Használhatja az erőforrás-jogkivonatot (cosmos DB-felhasználók és engedélyek létrehozásával), ha hozzáférést szeretne biztosítani a Cosmos DB-fiók erőforrásaihoz egy olyan ügyfélszámára, amely nem megbízható a főkulccsal.  

A Cosmos DB erőforrás-tokenek biztonságos alternatívát biztosítanak, amely lehetővé teszi az ügyfelek számára, hogy a Cosmos DB-fiókban lévő erőforrásokat a megadott engedélyeknek megfelelően olvassák, írják és töröljék, és anélkül, hogy egy fő- vagy csak olvasható kulcsot kellene megadniuk.

Íme egy tipikus tervezési minta, amely nek köszönhetően az erőforrás-jogkivonatokat lehet kérni, létrehozni és szállítani az ügyfeleknek:

1. A középszintű szolgáltatás úgy van beállítva, hogy egy mobilalkalmazást szolgáljon ki a felhasználói fényképek megosztására.
2. A középszintű szolgáltatás rendelkezik a Cosmos DB-fiók fő kulcsával.
3. A fotóalkalmazás a végfelhasználói mobileszközökre van telepítve.
4. Bejelentkezéskor a fotóalkalmazás létrehozza a felhasználó identitását a középszintű szolgáltatással. Ez a mechanizmus az identitás létrehozása kizárólag fel a kérelmet.
5. Az identitás létrehozása után a középszintű szolgáltatás az identitás alapján kér engedélyeket.
6. A középszintű szolgáltatás egy erőforrás-jogkivonatot küld vissza a telefonos alkalmazásnak.
7. A telefonos alkalmazás továbbra is használhatja az erőforrás-jogkivonatot a Cosmos DB-erőforrások közvetlen eléréséhez az erőforrás-jogkivonat által és az erőforrásjogkivonat által engedélyezett időközzel.
8. Amikor az erőforrás-jogkivonat lejár, a további kérelmek 401-es jogosulatlan kivételt kapnak.  Ezen a ponton a telefonos alkalmazás újra létrehozza az identitást, és új erőforrás-jogkivonatot kér.

    ![Az Azure Cosmos DB erőforrástokenek munkafolyamata](./media/secure-access-to-data/resourcekeyworkflow.png)

Az erőforrásjogkivonat-generálást és -kezelést a natív Cosmos DB ügyfélkódtárak kezelik; rest használata esetén azonban létre kell hoznia a kérelem/hitelesítés fejléceket. A REST hitelesítési fejléceinek létrehozásáról a [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) vagy [a Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)forráskódjában talál további információt. [Access Control on Cosmos DB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)

Az erőforrás-tokenek létrehozásához vagy ügynöki közvetítéséhez használt középső rétegbeli szolgáltatás példáját a [ResourceTokenBroker alkalmazás ban](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)láthatja.

## <a name="users"></a>Felhasználók<a id="users"></a>

Az Azure Cosmos DB-felhasználók egy Cosmos-adatbázishoz vannak társítva.  Minden adatbázis tartalmazhat nulla vagy több Cosmos DB felhasználók. Az alábbi kódminta bemutatja, hogyan hozhat létre egy Cosmos DB-felhasználót az [Azure Cosmos DB .NET SDK v3 használatával.](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement)

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Minden Cosmos DB-felhasználó rendelkezik egy ReadAsync() metódussal, amely a felhasználóhoz társított [engedélyek](#permissions) listájának lekéréséhez használható.

## <a name="permissions"></a>Engedélyek<a id="permissions"></a>

Az engedély-erőforrás egy felhasználóhoz van társítva, és a tárolóhoz, valamint a partíciókulcs szintjén van hozzárendelve. Minden felhasználó nulla vagy több engedélyt tartalmazhat. Egy engedélyerőforrás hozzáférést biztosít egy biztonsági jogkivonathoz, amelyre a felhasználónak szüksége van, amikor egy adott tárolóhoz vagy egy adott partíciókulcs adataihoz próbál hozzáférni. Egy engedélyerőforrás két elérhető hozzáférési szinttel rendelkezhet:

- Mind: A felhasználó teljes körű engedéllyel rendelkezik az erőforráshoz.
- Olvasás: A felhasználó csak az erőforrás tartalmát tudja olvasni, de nem tud írási, frissítési vagy törlési műveleteket végrehajtani az erőforráson.

> [!NOTE]
> A tárolt eljárások futtatásához a felhasználónak rendelkeznie kell a Minden engedéllyel ahhoz a tárolóhoz, amelyben a tárolt eljárás futni fog.

### <a name="code-sample-to-create-permission"></a>Kódminta az engedély létrehozásához

A következő kódminta bemutatja, hogyan hozhat létre engedélyerőforrást, hogyan olvashatja be az engedélyerőforrás-jogkivonatot, és hogyan társíthatja az engedélyeket a fent létrehozott [felhasználóhoz.](#users)

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

### <a name="code-sample-to-read-permission-for-user"></a>Kódminta a felhasználó engedélyének olvasásához

A következő kódrészlet bemutatja, hogyan lehet lekérni a fent létrehozott felhasználóhoz társított engedélyt, és a felhasználó nevében egy új CosmosClient-t létrehozni, egyetlen partíciókulcsra kódolva.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Felhasználók hozzáadása és szerepkörök hozzárendelése

Az Azure Cosmos DB-fiókolvasó-hozzáférés hozzáadásához a felhasználói fiókhoz egy előfizetés-tulajdonos hajtsa végre a következő lépéseket az Azure Portalon.

1. Nyissa meg az Azure Portalt, és válassza ki az Azure Cosmos DB-fiókját.
2. Kattintson a **Hozzáférés-vezérlés (IAM)** fülre, majd **a + Szerepkör-hozzárendelés hozzáadása gombra.**
3. A **Szerepkör-hozzárendelés hozzáadása** ablaktáblán a **Szerepkör** mezőben válassza a **Cosmos DB-fiókolvasó szerepkör lehetőséget.**
4. A **Hozzáférés hozzárendelése a mezőben**válassza az **Azure AD-felhasználó, -csoport vagy -alkalmazás**lehetőséget.
5. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárban, amelyhez hozzáférést kíván adni.  A címtárban megjelenítendő név, e-mail cím vagy objektumazonosítók alapján kereshet.
    A kijelölt felhasználó, csoport vagy alkalmazás megjelenik a kijelölt tagok listájában.
6. Kattintson a **Mentés** gombra.

Az entitás most már olvashatja az Azure Cosmos DB-erőforrásokat.

## <a name="delete-or-export-user-data"></a>Felhasználói adatok törlése vagy exportálása

Az Azure Cosmos DB lehetővé teszi az adatbázisban vagy gyűjteményekben található személyes adatok keresését, kiválasztását, módosítását és törlését. Az Azure Cosmos DB API-kat biztosít a személyes adatok megkereséséhez és törléséhez, azonban az Ön felelőssége az API-k használata és a személyes adatok törléséhez szükséges logika meghatározása. Minden többmodelles API (SQL, MongoDB, Gremlin, Cassandra, Table) különböző nyelvű SDK-kat biztosít, amelyek a személyes adatok keresésének és törlésének módszereit tartalmazzák. Engedélyezheti azt is, hogy az [élő idő (TTL)](time-to-live.md) szolgáltatás automatikusan törölje az adatokat egy adott időszak után, további költségek nélkül.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések

- A Cosmos adatbázis-biztonságáról a [Cosmos DB Database security (Cosmos DB Database security) témakörben](database-security.md)olvashat bővebben.
- Az Azure Cosmos DB engedélyezési jogkivonatok létrehozásáról az [Azure Cosmos DB-erőforrások hozzáférés-vezérlése.](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)
- Felhasználókezelési minták felhasználókkal és engedélyekkel, [.NET SDK v3 felhasználói minták](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
