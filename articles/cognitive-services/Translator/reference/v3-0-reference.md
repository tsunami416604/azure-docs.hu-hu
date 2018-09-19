---
title: Translator Text API 3.0-referencia
titlesuffix: Azure Cognitive Services
description: A Translator Text API 3.0 dokumentációja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9282d8af30cbfb3346394bcd71510faf8d8c8a21
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129386"
---
# <a name="translator-text-api-v30"></a>Translator Text API 3.0-s verzió

## <a name="whats-new"></a>Újdonságok

3-as verziója a Translator Text API modern JSON-alapú webes API-t biztosít. Ez növeli a használhatóság és teljesítményét a meglévő funkciók konszolidálása kevesebb műveletek és, olyan új funkciókat biztosít.

 * Egy parancsprogram az egyik nyelven szöveg átalakítása egy másik parancsprogramra átbetűzésű.
 * Fordítási egyetlen kérelem több nyelvhez.
 * Nyelv észlelése, a fordítási és átbetűzésű egy kérelem.
 * A kifejezés található biztonsági-fordítások és a példákat a környezetben használt kifejezések keresési felkínálásával szótár.
 * Konkrétabb nyelvi észlelésének eredménye.

## <a name="base-urls"></a>Alap URL-címek

Szöveg API 3.0-s verzió a következő felhőben érhető el:

| Leírás | Régió | Alap URL-cím                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globális | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Hitelesítés

Fizessen elő a Translator Text API a Microsoft Cognitive Services és az előfizetési kulcs (az Azure Portalon érhető el) használja a hitelesítéshez. 

A legegyszerűbb módja, ha az Azure a titkos kulcs át a Translator szolgáltatás használatával a kérelem fejlécében `Ocp-Apim-Subscription-Key`.

Alternatív, hogy a titkos kulcsot egy engedélyezési jogkivonatot szerezni a jogkivonat-szolgáltatás. Ezt követően átadhatja a hitelesítési jogkivonat a Translator szolgáltatás használatával a `Authorization` kérés fejlécéhez. Egy engedélyezési jogkivonatot beszerezni, győződjön meg arról, egy `POST` kérelem a következő URL-CÍMRE:

| Környezet     | Hitelesítési szolgáltatás URL-címe                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Az alábbiakban például kérelmek egy adott titkos kulcs token beszerzése:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

A kérelem sikeres a kódolt hozzáférési jogkivonatot a válasz törzsében egyszerű szövegként adja vissza. Az érvényes token az engedélyt a tulajdonosi jogkivonattal, a Translator szolgáltatás kerülnek.

```
Authorization: Bearer <Base64-access_token>
```

Egy hitelesítési tokent a 10 percig érvényes. A jogkivonat a Translator API-k több hívása esetén kell újra felhasználni. Azonban ha a program kéréseket hajt végre a Translator API egy kiterjesztett időszakon belül, majd a program kell kérnie egy új hozzáférési jogkivonat rendszeres időközönként (például: 8 percenként).

Összefoglalva, a Translator API egy ügyfél kérelmet belefoglal egy engedélyeztetési fejléc a következő táblázatban:

<table width="100%">
  <th width="30%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>OCP-Apim-Subscription-Key</td>
    <td>*Cognitive Services-előfizetés használata, a titkos kulcs átadásakor*.<br/>A Translator Text API-előfizetéséhez tartozó Azure titkos kulcs értéke.</td>
  </tr>
  <tr>
    <td>Engedélyezés</td>
    <td>*Cognitive Services-előfizetés használata egy hitelesítési tokent átadásakor.*<br/>A tulajdonosi jogkivonat értéke: "tulajdonosi <token>".</td>
  </tr>
</table> 

## <a name="errors"></a>Hibák

Standard hiba választ a név-érték pár nevű JSON-objektum `error`. Az érték akkor is egy JSON-tulajdonságokkal rendelkező objektum:

  * `code`: A kiszolgáló által meghatározott hibakód.

  * `message`: Egy karakterlánc, így a hiba emberek számára olvasható reprezentációját.

Ha például egy ingyenes próba-előfizetéssel rendelkező ügyfél lenne a következő hibaüzenet után az ingyenes kvótát kimerül:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
