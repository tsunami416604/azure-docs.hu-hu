---
title: Végpont régió, a Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: A node.js-szel, keresés régióhoz végponti kulcs és az alkalmazás közzététele a LUIS-azonosítója.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 4d14569219c8db503fc91f52a6867de85373aa05
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724385"
---
# <a name="programmatically-find-endpoint-region-with-nodejs"></a>Programozott módon található végponti régió a node.js használatával
Ha rendelkezik a LUIS alkalmazás és a LUIS előfizetés-azonosító, Észreveheti, hogy melyik régiót kell végpont lekérdezések használatával.

> [!NOTE] 
> A teljes Node.js-megoldás érhető el a [ **Azure-minták** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>A LUIS végpont lekérdezés stratégia
Minden egyes LUIS végpont lekérdezés van szükség:

* A végpont kulcs
* Egy alkalmazás azonosítója
* Egy régióban

A LUIS-végpont lekérdezés megfelelő végpontra kulcs és az alkalmazás Azonosítóját használja, de nem a megfelelő régióba, ha a válasz kódja 401-es. A 401-es kérelem felé az előfizetési kvóta nem vesszük számításba. A kérelem alakítsa át a megfelelő régióban található összes régióban lekérdezésére stratégia. A megfelelő régióban a egyetlen kérést, amely a 2xx állapotkódot ad vissza. 

|Válaszkód|Paraméterek|
|--|--|
|2xx|a kulcs megfelelő végpontra<br>az alkalmazás megfelelő azonosító<br>megfelelő gazdagép régió|
|401|a kulcs megfelelő végpontra<br>az alkalmazás megfelelő azonosító<br>_helytelen_ fogadó régió|

## <a name="nodejs-code-to-find-region"></a>NODE.js-kód régió keresése
A Konzolalkalmazás átveszi a LUIS alkalmazás Azonosítóját és a végpont kulcs, és adja vissza a hozzá társított összes régióban. Egy végponti kulcs létrehozása régiónként éppen, ezért csak egy régió kell visszaadnia.

Az NPM-függőségek a következők:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Adjon állandókat. Cserélje le a változó értéke a `subscriptionKey` és `appId` a saját értékeire.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Adjon hozzá `searchRegions` függvényt, hogy a régióban. Összes helytelen régióban 401-es, adja vissza, amely észlelt, és figyelmen kívül hagyja.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Hívja a `searchRegions` funkciót, és egyetlen régióban adja vissza:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Az alkalmazás futtatásakor a terminál jelenít meg a régiót az alkalmazás azonosítóját.

![Képernyőkép a konzolalkalmazást, LUIS terület megjelenítése](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>További lépések

További tudnivalók a LUIS [régiók](luis-reference-regions.md).
