---
title: Jellemzőalapú navigáció megvalósítása a category hierarchiához – Azure Search az
description: Adja hozzá a értékkorlátozó navigációs alkalmazásokhoz, amelyekbe beépül az Azure Search, egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azure-ban.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 3/10/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 337ee5259e980509c73099f0e3417bb31ec3276d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313938"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jellemzőalapú navigáció megvalósítása az Azure Search-ben
Jellemzőalapú navigáció egy szűrési mechanizmus, amely alkalmazások keresése az önállóan irányított drilldown navigációs biztosít. A kifejezés "jellemzőalapú navigáció" ismeretlen, de valószínűleg felhasználta előtt. Ahogy az alábbi példa bemutatja, a jellemzőalapú navigáció má hodnotu nothing több, mint az eredmények szűréséhez használt kategóriák.

 ![Az Azure Search feladat portál bemutató][1]

Jellemzőalapú navigáció egy másik belépési pont keresése. Ez egy kényelmes alternatívát nyújt a kézzel beírja az összetett keresési kifejezéseket. Értékkorlátozással segítségével keresse meg azt, amit keres, közben biztosítva, hogy eredmény nem kap. A fejlesztők értékkorlátozással lehetővé teszik elérhetővé a leghasznosabb keresési feltételeknek, a keresési forrásgyűjteményébe navigáláshoz. Online kereskedelmi alkalmazások, a jellemzőalapú navigáció gyakran beépített márkákat, szervezeti egységek (gyerek a cipőeladásai), méret, ár, népszerűsége és minősítések keresztül. 

Jellemzőalapú navigáció megvalósítása között a keresési technológiák eltérő. Az Azure Search szolgáltatásban jellemzőalapú navigáció lekérdezéskor a beépített, amely a séma teljesítménykapacitást mezőkkel.

-   A lekérdezések, amelyek az alkalmazás létrejött, el kell küldenie egy lekérdezést a *értékkorlátozás lekérdezési paraméterek* beolvasni a rendelkezésre álló dimenzió szűrési értékek alapján, hogy a dokumentum eredményhalmaz.

-   Valójában az azokat a dokumentum eredményt állítsa be, az alkalmazást is telepíteni kell egy `$filter` kifejezés.

Az alkalmazások fejlesztéséhez jelent létrehozó alkalmazáskódban lekérdezések írása a munka nagy részét. Számos, az alkalmazás viselkedése, a jellemzőalapú navigáció meg a szolgáltatást, ideértve a beépített támogatást nyújt a tartományok definiálása és a darabszámokat értékkorlátozás eredményeket által biztosított. A szolgáltatás is magában foglalja a észszerű alapértelmezett értékeket, amelyek segítségével nehézkessé navigációs struktúrát elkerülése érdekében. 

## <a name="sample-code-and-demo"></a>Mintakód és bemutató
Ez a cikk egy feladat keresési portált használja példaként. A példában egy ASP.NET MVC alkalmazás van megvalósítva.

