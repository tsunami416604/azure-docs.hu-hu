---
title: A C# LUIS régióban található nyelvi ismertetése (LUIS) határok |} Microsoft Docs
description: Programozott módon keresés közzététele végpontkulcs és alkalmazás régió LUIS azonosítója.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: f0df14736e0ed47957999e3aa7c6a22b0b0c0a35
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110408"
---
# <a name="region-can-be-determined-from-api-call"></a>A régióban lehet meghatározni az API-hívás 
Ha az alkalmazás Azonosítóját és a LUIS előfizetés-azonosító LUIS, melyik régióban végpont lekérdezések használandó találja.

> [!NOTE] 
> A teljes C# megoldás érhető el a [ **LUIS-minták** Github-tárházban](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>C#-kódban osztály régióban található
A Konzolalkalmazás időt vesz igénybe a LUIS alkalmazás Azonosítóját és a végpont-kulcsot, és a vele társított minden egyes beolvasása. Egy végpont kulcs létrehozása régiónként éppen, ezért csak egy régió kell visszaadnia.

A .net könyvtár függőségek a következők:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Ebben a régióban található beépített egyéni LUIS osztály hozzáadása. Cserélje le a változó értéke a `luisAppId` és `luisSubscriptionKey` saját értékekkel. Minden egyes 401 visszaadó fog szerepelni a hibakereső konzolt. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Itt látható egy példa a Konzolalkalmazás fő metódus hívása az egyéni LUIS osztály:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Az alkalmazás futtatásakor a konzol a régió mutatja az alkalmazás azonosítóját.

![Konzolalkalmazás LUIS régió ábrázoló képernyőfelvétel](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>További lépések

További tudnivalók LUIS [régiók](luis-reference-regions.md).
