---
title: Adattárolási hely
description: Adattárolás és információk az Azure arc használatára képes kiszolgálókról (előzetes verzió).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048856"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Azure arc-kompatibilis kiszolgálók (előzetes verzió): az adatrezidens

Ez a cikk az adattárolási koncepciót ismerteti, valamint azt, hogy miként vonatkozik az Azure arc-kompatibilis kiszolgálókra (előzetes verzió).

Az Azure arc használatára képes kiszolgálók (előzetes verzió) a **Egyesült Államok, Európa vagy Ázsia és a csendes-óceáni térség**előzetes kiadásában **[érhető el](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>Adattárolási hely

Az Azure arc használatára képes kiszolgálók (előzetes verzió) az Azure virtuálisgép- [bővítmény](manage-vm-extensions.md) konfigurációs beállításait (azaz a tulajdonság értékeit) tárolják, mielőtt engedélyezné a csatlakozást a csatlakoztatott gépen. Ha például engedélyezi a Log Analytics virtuálisgép-bővítményt, a Log Analytics **munkaterület-azonosítót** és az **elsődleges kulcsot**kéri.

A rendszer a csatlakoztatott géppel kapcsolatos metaadatokat is gyűjti. Ezek konkrétan a következők:

* Operációs rendszer neve és verziója
* Számítógép neve
* Számítógép teljesen minősített tartományneve (FQDN)
* Csatlakoztatott gépi ügynök verziója

Az ív használatára képes kiszolgálók (előzetes verzió) segítségével megadhatja azt a régiót, ahol az adatai tárolva lesznek. A Microsoft más régiókban is replikálhat adatrugalmasságot, de a Microsoft nem replikálja és nem helyezi át az adatátvitelt a földrajzon kívül. Ezeket az adatforrásokat abban a régióban tárolja a rendszer, ahol az Azure arc machine-erőforrás konfigurálva van. Ha például a gép az USA keleti régiójában az ív értékkel van regisztrálva, akkor ezeket az adatterületet az USA régióban tárolja.

További információ a regionális rugalmasságról és megfelelőségi támogatásról: az [Azure földrajza](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Következő lépések

További információ az [Azure rugalmasságának](/azure/architecture/reliability/architect)tervezéséről.