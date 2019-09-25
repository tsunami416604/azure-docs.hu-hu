---
title: Azure Security Center-adatkapcsolatok csatlakoztatása az Azure Sentinelhez | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Azure Security Center-adatkapcsolat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240217"
---
# <a name="connect-data-from-azure-security-center"></a>Adatok összekapcsolásának Azure Security Center





Az Azure Sentinel lehetővé teszi a riasztások összekapcsolását [Azure Security Center](../security-center/security-center-intro.md) és az Azure sentinelbe való továbbítását. 

## <a name="prerequisites"></a>Előfeltételek

- Ha Azure Security Centerről szeretne riasztásokat exportálni, akkor olyan előfizetésben közreműködőnek kell lennie, amelyik a naplókat továbbítja.

- Az előfizetésben az [Azure Security Center standard szintű](../security-center/security-center-pricing.md) csomagnak kell futnia. Ha nem, [frissítse az előfizetést a standard szintre](https://azure.microsoft.com/pricing/details/security-center/).

- Olyan felhasználóval kell bejelentkeznie, amely globális rendszergazdai vagy biztonsági rendszergazdai jogosultságokkal rendelkezik minden olyan előfizetéshez, amelyhez csatlakozni szeretne.


## <a name="connect-to-azure-security-center"></a>Kapcsolódás Azure Security Centerhoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure Security Center** csempére.

1. A jobb oldalon kattintson a **Kapcsolódás** elemre minden olyan előfizetés mellett, amelynek a riasztásait továbbítani szeretné az Azure Sentinel szolgáltatásba. Ügyeljen arra, hogy az egyes előfizetéseket Azure Security Center Standard szintre frissítse, hogy a riasztásokat továbbítsa az Azure Sentinelnek.

1. Kiválaszthatja, hogy a riasztások a Azure Security Center automatikusan előállítsák-e az incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok**területen módosíthatja.

3. Kattintson a **Csatlakozás** gombra.

4. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure Security Center riasztásokhoz, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Security Center az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
