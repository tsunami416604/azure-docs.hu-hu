---
title: Az eredmények az Active Directory használatával történő vágásához használt biztonsági szűrők
titleSuffix: Azure Cognitive Search
description: Hozzáférés-vezérlés az Azure Cognitive Search tartalom biztonsági szűrők és az Azure Active Directory (AAD) identitások használatával.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794301"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Biztonsági szűrők az Azure Cognitive Search eredményeinek az Active Directory-identitások használatával történő vágásához

Ez a cikk bemutatja, hogyan használhatja az Azure Active Directory (AAD) biztonsági identitások és szűrők az Azure Cognitive Search a keresési eredmények a felhasználói csoporttagság alapján.

Ez a cikk a következő feladatokat mutatja be:
> [!div class="checklist"]
> - AAD-csoportok és felhasználók létrehozása
> - A felhasználó társítása a létrehozott csoporttal
> - Az új csoportok gyorsítótárazása
> - Dokumentumok indexelése társított csoportokkal
> - Keresési kérelem kiadása csoportazonosítók szűrővel
> 
> [!NOTE]
> A cikkben szereplő mintakódrészletek C#-ban vannak megírva. A teljes forráskódot a [GitHub](https://aka.ms/search-dotnet-howto) webhelyén találja. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Search indexének rendelkeznie kell egy [biztonsági mezővel](search-security-trimming-for-azure-search.md) a dokumentumhoz olvasási hozzáféréssel rendelkező csoportidentitások listájának tárolásához. Ez a használati eset egy-az-egyhez megfelelést feltételez egy biztonságos cikk (például egy személy főiskolai jelentkezése) és egy biztonsági mező között, amely meghatározza, hogy ki férhet hozzá az adott tételhez (felvételi személyzet).

Az AAD-rendszergazdai engedélyekkel kell rendelkeznie, amelyek a forgatókönyvben szükségesek a felhasználók, csoportok és társítások aAAD-ban való létrehozásához.

A jelentkezését regisztrálni kell az AAD-nél is, az alábbi eljárásnak megfelel.

### <a name="register-your-application-with-aad"></a>A jelentkezés regisztrálása az AAD-nál

Ez a lépés integrálja az alkalmazást az AAD-vel a felhasználói és csoportfiókok bejelentkezéseinek elfogadása céljából. Ha nem AAD-rendszergazda a szervezetben, előfordulhat, hogy létre kell [hoznia egy új bérlőt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) a következő lépések végrehajtásához.

