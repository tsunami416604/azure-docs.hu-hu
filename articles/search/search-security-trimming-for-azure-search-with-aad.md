---
title: Az eredmények a Active Directory használatával történő levágására szolgáló biztonsági szűrők
titleSuffix: Azure Cognitive Search
description: Biztonsági jogosultságok a dokumentum szintjén az Azure Cognitive Search keresési eredmények, biztonsági szűrők és Azure Active Directory (HRE) identitások használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: ee742eae38ae95756cf31d60b877f18629c569d4
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080490"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Az Azure Cognitive Search eredményeinek Active Directory identitások használatával történő kivágására szolgáló biztonsági szűrők

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory (HRE) biztonsági identitások az Cognitive Search Azure-beli szűrőkkel együtt, hogy a keresési eredményeket a felhasználói csoporttagság alapján vágja le.

Ez a cikk a következő feladatokat mutatja be:
> [!div class="checklist"]
> - HRE-csoportok és-felhasználók létrehozása
> - A felhasználó hozzárendelése a létrehozott csoporthoz
> - Az új csoportok gyorsítótárazása
> - Dokumentumok indexelése társított csoportokkal
> - Keresési kérelem kibocsátása csoportazonosító szűrővel
> 
> [!NOTE]
> A cikkben szereplő kódrészletek a C# nyelven íródnak. A teljes forráskódot a [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started) webhelyén találja. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Searchban lévő indexnek rendelkeznie kell egy olyan [biztonsági mezővel](search-security-trimming-for-azure-search.md) , amely a dokumentum olvasási hozzáféréssel rendelkező csoport-identitások listáját tárolja. Ez a használati eset feltételezi, hogy egy-az-egyhez a biztonságos elemek (például az egyéni főiskolai alkalmazások) és egy biztonsági mező, amely meghatározza, hogy ki férhet hozzá az adott tételhez (a felvételi személyzethez).

A HRE-ben a felhasználók, csoportok és társítások létrehozásához a HRE rendszergazdai engedélyekkel kell rendelkeznie.

Az alkalmazásnak a HRE-ben is regisztrálva kell lennie az alábbi eljárásban leírtak szerint.

### <a name="register-your-application-with-aad"></a>Az alkalmazás regisztrálása a HRE

Ez a lépés integrálja az alkalmazást a HRE-mel, hogy elfogadja a felhasználói és csoportfiókok bejelentkezését. Ha Ön nem HRE-rendszergazda a szervezetben, lehetséges, hogy [létre kell hoznia egy új bérlőt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) az alábbi lépések végrehajtásához.

