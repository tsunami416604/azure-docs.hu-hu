---
title: Veszélyforrások elleni védelem Azure Key Vault
description: Ez a cikk bemutatja, hogyan állíthatja be a Azure Key Vault speciális veszélyforrások elleni védelmét Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77914806"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Veszélyforrások elleni védelem Azure Key Vault (előzetes verzió)

A Azure Key Vault komplex veszélyforrások elleni védelme további biztonsági intelligencia réteget biztosít. Ez az eszköz észleli a Key Vault fiókok elérését vagy kiaknázását okozó potenciálisan káros kísérleteket. A Azure Security Center natív komplex veszélyforrások elleni védelem használatával a fenyegetéseket biztonsági szakértő nélkül, a további biztonsági figyelési rendszerek megismerése nélkül kezelheti.

Amikor Security Center észleli a rendellenes tevékenységeket, riasztásokat jelenít meg. Emellett e-mailben elküldi az előfizetés-rendszergazdának a gyanús tevékenységek részleteit, és javaslatokat tesz az azonosított fenyegetések kivizsgálására és szervizelésére.

## <a name="configuring-threat-protection-from-security-center"></a>Veszélyforrások elleni védelem konfigurálása Security Center

Alapértelmezés szerint a komplex veszélyforrások elleni védelem minden Key Vault-fiók esetében engedélyezve van, amikor előfizet a Security Center Standard díjszabási szintjére. További információ: [díjszabás](security-center-pricing.md).

Egy adott előfizetés védelmének engedélyezése vagy letiltása:

1. Security Center bal oldali ablaktábláján válassza a **díjszabás & beállítások**lehetőséget.

1. Válassza ki azt a Storage-fiókot tartalmazó előfizetést, amelyhez engedélyezni vagy tiltani szeretné a veszélyforrások elleni védelmet.

1. Válassza a **Tarifacsomag** lehetőséget,

1. Az **árképzési csomag kiválasztása erőforrástípus alapján** csoportban keresse meg a **Key Vaults** sort, és válassza az **engedélyezve** vagy a **Letiltva**lehetőséget.

    [![A Key Vault komplex veszélyforrások elleni védelmének engedélyezése vagy letiltása Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Kattintson a **Mentés** gombra.


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tilthatja le a Azure Key Vault komplex veszélyforrások elleni védelmét. 

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket:

- [Veszélyforrások elleni védelem Azure Security Centerban](threat-protection.md)– ez a cikk a Azure Security Center biztonsági riasztások forrásait ismerteti.
- [Key Vault biztonsági riasztások](alerts-reference.md#alerts-azurekv)– az összes Azure Security Center riasztáshoz tartozó hivatkozási tábla Key Vault szakasza