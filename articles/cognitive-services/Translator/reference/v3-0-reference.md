---
title: A Microsoft Translator szöveg API V3.0 referencia |} Microsoft Docs
description: A V3.0 Microsoft Translator szöveg API referenciadokumentációt tartalmaz.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347751"
---
#<a name="translator-text-api-v30"></a>A fordító szöveg API 3.0-s verzió

## <a name="whats-new"></a>Újdonságok

A Microsoft Translator szöveg API 3-as verziója modern JSON-alapú webes API alkalmazást biztosít. Ez növeli a használhatóság és teljesítményét szolgáltatások azáltal kevesebb műveleteket, és olyan új funkciókat biztosít.

 * Egy parancsfájl egy nyelven írt szöveg átalakítása egy másik parancsprogramra transliteration.
 * A fordítás egy kérelem több nyelvhez.
 * Nyelvi megkereshetők, fordítási és transliteration egy kérelem.
 * A kifejezés vissza-fordítások és a környezetben használt kifejezések bemutató példák keresése a keresési alternatív fordítások szótár.
 * Nyelvi észlelésének eredménye még informatívabbá.

## <a name="base-urls"></a>Alap URL-címek

A következő felhő szöveg API 3.0-s verzió érhető el:

| Leírás | Régió | Alap URL-cím                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globális | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Hitelesítés

Fizessen elő a fordító szöveg API kognitív Microsoft-szolgáltatásokban, és az előfizetés (az Azure portálon rendelkezésre álló) kulcs segítségével hitelesítheti. 

A legegyszerűbb módja a át az Azure titkos kulcs a fordító szolgáltatás használata a kérelem fejlécében `Ocp-Apim-Subscription-Key`.

A másik lehetőség egy engedélyezési jogkivonatot szerezni a jogkivonat-szolgáltatás a titkos kulcsot használ. Így a fordító szolgáltatás használata az engedélyezési jogkivonat továbbítsa a `Authorization` kérelemfejlécet. Ha szüksége van egy engedélyezési jogkivonatot, hogy egy `POST` kérelem a következő URL-cím:

| Környezet     | Hitelesítési szolgáltatás URL-címe                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Az alábbiakban például kérelmek egy titkos kulcsot megadott jogkivonat beszerzése:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

A kérelem sikeres a kódolt hozzáférési jogkivonat a választörzs egyszerű szövegként adja vissza. Az érvényes jogkivonat az engedélyt a tulajdonosi jogkivonattal, a fordító szolgáltatás kerülnek.

```
Authorization: Bearer <Base64-access_token>
```

Egy hitelesítési jogkivonatot 10 percig érvényes. A jogkivonat a fordító API-k több hívása esetén kell újra felhasználni. Azonban ha a program kérelmeket küld egy hosszú időn keresztül a a fordító API-t, majd a program kell igényelnie egy új hozzáférési jogkivonat rendszeres időközönként (pl. 8 percenként).

Összefoglalva, egy ügyfél kérelmet a fordító API tartalmaz egy engedélyezési fejléc a következő táblázatban:

<table width="100%">
  <th width="30%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Az OCP-Apim-előfizetés-kulcs</td>
    <td>*Az kognitív szolgáltatások előfizetéssel használni, ha a titkos kulcs átadott*.<br/>Az előfizetés csak a fordító szöveg API Azure titkos kulcs értéke.</td>
  </tr>
  <tr>
    <td>Engedélyezés</td>
    <td>*Használjon kognitív szolgáltatások előfizetéssel, ha egy hitelesítési jogkivonatot átadott.*<br/>A tulajdonosi jogkivonattal értéke: "tulajdonosi <token>".</td>
  </tr>
</table> 

## <a name="errors"></a>Hibák

A rendszer egy standard hiba választ, egy JSON-objektum a név-érték pár nevű `error`. Az érték akkor is egy JSON-objektum tulajdonságokkal:

  * `code`: A kiszolgáló által definiált hibakód.

  * `message`: Jogosultságot ad a emberek számára olvasható megjelenítése a hiba a karakterlánc.

Például egy ügyfél egy ingyenes próba-előfizetést lenne a következő hibaüzenet Ha elfogy a szabad kvóta:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
