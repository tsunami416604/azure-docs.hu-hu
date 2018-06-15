---
title: Tekintse át a nyelvi ismertetése (LUIS) – Azure active tanulási használandó végpont utterances |} Microsoft Docs
description: A "Végpont utterances tekintse át" nevű active learning szolgáltatással teljesítmény előrejelzéseket gyorsabban javítása érdekében.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35350086"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Aktív tanulási végpont utterances megtekintésével engedélyezése
Aktív tanulási az előrejelzés pontosságát, és a végrehajtásához a legegyszerűbb javításához három stratégiák egyik. 

## <a name="what-is-active-learning"></a>Mi az az aktív learning
Aktív tanulási két lépésből áll. Először LUIS választ kap az alkalmazás végpont érvényesítési igénylő utterances. A második lépést a app tulajdonos vagy közreműködő, érvényesíteni a kiválasztott utterances [tekintse át](label-suggested-utterances.md), beleértve a megfelelő cél és a entitások belül a célt. Miután megtekintette a utterances, betanítását, és tegye közzé újból az alkalmazást. 

## <a name="which-utterances-are-on-the-review-list"></a>A felülvizsgálati listán vannak mely utterances
Leképezés kiváltó felső alacsony pontszámmal rendelkezik, vagy a felső két leképezések pontszámok túl kicsi LUIS utterances felülvizsgálati listájához ad hozzá. 

## <a name="where-are-the-utterances-from"></a>Hol találhatók a utterances
A kérelem HTTP-végpont végfelhasználói lekérdezései végpont utterances kell venni. Ha az alkalmazás nincs közzétéve, vagy még nem kapott találatok, nincs bármely utterances áttekintéséhez. Ha egy adott célt vagy entitás nincs végpont találatok, nincs utterances áttekintheti azokat. 

## <a name="schedule-review-periodically"></a>Ellenőrizze rendszeresen ütemezése
Javasolt utterances áttekintése nem szükséges minden nap végezhető, de a rendszeres karbantartást LUIS részének kell lennie. 

## <a name="delete-review-items-programmatically"></a>Programozott módon felülvizsgálati elemek törlése
Ha az alkalmazás túl nagy, előfordulhat, hogy tekintse át az egyes utterances és választja programozott módon törlése a listáról a többi. Ehhez először [első](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) a listában, majd [törlése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) a utterances által azonosítóját.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [tekintse át](Label-Suggested-Utterances.md) végpont utterances
