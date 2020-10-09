---
title: ISO 27001-tervminta – Áttekintés
description: Az ISO 27001-tervminta áttekintése. Ennek a tervmintának a segítségével az ügyfelek felmérhetik az ISO 27001 adott vezérlőit.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 96156b592b7946a383b29ff701ac1f89a9e504fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86536799"
---
# <a name="overview-of-the-iso-27001-blueprint-sample"></a>Az ISO 27001-tervminta áttekintése

Az ISO 27001-tervminta az [Azure Policy](../../../policy/overview.md) használatával biztosít irányítási védőkorlátokat, amelyek segítségével felmérheti az ISO 27001 adott vezérlőit. Ez a terv lehetővé teszi az ügyfelek számára, hogy bármely olyan, Azure által üzembe helyezett architektúrához üzembe helyezzenek egy alapvető szabályzatkészletet, amelyhez implementálni kell az ISO 27001 vezérlőit. Két további ISO 27001-tervminta érhető el, amelyek segítségével [alapvető architektúrát](../iso27001-shared/index.md) és egy [ASE/SQL számítási feladatot](../iso27001-ase-sql-workload/index.md) helyezhet üzembe.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A vezérlőelem-leképezésről szóló szakasz részletesen ismerteti a tervben található szabályzatokat, és hogy ezek hogyan érintik az ISO 27001 különböző vezérlőit. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak. További információ: [Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>További lépések

Ezzel megismerte az ISO 27001 tervmintájának áttekintését és architektúráját.
Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [ISO 27001-terv – Vezérlőelem-leképezés](./control-mapping.md)
> [ISO 27001-terv – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
