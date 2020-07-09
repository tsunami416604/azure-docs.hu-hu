---
title: Biztonsági kapcsolattartási adatok megadása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan biztosíthat biztonsági kapcsolattartási adatokat a Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204100"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Biztonsági kapcsolattartási adatok megadása Azure Security Center
Az Azure Security Center javasolni fogja, hogy adja meg a biztonsági kapcsolattartó adatait az Azure-előfizetéséhez, ha még nem tette meg. A Microsoft arra használja ezt az információt, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön ügyféladataihoz. Az MSRC az Azure-hálózat és -infrastruktúra célzott biztonsági figyelését végzi, és fogadja a külső felektől kapott, fenyegetésekkel és visszaélésekkel kapcsolatos panaszokat.

A rendszer a nagyon súlyos riasztások napi első előfordulásakor e-mailes értesítést küld. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatóak. Az előfizetésben lévő erőforráscsoportok megöröklik ezeket a beállításokat. A riasztások csak a standard szintű Azure Security Center érhetők el.

A riasztási e-mail értesítések küldésére vonatkozó szabályok:
- Naponta egyetlen e-mail-címzettnek riasztástípusonként  
- Legfeljebb 3 e-mail-üzenet küldése egyetlen címzettnek egyetlen nap alatt
- Mindegyik e-mail-üzenet egyetlen riasztást tartalmaz, nem a riasztások összesítését.
- Csak a magas súlyosságú riasztások esetében

> [!TIP]
> Más súlyossági szintű riasztások esetén hozzon létre egy [munkafolyamat-automatizálást](workflow-automation.md) egy olyan logikai alkalmazás használatához, amely e-mailt küld az érintett személynek.
 
Ha például egy RDP-támadásról már kapott e-mail-üzenetet, RDP-támadásokról aznap már nem kap majd riasztást, még ha egy újabb riasztás aktiválódik is. 

> [!IMPORTANT]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.

## <a name="set-up-email-notifications-for-alerts"></a>Értesítő e-mail-értesítések beállítása<a name="email"></a>

1. Nyissa meg az **e-mail-értesítések** lapot a szerepkör-biztonsági rendszergazda vagy az előfizetés tulajdonosának felhasználója számára:

    - Riasztások esetén nyissa meg a **díjszabási & beállításait**, válassza ki a megfelelő előfizetést, és válassza az **e-mail értesítések**lehetőséget.

    - Ha javaslatot valósít meg, akkor a **javaslatok**területen válassza a **biztonsági kapcsolattartási adatok megadása**lehetőséget, majd válassza ki az Azure-előfizetést, amely a kapcsolattartási adatokat adja meg. Ekkor megnyílik az **e-mail-értesítések**.

   ![Biztonsági kapcsolattartói adatok megadása][2]

1. Adja meg a biztonsági kapcsolattartási e-mail címet vagy a címeket vesszővel elválasztva. A megadható e-mail-címek száma nincs korlátozva.

1. Ha a nagy súlyosságú riasztásokra vonatkozó e-maileket szeretne kapni, kapcsolja be a **riasztások küldése e-mailben**című lehetőséget. Egyéb súlyossági szint esetén a logikai alkalmazás a munkafolyamat- [automatizálásban](workflow-automation.md)ismertetett módon használható.

1. E-mailes értesítéseket küldhet az előfizetési tulajdonosoknak (a klasszikus szolgáltatás rendszergazdája és a társ-rendszergazdák, valamint a RBAC tulajdonosi szerepkör az előfizetés hatókörében).

1. A biztonsági kapcsolattartási adatok előfizetésre való alkalmazásához válassza a **Mentés**lehetőséget.

## <a name="see-also"></a>További információ
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Center-ben](security-center-managing-and-responding-alerts.md) – útmutató a biztonsági riasztások kezeléséhez és megválaszolásához.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
