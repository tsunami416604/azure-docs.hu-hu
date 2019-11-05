---
title: Komplex veszélyforrások elleni védelem beállítása a Azure Key Vaulthoz | Microsoft Docs
description: Ez a cikk bemutatja, hogyan állíthatja be a Azure Key Vault speciális veszélyforrások elleni védelmét Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521877"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Komplex veszélyforrások elleni védelem beállítása a Azure Key Vaulthoz (előzetes verzió)

A Azure Key Vault komplex veszélyforrások elleni védelme további biztonsági intelligencia réteget biztosít. Ez az eszköz észleli a Key Vault fiókok elérését vagy kiaknázását okozó potenciálisan káros kísérleteket. A Security Center natív komplex veszélyforrások elleni védelmének használatával biztonsági szakértő nélkül kezelheti a fenyegetéseket, és nem kell további biztonsági figyelési rendszereket megtanulnia.

Amikor Security Center észleli a rendellenes tevékenységeket, riasztásokat jelenít meg. Emellett e-mailben elküldi az előfizetés-rendszergazdának a gyanús tevékenységek részleteit, és javaslatokat tesz az azonosított fenyegetések kivizsgálására és szervizelésére. 

> [!NOTE]
> A Azure Key Vault komplex veszélyforrások elleni védelme jelenleg csak Észak-Amerika régiókban érhető el.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Összetett veszélyforrások elleni védelem beállítása Azure Security Center

Alapértelmezés szerint a komplex veszélyforrások elleni védelem minden Key Vault-fiók esetében engedélyezve van a Security Center Standard szintjére való előfizetéskor (lásd a [díjszabást](security-center-pricing.md)). 

Egy adott előfizetés védelmének engedélyezése vagy letiltása:

1. A Security Center oldalsávján kattintson a **díjszabás & beállítások**elemre.
1. Válassza ki azt a Storage-fiókot tartalmazó előfizetést, amelyhez engedélyezni vagy tiltani szeretné a veszélyforrások elleni védelmet.
1. Kattintson a **díjszabási**csomag elemre.
1. Az **árképzési csomag kiválasztása erőforrástípus alapján** csoportban keresse meg a Key Vaults sort, és kattintson az **engedélyezve** vagy a **Letiltva**lehetőségre.
    [![Key Vault speciális veszélyforrások elleni védelmének engedélyezése vagy letiltása Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Kattintson a **Save** (Mentés) gombra.


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tilthatja le a Azure Key Vault komplex veszélyforrások elleni védelmét. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikket:

- [Veszélyforrások észlelése a Security Center Azure-szolgáltatások rétegeiben](security-center-alerts-service-layer.md) – ez a cikk a komplex veszélyforrások elleni védelemmel kapcsolatos riasztásokat ismerteti Azure Key Vault