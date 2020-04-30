---
title: 'Minta: mezőtulajdonságok a házirend-definícióban'
description: Ez a Azure Policy minta azt szemlélteti, hogyan használható a Mezőtulajdonságok a házirend-definícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172862"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy minta: mező tulajdonságai

A [mező](../concepts/definition-structure.md#fields) operátor kiértékeli a megadott tulajdonságot vagy [aliast](../concepts/definition-structure.md#aliases) egy megadott értékre egy adott [feltételnél](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Példa a házirend-definícióra

Ez a szabályzat-definíció lehetővé teszi, hogy olyan engedélyezett régiókat határozzon meg, amelyek megfelelnek a szervezet földrajzi elhelyezkedési követelményeinek. Az engedélyezett erőforrások a **listOfAllowedLocations** (_Array_) paraméterben vannak meghatározva. A definíciónak megfelelő erőforrások [megtagadva](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

A **mező** operátor háromszor van használatban a [logikai operátor](../concepts/definition-structure.md#logical-operators) **allOf**.

- Az első használat kiértékeli `location` a tulajdonságot a **notIn** feltétellel a **listOfAllowedLocations** paraméterrel. a **notIn** úgy működik, ahogy egy _tömböt_ vár, és a paraméter egy _tömb_. `location` Ha a létrehozott vagy frissített erőforrás nem szerepel a jóváhagyott listán, ez az elem igaz értéket ad vissza.
- A második használat a `location` tulajdonságot is kiértékeli, de a **notEquals** feltételt használva ellenőrzi, hogy az erőforrás _globális_-e. `location` Ha a létrehozott vagy frissített erőforrás nem _globális_, ez az elem igaz értéket ad vissza.
- Az utolsó használat kiértékeli `type` a tulajdonságot, és a **notEquals** feltételt használja az erőforrástípus érvényesítéséhez, nem a _Microsoft. AzureActiveDirectory/b2cDirectories_. Ha nem, akkor ez az elem igaz értéket ad vissza.

Ha a **allOf** logikai operátor mindhárom feltétel-utasítása igaz értéket ad meg, a Azure Policy blokkolja az erőforrás-létrehozási vagy-frissítési műveletet.

## <a name="next-steps"></a>További lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).