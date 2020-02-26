---
title: Azure Security Center-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Megtudhatja, hogyan csatlakoztatható Azure Security Center-adatkapcsolat az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588535"
---
# <a name="connect-data-from-azure-security-center"></a>Adatok összekapcsolásának Azure Security Center





Az Azure Sentinel lehetővé teszi a riasztások összekapcsolását [Azure Security Center](../security-center/security-center-intro.md) és az Azure sentinelbe való továbbítását. 

## <a name="prerequisites"></a>Előfeltételek

- A riasztások Azure Security Centerból való exportálásához a továbbított naplók előfizetésében a biztonsági olvasó szerepkörrel kell rendelkeznie.

- Az előfizetésben az [Azure Security Center standard szintű](../security-center/security-center-pricing.md) csomagnak kell futnia. Ha nem, [frissítse az előfizetést a standard szintre](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Kapcsolódás Azure Security Centerhoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure Security Center** csempére.

1. A jobb oldalon kattintson a **Kapcsolódás** elemre minden olyan előfizetés mellett, amelynek a riasztásait továbbítani szeretné az Azure Sentinel szolgáltatásba. Ügyeljen arra, hogy az egyes előfizetéseket Azure Security Center Standard szintre frissítse, hogy a riasztásokat továbbítsa az Azure Sentinelnek.

1. Kiválaszthatja, hogy a riasztások a Azure Security Center automatikusan előállítsák-e az incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok**területen módosíthatja.

3. Kattintson a **Csatlakozás** gombra.

4. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure Security Center riasztásokhoz, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Security Center az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
