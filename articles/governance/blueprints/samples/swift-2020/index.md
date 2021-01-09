---
title: SWIFT CSP-CSCF v2020-tervminta – Áttekintés
description: A SWIFT CSP-CSCF v2020-tervminta áttekintése. Ennek a tervmintának a segítségével az ügyfelek felmérhetik a SWIFT CSP-CSCF adott vezérlőit.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 2fc2aa437cc6e69df3c5ae7c3a0acedfba67e636
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048742"
---
# <a name="overview-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>A SWIFT CSP-CSCF v2020-tervminta áttekintése

A SWIFT CSP-CSCF v2020-tervminta az [Azure Policy](../../../policy/overview.md) használatával biztosít irányítási védőkorlátokat, amelyekkel felmérheti a SWIFT CSP adott vezérlőit. Ez a tervminta lehetővé teszi az ügyfelek számára, hogy bármely olyan, Azure által üzembe helyezett architektúrához üzembe helyezzenek egy alapvető szabályzatkészletet, amelyhez implementálni kell a SWIFT CSP vezérlőit.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A vezérlőelem-leképezésről szóló szakasz részletesen ismerteti a tervben található szabályzatokat, és hogy ezek hogyan érintik a legújabb SWIFT CSP-CSCF vezérlőit. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak. További információ: [Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>További lépések

Áttekintette a SWIFT CSP-CSCF v2020-tervmintát. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF v2020-terv – Vezérlőelem-leképezés](./control-mapping.md)
> [SWIFT CSP-CSCF v2020-terv – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.