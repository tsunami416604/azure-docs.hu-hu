---
title: Adatgyűjtés Azure AD Identity Protection az Azure-Sentinel-Előzetesében |} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti össze az adatokat az Azure AD Identity Protection az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 02057fc0f288c8ae77a700f09a8b6e599f6d1b16
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993229"
---
# <a name="collect-data-from-azure-ad-identity-protection"></a>Adatokat gyűjthet az Azure AD Identity Protection

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Naplóinak streamelheti [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) Azure Sentinel-stream a riasztásokra az Azure Sentinel-megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat, és javíthatja a vizsgálat azokat. Az Azure Active Directory Identity Protection nézetet jelenít meg a kockázatos felhasználókról, kockázati eseményekről és biztonsági résekről, lehetővé teszi az azonnali kockázatkezelésre és a jövőbeli események automatikus javításának szabályzatok beállítását biztosít. A szolgáltatás a Microsoft élmény védelmével kapcsolatos tapasztalataira épül és kinyert összefoglaló 13 milliárd log beépülő moduljai, a nap. 


## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy [Azure Active Directory Premium P1 vagy P2-licenc](https://azure.microsoft.com/pricing/details/active-directory/)
- Globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó


## <a name="connect-to-azure-ad-identity-protection"></a>Az Azure AD Identity Protection csatlakoztatása

Ha már rendelkezik Azure AD Identity Protection, győződjön meg arról, hogy [engedélyezve van a hálózaton](../active-directory/identity-protection/enable.md).
Ha Azure AD Identity Protection telepítve van, és az adatok beolvasása a riasztási adatok könnyen továbbítható az Azure-Sentinel.


1. Az Azure-Sentinel, válassza **adatgyűjtés** és kattintson a **Azure AD Identity Protection** csempére.

2. Kattintson a **Connect** megkezdéséhez az Azure AD Identity Protection-események Azure Sentinel-be.


6. A megfelelő sémát használ a Log Analytics az Azure AD Identity Protection riasztásai, keresse meg **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Azure AD Identity Protection az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
