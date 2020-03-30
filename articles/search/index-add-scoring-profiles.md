---
title: A keresési rangsor növelése pontozási profilokkal
titleSuffix: Azure Cognitive Search
description: Növelje az Azure Cognitive Search találatainak keresési pontszámait pontozási profilok hozzáadásával.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
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
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245459"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Referenciaprofilok hozzáadása Azure Cognitive Search-indexekhez

  A pontozás a keresési eredményekben visszaadott minden elem *keresési pontszámának* kiszámítására vonatkozik. A pontszám az elemek relevanciáját jelzi az aktuális keresési művelet kontextusában. Minél nagyobb a pontszám, annál relevánsabb az elem. A keresési eredményekben az elemek rangsorolása magastól alacsonyig van rendezve az egyes elemekre kiszámított keresési pontszámok alapján.  

 Az Azure Cognitive Search az alapértelmezett pontozást használja a kezdeti pontszám kiszámításához, de a számítást egy *pontozási profilon*keresztül testreszabhatja. A pontozási profilok nagyobb ellenőrzést biztosítanak a keresési eredményekben szereplő elemek rangsorolása felett. Előfordulhat például, hogy a bevételi potenciáljuk alapján szeretné növelni a cikkeket, előléptetni az újabb cikkeket, vagy esetleg túl hosszú ideig a készletben lévő cikkeket.  

 A pontozási profil az indexdefiníció része, amely súlyozott mezőkből, függvényekből és paraméterekből áll.  

 Annak érdekében, hogy képet adjon arról, hogyan néz ki egy pontozási profil, a következő példa egy "geo" nevű egyszerű profilt mutat be. Ez kinöveli azokat az elemeket, amelyek keresési kifejezése a **hotelnév** mezőben található. Azt is `distance` használja a funkciót, hogy előnyben elemeket, amelyek tíz kilométeren belül az aktuális helyen. Ha valaki a "fogadó" és a "fogadó" kifejezésre keres, akkor a "fogadó" kifejezéssel rendelkező dokumentumok az aktuális hely 10 km-es sugarán belül magasabban jelennek meg a keresési eredmények között.  


