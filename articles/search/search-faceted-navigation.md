---
title: Az Azure Search jellemzőalapú navigációs megvalósításához |} Microsoft Docs
description: Adja hozzá Jellemzőalapú navigációs alkalmazásokat, amelyekbe beépül az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, Microsoft Azure-on.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 3/10/2017
ms.author: heidist
ms.openlocfilehash: e00e875619e4ed6800f5739362ff0c52971f6f16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195294"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jellemzőalapú navigáció megvalósítása az Azure Search-ben
Jellemzőalapú navigáció, amely az alkalmazások keresése irányuló részletezési navigációs szűrési mechanizmus. A kifejezés "jellemzőalapú navigációs" ismeretlen, de Ön már valószínűleg használta. Ahogy az alábbi példában látható, jellemzőalapú navigációs értéke "Nothing" több mint eredmények szűréséhez kategóriákat.

 ![Az Azure Search feladat portál bemutató][1]

Jellemzőalapú navigáció egy másik belépési pont keresése. Az alternatívája összetett keresési kifejezést kézzel beírja a. Értékkorlátozás segítséget, amit keresünk, biztosítva, hogy nem kapott eredmény. Fejlesztőként értékkorlátozás lehetővé teszik, hogy teszi közzé a leghasznosabb keresési feltételeknek, a keresési corpus navigáláshoz. Online kereskedelmi alkalmazások jellemzőalapú navigációs gyakran-t márkákat, szervezeti egységek (kid tartozó helyébe), méret, ár, időben népszerűvé vált és minősítése. 

Végrehajtási jellemzőalapú navigációs eltérő keresési technológiák között. Az Azure Search jellemzőalapú navigációs-t időben, a séma attribútummal mezőket.

-   A lekérdezések, amelyek az alkalmazás létrehozta, egy lekérdezést kell küldenie a *értékkorlátozás lekérdezési paraméterek* lekérése a rendelkezésre álló dimenzió szűrőértékek, hogy a dokumentum eredményhalmaz.

-   A dokumentum eredmény ténylegesen trim állítsa be, az alkalmazást is telepíteni kell egy `$filter` kifejezés.

Az alkalmazásfejlesztés jelent, amely hoz létre a lekérdezések programozás a munka nagy részét. Az alkalmazás viselkedéseket, amelyek a jellemzőalapú navigáció teheti meg számos beépített támogatása tartományok definiálása és a dimenzió eredmények száma a szolgáltatás által biztosított. A szolgáltatás is tartalmaz, amelyek segítenek ésszerű alapértelmezett kezelése nehézkessé navigációs struktúrák elkerülése érdekében. 

## <a name="sample-code-and-demo"></a>Mintakód és bemutató
Ez a cikk feladat keresési portál használ példaként. A példa egy ASP.NET MVC alkalmazás valósul meg.

