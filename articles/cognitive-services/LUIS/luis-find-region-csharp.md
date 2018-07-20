---
title: Language Understanding (LUIS) határain belül keresse meg a LUIS régió a C# |} A Microsoft Docs
description: Programozott módon a keresés régióhoz végponti kulcs és az alkalmazás közzététele a LUIS-azonosítója.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: f0df14736e0ed47957999e3aa7c6a22b0b0c0a35
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163071"
---
# <a name="region-can-be-determined-from-api-call"></a>Régió lehet meghatározni az API-hívás 
Ha rendelkezik a LUIS alkalmazás és a LUIS előfizetés-azonosító, Észreveheti, hogy melyik régiót kell végpont lekérdezések használatával.

> [!NOTE] 
> A teljes C# megoldás érhető el a [ **LUIS-Samples** Github-adattár](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>C#-kódot az osztály a régióban található
A Konzolalkalmazás átveszi a LUIS alkalmazás Azonosítóját és a végpont kulcs, és adja vissza a hozzá társított összes régióban. Egy végponti kulcs létrehozása régiónként éppen, ezért csak egy régió kell visszaadnia.

A .net könyvtár függőségeit tartalmazza:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Adja hozzá az egyéni LUIS osztály beépített a régióban található. Cserélje le a változó értéke a `luisAppId` és `luisSubscriptionKey` a saját értékeire. A hibakereső konzol minden olyan régió, amelyek 401-es vissza lesz írva. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Ez a példa az egyéni LUIS osztályt hívja meg a konzolalkalmazást a Main metódushoz:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Az alkalmazás futtatásakor a konzol jelenít meg a régiót az alkalmazás azonosítóját.

![Képernyőkép a konzolalkalmazást, LUIS terület megjelenítése](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>További lépések

További tudnivalók a LUIS [régiók](luis-reference-regions.md).
