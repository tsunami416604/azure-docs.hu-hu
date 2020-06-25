---
title: Azure AD Identity Protection-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Megtudhatja, hogyan csatlakoztatható Azure AD Identity Protection-adatkapcsolat az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 0f85e543c83413e840354c053a1f4cb0925fc271
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362900"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Adatok összekapcsolása Azure Active Directory (Azure AD) Identity Protectionből

A naplók a [Azure ad Identity Protectionból](../active-directory/identity-protection/overview-identity-protection.md) az Azure sentinelbe továbbítva továbbítják a riasztásokat az Azure Sentinel szolgáltatásba az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Azure Active Directory Identity Protection összevont nézetet biztosít a veszélyeztetett felhasználók, a kockázati észlelések és a sebezhetőségek számára, és lehetővé teszi a kockázatok azonnali javítását, és szabályzatok beállítását a jövőbeli események automatikus szervizeléséhez. A szolgáltatás a Microsoft által a fogyasztói identitások védelmét szolgáló tapasztalatra épül, és egy nap alatt több mint 13 000 000 000-es bejelentkezésből származó, hatalmas pontosságot nyer. 

## <a name="prerequisites"></a>Előfeltételek

- [Prémium szintű Azure ad P2-előfizetéssel](https://azure.microsoft.com/pricing/details/active-directory/)kell rendelkeznie.
- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználónak kell lennie.


## <a name="connect-to-azure-ad-identity-protection"></a>Kapcsolódás Azure AD Identity Protectionhoz

Ha prémium szintű Azure AD P2-előfizetéssel rendelkezik, Azure AD Identity Protection szerepel. Ha a [szabályzatok engedélyezve vannak](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) és riasztásokat generálnak, a riasztási adatátvitel könnyen továbbítható az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure ad Identity Protection** csempére.

1. A **Kapcsolódás** lehetőségre kattintva megkezdheti a streaming Azure ad Identity Protection események beküldését az Azure sentinelbe.

1. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure AD Identity Protection riasztásokhoz, keresse meg a **SecurityAlert**.

Ha tesztelni kívánja az összekötőt, [szimulálhatja az észleléseket](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) , hogy az Azure sentinelbe továbbítható minta-riasztásokat készítsen.

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure AD Identity Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