1. Nyissa meg az [**alkalmazás regisztrációs portálját**](https://apps.dev.microsoft.com), és  >   **Converged app**  >  **adjon hozzá egy alkalmazást**.
2. Adja meg az alkalmazás nevét, majd kattintson a **Létrehozás**gombra. 
3. Válassza ki az újonnan regisztrált alkalmazást a saját alkalmazások oldalon.
4. Az alkalmazás regisztrációja **oldalon > platform platform**  >  **hozzáadása**területen válassza a **webes API**lehetőséget.
5. Továbbra is az alkalmazás regisztrációja oldalon lépjen > **Microsoft Graph engedélyek**  >  **Hozzáadás**gombra.
6. Az engedélyek kiválasztása területen adja hozzá a következő delegált engedélyeket, majd kattintson az **OK**gombra:

   + **Directory. ReadWrite. All**
   + **Group.ReadWrite.All**
   + **User. ReadWrite. All**

A Microsoft Graph olyan API-t biztosít, amely lehetővé teszi a programozott hozzáférést a HRE egy REST APIon keresztül. Az útmutatóhoz tartozó mintakód a Microsoft Graph API-nak a csoportok, a felhasználók és a társítások létrehozásához szükséges engedélyeit használja. Az API-k a csoportok azonosítóinak gyorsítótárazására is használhatók a gyorsabb szűrés érdekében.

## <a name="create-users-and-groups"></a>Felhasználók és csoportok létrehozása

Ha a keresést egy létrehozott alkalmazáshoz adja hozzá, lehetséges, hogy már van felhasználói és csoportazonosító a HRE-ben. Ebben az esetben kihagyhatja a következő három lépést. 

Ha azonban nem rendelkezik meglévő felhasználókkal, a rendszerbiztonsági tag létrehozásához Microsoft Graph API-kat használhat. A következő kódrészletek bemutatják, hogyan hozhatja ki az azonosítókat, amelyek az Azure Cognitive Search index biztonsági mezőjének adatértékei lesznek. A feltételezett főiskolai felvételi alkalmazásban ez a bevezetések személyzetének biztonsági azonosítója lenne.

A felhasználók és a csoportok tagsága nagyon sok folyadék lehet, különösen a nagyméretű szervezeteknél. A felhasználók és csoportok identitásait felépítő programkódnak elég gyakran kell futnia ahhoz, hogy felvegye a változtatásokat a szervezeti tagságban. Hasonlóképpen, az Azure Cognitive Search indexéhez hasonló frissítési ütemtervre van szükség, hogy tükrözze az engedélyezett felhasználók és erőforrások aktuális állapotát.

### <a name="step-1-create-aad-group"></a>1. lépés: [HRE-csoport](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) létrehozása 
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
   
### <a name="step-2-create-aad-user"></a>2. lépés: [HRE-felhasználó](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0) létrehozása
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

### <a name="step-3-associate-user-and-group"></a>3. lépés: a felhasználó és a csoport hozzárendelése
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>4. lépés: a csoportok azonosítóinak gyorsítótárazása
Ha szeretné csökkenteni a hálózati késést, gyorsítótárba helyezheti a felhasználói csoport társításait, így a keresési kérelem kiadása után a rendszer visszaadja a csoportokat a gyorsítótárból, és egy oda-vissza menti a HRE. A [HRE batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) használatával egyetlen HTTP-kérést küldhet több felhasználóval, és felépítheti a gyorsítótárat.

A Microsoft Graph nagy mennyiségű kérelem kezelésére szolgál. Ha a rendszer több kérést is felvesz, Microsoft Graph a 429-as HTTP-állapotkód alapján meghiúsul a kérelem. További információ: [Microsoft Graph szabályozás](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Dokumentum indexelése az engedélyezett csoportokkal

Az Azure Cognitive Searchban végrehajtott lekérdezési műveletek Azure Cognitive Search-indexen keresztül hajthatók végre. Ebben a lépésben az indexelési művelet a kereshető adatmennyiséget egy indexbe importálja, beleértve a biztonsági szűrőként használt azonosítókat. 

Az Azure Cognitive Search nem hitelesíti a felhasználói identitásokat, illetve nem biztosít logikát annak meghatározásához, hogy egy felhasználó milyen tartalmat tekinthet meg. A biztonsági kivágás használati esete feltételezi, hogy Ön biztosítja a bizalmas dokumentum és a dokumentumhoz hozzáféréssel rendelkező csoportazonosító közötti társítást, és a rendszer érintetlenül importál egy keresési indexbe. 

A feltételezett példában egy Azure Cognitive Search indexbe helyezett PUT-kérelem törzse magában foglalja a pályázó főiskolai tanulmányát vagy átiratát, valamint a csoport azonosítóját, amely jogosult a tartalom megtekintésére. 

A jelen útmutatóban a kódban használt általános példában az index művelet a következőképpen nézhet ki:

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

A biztonsági kivágási célokra az index biztonsági mezőjében szereplő értékek a keresési eredményekben található dokumentumok belefoglalásához vagy kizárásához használt statikus értékek. Ha például a (z) "A11B22C33D44-E55F66G77-H88I99JKK" típusú felvételekhez tartozó csoportazonosító, az Azure Cognitive Search indexben lévő minden olyan dokumentum szerepel (vagy kizárva), amely a kérelmezőnek visszaadott keresési eredmények között szerepel.

A kérést kiállító felhasználó csoportjai alapján a keresési eredményekben visszaadott dokumentumok szűréséhez tekintse át a következő lépéseket.

### <a name="step-1-retrieve-users-group-identifiers"></a>1. lépés: a felhasználó csoport-azonosítóinak beolvasása

Ha a felhasználó csoportjai még nem lettek gyorsítótárazva, vagy a gyorsítótár lejárt, adja ki a [csoportok](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) kérését
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

### <a name="step-2-compose-the-search-request"></a>2. lépés: a keresési kérelem összeállítása

Feltéve, hogy a felhasználó csoportjának tagsága van, a keresési kérést a megfelelő szűrési értékekkel adhatja ki.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>3. lépés: az eredmények kezelése

A válasz magában foglalja a dokumentumok szűrt listáját, amely azokat a felhasználókat tartalmazza, amelyeknek a megtekintésére jogosult. A keresési eredmények oldalának létrehozási módjától függően előfordulhat, hogy a szűrt eredményhalmaz megjelenítéséhez vizuális kifejezéseket szeretne használni.

## <a name="conclusion"></a>Összegzés

Ebben az útmutatóban megtanulta a HRE-bejelentkezések használatának folyamatait a dokumentumok Azure-beli Cognitive Search eredményekben való szűréséhez, a kérelemben megadott szűrőnek nem megfelelő dokumentumok eredményeinek a kivágásával.

## <a name="see-also"></a>Lásd még

+ [Identitás-alapú hozzáférés-vezérlés az Azure Cognitive Search szűrők használatával](search-security-trimming-for-azure-search.md)
+ [Szűrők az Azure Cognitive Search](search-filters.md)
+ [Adatbiztonság és hozzáférés-vezérlés az Azure Cognitive Search Operations szolgáltatásban](search-security-overview.md)
