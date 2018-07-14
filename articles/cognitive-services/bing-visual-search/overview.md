---
title: A Bing Visual Search API – áttekintés |} A Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan adatai vagy információival, például a hasonló képek vagy vásárlási forrás lemezkép beolvasása.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa563d89b1834f5be952f13c31a2451d809709b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006529"
---
# <a name="what-is-bing-visual-search-api"></a>Mi a Bing Visual Search API?

A Bing Visual Search API-Bing.com/images látható részletei hasonló élményt nyújt. Vizuális keresés, a kép feltöltése, és vissza a képen például a vizuálisan hasonló képek, vásárlási forrás, amelyek közé tartozik a lemezkép és egyéb kapcsolatos elemzések lekérése. Kép feltöltése helyett is megadhatja egy, a képek keresési eredmények képet kap insights jogkivonatot (lásd: [Bing API-lemezképek](../bing-image-search/overview.md)).

Vizuális keresés hírességek, emlékművek és arcrész, műalkotások, otthoni berendezések, módon, termékek, karakterfelismerés (OCR) és több azonosíthatja.

Az alábbiakban a vizuális keresés segítségével megkeresheti az elemzéseket.

- Vizuálisan hasonló képek&mdash;a bemeneti kép vizuálisan hasonló képek egy listája
- Vizuálisan hasonló termékek&mdash;tartalmazó termékhez, melyek a bemeneti képen látható termékre vizuálisan hasonló képek egy listája
- Vásárlási forrás&mdash;ahol vásárolhat a bemeneti képen látható a cikk helyek listája
- Kapcsolódó keresések&mdash;mások vagy, amelyek kapcsolódó keresések listáját alapulnak a kép tartalma
- A lemezképet tartalmazó weblapokra&mdash;listáját, amelyek tartalmazzák a bemeneti kép
- Receptek&mdash;listáját, amelyek közé tartozik, hogy a bemeneti képen látható az étel receptek

Ezek az információkhoz vizuális keresés is kezébe a különböző használati (címkék) származik a bemeneti kép adja vissza. Ezek a címkék lehetővé teszik a felhasználóknak, hogy a lemezképben található fogalmak. Például ha a bemeneti kép híres sportoló, a címkék egyikét lehet a athlete nevét, egy másik címke lehet vagy sporttal kapcsolatosak. Vagy, ha a bemeneti kép egy apple torta, a címkék Apple torta, tortadiagram, édességeket választottam, így a felhasználók tallózhatnak a kapcsolódó fogalmak.

A vizuális keresési eredmények határoló hasznos helyhez az ábrán régiók mezők is. Például ha a lemezképet tartalmaz számos hírességek, az eredmények tartalmazhat határoló mezőkbe az ábrán a felismert hírességek mindegyikéhez. Vagy ha a Bing egy termék vagy a kép ruházati felismeri, az eredmény tartalmazhatnak a felismert termékhez vagy ruházati elem határolókeret.

> [!IMPORTANT]
> Ha használja a/képek/részletek végpontot, hogy [kép elemzések lekérése](../bing-image-search/image-insights.md), frissítenie kell a kódot használja helyette vizuális keresés, mivel átfogóbb elemzéseket biztosít.


## <a name="the-request"></a>A kérelem

Az alábbiakban a kép elemzésének lehetőségeit. 

- Küldjön egy insights jogkivonatot, amely az egyik előző hívás képet kap a [Bing API-lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) végpontok
- Kép URL-Címének küldése
- Töltsön fel egy képet (bináris)


Ha Visual Search küld egy kép token vagy URL-cím, az alábbiakban látható a JSON-objektum, amely szerepelnie kell a bejegyzés törzse. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

A `imageInfo` objektumot kell tartalmaznia, vagy a `url` vagy `imageInsightsToken` mező, de nem mindkettőt. Állítsa be a `url` az interneten elérhető kép URL-mezőt. A maximális támogatott kép mérete 1 MB.

