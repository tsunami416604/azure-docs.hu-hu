---
title: "Az Azure keresési találatok Active Directory identitásokkal levágási biztonsági szűrők |} Microsoft Docs"
description: "Hozzáférés-vezérlés az Azure Search tartalmakat a biztonsági szűrők és az Active Directory-identitás használatával."
services: search
author: revitalbarletz
manager: jlembicz
ms.service: search
ms.topic: article
ms.date: 11/07/2017
ms.author: revitalb
ms.openlocfilehash: 2113b59d6fec15067acbef8b4d4c1fc34c141e62
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Az Azure Active Directory identitásokkal találatok díszítésre biztonsági szűrők

Ez a cikk bemutatja, hogyan használható az Azure Active Directory (AAD) biztonsági azonosítók szűrők együtt az Azure Search lehet levágni a keresési eredmények felhasználói csoporttagság alapján.

Ez a cikk ismerteti a következő feladatokat:
> [!div class="checklist"]
- AAD-csoportok és felhasználók létrehozása
- Rendelje hozzá a felhasználó hozott létre a csoport
- Az új csoportok gyorsítótárazása
- Index dokumentumok kapcsolódó csoportokkal együtt
- Adjon ki egy keresési kérelmet csoport azonosítók szűrővel

>[!NOTE]
> Minta kódtöredékek ebben a cikkben C# nyelven íródtak. A teljes forráskódot a [GitHub](http://aka.ms/search-dotnet-howto) webhelyén találja. 

## <a name="prerequisites"></a>Előfeltételek

Az index az Azure Search rendelkeznie kell egy [biztonsági mező](search-security-trimming-for-azure-search.md) a dokumentum olvasási hozzáféréssel rendelkező csoport azonosítók listája tárolásához. A használati eset azt feltételezi, hogy egy-az-egyhez levelezést egy biztonságos elem (például egy adott felsőoktatási alkalmazás) és egy biztonsági mező, ki férhet hozzá, hogy az elem (belépési személyzet) megadása között.

Az AAD-rendszergazdai engedélyekkel, a forgatókönyv az aad-ben a felhasználók, csoportok és társításokat létrehozásához szükséges kell rendelkeznie.

Az alkalmazás is regisztrálni kell az aad-ben, a következő eljárásban leírtak szerint.

### <a name="register-your-application-with-aad"></a>Az alkalmazás regisztrálása az aad-ben

Ezt a lépést az alkalmazás integrálja az aad-ben felhasználói és csoportfiókok bejelentkezések átvételére. Ha nem az AAD-rendszergazda a szervezetében, szükség lehet [hozzon létre egy új bérlőt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) a következő lépések végrehajtására.

