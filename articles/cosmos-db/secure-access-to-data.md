---
title: Megtudhatja, hogyan biztosíthat biztonságos hozzáférést a adatokat az Adatbázisba az Azure Cosmos |} Microsoft Docs
description: További tudnivalók access control jellemzői Azure Cosmos DB, beleértve a főkulcs, csak olvasható kulcsok, felhasználók és engedélyek.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: sngun
ms.openlocfilehash: 7a53dda7d6b49187d77ca44bcb55db5f9c305f64
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Azure Cosmos DB adatokhoz való hozzáférés biztonságossá tétele
Ez a cikk áttekintést a adataihoz való hozzáférés biztosítása [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos-adatbázis kétféle kulcsok segítségével hitelesíti a felhasználókat, és az adatok és erőforrások eléréséhez. 

|Kulcstípus|További források|
|---|---|
|[Főkulcsok](#master-keys) |Felügyeleti erőforrások használt: adatbázis-fiókok, adatbázisok, felhasználók és engedélyek|
|[Erőforrás-tokenek](#resource-tokens)|Alkalmazás-erőforrásokat használt: gyűjtemények, dokumentumok, a mellékletek, tárolt eljárások, eseményindítók és felhasználó által megadott függvények|

<a id="master-keys"></a>

## <a name="master-keys"></a>Főkulcsok 

Főkulcsait az összes felügyeleti erőforrást az adatbázis fiókjához tartozó hozzáférést biztosítanak. Főkulcsok:  
- Fiókok, adatbázisok, felhasználók és engedélyek hozzáférést biztosítanak. 
- Nem használható a részletes hozzáférést biztosít a gyűjtemények és dokumentumok.
- A fiók létrehozása során jönnek létre.
- Bármikor helyreállíthatja.

Minden felhasználói fiókhoz két főkulcsok áll: egy elsődleges és másodlagos kulcsot. Kettős kulcsok célja, hogy generálni, vagy állítsa a kulcsokat, a fiók és az adatok folyamatos hozzáférést biztosító. 

A Cosmos DB fiók két fő kulcsot kívül két írásvédett kulcsot. A csak olvasható kulcsokat engedélyezése csak a fiók az olvasási műveletek. Csak olvasható kulcsok nem biztosítanak engedélyeket erőforrások olvasási hozzáférést.

Elsődleges, másodlagos csak olvasható és írható-olvasható főkulcsok lekérhető, és újra létrehozza a rendszer az Azure portál használatával. Útmutatásért lásd: [megtekintése, másolása és újragenerálása hívóbetűk](manage-account.md#keys).

![Az Azure portál – bemutatásához NoSQL-adatbázis biztonsági hozzáférés-vezérlés (IAM)](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

A főkulcs elforgatása folyamata felettébb egyszerű. Keresse meg a másodlagos kulcs lekérését, majd cserélje le az elsődleges kulcsra a másodlagos kulcs az alkalmazás az Azure portálon, majd forgassa el az elsődleges kulcsot az Azure portálon.

![Az Azure portál – NoSQL-adatbázis biztonsági, amely tartalmazza a főkulcs Elforgatás](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>A főkulcs használandó kódminta

A következő példakód azt ábrázolja, hogyan Cosmos DB fiók végpontját és főkulcs használatával hozható létre egy DocumentClient, és hozzon létre egy adatbázist. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Erőforrás-tokenek

Erőforrás-jogkivonatokat biztosítanak hozzáférést az adatbázisban lévő alkalmazás-erőforrásokat. Erőforrás-jogkivonatokat:
- Adott gyűjteményekre, partíciós kulcsok, dokumentumok, a mellékletek, tárolt eljárások, eseményindítók és felhasználó által megadott függvények hozzáférést biztosítanak.
- Jönnek létre, amikor egy [felhasználói](#users) kap [engedélyek](#permissions) adott erőforráshoz.
- Jönnek létre újból, akkor egy engedély erőforrás van a FELADÁS egy vagy több, a GET vagy a PUT hívás.
- A felhasználói, erőforrás és engedélye kifejezetten összeállított kivonatoló erőforrás jogkivonatot használja.
- Testre szabható érvényességi időtartam kötött megnyitásakor. Az alapértelmezett érvényes timespan egy óra. A jogkivonatok élettartama, azonban előfordulhat, hogy explicit módon megadni, legfeljebb öt órát.
- Adjon meg egy biztonságos helyett a főkulcs kiadása. 
- Engedélyezi az ügyfelek számára olvasási, írási és törli az erőforrást a Cosmos DB fiók megfelelően korábban megadott engedélyeket.

Egy erőforrás-jogkivonat (létrehozásával Cosmos DB felhasználókat és engedélyeket) is használhatja az Cosmos DB-fiókban lévő erőforrásokhoz való hozzáférés biztosításához ügyfél, amely nem adható meg megbízhatóként a főkulcs a kívánt.  

Cosmos DB erőforrás jogkivonatokat adjon meg egy biztonságos megoldás, amely lehetővé teszi az ügyfelek számára olvasási, írási és törlési a jogosultságaitól megfelelően, és egy fő vagy a kulcs csak olvasható nélkül a Cosmos DB fiók erőforrásai.

Íme egy tipikus kialakításban, amellyel erőforrás jogkivonatok kérhetők, jön létre, és ügyfelek felé:

1. Egy közepes réteg szolgáltatás megosztásához a felhasználó fényképeihez mobilalkalmazás kiszolgálására van beállítva. 
2. A közepes réteg szolgáltatás a fő oszlopkulcs Cosmos DB fiók rendelkezik.
3. Az fényképező alkalmazás telepítve van a végfelhasználói mobileszközökön. 
4. A bejelentkezés a fényképező alkalmazás létrehozza a felhasználó a közepes réteg szolgáltatással. A mechanizmus identitás létesítmény kizárólag az alkalmazás esetén.
5. Miután az identitása létrejött, a közepes réteg a szolgáltatáskérések engedélyek személyazonossága alapján.
6. A közepes réteg szolgáltatás elküldi egy erőforrás-jogkivonat a telefonalkalmazás.
7. A telefonalkalmazás továbbra is használhatja az erőforrás-jogkivonat erőforrásokhoz való közvetlen hozzáféréshez Cosmos DB definiálva az erőforrás-jogkivonat és a alatt, az erőforrás-jogkivonat által engedélyezett engedélyekkel. 
8. Amikor az erőforrás-jogkivonat lejár, a későbbi kérelmek kapnak a 401-es jogosulatlan kivételt.  Ezen a ponton a telefonalkalmazás újból létrehozza az identitás, és egy új erőforrás-jogkivonat kéri.

    ![Az Azure Cosmos DB erőforrás jogkivonatok munkafolyamat](./media/secure-access-to-data/resourcekeyworkflow.png)

Erőforrás-token létrehozása és kezelése végzi el a natív Cosmos DB ügyfél könyvtárakat. REST használatakor a kérelem/hitelesítési fejlécek kell létrehozni. A többi hitelesítési fejlécek létrehozásáról további információk: [Cosmos DB erőforrások hozzáférés-vezérlés](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) vagy a [forráskód az SDK-k](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

A középső réteg szolgáltatás létrehozásához vagy replikaszervező erőforrás jogkivonatok használt példáért lásd: a [ResourceTokenBroker app](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Felhasználók
Cosmos DB felhasználók hozzárendelve egy Cosmos DB adatbázisban.  Az egyes adatbázisok nulla vagy több Cosmos DB felhasználókat is tartalmazhat.  A következő példakód bemutatja, hogyan hozzon létre egy Cosmos DB felhasználói erőforrást.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Minden egyes Cosmos DB felhasználói segítségével listájának beolvasása PermissionsLink tulajdonsággal rendelkezik [engedélyek](#permissions) a felhasználóhoz társított.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Engedélyek
A Cosmos DB engedély erőforrás társítva egy Cosmos DB felhasználói.  Minden felhasználó nulla vagy több Cosmos DB engedélyeket is tartalmazhat.  Engedély erőforrás egy biztonsági jogkivonatot, amely a felhasználó számára szükséges egy adott alkalmazás erőforrás elérésére tett kísérlet során hozzáférést biztosít.
Van engedélye az erőforrás által is megadható két rendelkezésre álló hozzáférési szintek:

* Összes: A felhasználó teljes körű hozzáféréssel rendelkezik az erőforráson.
* Olvassa el: A felhasználó csak olvashatók a tartalmát az erőforrás, de nem hajtható végre, írási, update vagy delete művelethez az erőforráson.

> [!NOTE]
> Cosmos DB futtatásához tárolt eljárások a felhasználónak kell rendelkeznie az All engedély a gyűjteményt, amelyben a tárolt eljárás fog futni.
> 
> 

### <a name="code-sample-to-create-permission"></a>Kódminta engedély létrehozása

A következő példakód bemutatja, hogyan hozzon létre egy engedély erőforrást, olvassa el az erőforrás-jogkivonat a engedély erőforrás, és rendelje hozzá a az engedélyeket a [felhasználói](#users) a fenti létrehozott.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Ha a gyűjtemény adott meg partíciókulcsot, az engedély a gyűjtemény, a dokumentum és a melléklet erőforrások tartalmaznia mellett a ResourceLink ResourcePartitionKey.

### <a name="code-sample-to-read-permissions-for-user"></a>Olvassa el a felhasználó engedélyeit kódminta

Könnyen beszerzése az all engedély erőforrásokat egy adott felhasználóhoz társított, Cosmos DB teszi elérhetővé minden felhasználói objektum hírcsatorna engedély.  A következő kódrészletet bemutatja, hogyan beolvasni az előbb létrehozott felhasználói rendelt engedélynél, engedély listáját, és hozható létre egy új documentclient-ügyfelet a felhasználó nevében.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>További lépések
* Cosmos-adatbázis adatbázis-biztonsággal kapcsolatos további tudnivalókért lásd: [Cosmos DB: adatbázis-biztonsági](database-security.md).
* Fő és a csak olvasható kezelésével kapcsolatos információkért lásd: [Azure Cosmos DB fiók kezelése](manage-account.md#keys).
* Azure Cosmos DB engedélyezési jogkivonatok létrehozásához, lásd: [Azure Cosmos DB erőforrások hozzáférés-vezérlés](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