-   Tekintse meg, és tesztelje a működő bemutató online, [Azure keresési feladat Portal bemutató](http://azjobsdemo.azurewebsites.net/).

-   Töltse le a kódot a [tárház Azure-minták a githubon](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Bevezetés
Ha most ismerkedik fejlesztési kereséséhez, a jellemzőalapú navigáció gondol legkönnyebben irányuló keresési lehetőségeit mutatja. Olyan típusú leásási keresési élményt biztosít, a keresési eredmények keresztül pont, és kattintson a műveletek gyorsan szűkíteni használt előre definiált szűrők alapján. 

### <a name="interaction-model"></a>Interakció modell

A keresési élményt biztosít a jellemzőalapú navigáció iteratív, így érdemes megismerni azt a lekérdezések, amelyek felhasználói műveletek válaszul unfold sorozatot.

A kiindulási pontja egy alkalmazás-lap, amely általában helyezve kerülete jellemzőalapú navigáció. A faszerkezetben, minden értéknek vagy kattintható jelölőnégyzetekkel jellemzőalapú navigációs gyakran. 

1. Egy lekérdezést küld az Azure Search a jellemzőalapú navigáció struktúra egy vagy több dimenzió lekérdezési paraméterek segítségével határozza meg. Például a lekérdezés tartalmazhat `facet=Rating`, lehet, hogy az egy `:values` vagy `:sort` tovább pontosíthatja a megjelenítési beállítást.
2. A bemutató réteg képezi le egy keresése oldal, amely használatával a típushoz a kérésben megadott jellemzőalapú navigáció.
3. Egy jellemzőalapú navigációs szerkezetben, amely tartalmazza az értékelést kap, akkor kattintson a "4" annak jelzésére, hogy csak egy minősítés 4 vagy újabb termékek megjelenjenek-e. 
4. A válasz az alkalmazás küld egy lekérdezést, amely tartalmazza `$filter=Rating ge 4` 
5. A bemutató réteg frissíti a lapot, amely az csökkentett eredménykészletet, csak a megadott elemeket, amelyek megfelelnek az új feltételeket tartalmazó (ebben az esetben a termékek besorolású 4 és legfeljebb).

Egy dimenzió egy lekérdezési paraméter, de ne tévessze össze az lekérdezés bevitellel. A lekérdezésben kiválasztási feltételek, soha nem használható. Ehelyett gondolja, hogy a dimenzió lekérdezési paraméterek ismét elérhető lesz a válaszban szereplő szerkezeti bemeneteként. Minden dimenzió lekérdezési paraméter, akkor adja meg, az Azure Search értékeli ki, hány dokumentumok minden értékkorlátozás értéke részleges eredményei nem.

Figyelje meg a `$filter` a 4. lépésben. A szűrő jellemzőalapú navigációs fontos eleme. Annak ellenére, hogy facets és a szűrők függetlenek az API-ban, mind a kívánt élmény szüksége. 

### <a name="app-design-pattern"></a>Alkalmazás-kialakítási mintája

Az alkalmazás kódjában a minta nem térjen vissza a jellemzőalapú navigációs szerkezetben értékkorlátozás eredményeket, valamint a $filter kifejezés együtt értékkorlátozás lekérdezési paraméterek használatával.  A szűrőkifejezés kezeli az kattintson eseményt értékkorlátozás értéke. Gondoljon a `$filter` kifejezés a tényleges levágási tartozó kódot, a keresési eredmények a bemutató réteghez adja vissza. Egy színek dimenzió megadott, a vörös színt kattintva keresztül történik egy `$filter` kifejezés, amely csak a vörös színt rendelkező elem kijelölése. 

### <a name="query-basics"></a>Lekérdezés alapjai

Az Azure Search, egy kérelem egy vagy több lekérdezési paraméterek keresztül megadott (lásd: [dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) minden egyes leírása). A lekérdezés-paraméterek egyike sem kötelező, de kell ahhoz, hogy a lekérdezés érvényes legyen, legalább egy.

Pontosság, megértettem, a lehetőség nem számít találatok sorrendekben legalább ezek a kifejezések egyike:

-   **keresési =**  
    Ez a paraméter értékének a keresési kifejezés számít. Előfordulhat, hogy egy darab szöveg vagy egy összetett keresési kifejezés, amely tartalmazza a feltételeket és a kezelők. A kiszolgálón a keresési kifejezés használható teljes szöveges keresés az index az egyező kifejezések, eredményt visszaadó sorrend a kereshető mezők lekérdezése. Ha `search` be NULL értékre, lekérdezés végrehajtása során a teljes index van (Ez azt jelenti, hogy `search=*`). Ebben az esetben a lekérdezés egyéb elemek, mint egy `$filter` vagy pontozási profil, a dokumentumok visszaadott érintő elsődleges tényezők `($filter`), hogy milyen sorrendben (`scoringProfile` vagy `$orderby`).

-   **$filter =**  
    A szűrő akkor egy rendkívül erős eszköz korlátozza a keresési eredmények között, az adott dokumentum attribútum értékei alapján méretét. A `$filter` először értékeli ki, a értékkorlátozás logika, amely a rendelkezésre álló értékeket, és az értékek megfelelő számát követi

Összetett keresési kifejezések csökkentse a lekérdezés teljesítményét. Ahol lehetséges, segítségével jól felépített szűrőkifejezéseket pontosság növeléséhez, és javíthatja a lekérdezések teljesítményét.

Jobb megértése, hogyan szűrőt ad pontosabb, hasonlítsa össze egy összetett keresési kifejezés, amely tartalmazza a szűrőkifejezést:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Mindkét lekérdezések érvényes, de a második pedig jobb, ha a keresett nem motelek a budapesti várakozást.
-   Az első lekérdezés listázzák említett vagy a nem szerepel a karakterlánc típusú, például a nevét, leírását és bármely más kereshető adatokat tartalmazó mező támaszkodik.
-   A második lekérdezés a strukturált adatok pontos egyezés keresi, és várhatóan lényegesen pontosabban.

Olyan alkalmazások, amelyek tartalmazzák a jellemzőalapú navigáció győződjön meg arról, hogy minden egyes felhasználói művelet egy jellemzőalapú navigációs szerkezetben keresztül csatolni a keresési eredmények szűkítését. Eredmények szűkítéséhez használja egy kifejezést.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Hozza létre a jellemzőalapú navigáció alkalmazását
Az Azure Search jellemzőalapú navigációs az alkalmazás kódjában, a keresési kérelem épülő valósítja meg. A jellemzőalapú navigáció a sémában, amely a korábban meghatározott elemek támaszkodik.

Az előre megadott a keresésben a indexe a `Facetable [true|false]` index attribútum, állítsa be a kijelölt mezőket engedélyezheti vagy tilthatja le a használatukat jellemzőalapú navigációs szerkezetben. Nélkül `"Facetable" = true`, egy mező nem lehet felhasználni dimenzió navigációs.

A bemutató réteg a kódban a felhasználói élményt nyújt. Az szerepelnie kell a jellemzőalapú navigáció, például a címke értékek, jelölőnégyzeteket vagy a szám a része. Az Azure Search REST API platform független, ezért bármilyen nyelvet és a kívánt platform. Fontos, hogy tartalmazza a felhasználói felületi elemei, amelyek támogatják a növekményes frissítést, a frissített felhasználói felület állapota, minden további értékkorlátozás ki van. 

A lekérdezés idejét, az alkalmazás kódjában kérést hoz létre, amely tartalmazza az `facet=[string]`, egy kérelem paraméter, amely a dimenzió által mező. Lekérdezés például rendelkezhet több facets `&facet=color&facet=category&facet=rating`, egymástól által az ampersand (&) karaktert.

Alkalmazás kódja is kell létrehozni egy `$filter` kifejezés kezelni a jellemzőalapú navigációs eseményeit. A `$filter` csökkenti a keresési eredmények között, a dimenzióértéknél használja, mint szűrési feltételeket.

Az Azure Search adja vissza a keresési eredmények között, egy vagy több ad meg, és a jellemzőalapú navigációs szerkezetben frissítések feltételek alapján. Az Azure Search jellemzőalapú navigációs egy egyszintű konstrukció értékkorlátozás értékekkel, és megjeleníti, hogy hány eredmények találhatók minden egyes.

A következő szakaszokban azt hogyan hozhat létre minden egyes részben közelebbről is igénybe vehet.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Az index létrehozása
Értékkorlátozás engedélyezve van az indexben, az index attribútum keresztül mező által alapon: `"Facetable": true`.  
Minden mező típusok, amelyeket esetleg használhatnak jellemzőalapú navigációs `Facetable` alapértelmezés szerint. Az ilyen típusú tartalmaznak `Edm.String`, `Edm.DateTimeOffset`, és a számmező típusok (alapvetően a minden mező típusok a következők kivételével kategorizálható `Edm.GeographyPoint`, jellemzőalapú navigációs nem használható). 

Az index létrehozásakor a jellemzőalapú navigáció ajánlott explicit módon kikapcsolása értékkorlátozás mezőben, amely soha nem használható mint egy dimenzió.  Különösen a singleton értékek, például az azonosító vagy a termék nevét, a karakterlánc típusú meg `"Facetable": false` jellemzőalapú navigációs véletlen (és hatástalan) használatuk megelőzése érdekében. Értékkorlátozás nem kell ki bekapcsolása segít, hogy maradjon kicsi a index méretét, és általában javítja a teljesítményt.

Az alábbiakban olvashat a feladat Portal bemutató mintát alkalmazás esetén a rövidített méretének csökkentése érdekében egyes attribútumok séma részeként:

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

Ahogy látja, a minta sémában `Facetable` , értékkorlátozás, például az azonosító értékek nem használható a karakterlánc típusú ki van kapcsolva. Értékkorlátozás nem kell ki bekapcsolása segít, hogy maradjon kicsi a index méretét, és általában javítja a teljesítményt.

> [!TIP]
> Ajánlott eljárásként minden mező attribútum teljes készletét tartalmazza. Bár a `Facetable` alapértelmezés szerint be szinte minden mező szándékosan beállítása minden attribútum segítségével gondolja, hogy minden egyes séma döntési következményeit keresztül. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Az adatok ellenőrzése
Az adatok minőségének közvetlen hatással van a hogy a jellemzőalapú navigációs szerkezetben megvalósul a várt módon. Hatással van a könnyű úgy, hogy csökkentse az eredményhalmaz hozhat létre.

Ha szeretné a márka vagy árlista dimenzió, egyes dokumentumok tartalmaznia kell értékeinek *márkanév* és *ProductPrice* , amelyek érvényes, egységes, és a termelékenység szűrő beállításként.

Az alábbiakban néhány emlékeztetők, mit a megtisztítás:

* Minden mező által értékkorlátozás kívánt tegye fel magának hogy tartalmaz-e a irányuló keresési szűrőként megfelelő értékeket. Az értékeket rövid leíró és eléggé jellegzetes versengő lehetőségek közötti választási lehetőséget biztosítani kell lennie.
* Talál helyesírási hibát vagy majdnem egyező értékek. Ha a színt és a mezőértékek értékkorlátozás közé tartozik a narancssárga és Ornage (helyesírási), egy dimenzió szín mező alapján lenne érvényesítéséhez mindkét.
* Vegyes eset szöveget is képes teszi pusztítást jellemzőalapú navigációs narancssárga és narancssárga két különböző értékként jelenik meg. 
* Egyetlen és többes számú verziók ugyanazt az értéket az egyes egy külön dimenzió eredményezhet.

Módon is tekinthető, az adatok előkészítése az gondossággal hatékony jellemzőalapú navigációs lényeges eleme.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>A felhasználói felület létrehozása
Vissza a bemutató rétegből működő segítséget nyújt a követelményeket, ellenkező esetben előfordulhat, hogy hiányzik, és megismerje milyen lehetőségek állnak a keresési élményt biztosít alapvető fontosságú fedik le.

Jellemzőalapú navigációs tekintetében a webhely vagy alkalmazás lap megjeleníti a jellemzőalapú navigációs szerkezetben, észleli a lap felhasználói bevitelt és a módosított elemek beszúrása. 

Webes alkalmazásokhoz az AJAX gyakran használják a bemutató rétegben mivel lehetővé teszi a növekményes változásokat frissítéséhez. ASP.NET MVC vagy bármely más képi megjelenítés platform, amely HTTP-kapcsolaton keresztül képes csatlakozni az Azure Search szolgáltatást is használhatja. Ebben a cikkben – hivatkozott mintaalkalmazást a **Azure keresési feladat Portal bemutató** – történik, az ASP.NET MVC alkalmazások kell.

A minta jellemzőalapú navigációs beépített a keresési eredmények oldalát. A következő példában venni a `index.cshtml` fájl a mintaalkalmazásnak jeleníti meg a statikus HTML-szerkezet jellemzőalapú navigációs megjelenítése a keresési eredmények. Értékkorlátozás listájának beépített vagy az újonnan létrehozott dinamikusan küldje el a kívánt keresőkifejezést, vagy válassza ki vagy egy dimenzió törölje.

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

A következő kódrészletet a a `index.cshtml` lap dinamikusan összeállít az első értékkorlátozás, üzleti cím megjelenítése a HTML. Hasonló funkciókat dinamikusan létrehozni a HTML-KÓDBAN, a másik típushoz. Minden dimenzió rendelkezik egy címke és számát, amely dimenzió eredményező talált elemek számát jeleníti meg.

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
> Amikor a keresési eredmények oldalát, akkor ne felejtse el hozzáadni egy olyan mechanizmus clearing értékkorlátozás. Ha jelölőnégyzeteket, hogyan kell a szűrő törlése könnyen láthatja. Más elrendezés esetén szükség lehet egy webhely-navigációs mintát vagy egy másik kreatív megközelítés. Például a feladat keresési portál mintaalkalmazást, válassza a `[X]` után törölje a dimenzió egy kijelölt dimenzió.

<a name="buildquery"></a>

## <a name="build-the-query"></a>A lekérdezés
Készítéséhez lekérdezéseket írhat kódot adjon meg érvényes lekérdezést, beleértve a keresési kifejezéseket, értékkorlátozás, szűrők, pontozási profilok – semmit állítson össze egy kérelem használt minden részén. Ez a szakasz azt megismerkedhet ahol értékkorlátozás felelnek meg a lekérdezés, és hogyan szűrők megnyilvánulása biztosításához használt csökkentett eredménykészletet.

Figyelje meg, hogy a mintaalkalmazást a részét képezik-e értékkorlátozást. A keresési élményt a feladat Portal bemutató jellemzőalapú navigáció és a szűrők ellátására van kialakítva. Az oldalon jellemzőalapú navigációs jól láthatóan elhelyezett elhelyezését az fontos mutatja be. 

Példa: gyakran remek megkezdéséhez. A következő példában venni a `JobsSearch.cs` fájl, a kérelmeket, amelyek hoz létre a dimenzió navigációs alapján üzleti cím, a helyet, a könyvelési típusa és a minimális fizetés buildek. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Egy dimenzió lekérdezési paraméter értéke egy mező, és attól függően, írja be az adatokat, is lehet további paraméterezni vesszővel tagolt listája, amely tartalmazza az `count:<integer>`, `sort:<>`, `interval:<integer>`, és `values:<list>`. Értékek listáját támogatott numerikus tartományok beállítása során. Lásd: [dokumentumok keresése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) a használat részleteiről.

Értékkorlátozás, valamint a kérelem, az alkalmazás által megfogalmazott is összeállítsa-e úgy, hogy leszűkítheti jelölt dokumentumok értékkorlátozás értéke a kijelölés alapján. Egy kerékpárt Store jellemzőalapú navigációs biztosít keresik kérdésekre, például *érhetők el a milyen színek, gyártók és kerékpárt típusú?*. Szűrés kérdésekre ad választ például *hegyi kerékpárt, mely pontos kerékpárt piros, tartomány ár ezen?*. Kattintson a "Red" annak jelzésére, hogy megjelenjenek-e csak piros termékek, tartalmaz-e az alkalmazás a következő lekérdezés `$filter=Color eq ‘Red’`.

A következő kódrészletet a a `JobsSearch.cs` lap ad a szűrőt a kiválasztott üzleti címe, ha értéket választja ki az üzleti cím értékkorlátozás.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

### <a name="indexing-tips"></a>Az indexelő tippek
**Növelje a index hatékonyságát, ha nem adja meg a keresési mezőbe**

Ha az alkalmazás jellemzőalapú navigációs kizárólag (Ez azt jelenti, hogy nincs keresőmezőbe) jelölheti meg a mező `searchable=false`, `facetable=true` tömörebb index létrehozásához. Ezenkívül indexelő akkor következik be, csak a teljes dimenzióértéknek nem szóhatárolással vagy az indexelő összetevő részből álló több word értéket.

**Adja meg, melyik mezőkre értékkorlátozás használható**

Az index a séma határozza meg, hogy mely mezők érhetők el kívánja használni, mint egy dimenzió visszaírási. Feltéve, hogy egy mező kategorizálható, a lekérdezés határoz meg, amely mező a dimenzió által. A mező, amellyel értékkorlátozás program jeleníti meg a címke alatt jelennek meg. 

Az értékek, amelyek az egyes címkék jelennek meg az index lekért. Például, ha a dimenzió mező *szín*, a rendelkezésre álló további szűréséhez értékek mező - értékek piros, fekete és így tovább.

A numerikus és DateTime típusú értékek csak, explicit módon megadhatja értékeket a dimenzió mező (például `facet=Rating,values:1|2|3|4|5`). Mező típusaival egyszerűbbé teheti a dimenzió eredmények való összefüggő tartományok (vagy numerikus értékek vagy időszakoknak alapján tartományok) az engedélyezett értékek listáját. 

**Alapértelmezés szerint csak akkor jellemzőalapú navigációs egy szintjének összecsukása** 

Amint, nincs beágyazási értékkorlátozás a hierarchiában nincs közvetlen támogatása. Alapértelmezés szerint az Azure Search jellemzőalapú navigációs csak egy szintjének összecsukása szűrők támogatja. Azonban lehetséges megoldások léteznek. A dimenzió hierarchikus struktúra is kódol egy `Collection(Edm.String)` egy belépési pont száma hierarchiánként. A megoldás nem ez a cikk a terjed. 

### <a name="querying-tips"></a>Tippek lekérdezése
**Ellenőrizze a mezőket**

Ha értékkorlátozás nem megbízható felhasználói bevitel alapján dinamikusan listája, ellenőrizze, hogy érvényesek-e a jellemzőalapú mezők nevét. Vagy a nevek escape-URL-címek használatával vagy fejlesztéskor `Uri.EscapeDataString()` .NET, vagy az ezzel egyenértékű jogosultságokkal a kiválasztott platformon.

### <a name="filtering-tips"></a>Szűrési tippek
**Keresési pontosság növeléséhez szűrőkkel**

Szűrők használata. Ha csak keresési kifejezés önmagában, amelynek támadó egy dokumentumot, amely nem rendelkezik a pontos értékkorlátozás értéke a mezők valamelyikében kell állítani.

**A szűrők keresési teljesítményének javítása**

Szűrők jelölt dokumentumok Search leszűkítheti, és kizárja őket rangsorolási. Ha nagy számú dokumentumok, egy szelektív dimenzió leásási gyakran használata lehetővé teszi az jobb teljesítmény érdekében.
  
**Csak a jellemzőalapú mezők szűrése**

Jellemzőalapú leásási általában érdemes csak a dokumentumok az értékkorlátozás értéke egy adott (jellemzőalapú) mező nem bárhol közötti összes kereshető mezőt tartalmaz. A célmező hozzáad egy szűrőt megerősíti úgy irányítja a szolgáltatást, hogy csak a megfelelő érték a jellemzőalapú mezőben keressen.

**Trim értékkorlátozás eredmények további szűrőkkel**

Értékkorlátozás eredményei dokumentumok, a keresési eredmények, amelyek megfelelnek egy dimenzió-kifejezés szerepel. A következő példában a keresési eredmények *a felhőalapú informatika*, 254 elemek is *belső előírás* tartalom típusként. Elemek már nem szükségszerűen kölcsönösen kizárják egymást. Ha egy cikk mindkét szűrők feltételeknek, a minden egyes számít. A lemezmásolási kell lehetséges, ha a értékkorlátozás `Collection(Edm.String)` mezők, amelyek gyakran használják a dokumentum címkézése végrehajtásához.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Általánosságban elmondható Ha talál meg, hogy a dimenzió eredmények következetesen túl nagyok, azt javasoljuk, hogy hozzáadhat további szűrők lehetőséget nyújt a felhasználóknak a keresés szűkítését több lehetősége.

### <a name="tips-about-result-count"></a>Eredményszám kapcsolatos tippek

**A dimenzió navigációs tételszámának korlátozása**

Minden jellemzőalapú navigációs fában mezőhöz nincs alapértelmezett maximális száma 10 értékből. Ez az alapértelmezett szabálykészletében navigációs struktúrákhoz mert kezelhető méretre tartja az értékek listája. Az alapértelmezett érték felülírható rendelhet a count érték.

* `&facet=city,count:5` Meghatározza, hogy csak az első öt városokat szerint rangsorolunk eredmények tetején található dimenzió emiatt kerüljenek vissza. Fontolja meg a kívánt keresőkifejezést "repülőtéri" és a 32 megegyezik a mintalekérdezést. Ha a lekérdezés `&facet=city,count:5`, csak az első öt egyedi város dokumentumokkal a legtöbb a a találatok között szerepelnek a dimenzió eredmények.

Figyelje meg értékkorlátozás eredmények és a keresési eredmények közötti különbségtételt. A program, amelyek megfelelnek a lekérdezés összes dokumentumot. Értékkorlátozás eredményei az egyes értékkorlátozás értéke megfelelő találat. A példában a találatok között szerepelnek a városnév, amelyek nem szerepelnek a dimenzió problémabesorolások listája (ebben a példában 5). Keresztül válnak láthatóvá értékkorlátozás törölje, vagy válassza a város mellett egyéb facets jellemzőalapú navigációs kiszűrt eredményeket. 

> [!NOTE]
> Témák megvitatásához `count` Ha egynél több típus zavaró lehet. Az alábbi táblázat röviden összefoglalva a hogyan kifejezés Azure Search API, a mintakódot, és a dokumentációja nyújt. 

* `@colorFacet.count`<br/>
  A bemutató kódban megjelenik egy count paraméter a dimenzió értékkorlátozás eredmények számának megjelenítésére használatos. Értékkorlátozás eredmények száma a dimenzió kifejezés vagy a tartomány megfelelő dokumentumok számát jelzi.
* `&facet=City,count:12`<br/>
  A dimenzió lekérdezésben száma értéket állíthat be.  Az alapértelmezett érték 10, de be lehet állítani magasabb vagy alacsonyabb. Beállítás `count:12` dokumentum száma szerint a dimenzió eredmények megfelel az első 12 lekérdezi.
* „`@odata.count`”<br/>
  Válaszban az érték adja meg a keresési eredmények egyező elemek száma. Átlagosan kombinálja, a található elemek, amelyek megfelelnek a keresési kifejezés miatt az összes dimenzió eredmények összege nagyobb, de nincs értékkorlátozás értéke megegyezik kell.

**Értékkorlátozás eredmények összesítése beolvasása**

Amikor jellemzőalapú lekérdezést adjon hozzá egy szűrőt, előfordulhat, hogy szeretné megőrizni a dimenzió utasítás (például `facet=Rating&$filter=Rating ge 4`). Technikai szempontból értékkorlátozás = minősítő nem szükséges, de a számát is dimenzióértéknek értékelések lekapcsolva tartja ad vissza, 4 és újabb rendszer. Például ha "4" kattint, és a lekérdezés szűrője nagyobbnak vagy azzal egyenlő "4", számok pedig visszaadott minden besorolása, amely 4 és újabb rendszer.  

**Ellenőrizze, hogy elérhetővé pontos értékkorlátozás száma**

Bizonyos esetekben előfordulhat, hogy értékkorlátozás száma nem egyezik a eredményhalmazt (lásd: [az Azure Search (fórumbejegyzést) Jellemzőalapú navigációs](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Értékkorlátozás száma miatt a horizontális architektúrát pontatlan lehet. Minden keresési index több szilánkok rendelkezik, és minden shard jelent a felső N értékkorlátozás dokumentum száma, amelyek majd egyesítve egyetlen eredmény szerint. Ha néhány szilánkok sok egyező értékek, míg mások kisebb értékre, előfordulhat, hogy néhány dimenzióértéknek hiányzik, vagy a felvenni az eredmények között.

Bár ez a viselkedés módosulhatnak bármikor, ha ez a viselkedés ma, akkor azt úgy kerülheti mesterségesen fúvódnia az a szám:<number> sok kényszerítéséhez minden egyes shard teljes jelentést. Ha a count értékének: nagyobb vagy egyenlő egyedi értékek mezőjében számával, akkor garantáltan pontos eredményeket. Azonban amikor magas a dokumentumok számát, nincs-e a teljesítményét, ezért ez a beállítás használatakor.

### <a name="user-interface-tips"></a>Felhasználói felület tippek
**Adja hozzá a címkék az egyes mezők értékkorlátozás navigációs sáv**

Címkék általában definiálják HTML vagy formátumban (`index.cshtml` a mintaalkalmazás). Nincs az Azure Search értékkorlátozás navigációs feliratainak nincs API vagy bármely egyéb metaadatokat.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Szűrő alapján
Értékkorlátozás értékek tartományok keresztül egy közös keresési alkalmazás követelményeinek. Tartományok numerikus adatokat és a dátum/idő értékek vannak támogatva. További kapcsolatos mindkét megközelítés a [dokumentumok keresése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Az Azure Search tartomány konstrukció két megközelítés biztosít a számítási tartomány egyszerűbbé teszi. Mindkét megközelítés az Azure Search hoz létre a megfelelő tartományokon a megadott bemeneti adatok megadott. Például ha megadja a tartomány értékének 10 |} 20 |} 30, automatikusan létrehozza a 0 – 10, 10-20, 20 – 30 tartományait. Az alkalmazás opcionálisan eltávolíthatja bármely intervallumok üres. 

**1. módszer: A időköz paraméter használható.**  
Árlista értékkorlátozás $10 lépésekben beállításához megadni: `&facet=price,interval:10`

**2. módszer: Értékek listáját használja**  
A numerikus adatok értékek listáját is használhatja.  Érdemes lehet a dimenzió értéktartományán egy `listPrice` mező megjelenítése az alábbiak szerint:

  ![A minta értékek listája][5]

Az a fenti képernyőfelvételen hasonló értékkorlátozás széles megadásához használja az értékek listáját:

    facet=listPrice,values:10|25|100|500|1000|2500

Minden tartományban épül, 0 a kiindulási pontként, a listában a végpontok közötti értéket, és majd levágja a korábbi tartomány diszkrét intervallumok létrehozásához. Az Azure Search jellemzőalapú navigációs részeként hajtja végre ezeket a módosításokat. Nincs írhat kódot minden időköz rendszerezésére szolgál.

### <a name="build-a-filter-for-a-range"></a>Összeállítása szűrőt, amely a tartomány
Dokumentumok választja alapján szűrni, használhatja a `"ge"` és `"lt"` operátorokat egy kétlépéses kifejezés, amely meghatározza a tartomány a végpontok szűrése. Ha úgy dönt, hogy a tartomány 10-25 a például a `listPrice` mezőt, a szűrő lenne `$filter=listPrice ge 10 and listPrice lt 25`. Példakód, használja a szűrőkifejezés **priceFrom** és **priceTo** paraméterek a végpontokat. 

  ![A kívánt értéktartományt lekérdezés][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Távolság alapján szűrhet
Az common megjelenítéséhez, amelyek segítik a tároló, éttermi vagy a cél az az aktuális hely közelében alapján választja szűrők. Bár az ilyen típusú szűrő jellemzőalapú navigációs nézhet, ez a beállítás csak egy szűrő. A Microsoft említse meg itt azok az Ön kifejezetten keres, hogy adott kialakítás probléma megvalósítási tanácsokat.

Két olyan földrajzi funkció van az Azure Search **geo.distance** és **geo.intersects**.

* A **geo.distance** függvény adja vissza kilométerben két pont között. Egy pont mező, másik megnevezése a szűrő egy konstans. 
* A **geo.intersects** függvény igaz értéket ad vissza egy adott sokszögön belül egy adott pont esetén. A pont mező, a szűrő megnevezése koordináták állandó listáját a sokszög van megadva.

A szűrő példák található [OData-kifejezésszintaxist (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Próbálja ki a demót
Az Azure Search feladat Portal bemutató cikkben hivatkozott példák tartalmazza.

-   Tekintse meg, és tesztelje a működő bemutató online, [Azure keresési feladat Portal bemutató](http://azjobsdemo.azurewebsites.net/).

-   Töltse le a kódot a [tárház Azure-minták a githubon](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Munka a keresési eredmények között, nézze meg az URL-cím, a lekérdezés konstrukció változásokat. Ez az alkalmazás értékkorlátozás hozzáfűzése az URI, minden egyes kiválasztásakor történik.

1. A bemutató alkalmazás leképezési funkcióinak használatához a Bing Maps kulcsot az beszerzése a [a Bing térképek fejlesztői központjához](https://www.bingmapsportal.com/). Illessze be a meglévő kulcshoz keresztül a `index.cshtml` lap. A `BingApiKey` beállítást azokban a `Web.config` fájl nincs használatban. 

2. Futtassa az alkalmazást. Ismerkedés a nem kötelező, vagy zárja be a párbeszédpanelt.
   
3. Adja meg a kívánt keresőkifejezést, például a "elemző", és kattintson a keresés ikonra. A lekérdezés gyorsan hajtja végre.
   
   A keresési eredmények között is lett visszaadva egy jellemzőalapú navigációs szerkezetben. A keresési eredmények oldalát a jellemzőalapú navigációs szerkezetben tartalmaz minden dimenzió eredmény hány példányban. Nincs facets vannak kijelölve, ezért minden egyező eredményeinek.
   
   ![Keresési eredmények értékkorlátozás kiválasztása előtt][11]

4. Kattintson egy üzleti cím, a hely vagy a minimális fizetés. Értékkorlátozás a kezdeti Search null értékű, de mivel azok az értékek, a keresési eredmények nem egyező elemek lesz.
   
   ![Keresési eredmények értékkorlátozás kiválasztása után][12]

5. Így eltérő lekérdezési viselkedés próbálkozhat a jellemzőalapú lekérdezés törléséhez kattintson a `[X]` után törölje a Decimal típushoz kiválasztott típushoz.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Részletek
A Watch [az Azure Search mélyreható](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). A 45:25 van egy bemutató facets megvalósításához.

További áttekinthetik a jellemzőalapú navigáció tervezési alapelvek a következőket javasoljuk:

* [A Jellemzőalapú keresési tervezése](http://www.uie.com/articles/faceted_search/)
* [Kialakítási minták: Jellemzőalapú navigációs](http://alistapart.com/article/design-patterns-faceted-navigation)


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

