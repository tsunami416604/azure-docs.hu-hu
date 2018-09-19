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
ms.openlocfilehash: 87814571e6f1c20b219020651eb798fa49a28deb
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127933"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adja hozzá a Translator Text API szűrés cenzúrázása

A Translator szolgáltatás általában megtalálható a forrás a fordítás cenzúrázása őrzi meg. A fokú cenzúrázása és a környezet, amellyel szavakat profán kulturális környezetek eltérő. Ennek eredményeképpen a párhuzamossági cenzúrázása a célként megadott nyelven kiegészíthetők előfordulhat, hogy, illetve csökkenteni.

Ha azt szeretné, a fordítás cenzúrázása (akkor is, ha cenzúrázása a forrás-szövegben található) megjelenésének elkerüléséhez, használja a vulgáris szűrés lehetőséget a Translate() metódusban. Ez a beállítás lehetővé teszi, hogy adja meg, hogy meg szeretné tekinteni a vulgáris törölték, vagy a megfelelő címkék megjelölve vagy hajt végre semmilyen műveletet.

A Translate() metódushoz egy "beállítások" paramétert, amely az új "ProfanityAction" elemet tartalmaz. ProfanityAction elfogadott értékek "NoAction," "Megjelölt" és "Törölt".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Elfogadott értékek ProfanityAction és példák
|ProfanityAction érték | Műveletek | Például: Forrás - japán | Például: Cél - angol nyelven|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Káromkodás forrásból cél továbbítja. | 彼は変態です。 | Egy jerk áll. |
| Megjelölve | XML-címkék veszi körül profán szavakat \<cenzúrázása >... \</profanity >. | 彼は変態です。 | Ő egy \<cenzúrázása > jerk\</profanity >. |
| Törölve | A kimenet nélküli helyettesítő profán szavak törlődnek. | 彼は。 | Ő egy. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A alkalmazni a Translator API-hívással szűrés cenzúrázása](reference/v3-0-translate.md)

