---
title: Csatlakozás az Azure AD-adatok Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg az Azure Active Directory-adatok csatlakoztatása az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f29ae9a8fcaecfc345efae02084f31d133e67b5d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494884"
---
# <a name="connect-data-from-azure-active-directory"></a>Adatok csatlakoztatása az Azure Active Directoryból

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel lehetővé teszi az adatok gyűjtéséhez [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) és az Azure-Sentinel eseménysorozatot. Választhat a streamre [bejelentkezési naplók](../active-directory/reports-monitoring/concept-sign-ins.md) és [auditnaplók](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Előfeltételek

- Ha azt szeretné, bejelentkezési adatokat exportálhat az Active Directory, az Azure AD P1 vagy P2 licenccel kell rendelkeznie.

- A felhasználó globális rendszergazdai vagy biztonsági rendszergazdai jogosultságokkal rendelkezik, az adatfolyam a naplók a kívánt bérlő.


## <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz

1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Azure Active Directory** csempére.

2. A naplók streamelése az Azure-Sentinel szeretne, mellett kattintson **Connect**.

6. A megfelelő sémát használ a Log Analytics az Azure AD-riasztásokat, keresse meg **SigninLogs** és **Adatmodelltáblához**.




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure AD csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
