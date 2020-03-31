---
title: Köralapú navigációs kategória hierarchiájának hozzáadása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search szolgáltatásba integráló keresési alkalmazásokban az önirányított szűréshez hozzáadhat idúros navigációt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f4435ca213584fff84f3ddad9bda6f7e06628a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283159"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>A lesialapú navigáció megvalósítása az Azure Cognitive Search szolgáltatásban

A sokoldalú navigáció egy olyan szűrési mechanizmus, amely önirányított részletező navigációt biztosít a keresési alkalmazásokban. A "sokoldalú navigáció" kifejezés lehet, hogy ismeretlen, de valószínűleg már használta korábban. Ahogy a következő példa is mutatja, a köralapú navigáció nem más, mint az eredmények szűrésére használt kategóriák.

 ![Az Azure Cognitive Search Állásportál bemutatója](media/search-faceted-navigation/azure-search-faceting-example.png "Az Azure Cognitive Search Állásportál bemutatója")

A sokoldalú navigáció egy alternatív belépési pont a kereséshez. Kényelmes alternatívát kínál az összetett keresési kifejezések kézzel való gépeléséhez. A facets segítségével megtalálhatja, amit keres, miközben biztosítja, hogy ne kapjon nulla eredményt. Fejlesztőként a facets segítségével megadhatja a keresési indexben való navigálás leghasznosabb keresési feltételeket. Az online kiskereskedelmi alkalmazásokban a csiszolt navigáció gyakran a márkák, osztályok (gyerekcipők), méret, ár, népszerűség és minősítések fölé épül. 

A lefelé irányuló navigáció megvalósítása a keresési technológiák között eltérő. Az Azure Cognitive Search, a felhasználóalapú navigáció lekérdezési időben épül fel, a sémában korábban hozzárendelt mezők használatával.

-   Az alkalmazás által létrehozott lekérdezésekben a lekérdezésnek *mezőlekérdezési paramétereket* kell küldenie a dokumentum eredményhalmazához rendelkezésre álló értékszűrő-értékek lekérnie.

-   A dokumentum eredményhalmazának tényleges levágásához `$filter` az alkalmazásnak egy kifejezést is alkalmaznia kell.

Az alkalmazásfejlesztésben a lekérdezéseket alkotó kód írása a munka nagy részét teszi ki. A szolgáltatás biztosítja a szolgáltatás által a tartományok definiálásához és a tulajdonságeredmények számának megszerzéséhez. A szolgáltatás ésszerű alapértelmezett értékeket is tartalmaz, amelyek segítenek elkerülni a nehézkes navigációs struktúrákat. 

## <a name="sample-code-and-demo"></a>Mintakód és bemutató
Ez a cikk példaként egy álláskeresési portált használ. A példa egy ASP.NET MVC-alkalmazásként van megvalósítva.

