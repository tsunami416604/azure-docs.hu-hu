---
title: Keresési lekérdezéseket küld a Bing Visual Search API
titlesuffix: Azure Cognitive Services
description: További információ a REST API a Bing Visual Search API használt paraméterek.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 12/18/2018
ms.author: aahi
ms.openlocfilehash: 07f471debad87a2fa2f6583db624e9ee204d02bc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821167"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Keresési lekérdezéseket küld a Bing Visual Search API

Ez a cikk ismerteti a paramétereket és a kéréseket a Bing Visual Search API, valamint a válasz-objektum attribútumai.

Betekintést nyerhet kép három módon: 

- a, a lemezkép egyik előző hívásából insights token használatával a [a Bing Image Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) végpontok.
- A kép URL-cím küldésekor.
- (A bináris) kép feltöltése


## <a name="bing-visual-search-requests"></a>A Bing vizuális keresési kérelmek

Ha képhez tartozó jogkivonatot vagy URL-címet küld a Visual Search szolgáltatásnak, az alábbiakban láthatja a POST törzsében kötelezően szerepeltetendő JSON-objektumot. 

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

Az `imageInfo` objektumnak tartalmaznia kell az `url` vagy az `imageInsightsToken` mezőt, de nem mindkettőt. Az `url` mezőben adja meg egy interneten elérhető kép URL-címét. A legnagyobb támogatott képméret 1 MB.

Az `imageInsightsToken` mezőben egy megállapítási jogkivonatot kell megadni. A megállapítási jogkivonat beszerzéséhez hívja meg a Bing Image API-t. A válasz egy `Image` objektumokból álló listát tartalmaz. Mindegyik `Image` objektumban van egy `imageInsightsToken` mező, amely a jogkivonatot tartalmazza.

A `cropArea` mező kitöltése nem kötelező. A körülvágási terület a releváns tartomány bal felső sarkát és jobb alsó sarkát határozza meg. Adja meg az értékeket egy 0,0 és a 1,0 közötti tartományban. Az értékek a teljes szélesség vagy magasság százalékos értékei. Például a fenti példa a kép jobb oldalát jelöli meg releváns tartományként. Akkor használja, ha a releváns tartományra szeretné korlátozni a megállapítások lekérését.

A `filters` objektum egy webhelyszűrőt tartalmaz (lásd a `site` mezőt), amellyel egy adott tartományra korlátozhatja a hasonló képek és hasonló termékek eredményeit. Például ha a kép egy Surface Book-ot ábrázol, a `site` értékeként beállíthatja a www.microsoft.com webhelyet. 

Ha megállapításokat szeretne lekérni egy kép helyi másolatáról, töltse fel a képet bináris adatokként.

