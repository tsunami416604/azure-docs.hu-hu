---
title: Az Azure AD Identity Protection adatainak összekapcsolása az Azure Sentinelhez
description: Ismerje meg, hogyan kapcsolhatja össze az Azure AD Identity Protection adatait az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588569"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Adatok csatlakoztatása az Azure AD Identity Protection szolgáltatásból



Az [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) naplóit streamelheti az Azure Sentinelbe, így riasztásokat továbbíthat az Azure Sentinelbe az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. Az Azure Active Directory identity Protection összevont nézetet biztosít a veszélyeztetett felhasználók, a kockázatészlelések és a biztonsági rések, a kockázat azonnali elhárítása, és állítsa be a szabályzatokat, hogy automatikusan kiigazítsa a jövőbeli eseményeket. A szolgáltatás a Microsoft felhasználói identitásának védelmével kapcsolatos tapasztalataira épül, és óriási pontosságot nyer a napi több mint 13 milliárd bejelentkezésből származó jelből. 


## <a name="prerequisites"></a>Előfeltételek

- Azure Active [Directory Premium P1 vagy P2 licenccel](https://azure.microsoft.com/pricing/details/active-directory/) kell rendelkeznie
- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó


## <a name="connect-to-azure-ad-identity-protection"></a>Csatlakozás az Azure AD-identitásvédelemhez

Ha már rendelkezik az Azure AD Identity Protection szolgáltatással, győződjön meg arról, hogy engedélyezve van [a hálózaton.](../active-directory/identity-protection/overview-identity-protection.md)
Ha az Azure AD Identity Protection telepítve van, és adatokat kap, a riasztási adatok könnyen streamelhetők az Azure Sentinelbe.


1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Azure AD Identity Protection** csempére.

2. Kattintson **a Csatlakozás** gombra az Azure AD Identity Protection események Azure Sentinelbe való streamelésének megkezdéséhez.


6. Ha a megfelelő sémát szeretné használni a Log Analytics az Azure AD Identity Protection riasztások, keresse meg a **SecurityAlert.**

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure AD Identity Protectiont az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
