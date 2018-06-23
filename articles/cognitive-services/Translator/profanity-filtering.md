---
title: Profanitás szűrése a Microsoft Translator szöveg API-val |} Microsoft Docs
description: A Microsoft Translator szöveg API szűrés Profanitás használja.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347678"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Szűrése a Microsoft Translator szöveg API-val Profanitás hozzáadása

Általában a fordító megtartja Profanitás, amely a fordítás a forráshelyen található. A fokú Profanitás és a környezetben, amelynek eredményeképpen a szavakat profán eltérő kulturális környezetek. Ennek eredményeképpen fokának Profanitás a célként megadott nyelven kiegészíthetők előfordulhat, hogy, illetve csökkenteni.

Ha azt szeretné, a fordítás (függetlenül a forrás szöveg Profanitás jelenléte) Profanitás megjelenésének elkerüléséhez, használhatja a Profanitás szűrési beállítás Translate() metódus. A beállítás lehetővé teszi annak meghatározását, hogy meg szeretné tekinteni a Profanitás törölték, vagy a megfelelő címkéket jelölésű, vagy a hajt végre semmilyen műveletet.

A Translate() metódust egy "beállítások" paraméter, amely tartalmazza az új elem "ProfanityAction" vesz igénybe. A ProfanityAction elfogadott értékei a következők: "NoAction", "Marked" és "Törölt".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Elfogadható értékek ProfanityAction és példák
|ProfanityAction érték | Műveletek | Példa: Forrás - japán | Példa: Cél - angol nyelven|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Profanitás forrásból cél továbbítja. | 彼は変態です。 | Egy jerk. |
| Megjelölve | XML-címkék tette profán szavak \<Profanitás >... \</profanity >. | 彼は変態です。 | Van egy \<Profanitás > jerk\</profanity >. |
| Törölve | Profán szavak el lesznek távolítva a kimeneti helyettesítő nélkül. | 彼は。 | Van egy. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Szűrés a fordító API-hívással Profanitás alkalmazása](reference/v3-0-translate.md)

