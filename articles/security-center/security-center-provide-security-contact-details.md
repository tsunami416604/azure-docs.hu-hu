---
title: Adja meg a biztonsági kapcsolattartási adatokat az Azure Security Centerben | Microsoft dokumentumok
description: Ez a dokumentum bemutatja, hogyan adhat meg biztonsági kapcsolattartási adatokat az Azure Security Centerben.
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
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387818"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Adja meg a biztonsági kapcsolattartási adatokat az Azure Security Centerben
Az Azure Security Center javasolni fogja, hogy adja meg a biztonsági kapcsolattartó adatait az Azure-előfizetéséhez, ha még nem tette meg. A Microsoft arra használja ezt az információt, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön ügyféladataihoz. Az MSRC az Azure-hálózat és -infrastruktúra célzott biztonsági figyelését végzi, és fogadja a külső felektől kapott, fenyegetésekkel és visszaélésekkel kapcsolatos panaszokat.

A rendszer a nagyon súlyos riasztások napi első előfordulásakor e-mailes értesítést küld. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatóak. Az előfizetésben lévő erőforráscsoportok megöröklik ezeket a beállításokat. A riasztások csak az Azure Security Center standard szintű szintjén érhetők el.

A riasztási e-mail értesítések küldésére vonatkozó szabályok:
- Naponta egyetlen e-mail-címzettnek riasztástípusonként  
- Egyetlen nap alatt legfeljebb 3 e-mail üzenetet küld egyetlen címzettnek
- Mindegyik e-mail-üzenet egyetlen riasztást tartalmaz, nem a riasztások összesítését.
- Csak a magas súlyosságú riasztások esetében

> [!TIP]
> A riasztások más súlyossági szintekkel, hozzon létre egy [munkafolyamat-automatizálási](workflow-automation.md) egy logikai alkalmazás, amely e-maileket küld az érintett személyzetnek.
 
Ha például egy RDP-támadásról már kapott e-mail-üzenetet, RDP-támadásokról aznap már nem kap majd riasztást, még ha egy újabb riasztás aktiválódik is. 

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.

## <a name="set-up-email-notifications-for-alerts"></a>E-mail értesítések beállítása riasztásokhoz<a name="email"></a>

1. Nyissa meg az **E-mail értesítések** lapot:

    - Riasztások esetén nyissa meg **az Árképzési & beállításait,** válassza ki a megfelelő előfizetést, és válassza **az E-mail értesítések**lehetőséget.

    - Ha egy javaslatot valósít meg, majd **a javaslatok**csoportban válassza a **Biztonsági kapcsolattartási adatok megadásához**lehetőséget, válassza ki az Azure-előfizetést, hogy megadhassa a kapcsolattartási adatokat. Ezzel megnyitja **az e-mail értesítéseket.**

   ![Biztonsági kapcsolattartói adatok megadása][2]

1. Adja meg a biztonsági kapcsolattartó e-mail címét vagy címét vesszővel elválasztva. A beírható e-mail címek száma nincs korlátozva.

1. Ha e-maileket szeretne kapni a magas súlyosságú riasztásokról, kapcsolja be a **Figyelmeztetések küldése e-maileket.** Más súlyossági szintekhez használja a logikai alkalmazást a [munkafolyamat-automatizálásban](workflow-automation.md)leírtak szerint.

1. E-mail értesítéseket küldhet az előfizetés-tulajdonosoknak (klasszikus szolgáltatás-rendszergazda és társrendszergazdák, valamint RBAC-tulajdonos szerepkör az előfizetés hatókörében).

1. Ha a biztonsági kapcsolattartási adatokat szeretné alkalmazni az előfizetésre, válassza a **Mentés gombot.**

## <a name="see-also"></a>Lásd még
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [A biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Ismerje meg, hogyan segíthetnek a javaslatok az Azure-erőforrások védelmében.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti és válaszolhat azokra a biztonsági riasztásokra.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