1. Nyissa meg az [**Alkalmazásregisztrációs portál**](https://apps.dev.microsoft.com) >  **konvergens alkalmazás** > **alkalmazás hozzáadása**.
2. Adja meg az alkalmazás nevét, majd kattintson a **Létrehozás gombra.** 
3. Válassza ki az újonnan regisztrált alkalmazást a Saját alkalmazások lapon.
4. Az alkalmazásregisztrációs lapon > **Platformok** > **platform hozzáadása**lapon válassza a Webes **API**lehetőséget.
5. Még mindig az alkalmazás regisztrációs lapján nyissa meg > **Microsoft Graph engedélyek** > **hozzáadása című lapot.**
6. Az Engedélyek kiválasztása csoportban adja hozzá a következő delegált engedélyeket, majd kattintson az **OK**gombra:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

A Microsoft Graph olyan API-t biztosít, amely lehetővé teszi az AAD programozott elérését egy REST API-n keresztül. A forgatókönyv kódmintája a Microsoft Graph API hívására vonatkozó engedélyeket használja csoportok, felhasználók és társítások létrehozásához. Az API-k is használják a csoportazonosítók gyorsítótárazására a gyorsabb szűrés érdekében.

## <a name="create-users-and-groups"></a>Felhasználók és csoportok létrehozása

Ha egy létrehozott alkalmazáshoz ad hozzá keresést, előfordulhat, hogy meglévő felhasználói és csoportazonosítókkal rendelkezik az AAD-ben. Ebben az esetben kihagyhatja a következő három lépést. 

Ha azonban nem rendelkezik meglévő felhasználókkal, a Microsoft Graph API-k segítségével létrehozhatja a rendszerbiztonsági tagokat. A következő kódrészletek bemutatják, hogyan hozhat létre azonosítókat, amelyek az Azure Cognitive Search index biztonsági mezőjének adatértékeivé válnak. A mi hipotetikus főiskolai felvételi kérelmet, ez lenne a biztonsági azonosítók felvételi személyzet.

A felhasználói és csoporttagság nagyon gördülékeny lehet, különösen a nagy szervezeteknél. A felhasználói és csoportidentitásokat létrehozó kódnak elég gyakran kell futnia ahhoz, hogy felvegye a szervezeti tagság változásait. Hasonlóképpen az Azure Cognitive Search indexéhez is hasonló frissítési ütemezésre van szükség, hogy tükrözze az engedélyezett felhasználók és erőforrások aktuális állapotát.

### <a name="step-1-create-aad-group"></a>1. lépés: [AAD-csoport](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) létrehozása 
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
   
### <a name="step-2-create-aad-user"></a>2. lépés: [AAD-felhasználó](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0) létrehozása
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

### <a name="step-3-associate-user-and-group"></a>3. lépés: Felhasználó és csoport társítása
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>4. lépés: A csoportazonosítók gyorsítótárazása
A hálózati késés csökkentése érdekében gyorsítótárazni tudja a felhasználói csoporttársításokat, hogy a keresési kérelem kiadásakor a rendszer csoportokat küldjön vissza a gyorsítótárból, és mentsen egy oda-vissza utat az AAD-re. [Az AAD Batch API segítségével](https://developer.microsoft.com/graph/docs/concepts/json_batching) egyetlen Http-kérelmet küldhet több felhasználóval, és létrehozhatja a gyorsítótárat.

A Microsoft Graph nagy mennyiségű kérelem kezelésére szolgál. Ha túlnyomó számú kérelem történik, a Microsoft Graph a 429-es HTTP-állapotkóddal rendelkező kérést meghiúsítja. További információt a [Microsoft Graph szabályozása](https://developer.microsoft.com/graph/docs/concepts/throttling)című témakörben talál.

## <a name="index-document-with-their-permitted-groups"></a>Bizonylat indexelése az engedélyezett csoportokkal

Az Azure Cognitive Search lekérdezési műveletei az Azure Cognitive Search indexen keresztül kerülnek végrehajtásra. Ebben a lépésben egy indexelő művelet importálja a kereshető adatokat egy indexbe, beleértve a biztonsági szűrőkként használt azonosítókat is. 

Az Azure Cognitive Search nem hitelesíti a felhasználói identitásokat, és nem biztosít logikát annak megállapításához, hogy a felhasználó melyik tartalom megtekintésére jogosult. A biztonsági vágás használati esete feltételezi, hogy ön adja meg a kapcsolatot egy bizalmas dokumentum és a dokumentumhoz hozzáféréssel rendelkező csoportazonosító között, amelyet sértetlenül importál egy keresési indexbe. 

A hipotetikus példában a szervezet a PUT-kérelem egy Azure Cognitive Search index tartalmazná a kérelmező főiskolai esszé vagy átirata mellett a csoport azonosítója, amelynek engedélye van a tartalom megtekintéséhez. 

A forgatókönyv kódmintájában használt általános példában az indexművelet a következőképpen nézhet ki:

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

## <a name="issue-a-search-request"></a>Keresési kérelem kiadása

Biztonsági vágási célokra az index biztonsági mezőjében lévő értékek statikus értékek, amelyek a dokumentumok keresési eredményekben való felolására vagy kizárására szolgálnak. Ha például a felvételi csoport azonosítója "A11B22C33D44-E55F66G77-H88I99JKKK", az Azure Cognitive Search indexben lévő, a megadott azonosítóval rendelkező dokumentumok szerepelnek (vagy ki vannak zárva) a kérelmezőnek visszaküldött keresési eredményekben.

A keresési eredményekben visszaadott dokumentumok szűréséhez tekintse át a következő lépéseket a kérelmet kiállító felhasználó csoportjai alapján.

### <a name="step-1-retrieve-users-group-identifiers"></a>1. lépés: A felhasználó csoportazonosítóinak lekérése

Ha a felhasználó csoportjai még nem voltak gyorsítótárazva, vagy a gyorsítótár lejárt, adja ki a [csoportok](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) kérését
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

Feltéve, hogy rendelkezik a felhasználó csoporttagságával, a keresési kérelmet a megfelelő szűrőértékekkel bocsáthatja ki.

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

A válasz a dokumentumok szűrt listáját tartalmazza, amely azokat tartalmazza, amelyek megtekintésére a felhasználó jogosult. Akeresési eredmények lapjának felépítésétől függően érdemes lehet vizuális jelzéseket is megadni a szűrt eredményhalmaznak megfelelően.

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben megtanulta az AAD-bejelentkezések használatával a dokumentumok szűrésére az Azure Cognitive Search eredményeinek szűréséhez, és levágja a kérésben megadott szűrőnek nem megfelelő dokumentumok eredményeit.

## <a name="see-also"></a>Lásd még

+ [Identitásalapú hozzáférés-vezérlés az Azure Cognitive Search szűrőivel](search-security-trimming-for-azure-search.md)
+ [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
+ [Adatbiztonság és hozzáférés-vezérlés az Azure Cognitive Search műveleteiben](search-security-overview.md)
