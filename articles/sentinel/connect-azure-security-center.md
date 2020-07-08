---
title: Azure Security Center-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Ismerje meg, hogyan csatlakoztathatók a riasztások a Azure Security Center (ASC) standard csomagból, és hogyan továbbíthatók az Azure Sentinelbe.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559159"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Adatok összekötése Azure Security Centerról (ASC)

Az Azure Sentinel lehetővé teszi a riasztások összekapcsolását [Azure Security Center](../security-center/security-center-intro.md) és az Azure sentinelbe való továbbítását. 

## <a name="prerequisites"></a>Előfeltételek

- A riasztások Azure Security Centerból való exportálásához a továbbított naplók előfizetésében a biztonsági olvasó szerepkörrel kell rendelkeznie.

- Az előfizetésben az [Azure Security Center standard szintű](../security-center/security-center-pricing.md) csomagnak kell futnia. Ha nem, [frissítse az előfizetést a standard szintre](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Kapcsolódás Azure Security Centerhoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Az adatösszekötők katalógusában válassza a **Azure Security Center**lehetőséget, majd kattintson az **összekötő lap megnyitása** gombra.

1. A **konfiguráció**alatt kattintson a **Kapcsolódás** elemre minden olyan előfizetés mellett, amelynek a riasztásait továbbítani szeretné az Azure Sentinel szolgáltatásba. A csatlakozás gomb csak akkor lesz elérhető, ha rendelkezik a szükséges engedélyekkel és az ASC standard szintű előfizetéssel.

1. Kiválaszthatja, hogy a Azure Security Center riasztások automatikusan előállítanak-e incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása**területen válassza az **engedélyezve** lehetőséget az alapértelmezett elemzési szabály bekapcsolásához, amely automatikusan létrehozza az incidenseket a riasztásokból. Ezt a szabályt az **elemzés**területen módosíthatja az **aktív szabályok** lapon.

1. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure Security Center riasztásokhoz, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Security Center az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
