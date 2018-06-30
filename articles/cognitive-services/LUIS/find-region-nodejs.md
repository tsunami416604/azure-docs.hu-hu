---
title: LUIS régióban, Node.js nyelvi ismertetése (LUIS) határain belül található |} Microsoft Docs
description: Programozott módon keresés közzététele végpontkulcs és alkalmazás régió LUIS azonosítója.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 6d85e6007b3e85a1b55997541e721ad57c22dddf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111745"
---
# <a name="region-can-be-determined-from-api-call"></a>A régióban lehet meghatározni az API-hívás 
Ha az alkalmazás Azonosítóját és a LUIS előfizetés-azonosító LUIS, melyik régióban végpont lekérdezések használandó találja.

> [!NOTE] 
> A teljes Node.js megoldás érhető el a [ **LUIS-minták** Github-tárházban](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>LUIS végpont lekérdezés stratégia
Minden egyes LUIS végpont lekérdezésre van szükség:

* Egy végpontkulcs
* Az alkalmazás azonosítója
* A régió

Ha a LUIS végpont lekérdezés a megfelelő végpont kulcs és az alkalmazás Azonosítóját használja, de a megfelelő régióba, ez a válaszkód 401-es. A 401-es kérelem nem számít az előfizetési kvóta felé. A kérelem ikonná, és kérdezze le a megfelelő régióban található minden egyes stratégiát. A megfelelő terület a csak kérelmet, amely egy 2xx állapotkódot adja vissza. 

|Válaszkód|Paraméterek|
|--|--|
|2xx|megfelelő végpontkulcs<br>Javítsa ki az alkalmazás azonosítója<br>megfelelő fogadó régió|
|401|megfelelő végpontkulcs<br>Javítsa ki az alkalmazás azonosítója<br>_helytelen_ fogadó régió|

## <a name="nodejs-code-to-find-region"></a>NODE.js kód régióban található
A Konzolalkalmazás időt vesz igénybe a LUIS alkalmazás Azonosítóját és a végpont-kulcsot, és a vele társított minden egyes beolvasása. Egy végpont kulcs létrehozása régiónként éppen, ezért csak egy régió kell visszaadnia.

Az NPM függőségek a következők:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Adja hozzá a konstans. Cserélje le a változó értéke a `subscriptionKey` és `appId` saját értékekkel.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Adja hozzá `searchRegions` függvényt, hogy a régióban. Helytelen minden egyes 401-es, adja vissza, amely észlelés és figyelmen kívül hagyja.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Hívja a `searchRegions` funkciót, és egyetlen régión vissza:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Az alkalmazás futtatásakor a Terminálszolgáltatások a régió mutatja az alkalmazás azonosítóját.

![Konzolalkalmazás LUIS régió ábrázoló képernyőfelvétel](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>További lépések

További tudnivalók LUIS [régiók](luis-reference-regions.md).
