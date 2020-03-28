---
title: 'Minta: Csoportházirend-definíciók kezdeményezésekkel'
description: Ez az Azure-szabályzati minta példát mutat be arra, hogyan csoportosíthatja a szabályzatdefiníciókat egy kezdeményezésbe
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172855"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure-szabályzatminta: csoportházirend-definíciók

A kezdeményezés a házirend-definíciók csoportja. Ha a kapcsolódó házirend-definíciókat egyetlen objektumba csoportosítja, létrehozhat egyetlen hozzárendelést, amely több hozzárendelés lett volna.

## <a name="sample-initiative-definition"></a>Mintakezdeményezés meghatározása

Ez a kezdeményezés két házirend-definíciót telepít, amelyek mindegyike a **tagName** és **a tagValue** paramétereket veszi fel. Magának a kezdeményezésnek két paramétere van: **costCenterValue** és **productNameValue**.
Ezek a kezdeményezési paraméterek mindegyik csoportba sorolt házirend-definícióhoz tartoznak. Ez a kialakítás maximalizálja a meglévő házirend-definíciók újrafelhasználását, miközben korlátozza a végrehajtásukhoz létrehozott hozzárendelések számát, ha szükséges.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Magyarázat

#### <a name="initiative-parameters"></a>Kezdeményezési paraméterek

Egy kezdeményezés meghatározhatja saját paramétereit, amelyeket acsoportosított házirend-definíciók nak továbbítanak.
Ebben a példában mind **a costCenterValue,** mind a **productNameValue** kezdeményezési paraméterekként van definiálva. Az értékek a kezdeményezés hozzárendelésekéneknél kerülnek megadva.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Házirend-definíciókat tartalmaz

Minden tartalmazott házirend-definíciónak meg kell adnia a **policyDefinitionId azonosítót** és egy **paramétertömböt,** ha a házirend-definíció elfogadja a paramétereket. Az alábbi kódrészletben a mellékelt házirend-definíció két paramétert vesz igénybe: **tagName** és **tagValue**. **a tagName** konstans, de **a tagValue** a kezdeményezés által meghatározott **costCenterValue** paramétert használja. Az értékek ezen áteressének javítása javítja az újrafelhasználást.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).