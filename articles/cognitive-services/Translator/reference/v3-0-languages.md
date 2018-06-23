---
title: A Microsoft Translator szöveg API nyelvek metódus |} Microsoft Docs
description: A Microsoft Translator szöveg API nyelvének módszert.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 93c06218a560faf439f05903438d021b372ce257
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348722"
---
# <a name="text-api-30-languages"></a>Szöveg API 3.0: nyelvek

Lekérdezi a meghatározott jelenleg más műveleteket a szöveg API által támogatott nyelv. 

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `GET` elküldeni a kérelmet:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A lekérdezési karakterlánc átadja a kérelemben szereplő paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*A paraméter kötelező*.<br/>Az API-t, az ügyfél által kért verzióját. Az értéknek kell lennie `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Nem kötelező paraméter*.<br/>Egy vesszővel tagolt listája nyelv vissza a csoport meghatározása. Csoportnevek engedélyezett: `translation`, `transliteration` és `dictionary`. Ha nincs megadva hatókör kap, akkor minden csoport ad vissza, amely megegyezik sikeres `scope=translation,transliteration,dictionary`. Dönteni, hogy a támogatott nyelvek körét a forgatókönyvnek megfelelő-e, tekintse meg a leírása a [válasz objektum](#response-body).</td>
  </tr>
</table> 

A kérelem fejléc van:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Fogadja el nyelv</td>
    <td>*Nem kötelező fejléc*.<br/>A felhasználói felület karakterláncok használni kívánt nyelvet. A válaszban szereplő mezők nyelv nevek vagy régiók nevek. Használja ezt a paramétert a nyelvet, ahol ezek a nevek visszaadott határozza meg. A nyelv, adja meg a megfelelően formázott BCP 47 nyelvi címke van megadva. Használja például a érték `fr` kérése nevek francia nyelven vagy értéket `zh-Hant` hagyományos kínai kérelem nevek.<br/>Nevek szerepelnek az angol nyelv, ha nincs megadva a cél nyelven, vagy ha a honosítás nem érhető el.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Nem kötelező fejléc*.<br/>Ügyfél által létrehozott GUID-azonosító egyedi módon azonosítja a kérelmet.</td>
  </tr>
</table> 

Hitelesítési nyelvi erőforrások nem szükséges.

## <a name="response-body"></a>Választörzs

Egy ügyfél használ a `scope` paraméterrel adjuk meg, mely csoportok nyelv azt iránt érdeklődik lekérdezése.

* `scope=translation` Itt lefordítani más nyelven; egyik nyelvről szöveg támogatott nyelvek

* `scope=transliteration` képességeket biztosít az egy parancsfájl egy nyelven írt szöveg átalakításához egy másik parancsprogramra;

* `scope=dictionary` nyelvi párok biztosít, amelynek `Dictionary` műveletek vissza adatokat.

Egy ügyfél lehet, hogy több csoportjainak lekérdezésére egyidejűleg ad meg a neveket vesszővel tagolt listája. Például `scope=translation,transliteration,dictionary` alakítanák vissza az összes csoport támogatott nyelveket.

A sikeres válasz egy JSON-objektum és az egyes kért egy tulajdonsága:

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

Minden egyes tulajdonság értéke az alábbiak szerint.

* `translation` Tulajdonság

  Értékét a `translation` tulajdonság értéke (kulcs, érték) dictionary párokat. Minden egyes kulcsa a BCP 47 nyelvcímkének. A kulcs egy nyelvet, amelynek szöveg lefordítani vagy a lefordított azonosítja. A kulcshoz hozzárendelt érték egy JSON-objektum leíró nyelv tulajdonságokkal:

  * `name`: A területi keresztül kért nyelv megjelenített név `Accept-Language` fejléc.

  * `nativeName`: A nyelv nevének megjelenítése a natív ezt a nyelvet a területi beállításai szerint.

  * `dir`: Irányú, amely `rtl` jobbról balra író nyelvek esetén vagy `ltr` jobbról balra író nyelvek esetén.

  Példa::
          
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

* `transliteration` Tulajdonság

  Értékét a `transliteration` tulajdonság értéke (kulcs, érték) dictionary párokat. Minden egyes kulcsa a BCP 47 nyelvcímkének. A kulcs egy nyelvet, amelynek szöveg egy másik parancsprogramra egy parancsfájlból származó átalakítható azonosítja. A kulcshoz hozzárendelt érték egy JSON-objektum leíró nyelv és a támogatott szkriptek tulajdonságokkal:

  * `name`: A területi keresztül kért nyelv megjelenített név `Accept-Language` fejléc.

  * `nativeName`: A nyelv nevének megjelenítése a natív ezt a nyelvet a területi beállításai szerint.

  * `scripts`: A próbaverzióról parancsfájlok listája. Egyes elemeinek a `scripts` lista tulajdonságai:

    * `code`: A parancsfájl azonosító kód.

    * `name`: A parancsfájl keresztül kért területi megjelenített név `Accept-Language` fejléc.

    * `nativeName`: A natív nyelv területibeállítás a nyelv nevének megjelenítése.

    * `dir`: Irányú, amely `rtl` jobbról balra író nyelvek esetén vagy `ltr` jobbról balra író nyelvek esetén.

    * `toScripts`: A parancsfájlok szöveg átalakítása rendelkezésre álló listája. Egyes elemeinek a `toScripts` lista tulajdonságai `code`, `name`, `nativeName`, és `dir` ismertetett módon.

  Példa::

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

* `dictionary` Tulajdonság

  Értékét a `dictionary` tulajdonság értéke (kulcs, érték) dictionary párokat. Minden egyes kulcsa a BCP 47 nyelvcímkének. A kulcs azonosítja az egy nyelvet, amely alternatív fordítások és vissza-fordítások érhetők el. Az érték egy JSON-objektum, amely leírja a adatforrás nyelvi és a cél fordításokat nyelvek:

  * `name`: Az adatforrás nyelvi keresztül kért területi megjelenítendő nevét `Accept-Language` fejléc.

  * `nativeName`: A nyelv nevének megjelenítése a natív ezt a nyelvet a területi beállításai szerint.

  * `dir`: Irányú, amely `rtl` jobbról balra író nyelvek esetén vagy `ltr` jobbról balra író nyelvek esetén.

  * `translations`: Az alternatív fordítások és példák az adatforrás nyelvi kifejezett lekérdezés nyelvek listáját. Egyes elemeinek a `translations` lista tulajdonságai:

    * `name`: A területi keresztül a kért cél nyelv megjelenített név `Accept-Language` fejléc.

    * `nativeName`: Megjelenített neve a cél nyelvet a területi beállításai szerint natív a célként megadott nyelven.

    * `dir`: Irányú, amely `rtl` jobbról balra író nyelvek esetén vagy `ltr` jobbról balra író nyelvek esetén.
    
    * `code`: A cél nyelvi azonosító nyelvi kódot.

  Példa::

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

A válasz objektum struktúra nem módosítja az API-t verziójában megváltoztatása nélkül. Az API-t ugyanazon verzióját, a rendelkezésre álló nyelvek listáját idővel megváltozhat, mert a Microsoft Translator folyamatosan bővíti a szolgáltatások által támogatott nyelvek listáját.

A támogatott nyelvek listája nem változik gyakran. Hálózati sávszélesség, és javíthatják a válaszképesség ügyfélalkalmazás vegye nyelvi erőforrások és a megfelelő entitáscímke gyorsítótárazás (`ETag`). Ezt követően az ügyfélalkalmazás rendszeres időközönként (például az 24 óránként) lehet lekérdezni a szolgáltatást a legújabb meghatározott nyelv támogatott beolvasása. Az aktuális átadásakor `ETag` értéket egy `If-None-Match` fejlécmező lehetővé teszi a szolgáltatás a válasz optimalizálása érdekében. Ha az erőforrás nem módosult, a szolgáltatás 304 állapotkódot és azt egy üres adott válasz törzsének vissza.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>ETag</td>
    <td>A támogatott nyelvek kért csoportjai számára entitáscímke aktuális értéke. Ahhoz, hogy hatékonyabb kérelmeknél, az ügyfél küldhet a `ETag` értéket egy `If-None-Match` fejlécmező.
    </td>
  </tr>
  <tr>
    <td>X-kérelemazonosító</td>
    <td>A szolgáltatás azonosítására a kérelem által generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Állapotkódok

Az alábbiakban a lehetséges HTTP-állapotkódok, egy kérelem ad vissza. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Az erőforrás a kérelem fejlécében megadott verzió óta nem módosították `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezés-paraméterek egyike hiányzik vagy érvénytelen. A kérelemben szereplő paraméterek megkísérlése előtt javítsa ki.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a probléma továbbra is fennáll, jelentse a: dátum és idő, a hiba, a válaszfejléc azonosítóját `X-RequestId`, és a kérelem fejlécében ügyfél-azonosítókra `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló ideiglenesen nem érhető el. Próbálkozzon újra a kéréssel. Ha a probléma továbbra is fennáll, jelentse a: dátum és idő, a hiba, a válaszfejléc azonosítóját `X-RequestId`, és a kérelem fejlécében ügyfél-azonosítókra `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Példák

A következő példa bemutatja, hogyan lehet lekérni a szöveget fordításhoz támogatott nyelvek.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