```json
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


 A pontozási profil használatához a lekérdezés a lekérdezési karakterlánc profiljának megadására kerül. Az alábbi lekérdezésben figyelje `scoringProfile=geo` meg a lekérdezési paramétert a kérelemben.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Ez a lekérdezés a "fogadó" kifejezésre keres, és az aktuális helyen halad át. Figyelje meg, hogy ez a `scoringParameter`lekérdezés más paramétereket is tartalmaz, például a . A lekérdezési paraméterek leírása a [Search Documents &#40;Az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)ismerteti.  

 Kattintson a [Példa](#bkmk_ex) gombra a pontozási profil részletesebb példájának áttekintéséhez.  

## <a name="what-is-default-scoring"></a>Mi az alapértelmezett pontozás?  
 A pontozás egy rangsorolási sorban lévő minden egyes elem keresési pontszámát számítja ki. A keresési eredményhalmaz minden eleme keresési pontszámot kap, majd a legmagasabbtól a legalacsonyabbig rangsorolja. A magasabb pontszámmal rendelkező elemek et visszaadja az alkalmazásnak. Alapértelmezés szerint a felső 50 ad vissza, de a `$top` paraméter segítségével vissza adhat kisebb vagy nagyobb számú elem (legfeljebb 1000 egyetlen válasz).  

A keresési pontszám kiszámítása az adatok és a lekérdezés statisztikai tulajdonságai alapján történik. Az Azure Cognitive Search olyan dokumentumokat keres, amelyek tartalmazzák a `searchMode`keresési kifejezéseket a lekérdezési karakterláncban (néhány vagy az összes, attól függően), előnyben részesítve a keresési kifejezés számos példányát tartalmazó dokumentumokat. A keresési pontszám még magasabbra emelkedik, ha a kifejezés ritka az adatindexben, de gyakori a dokumentumban. A számítási relevancia ezen megközelítésének alapja a [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) vagy a kifejezés frekvencia-inverz dokumentumgyakoriság.  

 Feltéve, hogy nincs egyéni rendezés, az eredményeket a rendszer a keresési pontszám szerint rangsorolja, mielőtt visszakerülnének a hívó alkalmazásba. Ha $top nincs megadva, a rendszer 50, a legmagasabb keresési pontszámmal rendelkező elemet ad vissza.  

 A keresési pontszámértékek megismételhetők az eredményhalmazban. Előfordulhat például, hogy 10 elem, 1,2-es pontszámmal, 20 1,0-es pontszámmal, és 20 0,5-ös pontszámmal rendelkező elem. Ha több lekérés azonos keresési pontszámmal rendelkezik, az azonos pontozott elemek sorrendje nincs definiálva, és nem stabil. Futtassa újra a lekérdezést, és előfordulhat, hogy az elemek elmozdulnak a pozíciótól. Mivel két elem azonos pontszámot, nincs garancia, hogy melyik jelenik meg először.  

## <a name="when-to-use-custom-scoring"></a>Mikor kell egyéni pontozást használni?  
 Hozzon létre egy vagy több pontozási profilt, ha az alapértelmezett rangsorolási viselkedés nem megy elég messzire az üzleti célok elérésében. Dönthet például úgy, hogy a keresési relevancia az újonnan hozzáadott elemeket részesíti előnyben. Hasonlóképpen előfordulhat, hogy van egy mező, amely haszonkulcsot tartalmaz, vagy más, bevételi potenciált jelző mező. A találatok növelése, amelyek előnyökkel járnak a vállalkozás számára, fontos tényező lehet a pontozási profilok használatának eldöntésében.  

 A relevancia-alapú rendelés pontozási profilokon keresztül is megvalósul. Fontolja meg a korábban használt keresési eredményoldalakat, amelyek lehetővé teszik az ár, a dátum, az értékelés vagy a relevancia alapján történő rendezést. Az Azure Cognitive Search,pontozási profilok meghajtó a "relevancia" opciót. A relevancia meghatározását Ön szabályozza, az üzleti célokon és a kívánt keresési élmény típusán alapul.  

##  <a name="example"></a><a name="bkmk_ex"></a>Példa  
 Ahogy korábban említettük, a testreszabott pontozási egy vagy több, indexsémában definiált pontozási profilon keresztül valósul meg.  

 Ez a példa egy két pontozási profillal (`boostGenre` `newAndHighlyRated`, ) rendelkező index sémáját mutatja be. Az indexre vonatkozó minden olyan lekérdezés, amely bármelyik profilt lekérdezési paraméterként tartalmazza, a profilt fogja használni az eredményhalmaz pontozásához.  

```json
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
 Egyéni pontozási viselkedés megvalósításához adjon hozzá egy pontozási profilt az indexet meghatározó sémához. Egy indexen belül legfeljebb 100 pontozási profil tartozhat (lásd: [Szolgáltatáskorlátok),](search-limits-quotas-capacity.md)de egy adott lekérdezésben egyszerre csak egy profilt adhat meg.  

 Kezdje a témakörben található [sablonnal.](#bkmk_template)  

 Adjon meg egy nevet. A pontozási profilok megadása nem kötelező, de ha hozzáad egyet, a név megadása kötelező. Ügyeljen arra, hogy kövesse a mezők elnevezési konvencióit (betűvel kezdődik, elkerüli a speciális karaktereket és a fenntartott szavakat). A teljes listát [&#40;az Azure Cognitive Search&#41;elnevezési szabályok](https://docs.microsoft.com/rest/api/searchservice/naming-rules) című témakörben olvashat.  

 A pontozási profil teste súlyozott mezőkből és függvényekből épül fel.  

|||  
|-|-|  
|**Súlyok**|Adja meg azokat a név-érték párokat, amelyek relatív súlyt rendelnek egy mezőhöz. A [Példa mezőben](#bkmk_ex)a Title, a genre és az artistName mezők 1.5, 5 és 2. Miért van a műfaj lendületes sokkal magasabb, mint a többiek? Ha a keresés olyan adatokon keresztül történik, amelyek kissé homogének (mint `musicstoreindex`a "műfaj" esetében), akkor nagyobb varianciára lehet szükség a relatív súlyok között. A `musicstoreindex`, a "rock" például műfajként és azonos irányban megfogalmazott műfajleírásokban is megjelenik. Ha azt szeretné, műfaj meghaladja műfaj leírása, a műfaj területén lesz szükség egy sokkal nagyobb relatív súlya.|  
|**Functions**|Akkor használatos, ha bizonyos környezetekhez további számításokra van szükség. Az érvényes `freshness` `magnitude`értékek `distance`a `tag`, , és . Minden függvény egyedi paramétereket.<br /><br /> -   `freshness`akkor kell használni, ha egy elem új vagy régi ével szeretne kiemelni. Ez a függvény csak `datetime` mezőkkel (edm. DataTimeOffset). Figyelje `boostingDuration` meg, hogy az `freshness` attribútum csak a függvényben használatos.<br />-   `magnitude`akkor kell használni, ha a numerikus érték magas vagy alacsony értéke alapján szeretne kiemelni. A funkciót megszólító forgatókönyvek közé tartozik a haszonkulcs, a legmagasabb ár, a legalacsonyabb ár vagy a letöltések száma. Ez a funkció csak dupla és egész mezőkkel használható.<br />     A `magnitude` függvényesetében megfordíthatja a tartományt, magastól alacsonyig, ha az inverz mintát szeretné (például az alacsonyabb árú cikkeket a magasabb árú cikkeknél nagyobbra növelni). Adott egy sor árak $ 100 és $ `boostingRangeStart` 1, akkor `boostingRangeEnd` meg a 100 és 1, hogy növeljék az alacsonyabb árú elemeket.<br />-   `distance`akkor kell használni, ha közelség vagy földrajzi hely szerint szeretne kiemelni. Ez a függvény csak `Edm.GeographyPoint` mezőkkel használható.<br />-   `tag`akkor kell használni, ha a dokumentumok és a keresési lekérdezések közötti közös címkékkel szeretne kiemelni. Ez a funkció csak `Edm.String` `Collection(Edm.String)` mezőkkel és mezőkkel használható.<br /><br /> **A függvények használatára vonatkozó szabályok**<br /><br /> A függvénytípusnak`freshness`( , `magnitude`, ), `distance` `tag` kisbetűsnek kell lennie.<br /><br /> A függvények nem tartalmazhatnak null vagy üres értékeket. Pontosabban, ha a mezőnevet is feladják, be kell állítania valamit.<br /><br /> A függvények csak szűrhető mezőkre alkalmazhatók. A szűrhető mezőkről az [Index létrehozása &#40;az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) című témakörben olvashat bővebben.<br /><br /> A függvények csak az index mezőgyűjteményében definiált mezőkre alkalmazhatók.|  

 Az index definiálása után az indexet az indexséma feltöltésével hozhatja létre, amelyet dokumentumok követnek. Az [index létrehozása &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) és dokumentumok [hozzáadása, frissítése vagy törlése &#40;Azure Cognitive Search REST API-&#41;](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) című témakörben talál utasításokat ezekhez a műveletekhez. Az index létrehozása után rendelkeznie kell egy funkcionális pontozási profillal, amely együttműködik a keresési adatokkal.  

##  <a name="template"></a><a name="bkmk_template"></a>Sablon  
 Ez a szakasz a szintaxist és a pontozási profilok sablonját mutatja be. Az attribútumok leírását lásd a következő szakasz [Index attribútumainak hivatkozása](#bkmk_indexref) című részében.  

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

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a>Indexattribútumok hivatkozása  

> [!NOTE]  
>  A pontozási függvény csak szűrhető mezőkre alkalmazható.  

|Attribútum|Leírás|  
|---------------|-----------------|  
|`name`|Kötelező. Ez a pontozási profil neve. A mező elnevezési konvencióit követi. Betűvel kell kezdődnie, nem tartalmazhat pontokat, kettőspontokat vagy @ szimbólumokat, és nem kezdheti az "azureSearch" (kis- és nagybetűk megkülönböztetése) kifejezéssel.|  
|`text`|A súlyok tulajdonságot tartalmazza.|  
|`weights`|Választható. Olyan név-érték párokat tartalmaz, amelyek mindegyike mezőnevet és relatív vastagságot ad meg. A relatív súlynak pozitív egész számnak vagy lebegőpontos számnak kell lennie.<br /><br /> A súlyok jelzik az egyik kereshető mező fontosságát a másikhoz képest.|  
|`functions`|Választható. A pontozási függvény csak szűrhető mezőkre alkalmazható.|  
|`type`|A pontozási függvényekhez szükséges. A használandó függvény típusát jelzi. Az érvényes értékek közé tartozik a magnitúdó, a frissesség, a távolság és a címke. Minden pontozási profilba több függvényt is felvehet. A függvény nevének kisbetűsnek kell lennie.|  
|`boost`|A pontozási függvényekhez szükséges. A nyers pontszám szorzójaként használt pozitív szám. Nem lehet egyenlő 1-vel.|  
|`fieldname`|A pontozási függvényekhez szükséges. A pontozási függvény csak olyan mezőkre alkalmazható, amelyek az index mezőgyűjteményének részét képezik, és amelyek szűrhetők. Ezenkívül minden függvénytípus további korlátozásokat vezet be (a frissességet a dátumidő mezők, az egész vagy kettős mezők nagysága, valamint a helymezők távolsága használja). Függvénydefiníciónként csak egy mezőt adhat meg. Ha például kétszer szeretné használni a nagyságát ugyanabban a profilban, két definíciónagyságú, minden mezőhöz egyet kell megadnia.|  
|`interpolation`|A pontozási függvényekhez szükséges. Azt a lejtést határozza meg, amelynél a pontszám növelése a tartomány elejétől a tartomány végéig növekszik. Az érvényes értékek a következők: Lineáris (alapértelmezett), Állandó, Másodfokú és Logaritmikus. A részleteket az [Interpolációk beállítása.](#bkmk_interpolation)|  
|`magnitude`|A magnitúdó pontozási függvény a rangsorok módosítására szolgál a numerikus mező értéktartománya alapján. Néhány a leggyakoribb használati példa erre a következők:<br /><br /> -   **Csillag értékelések:** Módosítsa a pontozást a "Csillagminősítés" mező értéke alapján. Ha két elem releváns, a magasabb minősítésű elem jelenik meg először.<br />-   **Árrés:** Ha két dokumentum releváns, előfordulhat, hogy a kiskereskedő először magasabb árréssel rendelkező dokumentumokat szeretne kiemelni.<br />-   **Kattintások száma:** Azoknál az alkalmazásoknál, amelyek nyomon követik a termékekre vagy oldalakra irányuló kattintási műveleteket, nagyesíteni használhatja a legnagyobb forgalmat hozó elemek növelését.<br />-   **Letöltési számok:** A letöltéseket nyomon követő alkalmazások esetében a magnitúdó funkció lehetővé teszi a legtöbb letöltéssel rendelkező elemek kiemelését.|  
|`magnitude`&#124;`boostingRangeStart`|Annak a tartománynak a kezdőértékét adja meg, amelyen a magnitúdó pontozható. Az értéknek egész számnak vagy lebegőpontos számnak kell lennie. Az 1-től 4-ig történő csillagos értékelések esetében ez 1 lesz. Az 50% feletti árrés esetében ez 50 lenne.|  
|`magnitude`&#124;`boostingRangeEnd`|Annak a tartománynak a végértékét adja meg, amelyen a magnitúdó pontozható. Az értéknek egész számnak vagy lebegőpontos számnak kell lennie. Az 1-től 4-ig történő csillagos értékelések esetében ez 4 lesz.|  
|`magnitude`&#124;`constantBoostBeyondRange`|Az érvényes értékek igazak vagy hamisak (alapértelmezett). Ha a beállítás igaz, a teljes kiemelés továbbra is érvényes lesz azokra a dokumentumokra, amelyek nek a célmezőértéke magasabb, mint a tartomány felső vége. Ha hamis, akkor a függvény kiemelése nem lesz alkalmazva azokra a dokumentumokra, amelyek nek értéke a célmezőhöz a tartományon kívül esik.|  
|`freshness`|A frissesség pontozási függvény a mezők értékein `DateTimeOffset` alapuló cikkek rangsorolási pontszámainak módosítására szolgál. Egy újabb dátummal rendelkező elem például magasabbra sorolható, mint a régebbi elemek.<br /><br /> Az is lehetséges, hogy rangsorolja elemek, mint a naptári események a jövőbeli dátumokat, hogy a tételek közelebb a jelen lehet rangsorolni magasabb, mint a tételek tovább a jövőben.<br /><br /> Az aktuális szolgáltatáskiadásban a tartomány egyik vége az aktuális időponthoz lesz rögzítve. A másik végén egy időben a `boostingDuration`múltban alapuló . A jövőben számos alkalommal növelni szeretné a `boostingDuration`programot, használjon negatív .<br /><br /> Azt a sebességet, amellyel a kiemelés a maximális és a minimális tartományról változik, a pontozási profilra alkalmazott interpoláció határozza meg (lásd az alábbi ábrát). Az alkalmazott kiemelési tényező visszafordításához válasszon 1-nél kisebb kiemelési tényezőt.|  
|`freshness`&#124;`boostingDuration`|Beállítja azt a lejárati időszakot, amely után egy adott dokumentum kiemelése leáll. A szintaxisért és példákért lásd: [BoostingDuration az](#bkmk_boostdur) alábbi szakaszban.|  
|`distance`|A távolságpontozási funkció a dokumentumok pontszámának befolyásolására szolgál annak alapján, hogy milyen közel vagy messze vannak egy referencia földrajzi helyhez képest. A hivatkozási hely a lekérdezés részeként van `scoringParameterquery` megadva egy paraméterben (a karakterlánc-beállítás használatával) lon,lat argumentumként.|  
|`distance`&#124;`referencePointParameter`|Referenciahelyként használandó lekérdezésekben átadandó paraméter. `scoringParameter`egy lekérdezési paraméter. A lekérdezési paraméterek leírását az [Azure Cognitive Search REST API-&#41;&#40;keresési](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) dokumentumok ban találja.|  
|`distance`&#124;`boostingDistance`|A kiemelési tartomány végét tartalmazó referenciahelytől kilométerben megott távolságot jelző szám.|  
|`tag`|A címkepontozási funkció a dokumentumok és a keresési lekérdezések címkéin alapuló dokumentumok pontszámának befolyásolására szolgál. A rendszer kinagyul a keresési lekérdezéssel közös címkékkel rendelkező dokumentumok kiemelésével. A keresési lekérdezés címkéi minden keresési kérelemben pontozási paraméterként vannak megadva (a `scoringParameterquery` karakterlánc-beállítás használatával).|  
|`tag`&#124;`tagsParameter`|Egy adott kérelem címkéinek megadásához a lekérdezésekben átadandó paraméter. `scoringParameter`egy lekérdezési paraméter. A lekérdezési paraméterek leírását az [Azure Cognitive Search REST API-&#41;&#40;keresési](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) dokumentumok ban találja.|  
|`functionAggregation`|Választható. Csak akkor érvényes, ha a függvények meg vannak adva. Az érvényes értékek a következők: összeg (alapértelmezett), átlag, minimum, maximum és firstMatching. A keresési pontszám egyetlen érték, amely több változóból számítható ki, beleértve több függvényt is. Ez az attribútum azt jelzi, hogy az összes függvény kiemelések egyetlen összesített kiemelésbe kerülnek, amelyet aztán az alapdokumentum-pontszámra alkalmazanak. Az alappontszám a dokumentumból és a keresési lekérdezésből kiszámított [tf-idf](http://www.tfidf.com/) értéken alapul.|  
|`defaultScoringProfile`|Keresési kérelem végrehajtásakor, ha nincs megadva pontozási profil, akkor az alapértelmezett pontozási beállítást használja a rendszer (csak[tf-idf).](http://www.tfidf.com/)<br /><br /> Itt beállítható egy alapértelmezett pontozási profilnév, amelynek hatására az Azure Cognitive Search akkor használja ezt a profilt, ha a keresési kérelemben nincs megadva konkrét profil.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a>Interpolációk beállítása  
 Az interpolációk lehetővé teszik a pontozáshoz használt lejtés alakjának beállítását. Mivel a pontozás magastól az alacsonyig, a lejtés mindig csökken, de az interpoláció határozza meg a lefelé irányuló lejtés görbéjét. A következő interpolációk használhatók:  

|||  
|-|-|  
|`linear`|A maximális és a min tartományon belüli cikkek esetében a cikkre alkalmazott kiemelés folyamatosan csökkenő mennyiségben történik. Lineáris az alapértelmezett interpoláció a pontozási profilhoz.|  
|`constant`|A kezdő és záró tartományon belüli cikkek esetében a rangsor eredményére állandó kiemelés lép érvénybe.|  
|`quadratic`|Összehasonlítva a lineáris interpoláció, amely folyamatosan csökkenő lendületet, Quadratic kezdetben csökken kisebb ütemben, majd ahogy közeledik a végtartomány, csökken egy sokkal nagyobb intervallumban. Ez az interpolációs beállítás nem engedélyezett a címkepontozási függvényekben.|  
|`logarithmic`|A folyamatosan csökkenő növekedésű lineáris interpolációhoz képest a logaritmikus kezdetben nagyobb ütemben csökken, majd a végtartomány felé közeledve sokkal kisebb időközönként csökken. Ez az interpolációs beállítás nem engedélyezett a címkepontozási függvényekben.|  

 ![Állandó, lineáris, másodfokú, log10 vonalak a grafikonon](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a>BoostingDuration beállítása  
 `boostingDuration`a `freshness` függvény attribútuma. Segítségével beállíthat egy lejárati időszakot, amely után egy adott dokumentum kiemelése leáll. Ha például egy termékvonalat vagy márkát 10 napos promóciós időszakra szeretne kiemelni, akkor a 10 napos időszakot "P10D"-ként kell megadnia a dokumentumokhoz.  

 `boostingDuration`xsd "dayTimeDuration" értékként kell formázni (az ISO 8601 időtartam érték korlátozott részhalmaza). Ennek mintája a következő: "P[nD][T[nH][nM][nS]]".  

 Az alábbi táblázat több példát is tartalmaz.  

|Időtartam|boostingDuration|  
|--------------|----------------------|  
|1 nap|"P1D"|  
|2 nap és 12 óra|"P2DT12H"|  
|15 perc|"PT15M"|  
|30 nap, 5 óra, 10 perc és 6,334 másodperc|"P30DT5H10M6.334s"|  

 További példák: [XML-séma: Adattípusok (W3.org webhely)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)című témakörben talál.  

## <a name="see-also"></a>Lásd még  
 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Index létrehozása &#40;Azure Cognitive Search REST API-&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
