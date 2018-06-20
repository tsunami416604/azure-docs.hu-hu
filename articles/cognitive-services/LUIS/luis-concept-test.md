---
title: A alkalmazás tesztelése a LUIS - Azure |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) segítségével az alkalmazás számára, pontosítsa és a nyelvi megértése folyamatosan működik.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8c702d2adbadd2736eed05c7580e8aabf69affbf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266328"
---
# <a name="testing-in-luis"></a>LUIS tesztelése

Tesztelés az a folyamat LUIS minta utterances biztosítása és LUIS felismerhető leképezések és entitások választ kap. 

Is [tesztelése](interactive-test.md) LUIS interaktív módon, egy utterance egyszerre, vagy adjon meg egy [kötegelt](luis-concept-batch-test.md) a utterances. Tesztelési, akkor összehasonlíthatja az aktuális [aktív](luis-concept-version.md#active-version) a közzétett modell modellre. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Mi az a tesztelése pontszámot?
Lásd: [előrejelzés pontszám](luis-concept-prediction-score.md) koncepciókról további információk előrejelzés pontszámait.

## <a name="interactive-testing"></a>Interaktív tesztelése
Interaktív tesztelés végezheti el a **teszt** a webhely panel. Megadhat egy utterance hogyan leképezések és entitások azonosított és pontozza a mennyiségeket. Ha a tesztelés ablaktáblán egy utterance a várt módon a leképezések és entitások nem előrejelzésére LUIS, másolja a **leképezés** egy új utterance a lapra. Ezután címke adott utterance részeit, és LUIS betanításához. 

## <a name="batch-testing"></a>Kötegelt tesztelése
Lásd: [kötegelt tesztelés](luis-concept-batch-test.md) egyszerre egynél több utterance teszteléséhez.

## <a name="endpoint-testing"></a>Végpont tesztelése
Segítségével tesztelheti a [végpont](luis-glossary.md#endpoint) az alkalmazás két verziója legfeljebb. A fő vagy élő verziójával állítja be az alkalmazás a **éles** végpont, adja hozzá a második verzióra a **átmeneti** végpont. Ez a megközelítés lehetővé teszi az olyan utterance három verzióját: az aktuális modell vizsgálati panelén a [LUIS] [ LUIS] webhelyet, és a két különböző végponton két verziója. 

Összes végpont tesztelési száma a memóriahasználati kvóta felé. 

## <a name="do-not-log-tests"></a>Nem jelentkezhet tesztek
Ha a második megvizsgál egy végpontot, és nem szeretné, a naplózott utterance, fontos, hogy a `logging=false` konfiguráció lekérdezés-karakterlánc.

## <a name="where-to-find-utterances"></a>Hol található utterances
LUIS összes naplózott utterances tárolja a lekérdezési napló, a letölthető a [LUIS] [ LUIS] webhely **alkalmazások** lista lap, valamint a LUIS [szerzői API-k ](https://aka.ms/luis-authoring-apis). 

Bármely utterances LUIS nem szerepelnek a **[tekintse át a végpont utterances](label-suggested-utterances.md)** oldalán a [LUIS] [ LUIS] webhelyet. 

![Tekintse át a végpont utterances](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Ne felejtse el képzése
Ne felejtse el [betanítása](luis-how-to-train.md) LUIS után módosítja a modellt. A LUIS alkalmazás nem látják a tesztelése, amíg az alkalmazás be van tanítva. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

* További információ [tesztelés](interactive-test.md) a utterances.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions