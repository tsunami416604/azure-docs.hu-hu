---
title: Relevanciaprofil hozzáadása a search-index – Azure Search
description: Pontozási profilok hozzáadásával jelentősen növelheti a keresési rangsorolják pontszámait az Azure Search keresési eredmények.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: eae7de00294a6a09cb7f942d11ee2391710fc55f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008488"
---
# <a name="add-scoring-profiles-to-an-azure-search-index"></a>Relevanciaprofil hozzáadása az Azure Search-index

  Pontozás hivatkozik, a számítás egy *keresési pontszáma* minden elem keresési eredmények. A pontszám, azt jelzi, hogy a cikk relevanciájának az aktuális keresési művelet keretében. Minél nagyobb a pontszám, több megfelelő az elemet. Keresési eredmények között a cikkeket rank, a magas, alacsony, az egyes elemekhez tartozó számított keresési pontszámok alapján rendezve.  

 Az Azure Search egy kezdeti pontszám számítási pontozási alapértelmezett használja, de testre szabhatja a számítás keresztül egy *relevanciaprofil*. Pontozási profilok lehetővé teszik, az elemek rangsorolási nagyobb mértékben vezérelheti a keresési eredmények között. Például érdemes jelentősen növelheti a bevételnövelés alapján elemek, az újabb elemek előléptetése vagy esetleg jelentősen növelheti az elemeket, amelyeket a készletben túl hosszú.  

 A relevanciaprofil az index definícióját, súlyozott mezők, funkciók és paraméterek álló részét képezi.  

 Támpontot, mi a relevanciaprofil néz ki, hogy az alábbi példa bemutatja egy "földrajzi" nevű egyszerű profilt. Ez egy serkenti az elemek, amelyekhez a keresési kifejezést a a **Mezőmeghatározása** mező. Emellett a `distance` függvényt, hogy tíz adja meg kilométerben, ahol a jelenlegi hely belül elemek alkalmazást. Ha valaki "adóazonosító" kifejezést keresi, és a "adóazonosító" Szálloda neve részeként történik, dokumentumok, amelyek tartalmazzák a "Hotels" a "adóazonosító" az aktuális hely 10 KM sugarú körön belül nagyobb a keresési eredmények között megjelenik.  


