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
ms.openlocfilehash: 15029c3e0bd3959000786af484a42691f00bb704
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603567"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Biztonsági kapcsolattartási adatok megadása Azure Security Center
Azure Security Center azt javasolja, hogy ha még nem tette meg, az Azure-előfizetéséhez adja meg a biztonsági kapcsolattartási adatait. A Microsoft arra használja ezt az információt, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön ügyféladataihoz. A MSRC az Azure-hálózat és-infrastruktúra biztonsági monitorozását választja, és a fenyegetésekkel kapcsolatos intelligenciát és a harmadik felektől érkező visszaéléseket fogad.

A rendszer a nagyon súlyos riasztások napi első előfordulásakor e-mailes értesítést küld. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatóak. Az előfizetésben lévő erőforráscsoportok megöröklik ezeket a beállításokat. A riasztások csak a standard szintű Azure Security Center érhetők el.

A riasztási e-mail értesítések küldésére vonatkozó szabályok:
- Csak a magas súlyosságú riasztások esetében
- Naponta egyetlen e-mail-címzettnek riasztástípusonként  
- Legfeljebb 3 e-mail-üzenet küldése egyetlen címzettnek egyetlen nap alatt
- Mindegyik e-mail-üzenet egyetlen riasztást tartalmaz, nem a riasztások összesítését.
 
Ha például egy RDP-támadásról már kapott e-mail-üzenetet, RDP-támadásokról aznap már nem kap majd riasztást, még ha egy újabb riasztás aktiválódik is. 

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.

## Értesítő e-mail-értesítések beállítása<a name="email"></a>

1. A portálon válassza a **díjszabás & beállítások**lehetőséget.
1. Kattintson az előfizetésre.
1. Kattintson az **e-mail értesítések**lehetőségre.

> [!NOTE]
> Ha javaslatot valósít meg, akkor a **javaslatok**területen válassza a **biztonsági kapcsolattartási adatok megadása**lehetőséget, majd válassza ki az Azure-előfizetést, amely a kapcsolattartási adatokat adja meg. Ekkor megnyílik az **e-mail-értesítések**.

   ![Biztonsági kapcsolattartói adatok megadása][2]

   * Adja meg a biztonsági kapcsolattartási e-mail címet vagy a címeket vesszővel elválasztva. Nincs korlátozva a megadható e-mail-címek száma.
   * Adjon meg egy biztonsági kapcsolattartó nemzetközi telefonszámot.
   * Ha a nagy súlyosságú riasztásokra vonatkozó e-maileket szeretne kapni, kapcsolja be a **riasztások küldése e-mailben**című lehetőséget.
   * Lehetősége van e-mail-értesítések küldésére az előfizetés-tulajdonosoknak (a klasszikus szolgáltatás rendszergazdája és a társ-rendszergazdák, valamint a RBAC tulajdonosi szerepköre az előfizetés hatókörében).
   * Válassza a **Mentés** lehetőséget a biztonsági kapcsolattartási adatok előfizetésre való alkalmazásához.

## <a name="see-also"></a>Lásd még
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Ez a cikk ismerteti a partnermegoldások biztonsági állapotának figyelését.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
