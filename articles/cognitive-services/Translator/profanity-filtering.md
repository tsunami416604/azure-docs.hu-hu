---
title: Káromkodás szűrése – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Translator Text API szűrés cenzúrázása használja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 4154950cf8d8b6ec2e47a9f8100cb7983ac127bf
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648037"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adja hozzá a Translator Text API szűrés cenzúrázása

A Translator szolgáltatás általában megtalálható a forrás a fordítás cenzúrázása őrzi meg. A fokú cenzúrázása és a környezet, amellyel szavakat profán kulturális környezetek eltérő. Ennek eredményeképpen a párhuzamossági cenzúrázása a célként megadott nyelven kiegészíthetők előfordulhat, hogy, illetve csökkenteni.

A fordítás cenzúrázása megjelenésének elkerüléséhez akkor is, ha cenzúrázása a forrás-szövegben található, használja a vulgáris szűrési lehetőségek érhetők el a Translate() metódusban. Ez a beállítás lehetővé teszi, hogy válassza ki, hogy cenzúrázása törölni, hogy a megfelelő címkék megjelölve, vagy nem tett semmit.

A Translate() metódushoz a "beállítások" paramétert, amely az új "ProfanityAction" elemet tartalmaz. A ProfanityAction az elfogadott értékek a következők "NoAction", "Marked" és "Törölt".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Elfogadott értékek ProfanityAction és példák
|ProfanityAction érték | Műveletek | Például: Forrás - japán | Például: Cél - angol nyelven|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Káromkodás forrásból cél továbbítja. | 彼は変態です。 | Egy jerk áll. |
| Megjelölve | XML-címkék veszi körül profán szavakat \<cenzúrázása >... \</profanity >. | 彼は変態です。 | Ő egy \<cenzúrázása > jerk\</profanity >. |
| Törölve | A kimenet nélküli helyettesítő profán szavak törlődnek. | 彼は。 | Ő egy. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A alkalmazni a Translator API-hívással szűrés cenzúrázása](reference/v3-0-translate.md)