```  
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 A relevanciaprofil használatához adja meg a profil, a lekérdezési karakterlánc meg a lekérdezés megfogalmazása. Figyelje meg az alábbi lekérdezést a lekérdezési paraméter `scoringProfile=geo` a kérésben.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2017-11-11  
```  

 Ez a lekérdezés "adóazonosító" kifejezést keresi, és átadja az aktuális helyen. Vegye figyelembe, hogy ez a lekérdezés például tartalmazza-e más paramétereket, `scoringParameter`. Lekérdezési paraméterek ismertetett [dokumentumok keresése &#40;Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Kattintson a [példa](#bkmk_ex) egy részletes példa a relevanciaprofil áttekintéséhez.  

## <a name="what-is-default-scoring"></a>Mi az alapértelmezett pontozási?  
 A pontozás kiszámítja a keresési pontszámok rangsorolják rendezett eredményhalmazt minden elemére. Minden eleme egy keresési eredményhalmaz keresési pontszámok hozzárendelve, akkor legalacsonyabb, legmagasabb rangsorolt. A magasabb pontszámokat elemeket visszaküldi az alkalmazáshoz. Alapértelmezés szerint az első 50 adja vissza, de használhatja a `$top` paramétert (legfeljebb 1000 egyetlen válasz) elemek kisebb vagy nagyobb számának visszaadása.  

A keresési pontszámtól statisztikai az adatok és a lekérdezés tulajdonságai alapján számítja ki. Az Azure Search megkeresi a lekérdezési karakterláncban a keresési feltételeket tartalmazó dokumentum (vagy egésze, attól függően, `searchMode`), a keresési kifejezés több példányát tartalmazó dokumentumok beállítva. A keresési pontszámtól megnő még magasabb, ha az adatok korpusz, de közös, ahányszor a jelen dokumentumban lévő kifejezés egy ritka. Ez a megközelítés a számítástechnikai relevancia alapján végzett alapjaként néven [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) vagy kifejezés gyakorisága – inverz dokumentum gyakoriságát.  

 Feltételezve, hogy egyéni rendezés, eredmények majd rangsora szerint keresési pontszáma előtt visszakerül a hívó alkalmazás. Ha $top nincs megadva, a rendszer a legmagasabb keresési pontszámtól kellene 50 elemeket adja vissza.  

 Keresési eredmény értékeit is megismételhető, teljes eredményhalmaz. Például előfordulhat, hogy rendelkezik a pontszámot 1.2, 10 tétel 1.0 pontszámot 20 cikket, és 20 tételt a pontszámot pedig 0,5. Ha több találatok száma az ugyanazon keresési pontszámtól, azonos pontozott elemek sorrendje nincs definiálva, és nem stabil. Futtassa újra a lekérdezést, és láthat elemek shift pozíciója. Adja meg egy azonos pontszám a két elem, nincs garancia arra, melyiket jelenik meg először.  

## <a name="when-to-use-custom-scoring"></a>Mikor érdemes használni az egyéni relevancia  
 Létre kell hoznia egy vagy több pontozási profilok, ha az alapértelmezett viselkedés prioritása nem sokkal elegendő a megfelel az üzleti célok. Például dönthet úgy, hogy a keresés relevancia alapján végzett részesítse újonnan hozzáadott elemeket. Hasonlóképpen lehetséges, hogy nyereség tartalmazó mezőt, vagy lehetséges bevétel utaló más területről. Üzleti előnyök által a találatok kiemelése, lehet, hogy fontos tényező pontozási profilok használata mellett döntenek.  

 Pontozási profilok segítségével is használják a alkalmazhatóságát-alapú rendezés. Fontolja meg a keresési eredmények lapok, amelyekkel az árat, dátum, minősítés vagy relevancia alapján végzett rendezés korábban már használta. Az Azure Search szolgáltatásban a pontozási profilok meghajtó a "Relevancia alapján végzett" lehetőséget. A relevancia alapján végzett meghatározását, való határozza meg a üzleti célokhoz és a keresési funkciót szeretne típusát határozza meg.  

##  <a name="bkmk_ex"></a> Példa  
 Az indexsémában meghatározott egy vagy több pontozási profilok korábban feljegyzett testre szabott pontozási biztosítják.  

 Ez a példa bemutatja egy index ugyanezzel a pontozási profilok két sémája (`boostGenre`, `newAndHighlyRated`). Bármely ezt az indexet egy lekérdezési paraméter pontszámot rendelni az eredményhalmaz fogja használni a profilt, vagy a profil tartalmazó lekérdezést.  

```  
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Munkafolyamat  
 Egyéni pontozási viselkedés implementálásához az indexet meghatározó sémát a relevanciaprofil hozzáadása. Pontozási profilok index belül legfeljebb 100 lehet (lásd: [Szolgáltatáskorlátok](search-limits-quotas-capacity.md)), de bármely adott lekérdezés időpontban csak megadhat egy profilt.  

 Kezdje a [sablon](#bkmk_template) ebben a témakörben található.  

 Adjon meg egy nevet. Pontozási profilok nem kötelező, de ha hozzáad egy, a nevének megadása kötelező. Ügyeljen arra, hogy az elnevezési konvencióinak mezők (egy betűvel kezdődik elkerüli a különleges karakterek és fenntartott szavak). Lásd: [elnevezési szabályok &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) a teljes listát.  

 A relevanciaprofil törzse súlyozott mezőket és a functions jön létre.  

|||  
|-|-|  
|**Súlyok**|Adja meg a név-érték párok, amely a relatív súly hozzárendelése a mezőhöz. Az a [példa](#bkmk_ex), albumTitle műfaj és artistName mezőket a gyorsított 1.5-ös, 5 és a 2 jelölik. Miért van így sokkal magasabb, mint a többi műfaj súlyozott? Ha keresés, amely valamivel homogén adatokon végez (hasonlóan a "műfaj" az a `musicstoreindex`), szüksége lehet egy relatív súlya nagyobb eltérés. Például a `musicstoreindex`, mind a műfaj, és egyformán phrased műfaj leírásában megadottaknak megfelelően jelenik "rock". Ha azt szeretné, hogy a műfaj leírás nyomósabbak műfaj, a műfaj mező egy sokkal magasabb relatív súly kell.|  
|**Functions**|Ha további számításokat szükség az adott környezetben használni. Érvényes értékek a következők `freshness`, `magnitude`, `distance`, és `tag`. Minden függvény azt az egyedi paramétereket rendelkezik.<br /><br /> -   `freshness` Ha azt szeretné, hogy miként használható szerint hogyan új vagy régi egy elemet. Ez a funkció csak akkor használható a `datetime` mezők (edm. DataTimeOffset). Megjegyzés: a `boostingDuration` attribútum csak a használatos a `freshness` függvény.<br />-   `magnitude` Ha szeretné növelése érdekében hogyan csúcs alapján, illetve alacsony számérték használandó. Hívja meg ezt a funkciót a forgatókönyvek például kiemelési haszonkulcs, legmagasabb árat, legalacsonyabb ár vagy letöltések számát. Ez a funkció csak a dupla és egész szám mező használható.<br />     Az a `magnitude` függvény, megfordíthatja a tartományt, magas, alacsony, ha azt szeretné, hogy a más néven inverz minta (például, hogy több, mint a magasabb szintű árú elemek alacsonyabb árú boost elem). 100 $ $ 1 árak számos tekintve lenne beállítva `boostingRangeStart` 100 és `boostingRangeEnd` 1 alacsonyabb árú elemek növelése érdekében.<br />-   `distance` Ha meg szeretné közelségi vagy földrajzi hely szerint növelheti használandó. Ez a funkció csak akkor használható a `Edm.GeographyPoint` mezőket.<br />-   `tag` Ha meg szeretné jelentősen növelheti a gyakori között a dokumentumokat és a keresési lekérdezések címkék szerint használandó. Ez a funkció csak akkor használható a `Edm.String` és `Collection(Edm.String)` mezőket.<br /><br /> **Funkciók használatára vonatkozó szabályok**<br /><br /> Függvény típusa (`freshness`, `magnitude`, `distance`), `tag` kisbetűs kell lennie.<br /><br /> Függvények nem tartalmazhat null értékű vagy üres értékeket. Kifejezetten Ha fieldname adja meg, akkor állítsa azt.<br /><br /> Függvények csak akkor alkalmazható, szűrhető mezők. Lásd: [a Create Index &#40;Azure Search szolgáltatás REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) szűrhető mezők további információt.<br /><br /> Függvények csak akkor alkalmazható, a mezők gyűjteménye az index meghatározott mezőkre.|  

 Az index meghatározása után az index létrehozása az indexsémát, kiegészítve a dokumentumok feltöltésével. Lásd: [a Create Index &#40;Azure Search szolgáltatás REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) és [törlése dokumentumok hozzáadása, frissítése vagy &#40;Azure Search szolgáltatás REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) útmutató ezeket a műveleteket. Az index létrehozása után rendelkeznie kell egy funkcionális relevanciaprofil, amely együttműködik a keresési adatait.  

##  <a name="bkmk_template"></a> sablon  
 Ez a szakasz bemutatja a szintaxist és a pontozási profilok sablonját. Tekintse meg [attributes dokumentáció Index](#bkmk_indexref) attribútumok leírását a következő szakaszban.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="bkmk_indexref"></a> Index attributes dokumentáció  

> [!NOTE]  
>  Pontozó függvény csak akkor alkalmazható, amelyek szűrhető mezők.  

|Attribútum|Leírás|  
|---------------|-----------------|  
|`Name`|Kötelező. Ez az a relevanciaprofil nevét. Ebből következik, hogy egy mező ugyanazokat az elnevezési konvenciókat. Ez egy betűvel kell kezdődnie, nem tartalmazhat pontokat, kettőspontokat @ szimbólum, és nem kezdődhet a "azureSearch" (kis-és nagybetűket) kifejezést.|  
|`Text`|A súlyok tulajdonság tartalmazza.|  
|`Weights`|Választható. Egy név-érték párhoz, amely megadja a mező nevét és a relatív súly. Relatív súly pozitív egész vagy lebegőpontos szám kell lennie. A maximális érték pedig int32. MaxValue.<br /><br /> A mező nevét, egy megfelelő súly nélkül is megadhat. Súlyok egy mezőt egy másik viszonyított fontosságát használatosak.|  
|`Functions`|Választható. Vegye figyelembe, hogy egy pontozó függvény csak a szűrhető mezők alkalmazható.|  
|`Type`|Pontozó függvények szükséges. Azt jelzi, hogy a használni kívánt függvény típusát. Érvényes értékek a következők: magnitude, frissesség, távolság és címkével. Az egyes pontozási profilok egynél több függvényt is felvehet. A függvény nevét a kisbetűs kell lennie.|  
|`Boost`|Pontozó függvények szükséges. A nyers pontszám szorzójaként használt pozitív szám. Nem lehet egyenlő 1.|  
|`Fieldname`|Pontozó függvények szükséges. Pontozó függvény csak akkor alkalmazható, amelyek a mező gyűjtemény az index részét képezik, és szűrhető mezők. Emellett minden egyes függvény vezet be további korlátozásokat (a frissesség mezőkkel dátum és idő, egész szám vagy double mezők nagyságát, és a távolság a földrajzi helyet megadó mezők szerepel). Csak egyetlen mező / függvény definíciója adható meg. Például magnitude kétszer ugyanazt a profilt a használatához, kellene két definíciók magnitude, egyet az egyes mezőkhöz tartozó tartalmazza.|  
|`Interpolation`|Pontozó függvények szükséges. Azt a görbét, amelynek a pontszámok kiemelése növekszik a tartomány kezdetétől a tartomány végéig. Érvényes értékek a következők: lineáris (alapértelmezett), állandó, kvadratikus és logaritmikus. Lásd: [interpolations beállítása](#bkmk_interpolation) részleteiről.|  
|`magnitude`|Pontozó függvény mértékétől módosítják a rangsort értékek számmező értéktartománya alapján történik. A leggyakoribb használati példák erre a következők:<br /><br /> -   **Csillagos minősítésekkel kapható:** Az ALTER a pontozás a "Star-értékelés" mező értéke alapján. Ha a két elem releváns, az elem a magasabb minősítéssel rendelkező először jelenik meg.<br />-   **Margin:** Ha két dokumentumot vonatkozó,-kereskedő Kezdésként dokumentumokon, amelyek magasabb margók először növelése érdekében.<br />-   **Kattintson a száma:** Alkalmazások, amelyek nyomon követik a kattintson a műveletek termékek vagy lapok keresztül, boost elemekre, amely általában a legtöbb forgalom nagyságának használatával.<br />-   **Töltse le a száma:** Alkalmazások követése letöltések, növeli magnitude funkció lehetővé teszi elemek, amelyek a legtöbb letöltések rendelkezik.|  
|`magnitude` &#124; `boostingRangeStart`|Beállítja a tartomány, amelyben magnitude sorolódik. Az értéknek egész vagy lebegőpontos szám kell lennie. 1-től 4 csillagosig 1 lenne. Az 50 % feletti nyereségek ez pedig 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Beállítja a tartomány, amelyben magnitude sorolódik záróértékét. Az értéknek egész vagy lebegőpontos szám kell lennie. 1-től 4 csillagosig ez lenne a 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Érvényes értékek: true vagy false (alapértelmezett). Ha értéke igaz, a teljes kiemelést továbbra is a alkalmazni a dokumentumokon, amelyek a, amelyek célmezőjének értéke magasabb, mint a tartomány felső végén értékét. Ha FALSE (hamis), a függvény kiemelése, a tartományon kívül esik, amelyek célmezőjének értéke nem alkalmazható.|  
|`freshness`|A frissességet pontozó függvény segítségével levő értékek alapján elemek rangsorolási pontszámait `DateTimeOffset` mezőket. Például a közelmúltban elemet is rangsorolni magasabb, mint a régebbi elemek.<br /><br /> Fontos megjegyezni, hogy is rangsorolják elemekhez, például a jövőbeli dátumok naptárbeli események lehetséges, hogy a jelenlegi elemek közelebb is rangsorolni magasabb, mint az elemek további a jövőben.<br /><br /> A szolgáltatás jelenlegi kiadása a tartomány egyik végén lesz kijavítva, az aktuális időpontnál. Az egyéb célból egyszerre a múltban alapján a `boostingDuration`. Jelentősen növelheti a jövőben alkalommal számos, használja a negatív `boostingDuration`.<br /><br /> A relevanciaprofil alkalmazott módosítja a kiemelési maximális és minimális tartomány határozza meg a interpolációs gyakorisága (lásd az alábbi ábra). A alkalmazni kiemelési tényező megfordításához válassza egy boost tényezője 1-nél kisebb.|  
|`freshness` &#124; `boostingDuration`|Beállít egy olyan lejárati időt, amely után az adott dokumentum kiemelése megszűnik. Lásd: [boostingDuration beállítása](#bkmk_boostdur) szintaxist és példákat a következő szakaszban.|  
|`distance`|A távolságot pontozó függvény segítségével hatással vannak a pontszám alapján hogyan dokumentumok zárja be, vagy távol vannak képest relatív hivatkozást földrajzi helyet. A referencia-hely van megadva a lekérdezést, paraméterben egy részeként (használatával a `scoringParameterquery` karakterlánc-beállítás), egy maga a szél argumentum.|  
|`distance` &#124; `referencePointParameter`|Hivatkozás helyként használandó lekérdezésekben paramétere. `scoringParameter` az egy lekérdezési paraméter. Lásd: [dokumentumok keresése &#40;Azure Search szolgáltatás REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) lekérdezési paraméterek leírását.|  
|`distance` &#124; `boostingDistance`|Egy szám, amely azt jelzi, hogy a távolságot adja meg kilométerben a hivatkozási helyről, ahol a kiemelési tartomány véget ér.|  
|`tag`|Pontozó függvény címke hatással vannak a pontszám a dokumentumok címkékkel a dokumentumok és a keresési lekérdezések alapján történik. Dokumentumokon, amelyek a keresési lekérdezés linkedint címkék fog növelhető. A címkék a keresési lekérdezés biztosított egyes keresési kérelem pontozási paramétert (használatával a `scoringParameterquery` karakterlánc-beállítás).|  
|`tag` &#124; `tagsParameter`|A paraméter egy adott kérés címkék adhatók meg lekérdezésekben. `scoringParameter` az egy lekérdezési paraméter. Lásd: [dokumentumok keresése &#40;Azure Search szolgáltatás REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) lekérdezési paraméterek leírását.|  
|`functionAggregation`|Választható. Érvényes, csak ha funkciók vannak megadva. Érvényes értékek a következők: sum (alapértelmezett), átlagos, minimális, maximális és firstMatching. Keresési pontszámok egyetlen több változó, beleértve a több funkciók kiszámított érték. Ez az attribútum hogyan az összes az funkciók egy egyszeri összesítés vannak egyesítve a kiadóvállalat növelése, amely jelzi, majd a dokumentum kiindulási pontszámot van alkalmazva. A pontszám alapján a [tf-idf](http://www.tfidf.com/) a dokumentumot, és a keresési lekérdezés alapján kiszámított érték.|  
|`defaultScoringProfile`|Egy keresési kérelmet végrehajtásakor, ha nincs relevanciaprofil van megadva, akkor a használt alapértelmezett pontozási ([tf-idf](http://www.tfidf.com/) csak).<br /><br /> Profilnév pontozási alapértelmezett állítható be itt nincs konkrét profil van megadva, a keresési kérelmet a profilhoz használandó Azure Search okozó.|  

##  <a name="bkmk_interpolation"></a> Set interpolations  
 Interpolations lehetővé teszik a használt pontozási meredekség alakját. Mivel a pontozási magastól alacsonyig, a görbét mindig csökken, de a interpolációs meghatározza, hogy a lefelé mutató meredekség görbe. A következő interpolations használhatók:  

|||  
|-|-|  
|`Linear`|A minimális és maximális hatótávolságán belül elemek a alkalmazni az elem boost végezheti el egy folyamatosan csökkenő összegét. Lineáris van az alapértelmezett interpolációs pontozási profilok.|  
|`Constant`|A kezdő és záró tartományon belül cikkek egy állandó kiemelés rangsorolják eredményeként lépnek érvénybe.|  
|`Quadratic`|Lineáris interpolációs, amely rendelkezik egy folyamatosan csökkenő boost, támogatáshoz képest korlátozottabb kvadratikus kezdetben csökkennek kisebb tempójában, és majd, megközelíti a tartomány vége, mivel csökkenti a sokkal magasabb történik. Ez a beállítás interpolációs címkében pontozó függvények nem engedélyezett.|  
|`Logarithmic`|Lineáris interpolációs, amely rendelkezik egy folyamatosan csökkenő boost, támogatáshoz képest korlátozottabb logaritmikus kezdetben csökkennek magasabb ütemben, és ezután azt megközelíti a tartomány vége, mivel csökkenti egy sokkal kisebb időközönként. Ez a beállítás interpolációs címkében pontozó függvények nem engedélyezett.|  

 ![Állandó, lineáris, négyzetösszege, log10 sorok grafikonon](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a> Set boostingDuration  
 `boostingDuration` az attribútum a `freshness` függvény. Az eszköz használatával állítsa be egy lejárati időszak után az adott dokumentum melyik kiemelése megszűnik. Például hogy miként egy termékvonal vagy márka egy 10 napos kedvezményes időszak, kell megadni a 10 napos időszak "P10D", a közzétett dokumentumokat.  

 `boostingDuration` egy XSD "nyelv szerinti dayTimeDuration" értékként (egy olyan ISO 8601 időtartamérték korlátozott részhalmaza) kell formázni. Ez a minta a következő: "P[nD][T[nH][nM][nS]]".  

 Az alábbi táblázatban néhány példa.  

|Időtartam|boostingDuration|  
|--------------|----------------------|  
|1 nap|"P1D"|  
|2 nap és 12 óra|"P2DT12H"|  
|15 perc|"PT15M"|  
|30 nappal, 5 óra, 10 percig, és 6.334 másodperc|"P30DT5H10M6.334S"|  

 További példák: [XML-séma: Adattípusok (W3.org webhely)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Lásd még  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Index létrehozása &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
