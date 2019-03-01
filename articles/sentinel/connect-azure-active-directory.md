---
title: Az Azure-Sentinel-előzetes verzióban elérhető Azure AD-adatok gyűjtése |} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti össze az Azure Active Directory Azure Sentinel-adatokat.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: e145807b3170b7b822eabba09ce2e97da5467761
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993257"
---
# <a name="collect-data-from-azure-active-directory"></a>Adatokat gyűjthet az Azure Active Directoryból

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel lehetővé teszi az adatok gyűjtéséhez [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) és az Azure-Sentinel eseménysorozatot. Választhat a streamre [bejelentkezési naplók](../active-directory/reports-monitoring/concept-sign-ins.md) és [auditnaplók](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Előfeltételek

- Ha azt szeretné, bejelentkezési adatokat exportálhat az Active Directory, az Azure AD P1 vagy P2 licenccel kell rendelkeznie.

- A felhasználó globális rendszergazdai vagy biztonsági rendszergazdai jogosultságokkal rendelkezik, az adatfolyam a naplók a kívánt bérlő.


## <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz

1. Az Azure-Sentinel, válassza **adatgyűjtés** és kattintson a **Azure Active Directory** csempére.

2. A naplók streamelése az Azure-Sentinel szeretne, mellett kattintson **Connect**.






## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure AD csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
