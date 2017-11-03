---
title: "Adja meg biztonsági kapcsolattartási adatait az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum bemutatja, hogyan adja meg az Azure Security Centerben a biztonsági kapcsolattartási adatait."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 726b59c45e2eb18eebe28a180db23336ae141408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Adja meg az Azure Security Centerben a biztonsági kapcsolattartási adatait
Azure Security Center javasolni fogja adni biztonsági kapcsolattartási adatait az Azure-előfizetéssel, ha még nem tette meg. A Microsoft arra használja ezt az információt, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön ügyféladataihoz. MSRC hajt végre, válassza ki a biztonság ellenőrzése az Azure-hálózatot és az infrastruktúra, és fenyegetést eszközintelligencia és visszaélés panaszokat kapott harmadik felek számára.

E-mailben értesítést küldött riasztást, és csak magas súlyosságú riasztások az első napi előfordulása esetén. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatóak. Erőforráscsoportok előfizetésen belül öröklik ezeket a beállításokat.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. A **javaslatok**, jelölje be **adja meg biztonsági kapcsolattartási adatait**.
   ![Adja meg a biztonsági ügyfél][1]
2. Válassza ki az Azure-előfizetés a kapcsolattartási adatok megadása.
3. Ekkor megnyílik **biztonsági házirend - értesítő e-mailek**.

   ![Biztonsági kapcsolattartói adatok megadása][2]

   * Adja meg a biztonsági kapcsolattartási e-mail címet vagy címeket vesszővel válassza el egymástól elválasztva. Nincs korlátozni szeretné az e-mail címeket, amelyeket megadhat számát.
   * Adjon meg egy biztonsági nemzetközi telefonszámát.
   * Magas súlyosságú riasztások vonatkozó e-mailt kap, a beállítás bekapcsolása **küldése e-mailt küld kapcsolatos riasztások**.
   * A jövőben akkor is előfizetésnél tulajdonos e-mail értesítések küldéséhez. Ez a beállítás jelenleg szürkén jelenik meg.
   * Válassza ki **mentése** biztonsági kapcsolattartási adatainak alkalmazása az előfizetéshez.

## <a name="see-also"></a>Lásd még:
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) --az Azure biztonsági legfrissebb hírek és információ.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
