---
title: Adattárolási hely
description: Adatok tartózkodási helye és információk az Azure arc használatára képes kiszolgálókról.
ms.topic: reference
ms.date: 09/02/2020
ms.custom: references_regions
ms.openlocfilehash: 8b4b8171bd7133e52928a5227c488bd6234ce686
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908129"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure arc-kompatibilis kiszolgálók: az adatrezidens

Ez a cikk az adattárolási koncepciót ismerteti, valamint azt, hogy hogyan vonatkozik az Azure arc-kompatibilis kiszolgálókra.

Az Azure arc-kompatibilis kiszolgálók a **Egyesült Államokban, Európában vagy Ázsia és a csendes-óceáni térség** **[érhetők el](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>Adattárolási hely

Az Azure arc használatára képes kiszolgálók tárolják az Azure virtuálisgép- [bővítmény](manage-vm-extensions.md) konfigurációs beállításait (azaz a tulajdonság értékeit) a bővítménynek meg kell határoznia a csatlakozást, mielőtt engedélyezné a csatlakoztatott számítógépen. Ha például engedélyezi a Log Analytics virtuálisgép-bővítményt, a Log Analytics **munkaterület-azonosítót** és az **elsődleges kulcsot**kéri.

A rendszer a csatlakoztatott géppel kapcsolatos metaadatokat is gyűjti. Ezek konkrétan a következők:

* Operációs rendszer neve és verziója
* Számítógép neve
* Számítógép teljesen minősített tartományneve (FQDN)
* Csatlakoztatott gépi ügynök verziója

Az ív használatára képes kiszolgálók lehetővé teszik, hogy megadja a régiót, ahol az adatait tárolni fogja. A Microsoft más régiókban is replikálhat adatrugalmasságot, de a Microsoft nem replikálja és nem helyezi át az adatátvitelt a földrajzon kívül. Ezeket az adatforrásokat abban a régióban tárolja a rendszer, ahol az Azure arc machine-erőforrás konfigurálva van. Ha például a gép az USA keleti régiójában az ív értékkel van regisztrálva, akkor ezeket az adatterületet az USA régióban tárolja.

További információ a regionális rugalmasságról és megfelelőségi támogatásról: az [Azure földrajza](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Következő lépések

További információ az [Azure rugalmasságának](/azure/architecture/reliability/architect)tervezéséről.