1. Lépjen a [ **alkalmazásregisztrációs portálra**](https://apps.dev.microsoft.com) >  **összevont alkalmazás** > **hozzáadhat egy alkalmazást**.
2. Adja meg az alkalmazás nevét, majd kattintson a **létrehozása**. 
3. Az alkalmazások saját oldalon válassza ki az újonnan regisztrált alkalmazását.
4. Az alkalmazás regisztrációs oldalon > **platformok** > **hozzáadása Platform**, válassza a **Web API**.
5. Továbbra is az alkalmazás regisztrációja lapon Ugrás > **Microsoft Graph engedélyek** > **Hozzáadás**.
6. Válassza ki az engedélyeket, adja hozzá a következő delegált engedélyekkel, és kattintson a **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph egy API-t, amely lehetővé teszi a REST API-n keresztül AAD programozott hozzáférést biztosít. A forgatókönyv a kódmintában a engedélyeket csoportok, a felhasználók és a társítást a Microsoft Graph API hívásához használandó használja. Az API-kat is használhatók gyorsítótár csoportazonosítókhoz gyorsabban szűréséhez.

## <a name="create-users-and-groups"></a>Felhasználók és csoportok létrehozása

Keresési hozzáadni egy meglévő alkalmazást, ha lehetséges, hogy meglévő felhasználók és csoportazonosítókhoz az aad-ben. Ebben az esetben ugorjon a következő három lépést. 

Azonban ha nem rendelkezik meglévő felhasználók, használhatja Microsoft Graph API-k rendszerbiztonsági tagoknak a biztonsági létrehozásához. Az alábbi kódtöredékek bemutatják, hogyan lehet azonosítók, az Azure Search-index biztonsági mezője adatértékeket váló létrehozásához. Az elméleti felsőoktatási belépési alkalmazás ez lenne a biztonsági azonosítók belépési személyzetet.

Felhasználó és csoport tagságának lehet nagyon folyékony, különösen a nagy méretű szervezeteknek. Felhasználó- és identitások épülő kód szervezet tagság módosítások elég gyakran kell futtatnia. Hasonlóképpen az Azure Search-index szükséges a hasonló frissítési ütemezés megfelelően engedélyezett felhasználókat és erőforrásokat az aktuális állapotát.

### <a name="step-1-create-aad-grouphttpsdevelopermicrosoftcomgraphdocsapi-referencev10apigrouppostgroups"></a>1. lépés: Hozzon létre [AAD csoport](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/group_post_groups) 
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
   
### <a name="step-2-create-aad-userhttpsdevelopermicrosoftcomgraphdocsapi-referencev10apiuserpostusers"></a>2. lépés: Hozzon létre [AAD-felhasználó](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_post_users) 
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

### <a name="step-4-cache-the-groups-identifiers"></a>4. lépés: Csoportok azonosítók gyorsítótárazása
Szükség esetén a hálózati késés csökkentésére, gyorsítótárazhatja a felhasználócsoport-társítást, hogy a keresési kérelem elküldésekor csoportok rendszer adja vissza a gyorsítótárból, egy körbejárási mentése az aad-be. Használhatja (AAD kötegelt API-t) [https://developer.microsoft.com/graph/docs/concepts/json_batching] több felhasználóval rendelkező egyetlen Http-kérelem küldéséhez, és a gyorsítótár létrehozása.

A Microsoft Graph nagy mennyiségű kérést kezelésére terveztek. Kérelmek túlságosan száma fordulhat elő, ha a Microsoft Graph a HTTP-állapotkód 429 kérelem sikertelen lesz. További információkért lásd: [Microsoft Graph-szabályozás](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>A dokumentum index az engedélyezett csoportokkal

Az Azure Search lekérdezési műveletek végrehajtása az Azure Search-index keresztül. Ebben a lépésben az indexelési művelet kereshető adatokat importál egy index, többek között az azonosítók biztonsági használhatja. 

Az Azure Search nem személyazonosság igazolása a felhasználó, vagy adjon meg logikai létrehozó, milyen tartalmakat a felhasználó megtekintésére jogosult. A használati eset biztonsági Elrejtés a azt feltételezi, hogy megadta-e egy bizalmas dokumentumot és a csoport azonosító is hozzáférhetnek a dokumentum, egy keresési indexszel átvenni importálni társítását. 

A képzeletbeli példában az Azure Search-index a PUT kérés törzsében egy kérelmező felsőoktatási jelentésében vagy együtt, hogy a tartalmak megtekintésére vonatkozó jogosultság csoportazonosítója másolatként tartalmazhat. 

Az általános példában a kódmintában a forgatókönyv az index művelet a következő lehet:

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

## <a name="issue-a-search-request"></a>Adjon ki egy keresési kérelmet

Tisztítás biztonsági okokból az indexben a biztonsági mező értékei statikus értékeket is beleértve vagy nélkülük dokumentumok a találatok között. Például ha belépési csoport azonosítója "A11B22C33D44-E55F66G77-H88I99JKK", a dokumentumok a megadott azonosítóval rendelkező bejegyezve biztonság Azure Search-index tartalmazza (és kizárt) a keresési eredmények között küldött a kérelmező.

A dokumentumok keresési eredményeiben a kérelmet a felhasználói csoportok alapján szűrni, tekintse át az alábbi lépéseket.

### <a name="step-1-retrieve-users-group-identifiers"></a>1. lépés: Felhasználó csoportazonosítókhoz beolvasása

Ha a felhasználói csoportok már nem gyorsítótárazott, vagy lejárt a gyorsítótár, a [csoportok](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/directoryobject_getmembergroups) kérelem
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

### <a name="step-2-compose-the-search-request"></a>2. lépés: A keresési kérelem összeállítása

Feltéve, hogy a felhasználói csoportok tagsága, a megfelelő szűrő értékekkel a keresési kérelem adhat ki.

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

A válasz, amely a felhasználó jogosult megtekinteni álló dokumentumok szűrt listáját tartalmazza. Attól függően, hogyan hozható létre a keresési eredmények oldalát előfordulhat, hogy szeretne vizuális jelek a szűrt eredménykészlet megfelelően.

## <a name="conclusion"></a>Összegzés

Ebben a bemutatóban megismerte technikák használatával AAD bejelentkezések dokumentumok Azure keresési eredmények szűréséhez díszítésre dokumentumok, amelyek nem felelnek meg a kérés esetén megadott szűrő eredményeit.

## <a name="see-also"></a>Lásd még:

+ [Azure Search szűrőkkel azonosító-alapú hozzáférés-vezérlés](search-security-trimming-for-azure-search.md)
+ [Az Azure Search szűrők](search-filters.md)
+ [Adatok biztonsági és hozzáférés-vezérlés az Azure Search műveletek](search-security-overview.md)