-   Tekintse meg és tesztelje a működő bemutatót online, [Azure Search feladat portál bemutató](http://azjobsdemo.azurewebsites.net/).

-   A kód letöltése a [Azure-Samples-adattárat a Githubon](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Bevezetés
Ha most ismerkedik a keresse development, a jellemzőalapú navigáció gondolja át, hogy a legjobb módszer, hogy látható-e önállóan irányított keresés lehetőségeit. Olyan típusú Lehatolás keresési funkciót, a keresési eredményeket a pont, és kattintson a műveletek segítségével gyorsan szűkítheti használt előre definiált szűrők alapján. 

### <a name="interaction-model"></a>Interakció modell

A keresési funkciót, a jellemzőalapú navigáció meghatározási, így érdemes megismerni, lekérdezések, amelyek a felhasználói műveleteknek kibontása sorozataként indítása.

A kiindulási pont egy jellemzőalapú navigáció, általában a peremétől helyezett biztosító alkalmazások lap. Jellemzőalapú navigáció legtöbbször jelölőnégyzeteket tartalmazó elemlistává változik, minden értéket vagy kattintható szöveg fastruktúrában. 

1. Egy lekérdezést küld az Azure Search megadja a jellemzőalapú navigációs struktúrát egy vagy több értékkorlátozás lekérdezési paraméterek használatával. A lekérdezés lehetnek például `facet=Rating`, például az egy `:values` vagy `:sort` tovább finomíthatja a bemutató lehetőséget.
2. A megjelenítési réteggel rendereli a jellemzőalapú navigáció, a kérésben megadott aspektusait használatával biztosító keresési lapot.
3. Adja meg egy jellemzőalapú navigációs struktúrát, amely tartalmazza a minősítést, akkor kattintson a "4" jelzi, hogy csak egy minősítése 4 vagy újabb termékek jelenjenek meg. 
4. Válaszként az alkalmazás elküld egy lekérdezést, amely magában foglalja `$filter=Rating ge 4` 
5. A megjelenítési réteggel frissíti a lapot, amely egy korlátozott eredményhalmaz, csak azok az elemek, amelyek megfelelnek az új feltételek tartalmazó (ebben az esetben a termékek minősített 4 és újabb).

Egy dimenzió egy lekérdezési paraméter, de ne keverje össze, a lekérdezés bemenete. Kiválasztási feltételek a lekérdezésben, soha nem használható. Tekintsen inkább értékkorlátozás lekérdezési paraméterek a navigációs szerkezet, amely a válaszban bemeneteként. Egyes értékkorlátozás lekérdezési paraméterhez, akkor adja meg az Azure Search értékeli ki, hány dokumentumok vannak az egyes értékkorlátozás értéke a részleges eredményeket.

Figyelje meg a `$filter` 4. lépésben. A szűrő abban az fontos szempont a jellemzőalapú navigáció. Annak ellenére, hogy értékkorlátozással és szűrők függetlenek az API-ban, mind a kívánt felhasználói élményt nyújthat szüksége. 

### <a name="app-design-pattern"></a>Alkalmazás-tervezési minta

Az alkalmazás kódjában az egyik értékkorlátozás lekérdezési paraméterek használatával adja vissza a jellemzőalapú navigációs struktúrát értékkorlátozás eredményeket, és a egy $filter kifejezés együtt.  A szűrőkifejezés kezeli a kattintás eseményt, értékkorlátozás értéke. Gondoljon a `$filter` kifejezést a tényleges levágási mögötti kódként a keresési eredmények vissza a megjelenítési réteggel. Adja meg a színek értékkorlátozás, kattintson a szín vörös keresztül valósul egy `$filter` kifejezés, amely kiválasztja a csak azok az elemek, amelyek egy vörös színt. 

### <a name="query-basics"></a>Lekérdezés alapjai

Az Azure Search szolgáltatásban egy kérelem egy vagy több lekérdezési paraméterek keresztül megadott (lásd: [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) mindegyiknél leírása). A lekérdezési paraméterek egyike sem kötelező, de rendelkeznie kell legalább egy ahhoz, hogy egy lekérdezést, hogy érvényesek legyenek.

Pontosság, érthető módon irreleváns találatok szűréséhez lehetővé teszi a gazdafájlon keresztül, egyik vagy mindkét ezek a kifejezések:

-   **Keresés =**  
    Ez a paraméter értékét a keresési kifejezés jelent. Érdemes lehet egy darab szöveg vagy egy összetett keresési kifejezés, amely tartalmazza a feltételeket és a kezelők. A kiszolgálón teljes szöveges keresés, kereshető mezőket a feltételeket, eredményt adnak vissza a sorrend egyeztetéséhez az index lekérdezése a keresési kifejezés használható. Ha `search` , null, a lekérdezés végrehajtása során a teljes indexre van (azaz `search=*`). Ebben az esetben a lekérdezés többi elemének például egy `$filter` vagy relevanciaprofil, mely dokumentumokat visszaadott befolyásoló legfontosabb tényezők `($filter`), és milyen sorrendben (`scoringProfile` vagy `$orderby`).

-   **$filter =**  
    Szűrő az adott dokumentum attribútumának alapuló keresési eredményeknek méretét korlátozó egy hatékony mechanizmusa. A `$filter` először értékeli ki, a többnyelvűséget logika, amely létrehozza az elérhető értékek és a megfelelő száma minden egyes érték követ.

Összetett keresési kifejezések csökkentse a lekérdezés teljesítményét. Ahol lehetséges, használjon jól felépített szűrőkifejezésekben pontosság növeléséhez, és javíthatja a lekérdezések teljesítményét.

Segít jobban megérteni, hogyan ad hozzá a szűrőt a pontosabb, hasonlítsa össze egy összetett keresési kifejezés, amely tartalmaz egy kifejezést:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Mindkét lekérdezést érvényes, de a második kiváló, ha a keresett nem motelek a budapesti várakozást.
-   Az első lekérdezés listázzák említett vagy a nem szerepelnek a karakterlánc-mezők, például a nevét, leírását és bármely más mező kereshető adatokat tartalmazó támaszkodik.
-   A második lekérdezés a strukturált adatok pontos egyezés keres, és valószínűleg sokkal pontos.

Olyan alkalmazások, amelyek tartalmazzák a jellemzőalapú navigáció győződjön meg arról, hogy minden egyes felhasználói művelet felett egy jellemzőalapú navigációs struktúrát csatolni a keresési eredmények szűkítheti. Eredmények szűkítéséhez használjon egy kifejezést.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Jellemzőalapú navigáció alkalmazás készítése
Jellemzőalapú navigáció az Azure Search szolgáltatással az alkalmazás kódjában, amely összeállítja a keresési kérelem valósíthatja meg. A jellemzőalapú navigáció a sémában, amely a korábban meghatározott elemek támaszkodik.

Az előre meghatározott a keresési index van a `Facetable [true|false]` index attribútumkészlet, engedélyezni vagy letiltani a használatukat jellemzőalapú navigációs szerkezetben kiválasztott mező alapján. Nélkül `"Facetable" = true`, értékkorlátozó navigációs egy mező nem használható.

A megjelenítési réteggel, a kódban a felhasználói élményt nyújt. Szerepelnie kell, a jellemzőalapú navigáció, például a címke, értékeket, jelölőnégyzetet és a szám a része. Az Azure Search REST API platform-agnosztikus, ezért bármilyen nyelv és platform. A lényeg az, hogy tartalmazza a felhasználói felületi elemeket, amelyek támogatják a növekményes frissítést, a frissített felhasználói felület állapota minden további tényező van kiválasztva. 

Lekérdezéskor, az alkalmazás kódjában, amely tartalmazza az kérést hoz létre `facet=[string]`, egy kérelem paraméter, amely a mező szerint értékkorlátozás biztosít. A lekérdezés több metszettel, például rendelkezhet `&facet=color&facet=category&facet=rating`, mindegyik az ampersand (&) karakterrel elválasztva.

Alkalmazáskód is kell összeállítani egy `$filter` kifejezés, a jellemzőalapú navigáció kattintás események. A `$filter` csökkenti a keresési eredmények között, az értékkorlátozás értéke használatával szűrési feltételeket.

Az Azure Search a keresési eredmények között, egy vagy több olyan feltételek, amelyek ad meg, és a jellemzőalapú navigációs szerkezetben frissítések alapján ad vissza. Az Azure Search szolgáltatásban jellemzőalapú navigáció egy egyszintű konstrukció értékkorlátozás értékekkel, és hány eredmények találhatók minden egyes száma.

A következő szakaszokban azt, hogyan hozhat létre az egyes részek közelebbről is.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Az index létrehozása
Jellemzőalapú engedélyezve van az indexben, az index attribútum keresztül mező szerint történik: `"Facetable": true`.  
Minden mező típusok valószínűleg használható jellemzőalapú navigációs `Facetable` alapértelmezés szerint. Az ilyen mező típusok a következők `Edm.String`, `Edm.DateTimeOffset`, és minden mező numerikus típus (alapvetően a minden mező típusok a következők kivételével kategorizálható `Edm.GeographyPoint`, a jellemzőalapú navigáció nem használható). 

Egy index létrehozását, a jellemzőalapú navigáció az ajánlott eljárás az explicit módon kikapcsolása jellemzőkezelés értékkorlátozásként soha nem használt mezők.  Különösen a singleton értékek, például egy azonosító vagy a termék nevét, a karakterlánc típusú meg `"Facetable": false` jellemzőalapú navigációs véletlen (és hatástalan) használati elkerülése érdekében. Kapcsolja ki, ha nincs szüksége arra jellemzőkezelés teszi lehetővé, az index mérete kisebb, és általában javítja a teljesítményt.

Következő része a feladat portál bemutató mintaalkalmazást, vágott egyes attribútumok méretének csökkentése érdekében a sémában:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Amint láthatja, hogy a minta sémában `Facetable` ki van kapcsolva, amely nem használható metszettel, például a Azonosítóinak értékeit tartalmazó karakterláncot tartalmazó mezők esetében. Kapcsolja ki, ha nincs szüksége arra jellemzőkezelés teszi lehetővé, az index mérete kisebb, és általában javítja a teljesítményt.

> [!TIP]
> Ajánlott eljárásként például az egyes mezőkhöz tartozó indexattribútumokat teljes készletét. Bár a `Facetable` alapértelmezés szerint be szinte minden mezők beállítása szándékosan minden attribútum segítségével úgy gondolja, hogy minden egyes séma döntési következményei keresztül. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Ellenőrizze az adatokat
Az adatok minőségét e megvalósul a jellemzőalapú navigációs struktúrát, a várt módon közvetlen hatással van. Is hozhat létre szűrőket, hogy csökkentse az eredményhalmaz könnyű hatással van.

Értékkorlátozás márkájáról vagy ár szerint szeretné, ha minden egyes dokumentum tartalmaznia kell-e értékeit *BrandName* és *ProductPrice* , amelyek érvényes, egységes és a egy szűrőlehetőség érhető el hatékonyan dolgozhat.

Az alábbiakban néhány emlékeztetőket, hogy el kell távolítani a Mi:

* Minden mező szerint értékkorlátozás kívánt tegye fel magának e önállóan irányított keresés szűrőként megfelelő értékeket tartalmaz. Az értékek rövid leíró és eléggé jellegzetes versengő beállításai között választási lehetőséget kínáló kell lennie.
* Elírások vagy szinte egyező értékek. Ha a dimenzió a színt és a mezők értékeit tartalmazza, Orange és Ornage (helyesírási), egy dimenzió szín mező alapján szeretne csomópontmetrikák is.
* Vegyes megkülönbözteti a kis szöveges is is teszi károkat, a jellemzőalapú navigáció, az orange és az Orange két különböző értékként jelennek meg. 
* Egyetlen és többes számú verzióit ugyanazt az értéket az egyes egy külön dimenzió eredményezhet.

Belegondolunk, az adatok legkörültekintőbben hatékony jellemzőalapú navigáció alapvető aspektusaihoz.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>A felhasználói felület létrehozása
Vissza a megjelenítési réteggel való munka segíthet milyen lehetőségek állnak a keresési funkció alapvető fontosságú, ellenkező esetben előfordulhat, hogy kimarad, és tudomásul követelmények elemzése révén.

Tekintetében jellemzőalapú navigáció, a webhely vagy alkalmazás oldal megjeleníti a jellemzőalapú navigációs szerkezetben, észleli a felhasználói bevitel az oldalon és a szúr be a módosított elemeket. 

Webalkalmazások esetén AJAX gyakran használják a megjelenítési réteggel, mivel lehetővé teszi, hogy frissítse a növekményes változásokat. ASP.NET MVC- vagy bármely más vizualizációt platform, amely HTTP-n keresztül kapcsolódhat az Azure Search szolgáltatást is használhatja. A hivatkozott ebben a cikkben – mintaalkalmazás a **Azure Search feladat portál bemutató** – történetesen az ASP.NET MVC alkalmazásnak.

A mintában a jellemzőalapú navigáció a keresési eredmények oldalának be van építve. A következő példában a származik a `index.cshtml` fájlt annak a mintaalkalmazásnak, a jellemzőalapú navigáció megjelenítése a Search mutat be a statikus HTML-szerkezet eredményeit tartalmazó lap. Értékkorlátozással listájának beépített vagy az újonnan létrehozott dinamikusan elküldése egy keresési kifejezést, vagy válassza ki vagy egy dimenzió törlése.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

A következő kódrészletet a a `index.cshtml` lap dinamikusan összeállít első aspektus, üzleti cím megjelenítéséhez a HTML-kódot. Hasonló funkciókat dinamikusan hozhat létre a HTML-egyéb aspektusait. Minden egyes értékkorlátozás rendelkezik, egy címke és a egy számláló, amely megjeleníti a dimenzió eredmény található elemek száma.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Amikor a keresési eredmények oldalát, ne felejtse el hozzáadni egy mechanizmust, az aspektusokat. Ha hozzá jelölőnégyzetéből, könnyedén megtudhatja hogyan a szűrő törlése. Más elrendezések szükség lehet egy webhely-navigációs minta vagy más kreatív funkciót. Ha például a feladat portál mintaalkalmazás kattintva a `[X]` után törölje a dimenzió egy kijelölt dimenzió.

<a name="buildquery"></a>

## <a name="build-the-query"></a>A lekérdezés
A lekérdezések létrehozásához írt kóddal meg kell adnia egy érvényes lekérdezést, beleértve a keresési kifejezéseket, értékkorlátozással, szűrőket, pontozási profilok – semmi állítson össze egy kérelmet használt minden része. Ebben a szakaszban tárgyaljuk, ahol értékkorlátozással illik-e egy lekérdezést, és a szűrők használata az aspektusokat, hogy egy kisebb eredményhalmaz.

Figyelje meg, hogy ez a mintaalkalmazás a részét képezik-e metszettel. A keresési funkciót, a feladat portál bemutató a jellemzőalapú navigáció és a szűrők tervezték. Jellemzőalapú navigáció az oldalon neves elhelyezését az fontos mutatja be. 

Ilyen például, gyakran egy nagyszerű hely megkezdéséhez. A következő példában a származik a `JobsSearch.cs` fájlt, egy kérelmet, amely létrehozza az értékkorlátozó navigációs üzleti címét, a hely, a könyvelési típusa és a minimális bér alapján buildek. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Egy dimenzió lekérdezési paraméter értéke egy mezőt, és attól függően, írja be az adatokat, további rendelkeznek által vesszővel tagolt listája, amely tartalmazza az `count:<integer>`, `sort:<>`, `interval:<integer>`, és `values:<list>`. Értékek listáját támogatott numerikus adatokat tartományok beállítása során. Lásd: [dokumentumok keresése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) használattal kapcsolatos részleteket.

Metszettel, valamint a kérést, az alkalmazás által megfogalmazott is kialakítson szűrők értékkorlátozás értéke a kijelölés alapján jelölt dokumentumok köre szűkítéséhez. Kerékpár áruházbeli, a jellemzőalapú navigáció biztosít a keresőmotorok kérdésekre, például *érhetők el a milyen színeket, gyártók és kerékpár típusú?*. Például a szűrés kérdésekre *mountain Bike-OK, mely pontos kerékpárok piros, tartomány díjszabása a jelen?*. Ha jelzi, hogy csak a piros termékek jelenjenek meg a "Red" gombra kattint, a következő lekérdezés az alkalmazás elküld tartalmaz `$filter=Color eq ‘Red’`.

A következő kódrészletet a a `JobsSearch.cs` lap a szűrőt a kiválasztott üzleti cím ad hozzá, ha kiválaszt egy értéket, az üzleti cím értékkorlátozás.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

### <a name="indexing-tips"></a>Az indexelő tippek
**Index hatékonyság növelése, ha nem használja a keresőmezőt**

Ha az alkalmazás jellemzőalapú navigáció kizárólag (azaz nincs keresőmezőbe), a mező szerint jelölheti meg `searchable=false`, `facetable=true` egy összefogottabb index létrehozásához. Emellett az indexelés akkor fordul elő, csak a teljes értékkorlátozás értékek, nincs szóhatárolással vagy Többszavas értéket összetevő részei az indexelés.

**Adja meg, melyik mezők értékkorlátozással is használható**

Ne felejtse el, hogy a séma az index határozza meg, melyik mezők érhetők el egy dimenzió adatokként. Feltéve, hogy az adott mező kitöltése kategorizálható, a lekérdezés határoz meg, amely mező a dimenzió által. A mező, amely jellemzőkezelés biztosít az értékeket, amelyeket a felirat alatt jelennek meg. 

Az értékek, amelyek minden címke alatt jelennek meg az index lekért. Például, ha a dimenzió mező *szín*, a további szűréshez elérhető értékek az értékeket az adott mező – piros, fekete és így tovább.

A numerikus és dátum/idő értékek csak explicit módon megadhatja azt értékeket a dimenzió mező (például `facet=Rating,values:1|2|3|4|5`). Ezek mezőtípusok egyszerűsítése érdekében az összefüggő tartomány (vagy számértékeket vagy időszakoknak-alapú tartományok) értékkorlátozás eredményeket elválasztása engedélyezett értékek listáját. 

**Alapértelmezés szerint akkor legfeljebb egyetlen szinten jellemzőalapú navigáció** 

Feljegyzett, nincs beágyazása értékkorlátozással a hierarchiában nincs közvetlen támogatásához. Alapértelmezés szerint a jellemzőalapú navigáció az Azure Search csak egy szinttel, a szűrők támogatja. Azonban lehetséges megoldások léteznek. Egy dimenzió hierarchikus struktúra a kódolhatja egy `Collection(Edm.String)` egy belépési pont száma hierarchiánként. Ez a megoldás megvalósításához van ez a cikk nem foglalkozik. 

### <a name="querying-tips"></a>Lekérdezési tippek
**Mező érvényesítése**

Készít a nem megbízható felhasználói bemenet alapján dinamikusan értékkorlátozással listáját, ellenőrizze, hogy érvényesek-e a jellemzőalapú mezők nevét. Vagy, a nevek escape-URL-címek egyikével készítésekor `Uri.EscapeDataString()` a .NET vagy a választott platformnak megfelelő.

### <a name="filtering-tips"></a>Szűrési tippek
**Keresés pontosság növeléséhez szűrőkkel**

Szűrők használata. Ha csak keresési kifejezés önmagában, a dokumentum, amely nem rendelkezik a pontos értékkorlátozás értéke a bármelyik mezőjét adja vissza, amely szerint okozhat támaszkodnak.

**Keresés a teljesítmény növelése szűrőkkel**

Szűrők leszűkítheti jelölt dokumentumok kereséshez, és ennek a területnek zárja ki őket. Ha nagy számú dokumentumok, gyakran használatával egy szelektív dimenzió Lehatolás jobb teljesítményt biztosítja.
  
**Csak a jellemzőalapú mezők szűrése**

Jellemzőalapú Lehatolás, általában szeretné csak a dokumentumokon, amelyek az értékkorlátozás értéke a megadott (jellemzőalapú) mezőben, bárhol nem között az összes kereshető mezőket tartalmazzák. A szolgáltatást, hogy csak a megfelelő érték a jellemzőalapú mezőben keressen megkérnek egy szűrő hozzáadása biztonságérzetet ad a cél mezőben.

**Trim értékkorlátozás eredményeket további szűrőkkel**

Értékkorlátozás eredményeket a keresési eredmények között, amelyek megfelelnek egy értékkorlátozó kifejezés található dokumentumok is. A következő példában a keresési eredmények *felhő-számítástechnika*, 254 elemeket is, hogy *belső előírás* tartalom típusként. Elemek, amelyek nem feltétlenül kölcsönösen kizárják egymást. Ha egy elem megfelel a feltételeknek a mindkét szűrők, az egyes akkor számít. A duplikáció kötelező lehetséges, ha a többnyelvűséget `Collection(Edm.String)` mezők, amelyek gyakran használják a dokumentum címkézése megvalósításához.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Általánosságban elmondható Ha úgy találja, hogy értékkorlátozás eredményeket következetesen túl nagy, azt javasoljuk, hogy hozzáadhat további szűrők megadásával szűkítheti a keresést a további lehetőségeket biztosít a felhasználóknak.

### <a name="tips-about-result-count"></a>Eredményszámot kapcsolatos tippek

**Az értékkorlátozó navigációs elemek számának korlátozása**

A navigációs fa jellemzőalapú mezők 10 értékből álló alapértelmezett korlátozva van. Ez az alapértelmezett értelme navigációs struktúrákhoz, mert az értékek listája a kezelhető mérettel szinkronban tartja. A megszámlálandó értékeket rendelne a felülírhatja az alapértelmezett.

* `&facet=city,count:5` Itt adhatja meg, hogy csak az első öt városok eredmények rangsorolt felső értékkorlátozás eredményeként visszaadott. Fontolja meg a keresési kifejezés "repülőtér" és a 32 egyezések mintalekérdezés. Ha a lekérdezés határoz meg `&facet=city,count:5`, csak az első öt egyedi városok a legtöbb dokumentumot a keresési eredmények között szerepelnek a értékkorlátozás eredményeket.

Figyelje meg értékkorlátozás eredményeket és a keresési eredmények közötti különbséget. Keresési eredmények megfelelnek a lekérdezésnek megfelelő összes dokumentumot. Értékkorlátozás eredményeket az egyes értékkorlátozás értéke találat. A példában a keresési eredmények tartalmazzák a városok nevei, amelyek nem szerepelnek a értékkorlátozás a problémabesorolások listája (a példánkban 5). Jellemzőalapú navigáció keresztül kiszűrt eredményeket értékkorlátozással törölje, vagy válasszon más aspektusokat mellett az városa láthatóvá válhat. 

> [!NOTE]
> Témák megvitatásához `count` esetén több zavaró lehet. Az alábbi táblázat röviden összefoglalva a hogyan kifejezés az Azure Search API, mintakódokat és dokumentáció kínál. 

* `@colorFacet.count`<br/>
  Bemutató kódban látnia kell egy count paraméter a omezující podmínka eredmények számát jeleníti meg. Az értékkorlátozás eredményeket száma, amelyek megfelelnek a dimenzió kifejezés vagy a tartomány a dokumentumok számát jelzi.
* `&facet=City,count:12`<br/>
  A facet lekérdezésben beállíthat száma értékre.  Az alapértelmezett érték 10-es, de magasabbra vagy alacsonyabbra állíthatja. Beállítás `count:12` lekérdezi az első 12 megfelelő értékkorlátozás eredményeket a dokumentum száma szerint.
* „`@odata.count`”<br/>
  A lekérdezésekre adott válaszok Ez az érték azt jelzi, hogy a keresési eredmények megfelelő elemek száma. Átlagosan együttesen elemek, amelyek megfelelnek a keresési kifejezést, mert az összes értékkorlátozás eredményeket összege nagyobb, de rendelkezik nincs értékkorlátozás értéke megegyezik.

**Értékkorlátozás eredményeket kaphat száma**

Ha szűrőt ad hozzá egy jellemzőalapú lekérdezés, előfordulhat, hogy szeretné megőrizni az értékkorlátozás utasítás (például `facet=Rating&$filter=Rating ge 4`). Technikailag értékkorlátozó = besorolása nem szükséges, de lekapcsolva tartja, az értékelésekhez értékkorlátozás értékek számát adja vissza, 4 és újabb. Például ha "4" gombra kattint, és a lekérdezés tartalmazza a szűrőt, amely nagyobb vagy egyenlő "4", számok pedig visszaadott egyes besorolása, amely a 4 és újabb verzióit.  

**Ellenőrizze, hogy a számlálás pontos értékkorlátozás kap**

Bizonyos körülmények között, előfordulhat, hogy értékkorlátozás száma nem egyezik az eredménykészlet (lásd: [Jellemzőalapú navigáció az Azure Search (fórumbejegyzést)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Értékkorlátozás száma miatt a horizontális skálázás architektúra pontatlan lehet. Minden keresési index több szegmenst tartalmaz, és minden egyes szegmens a felső N értékkorlátozással jelentések szerint dokumentumok száma, amelyek egyetlen eredmény, majd összesítve. Ha egyes szegmensek több egyező értékek, míg mások kisebb, előfordulhat, hogy néhány értékkorlátozás érték hiányzik, vagy alatt-tranzakciónak az eredmények között.

Bár ez a viselkedés bármikor módosulhatnak, ha ezt a viselkedést tapasztal még ma, megkerüléséhez azt a számát mesterségesen fúvódnia:<number> kényszeríteni a teljes jelentés az egyes szegmensek nagy számú. Ha a számláló értéke: nagyobb vagy a mezőben egyedi értékek számának egyenlőnek, garantáltan pontos eredményeket. Azonban ha nagy dokumentumok számát, a teljesítményét van, ezért ezt a beállítást megfontoltan.

### <a name="user-interface-tips"></a>Felhasználói felület tippek
**Adja hozzá a címkéket az egyes mezőkhöz tartozó értékkorlátozó navigációs**

Címkék általában határozzák meg a HTML- vagy az űrlap (`index.cshtml` a mintaalkalmazásban). Nincs nem értékkorlátozó navigációs címkéket az Azure Search API-t vagy bármely más metaadatokat.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Szűrés annak alapján, a tartomány
Jellemzőalapú értéktartományokat keresztül keresése alkalmazás általános követelmény. Tartományok numerikus adatokat és a dátum/idő értékek támogatottak. Tudjon meg többet a mindkét megközelítés kapcsolatos [dokumentumok keresése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Az Azure Search egyszerűbbé teszi a tartomány konstrukció számítástechnika széles két módszer megadásával. Mindkét módszerénél az Azure Search a megfelelő tartományokon a megadott bemeneti adatok hoz létre. Például ha adja meg a 10 tartományértékeknek |} 20 |} 30, automatikusan létrehoz tartományokat, a 0 – 10, 10 – 20, a 20-30. Az alkalmazás bármely az üres intervallumok eltávolításával. 

**1. módszer: Az intervallum paraméter használata**  
10 USD lépésekben ár értékkorlátozással beállításához megadni: `&facet=price,interval:10`

**2. módszer: Értékek listáját használja**  
Numerikus adatok, az értékek listáját is használhatja.  Fontolja meg értékkorlátozás tartományát egy `listPrice` mezőben jelenik meg a következők szerint:

  ![Minta értékek listája][5]

Adja meg az előző képernyőképen látható egy hasonló értékkorlátozás széles, használja az értékek listáját:

    facet=listPrice,values:10|25|100|500|1000|2500

Minden tartomány 0 használja kiindulási pontként egy értéket végpontként, a listából, és a korábbi tartomány létrehozása diszkrét időközök majd vágott épül. Az Azure Search jellemzőalapú navigáció részeként hajtja végre ezeket a módosításokat. Nem kell minden egyes időköz rendszerezéséhez kód írására.

### <a name="build-a-filter-for-a-range"></a>Szűrheti a tartomány létrehozása
Dokumentumok választja számos alapján szűrheti, használhatja a `"ge"` és `"lt"` szűrése, amely meghatározza a tartomány a végpontok kétlépéses kifejezésben operátorok. Például, ha úgy dönt, hogy 10-25 tartományát egy `listPrice` mező, a szűrő lenne `$filter=listPrice ge 10 and listPrice lt 25`. A mintakód a szűrőkifejezés használ **priceFrom** és **priceTo** paraméterek a végpontok beállításához. 

  ![Különböző értékeket, lekérdezés][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>A távolságskála alapján szűrése
Annak közös megtekintéséhez, amelyek segítenek úgy dönt, hogy egy tároló, éttermi vagy cél helyezkedik el saját tartózkodási helye alapján szűri. Az ilyen típusú szűrő hasonló lehet jellemzőalapú navigáció, miközben csak egy szűrő. Itt azoknak, akik kifejezetten adott kialakítás problémát megvalósítási javaslatokat azt említik.

Nincsenek két térinformatikai funkciók az Azure Search szolgáltatásban **geo.distance** és **geo.intersects**.

* A **geo.distance** függvényt ad vissza a távolságot adja meg kilométerben két pont között. Egy pont mező másik pedig a szűrő részeként átadott konstans. 
* A **geo.intersects** függvény igaz értéket ad vissza, ha egy adott időpontra van egy adott sokszög belül. A pont egy mezőt, és a sokszög a szűrő részeként átadott koordináták állandó listaként van megadva.

A szűrő példái annak [OData-kifejezések szintaxisa (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Próbálja ki a demót
Az Azure Search feladat portál bemutató példák ebben a cikkben tartalmazza.

-   Tekintse meg és tesztelje a működő bemutatót online, [Azure Search feladat portál bemutató](http://azjobsdemo.azurewebsites.net/).

-   A kód letöltése a [Azure-Samples-adattárat a Githubon](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Végzett munka, a keresési eredmények között, nézze meg az URL-cím lekérdezési konstrukció változásairól. Ez az alkalmazás értékkorlátozással hozzáfűzése az URI-t, jelölje ki egyenként történik.

1. A bemutató alkalmazás hozzárendelési funkcióinak használatához kérje le a Bing térképek-kulcs a [Bing térképek fejlesztői központjában](https://www.bingmapsportal.com/). A meglévő kulcsot a illessze a `index.cshtml` lapot. A `BingApiKey` beállítását a `Web.config` fájlt nem használja. 

2. Futtassa az alkalmazást. Ismerkedés a nem kötelező, vagy a párbeszédpanel bezárásához.
   
3. Adjon meg egy keresési kifejezést, például a "elemző", és kattintson a keresés ikonra. A lekérdezés gyorsan hajtja végre.
   
   A keresési eredmények között is is visszaad egy jellemzőalapú navigációs struktúrát. A keresési eredmények oldalon a jellemzőalapú navigációs struktúrát tartalmaz darabszáma – minden értékkorlátozás eredmény. Nincs értékkorlátozással van jelölve, így az összes megfelelő eredményeket ad vissza.
   
   ![Keresési eredmények értékkorlátozással kiválasztása előtt][11]

4. Kattintson egy üzleti cím, a hely vagy a minimális fizetés. Értékkorlátozással voltak a kezdeti Search null értékű, de értékek igénybe vehet, mivel a keresési eredmények között már nem megfelelő elemek lesz.
   
   ![Keresési eredmények értékkorlátozással kiválasztása után][12]

5. Úgy, hogy próbálja meg más lekérdezési viselkedés a jellemzőalapú lekérdezés törléséhez kattintson a `[X]` után törölje a értékkorlátozással kiválasztott aspektusait.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Részletek
Tekintse meg [részletes ismertetése az Azure Search](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). 45:25, jelenleg nincs egy bemutatót értékkorlátozással megvalósítása.

A további elemzés, a jellemzőalapú navigáció tervezési elvek javasoljuk, hogy az alábbi hivatkozásokat:

* [Jellemzőalapú keresés tervezése](http://www.uie.com/articles/faceted_search/)
* [Tervezési minták: Jellemzőalapú navigáció](http://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

