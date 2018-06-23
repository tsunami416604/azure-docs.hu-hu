---
title: Bing Visual Search API áttekintése |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan bevásárlási források vagy hasonló képek például lemezkép észrevételeket vagy adatokat lekérni.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 95f10d8ea7ebe1d40d45231a8ea40df81543fe8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349123"
---
# <a name="what-is-bing-visual-search-api"></a>Mi az a Bing Visual keresési API-t?

Bing Visual Search API Bing.com/images látható részletei hasonló élményt nyújt. A vizuális keresési kép feltöltése szabadon segítségnyújtáshoz például vizuálisan hasonló lemezképek, a vásárlásra szolgáló adatforrások, amelyek közé tartozik a lemezképet, és több képet kaphat. Helyett egy kép feltöltésével adja meg egy insights jogkivonatot kapott, a lemezképek keresési eredmények lemezképpel (lásd: [Bing képek API](../bing-image-search/overview.md)).

Visual keresési azonosíthatja az celebrities, műemlékek és jellegzetes hely, alkotást, otthoni berendezések, módon, termékek, karakter felismerési (OCR), és több.

A következők feltárása, hogy a Visual keresési lehetővé teszi, hogy felderítése.

- Vizuálisan hasonló képek&mdash;lemezképbe, amelyek a bemeneti kép vizuálisan hasonló listája
- Vizuálisan hasonló termékek&mdash;lemezképeket, amelyek tartalmazzák a termék a bemeneti ábrának vizuálisan hasonló termékek listáját
- Adatforrások vásárlás&mdash;helyen, ahol a bemeneti kép megjelenített elem vásárolhat listája
- A keresések kapcsolódó&mdash;kapcsolódó olyan vagy mások által végzett keresések listáját a lemezkép tartalmát alapuló
- A lemezképet tartalmazó weblapokra&mdash;listáját, amelyek közé tartozik a bemeneti kép
- Receptet&mdash;, hogy a bemeneti ábrának edényt receptet tartalmazó weblapok listája

Ezek insights mellett Visual keresési is adja vissza, amely különböző feltételeket (címke) a bemeneti kép származik. Ezekkel a címkékkel engedélyezése a felhasználók számára a lemezképben található fogalmak vizsgálatát. Például ha a bemeneti kép famous sportoló, egyik címkék lehet a sportoló nevét, egy másik címke lehet sport. Vagy, ha a bemeneti kép egy apple torta, a címkék Apple torta, piték, desszertek, így a felhasználók ismerje meg a kapcsolódó fogalmak.

A Visual találatok között is szerepelnek, a lemezkép iránti érdeklődését régiókhoz be határolókeret. Ha a kép több celebrities tartalmaz, az eredmények lehet, hogy például a mezőkbe az egyes a kép felismert celebrities határolókeret. Vagy, a Bing felismeri a termék vagy a kép ruházati, ha az eredmény az ismert vagy ruházati elem határolókeret előfordulhat, hogy tartalmazza.

> [!IMPORTANT]
> Használja-e a/képek/részletek végpontot [kép nyerhet](../bing-image-search/image-insights.md), frissítse a kódot használja helyette Visual keresési, mivel átfogóbb insights biztosít.


## <a name="the-request"></a>A kérelem

Az alábbiakban nyerek betekintést az adatokba kép beállításait. 

- Küldjön egy insights jogkivonatot kapott egy korábbi hívás egy képet a [Bing képek API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) végpontok
- A kép URL-küldése
- (Bináris) lemezkép feltöltése


Ha Visual keresési küldi el egy kép token vagy URL-cím, az alábbiakban látható a JSON-objektumból, amely meg kell adni a FELADÁS egy vagy több törzsében. 

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

A `imageInfo` objektumot kell tartalmaznia, vagy a `url` és `imageInsightsToken` mező, de soha sem egyszerre mindkettőre. Állítsa be a `url` mező Internet elérhető kép URL-címét. A maximális támogatott lemezkép mérete 1 MB.

A `imageInsightsToken` egy insights jogkivonatot kell beállítani. Ahhoz, hogy egy insights jogkivonatot, a Bing kép API hívása. A válasz listáját tartalmazza `Image` objektumok. Minden egyes `Image` objektum tartalmaz egy `imageInsightsToken` mező, amely tartalmazza a jogkivonatot.

