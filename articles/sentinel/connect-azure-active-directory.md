---
title: Azure AD-adatkapcsolatok összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Azure Active Directory-adatkapcsolat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 43405bd3e97dbee325f0a5ed82c5848880775eee
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240816"
---
# <a name="connect-data-from-azure-active-directory"></a>Adatok összekapcsolásának Azure Active Directory



Az Azure Sentinel lehetővé teszi az adatok összegyűjtését [Azure Active Directoryről](../active-directory/fundamentals/active-directory-whatis.md) , és az Azure sentinelbe való továbbítását. Dönthet úgy, hogy a [bejelentkezési naplókat](../active-directory/reports-monitoring/concept-sign-ins.md) és a [](../active-directory/reports-monitoring/concept-audit-logs.md) naplókat is továbbítja.

## <a name="prerequisites"></a>Előfeltételek

- Ha a bejelentkezési adatok Active Directoryból való exportálására van szükség, rendelkeznie kell Azure AD P1 vagy P2 licenccel.

- A globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó, aki a naplókat továbbítani szeretné a bérlőn.

- A kapcsolat állapotának megtekintéséhez engedéllyel kell rendelkeznie az Azure AD diagnosztikai naplók eléréséhez. 


## <a name="connect-to-azure-ad"></a>Összekapcsolás az Azure AD-vel

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure Active Directory** csempére.

1. Kattintson a **Kapcsolódás**lehetőségre az Azure sentinelbe továbbítani kívánt naplók mellett.

1. Kiválaszthatja, hogy az Azure AD-riasztások automatikusan előállítanak-e incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok**területen módosíthatja.

1. Az Azure AD-riasztásokhoz tartozó Log Analytics vonatkozó sémájának használatához keresse meg a **SigninLogs** és a **AuditLogs**.




## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Azure AD az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
