<properties
    pageTitle="Azure Search-index létrehozása .NET SDK használatával | Microsoft Azure | Üzemeltetett felhőalapú keresőszolgáltatás"
    description="Index létrehozása kódban Azure Search .NET SDK használatával."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>


# Azure Search-index létrehozása .NET SDK használatával
> [AZURE.SELECTOR]
- [Áttekintés](search-what-is-an-index.md)
- [Portál](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Ez a cikk végigvezeti az Azure Search-[index](https://msdn.microsoft.com/library/azure/dn798941.aspx) [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) használatával történő létrehozásának folyamatán.

Már az útmutató követése és az index létrehozása előtt [létre kell hoznia egy Azure Search szolgáltatást](search-create-service-portal.md).

Vegye figyelembe, hogy a cikkben szereplő összes példakód C# nyelven van megírva. A teljes forráskódot a [GitHub](http://aka.ms/search-dotnet-howto) webhelyén találja.

## I. Az Azure Search szolgáltatás adminisztrációs API-kulcsának azonosítása
Most, hogy létrehozta az Azure Search szolgáltatást, csaknem készen áll arra, hogy a .NET SDK használatával kérelmeket bocsásson ki a szolgáltatásvégponton. Először meg kell szereznie a létrehozott keresőszolgáltatáshoz generált adminisztrációs API-kulcsok egyikét. A .NET SDK minden kérelemnél elküldi ezt az API-kulcsot a szolgáltatásának. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

1. A szolgáltatás API-kulcsainak megkereséséhez be kell jelentkeznie az [Azure portálra](https://portal.azure.com/)
2. Nyissa meg az Azure Search szolgáltatáspaneljét
3. Kattintson a „Kulcsok” ikonra

A szolgáltatás *rendszergazdai kulcsokkal* és *lekérdezési kulcsokkal* fog rendelkezni.

  - Az elsődleges és másodlagos *rendszergazdai kulcsok* teljes jogosultságot biztosítanak az összes művelethez, beleértve a szolgáltatás felügyeletének, valamint az indexek, indexelők és adatforrások létrehozásának és törlésének képességét. Két kulcs létezi, tehát ha az elsődleges kulcs újbóli létrehozása mellett dönt, a másodlagos kulcsot továbbra is használhatja (ez fordítva is igaz).
  - A *lekérdezési kulcsok* csak olvasási hozzáférést biztosítanak az indexekhez és dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások kapják meg őket.

Index létrehozása céljából az elsődleges és a másodlagos adminisztrációs kulcsot is használhatja.

<a name="CreateSearchServiceClient"></a>
## II. A SearchServiceClient osztály példányának létrehozása
Az Azure Search .NET SDK használatához létre kell hoznia a `SearchServiceClient` osztály egy példányát. Ez az osztály több konstruktorral rendelkezik. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. `SearchCredentials` becsomagolja az API-kulcsot.

Az alábbi kód egy új `SearchServiceClient`-példányt hoz létre a keresőszolgáltatás nevének, valamint az API-kulcsnak az alkalmazás konfigurációs fájljában (`app.config` vagy `web.config`) tárolt értékének a felhasználásával.

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` `Indexes` tulajdonsággal rendelkezik. Ez a tulajdonság az Azure Search-indexek létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert biztosítja.

> [AZURE.NOTE] A `SearchServiceClient` osztály kezeli a keresőszolgáltatása kapcsolatait. A túl sok kapcsolat megnyitásának elkerülése érdekében, ha lehetséges, próbálja meg a `SearchServiceClient` egyetlen példányát megosztani az alkalmazásban. A módszerei szálbiztosak az ilyen megosztás engedélyezéséhez.

<a name="DefineIndex"></a>
## III. Az Azure Search-index meghatározása a `Index` osztály használatával
A `Indexes.Create` módszer egyetlen meghívása létrehozza az indexet. Ez a módszer egy `Index` objektumot használ paraméterként, amely meghatározza az Azure Search-indexet. Létre kell hoznia és inicializálnia kell egy `Index` objektumot az alábbiak szerint:

1. Állítsa be az `Index` objektum `Name` tulajdonságát az index nevének.
2. Állítsa be az `Index` objektum `Fields` tulajdonságát a `Field` objektumok tömbjének. A `Field` objektumok mindegyike meghatározza az indexben lévő egy-egy mező viselkedését. A konstruktornak megadhatja a mező nevét és az adattípust (vagy karakterláncmezők esetében az elemzőnek). Más tulajdonságokat is beállíthat, például: `IsSearchable`, `IsFilterable` stb.

Az index tervezésekor nagyon fontos figyelembe venni a keresés során tapasztalt felhasználói élményt és az üzleti igényeket, mivel minden egyes `Field` a [megfelelő attribútumokhoz](https://msdn.microsoft.com/library/azure/dn798941.aspx) rendelendő hozzá. Ezek a tulajdonságok határozzák meg, hogy melyik mezőkre melyik keresési funkciók (szűrés, értékkorlátozás, rendezés, teljes szöveges keresés stb.) vonatkoznak. Azon tulajdonságok esetében, amelyeket külön nem állított be, a `Field` osztály alapértelmezés szerint letiltja a megfelelő keresési funkciót, kivéve, ha Ön kifejezetten engedélyezi.

A fenti példában az indexnek a „hotels” nevet adtuk, és a mezőket az alábbiak szerint definiáltuk:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Minden `Field` esetében annak alapján választottuk ki az indexattribútumokat, ahogyan szerintünk az alkalmazások használni fogják őket. Valószínű például, hogy a szállodákat kereső személyeket érdekelni fogják a `description` mező kulcsszóra kapott találatok, ezért az `IsSearchable` feltételt `true` értékre beállítva engedélyezzük a teljes szöveges keresést.

Vegye figyelembe, hogy az indexében pontosan egy `DataType.String` típusú mező lehet kijelölve _kulcs_mezőként az `IsKey` feltétel `true` értékre történő beállításával (lásd: `hotelId` a fenti példában).

A fenti indexdefiníció egyéni nyelvi elemzőt használ a `description_fr` mezőhöz, mert a mező francia szöveg tárolására szolgál. A nyelvi elemzőkkel kapcsolatos további információkért tekintse meg [az MSDN Nyelvi támogatás című témakörét](https://msdn.microsoft.com/library/azure/dn879793.aspx), valamint a vonatkozó [blogbejegyzést](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [AZURE.NOTE]  Vegye figyelembe, hogy a konstruktorban lévő `AnalyzerName.FrLucene` átadásával a `Field` automatikusan `DataType.String` típusú lesz, és az `IsSearchable` feltétel `true` értékre lesz beállítva.

## IV. Az index létrehozása
Most, hogy már rendelkezik egy inicializált `Index` objektummal, a `SearchServiceClient` objektumon lévő `Indexes.Create` meghívásával egyszerűen létrehozhatja az indexet:

```csharp
serviceClient.Indexes.Create(definition);
```

Sikeres kérelem esetén a módszer normálisan tér vissza. Ha probléma merül fel, például érvénytelen egy paraméter, a módszer a `CloudException` kivételt fogja kijelezni.

Miután létrehozta az indexet, és törölni szeretné, csak hívja meg a `SearchServiceClient` objektumon lévő `Indexes.Delete` módszert. A „hotels” nevű index például a következőképpen törölhető:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] Ebben a cikkben a példakód az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a fenti esetekben használhatja a `CreateAsync` és a `DeleteAsync` módszert a `Create` és a `Delete` helyett.

## Tovább
Az Azure Search-index létrehozása után készen áll arra, hogy [feltöltse a tartalmát az indexbe](search-what-is-data-import.md), és megkezdje az adatok keresését.



<!--HONumber=Sep16_HO4-->