A `cropArea` mező kitöltése nem kötelező. A vágási területen felső és alsó, bal sarok jobb felső sarkában a tartományban, adja meg. Adja meg az értékeket a 0,0 és 1,0 közé. A teljes szélesség vagy magasság százalékos értékek. Például a fenti példa állapotúként jelöli meg a jobb oldali fele a lemezképet a tartományban. Ha szeretné korlátozni a insights-kérést a tartományban, adja hozzá.

A `filters` objektum tartalmazza a hely szűrőt (lásd a `site` mező) használható a hasonló képek és hasonló termékek eredmények korlátozása egy adott tartományban. Például, ha a lemezkép egy Surface Book, beállíthatja `site` www.microsoft.com. 

Ha le szeretné kérdezni a lemezkép másolata észrevételeket, feltölti a lemezképet a bináris adat.

Ezek a beállítások a FELADÁS egy vagy több törzsében kapcsolatos részletekért lásd: [űrlap tartalomtípus](#content-form-types).


### <a name="endpoint"></a>Végpont

A vizuális keresési végpontja: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Kérések, csak a HTTP POST-kérésnél kell elküldeni. 


### <a name="query-parameters"></a>Lekérdezési paraméterek

Az alábbiakban adja meg a kérelem lekérdezési paraméterekhez. Tartalmaznia kell legalább a `mkt` lekérdezési paraméter.

|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|<a name="cc" />másolatot kap|2 álló országkódot az ország, ahol az eredmények származik.<br /><br /> Ha a paraméter értéke, meg kell adni a [elfogadás-nyelv](#acceptlanguage) fejléc. Bing talál olyan nyelvet a listából, és határozza meg a piacon találatot, a megadott országhívószám-t ötvözi a nyelvi első támogatott nyelvet használja. Ha a nyelvek listája nem tartalmazza a támogatott nyelvi, a Bing megkeresi a legközelebbi nyelvet és a piaci, amely támogatja a kérelem. Vagy előfordulhat, hogy használjon egy összesített vagy alapértelmezett piaci megadott helyett a eredmény elérése érdekében.<br /><br /> A következő lekérdezésparaméter kell használnia, és a `Accept-Language` lekérdezésparaméter csak akkor, ha több nyelvet ad meg; ellenkező esetben kell használnia a `mkt` és `setLang` lekérdezési paramétert.<br /><br /> Ez a paraméter és a [mkt](#mkt) lekérdezésparaméter, egymást kölcsönösen kizáró&mdash;nem adható meg egyszerre.|Sztring|Nem|  
|<a name="mkt" />mkt|A piacon, honnan származnak az eredményeket. <br /><br /> **Megjegyzés:** áll javasoljuk, hogy mindig adja meg a piacon, ha ismert. A piacon segít a Bing továbbítja a kérelmet, és egy megfelelő és optimális választ küld vissza.<br /><br /> Ez a paraméter és a [cc](#cc) lekérdezésparaméter, egymást kölcsönösen kizáró&mdash;nem adható meg egyszerre.|Sztring|Igen|  
|<a name="safesearch" />biztonságos keresés|Felnőtt tartalom szűrésére használt szűrő. A következő értékeket a lehetséges azonban nem szűrő.<br /><ul><li>Ki&mdash;felnőtt szöveg és kép weblapok adja vissza.<br /><br/></li><li>Mérsékelt&mdash;felnőtt szöveget, de nem felnőtt képek weblapok adja vissza.<br /><br/></li><li>Szigorú&mdash;felnőtt szöveg és kép weblapok nem adják vissza.</li></ul><br /> Az alapértelmezett érték a közepes.<br /><br /> **Megjegyzés:** Ha a kérelem egy piaci származik, hogy a Bing felnőtt házirend megköveteli, hogy `safeSearch` Strict állítani, a Bing figyelmen kívül hagyja a `safeSearch` értékét, és a Strict használja.<br/><br/>**Megjegyzés:** használatakor a `site:` lekérdezési operátor nincs az esélye, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy mi a `safeSearch` lekérdezési paraméter értéke. Használjon `site:` csak akkor, ha tisztában lehet a tartalmat a helyen, és adott esetben támogatja a felnőtt tartalom lehetőségét. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület karakterláncok használni kívánt nyelvet. Adja meg a nyelvet a ISO 639-1-2 levelek nyelvi kódot. Például az angol nyelvi kódját EN. Az alapértelmezett érték EN (angol nyelvű).<br /><br /> Bár nem kötelező, a nyelvi mindig meg kell adnia. Általában beállított `setLang` által megadott azonos nyelvének `mkt` kivéve, ha a felhasználó szeretne-e a felhasználói felület karakterláncok más nyelven jelenik meg.<br /><br /> Ez a paraméter és a [elfogadás-nyelv](#acceptlanguage) fejléc, egymást kölcsönösen kizáró&mdash;nem adható meg egyszerre.<br /><br /> A felhasználói felület karakterlánc: a felhasználói felületen címkeként használt karakterlánc. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncok. A válasz objektumok Bing.com tulajdonságok mutató hivatkozások is, a megadott nyelv alkalmazni.|Sztring|Nem| 

### <a name="headers"></a>Fejlécek

Az alábbiakban adja meg a kérést a fejlécek. Az Ocp-Apim-előfizetés-kulcs és a Content-Type fejléc csak a szükséges fejléceket, de a felhasználói ügynök, X-MSEdge-ClientID, X-MSEdge-Ügyfélip és X-keresési-hely is meg kell tartalmaznia.


|Fejléc|Leírás|  
|------------|-----------------|  
|<a name="acceptlanguage" />Fogadja el nyelv|Nem kötelező kérelemfejlécet.<br /><br /> Nyelvek használandó felhasználói felület karakterláncok vesszővel tagolt listáját. A lista van csökkenő sorrendben. További információ, beleértve a várt formátum: [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ezt a fejlécet, és a [setLang](#setlang) lekérdezésparaméter, egymást kölcsönösen kizáró&mdash;nem adható meg egyszerre.<br /><br /> Ha megadta ezt a fejlécet, meg kell adni a [cc](#cc) lekérdezési paraméter. Annak megállapításához, a piacon vissza az eredményeket, a Bing megkeresi a listából, és egyesíti a első támogatott nyelvet használja a `cc` paraméter értékét. Ha a lista nem tartalmazza a támogatott nyelvi, Bing megkeresi a legközelebbi nyelvet és a piaci, amely támogatja a kérelem, vagy használja egy összesített vagy alapértelmezett piaci a eredmény elérése érdekében. Annak megállapításához, a Bing használt piacára, tekintse meg a piacra jutási BingAPIs fejléc.<br /><br /> Használja ezt a fejlécet, és a `cc` lekérdezésparaméter csak akkor, ha több nyelvet ad meg. Ellenkező esetben használja a [mkt](#mkt) és [setLang](#setlang) lekérdezési paramétert.<br /><br /> A felhasználói felület karakterlánc: a felhasználói felületen címkeként használt karakterlánc. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncok. A válasz objektumok Bing.com tulajdonságok mutató hivatkozások alkalmazza a megadott nyelven.|  
|<a name="contenttype" />Tartalomtípus|Szükséges kérelemfejlécet.<br /><br />Multipart/űrlapadat értékre kell állítani, és adja meg a határ értékét (például multipart/űrlapadat; boundary =\<határ karakterlánc\>). További részletekért lásd: [űrlap tartalomtípus](#content-form-types).
|<a name="market" />Piacra jutási BingAPIs|Válaszfejléc.<br /><br /> A piacon, a kérelem által használt. Az űrlap \<languageCode\>-\<országhívószám\>. Például: hu-hu.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> Az Eseménynapló-bejegyzés, amely tartalmazza a részleteket a kérelem az azonosítója. Ha hiba lép fel, rögzítése ezt. Ha nem határozza meg és hárítsa el a problémát, tartalmazzák ezt az Azonosítót és az egyéb adatokat, hogy a támogatási csapatával.|  
|<a name="subscriptionkey" />Az OCP-Apim-előfizetés-kulcs|Szükséges kérelemfejlécet.<br /><br /> Az Előfizetés kulcs, hogy az ehhez a szolgáltatáshoz, a regisztráció során [kognitív szolgáltatások](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Nem kötelező fejléc<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalom visszaadása, ha elérhető. Gyorsítótárazott tartalom visszaküldésével a Bing megakadályozása érdekében állítsa a Pragma fejléc no-cache (például Pragma: no-cache).
|<a name="useragent" />Felhasználói ügynök|Nem kötelező kérelemfejlécet.<br /><br /> A felhasználói ügynök származó a kérelmet. Bing optimalizált élményt biztosít a mobil felhasználók a felhasználói ügynök használja. Bár nem kötelező, meg hosszúan mindig meg ezt a fejlécet.<br /><br /> A felhasználói ügynök által a leggyakrabban használt böngészők ugyanazt a karakterláncot kell lennie. Felhasználói ügynök kapcsolatos információkért lásd: [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> A következő példák felhasználói ügynök karakterláncok.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilis; MSIE 10.0; Windows Phone 8.0; Trident vagy 6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/MÉZESKALÁCS) AppleWebKit/533.1 (KHTML; például gekkó) vagy 4.0-s verziójú Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone operációs rendszer 6_1, például a Mac OS X) AppleWebKit/536.26 (KHTML; például gekkó) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (a Windows NT 6.3; WOW64; Trident/7.0; Touch; RV:11.0), például gekkó<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 például a Mac OS X) AppleWebKit/537.51.1 (például gekkó KHTML) verzió/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Nem kötelező kérelem-válasz fejléce.<br /><br /> Bing felhasználók biztosít a konzisztens viselkedése Bing API-hívások használja ezt a fejlécet. Bing gyakran repülőútra új szolgáltatásait és fejlesztéseit, és különböző járatok forgalom hozzárendelése kulcsaként használ az ügyfél-azonosító. Címzett több kérés során a felhasználó nem használja az ugyanazon ügyfél-azonosító, ha a Bing is hozzárendelheti a felhasználó több ütköző járatok. Több ütköző járatok lett hozzárendelve inkonzisztens felhasználói élményt is járhat. Például ha a második kérelmet egy másik repülési-hozzárendelést, mint az első, a felhasználói élmény lehet váratlan. Is, a Bing használatával az ügyfél-azonosító személyessé tétele érdekében, hogy az ügyfél webes eredmények keresési előzmények gazdagabb élményt biztosítva a felhasználói azonosítót.<br /><br /> Bing is használja ezt a fejlécet a tevékenység egy ügyfél-azonosítót. által generált elemzésével eredmény rangsort javítása érdekében A fontos fejlesztéseket kézbesíteni Bing API-k és pedig lehetővé teszi, hogy a nagyobb kattintások sebesség API fogyasztó eredmények magasabb színvonalú segít.<br /><br /> **Fontos:** bár nem kötelező, vegye figyelembe ezt a kötelező fejlécet. Megőrzése az ügyfél-azonosító címzett több kérés során ugyanazon felhasználó és eszköz kombináció lehetővé teszi a 1.) az API fogyasztó úgy, hogy a következetes felhasználói élményt biztosít, és 2) magasabb kattintások díjszabás keresztül eredmények magasabb színvonalú fogadjon a Bing API-k.<br /><br /> Az alábbiakban az alapvető használati szabályok ezt a fejlécet.<br /><ul><li>Minden olyan felhasználóhoz, használja az alkalmazást az eszközön rendelkeznie kell egy egyedi, a Bing létrehozott ügyfél-azonosítót.<br /><br/>Ha a kérelem nem adja meg ezt a fejlécet, a Bing Azonosítót hoz létre, és adja vissza, az X-MSEdge-ClientID válaszfejléc. A csak, hogy nem bele kell foglalni ezt a fejlécet a kérelem, az első alkalommal a felhasználó használja az alkalmazást azokon az eszközökön.<br /><br/></li><li>**Figyelem:** meg kell győződnie arról, hogy az ügyfél-azonosító nem összekapcsolhatónak a hitelesített felhasználói fiók adatokat.</li><li>Az ügyfél-Azonosítót használja az alkalmazás lehetővé teszi a felhasználó az eszköz minden egyes Bing API-kérelemhez.<br /><br/></li><li>Megőrzése az ügyfél-azonosítót. Továbbra is fennáll az azonosító egy webböngésző alkalmazást, állandó HTTP cookie-k segítségével ellenőrizze az azonosító olyan munkamenetekben. Ne használjon egy munkamenetcookie-t. Más alkalmazások, például a mobile apps szolgáltatásban használja az eszköz állandó tároló megőrizni a azonosítóját.<br /><br/>A felhasználó használja az alkalmazást azokon az eszközökön, amikor legközelebb megőrizte ügyfél-azonosító beolvasása.</li></ul><br /> **Megjegyzés:** Bing válaszok is, vagy nem tartalmazza ezt a fejlécet. A válasz tartalmazza ezt a fejlécet, ha rögzíti az ügyfél-Azonosítót, és a későbbi Bing azokon az eszközökön a felhasználó használja azt.<br /><br /> **Megjegyzés:** Ha adja meg az X-MSEdge-ClientID, nem tartalmazhat-e be a cookie-kat a kérelemben.|  
|<a name="clientip" />X-MSEdge-Ügyfélip|Nem kötelező kérelemfejlécet.<br /><br /> Az ügyféleszköz IPv4 vagy IPv6-cím. Az IP-címet a felhasználó földrajzi helye felderítésére szolgál. Bing a Tartózkodásihely-adatok a biztonságos keresési viselkedés meghatározására használja.<br /><br /> **Megjegyzés:** bár nem kötelező, meg hosszúan mindig adja meg ezt a fejlécet, és az X-keresési-helyet megjelölő fejlécet.<br /><br /> Nem takarják a címet (például, ha megváltoztatja az utolsó oktett 0). A cím eredmények nem bárhol mellett az eszköz tényleges helyére a helyen obfuscating, emiatt a Bing hibás eredmények szolgál.|  
|<a name="location" />X keresése|Nem kötelező kérelemfejlécet.<br /><br /> Az ügyfél földrajzi helye leíró kulcs/érték párok pontosvesszővel elválasztott listája. Bing használja a helyére vonatkozó információkat, annak meghatározásához, hogy biztonságos keresés és a kapcsolódó helyi tartalom. Adja meg, mint a kulcs/érték pár \<kulcs\>:\<érték\>. Az alábbiakban a kulcsokhoz, használja a felhasználó helyének megadására.<br /><br /><ul><li>LAT&mdash;szükséges. A szélesség fok, az ügyfél helye. A szélesség kell lennie nullánál-90.0 és kisebb vagy egyenlő, mint +90.0. Negatív értékek azt jelzik, déli földrajzi szélesség és pozitív értékek azt jelzik, északi szélességet.<br /><br /></li><li>hosszú&mdash;szükséges. A hosszúsági koordinátákkal meghatározni az ügyfél helyét (fokban megadva). A hosszúság kell lennie nullánál-180.0 és kisebb vagy egyenlő, mint +180.0. Negatív értékek azt jelzik, Nyugat hosszúságot, és pozitív értékek azt jelzik, eastern hosszúságot.<br /><br /></li><li>-&mdash;szükséges. A radius, a mérőszámok, adja meg a koordináták a vízszintes pontosságát. Az eszköz helymeghatározó szolgáltatás által visszaadott értéket átadni. Tipikus értékek 22m GPS/Wi-Fi, a cella torony keresztárfolyam 380m és a fordított IP-címkeresés 18,000m lehet.<br /><br /></li><li>TS&mdash;nem kötelező. Ha az ügyfél volt-e az adott helyen UTC UNIX időbélyegzője. (A UNIX-időbélyeg érték 1970. január 1. óta eltelt percek számát.)<br /><br /></li><li>HEAD&mdash;nem kötelező. Az ügyfél relatív címsor vagy utazás irányát. Adja meg a 0 – 360, a számbavételi jobbra viszonyítva igaz északi fok utazás irányát. Adja meg a kulcs csak akkor, ha a `sp` kulcs értéke nem nulla.<br /><br /></li><li>SP&mdash;nem kötelező. A vízszintes sebesség (sebesség), az a másodpercenkénti, az ügyféleszközök utazik mérőszámok.<br /><br /></li><li>ALT&mdash;nem kötelező. Az ügyfél eszköz, a mérőszámok magasság.<br /><br /></li><li>vannak&mdash;nem kötelező. A radius, a mérőszámok, amely meghatározza a koordináták a függőleges pontosságát. Adja meg ezt a kulcsot csak akkor, ha megadja a `alt` kulcs.<br /><br /></li></ul> **Megjegyzés:** annak ellenére, hogy a kulcsokat számos nem kötelező, a további információt ad meg, annál pontosabb a hely eredményei.<br /><br /> **Megjegyzés:** bár nem kötelező, meg hosszúan mindig adja meg a felhasználó földrajzi helye. Különösen fontos, ha az ügyfél IP-cím nem pontosan mutassa a felhasználó fizikai helyét (például, ha az ügyfél használ VPN) biztosít a hely. Az optimális eredmény ezt a fejlécet, és az X-MSEdge-Ügyfélip fejléc kell foglalni, de mindenképpen tartalmaznia kell ezt a fejlécet.|

> [!NOTE] 
> Ne feledje, hogy a vonatkozó törvényeket, beleértve az ezek a fejlécek vonatkozó megfelelőségi a használati feltételek esetében. Például bizonyos országokban, Európa, például nincsenek beszerzése a felhasználói hozzájárulás előtt bizonyos követési eszközök felhasználói eszközökön.


<a name="content-form-types" />

### <a name="content-form-types"></a>Tartalom űrlap típusok

Minden egyes kérelem tartalmaznia kell a Content-Type fejléc. A fejléc értékre kell állítani: multipart /-űrlapadat; határ =\<határ karakterlánc\>, ahol \<határ karakterlánc\> egyedi, nem átlátszó karakterlánc, amely azonosítja a határ adatai. Például a határ boundary_1234-abcd =.


Ha Visual keresési küldi el egy kép token vagy URL-címe, a következő jeleníti meg az űrlap adatait meg kell adni a FELADÁS egy vagy több törzsében. Az űrlap adatait tartalmaznia kell a tartalomtípus-szabályozó fejléc és a `name` paraméter "knowledgeRequest" értékre kell állítani. A vonatkozó további információért a `imageInfo` objektumazonosító, lásd: [a kérelem](#the-request).


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

Ha feltölti egy helyi lemezképet, a következő jeleníti meg az űrlap adatait meg kell adni a FELADÁS egy vagy több törzsében. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejlécben. A `name` paraméter "kép" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A Content-Type fejléc minden gyakran használt kép mime-típus lehet beállítani. A képernyő tartalmát a bináris kép. A maximális kép lehet, hogy feltölti mérete 1 MB. 


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

A következő bemutatja, hogyan adhatja meg a tartományban, a feltöltött lemezkép.

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

A következő jeleníti meg a a teljes kép insights kérést kapott egy kép token és tartományban. A insights jogkivonat beszerzése egy korábbi hívás /images/search.


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

Ha vannak a kép insights, a válasz egy vagy több tartalmaz `tags` feltárása tartalmaznak. A `image` mezőben a bemeneti kép insights lexikális eleme.

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

A `tags` mező egy megjelenítési nevet és a műveletek (insights) listáját tartalmazza. A címkék egyike tartalmaz egy `displayName` állított üres karakterláncot kell megadni. Ezt a címkét tartalmaz, amelyek közé tartozik a lemezképet, vizuálisan hasonló lemezképek és a lemezképben található elemek vásárlásra szolgáló adatforrások például alapértelmezett feltárása. Mivel a teljes kép iránt, az alapértelmezett insights címke nem tartalmazza a határolókeret egyik fontos a régiók jelölőnégyzetéből.


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

Az alapértelmezett insights listájáért lásd: [insights alapértelmezett](./default-insights-tag.md).



A fennmaradó címkék más insights, amely lehet, hogy a felhasználó iránt tartalmaznak. Például a rendszerkép tartalmazza a szöveg, ha az egyik címkével tartalmazhatják egy TextResults insight, amely tartalmazza a felismert szöveget. Vagy ha Bing (személy, hely vagy dolog) entitás felismeri a kép, címkék egyikével, előfordulhat, hogy azonosítja az entitást. Visual keresési is adja vissza, amely különböző feltételeket (címke) a bemeneti kép származik. Ezekkel a címkékkel engedélyezése a felhasználók számára a lemezképben található fogalmak vizsgálatát. Például ha a bemeneti kép famous sportoló, annak a címkék lehetnek sport, amely sport képeket mutató hivatkozásokat tartalmaz.

Minden tag megjelenített név, használhatja a insight kategorizálását, amely azonosítja a insight alkalmazó iránt érdeklődik, maguk feltárása és a miniatűr kép határolókeret tartalmazza. Például ha a kép egy sport jersey elhasználódó személy, egyik címkével tartalmazhat egy határolókeret, amely a jersey bounds és VisualSearch és ProductVisualSearch insights tartalmazza. És egy másik címke tartalmazhat egy helyileg kapcsolódó képek lekérni egy /images/search API-kérelem URL-címet vagy egy Bing.com keresési URL-címet, amely a felhasználót, hogy a Bing.com kép keresési eredményeket tartalmazó ImageResults egyet.

Az alapértelmezett insights címke nem található összes kódcímkének például jelölőnégyzetéből, amelyek azonosítják a lemezkép iránti érdeklődését régiói határolókeret. Például a lemezkép több felismert személyt tartalmazza, ha a címkék tartalmazhatnak, határolókeret mezőkbe az egyes annak a személynek, vagy ha a kép felismert ruházati elemet tartalmaz, a címkék tartalmazhatnak, egyes felismert ruházati elemekhez tartozó mezőkben határolókeret. A határoló mezőkbe hozhat létre interaktív területek a képe, amely kattintáskor, adja meg az adott régióban, a kép tartalmának részleteit. A határoló jelölőnégyzetéből, amelyek azonosítják a teljes kép kép nem interaktív területek kell tartalmaznia.

### <a name="text-recognition"></a>Szöveg

A rendszerkép tartalmazza a szöveg, amelyet a szolgáltatás észleli, ha az egyik címkével egy TextResults insight (művelet) fogja tartalmazni. A insight `displayName` a felismert szöveget tartalmaz. 

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

Mivel a címke `displayName` mezőben ##TextRecognition ne használja azt a kategóriát a UX a cím Álljon a megjelenítendő nevet, amely kezdődik ##. Ehelyett használja a művelet megjelenített neve.


Szöveg is fel tudja ismerni a kapcsolattartási adatokat a névjegyek, például a telefonszámok és az e-mail címet. A határolókeret a kapcsolattartási adatokat a kártya helyét azonosítja. 

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

A rendszerkép tartalmazza a felismert entitás, például egy személy, hely vagy dolog, ha az egyik címkével entitás egyet tartalmazhat. Entitásokat is tartalmazhat trivia, a következő példában látható módon:

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
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "Trivia",
          "data" : {
            "value" : [
              {
                "name" : "Where was the cornerstone of the statue of liberty laid",
                "text" : "<the answer>",
                "hostPageUrl" : "http:\/\/contoso.com\/history\/...",
              },
              {
                "name" : "Why Is the Statue of Liberty Green",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/why-statue-of-liberty-is-green",
              },
              {
                "name" : "What is the Statue of Liberty made of",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/art-literature\/statue-liberty-made",
              }
            ]
          }
        }
      ]
    }
```



## <a name="next-steps"></a>További lépések

Használatának gyors megkezdése az első kérelem, tekintse meg a quickstarts: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Próbálja ki az API-t. Ugrás a [Visual keresési API-tesztelési konzol](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Ismerje meg, a [Visual keresési API-referencia](https://aka.ms/bingvisualsearchreferencedoc). A hivatkozás tartalmazza a végpontok, fejlécek és lekérdezési paraméterek, kérje a keresési eredmények között szeretné használni. A válasz objektumok meghatározását is tartalmaz. 

Olvassa el [Bing használatát és a megjelenített követelmények](./use-and-display-requirements.md) , nem megszakítja a keresési eredmények használatára vonatkozó szabályok egyike.


