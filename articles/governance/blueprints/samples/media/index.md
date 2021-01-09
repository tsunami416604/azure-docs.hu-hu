---
title: Médiatervminta áttekintése
description: A Médiatervminta áttekintése. Ennek a tervmintának a segítségével az ügyfelek adott médiavezérlőket mérhetnek fel.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: f2b9e29d8f5095947587107cc372beb34b18abb7
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034985"
---
# <a name="overview-of-the-media-blueprint-sample"></a>Médiatervminta áttekintése

A Médiatervminta az [Azure Policy](../../../policy/overview.md) segítségével biztosít irányítási védőkorlátokat, amelyek elősegítik a [Média](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)-igazolás megszerzését. 

## <a name="blueprint-sample"></a>Tervminta

Ez a tervminta segít az ügyfeleknek egy alapvető szabályzatkészlet bármilyen olyan architektúrában történő üzembe helyezésében, amely az Azure-ban van üzembe helyezve, és a Média keretrendszerére vonatkozó akkreditációt vagy megfelelőséget igényel. A [vezérlőelem-leképezésről](./control-mapping.md) szóló szakasz részletesen ismerteti a kezdeményezésben található szabályzatokat, és hogy ezek hogyan segítik elő a Média keretrendszere által meghatározott különböző vezérlőknek való megfelelést. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak.

## <a name="next-steps"></a>További lépések

Ezzel megismerte a Médiatervminta áttekintését. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [Médiaterv – Vezérlőelem-leképezés](./control-mapping.md)
> [Médiaterv – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.