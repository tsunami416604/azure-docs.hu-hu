---
title: Káromkodás szűrése – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Translator Text API szűrés cenzúrázása használja.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: bd7a05f2f597d1882293387e5aac8e4d7367d051
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916631"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adja hozzá a Translator Text API szűrés cenzúrázása

A Translator szolgáltatás általában megtalálható a forrás a fordítás cenzúrázása őrzi meg. A fokú cenzúrázása és a környezet, amellyel szavakat profán kulturális környezetek eltérő. Ennek eredményeképpen a párhuzamossági cenzúrázása a célként megadott nyelven kiegészíthetők előfordulhat, hogy, illetve csökkenteni.

A fordítás cenzúrázása megjelenésének elkerüléséhez akkor is, ha cenzúrázása a forrás-szövegben található, használja a vulgáris szűrési lehetőségek érhetők el a Translate() metódusban. Ez a beállítás lehetővé teszi, hogy válassza ki, hogy cenzúrázása törölni, hogy a megfelelő címkék megjelölve, vagy nem tett semmit.

A Translate() metódushoz a "beállítások" paramétert, amely az új "ProfanityAction" elemet tartalmaz. A ProfanityAction az elfogadott értékek a következők "NoAction", "Marked" és "Törölt".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Elfogadott értékek ProfanityAction és példák
|ProfanityAction érték | Műveletek | Példa: Forrás - japán | Példa: Cél - angol nyelven|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Káromkodás forrásból cél továbbítja. | 彼は変態です。 | Egy jerk áll. |
| Megjelölve | XML-címkék veszi körül profán szavakat \<cenzúrázása >... \</profanity >. | 彼は変態です。 | Ő egy \<cenzúrázása > jerk\</profanity >. |
| Törölve | A kimenet nélküli helyettesítő profán szavak törlődnek. | 彼は。 | Ő egy. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A alkalmazni a Translator API-hívással szűrés cenzúrázása](reference/v3-0-translate.md)