- Tekintse meg és tesztelje a munkabemutatót online az [Azure Cognitive Search Állásportál bemutatóján.](https://aka.ms/azjobsdemo)

- Töltse le a kódot az [Azure-minták tártárból a GitHubon.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

## <a name="get-started"></a>Bevezetés
Ha most jön a keresés fejlesztése, a legjobb módja annak, hogy szerintem a sokoldalú navigáció, hogy megmutatja a lehetőségeket az önálló irányított keresés. Ez egy előre definiált szűrőkön alapuló részletezési keresési élmény, amely a keresési eredmények gyors leszűkítéséhez szolgál a point-and-click műveletek kel. 

### <a name="interaction-model"></a>Interakciós modell

A kifejezésalapú navigáció keresési élménye iteratív, ezért kezdjük azzal, hogy a felhasználói műveletekre válaszul kibontakozó lekérdezések sorozataként értelmezi.

A kiindulási pont egy alkalmazásoldal, amely jellemzőalapú navigációt biztosít, amely általában a perifériára kerül. A ráható navigáció gyakran fastruktúra, amely minden értékhez jelölőnégyzeteket vagy kattintható szöveget tartalmaz. 

1. Az Azure Cognitive Search szolgáltatásba küldött lekérdezés egy vagy több facet lekérdezési paraméteren keresztül adja meg a faceted navigációs struktúrát. A lekérdezés tartalmazhat például `facet=Rating`, `:values` például a bemutató további finomítására szolgáló lehetőséggel. `:sort`
2. A bemutatóréteg egy olyan keresőlapot jelenít meg, amely a kérelemben megadott adhelyekkel rendelkezik.
3. Adott egy olyan sokoldalú navigációs struktúra, amely tartalmazza a Minősítést, a "4" gombra kattintva jelezheti, hogy csak a 4-es vagy magasabb minősítésű termékek jelenjenek meg. 
4. Válaszul az alkalmazás olyan lekérdezést küld, amely`$filter=Rating ge 4` 
5. A megjelenítési réteg frissíti az oldalt, csökkentett eredményhalmazt mutatva, amely csak azokat az elemeket tartalmazza, amelyek megfelelnek az új feltételeknek (ebben az esetben a 4-es vagy újabb besorolású termékek).

A lapt egy lekérdezési paraméter, de ne tévesszük össze a lekérdezésbemenettel. A lekérdezésben soha nem lesz kiválasztási feltételként használva. Ehelyett gondolja át a facet lekérdezési paraméterek bemenetek a navigációs struktúra, amely visszatér a válaszban. Az Azure Cognitive Search minden egyes megadott facet lekérdezési paraméter esetén kiértékeli, hogy hány dokumentum szerepel az egyes részértékek részleges eredményeiközött.

Figyelje `$filter` meg a 4. A szűrő a sokoldalú navigáció fontos aspektusa. Bár a facets és a szűrők függetlenek az API-ban, mindkettőnek meg kell adnia a kívánt élményt. 

### <a name="app-design-pattern"></a>Alkalmazástervezési minta

Az alkalmazáskódban a minta a facet lekérdezési paraméterek használatával adja vissza a faceted navigációs struktúra mellett a facet eredményeket, valamint egy $filter kifejezés.  A szűrőkifejezés kezeli a pontértékkattintási eseményét. Gondoljon `$filter` úgy a kifejezésre, mint a keresési eredmények tényleges vágása mögötti kódra, amely visszakerül a megjelenítési rétegbe. Adott színek mező, kattintson a szín piros `$filter` végre egy kifejezés, amely kiválasztja csak azokat az elemeket, amelyek a szín a piros. 

### <a name="query-basics"></a>A lekérdezés alapjai

Az Azure Cognitive Search egy kérelem egy vagy több lekérdezési paraméterek (lásd: [Dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) az egyes leírását). A lekérdezési paraméterek egyike sem szükséges, de a lekérdezés érvényességéhez legalább egy paraméterrel kell rendelkeznie.

A pontosság, amely a lényegtelen találatok kiszűrésének képességét illeti, az alábbi kifejezések egyikén vagy mindkettőn keresztül érhető el:

-   **search =**  
    Ennek a paraméternek az értéke a keresési kifejezés. Lehet, hogy egyetlen szöveg, vagy egy összetett keresési kifejezés, amely több kifejezést és operátort tartalmaz. A kiszolgálón a keresési kifejezés teljes szöveges kereséshez használatos, az indexben kereshető mezők lekérdezése az egyező kifejezésekhez, az eredmények sorrendje. Ha null `search` értékre állítja a lekérdezés végrehajtását, `search=*`a lekérdezés végrehajtása a teljes index (azaz ) felett van. Ebben az esetben a lekérdezés más elemei, például `$filter` egy vagy pontozási profil, `($filter`az elsődleges tényezők,`scoringProfile` `$orderby`amelyek befolyásolják a dokumentumok visszaküldését ) és milyen sorrendben ( vagy ).

-   **$filter =**  
    A szűrő hatékony mechanizmus a keresési eredmények méretének korlátozására az adott dokumentumattribútumok értékei alapján. Először az A-t `$filter` értékeli ki a rendszer, amelyet a rendelkezésre álló értékek és a megfelelő számok generáló értékeinek és megfelelő számának generálása követ.

Az összetett keresési kifejezések csökkentik a lekérdezés teljesítményét. Ahol lehetséges, használjon jól felépített szűrőkifejezéseket a pontosság növeléséhez és a lekérdezés teljesítményének növeléséhez.

Annak jobb megértéséhez, hogy egy szűrő hogyan ad hozzá pontosabban, hasonlítsa össze az összetett keresési kifejezést egy szűrőkifejezést tartalmazó kifejezéssel:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Mindkét lekérdezés érvényes, de a második jobb, ha nem olyan moteleket keres, amelyek seattle-i parkolóval rendelkeznek.
-   Az első lekérdezés a karakterláncmezőkben (például Név, Leírás) és a kereshető adatokat tartalmazó bármely más mezőben megemlített vagy meg nem nevezett szavakra támaszkodik.
-   A második lekérdezés a strukturált adatok pontos egyezéseit keresi, és valószínűleg sokkal pontosabb lesz.

A felhasználóalapú navigációt tartalmazó alkalmazásokban győződjön meg arról, hogy minden felhasználói művelet egy sokoldalú navigációs struktúrán a keresési eredmények szűkítése kíséri. Az eredmények szűkítéséhez használjon szűrőkifejezést.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Sokoldalú navigációs alkalmazás létrehozása
Az Azure Cognitive Search szolgáltatással valósítja meg a leépített alkalmazáskódban a leadott navigációt. A felületes navigáció a séma korábban definiált elemeire támaszkodik.

A keresési indexben előre `Facetable [true|false]` definiált index attribútum, amely a kijelölt mezőkön van beállítva, hogy engedélyezze vagy letiltsa azok használatát egy sokoldalú navigációs struktúrában. A `"Facetable" = true`mező nélkül nem használható a mezőalapú navigáció.

A kódban lévő bemutatóréteg biztosítja a felhasználói élményt. Fel kell sorolnia a csiszolt navigációs elemeket, például a címkét, az értékeket, a jelölőnégyzeteket és a számlálót. Az Azure Cognitive Search REST API platformfüggetlen, ezért bármilyen nyelvet és platformot használhat. A legfontosabb, hogy tartalmazza a felhasználói felület elemeit, amelyek támogatják a növekményes frissítést, a frissített felhasználói felület állapotát, ahogy minden további aspektusa van kiválasztva. 

Lekérdezési időben az alkalmazáskód létrehoz `facet=[string]`egy kérelmet, amely tartalmazza a kérés paramétert, amely biztosítja a mező tavási. Egy lekérdezés több adhatja, `&facet=color&facet=category&facet=rating`például a , amelyeket egy -& karakter választ el egymástól.

Az alkalmazáskódnak `$filter` egy kifejezést is létre kell, hogy kezelje a kattintási eseményeket a ráfelé irányuló navigációban. A `$filter` csökkenti a keresési eredményeket, a facet értéket használva szűrőfeltételként.

Az Azure Cognitive Search egy vagy több megadott kifejezés, valamint a sokoldalú navigációs struktúra frissítései alapján adja vissza a keresési eredményeket. Az Azure Cognitive Search, a sokoldalú navigáció egy egyszintű konstrukció, a facet értékeket, és megszámolja, hogy hány találat található mindegyikhez.

A következő szakaszokban közelebbről megvizsgáljuk, hogyan építsük meg az egyes részeket.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Az index létrehozása
A faceting mezőalapú alapon engedélyezve van az indexben, ezen `"Facetable": true`az index attribútumon keresztül: .  
A sokoldalú navigációban esetleg használható összes mezőtípus alapértelmezés szerint az. `Facetable` Az ilyen `Edm.String`mezőtípusok közé tartozik a , `Edm.DateTimeOffset`és az összes numerikus mezőtípus (lényegében minden mezőtípus lapkaként használható, kivéve `Edm.GeographyPoint`a , amely nem használható a sokoldalú navigációban). 

Index létrehozásakor a felületes navigáció ajánlott eljárása explicit módon kikapcsolni a faceting mezők, amelyek soha nem használható, mint a facet.  Különösen az egyedi értékek karakterláncmezőit, például az azonosítót vagy a `"Facetable": false` terméknevet úgy kell beállítani, hogy megakadályozza azok véletlen (és hatástalan) használatát a sokoldalú navigációban. A lefelé történő lekapcsolása ott, ahol nincs rá szükség, segít az index méretének kis méretében tartani, és általában javítja a teljesítményt.

Az alábbiakban a Job Portal Bemutató mintaalkalmazás sémájának része, amely a méret csökkentése érdekében néhány attribútumot levágott:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Amint látható a mintasémában, `Facetable` ki van kapcsolva a karakterlánc-mezők, amelyek nem használható a facets, például az azonosító értékeket. A lefelé történő lekapcsolása ott, ahol nincs rá szükség, segít az index méretének kis méretében tartani, és általában javítja a teljesítményt.

> [!TIP]
> Ajánlott eljárásként adja meg az egyes mezők tárgymutató-attribútumainak teljes készletét. Bár `Facetable` alapértelmezés szerint be van kapcsolva szinte minden mezőesetében, az egyes attribútumok szándékos beállítása segíthet az egyes séma-döntések következményeinek átgondolásában. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Az adatok ellenőrzése
Az adatok minősége közvetlen hatással van arra, hogy a sokoldalú navigációs struktúra a várakozásoknak megfelelően valósul-e meg. Az eredményhalmaz csökkentése érdekében a szűrők egyszerű felépítésére is hatással van.

Ha márkával vagy árral szeretne szembeszállni, minden dokumentumnak *tartalmaznia* kell a Márkanév és *a ProductPrice* értékét, amelyek érvényesek, konzisztensek és hatékonyak szűrőként.

Íme néhány emlékeztető, hogy mire kell súrolni:

* Minden olyan mezőesetében, amellyel szembe szeretne nézni, tegye fel magának a kérdést, hogy tartalmaz-e olyan értékeket, amelyek megfelelőek az önirányított keresésben. Az értékeknek rövidnek, leíró jellegűeknek és kellően megkülönböztetőnek kell lenniük ahhoz, hogy egyértelmű választási lehetőséget kínáljanak a versengő lehetőségek között.
* Elírások vagy majdnem egyező értékek. Ha a Szín mezőt, és a mezőértékek közé tartozik az Orange és az Ornage (elírás), a Szín mezőn alapuló lapt mindkét mező veszi fel.
* A vegyes kis- és nagybetűk szövege a sokoldalú navigációban is pusztítást okozhat, a narancssárga és az orange pedig két különböző értékként jelenik meg. 
* Az azonos értékű egy- és többes verziók mindegyikhez külön-külön is eredményezhetnek.

Képzelheti, hogy az adatok előkészítésében tanúsított gondosság a hatékony sokoldalú navigáció alapvető eleme.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>A felhasználói felület létrehozása
A bemutatórétegből való visszalépés segíthet feltárni azokat a követelményeket, amelyek egyébként kimaradhatnak, és megismerheti, hogy mely képességek elengedhetetlenek a keresési élményhez.

A felhasználóalapú navigáció szempontjából a web- vagy alkalmazásoldal megjeleníti a sokoldalú navigációs struktúrát, észleli a felhasználói bevitelt az oldalon, és beszúrja a módosított elemeket. 

A webes alkalmazások, AJAX gyakran használják a bemutató réteg, mert lehetővé teszi, hogy frissítse növekményes változásokat. Használhatja ASP.NET MVC-t vagy bármely más vizualizációs platformot, amely http-n keresztül csatlakozhat egy Azure Cognitive Search szolgáltatáshoz. A jelen cikkben hivatkozott mintaalkalmazás – az **Azure Cognitive Search Állásportál bemutatója** – történetesen egy ASP.NET MVC-alkalmazás.

A mintában a sokoldalú navigáció be van építve a keresési eredmények oldalára. A következő példa, `index.cshtml` amely a mintaalkalmazás fájljából származik, a kifejezésalapú navigáció keresési eredményoldalon való megjelenítéséhez szükséges statikus HTML-struktúrát mutatja be. A facets listája épül fel, vagy újraépíteni dinamikusan, amikor beküld egy keresési kifejezést, vagy válasszon vagy törölje a szempontot.

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

A következő kódrészlet az `index.cshtml` oldalról dinamikusan létrehozza a HTML-t az első adatlap, az Üzleti cím megjelenítéséhez. Hasonló függvények dinamikusan építeni a HTML a többi aspektusa. Minden egyes lapszám rendelkezik egy címkével és egy számlálóval, amely az adott értékeredményhez talált elemek számát jeleníti meg.

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
> A keresési eredményoldal tervezésekor ne felejtsen el megadni egy mechanizmust a lapttek törléséhez. Ha jelölőnégyzeteket ad hozzá, könnyen láthatja, hogyan törölheti a szűrőket. Más elrendezések esetén szükség lehet egy zsemlemorzsa mintára vagy más kreatív megközelítésre. Például az Álláskereső portál mintaalkalmazás, `[X]` kattintson a miután a kiválasztott aspektusa törölje a kis- és nagyrészt.

<a name="buildquery"></a>

## <a name="build-the-query"></a>A lekérdezés létrehozása
Az épületlekérdezésekhez írt kódnak meg kell adnia az érvényes lekérdezés minden részét, beleértve a keresési kifejezéseket, a részadatokat, a szűrőket, a pontozási profilokat – bármit, amit a kérelem megfogalmazásához használnak. Ebben a szakaszban azt vizsgálja, ahol a metszetek illeszkednek egy lekérdezésbe, és hogyan szűrők et használ a laptokkal, hogy csökkentett eredményhalmazt.

Figyelje meg, hogy a facets szerves részét képezik a minta alkalmazás. A keresési élmény a Job Portal Demo köré csiszolt navigáció és szűrők. A jellemzőalapú navigáció kiemelkedő elhelyezése az oldalon bizonyítja annak fontosságát. 

Egy példa gyakran jó kiindulópont. A következő példa a `JobsSearch.cs` fájlból kivett kérelmet hoz létre, amely a vállalkozás címe, helye, könyvelési típusa és minimális fizetése alapján hoz létre mezőnavigációt. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

A lapadat-lekérdezési paraméter mezőre van beállítva, és az adattípustól függően további paraméterezhető `interval:<integer>`a `values:<list>`vesszővel tagolt listával, amely tartalmazza `count:<integer>`a `sort:<>`, , és . A numerikus adatok értéklistája a tartományok beállításakor támogatott. A használati részletekért lásd: [Keresési dokumentumok (Azure Cognitive Search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

A kis- és nagyarányú, az alkalmazás által megfogalmazott kérelemhez szűrőket is létre kell hoznia a jelölt dokumentumok egy kisértékű érték kiválasztása alapján történő leszűkítéséhez. Egy kerékpár bolt, csiszolt navigáció nyomokat a kérdésekre, mint *a Milyen színek, gyártók, és típusú kerékpárok állnak rendelkezésre?*. Szűrés választ kérdésekre, mint *a pontos kerékpárok piros, mountain bike, ebben az árkategóriában?*. Ha a "Piros" gombra kattint, és jelzi, hogy csak `$filter=Color eq 'Red'`a vörös termékek jelenjenek meg, az alkalmazás által küldött következő lekérdezés tartalmazza a .

Ha a Vállalkozáscím mezőben kiválaszt a kiválasztott Üzleti címet, a következő kódrészlet az `JobsSearch.cs` oldalról hozzáadja a kiválasztott Üzleti címet a szűrőhöz.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

### <a name="indexing-tips"></a>Tippek indexelési tippek
**Az index hatékonyságának növelése, ha nem használ keresőmezőt**

Ha az alkalmazás kizárólag a dimenziós navigációt használja (azaz nincs `searchable=false` `facetable=true` keresőmező), megjelölheti a mezőt a , kompaktabb index létrehozásához. Ezenkívül az indexelés csak a teljes dimenzióértékeken történik, a többszavas érték összetevőinek szótörése vagy indexelése nélkül.

**Adja meg, hogy mely mezők használhatók lapként**

Emlékezzünk vissza, hogy az index sémája határozza meg, hogy mely mezők használhatók facetként. Feltételezve, hogy egy mező lapkaként érhető el, a lekérdezés határozza meg, hogy mely mezőkkel kell átnézni. A mező, amellyel szemben szerepel, a címke alatt megjelenő értékeket adja meg. 

Az egyes címkék alatt megjelenő értékeket a rendszer az indexből olvassa be. Ha például a színmező *szín,* a további szűréshez rendelkezésre álló értékek az adott mező értékei – piros, fekete stb.

Csak Numerikus és DateTime értékek esetén explicit módon állíthat be értékeket a facet mezőben `facet=Rating,values:1|2|3|4|5`(például). Ezekhez a mezőtípusokhoz értéklista engedélyezett, így a ponteredmények egymástól függő tartományokra (numerikus értékeken vagy időszakokon alapuló tartományokra) történő elkülönítésének egyszerűsítése érdekében. 

**Alapértelmezés szerint csak egy szintű felületalapú navigációs szintlehet.** 

Mint megjegyezte, nincs közvetlen támogatás a hierarchia beágyazási aspektusait. Alapértelmezés szerint az Azure Cognitive Search csak egy szintű szűrők et támogat. Vannak azonban lehetséges megoldások. Hierarchikus szempontstruktúrát hierarchikus szempontstruktúrát `Collection(Edm.String)` hierarchiánként egy belépési ponttal kódolhat. A megoldás megvalósítása nem tartozik a jelen cikk hatálya alá. 

### <a name="querying-tips"></a>Tippek lekérdezése
**Mezők ellenőrzése**

Ha dinamikusan, nem megbízható felhasználói bevitel alapján hoz létre a lapttartalmazók listáját, ellenőrizze, hogy a lesibe adott mezők nevei érvényesek-e. Vagy elkerülheti a neveket, amikor `Uri.EscapeDataString()` URL-eket hoz fel a .NET-ben vagy a választott platformmegfelelő használatával.

### <a name="filtering-tips"></a>Szűrési tippek
**A keresési pontosság növelése szűrőkkel**

Használjon szűrőket. Ha csak a keresési kifejezésekre támaszkodik, a származtatás olyan dokumentum visszaadását okozhatja, amely egyik területén sem rendelkezik a pontos értékértékkel.

**A keresési teljesítmény növelése szűrőkkel**

A szűrők szűkítik a keresési jelölt dokumentumok készletét, és kizárják őket a rangsorolásból. Ha nagy mennyiségű dokumentummal rendelkezik, a szelektív részletezés használata gyakran jobb teljesítményt nyújt.
  
**Csak a kihatozott mezők szűrése**

A jellemzőalapú részletezésben általában csak olyan dokumentumokat szeretne felvenni, amelyek egy adott (jellemzőalapú) mezőben a pontértékkel rendelkeznek, és nem az összes kereshető mező ben. Szűrő hozzáadása megerősíti a célmezőt azáltal, hogy a szolgáltatást úgy irányítja, hogy csak a sokoldalú mezőben keressen egy megfelelő értéket.

**A metszeti aspektusaeredményeket több szűrővel**

A facet-találatok olyan dokumentumok, amelyek a keresési eredményekben egyeznek meg. A következő példában a *felhőalapú számítástechnikára*vonatkozó keresési eredményekben 254 elem rendelkezik *tartalomtípusként belső specifikációval* is. A tételek nem feltétlenül zárják ki egymást. Ha egy cikk mindkét szűrő feltételeinek megfelel, akkor mindegyikben beleszámítja. Ez a párhuzamosság akkor lehetséges, `Collection(Edm.String)` ha mezőket fordít, amelyeket gyakran használnak a dokumentum címkézésének megvalósításához.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Általában, ha úgy találja, hogy a facet eredmények következetesen túl nagy, azt javasoljuk, hogy adjon hozzá további szűrőket, hogy a felhasználók több lehetőséget a keresés szűkítésére.

### <a name="tips-about-result-count"></a>Tippek az eredményszámhoz

**A lapszám-navigációelemeiszám korlátozása**

A navigációs fa minden egyes kihatozott mezőjéhez 10 érték alapértelmezett korlát van érvényben. Ennek az alapértelmezett beállításnak van értelme a navigációs struktúrák számára, mivel az értéklistát kezelhető méretben tartja. Az alapértelmezést felülírhatja, ha egy értéket rendel a számláláshoz.

* `&facet=city,count:5`megadja, hogy csak az első öt város található a legmagasabb rangsorolt eredmények et ad vissza, mint egy facet eredmény. Fontolja meg a "repülőtér" és 32 egyezéskeresési kifejezést betekintő mintalekérdezést. Ha a lekérdezés `&facet=city,count:5`megadja, csak az első öt egyedi város, ahol a legtöbb dokumentum szerepel a keresési eredményekben, szerepel a keresési eredményekközött.

Figyelje meg a különbséget a facet eredmények és a keresési eredmények között. A keresési eredmények a lekérdezésnek megfelelő dokumentumok. A facet eredmények az egyes csoportok értékének egyezései. A példában a keresési eredmények közé tartozik a város nevek, amelyek nem szerepelnek a facet besorolási listán (5 a példában). A sokoldalú navigáción keresztül kiszűrt eredmények akkor válnak láthatóvá, ha törli a tetszési szöget, vagy a Város mellett más szemszögeket választ. 

> [!NOTE]
> Megvitatása, `count` ha van több típus is zavaró lehet. Az alábbi táblázat röviden összefoglalja, hogyan használják a kifejezést az Azure Cognitive Search API-ban, a mintakódban és a dokumentációban. 

* `@colorFacet.count`<br/>
  A bemutató kód, meg kell jelennie egy számláló paraméter a facet, megjelenítéséhez használt a facet eredmények száma. A facet eredmények, a szám azt jelzi, hogy a dokumentumok száma, amelyek megfelelnek a facet kifejezés vagy tartomány.
* `&facet=City,count:12`<br/>
  Egy facet lekérdezésben beállíthatja a darabszámot egy értékre.  Az alapértelmezett érték 10, de magasabbra vagy alacsonyabbra is beállítható. Beállítás `count:12` leveszi a top 12 találat a facet eredmények dokumentum szám.
* "`@odata.count`"<br/>
  A lekérdezési válaszban ez az érték a keresési eredményekben szereplő egyező elemek számát jelzi. Átlagosan nagyobb, mint az összes facet-találat együttes összege, a keresési kifejezésnek megfelelő, de nem rendelkeznek a szembeni értékegyezések miatt.

**Számok megjelenítése a facet eredményekben**

Amikor szűrőt ad hozzá egy faceted lekérdezéshez, érdemes megtartani `facet=Rating&$filter=Rating ge 4`a facet utasítást (például). Technikailag facet=Értékelés nem szükséges, de megtartva azt adja vissza a 4-es és magasabb minősítések facet értékek számát. Ha például a "4" gombra kattint, és a lekérdezés tartalmaz egy "4" vagy azzal egyenlő szűrőt, a rendszer minden 4-es vagy magasabb minősítésnél számot ad vissza.  

**Győződjön meg róla, hogy pontos adhatja a számokkal**

Bizonyos körülmények között előfordulhat, hogy a facet számok száma nem egyezik meg az eredményhalmazokkal [(lásd: Faceted navigation az Azure Cognitive Search (fórumbejegyzés) című témakört).](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)

A facet-számok pontatlanok lehetnek a nagyítási architektúra miatt. Minden keresési index több szegmensek, és minden szegmens jelenti a felső N-facets dokumentumszám szerint, amely ezután egyetlen eredménybe. Ha egyes szegmensek sok egyező értékkel rendelkeznek, míg mások kevesebb, előfordulhat, hogy néhány facet értékek hiányoznak, vagy alul-számítaz eredményekben.

Bár ez a viselkedés bármikor változhat, ha ma találkozik ezzel a viselkedéssel, megkerülheti azt a számláló mesterséges felfújásával:\<a szám> nagy számra, hogy az egyes szegmensek teljes jelentési kényszerítése. Ha a darabszám értéke: nagyobb vagy egyenlő a mezőben lévő egyedi értékek számával, akkor garantáltan pontos eredményeket kell elérnie. Ha azonban a dokumentumok száma magas, teljesítménybüntetés van, ezért ezt a lehetőséget megfontoltan használja.

### <a name="user-interface-tips"></a>Tippek a felhasználói felülethez
**Címkék hozzáadása az egyes mezőhöz a mezőalapú navigációban**

A címkék általában HTML-ben vagy űrlapon vannak definiálva (`index.cshtml` a mintaalkalmazásban). Nincs API az Azure Cognitive Search a kis- és felületi navigációs címkék vagy bármely más metaadatok.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Tartományon alapuló szűrés
Értéktartományok on-val való faceting gyakori keresési alkalmazás követelmény. Numerikus adatok és DateTime értékek esetén a tartományok támogatottak. Az egyes megközelítésekről a [Keresési dokumentumok (Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)című dokumentumban olvashat bővebben.

Az Azure Cognitive Search leegyszerűsíti a tartományépítést azáltal, hogy két módszert biztosít egy tartomány kiszámításához. Mindkét megközelítés esetén az Azure Cognitive Search létrehozza a megfelelő tartományokat a megadott bemenetek miatt. Ha például 10|20|30 tartományértékeket ad meg, az automatikusan 0-10, 10-20, 20-30 tartományt hoz létre. Az alkalmazás opcionálisan eltávolíthatja az üres intervallumokat. 

**1. megközelítés: Használja az intervallum paramétert**  
Az ár-aránciák 1000-es lépésekben való beállításához adja meg a következőket:`&facet=price,interval:10`

**2. megközelítés: Értéklista használata**  
Numerikus adatokhoz értéklistát is használhatunk.  Tekintsük a mező `listPrice` lapttartományát, amely a következőképpen jelenik meg:

  ![Mintaértékek listája](media/search-faceted-navigation/Facet-5-Prices.PNG "Mintaértékek listája")

Az előző képernyőképen szereplőhöz hasonló értéktartomány megadásához használjon értéklistát:

    facet=listPrice,values:10|25|100|500|1000|2500

Minden tartomány a 0-t használja kiindulási pontként, a listából végpontként megadott értéket, majd az előző tartományt levágja, hogy diszkrét intervallumokat hozzon létre. Az Azure Cognitive Search ezeket a funkciókat a felületes navigáció részeként végzi el. Az egyes intervallumok strukturálásához nem kell kódot írnia.

### <a name="build-a-filter-for-a-range"></a>Szűrő létrehozása tartományhoz
A kijelölt tartomány alapján történő dokumentumok szűréséhez `"lt"` használhatja a és a `"ge"` szűrőoperátorokat egy kétrészes kifejezésben, amely meghatározza a tartomány végpontjait. Ha például egy `listPrice` mezőhöz a 10-25 tartományt `$filter=listPrice ge 10 and listPrice lt 25`választja, a szűrő a . A mintakódban a szűrőkifejezés a **priceFrom** és **a priceTo** paramétereket használja a végpontok beállításához. 

  ![Lekérdezés egy értéktartományhoz](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Lekérdezés egy értéktartományhoz")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Szűrés távolság alapján
Gyakori, hogy olyan szűrőket lát, amelyek segítenek kiválasztani egy üzletet, éttermet vagy úti célt az aktuális tartózkodási hely hez való közelsége alapján. Bár az ilyen típusú szűrő úgy tűnhet, mint a sokoldalú navigáció, ez csak egy szűrő. Megemlítjük, hogy itt azoknak, akik kifejezetten keres végrehajtási tanácsot, hogy az adott tervezési probléma.

Az Azure Cognitive Search két térinformatikai függvénye, **a geo.distance** és a **geo.intermets**.

* A **geo.distance** függvény két pont közötti távolságot adja vissza kilométerben. Az egyik pont egy mező, a másik pedig a szűrő részeként átadott állandó. 
* A **geo.intersects** függvény igaz értéket ad vissza, ha egy adott pont egy adott sokszögen belül van. A pont egy mező, és a sokszög a szűrő részeként átadott koordináták állandó listájaként van megadva.

Szűrőpéldákat az [OData-kifejezés szintaxisában (Azure Cognitive Search)](query-odata-filter-orderby-syntax.md)találhat.

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Próbálja ki a demót
Az Azure Cognitive Search Állásportál bemutató jave a cikkben hivatkozott példákat tartalmazza.

-   Tekintse meg és tesztelje a munkabemutatót online az [Azure Cognitive Search Állásportál bemutatóján.](https://aka.ms/azjobsdemo)

-   Töltse le a kódot az [Azure-minták tártárból a GitHubon.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

A keresési eredmények kel végzett munka során tekintse meg az URL-címet a lekérdezések felépítésében bekövetkező változásokhoz. Ez az alkalmazás történetesen hozzáfűzi facets az URI-hoz, ahogy kiválasztja mindegyiket.

1. A bemutatóalkalmazás leképezési funkciójának használatához szerezzen be egy Bing Térképek kulcsot a [Bing Térképek fejlesztői központból.](https://www.bingmapsportal.com/) Illessze be a `index.cshtml` lap meglévő kulcsa fölé. A `BingApiKey` fájlban `Web.config` lévő beállítás nem használatos. 

2. Futtassa az alkalmazást. Vigye el a választható bemutatót, vagy utasítsa el a párbeszédpanelt.
   
3. Írjon be egy keresési kifejezést, például "elemző", és kattintson a Keresés ikonra. A lekérdezés végrehajtása gyorsan.
   
   A rendszer egy sokoldalú navigációs struktúrát is visszaad a keresési eredményekkel együtt. A keresési eredmény lapon a sokoldalú navigációs struktúra tartalmazza az egyes egyes egyes aspektusa-eredmény számait. Nincs enek kijelölve a leadott áttéteket, így az összes egyező eredmény ad vissza.
   
   ![Keresési eredmények a laptök kiválasztása előtt](media/search-faceted-navigation/faceted-search-before-facets.png "Keresési eredmények a laptök kiválasztása előtt")

4. Kattintson egy vállalkozás címére, helyére vagy minimális fizetésére. A kezdeti keresésnél a facets értékei null értékűek voltak, de az értékek elérésekor a keresési eredmények et levágják az olyan elemek, amelyek már nem egyeznek.
   
   ![Keresési eredmények a laptök kiválasztása után](media/search-faceted-navigation/faceted-search-after-facets.png "Keresési eredmények a laptök kiválasztása után")

5. A faceted lekérdezés törléséhez, hogy meg próbálhassa a különböző lekérdezési viselkedéseket, kattintson az után a `[X]` kiválasztott facets törölje a facets.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Részletek
Tekintse meg [az Azure Cognitive Search Deep Dive -t.](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) 45:25-kor van egy demó arról, hogyan valósítható meg a ta-k.

A főre mutató navigáció tervezési alapelveivel kapcsolatos további információkért az alábbi hivatkozásokat javasoljuk:

* [Tervezési minták: Faceted Navigation](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front End aggodalmak végrehajtása során faceted keresés - 1. rész](https://articles.uie.com/faceted_search2/)

