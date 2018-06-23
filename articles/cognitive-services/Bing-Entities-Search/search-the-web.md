---
title: Mi az a Bing entitás keresési? | Microsoft Docs
description: Megtudhatja, hogyan kereshet a weben entitásokat és a helyek entitás a Bing keresési API segítségével.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: f1b87c07d5b56307fd6b3fc68999598aeab6eb82
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349215"
---
# <a name="what-is-bing-entity-search"></a>Mi az a Bing entitás keresési?

Az entitás Bing keresési API keresési lekérdezést küld a Bing, és lekérdezi az eredmények, amelyek tartalmazzák az entitásokat és helyek. Hely eredmények tartalmazzák a éttermekben, Szálloda vagy más helyi vállalatok számára. Bing helyen adja vissza, ha a lekérdezés a helyi vállalati nevét adja meg, vagy olyan üzleti (például a közeli éttermekben) típusú kér. Bing entitásokat ad vissza, ha a lekérdezés a jól ismert személyek, helyek (sport létesítmények, állapotok, országok, stb.) vagy dolgot határoz meg.

## <a name="suggesting--using-search-terms"></a>Javasolhat & keresési feltételek használatával

Ha megad egy keresőmezőt, ahol a felhasználó megadja a kívánt keresőkifejezést, használja a [Bing automatikus kiegészítési API](../bing-autosuggest/get-suggested-search-terms.md) élményének növelése érdekében. Az API-t adja vissza a javasolt lekérdezési karakterláncok a felhasználótípusokat részleges keresési feltételek alapján.

Után a felhasználó adja meg a kívánt keresőkifejezést, URL-cím kódolása kifejezés beállítása előtt a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) lekérdezési paraméter. Például, ha a felhasználó megadja *Marcus Appel*, beállíthatja `q` való *Marcus + Appel* vagy *Marcus % 20Appel*.

Ha a keresési kifejezés helyesen adta-e hibát tartalmaz, a keresési válasz tartalmazza a [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) objektum. Az objektum az eredeti helyesírást és a javított helyesírás, amely a kereséshez használt Bing jeleníti meg.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>A kért entitások

Egy példa egy kérelem, lásd: [első kérés a](./quick-start.md).

## <a name="the-response"></a>A válasz

A válasz tartalmazza a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objektum. Bing talál egy entitás vagy a hely, fontos, ha az objektum tartalmazza a `entities` mezőben `places` mező, vagy mindkettőt. Ellenkező esetben a válasz objektum nem tartalmaz vagy mező.

A `entities` mező egy [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) listáját tartalmazó objektum [entitás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) objektumok (lásd a `value` mező). A lista is tartalmazhat, egyetlen meghatározó entitás, entitásokat Egyértelműsítő vagy mindkettőt. 

Egy meghatározó olyan entitás, amely a Bing úgy véli, hogy az egyetlen entitás, amely eleget tesz a kérelem (nincs nem egyértelműek, hogy mely entitás eleget tesz a kérelem). Több entitás sikerült teljesíteni a kérést, ha a listában több Egyértelműsítő entitást. Például ha a kérelem egy movie franchise általános címe használ, a listán, valószínűleg Egyértelműsítő entitásokat tartalmaz. De a kérelem egy konkrét cím és a franchise határozza meg, ha a listán, valószínűleg egy egyetlen meghatározó entitást tartalmaz.

