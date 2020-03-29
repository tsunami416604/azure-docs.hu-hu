---
title: Fenyegetéselleni védelem az Azure Key Vaultszámára
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a speciális veszélyforrások elleni védelmet az Azure Key Vault hoz az Azure Security Centerben
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914806"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Fenyegetéselleni védelem az Azure Key Vaulthoz (előzetes verzió)

Az Azure Key Vault speciális veszélyforrások elleni védelme további biztonsági intelligenciaréteget biztosít. Ez az eszköz észleli a Key Vault-fiókok elérésére vagy kihasználására irányuló potenciálisan káros kísérleteket. Az Azure Security Center natív speciális veszélyforrások elleni védelmét anélkül is kezelheti, hogy biztonsági szakértő lenne, és további biztonsági figyelőrendszereket tanulna.

Amikor a Security Center rendellenes tevékenységet észlel, riasztásokat jelenít meg. Emellett e-mailben az előfizetés rendszergazdája a gyanús tevékenység részleteit, és ajánlásokat, hogyan vizsgálja meg és orvosolja az azonosított fenyegetéseket.

## <a name="configuring-threat-protection-from-security-center"></a>Fenyegetésvédelem konfigurálása a Biztonsági központból

Alapértelmezés szerint a speciális veszélyforrások elleni védelem engedélyezve van az összes Key Vault-fiókhoz, amikor előfizet a Security Center szabványos tarifacsomagra. További információ: [Pricing](security-center-pricing.md).

Egy adott előfizetés védelmének engedélyezése vagy letiltása:

1. A Biztonsági központ bal oldali ablaktáblájában válassza **a Díjszabás i & beállítás lehetőséget.**

1. Válassza ki az előfizetést azokkal a tárfiókokkal, amelyekhez engedélyezni vagy letiltani szeretné a veszélyforrások elleni védelmet.

1. Válassza a **Tarifacsomag** lehetőséget,

1. A **Tarifacsomag kiválasztása erőforrástípus szerint** csoportban keresse meg a **Key Vaults** sort, és válassza az **Engedélyezve** vagy **a Letiltva**lehetőséget.

    [![Speciális veszélyforrások elleni védelem engedélyezése vagy letiltása key vaultban az Azure Security Centerben](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Kattintson a **Mentés** gombra.


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tilthatja le az Azure Key Vault speciális veszélyforrások elleni védelmét. 

A kapcsolódó anyagokat lásd a következő cikkekben:

- [Fenyegetésvédelem az Azure Security Centerben](threat-protection.md)– Ez a cikk ismerteti az Azure Security Center biztonsági riasztások forrásait.
- [Key Vault biztonsági riasztások](alerts-reference.md#alerts-azurekv)- A referenciatábla Key Vault szakasza az összes Azure Security Center-riasztáshoz