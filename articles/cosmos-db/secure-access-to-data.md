---
title: Ismerje meg, hogyan biztonságos hozzáférés az adatokhoz az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg, access control fogalmaival, az Azure Cosmos DB, beleértve a főkulcsok, csak olvasható kulcsok, felhasználókat és engedélyeket.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/19/2018
ms.author: rafats
ms.openlocfilehash: ed97a2c31897d1e5e61421ea489a35af377f4f37
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621445"
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Azure Cosmos DB-adatokhoz való hozzáférés biztonságossá tétele
Ez a cikk áttekintést tárolt adatokhoz való hozzáférés biztonságossá tétele [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Az Azure Cosmos DB kétféle típusú kulcsok használatával hitelesítheti a felhasználókat, és adja meg az adatok és erőforrások elérését. 

|Kulcs típusa|További források|
|---|---|
|[Mesterkulcs](#master-keys) |Felügyeleti erőforrásainak: adatbázis-fiókok, adatbázisok, felhasználók és engedélyek|
|[Erőforrás-jogkivonatokat](#resource-tokens)|Alkalmazás erőforrásainak: tárolók, dokumentumok, a mellékleteket, tárolt eljárások, eseményindítók és felhasználói függvények|

<a id="master-keys"></a>

## <a name="master-keys"></a>Mesterkulcs 

Mesterkulcs biztosítanak az erőforrásokhoz való teljes hozzáférés a felügyeleti az adatbázis fiókjához tartozó. Fő kulcsok:  
- Fiókok, adatbázisok, felhasználók és engedélyek hozzáférést biztosítanak. 
- Nem használható a tárolókhoz és a dokumentumok testre szabott hozzáférés biztosításához.
- Fiók létrehozása során jönnek létre.
- Bármikor helyreállíthatja.

Minden fiók két főkulcsok áll: egy elsődleges és másodlagos kulcsot. A kettős kulcsok célja, hogy hozza létre újra, vagy visszaállítani, kulcsok biztosít folyamatos hozzáférést fiókját és adatait. 

A Cosmos DB-fiók két fő kulcsok kívül két csak olvasható kulcsok. A csak olvasható kulcsokat csak engedélyezze a fiókot az olvasási műveletek. Írásvédett kulcsok nem biztosítanak engedélyeket erőforrások olvasási hozzáférést.

Elsődleges, másodlagos csak olvasható és írható-olvasható főkulcsok lekérhető és újragenerálta az Azure portal használatával. Útmutatásért lásd: [megtekintése, másolása és újragenerálása tárelérési kulcsok](manage-with-cli.md#regenerate-account-key).

![Hozzáférés-vezérlés (IAM) bemutatására NoSQL adatbázis-biztonság – az Azure Portalon](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

A főkulcs Elforgatás folyamata használata egyszerű. Keresse meg az Azure Portalra, kérje le a másodlagos kulcsot, majd cserélje le az elsődleges kulcsot az alkalmazás a másodlagos kulcsot, majd az Azure Portalon az elsődleges kulcs rotálása.

![A főkulcs Elforgatás bemutatásához NoSQL adatbázis-biztonság – az Azure Portalon](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Kódminta főkulcs használata

A következő példakód azt ábrázolja, hogyan használja a Cosmos DB-fiók végpontját és a főkulcs egy DocumentClient-példányt létrehozni, és hozzon létre egy adatbázist. 

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

## <a name="resource-tokens"></a>Erőforrás-jogkivonatokat

Erőforrás-jogkivonatokat adatbázison belüli alkalmazás-erőforrásokhoz való hozzáférést biztosítanak. Erőforrás-jogkivonatokat:
- Adott tárolók, partíciókulcsok, dokumentumok, a mellékleteket, tárolt eljárások, eseményindítók és felhasználói függvények hozzáférést biztosítanak.
- Jönnek létre, amikor egy [felhasználói](#users) kap [engedélyek](#permissions) egy adott erőforráshoz.
- Létrejönnek, amikor engedélyt erőforrás van intézkedni a POST, GET vagy PUT hívás által.
- Használjon egy kivonatot erőforrás-jogkivonat épített kifejezetten a felhasználó, az erőforrás és engedélyt.
- Kötve egy testre szabható érvényességi időt. Az alapértelmezett érvényes időtartam érték egy óra. Jogkivonat élettartama, azonban előfordulhat, hogy explicit módon megadni, legfeljebb 5 óra.
- Adjon meg egy biztonságos alternatívája lehet a főkulcs kiadása. 
- Engedélyezése az ügyfelek számára olvasási, írási és törlési azok szerez engedélyeinek megfelelően a Cosmos DB-fiók erőforrásokat.

Használhat egy erőforrás-jogkivonat (Cosmos DB-felhasználók és engedélyek létrehozása) Ha lehetővé szeretné tenni a Cosmos DB-fiókban lévő erőforrásokhoz való hozzáférést egy ügyfélre, mely nem megbízható főkulccsal.  

A cosmos DB erőforrás-jogkivonatokat adjon meg egy biztonságos megoldás, amely lehetővé teszi az ügyfelek számára, hogy az olvasási, írási és törlési erőforrásokat a Cosmos DB-fiókban megfelelően a jogosultságaitól és a master és a kulcs csak olvasható nélkül.

Íme egy jellemző tervezési minta, amellyel erőforrás-jogkivonatokat kérhetők, jön létre, és ügyfelek felé:

1. A középső rétegbeli szolgáltatás szolgálja ki a felhasználói fényképek megosztására mobilalkalmazás van beállítva. 
2. A középső rétegbeli szolgáltatás a főkulcs a Cosmos DB-fiók birtokában van.
3. Az alkalmazás végfelhasználói mobileszközökön telepítve van. 
4. A bejelentkezés az alkalmazás létrehozza a felhasználó a középső rétegbeli szolgáltatással. Ez a mechanizmus az identitás létrehozása kizárólag az alkalmazás esetén.
5. Ha az identitás létrejött, akkor a középső rétegbeli szolgáltatás az identitása alapján engedélyt kér.
6. A középső rétegbeli szolgáltatás elküldi egy erőforrás-jogkivonat a telefonos alkalmazást.
7. A telefonos alkalmazást továbbra is az erőforrás-jogkivonat használatával közvetlenül az erőforrás-jogkivonat és az erőforrás-jogkivonat által engedélyezett az az időtartam alatt meghatározott engedélyekkel a Cosmos DB-erőforrások eléréséhez. 
8. Ha az erőforrás-jogkivonat lejár, későbbi kérelmeket fogadni a 401-es jogosulatlan kivétel.  Ezen a ponton a telefonalkalmazás újból létrehozza az identitást, és kéri egy új erőforrás-jogkivonat.

    ![Az Azure Cosmos DB erőforrás-jogkivonatok munkafolyamat](./media/secure-access-to-data/resourcekeyworkflow.png)

Erőforrás-jogkivonat létrehozása és kezelése a natív Cosmos DB-klienskódtárakkal; kezeli Ha a REST használata a kérelem/hitelesítési fejléceket kell létrehozniuk. A hitelesítési fejléceket hoz létre REST további információkért lásd: [Cosmos DB-erőforrások hozzáférés-vezérlés](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) vagy a [forráskódját az SDK-Ink](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

A középső réteg szolgáltatás készítése vagy erőforrás-jogkivonatokat közvetítse egy példa: a [ResourceTokenBroker alkalmazás](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Felhasználók
Cosmos DB felhasználók hozzárendelve a Cosmos DB-hez.  Minden adatbázis nulla vagy több Cosmos DB-felhasználókat is tartalmazhat.  Az alábbi példakód bemutatja, hogyan hozhat létre egy Cosmos DB felhasználói erőforrás.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Egyes Cosmos DB-felhasználók listájának beolvasása használható PermissionsLink tulajdonsággal rendelkezik [engedélyek](#permissions) a felhasználóhoz társított.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Engedélyek
Egy Cosmos DB-engedély erőforráshoz társítva a Cosmos DB-felhasználó.  Minden felhasználó nulla vagy több Cosmos DB-engedélyeket is tartalmazhat.  Engedély erőforrás egy biztonsági jogkivonatot, amely a felhasználónak egy adott alkalmazás erőforrás elérésére tett kísérlet során hozzáférést biztosít.
Nincsenek két elérhető hozzáférési szintek engedéllyel az erőforrás által biztosított:

* All: A felhasználó teljes körű hozzáféréssel rendelkezik az erőforráson.
* Olvasás: A felhasználó az erőforrás a tartalmát csak olvasható, de nem hajtható végre, írási, frissítési vagy törlési műveletek az erőforráson.

> [!NOTE]
> Annak érdekében, hogy futtassa a Cosmos DB tárolt eljárásokat, a felhasználó az összes engedéllyel kell rendelkeznie a tárolóhoz, amelyben a tárolt eljárás futni fog.
> 
> 

### <a name="code-sample-to-create-permission"></a>Kódminták az engedélyek létrehozása

Az alábbi példakód bemutatja, hogyan engedély erőforrás létrehozása, olvassa el az erőforrás-jogkivonat az engedély erőforrás és az engedélyek hozzárendelése a [felhasználói](#users) fent létrehozott.

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

Ha a gyűjtemény a megadott egy partíciókulcsot, majd az engedély a gyűjtemény, a dokumentum és a melléklet erőforrások is tartalmaznia kell a ResourcePartitionKey mellett a ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Kódmintát, hogy a felhasználó engedélyeinek olvasása

Könnyedén beszerzése az all engedély erőforrások társított egy adott felhasználó, a Cosmos DB révén az elérhető minden felhasználói objektum hírcsatorna engedélyt.  A következő kódrészletet bemutatja, hogyan lekérni az engedélyt, a fent létrehozott felhasználóhoz tartozó, egy engedélylistában hozhatnak létre és példányt létrehozni egy új documentclient-ügyfelet a felhasználó nevében.

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

## <a name="add-users-and-assign-roles"></a>Felhasználók hozzáadása és szerepkörök hozzárendelése

Azure Cosmos DB fiók olvasói hozzáférés hozzáadása a felhasználói fiókjával, rendelkeznie egy előfizetés tulajdonosa, hajtsa végre az alábbi lépéseket az Azure Portalon.

1. Nyissa meg az Azure Portalon, és válassza ki az Azure Cosmos DB-fiókját.
2. Kattintson a **hozzáférés-vezérlés (IAM)** fülre, majd **+ Hozzáadás**.
3. Az a **engedélyek hozzáadása** ablaktáblán, a a **szerepkör** jelölje ki **Cosmos DB-fiók olvasói szerepköre**.
4. Az a **hozzáférés hozzárendelése a boxba**válassza **az Azure AD-felhasználó, csoport vagy alkalmazás**.
5. Válassza ki a címtárban, amelyhez hozzáférést szeretne a felhasználó, csoport vagy alkalmazás.  A címtár megjelenített név, e-mail címét vagy objektumazonosítók kereshet.
    A kiválasztott felhasználó, csoport vagy alkalmazás a kijelölt tagok listájában jelenik meg.
6. Kattintson a **Save** (Mentés) gombra.

Az entitás már képes olvasni az Azure Cosmos DB erőforrásokat.

## <a name="delete-or-export-user-data"></a>Törölje vagy felhasználói adatok exportálása
Az Azure Cosmos DB lehetővé teszi, keresését, válassza ki, módosítani és törölni az adatbázisban vagy gyűjtemények található személyes adatok. Azure Cosmos DB API-k megkeressék és töröljék a személyes adatok azonban biztosítja, hogy az a feladata, hogy az API-kkal és a személyes adatok törlésére szükséges logika definiálható. Minden egyes többmodelles API (SQL API-t, a MongoDB API-t, Gremlin API, Cassandra API, Table API) biztosít a különböző nyelvű SDK-k keresését, és törölje a személyes adatokat metódusokat tartalmaznak. Is engedélyezheti a [idő Élettartam (TTL)](time-to-live.md) funkció törli az adatokat automatikusan egy megadott időszak után járó minden további költség nélkül.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések
* Cosmos DB-adatbázis biztonsági kapcsolatos további információkért lásd: [Cosmos DB: adatbázis-biztonság](database-security.md).
* Ismerje meg, hogyan hozhat létre Azure Cosmos DB engedélyezési jogkivonatok, lásd: [hozzáférés-vezérlés az Azure Cosmos DB-erőforrások](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
