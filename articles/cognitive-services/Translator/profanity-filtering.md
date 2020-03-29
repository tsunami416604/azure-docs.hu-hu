---
title: Profanitás szűrése - Fordító szöveg API
titleSuffix: Azure Cognitive Services
description: A profanitás szűrésével határozza meg a szövegben lefordított káromkodás szintjét az Azure Cognitive Services Translator Text API-ban.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836225"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Káromkodásszűrés hozzáadása a Translator Text API-val

Normális esetben a fordító szolgáltatás megtartja káromkodás, amely jelen van a forrás a fordításban. A káromkodás mértéke és a kontextus, ami a szavakat a kultúrák között különbözik. Ennek eredményeképpen a célnyelvben a káromkodás mértéke felerősödhet vagy csökkenhet.

Ha el szeretné kerülni a káromkodást a fordításban, még akkor is, ha a káromkodás szerepel a forrásszövegben, használja a Fordítás() metódusban elérhető profanitás szűrési lehetőséget. Ezzel a beállítással megadhatja, hogy a káromkodást törölni, megfelelő címkékkel jelölve kívánja-e, vagy nem kíván-e műveletet végrehajtani.

A Translate() metódus a "options" paramétert veszi igénybe, amely a "ProfanityAction" új elemet tartalmazza. A ProfanityAction elfogadott értékei a "NoAction", "Megjelölt" és "Törölve".

## <a name="accepted-values-of-profanityaction-and-examples"></a>A ProfanityAction elfogadott értékei és példák
|ProfanityAction érték | Műveletek | Példa: Forrás - Japán | Példa: Target - angol|
| :---|:---|:---|:---|
| NoAction (Művelet nélkül) | Default (Alapértelmezett): Ugyanaz, mint nem a beállítás a lehetőséget. A káromkodás a forrástól a célig halad. | A ( a ) | Ő egy bunkó. |
| Jelölt | Profán szavak körül XML \<címkék káromkodás> ... \</káromkodás>. | A ( a ) | Ő egy \<káromkodás>\<bunkó / káromkodás>. |
| Törölve | A profán szavak cseréje nélkül törlődnek a kimenetből. | A | Ő egy. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Káromkodásszűrés alkalmazása a Translator API-hívással](reference/v3-0-translate.md)
