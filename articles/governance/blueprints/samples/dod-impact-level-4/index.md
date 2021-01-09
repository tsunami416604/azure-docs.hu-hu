---
title: A DoD Impact Level 4 tervmintájának áttekintése
description: Áttekintheti a DoD Impact Level 4 mintáját. Ez a tervminta segít az ügyfeleknek a DoD Impact Level 4 egyes vezérlőinek felmérésében.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 3fdb14d1abdac182dceed844fe8b9ab65bed3210
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035087"
---
# <a name="overview-of-the-dod-impact-level-4-blueprint-sample"></a>A DoD Impact Level 4 tervminta áttekintése

A Department of Defense Impact Level 4 (DoD IL4, Egyesült Államok Védelmi Minisztériuma, 4. hatásszint) tervminta az [Azure Policy](../../../policy/overview.md) használatával biztosít irányítási korlátokat, amelyek segítségével felmérheti a DoD Impact Level 4 adott vezérlőit. Ez a tervminta lehetővé teszi az ügyfelek számára, hogy bármely olyan, az Azure által üzembe helyezett architektúrához üzembe helyezzenek egy alapvető szabályzatkészletet, amelyhez implementálni kell a DoD Impact Level 4 vezérlőit. A DoD Impact Level 4 szintű hitelesítésnek megfelelő Azure-felhőkkel és -szolgáltatásokkal kapcsolatos legfrissebb információkért lásd [az Azure-szolgáltatások FedRAMP- és DoD CC SRG-naplózási hatókör szerinti csoportosításával foglalkozó részt](../../../../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

> [!NOTE]
> Ez a tervminta elérhető az Azure Governmentben.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A vezérlőelem-leképezésről szóló szakasz részletesen ismerteti a tervben található szabályzatokat és azt, hogy ezek hogyan érintik a DoD Impact Level 4 különböző vezérlőit. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak. További információ: [Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>További lépések

Áttekintette a DoD Impact Level 4 tervmintáját. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [DoD Impact Level 4 terv – Vezérlőelem-leképezés](./control-mapping.md)
> [DoD Impact Level 4 terv - Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.