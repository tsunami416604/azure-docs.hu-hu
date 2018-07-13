---
title: Mi a Bing Entity Search? | Microsoft Docs
description: Ismerje meg, hogyan entitásokat és a helyek keresése a weben a Bing Entity Search API használatával.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 275430bc6ee8f935978243e61f68713974648189
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008110"
---
# <a name="what-is-bing-entity-search"></a>Mi a Bing Entity Search?

A Bing Entity Search API a Bing keresési lekérdezést küld, és lekéri az eredmények, amelyek tartalmazzák az entitások és a helyek. Éttermek, Szálloda vagy más helyi üzletek helyre kiterjed. A Bing helyen adja vissza, ha a lekérdezés nevét adja meg a helyi üzleti vagy üzleti (például éttermek a közelben) írja be a következőt kéri. A Bing entitásokat ad vissza, ha a lekérdezés a jól ismert személyek, helyek (idegenforgalmi létesítmények, állapotok, országban, stb.) vagy dolgot határoz meg.

## <a name="suggesting--using-search-terms"></a>Keresési kifejezések javaslása és használata

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja a keresőkifejezést, kódolja URL-címként a karakterláncot a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) lekérdezési paraméter beállítása előtt. Például, ha a felhasználó megadja *Marcus Appel*állítsa be `q` való *Marcus + Appel* vagy *Marcus % 20Appel*.

Ha a keresett kifejezés egy gépelési hibát tartalmaz, a keresési válasz tartalmazza a [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) objektum. Az objektum az eredeti helyesírás- és a javított helyesírás-ellenőrzés, hogy a keresési Bing használt jeleníti meg.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Entitások kérése

Egy kérelem (példa), lásd: [az első kérést](./quick-start.md).

## <a name="the-response"></a>A válasz

