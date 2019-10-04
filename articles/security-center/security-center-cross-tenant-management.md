---
title: Több-bérlős felügyelet a Azure Security Centerban | Microsoft Docs
description: " Ismerje meg, hogyan lehet engedélyezni az adatgyűjtést az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 178911390a4cb694171adf6c807369cab0c0499a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202365"
---
# <a name="cross-tenant-management-in-security-center"></a>Több-bérlős felügyelet a Security Center

A több-bérlős felügyelet lehetővé teszi, hogy az Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md) kihasználva megtekintse és felügyelje Security Center több bérlő biztonsági állapotát. Több bérlő hatékony kezelése egyetlen nézetből, anélkül, hogy be kellene jelentkeznie az egyes bérlői címtárba.

- A szolgáltatók több ügyfél számára is kezelhetik az erőforrások biztonsági állapotát, a saját bérlőn belül.

- A több Bérlővel rendelkező szervezetek biztonsági csoportjai egyetlen helyről tekinthetik meg és kezelhetik biztonsági állapotát.

## <a name="set-up-cross-tenant-management"></a>A több-bérlős felügyelet beállítása

A több-bérlős felügyelet beállítása a felügyelt bérlők erőforrásaihoz való hozzáférés delegálásával a saját bérlőhöz az [Azure delegált erőforrás-kezelés](../lighthouse/concepts/azure-delegated-resource-management.md)használatával.

> [!NOTE]
> Az Azure-beli delegált erőforrás-kezelés az Azure Lighthouse egyik fő összetevője.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hogyan működik a több-bérlős felügyelet Security Center

Az előfizetések több bérlőn is megtekinthetők és kezelhetők ugyanúgy, mint a több előfizetés egyetlen bérlőn való kezeléséhez.

A felső menüsorban kattintson a szűrő ikonra, és válassza ki az előfizetéseket minden bérlő címtárában, amelyet meg szeretne tekinteni.

  ![Bérlők szűrése](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

A nézetek és a műveletek alapvetően azonosak. Néhány példa:

- **Biztonsági házirendek kezelése**: Az egyik nézetből számos erőforrás biztonsági állapotát kezelheti a [szabályzatokkal](tutorial-security-policy.md), biztonsági javaslatokat tehet a műveletekre, valamint biztonsági adatokat gyűjthet és kezelhet.
- **A biztonságos pontszám és a megfelelőségi testhelyzet javítása**: A több-bérlős láthatóság lehetővé teszi, hogy megtekintse az összes bérlő teljes biztonsági állapotát, valamint azt, hogy hol és hogyan lehet a lehető legjobban javítani a [biztonságos pontszám](security-center-secure-score.md) és a [megfelelőségi testhelyzetet](security-center-compliance-dashboard.md) .
- **Javaslatok szervizelése**: A különböző bérlők számos erőforrására vonatkozó [javaslat](security-center-recommendations.md) figyelése és szervizelése egyszerre. Ezután azonnal megoldhatja azokat a biztonsági réseket, amelyek a legnagyobb kockázatot jelentik az összes bérlő esetében.
- **Riasztások kezelése**: [Riasztások](security-center-alerts-overview.md) észlelése a különböző bérlők között. Tegyen lépéseket olyan erőforrásokra, amelyek nem felelnek meg a gyakorlatban használható [szervizelési lépéseknek](security-center-managing-and-responding-alerts.md).

- **A Cloud Defense speciális szolgáltatásainak kezelése és egyéb funkciók**: Kezelheti a veszélyforrások észlelési és védelmi szolgáltatásait, például az igény szerinti [(JIT)](security-center-just-in-time.md)virtuálisgép-hozzáférést, az [adaptív hálózat megerősítését](security-center-adaptive-network-hardening.md), az [adaptív alkalmazások vezérlését](security-center-adaptive-application.md)és egyebeket.
 
## <a name="next-steps"></a>További lépések
Ez a cikk bemutatja, hogyan működik a több-bérlős felügyelet a Security Centerban. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Megerősítheti a biztonsági helyzeteket Azure Security Centerokkal](security-center-monitoring.md)– megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
