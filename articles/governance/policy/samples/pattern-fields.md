---
title: 'Minta: Mezőtulajdonságok egy házirend-definícióban'
description: Ez az Azure Policy minta egy példa arra, hogyan kell használni a mező tulajdonságait egy szabályzat definíciójában.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172862"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy minta: mező tulajdonságok

A [mezőoperátor](../concepts/definition-structure.md#fields) a megadott tulajdonságot vagy [aliast](../concepts/definition-structure.md#aliases) egy adott feltétel hez megadott értékre [értékeli](../concepts/definition-structure.md#conditions)ki.

## <a name="sample-policy-definition"></a>Mintaházirend-definíciója

Ez a házirend-definíció lehetővé teszi, hogy olyan engedélyezett régiókat határozzon meg, amelyek megfelelnek a szervezet földrajzi helymeghatározási követelményeinek. Az engedélyezett erőforrások a **parameter listOfAllowedLocations** (_tömb_) paraméterlistában vannak definiálva. A definíciónak megfelelő erőforrások at a (d) megtagadta a [definíciónak.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

A **mező** operátorháromszor használatos az **allOf** [logikai operátoron](../concepts/definition-structure.md#logical-operators) belül.

- Az első használat `location` kiértékeli a tulajdonságot a **notIn** feltétellel a **listOfAllowedLocations** paraméterre. **notIn** működik, mert elvárja a _tömb,_ és a paraméter egy _tömb_. Ha `location` a létrehozott vagy frissített erőforrás nem szerepel a jóváhagyott listában, akkor ez az elem igaz értéket ad ki.
- A második használat is `location` kiértékeli a tulajdonságot, de a **notEquals** feltételt használja annak megállapítására, hogy az erőforrás _globális-e._ Ha `location` a létrehozott vagy frissített erőforrás nem _globális,_ ez az elem igaz értéket ad ki.
- Az utolsó használat `type` kiértékeli a tulajdonságot, és a **notEquals** feltételt használja az erőforrástípus érvényesítéséhez nem _Microsoft.AzureActiveDirectory/b2cDirectories_. Ha nem, akkor ez az elem igaz értéket ad ki.

Ha az **allOf** logikai operátor mindhárom feltételutasítása igaz értéket értékel ki, az Azure-szabályzat blokkolja az erőforrás-létrehozást vagy -frissítést.

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).