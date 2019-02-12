---
title: Biztonsági szűrők szűkítheti az eredményeket az Active Directory – Azure Search
description: Hozzáférés-vezérlést a biztonsági szűrők és identitások Azure Active Directory (AAD) használatával az Azure Search-tartalom.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 3f55b3b099cc22fda2bebf0dcb8d3e9c1a580f02
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099694"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Az Azure Active Directory-identitások használatával találatok vágást biztonsági szűrők

Ez a cikk bemutatja, hogyan használja az Azure Active Directory (AAD) biztonsági identitások szűrők együtt az Azure Search szűkítheti a keresési eredmények a felhasználói csoporttagság alapján.

Ez a cikk a következő feladatokat mutatja be:
> [!div class="checklist"]
- AAD-csoportok és felhasználók létrehozása
- Társítsa a felhasználót a létrehozott csoport
- Az új csoportok gyorsítótárazása
- Index dokumentumokhoz kapcsolódó csoportokkal együtt
- Adjon ki egy keresési kérelmet csoport azonosítók szűrővel

>[!NOTE]
> Példa kódtöredékek ebben a cikkben C# nyelven íródtak. A teljes forráskódot a [GitHub](https://aka.ms/search-dotnet-howto) webhelyén találja. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure Search-index rendelkeznie kell egy [biztonsági mező](search-security-trimming-for-azure-search.md) tárolására a dokumentum olvasási hozzáféréssel rendelkező csoport identitások listájában. Ezt a használati esetet feltételezi, hogy egy az egyhez típusú egyezésnek egy biztonságos elem (például egy személy college alkalmazás) és a egy biztonsági mezőben adja meg, ki férhet hozzá az adott elem (tartózkodások csoporthoz) között.

Ez az útmutató az aad-felhasználók, csoportok és társítások létrehozásához szükséges, aad-ben rendszergazdai jogosultságokkal kell rendelkeznie.

Az alkalmazás is regisztrálni kell az aad-vel, az alábbi eljárásban leírtak szerint.

### <a name="register-your-application-with-aad"></a>Az alkalmazás regisztrálása az aad-vel

Ebben a lépésben az alkalmazás integrálja az aad-vel fogadja a felhasználói és csoportfiókok bejelentkezések céljából. Ha nem az AAD-rendszergazda a szervezetben, szüksége lehet [hozzon létre egy új bérlőt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) az alábbi lépések végrehajtásához.

