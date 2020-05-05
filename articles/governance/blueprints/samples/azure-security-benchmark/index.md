---
title: Azure-biztonsági teljesítményteszt tervmintájának áttekintése
description: Az Azure-biztonsági teljesítményteszt tervmintájának áttekintése. Ennek a tervmintának a segítségével az ügyfelek adott vezérlőket mérhetnek fel.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: b7ac8c053bc909f4b0ae605b179e24847fceb3e1
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82701650"
---
# <a name="overview-of-the-azure-security-benchmark-blueprint-sample"></a>Az Azure-biztonsági teljesítményteszt tervmintájának áttekintése

Az Azure-biztonsági teljesítményteszt az [Azure Policy](../../../policy/overview.md) használatával biztosít irányítási korlátokat, amelyek segítségével felmérheti az [Azure-biztonsági teljesítményteszt](../../../../security/benchmarks/overview.md) adott vezérlőit. Ez a tervminta lehetővé teszi az ügyfelek számára, hogy bármely olyan, az Azure által üzembe helyezett architektúrához üzembe helyezzenek egy alapvető szabályzatkészletet, amelyhez implementálni szeretnék az Azure-biztonsági teljesítményteszt vezérlőit.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A vezérlőelem-leképezésről szóló szakasz részletesen ismerteti a tervben található szabályzatokat, és hogy ezek hogyan érintik az Azure-biztonsági teljesítményteszt vezérlőit. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak. További információ: [Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>További lépések

Megismerte az Azure-biztonsági teljesítményteszt tervmintájának áttekintését.
Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlők leképezését ismertetik:

> [!div class="nextstepaction"]
> [Azure-biztonsági teljesítményteszt terve – Vezérlőelem-leképezés](./control-mapping.md)
> [Azure-biztonsági teljesítményteszt terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.