A lehetőségek POST-törzsbe való belefoglalásával kapcsolatos részletekért lásd: [A tartaloműrlapok típusai](#content-form-types).


### <a name="search-endpoint"></a>Search-végpont

A Visual Search-végpont a következő: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

A kérelmeket csak HTTP POST kérelemként lehet elküldeni. 


### <a name="query-parameters"></a>Lekérdezési paraméterek

Az alábbiakban azok a lekérdezési paraméterek láthatók, amelyeket a kérelemnek meg kell adnia. A kérelemnek tartalmaznia kell legalább az `mkt` lekérdezési paramétert.

| Name (Név)                              | Érték                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Typo   | Szükséges |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|----------|
| <a name="cc" />cc                 | Annak az országnak a két karakterből álló országkódja, ahonnan az eredmények származnak.<br /><br /> Ha beállítja ezt a paramétert, az [Accept-Language](#acceptlanguage) fejlécet is meg kell adnia. A Bing a nyelvek listájának első támogatott nyelvét használja, és kombinálja azt a megadott országkóddal, hogy meghatározza a piacot, ahonnan vissza fogja adni az eredményeket. Ha a nyelvek listája nem tartalmaz támogatott nyelvet, a Bing megkeresi a kérelmet támogató legközelebbi nyelvet és piacot. Másik lehetőségként a megadott piac helyett egy összesített vagy alapértelmezett piacot is használhat az eredmények megszerzéséhez.<br /><br /> Csak akkor használja ezt lekérdezési paramétert és az `Accept-Language` lekérdezési paramétert, ha több nyelvet ad meg, ellenkező esetben az `mkt` és a `setLang` paramétereket használja.<br /><br /> Ez a paraméter és az [mkt](#mkt) lekérdezési paraméter kölcsönösen kizárják egymást, ne adja meg mindkettőt. | String | Nem       |
| <a name="mkt" />mkt               | A piac, ahonnan az eredmények származnak. <br /><br /> **MEGJEGYZÉS:** Hosszúan mindig adja meg a piacon, ha ismert. Ha megadja a piacot, azzal elősegíti, hogy a Bing a kérelmet továbbítva megfelelő, optimális választ adjon vissza.<br /><br /> Ez a paraméter és a [cc](#cc) lekérdezési paraméter kölcsönösen kizárják egymást, ne adja meg mindkettőt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | String | Igen      |
| <a name="safesearch" />safeSearch | Felnőtt tartalmak szűrésére szolgáló szűrő. A következők azok a lehetséges szűrőértékek, amelyek nem különböztetik meg a kis- és nagybetűket.<br /><ul><li>Off (Ki) – Visszaadja a felnőtt szöveget vagy képeket tartalmazó weblapokat.<br /><br/></li><li>Moderate (Közepes) – Visszaadja a felnőtt szöveget tartalmazó weblapokat, de a felnőtt képeket tartalmazó weblapokat nem.<br /><br/></li><li>Strict (Szigorú) – Nem ad vissza felnőtt szöveget és képeket tartalmazó weblapokat.</li></ul><br /> Az alapértelmezett érték a Moderate.<br /><br /> **MEGJEGYZÉS:** Ha a kérelem egy piaci származik, a Bing felnőtt szabályzat megköveteli, hogy `safeSearch` Strict beállítása, a Bing figyelmen kívül hagyja a `safeSearch` értékét, és szigorú használja.<br/><br/>**MEGJEGYZÉS:** Ha használja a `site:` operátor, annak az esélyét, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy mi van a `safeSearch` lekérdezési paraméter értéke. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.                                    | String | Nem       |
| <a name="setlang" />setLang       | A felhasználói felület sztringjeihez használni kívánt nyelv. A nyelv megadásához használja az ISO 639-1 kétbetűs nyelvkódját. Az angol nyelv nyelvkódja például az EN. Az alapértelmezett érték az EN (angol).<br /><br /> Bár nem kötelező, javasoljuk, hogy mindig adja meg a nyelvet. A `setLang` paramétert általában az `mkt` által meghatározott nyelvre kell állítani, hacsak a felhasználó nem szeretné más nyelven megjeleníteni a felhasználói felület sztringjeit.<br /><br /> Ez a paraméter és az [Accept-Language](#acceptlanguage) fejléc kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások is a megadott nyelvet alkalmazzák.                                                                                                            | String | Nem       |

## <a name="headers"></a>Fejlécek

Az alábbiakban azok a fejlécek láthatók, amelyeket a kérelmének meg kell adnia. Csak a Content-Type és az Ocp-Apim-Subscription-Key a kötelező fejlécek, de a User-Agent, X-MSEdge-ClientID, X-MSEdge-ClientIP és X-Search-Location fejléceket is érdemes belefoglalni a kérelembe.


| Fejléc                                                | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <a name="acceptlanguage" />Accept-Language            | Választható kérelemfejléc.<br /><br /> Nyelvek vesszővel elválasztott listája a felhasználói felület sztringjeihez. A lista prioritás szerinti csökkenő sorrendben jelenik meg. További információért, például a várt formátummal kapcsolatos részletekért lásd: [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ez a fejléc és a [setLang](#setlang) lekérdezési paraméter kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> Ha beállítja ezt a fejlécet, akkor a [cc](#cc) lekérdezési paramétert is meg kell adnia. A megfelelő piac meghatározásához a Bing a listában talált első támogatott nyelvet használja, és kombinálja azt a `cc` paraméter értékével. Ha a lista nem tartalmaz támogatott nyelvet, a Bing megkeresi a kérelmet támogató legközelebbi nyelvet és piacot, vagy másik lehetőségként egy összesített vagy alapértelmezett piacot használ az eredmények beszerzéséhez. A Bing által használt piac meghatározásához tekintse meg a BingAPIs-Market fejlécet.<br /><br /> Csak akkor használja ezt a fejlécet és a `cc` lekérdezési paramétert, ha több nyelvet ad meg. Ellenkező esetben használja az [mkt](#mkt) és a [setLang](#setlang) lekérdezési paramétereket.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások a megadott nyelvet alkalmazzák.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| <a name="contenttype" />Content-Type                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| <a name="market" />BingAPIs-Market                    | Válaszfejléc.<br /><br /> A kérelem által használt piac. A formátum a következő: \<languageCode\>-\<countryCode\>. Például: en-US.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| <a name="traceid" />BingAPIs-TraceId                  | Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó naplóbejegyzés azonosítója. Ha hiba történik, rögzítse ezt az azonosítót. Ha nem tudja meghatározni és megoldani a problémát, foglalja bele a kérelembe ezt az azonosítót is a támogatási csoportnak megadott többi információval együtt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| <a name="subscriptionkey" />Ocp-Apim-Subscription-Key | Kötelező kérelemfejléc.<br /><br /> Az előfizetési kulcs, amelyet akkor kapott, amikor feliratkozott a szolgáltatásra a [Cognitive Servicesben](https://www.microsoft.com/cognitive-services/).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| <a name="pragma" />Pragma                             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| <a name="useragent" />User-Agent                      | Választható kérelemfejléc.<br /><br /> A kérelmet küldő felhasználói ügynök. A Bing arra használja a felhasználói ügynököt, hogy optimalizált élményt nyújtson a mobilfelhasználóknak. Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet.<br /><br /> A felhasználói ügynöknek a gyakran használt böngészők által küldött sztringgel megegyezőnek kell lennie. A felhasználói ügynökökkel kapcsolatos információért lásd: [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Az alábbiakban példákat láthat a felhasználóiügynök-sztringekre.<br /><ul><li>Windows Phone – Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android – Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone – Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC – Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad – Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| <a name="clientid" />X-MSEdge-ClientID                | Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br /> **FONTOS:** Bár nem kötelező, fontolja meg ezt a kötelező fejlécet. Ha ugyanahhoz a végfelhasználóhoz és eszközkombinációhoz több kérelemben is megőrzi az ügyfél-azonosítót, azzal lehetővé teszi, 1) hogy az API fogyasztójának egységes felhasználói élményben legyen része, és 2) magasabb legyen az átkattintási arány a Bing API-k jobb minőségű eredményeinek köszönhetően.<br /><br /> A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>**FIGYELEM:** Biztosítania kell, hogy az ügyfél-azonosító nem összekapcsolhatónak a hitelesített felhasználói fiók adatokat.</li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok is, vagy nem feltétlenül tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha az X-MSEdge-ClientID adja meg, nem tartalmazhat cookie-kat a kérésben. |
| <a name="clientip" />X-MSEdge-ClientIP                | Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> **MEGJEGYZÉS:** Bár nem kötelező, csak Ön javasoljuk, hogy mindig adja meg ezt a fejlécet és az X-Search-Location fejlécet.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| <a name="location" />X-Search-Location                | Választható kérelemfejléc.<br /><br /> Kulcs/érték párok pontosvesszővel elválasztott listája, amely leírja az ügyfél földrajzi helyét. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését, illetve releváns helyi tartalmakat adjon vissza. A kulcs/érték párt \<kulcs\>:\<érték\> formátumban adja meg. Az alábbiakban láthatja a felhasználó tartózkodási helyének megadására használt kulcsokat.<br /><br /><ul><li>lat – Kötelező. Az ügyfél tartózkodási helyének földrajzi szélessége, fokban megadva. A földrajzi szélesség nem lehet -90,0 foknál kisebb és +90,0 foknál nagyobb. A negatív értékek a déli szélességeket, a pozitív értékek pedig az északi szélességeket jelölik.<br /><br /></li><li>long – Kötelező. Az ügyfél tartózkodási helyének földrajzi hosszúsága, fokban megadva. A földrajzi hosszúság nem lehet -180,0 foknál kisebb és +180,0 foknál nagyobb. A negatív értékek a nyugati hosszúságokat, a pozitív értékek pedig a keleti hosszúságokat jelölik.<br /><br /></li><li>re – Kötelező. A méterben megadott sugár, amely meghatározza a koordináták vízszintes pontosságát. Adja át az eszköz helymeghatározási szolgáltatása által visszaadott értéket. A tipikus érték GPS/Wi-Fi esetén 22 m, telefontoronnyal végzett háromszögelés esetén 380 m, fordított IP-keresés esetén pedig 18 000 m.<br /><br /></li><li>ts – Választható. Az UTC UNIX-időbélyeg arról, hogy mikor tartózkodott az ügyfél az adott helyen. (Az UNIX-időbélyeg az 1970. január 1. óta eltelt másodpercek száma).<br /><br /></li><li>head – Választható. Az ügyfél relatív haladási vagy utazási iránya. Az utazás irányt fokban adja meg 0 és 360 között, a tényleges északhoz képest az óramutató járásával megegyező irányban számítva. Csak akkor adja meg ezt a kulcsot, ha az `sp` kulcs értéke nem nulla.<br /><br /></li><li>sp – Választható. A méter per másodpercben megadott horizontális sebesség, amellyel az ügyféleszköz halad.<br /><br /></li><li>alt – Választható. Az ügyféleszköz tengerszint feletti magassága, méterben megadva.<br /><br /></li><li>are – Választható. A méterben megadott sugár, amely meghatározza a koordináták függőleges pontosságát. Csak akkor adja meg ezt a kulcsot, ha az `alt` kulcsot is megadja.<br /><br /></li></ul> **MEGJEGYZÉS:** Bár a kulcsokat számos nem kötelező, a további adatokat ad meg, annál pontosabb helyet eredménye.<br /><br /> **MEGJEGYZÉS:** Bár nem kötelező, csak Ön javasoljuk, hogy mindig adja meg a felhasználó földrajzi helyét. Különösen fontos megadni a helyet, ha az ügyfél IP-címe nem tükrözi pontosan a felhasználó fizikai helyét (például ha az ügyfél VPN-t használ). Az optimális eredmény érdekében foglalja bele a kérelembe ezt a fejlécet és az X-MSEdge-ClientIP fejlécet, de legalább ezt a fejlécet.                                                                                                                                                              |

> [!NOTE] 
> Ne feledje, hogy a Használati feltételek a fejlécek használatával kapcsolatban is előírják a vonatkozó törvények betartását. Egyes joghatóságokban, például Európában vannak olyan követelmények, amelyek előírják a felhasználói hozzájárulás megszerzését a nyomkövető eszközök felhasználói eszközökön való elhelyezése előtt.


<a name="content-form-types" />

### <a name="content-form-types"></a>A tartaloműrlapok típusai

Minden kérelemnek tartalmaznia kell a Content-Type fejlécet. A fejléc beállításának a következőnek kell lennie: multipart/form-data; boundary=\<boundary string\>, ahol a \<boundary string\> egy egyedi, átlátszatlan sztring, amely az űrlapadatok határát azonosítja. Például: boundary=boundary_1234-abcd.


Ha képhez tartozó jogkivonatot vagy URL-címet küld a Visual Search szolgáltatásnak, az alábbiakban láthatja a POST törzsében kötelezően szerepeltetendő űrlapadatokat. Az űrlapadatoknak tartalmazniuk kell a Content-Disposition fejlécet, és a `name` paraméterét „knowledgeRequest” értékre kell állítani. További információ a `imageInfo` objektumazonosító, tekintse meg a kérelmet.


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

Ha helyi képet tölt fel, az alábbiakban láthatja a POST-törzsben kötelezően megadandó űrlapadatokat. Az űrlapadatoknak tartalmazniuk kell a Tartalom-Témakör fejlécet. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. A Content-Type fejléchez bármelyik gyakran használt, képekre vonatkozó MIME-típust be lehet állítani. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB. A legnagyobb szélesség vagy magasság legfeljebb 1500 képpont lehet.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Az alábbiak bemutatják, hogyan lehet megadni egy feltöltött kép releváns tartományát.

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


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Példakérelem

Az alábbiakban egy teljes képmegállapítási kérelmet láthat, amely egy képhez tartozó jogkivonatot és egy releváns tartományt ad át. A megállapítási jogkivonatot egy /images/search felé irányuló korábbi hívásból lehet beszerezni.


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


## <a name="bing-visual-search-responses"></a>A Bing Visual Search válaszai

Ha vannak elérhető megállapítások a képhez, a válasz egy vagy több `tags` mezőt tartalmaz, bennük a megállapításokkal. Az `image` mező tartalmazza a bemeneti kép megállapítási jogkivonatát.

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

A `tags` mező egy megjelenítendő nevet és egy műveletlistát (megállapítások) tartalmaz. Az egyik címke egy `displayName` mezőt tartalmaz, amelyhez egy üres sztring van beállítva. Ez a címke tartalmazza az alapértelmezett megállapításokat, például a képet tartalmazó weblapokat, a vizuálisan hasonló képeket és a vásárlási lehetőségeket a képen látható tárgyakhoz. Mivel az egész kép releváns, az alapértelmezett megállapítások címkéje nem tartalmaz határoló kereteket a releváns tartományokhoz.


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

Az alapértelmezett megállapítások listájáért lásd: [Alapértelmezett megállapítások](../default-insights-tag.md).



A többi címke más megállapításokat tartalmaz, amelyeket a felhasználó érdekesnek találhat. Például ha a kép szöveget tartalmaz, az egyik címke egy TextResults megállapítást tartalmazhat, benne a felismert szöveggel. Vagy ha a Bing felismer egy entitást (személy, hely vagy dolog) a képen, a címkék egyike azonosíthatja az entitást. A Visual Search emellett egy, a bemeneti képből kinyert kifejezéskészletet (címkék) is visszaad. Ezek a címkék lehetővé teszik a felhasználók számára, hogy felfedezzék a képhez kapcsolódó fogalmakat. Például ha a bemeneti kép egy híres sportolót ábrázol, az egyik címke lehet a Sport, amely sporttal kapcsolatos képekre mutató hivatkozásokat tartalmaz.

Minden címke tartalmaz egy megjelenítendő nevet, amelyet a megállapítás kategorizálására használhat, egy határoló keretet, amely azonosítja a megállapítás releváns tartományát, magukat a megállapításokat, valamint a kép miniatűrjét. Például ha a kép egy sportmezt viselő személyt ábrázol, az egyik címke egy határoló keretet tartalmazhat, amely körülhatárolja a mezt és VisualSearch-, valamint ProductVisualSearch-megállapításokat tartalmaz. Egy másik címke pedig egy ImageResults-megállapítást tartalmazhat, benne egy kapcsolódó témájú képeket lekérő /images/search API-kérelem URL-címével, vagy egy Bing.com keresési URL-címmel, amely átirányítja a felhasználót a Bing.com képkeresési eredményeihez.

Az alapértelmezett megállapításcímkén kívül minden címke tartalmaz határoló kereteket, amelyek a kép érdeklődésre számot tartó tartományait azonosítják. Ha például a képen több személy is felismerhető, vagy a képen felismerhető ruhadarabok vannak, a címkék mindegyik személyhez vagy felismert ruhadarabhoz kapcsolódóan tartalmazhatnak határoló keretet. A határoló keretekkel interaktív területeket hozhat létre a képen, amelyekre kattintva információk jelennek meg a kép adott tartományában lévő tartalmakkal kapcsolatban. Nem hozzon létre interaktív területeket olyan képen, amelyen a határoló keretek az egész képet azonosítják.

### <a name="text-recognition"></a>Szövegfelismerés

Ha a kép olyan szöveget tartalmaz, amelyet a szolgáltatás felismer, az egyik címke egy TextResults megállapítást (műveletet) fog tartalmazni. A megállapítás `displayName` mezője tartalmazza a felismert szöveget. 

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

Mivel a címke `displayName` mezője a ##TextRecognition karakterláncot tartalmazza, ne használja azt kategóriacímként a felhasználói felületen. Ez érvényes a ## karakterlánccal kezdődő megjelenített nevek mindegyikére. Ilyen esetben használja a művelet megjelenített nevét.


A szövegfelismerés képes felismerni a névjegykártyákon található kapcsolattartási adatokat, például a telefonszámokat és e-mail-címeket. A határoló keret azonosítja a kapcsolattartási adatok helyét a kártyán. 

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

Ha a képen egy felismerhető entitás, például egy személy, hely vagy tárgy látható, a címkék egyike tartalmazhat egy Entity megállapítást. 

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

## <a name="see-also"></a>Lásd még

* [Mi az a Bing Visual Search API](../overview.md)
* [Oktatóanyag: Egyoldalas webes alkalmazás összeállítása – a Bing vizuális keresés](../tutorial-bing-visual-search-single-page-app.md)
