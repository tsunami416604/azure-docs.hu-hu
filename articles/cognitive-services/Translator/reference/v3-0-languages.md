---
title: Translator Text API languages metódus
titleSuffix: Azure Cognitive Services
description: A languages metódus a Translator Text API egyéb műveletei által jelenleg támogatott nyelveket olvassa be.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73835831"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3,0: nyelvek

A Translator Text API egyéb műveletei által jelenleg támogatott nyelvek készletének beolvasása. 

## <a name="request-url"></a>Kérés URL-címe

`GET` Kérelem küldése a következőnek:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td><em>Kötelező paraméter</em>.<br/>Az ügyfél által kért API-verzió. Az értéknek `3.0`a számnak kell lennie.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>Nem *kötelező paraméter*.<br/>A visszaadni kívánt nyelvek csoportját meghatározó nevek vesszővel tagolt listája. Az engedélyezett csoportok nevei a `translation`következők `transliteration` : `dictionary`és. Ha nincs megadva hatókör, a rendszer az összes csoportot visszaadja, ami egyenértékű az átadással `scope=translation,transliteration,dictionary`. Annak eldöntéséhez, hogy melyik támogatott nyelvek megfelelőek a forgatókönyvhöz, tekintse meg a [Válasz objektum](#response-body)leírását.</td>
  </tr>
</table> 

A kérelem fejlécei:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Accept-Language</td>
    <td>A *kérelem fejléce nem kötelező*.<br/>A felhasználói felület sztringjeihez használni kívánt nyelv. A válasz mezői a nyelvek nevei vagy a régiók nevei. Ezzel a paraméterrel határozhatja meg, hogy a rendszer milyen nyelven adja vissza ezeket a neveket. A nyelvet úgy kell megadni, hogy megfelelően formázott BCP 47 nyelvi címkét adjon meg. Például a nevek francia nyelven `fr` való meghívásához használja az értéket, vagy használja `zh-Hant` a nevet a hagyományos kínai nyelven.<br/>A nevek az angol nyelven jelennek meg, ha a célként megadott nyelv nincs megadva, vagy ha a honosítás nem érhető el.
    </td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td>A *kérelem fejléce nem kötelező*.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához.</td>
  </tr>
</table> 

A nyelvi erőforrások lekéréséhez nincs szükség hitelesítésre.

## <a name="response-body"></a>Választörzs

Az ügyfél a `scope` lekérdezési paraméter segítségével határozza meg, hogy mely nyelvekhez érdekli a szolgáltatás.

* `scope=translation`olyan nyelveket biztosít, amelyek támogatottak a szövegek egyik nyelvről egy másik nyelvre való fordításához.

* `scope=transliteration`a lehetővé teszi, hogy a szöveg átalakítása egy parancsfájlból egy másik parancsfájlba;

* `scope=dictionary`nyelvi párokat biztosít, `Dictionary` amelyek esetében az adatok visszaadása.

Egy ügyfél egyszerre több csoportot is beolvashat egy vesszővel tagolt nevek megadásával. Például `scope=translation,transliteration,dictionary` az összes csoport támogatott nyelveinek visszaadása.

A sikeres válasz egy JSON-objektum, amely egy tulajdonsággal rendelkezik az egyes kért csoportokhoz:

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

Az egyes tulajdonságok értéke a következő.

* `translation`tulajdonság

  A `translation` tulajdonság értéke (kulcs, érték) párok szótára. Az egyes kulcsok a BCP 47 nyelvi címkével rendelkeznek. A kulcs azonosítja azt a nyelvet, amelynek szövegét lefordíthatja vagy lefordíthatja. A kulcshoz társított érték egy JSON-objektum, amely a nyelvet leíró tulajdonságokkal rendelkezik:

  * `name`: A nyelvet a fejlécen keresztül `Accept-Language` kért területi beállításban jeleníti meg.

  * `nativeName`: Az adott nyelvhez tartozó natív területi beállítás nyelvének megjelenített neve.

  * `dir`: Írásirányát, amely `rtl` jobbról balra író nyelvekhez vagy `ltr` balról jobbra írt nyelvekhez használható.

  Példa:
          
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

* `transliteration`tulajdonság

  A `transliteration` tulajdonság értéke (kulcs, érték) párok szótára. Az egyes kulcsok a BCP 47 nyelvi címkével rendelkeznek. A kulcs azonosítja azt a nyelvet, amelynek szövegét át lehet alakítani egy parancsfájlból egy másikra. A kulcshoz társított érték egy JSON-objektum, amely a nyelvet és a támogatott parancsfájlokat leíró tulajdonságokkal rendelkezik:

  * `name`: A nyelvet a fejlécen keresztül `Accept-Language` kért területi beállításban jeleníti meg.

  * `nativeName`: Az adott nyelvhez tartozó natív területi beállítás nyelvének megjelenített neve.

  * `scripts`: A rendszerből konvertálandó parancsfájlok listája. A `scripts` lista egyes elemeinek tulajdonságai:

    * `code`: A szkriptet azonosító kód.

    * `name`: A (z) fejlécen keresztül `Accept-Language` kért területi beállításban szereplő parancsfájl megjelenítendő neve.

    * `nativeName`: A nyelv helyi natív nyelvének megjelenítendő neve.

    * `dir`: Írásirányát, amely `rtl` jobbról balra író nyelvekhez vagy `ltr` balról jobbra írt nyelvekhez használható.

    * `toScripts`: A szöveg szöveggé alakításához elérhető parancsfájlok listája. `toScripts` A lista egyes elemeinek tulajdonságai `code`, `name`, `nativeName`és `dir` a korábban leírtak szerint.

  Példa:

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

* `dictionary`tulajdonság

  A `dictionary` tulajdonság értéke (kulcs, érték) párok szótára. Az egyes kulcsok a BCP 47 nyelvi címkével rendelkeznek. A kulcs azonosítja azt a nyelvet, amellyel az alternatív fordítások és a háttér-fordítások elérhetők. Az érték egy JSON-objektum, amely leírja a forrás nyelvét és a megcélzott nyelveket az elérhető fordításokkal:

  * `name`: A forrás nyelvének megjelenített neve a fejlécen keresztül `Accept-Language` kért területi beállításban.

  * `nativeName`: Az adott nyelvhez tartozó natív területi beállítás nyelvének megjelenített neve.

  * `dir`: Írásirányát, amely `rtl` jobbról balra író nyelvekhez vagy `ltr` balról jobbra írt nyelvekhez használható.

  * `translations`: Azoknak a nyelveknek a listája, amelyeken a vértisztító fordítások és példák szerepelnek a forrás nyelvén kifejezett lekérdezéshez. A `translations` lista egyes elemeinek tulajdonságai:

    * `name`: A cél nyelvének megjelenítendő neve a fejlécen keresztül `Accept-Language` kért területi beállításban.

    * `nativeName`: A célnyelv megjelenítendő nyelvének megjelenített neve.

    * `dir`: Írásirányát, amely `rtl` jobbról balra író nyelvekhez vagy `ltr` balról jobbra írt nyelvekhez használható.
    
    * `code`: A célnyelv azonosítására szolgáló nyelvi kód.

  Példa:

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

A válasz objektum szerkezete nem változik az API verziójának módosítása nélkül. Az API ugyanazon verziójában az elérhető nyelvek listája idővel változhat, mivel a Microsoft Translator folyamatosan bővíti a szolgáltatásai által támogatott nyelvek listáját.

A támogatott nyelvek listája nem változik gyakran. Ha menteni szeretné a hálózati sávszélességet, és javítja a rugalmasságot, az ügyfélalkalmazás a nyelvi erőforrások gyorsítótárazását és a`ETag`megfelelő entitás címkéjét () kell figyelembe vennie. Ezután az ügyfélalkalmazás időnként (például 24 óránként) lekérdezi a szolgáltatást, hogy beolvassa a támogatott nyelvek legújabb készletét. Az aktuális `ETag` érték átadása `If-None-Match` egy fejléc mezőben lehetővé teszi, hogy a szolgáltatás optimalizálja a választ. Ha az erőforrás nem lett módosítva, a szolgáltatás a 304 állapotkódot és egy üres válasz törzsét fogja visszaadni.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>ETag</td>
    <td>A támogatott nyelvek kért csoportjaihoz tartozó entitás címkéje aktuális értéke. A későbbi kérelmek hatékonyabbá tételéhez az ügyfél elküldheti az `ETag` értéket egy `If-None-Match` fejléc mezőjében.
    </td>
  </tr>
  <tr>
    <td>X – kérelemazonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők: 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Az erőforrás nem módosult a kérelem fejlécei `If-None-Match`által megadott verzió óta.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa a kérelmek paramétereit.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója `X-RequestId`a válasz fejlécből és az ügyfél `X-ClientTraceId`azonosítója a kérelem fejlécében.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálja megismételni a kérelmet. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója `X-RequestId`a válasz fejlécből és az ügyfél `X-ClientTraceId`azonosítója a kérelem fejlécében.</td>
  </tr>
</table> 

Ha hiba történik, a kérés JSON-hibaüzenetet is ad vissza. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Text API hivatkozási oldalon](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan kérheti le a szöveg fordításához támogatott nyelveket.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
