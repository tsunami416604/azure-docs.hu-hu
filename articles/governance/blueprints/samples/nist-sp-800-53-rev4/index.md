---
title: NIST SP 800-53 R4-es tervminta – Áttekintés
description: Az NIST SP 800-53 R4 tervminta áttekintése. Ennek a tervmintának a segítségével az ügyfelek felmérhetik az NIST SP 800-53 R4 adott vezérlőit.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: e7a0acf2b43136aedd8c2281f0e87bb1499a81ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "74546581"
---
# <a name="overview-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Az NIST SP 800-53 R4 tervminta áttekintése

Az NIST SP 800-53 R4 tervminta az [Azure Policy](../../../policy/overview.md) használatával biztosít irányítási védőkorlátokat, amelyek segítségével felmérheti az NIST SP 800-53 R4 adott vezérlőit. Ez a terv lehetővé teszi az ügyfelek számára, hogy bármely olyan, Azure által üzembe helyezett architektúrához üzembe helyezzenek egy alapvető szabályzatkészletet, amelyhez implementálni kell az NIST SP 800-53 R4 vezérlőit.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A vezérlőelem-leképezésről szóló szakasz részletesen ismerteti a tervben található szabályzatokat, és hogy ezek hogyan érintik az NIST SP 800-53 R4 különböző vezérlőit. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak. További információ: [Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>Következő lépések

Ezzel megismerte az NIST SP 800-53 R4 tervminta áttekintését. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 terv – Vezérlőelem-leképezés](./control-mapping.md)
> [NIST SP 800-53 R4 terv – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