1. Nyissa meg a [ **alkalmazásregisztrációs portálon**](https://apps.dev.microsoft.com) >  **összevont alkalmazás** > **alkalmazás hozzáadása**.
2. Adja meg egy nevet az alkalmazásnak, majd kattintson a **létrehozás**. 
3. Válassza ki az újonnan regisztrált alkalmazás a saját alkalmazások oldalán.
4. Az alkalmazás regisztrációs lapján > **platformok** > **hozzáadása Platform**, válassza a **webes API-t**.
5. Továbbra is a alkalmazás való regisztrációhoz, Ugrás > **Microsoft Graph-engedélyek** > **Hozzáadás**.
6. Az engedélyek válassza ki a következő delegált engedélyek hozzáadásához, majd kattintson **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

A Microsoft Graph API-, amely lehetővé teszi az aad-ben, egy REST API-val programozott hozzáférést biztosít. Ebben a lépésenkénti útmutatóban a kódminta az engedélyek használatával a Microsoft Graph API meghívása csoportok, a felhasználók és a társítások létrehozásához. Az API-k is használhatók a gyorsítótár csoport azonosítók gyorsabban szűréshez.

## <a name="create-users-and-groups"></a>Felhasználók és csoportok létrehozása

Keresés hozzáadásakor egy meglévő alkalmazás meglévő felhasználó és csoport azonosítók előfordulhat az aad-ben. Ebben az esetben a következő három lépést kihagyhatja. 

Azonban ha nem rendelkezik meglévő felhasználók, használhatja a Microsoft Graph API-k létrehozása a biztonsági rendszerbiztonsági tagok. Az alábbi kódtöredékek bemutatják, hogyan lehet azonosítók válik az adatok értékeit a biztonság területén, az Azure Search-index létrehozása. Elméleti college tartózkodások alkalmazásunkat ez lenne a biztonsági azonosítók tartózkodások személyzetnek.

Felhasználó és csoport tagságának lehet nagyon képlékeny, különösen a nagy szervezetek is használnak. Kód, amely összeállítja a felhasználó- és identitások elég gyakran kell futnia a szervezet tagsági módosításainak életbe léptetéséhez. Hasonlóképpen az Azure Search-index az engedélyezett felhasználók és erőforrások aktuális állapotát tükröző hasonló frissítési ütemezést igényelnek.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>1. lépés: Hozzon létre [AAD-csoport](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>2. lépés: Hozzon létre [AAD-felhasználó](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>3. lépés: Felhasználók és csoportok hozzárendelése
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>4. lépés: A csoportok azonosítók gyorsítótárazása
Igény szerint hálózati késés csökkentése érdekében gyorsítótárazhatja a felhasználócsoport-társítások úgy, hogy egy keresési kérelmet ad ki, amikor csoportokat a rendszer adja vissza a gyorsítótár mentése folyamatban van egy körbejárási aad-ben. Használhat [AAD Batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) több felhasználóval rendelkező egyetlen Http-kérés küldése és a gyorsítótár létrehozása.

A Microsoft Graph úgy lett kialakítva, nagy mennyiségű kérelmet kezelni. Ha túlságosan sok kérelem fordul elő, a Microsoft Graph 429-es HTTP-állapotkód: a kérelem sikertelen lesz. További információkért lásd: [Microsoft Graph-szabályozás](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Engedélyezett csoporthoz tartozó index dokumentum

Az Azure Search lekérdezési műveletek végrehajtása az Azure Search-index keresztül. Ebben a lépésben egy indexelési művelet importálja a kereshető adatok index, beleértve a biztonsági szűrők használt azonosítókat. 

Az Azure Search nem felhasználói identitások hitelesítésére, vagy adjon meg logikai létrehozó, mely tartalmakat a felhasználó megtekintéséhez rendelkezik engedéllyel. A használati eset biztonságiadat azt feltételezi, hogy biztosítson egy bizalmas dokumentumot és a dokumentum, a keresési index átvenni importálva hozzáférés azonosítója közötti társítás. 

A képzeletbeli példában az Azure Search-index a PUT kérés törzsében tartalmazhat egy kérelmező college jelentésében vagy szöveges együtt a tartalom megtekintéséhez engedély kellene azonosítója. 

Az ebben a lépésenkénti útmutatóban a kódmintában használt általános példában az index művelet hasonlóan néz:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Egy keresési kérelmet

A biztonság tisztítás érdekében a biztonsági mezőt az indexben szereplő értékek értékek statikus tartalmazza a dokumentumok a keresési eredmények között, vagy használt. Például ha tartózkodások csoport azonosítója "A11B22C33D44-E55F66G77-H88I99JKK", minden olyan Azure Search-index az iktatott biztonsági megadott azonosítóval rendelkező dokumentumot is tartalmazza (vagy kizárt) vissza a kérelmezőnek küldött, a keresési eredmények között.

A dokumentumok a kérést kiadó felhasználó csoportok alapján, a keresési eredmények szűréséhez, tekintse át az alábbi lépéseket.

### <a name="step-1-retrieve-users-group-identifiers"></a>1. lépés: Felhasználói csoport azonosítók beolvasása

Ha a felhasználói csoportok nem már lettek gyorsítótárazva, vagy lejárt a gyorsítótár, a [csoportok](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) kérelem
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>2. lépés: Állítsa össze a keresési kérelem

Feltételezve, hogy a felhasználói csoportok tagságát, a megfelelő szűrési értékekkel a keresési kérelem adhat ki.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>3. lépés: Az eredmények kezelése

A válasz álló, amelyek a felhasználó jogosult megtekinteni, a dokumentumok szűrt listáját tartalmazza. Attól függően, hogyan hozhat létre a keresési eredmények oldalát előfordulhat, hogy szeretné, hogy a szűrt eredményhalmaz vizuális jelek közé tartozik.

## <a name="conclusion"></a>Összegzés

Ez az útmutató megtanulta, technikák használatával AAD bejelentkezések szűrése dokumentumok Azure keresési eredmények között, az eredményeket a dokumentum, amely nem egyezik a kérés esetén megadott szűrő-csonkolás.

## <a name="see-also"></a>Lásd még

+ [Identitásalapú hozzáférés-vezérlés az Azure Search-szűrők használata](search-security-trimming-for-azure-search.md)
+ [Szűrők az Azure Search szolgáltatásban](search-filters.md)
+ [Adatok biztonsági és hozzáférés-vezérlés az Azure Search-műveletek](search-security-overview.md)