Entitások közé tartoznak a jól ismert személyiséghez például előadók, szereplője, athletes, modellek stb.; helyek és jellegzetes hely például csatlakoztatási Rudolf vagy Lincoln emlékművet; és műveleteket, mint az banánbehozatali, goldendoodle, könyv vagy movie címét. A [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) mező tartalmazza, amelyek azonosítják az entitástípus mutatók. Ha például egy személy, movie, állat vagy vonzás. A lehetséges típusainak listáját lásd: [entitástípusok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Az alábbiakban látható egy választ, amely egy meghatározó és Egyértelműsítő entitást tartalmaz.

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

Az entitás tartalmaz egy `name`, `description`, és `image` mező. Ezeket a mezőket a felhasználói élmény jeleníti meg, amikor attribútumot kell őket. A `contractualRules` mező, telepítenie kell az adatok listáját tartalmazza. A szerződési szabály azonosítja a mezőt, amely a attribútumára vonatkozik. Attribútumára alkalmazásával kapcsolatos információkért lásd: [attribútumára](#data-attribution).

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

Az entitás információk (nevét, leírását és kép) megjelenítésekor is használnia kell az URL-címet a `webSearchUrl` mező összekapcsolása a Bing keresési eredmények entitást tartalmazó lap.


A `places` mező egy [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) listáját tartalmazó objektum [hely](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objektumok (lásd a `value` mező). A lista tartalmaz egy vagy több helyi entitások teljesíteni a kérést.

Forráshelyek közé tartoznak a éttermi, a szállodák vagy a helyi vállalatok számára. A [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) mező tartalmazza, amelyek azonosítják a helyi entitástípus mutatók. A listán például hely, LocalBusiness, éttermi mutatók listáját tartalmazza. A tömb egyes egymást követő mutató típusra van szűkítve a entity Type típusként. A lehetséges típusainak listáját lásd: [entitástípusok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```

Helyi kompatibilis entitás lekérdezése például *közeli éttermi* szükség arra, hogy pontos eredményeket a felhasználó földrajzi helye. A kérelmek mindig használjon a X-keresési-hely és az X-MSEdge-Ügyfélip fejlécek adhatja meg a felhasználó földrajzi helye. Bing úgy értelmezi, a lekérdezés a felhasználó földrajzi helye előnyös, ha beállítja a `askUserForLocation` mezőjében [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) való **igaz**. 

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

Hely eredményt tartalmazza a hely nevét, címét, telefonszámát és URL-cím az entitás webhelyre. Az entitás megjelenítheti, ha az URL-címet is használni kell a `webSearchUrl` mező összekapcsolása a Bing keresési eredmények entitást tartalmazó lap.

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
> Vagy egy harmadik féltől származó az Ön nevében, előfordulhat, hogy nem használja, megőrizni, tárolásához, gyorsítótár, megosztásához vagy terjesztése a entitások API tesztelése, fejleszt, betanítása, terjesztése vagy elérhetővé teszi a nem Microsoft-szolgáltatás adatokat vagy szolgáltatás.  

## <a name="data-attribution"></a>Adatok attribútumára

Bing entitás API válaszok harmadik felek által birtokolt információkat tartalmaznak. Az Ön felelőssége annak biztosítása érdekében használata megfelelő, például kreatív commons licenc támaszkodhat a felhasználói élmény mellett.

Ha egy válasz, vagy az eredmény tartalmazza a `contractualRules`, `attributions`, vagy `provider` mezők, az adatok kell attribútum. Ha a válasz nem tartalmazhatja a következő mezők, nem attribútumára megadása kötelező. Ha a válaszfájl tartalmazza a `contractualRules` mező és a `attributions` és/vagy `provider` mezők, kell használnia a szerződéses szabályok attribútum az adatokat.

A következő példa bemutatja egy entitás, amely tartalmazza egy MediaAttribution szerződéses szabályt és egy olyanra, amely magában foglalja a `provider` mező. A MediaAttribution szabály a szabály céljaként azonosítja a lemezképet, akkor figyelmen kívül a lemezkép operációs rendszerének `provider` mezőben, és inkább a MediaAttribution szabály attribútumára biztosításához.  

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

Ha egy szerződéses szabályt tartalmaz a `targetPropertyName` mezőt, a szabály vonatkozik csak a megcélzott mező. Ellenkező esetben a szabály vonatkozik-e a szülői objektumot, amely tartalmazza a `contractualRules` mező.

A következő példában a `LinkAttribution` szabályt tartalmaz a `targetPropertyName` mezőt, így a szabálynak az alkalmazása a `description` mező. Ez a szabály adott mezők vonatkozik meg kell adni a célként megadott adatokat, a szolgáltató webhelyéről mutató hivatkozást tartalmazó követő egy sor. Az attribútum a leírás, például egy sor azonnal ebben az esetben az adatokat a szolgáltató webhelyéről, a hivatkozást tartalmazó leírásának szövege a következő hozzon létre egy contoso.com hivatkozást.

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

### <a name="license-attribution"></a>Licenc attribútumára

Ha a szerződéses szabályok listáját tartalmazza egy [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) szabály, meg kell jelenítenie a hirdetmény a sor azonnal követően a tartalom, amelyre a licencszerződés vonatkozik. A `LicenseAttribution` szabályt használ a `targetPropertyName` mező azonosíthassa a tulajdonság, amelyre a licencszerződés vonatkozik.

A következő szemléltet, amely tartalmazza a `LicenseAttribution` szabály.

![Licenc attribútumára](./media/cognitive-services-bing-entities-api/licenseattribution.png)

A licenc hirdetmény megjelenített tartalmaznia kell a licenc információkat tartalmazó webhely mutató hivatkozás. Általában akkor teheti a nevet a licenc hivatkozás. Például, ha a nyilatkozat **CC biztonsági Társítás licence szöveg** és CC biztonsági Társítás a nevét, a licenc, akkor tenné CC biztonsági Társítás hivatkozás.

### <a name="link-and-text-attribution"></a>Csatolás és a szöveges attribútumára

A [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) és [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) szabályok általában a szolgáltató az adatok azonosítására szolgál. A `targetPropertyName` mező azonosítja a mezőt, amelyekre a szabály vonatkozik.

A szolgáltatók attribútuma, vegyen fel egy azonnal követően a tartalmat, amelyek érvényesek a megjelölését (például a célként megadott mező). A sor egyértelműen feliratú annak jelzésére, hogy a szolgáltatók a következők: az adatok forrását. Például "adatait: contoso.com". A `LinkAttribution` szabályok, létre kell hoznia a szolgáltató webhelyéről mutató hivatkozás.

A következő szemléltet, amely tartalmazza az `LinkAttribution` és `TextAttribution` szabályok.

![Hivatkozás szövege attribútumára](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Media attribútumára

Ha az entitást tartalmaz egy képet, és megjeleníti azt, meg kell adnia egy kattintások hivatkozást a szolgáltató webhelyéről. Ha az entitást tartalmaz egy [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) szabály, a szabály URL-CÍMÉT használja a kattintások kapcsolat létrehozásához. Ellenkező esetben használja az URL-cím szerepel a lemezkép operációs rendszerének `provider` mező a kattintások kapcsolat létrehozásához.

A következő példa egy példa, amely tartalmaz egy képet `provider` mező és szerződéses szabályokat. Mivel a példa a szerződéses szabályt tartalmaz, akkor figyelmen kívül a lemezkép operációs rendszerének `provider` mezőben, majd alkalmazza a `MediaAttribution` szabály.

![Media attribútumára](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Keresési vagy keresési hasonló élmény

Csakúgy, mint a Bing webes keresési API-val entitás a Bing keresési API csak használható egy közvetlen felhasználó lekérdezés vagy a keresési, vagy miatt egy művelet egy alkalmazás vagy logikailag értelmezhető keresési kérelem a felhasználó élmény belül. Illusztrációs célokat szolgálnak az alábbiakban néhány példát elfogadható keresési vagy keresési hasonló lép.

- Felhasználó ad meg egy lekérdezést közvetlenül a keresőmezőbe az alkalmazásban
- Adott szöveg vagy a kép és a kérések "További információ" vagy a "További információk" a felhasználó választ
- Felhasználó keresése bot kéri egy adott témakör
- Felhasználói dwells egy adott objektum vagy egy entitás visual keresési típus esetén

Ha nem biztos abban, ha a felhasználói élmény tekinthetők a keresési hasonló felhasználói élményt nyújtja, javasoljuk, hogy ellenőrizze a Microsoft.

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Használatának gyors megkezdése az első kérelem, lásd: [az első kérelem végrehajtása](./quick-start.md).

Ismerje meg, a [Bing entitás Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) hivatkozás. A hivatkozás a fejlécek és a keresési eredmények kéréséhez használt lekérdezési paramétereket tartalmaz. A válasz objektumok meghatározását is tartalmaz. 

A keresési mezőbe felhasználói élmény javítása érdekében tekintse meg a [Bing automatikus kiegészítési API](../bing-autosuggest/get-suggested-search-terms.md). A felhasználó adja meg a lekérdezési kifejezésre, mert hívása az API vonatkozó lekérdezés feltételeket, mások által használt eléréséhez.

Olvassa el [Bing használatát és a megjelenített követelmények](./use-display-requirements.md) , nem megszakítja a keresési eredmények használatára vonatkozó szabályok egyike.