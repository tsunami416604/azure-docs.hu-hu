---
title: 'Minta: A count operátor egy házirend-definícióban'
description: Ez az Azure-szabályzatminta egy példa arra, hogyan használhatja a count operátor egy szabályzatdefinícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172946"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy minta: a count operátor

A [count](../concepts/definition-structure.md#count) operátor kiértékeli \[ \* \] az alias tagjait.

## <a name="sample-policy-definition"></a>Mintaházirend-definíciója

Ez a házirend-definíció a bejövő távoli asztali protokoll (RDP) forgalom engedélyezésére konfigurált hálózati biztonsági csoportokat [naplózza.](../concepts/effects.md#audit)

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Magyarázat

A **count** operátor fő összetevői a _mező_, _ahol_és a feltétel. Mindegyik ki van emelve az alábbi kódrészletben.

- _mező_ megmondja, hogy melyik [aliast](../concepts/definition-structure.md#aliases) kell kiértékelni. Itt a hálózati biztonsági csoport **securityRules\[ \* ** alias _tömbjét_ vizsgáljuk.
- _ahol_ a házirend nyelvével határozza meg, hogy mely _tömbtagok_ felelnek meg a feltételeknek. Ebben a példában egy **logikai** operátor három különböző feltételértékelést csoportosít az alias _tömb_ tulajdonságaiból: _irány_, _hozzáférés_és _destinationPortRange_.
- Ebben a példában a count feltétel **nagyobb**. A count akkor felel meg igazként, ha az _aliastömb_ egy vagy több tagja megegyezik a _Where_ záradékkal.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).