A `imageInsightsToken` egy insights tokent kell beállítani. Insights jogkivonatot kapjon a Bing Image API hívja. A válasz a listáját tartalmazza `Image` objektumokat. Minden egyes `Image` az objektum tartalmaz egy `imageInsightsToken` mező, amely tartalmazza a tokent.

A `cropArea` mező kitöltése nem kötelező. A vágási területen megadja a felső és alsó, bal felső sarokban a lényeges terület jobb felső sarkában. Adja meg az értékeket a 0,0 és 1,0 tartományt. Az értékek a következők a teljes szélességének és magasságának százalékában. Például a fenti példában jelöli meg a jobb oldalon fele a lemezképet, a tartományban. Adja meg, ha szeretné korlátozni a régió a lényeges insights vonatkozó kérelem.

A `filters` objektum tartalmazza a hely szűrőt (lásd a `site` mező), hogy a hasonló képek és hasonló termékek eredmények korlátozása egy adott tartományhoz használhatja. Például, ha a kép egy Surface Book, beállíthat `site` www.microsoft.com. 

Ha szeretne kaphat elemzési információkat egy lemezképet egy helyi példányát, a Rendszerkép feltöltése a bináris adat.

Többek között ezek a beállítások a bejegyzés törzse kapcsolatos részletekért lásd: [űrlap tartalomtípus](#content-form-types).


### <a name="endpoint"></a>Végpont

A Visual Search-végpont: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Csak HTTP POST-kérések kell küldeni a kérelmeket. 


### <a name="query-parameters"></a>Lekérdezési paraméterek

Az alábbiakban a lekérdezési paraméterek a kérésnek meg kell határoznia. Tartalmaznia kell legalább a `mkt` lekérdezési paraméter.

|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|<a name="cc" />cc|2 karakterből álló országkódot az ország, ahol az eredményeket származnak.<br /><br /> Ha a paraméter értéke, meg kell adni a [Accept-nyelv](#acceptlanguage) fejléc. Bing – megkeresi a nyelvek listáját, és egyesíti az országkódot, a piac származó eredmények határozza meg kell adnia a nyelvi első támogatott nyelvet használja. Ha a nyelvek listája nem tartalmazza a támogatott nyelven, a Bing megkeresi a legközelebbi nyelvet és a piacon, amely támogatja a kérelem. Vagy lehet, hogy használjon egy összesített vagy alapértelmezett piacot az eredményeket a megadottól helyett.<br /><br /> A lekérdezési paramétert kell használnia, és a `Accept-Language` lekérdezési paraméter csak akkor, ha több nyelvet ad meg; ellenkező esetben használjon a `mkt` és `setLang` lekérdezési paramétereket.<br /><br /> Ez a paraméter és a [mkt](#mkt) lekérdezési paraméter, egymást kölcsönösen kizáró&mdash;ne adja meg mindkettőt.|Sztring|Nem|  
|<a name="mkt" />mkt|A piacon, honnan származnak az eredményeket. <br /><br /> **Megjegyzés:** hosszúan mindig adja meg a piacon, ha ismert. Adja meg a piacon segít a Bing irányítsa át a kérést, és a egy megfelelő és az optimális választ adja vissza.<br /><br /> Ez a paraméter és a [cc](#cc) lekérdezési paraméter, egymást kölcsönösen kizáró&mdash;ne adja meg mindkettőt.|Sztring|Igen|  
|<a name="safesearch" />biztonságos keresés|Felnőtteknek szóló tartalmak szűrésére használt szűrő. A kis-és szűrő lehetséges értékek a következők.<br /><ul><li>Ki&mdash;weblapjait a felnőtt szöveget és képeket ad vissza.<br /><br/></li><li>Mérsékelt&mdash;weblapjait a felnőtt szöveget, de nem felnőtt képeket ad vissza.<br /><br/></li><li>A szigorú&mdash;felnőtt szöveget és képeket a weblapok nem adott vissza.</li></ul><br /> Alapértelmezett értéke közepes.<br /><br /> **Megjegyzés:** , ha a kérelem egy piaci származik, a Bing felnőtt szabályzat megköveteli, hogy `safeSearch` Strict beállítása, a Bing figyelmen kívül hagyja a `safeSearch` értékét, és szigorú használja.<br/><br/>**Megjegyzés:** használatakor a `site:` operátor, annak az esélyét, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy mi van a `safeSearch` lekérdezési paraméter értéke. Használat `site:` csak akkor, ha tisztában a webhely tartalmának, és a forgatókönyv támogatja a lehetőségét, felnőtt tartalom. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület karakterláncokat használni kívánt nyelvet. Válassza ki a nyelvet, az ISO 639-1-2 levelek nyelvi kód használatával. Például angol nyelvi kódját áll EN. Az alapértelmezett érték EN (angol nyelven).<br /><br /> Bár nem kötelező, mindig adja meg a nyelvet. Általában beállítása `setLang` által megadott nyelvre `mkt` , kivéve, ha a felhasználó szeretne-e a felhasználói felület karakterláncok egy másik nyelven jelenik meg.<br /><br /> Ez a paraméter és a [Accept-nyelv](#acceptlanguage) fejléc, egymást kölcsönösen kizáró&mdash;ne adja meg mindkettőt.<br /><br /> A felhasználói felület karakterlánc egy karakterláncérték, amely egy címkét egy felhasználói felületen. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncokat. A válaszobjektumok Bing.com tulajdonságait mutató hivatkozásokat is, a a megadott nyelvre a alkalmazni.|Sztring|Nem| 

### <a name="headers"></a>Fejlécek

Az alábbiakban a fejlécek, amelyek a kérésnek meg kell határoznia. A Content-Type és Ocp-Apim-Subscription-Key fejléc csak a szükséges fejléceket, de a felhasználói ügynök, X-MSEdge-ClientID, X-MSEdge-Ügyfélip és a hely keresése X is meg kell tartalmaznia.


|Fejléc|Leírás|  
|------------|-----------------|  
|<a name="acceptlanguage" />Fogadja el nyelv|Nem kötelező kérelem fejléce.<br /><br /> Nyelvek használandó felhasználói felület karakterláncok vesszővel tagolt listája. A lista egy csökkenő sorrendben. További információk, beleértve a várt formátum: [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ezt a fejlécet és a [setLang](#setlang) lekérdezési paraméter, egymást kölcsönösen kizáró&mdash;ne adja meg mindkettőt.<br /><br /> Ha beállította ezt a fejlécet, meg kell adni a [cc](#cc) lekérdezési paraméter. Annak megállapításához, a piac eredményeket ad vissza, a Bing, megkeresi a listából, és egyesíti az első támogatott nyelvet használja az `cc` paraméter értéke. Ha a lista nem tartalmazza a támogatott nyelven, a Bing megkeresi a legközelebbi nyelvet és a piacon, amely támogatja a kérés, vagy használ egy összesített vagy alapértelmezett piaci az eredményeket a. Annak megállapításához, a piac, a Bing által használt, tekintse meg a piacra jutási BingAPIs fejléc.<br /><br /> Használja ezt a fejlécet és a `cc` lekérdezési paraméter csak akkor, ha több nyelvet ad meg. Ellenkező esetben a [mkt](#mkt) és [setLang](#setlang) lekérdezési paramétereket.<br /><br /> A felhasználói felület karakterlánc egy karakterláncérték, amely egy címkét egy felhasználói felületen. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncokat. A válaszobjektumok Bing.com tulajdonságait mutató hivatkozásokat a megadott nyelvre a alkalmazni.|  
|<a name="contenttype" />A Content-Type|Szükséges a kérelem fejlécében.<br /><br />Multipart/form-data értékre kell állítani, és a egy határ paramétert (például a multipart/form-data; a határ =\<határ karakterlánc\>). További információkért lásd: [űrlap tartalomtípus](#content-form-types).
|<a name="market" />BingAPIs piaci|Válaszfejléc.<br /><br /> A piac, a kérelem által használt. Az űrlap \<languageCode\>-\<countryCode\>. Például: hu-hu.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó a naplóbejegyzés azonosítója. Ha hiba történik, rögzítése forrástároló. Ha nem határozza meg, és a probléma megoldásához, tartalmazzák ezt az Azonosítót és az egyéb adatokat, hogy a támogatási csoporthoz.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Szükséges a kérelem fejlécében.<br /><br /> Az előfizetési kulcsot, ezt a szolgáltatást a regisztráció során kapott [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Direktiva pragma|Nem kötelező kérelem fejléce<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalmat vissza, ha elérhető. Gyorsítótárazott tartalom visszaadó megakadályozni a Bing, állítsa be a Pragma fejléc no-cache (például Pragma: no-cache).
|<a name="useragent" />Felhasználói ügynök|Nem kötelező kérelem fejléce.<br /><br /> A felhasználói ügynök, a kérelmet indító. A Bing a felhasználói ügynök használatával mobil felhasználók számára egy optimalizált felhasználói élményt biztosíthat. Bár nem kötelező, csak Ön javasoljuk, hogy mindig adja meg ezt a fejlécet.<br /><br /> A felhasználói ügynök ugyanazt a karakterláncot, amely bármely általánosan használt böngésző elküldi kell lennie. Felhasználói ügynökök kapcsolatos információkért lásd: [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> A következő példák felhasználói ügynök karakterláncokat.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilis. MSIE 10.0; Windows Phone 8.0-s; A Trident vagy 6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U. Android 2.3.5; en-us; SCH-I500 Build/MÉZESKALÁCS) AppleWebKit/533.1 (KHTML; például gekkó) vagy 4.0-s verziójú Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone operációs rendszer 6_1, például a Mac OS X) AppleWebKit/536.26 (KHTML; például gekkó) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3. WOW64; A Trident/7.0; Touch; RV:11.0), például gekkó<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 például a Mac OS X) AppleWebKit/537.51.1 (például gekkó KHTML) verziója/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérések és válaszok fejléc.<br /><br /> A Bing a fejléc használatával biztosítható a felhasználók konzisztens viselkedés között Bing API-hívások. Bing gyakran légi járművek új szolgáltatásait és fejlesztéseit, és hozzárendeléséhez a forgalmat a különböző repülőjáratok kulcsként használ az ügyfél-Azonosítót. Az azonos ügyfél-Azonosítót egy felhasználóhoz több kérés között nem használja, ha majd Bing lehet, hogy a felhasználó hozzárendelése több ütköző repülőjáratok. Több ütköző repülőjáratok folyamatban van rendelve egy konzisztens felhasználói élményt vezethet. Például ha a második kérés, mint az első különböző repülési hozzárendeléssel rendelkezik, a tapasztalatok lehet váratlan. A Bing segítségével is az ügyfél-azonosító webes találatokat, hogy az ügyfél a megfelelő kódolási sebesség kiválasztását azonosítóhoz tartozó keresési előzmények, a felhasználó egy gazdagabb élményt nyújtva.<br /><br /> A Bing is használja ezt a fejlécet a tevékenység egy ügyfél-azonosítót. által generált elemzésével eredmény módosítják a rangsort javítása érdekében A relevancia alapján végzett fejlesztések segítség az eredmények a Bing API-k és üzembe viszont lehetővé teszi, hogy nagyobb átkattintásos arányt API fogyasztók számára jobb minőségű.<br /><br /> **Fontos:** bár nem kötelező, csak akkor érdemes ezt a kötelező fejlécet. (1.) a felhasználó a API a Bing API-k fogadása, egységes felhasználói élmény, és (2) a nagyobb átkattintásos sebesség jobb minőségű eredmények via megőrzése az ügyfél-azonosító több kérés között ugyanazon felhasználó és eszköz kombinációja lehetővé teszi.<br /><br /> Az alábbiakban az alapszintű használati szabályok, amelyek a alkalmazni ezt a fejlécet.<br /><ul><li>Minden egyes felhasználó esetében használja az alkalmazást az eszközön rendelkeznie kell egy egyedi, a Bing létrehozott ügyfél-azonosítót.<br /><br/>Ha a kérés nem adja meg ezt a fejlécet, a Bing létrehoz egy Azonosítót, és visszaadja az X-MSEdge-ClientID fejlécet. Az egyetlen alkalom, hogy a kérelem kell tartalmazza ezt a fejlécet a felhasználó használja az alkalmazást az eszközön lévő első alkalommal.<br /><br/></li><li>**Figyelem:** gondoskodnia kell arról, hogy az ügyfél-azonosító nem összekapcsolhatónak a hitelesített felhasználói fiók adatokat.</li><li>Az ügyfél-azonosító használata minden egyes Bing API-kérelem, amely az alkalmazás lehetővé teszi a felhasználó az eszközön.<br /><br/></li><li>Megőrzése az ügyfél-azonosítót. Egy böngészőalkalmazásban Azonosítóját megőrizni, használjon állandó HTTP cookie-k, használja az összes munkamenetek között. Ne használjon egy munkamenetcookie-t. Más alkalmazások, például a mobilalkalmazások esetében használja az eszköz tartós tároláshoz megőrizni az azonosítója.<br /><br/>A következő alkalommal, amikor a felhasználó használja az alkalmazást, hogy az eszközről, amely megőrizte ügyfél-azonosító beszerzése.</li></ul><br /> **Megjegyzés:** Bing-válaszok is, vagy nem feltétlenül tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítheti az ügyfél-Azonosítót, és használhatja azt a felhasználót az eszközön lévő összes további Bing kérelmet.<br /><br /> **Megjegyzés:** Ha adja meg az X-MSEdge-ClientID, nem tartalmazhat-e be a cookie-kat a kérésben.|  
|<a name="clientip" />X-MSEdge-Ügyfélip|Nem kötelező kérelem fejléce.<br /><br /> Az ügyféleszköz IPv4 vagy IPv6-cím. Az IP-cím a felhasználó földrajzi helye felderítésére szolgál. A Bing biztonságos keresési viselkedés meghatározására használja a helyre vonatkozó adatokat.<br /><br /> **Megjegyzés:** bár nem kötelező, akkor arra biztatjuk mindig adja meg ezt a fejlécet és az X-Search-Location fejlécet.<br /><br /> A cím nem rejtse fel (például úgy, hogy az utolsó oktettet módosítása 0). A cím eredmények a helyen nincs folyamatban bárhol az eszköz tényleges hely közelében obfuscating, emiatt a Bing szolgálja ki a hibás eredményeket.|  
|<a name="location" />A hely keresése X|Nem kötelező kérelem fejléce.<br /><br /> Kulcs/érték párok, amelyek bemutatják, az ügyfél földrajzi helye pontosvesszővel tagolt listája. A Bing a helyre vonatkozó adatokat használ, annak meghatározásához, hogy biztonságos keresés és a megfelelő helyi tartalom. Adja meg a kulcs/érték pár, \<kulcs\>:\<érték\>. Az alábbiakban a kulcsokat, amelyekkel a felhasználó helyét adja meg.<br /><br /><ul><li>szél&mdash;szükséges. Az ügyfél helye fokban szélességét. A földrajzi szélesség kell lennie, nagyobb vagy egyenlő-90.0 és kisebb vagy egyenlő, mint +90.0. Negatív értékek azt jelzik, déli földrajzi szélesség és pozitív értékek azt jelzik, Észak-szélességet.<br /><br /></li><li>hosszú&mdash;szükséges. Az ügyfél helye fokban hosszúságát. Lehet, hogy a földrajzi hosszúság nagyobb vagy egyenlő-180.0 és kisebb vagy egyenlő, mint +180.0. Negatív értékek azt jelzik, nyugati hosszúságot, és pozitív értékek azt jelzik, kelet-hosszúságot.<br /><br /></li><li>re&mdash;szükséges. A radius, a mérőszámok, amely megadja a koordináták a vízszintes pontosságát. Írjon az eszköz helye szolgáltatás által visszaadott értéket. Tipikus értékek 22m, a GPS és Wi-Fi, esetében a cella tower keresztárfolyam 380m és az IP-címkeresés 18,000m lehet.<br /><br /></li><li>TS&mdash;nem kötelező. Az UTC UNIX timestamp, amikor az ügyfél volt-e a helyen. (A UNIX-időbélyegző az 1970. január 1. óta eltelt másodpercek számát.)<br /><br /></li><li>a fő&mdash;nem kötelező. Az ügyfél relatív fejléc vagy utazási irányát. Adja meg a 0 és 360, a számbavételi képest igaz északi elforgatása jobbra fok utazási irányát. Adja meg a kulcs csak akkor, ha a `sp` kulcs nem nulla.<br /><br /></li><li>SP&mdash;nem kötelező. A vízszintes sebesség (sebesség), a másodpercenkénti, az ügyféleszközön utazik méterben.<br /><br /></li><li>ALT&mdash;nem kötelező. A magasság, az ügyféleszközön, a mérőszámok.<br /><br /></li><li>vannak&mdash;nem kötelező. A radius méterben, amely meghatározza a koordináták a függőleges pontosságát. Adja meg ezt a kulcsot csak akkor, ha adja meg a `alt` kulcsot.<br /><br /></li></ul> **Megjegyzés:** a kulcsokat számos választható, bár a további adatokat ad meg, annál pontosabb helyet eredménye.<br /><br /> **Megjegyzés:** bár nem kötelező, akkor arra biztatjuk mindig adja meg a felhasználó földrajzi helyét. A hely megadása akkor kifejezetten fontos, ha az ügyfél IP-cím nem tükrözik a felhasználó fizikai helye (például, ha az ügyfél használja a VPN). Az optimális eredmények elérése érdekében akkor tartalmaznia kell ezt a fejlécet és az X-MSEdge-Ügyfélip fejléc, de legalább ezt a fejlécet kell tartalmaznia.|

> [!NOTE] 
> Ne feledje, hogy a vonatkozó törvényeket, beleértve a következő fejlécek az vonatkozó megfelelőségi a használati feltételek szükséges. Ha például bizonyos országokban, például Európában, nincsenek felhasználói beleegyezés előtt bizonyos követési eszközök elhelyezése a felhasználói eszközök beszerzése.


<a name="content-form-types" />

### <a name="content-form-types"></a>Tartalom űrlap típusok

Minden kérelmet tartalmaznia kell a Content-Type fejléc. A fejléc értékre kell állítani: multipart/form-data; határ =\<határ karakterlánc\>, ahol \<határ karakterlánc\> egy egyedi, átlátszatlan karakterlánc, amely azonosítja az űrlapadatok határait. Például a határ = boundary_1234-abcd.


Ha Visual Search küld egy kép token vagy URL-cím, az alábbiakban látható az űrlapadatok szerepelnie kell a bejegyzés törzse. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejléc, az `name` paramétert állítsa "knowledgeRequest." További információ a `imageInfo` objektumazonosító, lásd: [a kérelem](#the-request).


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Ha a helyi rendszerképet tölt fel, az alábbiakban látható az űrlapadatok szerepelnie kell a bejegyzés törzse. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter "image" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A Content-Type fejléc beállítható bármely általánosan használt kép mime-típus. A képernyő tartalmát a rendszerkép a bináris. A maximális képméret tölthet fel érték 1 MB. A lehető legnagyobb szélességének vagy magasságának 1500 képpont kell lennie, vagy kisebb.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Az alábbiakban látható a régió, a feltöltött kép lényeges megadása.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Kérelem (példa)

Az alábbiakban látható egy teljes kép insights-kérés, amelyet átad egy kép token és a lényeges terület. Az insights jogkivonat /images/search egy előző hívásából származó kap.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>A válasz

Ha vannak a kép insights, a válasz tartalmaz egy vagy több `tags` , amelyek tartalmazzák az elemzéseket. A `image` mező tartalmazza a bemeneti kép insights token.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

A `tags` mező tartalmaz egy megjelenítési nevet és az adott műveletek (insights). A címkék egyik tartalmaz egy `displayName` mező, amely egy üres karakterláncra van beállítva. Ez a címke tartalmazza, amelyek a lemezképet, a vizuálisan hasonló képek és a vásárlási forrás a lemezképben található elemek közé tartozik például az alapértelmezett insights. Mivel a teljes kép a lényeges, az alapértelmezett insights címke mezőbe a régiókat a lényeges határoló nem tartalmazza.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Az alapértelmezett insights listáját lásd: [insights alapértelmezett](./default-insights-tag.md).



A fennmaradó címkék más, amely lehet a felhasználó számára érdekes elemzéseket tartalmaznak. Például ha a kép szöveget tartalmaz, a címkék egyikét tartalmazhat egy TextResults insight, amely tartalmazza a felismert szöveget. Vagy, ha a Bing (személy, hely vagy dolog) entitás felismeri a kép, címkék egyikével, előfordulhat, hogy azonosítja az entitást. Vizuális keresés is kezébe a különböző használati (címkék) származik a bemeneti kép adja vissza. Ezek a címkék lehetővé teszik a felhasználóknak, hogy a lemezképben található fogalmak. Például ha a bemeneti kép híres sportoló, a címkék egyikét lehet például sportesemények, amely által az olimpián képére mutató hivatkozásokat tartalmaz.

Minden címke tartalmazza, amelyek segítségével a elemzés kategorizálásához, amely azonosítja a régió, amely a elemzés vonatkozik, az elemzések magukat, és a kép miniatűrjét határolókeret megjelenített neve. Például ha a kép egy sport jersey elhasználódó személy, a címkék egyikét tartalmazhat egy határolókeret, amely a jersey bounds és VisualSearch és ProductVisualSearch insights tartalmazza. És a egy másik címkét tartalmazhat egy helyileg kapcsolódó képek beolvasni egy /images/search API-kérelem URL-CÍMÉT vagy egy Bing.com Keresés URL-címet, amely a felhasználót, hogy a Bing.com eredményeit tartalmazó ImageResults elemzést.

Az alapértelmezett insights címke kívül az összes címke például határoló hasznos helyhez az ábrán területek azonosítására szolgáló mezők. Például ha a kép elfogadott többen is tartalmaz, a címkék lehetnek, mezők határoló minden annak a személynek, vagy ha a kép felismert ruházati elemet tartalmaz, a címkék lehetnek, egyes felismert ruházati elemekhez tartozó mezőkben határoló. A határoló mezők használatával hoz létre a hotspotok a kép fölött, amely rákattintáskor, adja meg a tartalmat az adott régióban, a kép adatait. A hotspotok határoló mezők, amelyek azonosítják a teljes kép a kép nem tartalmaznia.

### <a name="text-recognition"></a>Szövegek felismerése

A rendszerkép tartalmazza a szöveg, amely a szolgáltatás észleli, ha a címkék egyikét egy TextResults insight (művelet) fogja tartalmazni. Az insight `displayName` tartalmazza a felismert szöveget. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Mivel a címke `displayName` mező ##TextRecognition tartalmazza, ne használja a UX egy kategória címként Kezdődik, hogy minden megjelenítés goes nevet, amely ##. Ehelyett használja a művelet megjelenített neve.


Szövegek felismerése is képes felismerni a kapcsolattartási adatokat a névjegyek, például a telefonszámok és e-mail címek. A határolókeret azonosítja a helyét, a kapcsolattartási adatokat a kártyán. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Ha a rendszerkép tartalmazza a felismert entitás, például egy személy, hely vagy egy dolog, a címkék egyikét tartalmazhat egy entitás elemzést. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>További lépések

Gyorsan megkezdheti az első kérelmét, tekintse meg a rövid útmutató: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Próbálja ki az API-t. Lépjen a [Visual Search API-tesztelési konzol](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Ismerje meg az a [Visual Search API-referencia](https://aka.ms/bingvisualsearchreferencedoc). A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne. 

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./use-and-display-requirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.


