---
title: Azure AD Identity Protection-adatkapcsolatok csatlakoztatása az Azure Sentinelhez | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Azure AD Identity Protection-adatkapcsolat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: de8f31a02b62164a8a6b099a90297a207aefae80
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240801"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Adatok összekapcsolásának Azure AD Identity Protection



A naplók a Azure AD Identity Protectionból [](https://docs.microsoft.com/azure/information-protection/reports-aip) az Azure sentinelbe továbbítva továbbítják a riasztásokat az Azure Sentinel szolgáltatásba az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Azure Active Directory Identity Protection összevont nézetet biztosít a veszélyeztetett felhasználók, a kockázati észlelések és a sebezhetőségek számára, és lehetővé teszi a kockázatok azonnali javítását, és szabályzatok beállítását a jövőbeli események automatikus szervizeléséhez. A szolgáltatás a Microsoft által a fogyasztói identitások védelmét szolgáló tapasztalatra épül, és egy nap alatt több mint 13 000 000 000-es bejelentkezésből származó, hatalmas pontosságot nyer. 


## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy [prémium szintű Azure Active Directory P1 vagy P2 licenccel](https://azure.microsoft.com/pricing/details/active-directory/)
- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó


## <a name="connect-to-azure-ad-identity-protection"></a>Kapcsolódás Azure AD Identity Protectionhoz

Ha már rendelkezik Azure AD Identity Protection, győződjön meg arról, hogy az engedélyezve van a [hálózaton](../active-directory/identity-protection/enable.md).
Ha Azure AD Identity Protection üzembe helyezése és az adatolvasás, a riasztási adatátviteli szolgáltatás könnyen továbbítható az Azure Sentinelbe.


1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure ad Identity Protection** csempére.

2. A **Kapcsolódás** lehetőségre kattintva megkezdheti a streaming Azure ad Identity Protection események beküldését az Azure sentinelbe.


6. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure AD Identity Protection riasztásokhoz, keresse meg a **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure AD Identity Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