A válasz tartalmaz egy [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objektum. Bing talál egy entitás vagy a hely, amely fontos, ha az objektum tartalmazza a `entities` mezőben `places` mezőt, vagy mindkettőt. Ellenkező esetben a válasz objektum nem tartalmaz vagy mező.
> [!NOTE]
> Entitás válaszok támogatja több piacokon, de a helyek válasz csak az USA üzleti helyek támogatja. 

A `entities` mező egy [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) listáját tartalmazó objektum [entitás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) objektumok (lásd a `value` mezőben). A lista is tartalmazhat, egyetlen domináns entitás, több Egyértelműsítő entitás vagy mindkettőt. 

Domináns entitás olyan entitás, amely szerint a Bing, az egyetlen entitás, amely eleget tesz a kérelem (nincs feltárhatja, hogy mely entitás eleget tesz a kérelem félreérthetőség). Több entitás sikerült teljesíteni a kérést, lista tartalmazza-e több Egyértelműsítő entitást. Például ha a kérelem egy filmet névhasználati általános címe használ, a listán, valószínűleg Egyértelműsítő entitásokat tartalmaz. De ha a kérés a névhasználati az adott címét határozza meg, a listán, valószínűleg egy egyetlen domináns kiemelési entitást tartalmaz.

Entitások közé tartozik például az előadók, actors, elnyert érmek számának trendjeit, modellek jól ismert személyiséghez.; helyek és tereptárgyak felismerése, például a csatlakoztatási Rudolf vagy Lincoln emlékművet; és többek között az banán, goldendoodle, könyv vagy film címét. A [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) mező tartalmazza, amelyek azonosítják az entitástípus mutatók. Például ha egy személy, filmet, szolgáltatást vagy a vonzás. Lehetséges típusainak listáját lásd: [entitástípusok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Az alábbiakban látható egy választ, amely meghatározó és -egyértelműsítéssel entitást tartalmaz.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "http://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Az entitás tartalmaz egy `name`, `description`, és `image` mező. Ezek a mezők a felhasználói élmény jeleníti meg, amikor attribútum kell őket. A `contractualRules` mező, telepítenie kell az adatok listáját tartalmazza. Az általános szabály azonosítja a mezőt, amely a tesznek elérhetővé; ilyenek vonatkozik. Tesznek elérhetővé; ilyenek alkalmazásával kapcsolatos további információkért lásd: [tesznek elérhetővé; ilyenek](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "http://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Az entitásadatokat (név, leírás és image) megjelenítésekor az URL-címet is használnia kell a `webSearchUrl` mező segítségével a Bing keresési eredmények oldal, amely az entitás tartalmaz.


A `places` mező egy [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) listáját tartalmazó objektum [helyen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objektumok (lásd a `value` mezőben). A lista tartalmaz egy vagy több helyi entitások, amelyek megfelelnek a kérelmet.

Helyek éttermi, a "Hotels" vagy a helyi üzletek tartalmazza. A [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) mező tartalmazza, amelyek azonosítják a helyi entitástípus mutatók. A listán például hely, LocalBusiness, éttermi mutatók listáját tartalmazza. A tömb minden egyes egymást követő mutató szűkíthető az entitás típusa. Lehetséges típusainak listáját lásd: [entitástípusok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Entitás válaszok támogatja több piacokon, de a helyek válasz csak az USA üzleti helyek támogatja. 

Helyi figyelembe entitás lekérdezések: *éttermek a közelben* szükség pontos eredményeket a felhasználó földrajzi helye. A kérések mindig használjon a hely keresése X és az X-MSEdge-Ügyfélip fejlécet adja meg a felhasználó helyét. A Bing fenyegetésként észlel, a lekérdezés a felhasználó földrajzi helye előnyös, ha beállítja a `askUserForLocation` mezőjében [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) való **igaz**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Egy helyen eredményt a hely nevét, címét, telefonszámát és URL-cím tartalmazza az entitás webhelyre. Az entitásadatokat megjelenítésekor az URL-címet is használnia kell a `webSearchUrl` mező segítségével a Bing keresési eredmények oldal, amely az entitás tartalmaz.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Vagy egy harmadik féltől származó, az Ön nevében, előfordulhat, hogy nem használja, megőrzése, tárolásához, gyorsítótár, oszt meg, vagy a tesztelés, fejlesztés, képzés, terjesztése vagy minden nem Microsoft-szolgáltatás elérhetővé tétele céljából az entitások API-ból adatokat terjeszteni vagy szolgáltatás.  

## <a name="data-attribution"></a>Adatokat tesznek elérhetővé; ilyenek.

A Bing Entity API válaszok harmadik fél által birtokolt információkat tartalmaznak. Ön felelőssége annak biztosítása érdekében, használatára megfelelő, például úgy, hogy megfelel a creative commons licencet a felhasználói élmény támaszkodhat.

Ha egy válasz vagy az eredmény tartalmazza a `contractualRules`, `attributions`, vagy `provider` mezők, meg kell attribútum az adatokat. Ha a válasz nem tartalmazza a mezőt, nem tesznek elérhetővé; ilyenek nem szükséges. Ha az eredmény tartalmazza a `contractualRules` mezőt és a `attributions` és/vagy `provider` mezőket, szerződéses szabályok segítségével attribútum az adatokat.

Az alábbi példa bemutatja egy olyan entitás, amely tartalmaz egy MediaAttribution szerződéses szabály és a egy rendszerképet, amely tartalmazza a `provider` mező. A MediaAttribution szabály azonosítja a kép a szabály céljaként, így lenne, figyelmen kívül a lemezkép `provider` mező, ezért használja inkább a MediaAttribution szabály tesznek elérhetővé; ilyenek biztosít.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Ha egy szerződéses szabályt tartalmaz a `targetPropertyName` mező, a szabály vonatkozik csak a célként megadott mezőben. Ellenkező esetben a szabály vonatkozik-e a szülői objektumot, amely tartalmazza a `contractualRules` mező.

A következő példában a `LinkAttribution` szabály tartalmazza a `targetPropertyName` mező, ezért a szabály vonatkozik a `description` mező. Bizonyos mezők alkalmazó szabályok meg kell adnia egy követő a meghatározott adatok a szolgáltató webhelyéről mutató hivatkozást tartalmazó sor. Az attribútum a leírást, például egy vonal azonnal ebben az esetben a leíró szöveg, amely tartalmaz egy hivatkozást az adatok a szolgáltató webhelyéről, a következő létrehozása a contoso.com mutató hivatkozást.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Licenc megnevezése

Ha szerződéses szabályok listája tartalmaz egy [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) szabály, meg kell jelenítenie a hirdetmény a sor következett közvetlenül a tartalmat, amely a licenc vonatkozik. A `LicenseAttribution` szabályt használ a `targetPropertyName` mező segítségével azonosítja a tulajdonságot, amely a licenc vonatkozik.

A következő látható egy példa, amely magában foglalja egy `LicenseAttribution` szabály.

![Licenc megnevezése](./media/cognitive-services-bing-entities-api/licenseattribution.png)

A licenc figyelje meg, hogy jelenítjük meg azt a webhelyet, a licenc információkat tartalmaz egy hivatkozást kell tartalmaznia. Általában akkor győződjön meg arról, a licenc neve hivatkozás. Például, ha az értesítés **CC biztonsági Társítás licenc szöveg** és CC biztonsági Társítás a licenc neve, akkor biztosítja, CC biztonsági Társítás hivatkozás.

### <a name="link-and-text-attribution"></a>Csatolás és a szöveges megnevezése

A [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) és [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) szabályok általában az adatok a szolgáltató azonosítására szolgál. A `targetPropertyName` mező azonosítja a mezőt, amely a szabály vonatkozik.

Az attribútum a szolgáltatók, vegyen fel egy közvetlenül követő tartalmat a alkalmazni az adatok (például a megcélzott mezőhöz). A sor egyértelműen feliratú jelzi, hogy a szolgáltatók a következők: az adatok forrását. Például "adatait: contoso.com". A `LinkAttribution` szabályok, létre kell hoznia egy hivatkozást a szolgáltató webhelyéről.

Az alábbi példa, amely tartalmazza az `LinkAttribution` és `TextAttribution` szabályokat.

![Hivatkozás szövege megnevezése](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Media megnevezése

Ha az entitás tartalmaz egy képet, és megjeleníti azt, meg kell adnia a szolgáltató webhelyéről átkattintásos mutat. Ha az entitás tartalmaz egy [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) szabály, használja a szabály URL-címet az átkattintásos hivatkozást szeretne létrehozni. Ellenkező esetben használja az URL-címet, a lemezkép részeként történő `provider` mező az átkattintásos hivatkozást szeretne létrehozni.

A következő látható egy példa, amely tartalmaz egy képet `provider` mező, és szerződéses szabályokat. Mivel a példa tartalmazza az általános szabály, akkor figyelmen kívül a lemezkép `provider` mezőben, majd a alkalmazni a `MediaAttribution` szabály.

![Media megnevezése](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Keresés vagy a search-hez hasonló felhasználói élményt

Csakúgy, mint a Bing Web Search API a Bing Entity Search API csak használhatók egy közvetlen felhasználói lekérdezés vagy a keresési, vagy egy alkalmazás vagy környezetet biztosít, amely logikailag egy felhasználói keresési kérelmek úgy művelet eredményeként. Illusztrációs célok esetében az alábbiakban néhány példa a elfogadható search vagy hasonló keresési élményt.

- Felhasználó ad meg egy lekérdezést közvetlenül a keresőmezőbe az alkalmazás
- A megadott szöveg vagy kép és a kérelem "További információ" vagy "További információk" a felhasználó kiválaszt
- Felhasználói keresési robotprogramok kéri egy adott témakör
- Egy adott objektum vagy egy vizuális keresési típus forgatókönyvben entitás dwells felhasználó

Ha nem biztos benne, hogy a felhasználói élmény olyan keresési-szerű környezetet lehessen venni, javasoljuk, hogy ellenőrizze a Microsofttal.

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Gyorsan megkezdheti az első kérelmét, lásd: [tétele az első kérelem](./quick-start.md).

Ismerje meg az a [Bing Entity Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) hivatkozást. A hivatkozás a fejlécek és lekérdezési paraméterek, amellyel keresési eredmények kérelem tartalmazza. és a válaszobjektumok definícióit is megtalálja benne. 

A keresőmező felhasználói élményének fejlesztésére vonatkozó további tudnivalókért tekintse át a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) ismertetését. Ahogy a felhasználó megadja a lekérdezési kifejezést, meghívhatja ezt az API-t, hogy mások által használt kapcsolódó lekérdezési kifejezéseket jelenítsen meg.

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./use-display-requirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.