---
title: Káromkodás szűrése – Translator Text API
titleSuffix: Azure Cognitive Services
description: A káromkodás szűrésével meghatározhatja az Azure Cognitive Services Translator Text APIban lévő szövegben lefordított káromkodás szintjét.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836225"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Káromkodás-szűrés hozzáadása a Translator Text API

A fordítói szolgáltatás általában megőrzi a fordításban található forrásban lévő káromkodást. A trágárság foka és az olyan kontextus, amely a szavak káromkodását teszi lehetővé a különböző kultúrák között. Ennek eredményeképpen a megcélzott nyelvben a káromkodás mértéke felerősíthető vagy csökkenthető.

Ha nem szeretné megtekinteni a káromkodást a fordításban, akkor is, ha a forrás szövegében a káromkodás szerepel, használja a translate () metódusban elérhető káromkodás-szűrési lehetőséget. Ezzel a beállítással megadhatja, hogy szeretné-e megtekinteni a káromkodás törlését, a megfelelő címkék jelölését vagy a művelet elvégzését.

A translate () metódus a "Options" paramétert veszi fel, amely tartalmazza az új "ProfanityAction" elemet. A ProfanityAction elfogadott értékei a következők: "nincs művelet", "megjelölt" és "törölve".

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction és példák elfogadott értékei
|ProfanityAction érték | Műveletek | Példa: forrás – Japán | Példa: cél-angol|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás beállítása. A káromkodás a forrás és a cél között halad át. | 彼は変態です 。 | Egy bunkó. |
| Megjelölve | A profán szavakat az XML-címkék veszik \<a káromkodás >... \</profanity >. | 彼は変態です 。 | \<káromkodás > jerk\</profanity >. |
| Törölve | A rendszer eltávolítja a profán szavakat a kimenetből a csere nélkül. | 彼は 。 | Ő a. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Káromkodás-szűrés alkalmazása a Translator API-hívással](reference/v3-0-translate.md)
