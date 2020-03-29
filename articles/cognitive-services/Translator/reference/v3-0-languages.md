---
title: Fordítószöveg API-nyelvek módszer
titleSuffix: Azure Cognitive Services
description: A Nyelvek metódus leteszi a Translator Text API más műveletei által jelenleg támogatott nyelvek készletét.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835831"
---
# <a name="translator-text-api-30-languages"></a>Fordító szöveg API 3.0: Nyelvek

Leidegenszerzi a Translator Text API más műveletei által jelenleg támogatott nyelvek készletét. 

## <a name="request-url"></a>Kérés URL-címe

Kérés `GET` küldése a következő knek:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Kérelem paraméterei

A lekérdezési karakterláncon átadott kérelemparaméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td><em>Kötelező paraméter</em>.<br/>Az ügyfél által kért API verziója. Az értéknek kell lennie. `3.0`</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Nem kötelező paraméter*.<br/>A visszaadandó nyelvek csoportját meghatározó, vesszővel tagolt névlista. Az engedélyezett csoportnevek `transliteration` `dictionary`a következők: `translation`, és . Ha nincs megadva hatókör, akkor az összes csoportot `scope=translation,transliteration,dictionary`visszaadja, ami egyenértékű a továbbadásával. Annak eldöntéséhez, hogy mely támogatott nyelvek felelnek meg a forgatókönyvnek, tekintse meg a [válaszobjektum leírását.](#response-body)</td>
  </tr>
</table> 

A kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Választható kérelemfejléc*.<br/>A felhasználói felület sztringjeihez használni kívánt nyelv. A válasz mezői közül néhány a nyelvek vagy a régiók neve. Ezzel a paraméterrel határozhatja meg a nevek visszaadásának nyelvét. A nyelvet egy jól formázott BCP 47 nyelvi címke megadásával adja meg. Használja például az `fr` értéket a francia nevek `zh-Hant` kérelmezéséhez, vagy az érték használatával a hagyományos kínai nyelvben.<br/>A nevek angol nyelven vannak megadva, ha nincs megadva célnyelv, vagy ha a honosítás nem érhető el.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Választható kérelemfejléc*.<br/>Az ügyfél által létrehozott GUID a kérelem egyedi azonosítására.</td>
  </tr>
</table> 

A nyelvi erőforrások lekért hitelesítése nem szükséges.

## <a name="response-body"></a>Választörzs

Az ügyfél `scope` a lekérdezési paraméter segítségével határozza meg, hogy mely nyelvcsoportok érdeklik.

* `scope=translation`támogatott nyelveket biztosít a szöveg egyik nyelvről a másikra történő lefordításához;

* `scope=transliteration`lehetővé teszi a szöveg egyik nyelven történő konvertálását egyik parancsfájlból a másikba;

* `scope=dictionary`olyan nyelvpárokat `Dictionary` biztosít, amelyekhez a műveletek adatokat adnak vissza.

Az ügyfél egyszerre több csoportot is bekérhet, ha vesszővel tagolt névlistát ad meg. Például `scope=translation,transliteration,dictionary` az összes csoport támogatott nyelveit visszaadná.

A sikeres válasz egy JSON-objektum, amely minden egyes kért csoporthoz egy tulajdonságot kap:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Az egyes tulajdonok értéke a következő.

* `translation`Tulajdonság

  A `translation` tulajdonság értéke a (kulcs, érték) párok szótára. Minden kulcs egy BCP 47 nyelvi címke. A kulcs azonosítja azt a nyelvet, amelyre a szöveg lefordítható vagy abból fordítható. A kulcshoz társított érték egy JSON-objektum, amelynek tulajdonságai leírják a nyelvet:

  * `name`: A nyelv megjelenítendő neve `Accept-Language` a fejlécen keresztül kért területi beállításban.

  * `nativeName`: A nyelv megjelenítendő neve a nyelv hez használt honos nyelv területi beállításában.

  * `dir`: Irányítottság, amely `rtl` jobbról balra író `ltr` nyelvekre vagy balról jobbra író nyelvekre szól.

  Egy példa a következő:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration`Tulajdonság

  A `transliteration` tulajdonság értéke a (kulcs, érték) párok szótára. Minden kulcs egy BCP 47 nyelvi címke. A kulcs azonosítja azt a nyelvet, amelyhez a szöveg egyik parancsfájlból egy másik parancsfájlba konvertálható. A kulcshoz társított érték egy JSON-objektum, amelynek tulajdonságai leírják a nyelvet és a támogatott parancsfájlokat:

  * `name`: A nyelv megjelenítendő neve `Accept-Language` a fejlécen keresztül kért területi beállításban.

  * `nativeName`: A nyelv megjelenítendő neve a nyelv hez használt honos nyelv területi beállításában.

  * `scripts`: A konvertálandó parancsfájlok listája. A `scripts` lista minden eleme rendelkezik tulajdonságokkal:

    * `code`: A parancsfájlt azonosító kód.

    * `name`: A parancsfájl neve megjelenítése a `Accept-Language` fejlécen keresztül kért területi beállításban.

    * `nativeName`: A nyelv megjelenítendő neve a nyelv natív nyelvén.

    * `dir`: Irányítottság, amely `rtl` jobbról balra író `ltr` nyelvekre vagy balról jobbra író nyelvekre szól.

    * `toScripts`: A szöveg átalakításához rendelkezésre álló parancsfájlok listája. A lista `toScripts` minden elemének `name` `nativeName`tulajdonságai `dir` `code`, , , és a korábban leírtak szerint.

  Egy példa a következő:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary`Tulajdonság

  A `dictionary` tulajdonság értéke a (kulcs, érték) párok szótára. Minden kulcs egy BCP 47 nyelvi címke. A kulcs azonosítja azt a nyelvet, amelyhez alternatív fordítások és visszafordítások állnak rendelkezésre. Az érték egy JSON-objektum, amely a forrásnyelvet és a rendelkezésre álló fordításokat tartalmazó célnyelveket írja le:

  * `name`: A forrásnyelv megjelenítendő neve `Accept-Language` a fejlécen keresztül kért területi beállításban.

  * `nativeName`: A nyelv megjelenítendő neve a nyelv hez használt honos nyelv területi beállításában.

  * `dir`: Irányítottság, amely `rtl` jobbról balra író `ltr` nyelvekre vagy balról jobbra író nyelvekre szól.

  * `translations`: A változó fordításokat tartalmazó nyelvek listája és a forrásnyelven kifejezett lekérdezés példái. A `translations` lista minden eleme rendelkezik tulajdonságokkal:

    * `name`: A célnyelv megjelenítendő neve `Accept-Language` a fejlécen keresztül kért területi beállításban.

    * `nativeName`: A célnyelv megjelenítendő neve a célnyelv natív nyelvén.

    * `dir`: Irányítottság, amely `rtl` jobbról balra író `ltr` nyelvekre vagy balról jobbra író nyelvekre szól.
    
    * `code`: A célnyelvet azonosító nyelvkód.

  Egy példa a következő:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

A válaszobjektum szerkezete nem változik az API verziójának módosítása nélkül. Az API ugyanazon verziójának esetében az elérhető nyelvek listája idővel változhat, mivel a Microsoft Translator folyamatosan bővíti a szolgáltatásai által támogatott nyelvek listáját.

A támogatott nyelvek listája nem változik gyakran. A hálózati sávszélesség megtakarítása és a válaszidő javítása érdekében az ügyfélalkalmazásnak meg kell fontolnia a nyelvi erőforrások és a megfelelő entitáscímke ( gyorsítótárazását .`ETag`. Ezután az ügyfélalkalmazás rendszeres időközönként (például 24 óránként egyszer) lekérdezheti a szolgáltatást a támogatott nyelvek legújabb készletének lekéréséhez. Ha átadjuk az aktuális `ETag` értéket egy `If-None-Match` fejlécmezőben, a szolgáltatás optimalizálhatja a választ. Ha az erőforrás nem lett módosítva, a szolgáltatás 304-es állapotkódot és egy üres választörzset ad vissza.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Etag</td>
    <td>Az entitáscímke aktuális értéke a támogatott nyelvek kért csoportjaihoz. A későbbi kérések hatékonyabbá tétele `ETag` érdekében `If-None-Match` az ügyfél elküldheti az értéket egy fejlécmezőben.
    </td>
  </tr>
  <tr>
    <td>X-RequestId azonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására létrehozott érték. Hibaelhárítási célokra használják.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz állapotkódjai

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Az erőforrás nem módosult a kérelemfejlécek `If-None-Match`által megadott verzió óta.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa ki a kérelem paramétereit.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérést, mert az ügyfél túllépte a kérelemkorlátot.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: `X-RequestId`a hiba dátuma `X-ClientTraceId`és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálkozzon újra a kéréssel. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: `X-RequestId`a hiba dátuma `X-ClientTraceId`és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
</table> 

Ha hiba történik, a kérelem json-hibaválaszt is ad vissza. A hibakód egy 6 jegyű szám, amely a 3 jegyű HTTP-állapotkódot, majd egy 3 jegyű számot követ a hiba további kategorizálása érdekében. A gyakori hibakódok a [v3 Translator Text API referenciaoldalán](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

A következő példa bemutatja, hogyan lehet beolvasni a szövegfordításhoz támogatott nyelveket.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
