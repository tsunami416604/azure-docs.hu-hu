---
title: Azure Active Directory-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Azure Active Directory-adatkapcsolat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5ec8a37a0c782a5426b87f785af2d047ca35aa22
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374814"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Adatok összekötése Azure Active Directoryról (Azure AD)



Az Azure Sentinel lehetővé teszi az adatok összegyűjtését [Azure Active Directoryről](../active-directory/fundamentals/active-directory-whatis.md) , és az Azure sentinelbe való továbbítását. Dönthet úgy, hogy a [bejelentkezési naplókat](../active-directory/reports-monitoring/concept-sign-ins.md) [és a naplókat](../active-directory/reports-monitoring/concept-audit-logs.md) is továbbítja.

## <a name="prerequisites"></a>Előfeltételek

- Ha a bejelentkezési adatait az Azure AD-ből szeretné exportálni, rendelkeznie kell Azure AD P1 vagy P2 licenccel.

- A globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó, aki a naplókat továbbítani szeretné a bérlőn.

- A kapcsolat állapotának megtekintéséhez engedéllyel kell rendelkeznie az Azure AD diagnosztikai naplók eléréséhez. 


## <a name="connect-to-azure-active-directory"></a>Kapcsolódás Azure Active Directoryhoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Az adatösszekötők katalógusában válassza a **Azure Active Directory** lehetőséget, majd kattintson az **összekötő megnyitása lap** gombra.

1. Jelölje be az Azure Sentinelbe továbbítani kívánt naplók melletti jelölőnégyzeteket, majd kattintson a **Kapcsolódás**gombra.

1. Kiválaszthatja, hogy az Azure AD-riasztások automatikusan előállítanak-e incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok**területen módosíthatja.

1. Ha az Azure AD-riasztások lekérdezéséhez a Log Analytics vonatkozó sémát szeretné használni, írja be `SigninLogs` a vagy értéket `AuditLogs` a lekérdezési ablakba.




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Active Directory az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
