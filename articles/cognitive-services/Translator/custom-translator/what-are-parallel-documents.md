---
title: Mik a párhuzamos dokumentumokat? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Párhuzamos dokumentumok párjai dokumentumok ahol egyik, a másik a fordítás. A pár egyik dokumentum tartalmaz a Forrásnyelv mondatok és a többi dokumentum tartalmazza ezeket a Célnyelv fordítja mondatokat.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 10f34a24668981da5629bd9d03652aa76081bd44
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194112"
---
# <a name="what-are-parallel-documents"></a>Mik a párhuzamos dokumentumokat?

Párhuzamos dokumentumok párjai dokumentumok ahol egyik, a másik a fordítás. A pár egyik dokumentum tartalmaz a Forrásnyelv mondatok és a többi dokumentum tartalmazza ezeket a Célnyelv fordítja mondatokat.
Nem számít, hogy milyen nyelvű "forrás" van megjelölve, és milyen nyelvű "cél" van megjelölve, – bármelyik irányba fordítási rendszerüket párhuzamos dokumentum használható.

## <a name="requirements"></a>Követelmények

Szüksége lesz legalább egy rendszer betanítására 10 000 egyedi párhuzamos mondatokat. Ajánlott eljárásként, folyamatosan hozzáadhat több párhuzamos tartalmat és teljesített kapcsolat-újraépítési, a fordítási rendszer minőségének javítása érdekében.

A Microsoft megköveteli, hogy az egyéni a fordítót feltöltött dokumentumok sérti egy harmadik fél szerzői és szellemi tulajdonságai. További információkért tekintse meg a [használati](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
A portál használatával dokumentum feltöltése nem változtatja meg a dokumentum maga a szellemi tulajdonjogát.

## <a name="use-of-parallel-documents"></a>Párhuzamos dokumentumok használatát

A rendszer által használt párhuzamos dokumentumokat:

1.  Megtudhatja, hogyan szavak, a mondatok és a mondatok gyakran leképezve a két nyelvek között.

2.  További információt a megfelelő környezet függően a feldolgozóiszerepkör-kifejezések feldolgozása. Egy szó előfordulhat, hogy nem mindig fordíthatók le a más nyelven pontos ugyanazon szó.

Ajánlott eljárásként győződjön meg arról, hogy a forrás- és a dokumentumok verzióit Célnyelv 1:1 mondat eredménnyel.

Ha a projekthez adott tartomány (kategória), a dokumentumok konzisztens az adott kategórián belül terminológiája kell lennie. Az eredményül kapott fordítási rendszer minőségének a mondatok minőségét és a dokumentum beállítása a mondatok száma függ. További példák a kategóriára jellemző egy szó különböző eseténél tartalmazhatnak a dokumentumok, a jobb feladatot a rendszer teheti a fordítás során.

Dokumentumok feltöltése az egyes munkaterületeken a saját, és tetszés szerinti számú projektek vagy betanítások használt. A dokumentumok kinyert mondatokat külön a tárházban fájlként tárolt egyszerű Unicode szöveg, és érhetők el, törölje. Ne használja az egyéni a fordítót vagy a dokumentumot, nem tudják töltse le a dokumentumokat, a következő formátumban feltöltött feltöltött őket.



## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használható egy [szótár](what-is-dictionary.md) az egyéni a fordítót.
