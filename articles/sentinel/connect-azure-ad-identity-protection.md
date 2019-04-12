---
title: Az Azure AD Identity Protection-adatok csatlakoztatása Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: 'Útmutató: Azure Sentinel-adatokat az Azure AD Identity Protection csatlakozni.'
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8306056655809c69af2ed39b5bbf8efebe05d3f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496163"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Adatok csatlakoztatása az Azure AD Identity Protection

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


1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Azure AD Identity Protection** csempére.

2. Kattintson a **Connect** megkezdéséhez az Azure AD Identity Protection-események Azure Sentinel-be.


6. A megfelelő sémát használ a Log Analytics az Azure AD Identity Protection riasztásai, keresse meg **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Azure AD Identity Protection az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
