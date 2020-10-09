---
title: Adattárolási hely
description: Adatok tartózkodási helye és információk az Azure arc használatára képes kiszolgálókról.
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856449"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure arc-kompatibilis kiszolgálók: az adatrezidens

Ez a cikk az adattárolási koncepciót ismerteti, valamint azt, hogy hogyan vonatkozik az Azure arc-kompatibilis kiszolgálókra.

Az Azure arc-kompatibilis kiszolgálók a **Egyesült Államokban, Európában, az Egyesült Királyságban, Ausztráliában és Ázsia és a csendes-óceáni térség** **[érhetők el](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>Adattárolási hely

Az Azure arc használatára képes kiszolgálók tárolják az Azure virtuálisgép- [bővítmény](manage-vm-extensions.md) konfigurációs beállításait (azaz a tulajdonság értékeit) a bővítménynek meg kell határoznia a csatlakozást, mielőtt engedélyezné a csatlakoztatott számítógépen. Ha például engedélyezi a Log Analytics virtuálisgép-bővítményt, a Log Analytics **munkaterület-azonosítót** és az **elsődleges kulcsot**kéri.

A rendszer a csatlakoztatott géppel kapcsolatos metaadatokat is gyűjti. Ezek konkrétan a következők:

* Operációs rendszer neve és verziója
* Számítógép neve
* Számítógép teljesen minősített tartományneve (FQDN)
* Csatlakoztatott gépi ügynök verziója

Az ív használatára képes kiszolgálók lehetővé teszik, hogy megadja a régiót, ahol az adatait tárolni fogja. A Microsoft más régiókban is replikálhat adatrugalmasságot, de a Microsoft nem replikálja és nem helyezi át az adatátvitelt a földrajzon kívül. Ezeket az adatforrásokat abban a régióban tárolja a rendszer, ahol az Azure arc machine-erőforrás konfigurálva van. Ha például a gép az USA keleti régiójában az ív értékkel van regisztrálva, akkor ezeket az adatterületet az USA régióban tárolja.

További információ a regionális rugalmasságról és megfelelőségi támogatásról: az [Azure földrajza](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>További lépések

További információ az [Azure rugalmasságának](/azure/architecture/reliability/architect)tervezéséről.