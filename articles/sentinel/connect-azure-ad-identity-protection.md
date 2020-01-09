---
title: Azure AD Identity Protection-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Megtudhatja, hogyan csatlakoztatható Azure AD Identity Protection-adatkapcsolat az Azure Sentinelhez.
author: rkarlin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 8d916606913b19a9eb15cb280defbeb0440565e4
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563717"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Adatok összekapcsolásának Azure AD Identity Protection



A naplók a [Azure ad Identity Protectionból](https://docs.microsoft.com/azure/information-protection/reports-aip) az Azure sentinelbe továbbítva továbbítják a riasztásokat az Azure Sentinel szolgáltatásba az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Azure Active Directory Identity Protection összevont nézetet biztosít a veszélyeztetett felhasználók, a kockázati észlelések és a sebezhetőségek számára, és lehetővé teszi a kockázatok azonnali javítását, és szabályzatok beállítását a jövőbeli események automatikus szervizeléséhez. A szolgáltatás a Microsoft által a fogyasztói identitások védelmét szolgáló tapasztalatra épül, és egy nap alatt több mint 13 000 000 000-es bejelentkezésből származó, hatalmas pontosságot nyer. 


## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy [prémium szintű Azure Active Directory P1 vagy P2 licenccel](https://azure.microsoft.com/pricing/details/active-directory/)
- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó


## <a name="connect-to-azure-ad-identity-protection"></a>Kapcsolódás Azure AD Identity Protectionhoz

Ha már rendelkezik Azure AD Identity Protection, győződjön meg arról, hogy az engedélyezve van a [hálózaton](../active-directory/identity-protection/overview-identity-protection.md).
Ha Azure AD Identity Protection üzembe helyezése és az adatolvasás, a riasztási adatátviteli szolgáltatás könnyen továbbítható az Azure Sentinelbe.


1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure ad Identity Protection** csempére.

2. A **Kapcsolódás** lehetőségre kattintva megkezdheti a streaming Azure ad Identity Protection események beküldését az Azure sentinelbe.


6. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure AD Identity Protection riasztásokhoz, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure AD Identity Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
