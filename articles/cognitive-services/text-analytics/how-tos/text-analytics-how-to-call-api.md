---
title: Szövegelemzési API hívása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hívhatja meg az Azure Cognitive Services Text Analytics REST API-t és postást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219301"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>A Text Analytics REST API hívása

A **Text Analytics API hívásai** HTTP POST/GET hívások, amelyeket bármilyen nyelven megfogalmazhat. Ebben a cikkben a REST és [a Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) segítségével mutatjuk be a legfontosabb fogalmakat.

Minden kérelemnek tartalmaznia kell a hozzáférési kulcsot és egy HTTP-végpontot. A végpont határozza meg a regisztráció során kiválasztott régiót, a szolgáltatás URL-címét `languages`és `entities`a kérésben használt erőforrást: `sentiment`, `keyphrases`, és . 

Emlékezzünk vissza, hogy a Text Analytics állapot nélküli, így nincsenek kezelhet adateszközök. A rendszer feltölti, elemzi a szöveget a kézhezvételkor, és az eredményeket azonnal visszaküldi a hívó alkalmazásnak.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON-séma definíciója

A bemenetnek JSON-nak kell lennie a nyers strukturálatlan szövegben. Az XML nem támogatott. A séma egyszerű, amely az alábbi listában ismertetett elemekből áll. 

Jelenleg ugyanazokat a dokumentumokat küldheti el az összes Text Analytics-művelethez: hangulat, kulcskifejezés, nyelvfelismerés és entitásazonosítás. (A séma valószínűleg a jövőben minden egyes elemzésnél eltérő lesz.)

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus karakterlánc, de a gyakorlatban a dokumentumazonosítók általában egész számok. | Kötelező | A rendszer a megadott azonosítókat használja a kimenet strukturálásához. A nyelvkódok, a kulcskifejezések és a hangulatpontszámok a kérelemben szereplő minden azonosítóhoz létrejönnek.|
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5120 karakter. | Kötelező | A nyelvfelismeréshez a szöveg bármilyen nyelven kifejezhető. A hangulatelemzéshez, a kulcskifejezések kinyeréséhez és az entitás azonosításához a szövegnek [támogatott nyelven](../text-analytics-supported-languages.md)kell lennie. |
|`language` | 2 karakteres [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) kód egy [támogatott nyelvhez](../text-analytics-supported-languages.md) | Változó | Hangulatelemzéshez, kulcskifejezések kinyeréséhez és entitáscsatoláshoz szükséges; választható a nyelvfelismeréshez. Nincs hiba, ha kizárja, de az elemzés nélküle gyengül. A nyelvkódnak meg `text` kell felelnie az Ön által megadott kódnak. |

A korlátokról a [Szövegelemzés áttekintése > adatkorlátok](../overview.md#data-limits)című témakörben olvashat bővebben. 

## <a name="set-up-a-request-in-postman"></a>Kérelem beállítása a Postán

A szolgáltatás legfeljebb 1 MB méretű kérést fogad el. Ha postmant (vagy más webes API-teszteszközt) használ, állítsa be a végpontot úgy, hogy tartalmazza a használni kívánt erőforrást, és adja meg a hozzáférési kulcsot a kérelem fejlécében. Minden művelet hez hozzá kell fűzni a megfelelő erőforrást a végponthoz. 

1. A postás:

   + Válassza a **Közzététel** lehetőséget kéréstípusként.
   + Illessze be a portállapról másolt végpontot.
   + Erőforrás hozzáfűzése.

   Az erőforrás-végpontok a következők (a régió eltérő lehet):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Állítsa be a három kérelemfejlécet:

   + `Ocp-Apim-Subscription-Key`: az Azure Portalról beszerzett hozzáférési kulcs.
   + `Content-Type`: alkalmazás/json.
   + `Accept`: alkalmazás/json.

   A kérésnek a következő képernyőképhez hasonlóan kell kinéznie, feltételezve a **/keyPhrases** erőforrást.

   ![Képernyőkép kérése végpontdal és fejlécekkel](../media/postman-request-keyphrase-1.png)

4. Kattintson **a Törzs** gombra, és válassza a **formátumhoz** a nyers lehetőséget.

   ![Képernyőkép kérése a testbeállításokkal](../media/postman-request-body-raw.png)

5. Illessze be egyes JSON-dokumentumokat a tervezett elemzésre érvényes formátumban. Egy adott elemzéssel kapcsolatos további információkért lásd az alábbi témaköröket:

  + [Nyelvfelismerés](text-analytics-how-to-language-detection.md)  
  + [Kulcskifejezés kinyerése](text-analytics-how-to-keyword-extraction.md)  
  + [Hangulatelemzés](text-analytics-how-to-sentiment-analysis.md)  
  + [Entitások felismerése](text-analytics-how-to-entity-linking.md)  


6. A kérelem elküldéséhez kattintson a **Küldés** gombra. Az [adatkorlátok](../overview.md#data-limits) című szakaszban az áttekintésben olvashat a percenként és másodpercenként elküldhető kérések számáról.

   A Postman alkalmazásban a válasz a következő ablakban egyetlen JSON-dokumentumként jelenik meg, a kérelemben megadott minden dokumentumazonosítóhoz egy elemet.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyelv felismerése](text-analytics-how-to-language-detection.md)
