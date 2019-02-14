---
title: Szövegelemzési API hívása
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hívhat meg a Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 52c5cb640bfb861fb2da52ee711fe3955a169bcf
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244028"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>A Text Analytics REST API meghívása

Hívások a **Text Analytics API** HTTP POST vagy GET-hívás, amely bármilyen nyelven is állítson össze vannak. Ebben a cikkben használjuk a REST és [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) alapfogalmak bemutatásához.

Minden kérelmet tartalmaznia kell a hozzáférési kulccsal és a egy HTTP-végpontot. A végpont meghatározza a bejelentkezés során fel választott régióban, a szolgáltatás URL-CÍMÉT és a kérésben használt erőforrás: `sentiment`, `keyphrases`, `languages`, és `entities`. 

Ne felejtse el, hogy Szövegelemzés állapotmentes-e így nincsenek adategységek kezeléséhez. A szöveges feltöltése, beérkezésekor, elemzi és eredmények azonnal visszatér a hívó alkalmazás.

> [!Tip]
> Az egyszeri hívás, hogy az API működését, a beépített is küld POST kéréseket **API tesztelési konzollal**, bármely [API dokumentációs oldal](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Nincs beállítva, és mindössze egy hozzáférési kulcsot, és a JSON-dokumentumok illessze be a kérést. 

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Text Analytics API-val és a [végpontját és hozzáférési kulcsát](text-analytics-how-to-access-key.md) , amely jön létre, amikor regisztrál a Cognitive Services számára. 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON-séma definíció

Bemeneti nyers, strukturálatlan szöveges JSON kell lennie. Nem támogatott XML. A séma az egyszerű, amely az alábbi listában szereplő elemeket. 

Ugyanazt a dokumentumokat, a Text Analytics művelet jelenleg nyújthatja: véleményeket, a kulcsfontosságú kifejezések, nyelv észlelése és entitás azonosítója. (A sémát a eltérhet a jövőben az egyes elemzés céljából.)

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus karakterlánc, de a gyakorlatban dokumentumazonosító általában a egész szám lehet. | Szükséges | A rendszer adja át a kimenetet struktúra azonosítóját használja. A kérelemben szereplő minden egyes azonosító nyelvkódokról, kulcskifejezéseket és véleménypontszámának jön létre.|
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5000 karakter hosszú lehet. | Szükséges | Nyelv észlelése, a szöveg bármilyen nyelven lehet kifejezni. A hangulatelemzést, kulcsszókeresést és entitás azonosítása, a szöveget kell lennie egy [támogatott nyelven](../text-analytics-supported-languages.md). |
|`language` | 2 karakterből álló [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) helykódja egy [támogatott nyelven](../text-analytics-supported-languages.md) | Változó | Szükséges a hangulatelemzést, kulcsszókeresést és entitáskapcsolás; nyelv észlelése esetén nem kötelező. Nincs hiba, ha lehetséges, azonban az elemzés, e nélkül integritása csökken. A nyelvi kód meg kell egyeznie a `text` adnia. |

Korlátok kapcsolatos további információkért lásd: [Text Analytics áttekintés > Data korlátai](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Állítsa be a Postman-kérelem

A szolgáltatás fogadja a kérelem legfeljebb 1 MB méretű. Postman (vagy egy másik webes API tesztelési eszköz) használ, ha az erőforrás létrehozásához használni szeretne felvenni a végpont beállításához, és adja meg a fejléc a hozzáférési kulcsát. Minden egyes-művelet megköveteli, hogy a végponthoz való fűzze hozzá a megfelelő erőforrás. 

1. A Postmanben:

   + Válasszon **Post** a kérelem típusaként.
   + Illessze be a végpont a portál oldala fájlból kimásolt.
   + Hozzáfűzése erőforrás.

  Erőforrás-végpontok a következők az alábbiak szerint (a saját régiójában eltérhetnek):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

2. Állítsa be a három kérelemfejlécek:

   + `Ocp-Apim-Subscription-Key`: az Azure Portalról kapott a hozzáférési kulccsal.
   + `Content-Type`: az application/json.
   + `Accept`: az application/json.

  A kérelem az alábbi képernyőfelvételhez hasonlóan kell kinéznie feltéve, hogy egy **/keyPhrases** erőforrás.

   ![A végpont és a fejlécek kérelem képernyőképe](../media/postman-request-keyphrase-1.png)

4. Kattintson a **törzs** válassza **nyers** formátum.

   ![Képernyőkép kérelem törzse beállításokkal](../media/postman-request-body-raw.png)

5. Illessze be néhány JSON-dokumentumot, amely az importálni kívánt elemzés érvényes formátumban. Egy adott elemzés kapcsolatos további információkért lásd az alábbi témakörök:

  + [Nyelvfelismerés](text-analytics-how-to-language-detection.md)  
  + [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md)  
  + [Hangulatelemzés](text-analytics-how-to-sentiment-analysis.md)  
  + [Entitások felismerése (előzetes verzió)](text-analytics-how-to-entity-linking.md)  


6. Kattintson a **küldése** a kérelmet küldeni. Percenkénti kérések legfeljebb 100 küldhet. 

  A Postman a válasz megjelenik a következő időszakban, a JSON-dokumentumként egyetlen, az egyes dokumentumazonosító a kérésben megadott egy elemét.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyelv felismerése](text-analytics-how-to-language-detection.md)
