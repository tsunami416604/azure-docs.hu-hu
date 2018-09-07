---
title: A C# végpont régió található LUIS
titleSuffix: Azure Cognitive Services
description: Programozott módon a keresés régióhoz végponti kulcs és az alkalmazás közzététele a LUIS-azonosítója.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 7af58f141730557f103c61a6c591908cc7ec69d0
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057370"
---
# <a name="find-endpoint-region-with-c"></a>Keresse meg a végpont régió a C# használatával 
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
