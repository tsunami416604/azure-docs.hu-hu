---
title: Az Advanced Threat Protection beállítása az Azure Key Vaulthoz
description: Ez a cikk bemutatja, hogyan állíthatja be a Azure Key Vault speciális veszélyforrások elleni védelmét Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 07924201d4208a502aa979dda476c57cf6edd749
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430550"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Komplex veszélyforrások elleni védelem beállítása a Azure Key Vaulthoz (előzetes verzió)

A Azure Key Vault komplex veszélyforrások elleni védelme további biztonsági intelligencia réteget biztosít. Ez az eszköz észleli a Key Vault fiókok elérését vagy kiaknázását okozó potenciálisan káros kísérleteket. A Azure Security Center natív komplex veszélyforrások elleni védelem használatával a fenyegetéseket biztonsági szakértő nélkül, a további biztonsági figyelési rendszerek megismerése nélkül kezelheti.

Amikor Security Center észleli a rendellenes tevékenységeket, riasztásokat jelenít meg. Emellett e-mailben elküldi az előfizetés-rendszergazdának a gyanús tevékenységek részleteit, és javaslatokat tesz az azonosított fenyegetések kivizsgálására és szervizelésére.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Összetett veszélyforrások elleni védelem beállítása Azure Security Center

Alapértelmezés szerint a komplex veszélyforrások elleni védelem minden Key Vault-fiók esetében engedélyezve van a Security Center Standard csomagra való előfizetéskor. További információ: [díjszabás](security-center-pricing.md).

Egy adott előfizetés védelmének engedélyezéséhez vagy letiltásához kövesse az alábbi lépéseket.

1. Security Center bal oldali ablaktábláján válassza a **díjszabás & beállítások**lehetőséget.
1. Válassza ki azt a Storage-fiókot tartalmazó előfizetést, amelyhez engedélyezni vagy tiltani szeretné a veszélyforrások elleni védelmet.
1. Válassza a **Tarifacsomag** lehetőséget,
1. Az **árképzési csomag kiválasztása erőforrástípus alapján** csoportban keresse meg a **Key Vaults** sort, és válassza az **engedélyezve** vagy a **Letiltva**lehetőséget.

    [![Key Vault speciális veszélyforrások elleni védelmének engedélyezése vagy letiltása Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Kattintson a **Mentés** gombra.


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tilthatja le a Azure Key Vault komplex veszélyforrások elleni védelmét. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikket:

- [Veszélyforrások észlelése a Security Center Azure-szolgáltatások rétegeiben](security-center-alerts-service-layer.md): Ez a cikk a Azure Key Vault komplex veszélyforrások elleni védelmével kapcsolatos riasztásokat ismerteti.
