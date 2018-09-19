---
title: Translator Text API nyelvek metódus
titlesuffix: Azure Cognitive Services
description: A Translator Text API nyelvek módszert használja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 51f15bd9c75f24be0d477d10de55c93a51cfbf3f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129641"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: nyelv

A készlet más műveletek, a Translator Text API által jelenleg támogatott nyelvek beolvasása. 

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `GET` kérelmet:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A kérelem lekérdezési karakterláncot az átadott paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az érték lehet `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Nem kötelező paraméter*.<br/>Meghatározása a csoporthoz való visszatéréshez nyelveken neveket vesszővel tagolt listája. Engedélyezett a csoport neve: `translation`, `transliteration` és `dictionary`. Ha nincs hatóköre van megadva, akkor az összes csoport ad vissza, amely 03T00 megadásának `scope=translation,transliteration,dictionary`. Annak eldöntéséhez, hogy a támogatott nyelvek körét az adott forgatókönyvnek megfelelő, tekintse meg a leírását az [Válaszobjektum](#response-body).</td>
  </tr>
</table> 

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Fogadja el nyelv</td>
    <td>*Nem kötelező fejléc*.<br/>A felhasználói felület karakterláncokat használni kívánt nyelvet. A mezők a válaszban szereplő néhány nyelvek nevei vagy a régiók neveit. Használja ezt a paramétert, amelyben a rendszer ezeket a neveket adott vissza a nyelvet határozza meg. A nyelv azáltal, hogy megfelelően formázott BCP-47 nyelvi címke van megadva. Például használja az értéket `fr` kérése nevek francia nyelvű, vagy használja az értéket `zh-Hant` hagyományos kínai kérelem nevek.<br/>Nevek angol nyelven szerepelnek, ha nincs megadva a cél nyelvet, vagy ha a honosítás nem érhető el.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Nem kötelező fejléc*.<br/>Egy ügyfél által létrehozott GUID egyedi azonosítására szolgál a kérelmet.</td>
  </tr>
</table> 

Hitelesítési nyelvi erőforrások nem szükséges.

## <a name="response-body"></a>Választörzs

Ügyfél használ a `scope` lekérdezési paraméter meghatározásához, hogy mely csoportok nyelvek azt van érdekelné.

* `scope=translation` Itt a lefordítandó szöveg egyik nyelvről egy másik nyelvet; a támogatott nyelvek

* `scope=transliteration` funkciói lehetővé teszik egy parancsfájlból származó egyik nyelven írt szöveg átalakítása egy másik parancsprogramra;

* `scope=dictionary` nyelvi párok biztosít, amelynek `Dictionary` operatív adatokat adja vissza.

Egy ügyfél lehet, hogy több csoportjainak lekérdezésére egyszerre adja meg a neveket vesszővel tagolt listája. Ha például `scope=translation,transliteration,dictionary` adna vissza minden támogatott nyelven.

A sikeres válasz egy JSON-objektum egy tulajdonsággal kért csoportonkénti:

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

Minden egyes tulajdonság értéke a következőképpen.

* `translation` A tulajdonság

  Értékét a `translation` tulajdonság értéke egy szótár (kulcs, érték) párok. Minden egyes kulcs a BCP-47 nyelvi címke. A kulcs azonosítja, amelynek szöveget a lefordított vagy a lefordított nyelvet. A kulcshoz tartozó érték leíró nyelv tulajdonságokkal rendelkező JSON-objektum:

  * `name`: A területi beállításai szerint keresztül kért nyelv megjelenített név `Accept-Language` fejléc.

  * `nativeName`: A nyelv nevének megjelenítése a natív ezt a nyelvet a területi beállításai szerint.

  * `dir`: Írásmód, amely `rtl` jobbról balra író nyelvek vagy `ltr` jobbról balra író nyelvek.

  A következő egy példa:
          
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

* `transliteration` A tulajdonság

  Értékét a `transliteration` tulajdonság értéke egy szótár (kulcs, érték) párok. Minden egyes kulcs a BCP-47 nyelvi címke. A kulcs azonosítja, amelynek szöveget lehet alakítani egy parancsfájlból származó egy másik parancsprogramra nyelvet. A kulcshoz tartozó érték a nyelv és a támogatott szkriptek leíró tulajdonságokkal rendelkező JSON-objektum:

  * `name`: A területi beállításai szerint keresztül kért nyelv megjelenített név `Accept-Language` fejléc.

  * `nativeName`: A nyelv nevének megjelenítése a natív ezt a nyelvet a területi beállításai szerint.

  * `scripts`: A próbaverzióról parancsfájlok listája. Egyes elemeinek a `scripts` lista tulajdonságokkal rendelkezik:

    * `code`: A szkriptet azonosító kód.

    * `name`: A parancsfájl-n keresztül kérte a területi megjelenített név `Accept-Language` fejléc.

    * `nativeName`: A nyelv nevének megjelenítése nyelv natív területi.

    * `dir`: Írásmód, amely `rtl` jobbról balra író nyelvek vagy `ltr` jobbról balra író nyelvek.

    * `toScripts`: A szöveg konvertálása érhető el parancsprogram listája. Egyes elemeinek a `toScripts` lista tulajdonságokkal rendelkezik `code`, `name`, `nativeName`, és `dir` korábban leírtaknak megfelelően.

  A következő egy példa:

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

* `dictionary` A tulajdonság

  Értékét a `dictionary` tulajdonság értéke egy szótár (kulcs, érték) párok. Minden egyes kulcs a BCP-47 nyelvi címke. A kulcs azonosítja egy nyelvet, amely felkínálásával és vissza – fordítások érhetők el. Az érték a következő JSON-objektum, amely leírja a Forrásnyelv és a fordításokat és a cél nyelvek:

  * `name`: A Forrásnyelv keresztül kérte a területi megjelenítendő nevét `Accept-Language` fejléc.

  * `nativeName`: A nyelv nevének megjelenítése a natív ezt a nyelvet a területi beállításai szerint.

  * `dir`: Írásmód, amely `rtl` jobbról balra író nyelvek vagy `ltr` jobbról balra író nyelvek.

  * `translations`: Az alternatív fordítások és példák a lekérdezés a Forrásnyelv kifejezett nyelvek listáját. Egyes elemeinek a `translations` lista tulajdonságokkal rendelkezik:

    * `name`: A Célnyelv keresztül kérte a területi megjelenítendő nevét `Accept-Language` fejléc.

    * `nativeName`: Natív a célként megadott nyelvhez tartozó területi a célként megadott nyelv nevének megjelenítése.

    * `dir`: Írásmód, amely `rtl` jobbról balra író nyelvek vagy `ltr` jobbról balra író nyelvek.
    
    * `code`: A Célnyelv azonosítása nyelv kódja.

  A következő egy példa:

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

A válasz objektum szerkezete nem változik az API-verzió megváltoztatása nélkül. Az API-t ugyanazon verzióját, az elérhető nyelvek listájának idővel változhat, mert a Microsoft Translator folyamatosan bővíti a szolgáltatás által támogatott nyelvek listáját.

A támogatott nyelvek listája nem változik gyakran. Spórolhat a sávszélességgel, és növelni a válaszkészséget, az ügyfélalkalmazások vegye nyelvi erőforrások és a megfelelő entitáscímkéje gyorsítótárazás (`ETag`). Ezt követően az ügyfélalkalmazás is rendszeres időközönként (például az 24 óránként) le a szolgáltatást a legújabb készletét a támogatott nyelvek beolvasása. Az aktuális passing `ETag` értékét egy `If-None-Match` fejlécmezőt lehetővé teszi a válasz optimalizálása érdekében a szolgáltatás. Az erőforrás nem lett módosítva, ha a szolgáltatás visszaadja a 304-es állapotkód és a egy üres válasz törzsében.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>ETag</td>
    <td>A támogatott nyelvek a kért csoportok entitáscímkéje aktuális értéke. Ahhoz, hogy hatékonyabb szeretnének hozzáférni, az ügyfél küldhet a `ETag` értékét egy `If-None-Match` fejlécmezőt.
    </td>
  </tr>
  <tr>
    <td>X-RequestId:</td>
    <td>A kérelem azonosíthatja a szolgáltatás által létrehozott értéket. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Állapotkódok

Az alábbi táblázat a lehetséges HTTP-állapotkódok, amely egy kérés adja vissza. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Az erőforrás nem lett módosítva a kérelem fejlécében megadott verzió óta `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Javítsa ki a kérelem paramétereinek újrapróbálkozás előtt.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító `X-RequestId`, és az ügyfél-azonosítója a fejléc `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Ismételje meg a kérelmet. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító `X-RequestId`, és az ügyfél-azonosítója a fejléc `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan szövegfordítás támogatott nyelvek beolvasása.

# <a name="curltabcurl"></a>[A curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
