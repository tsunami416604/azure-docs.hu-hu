---
title: Az Azure Security Center biztonsági kapcsolattartói adatok megadása |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan lehet az Azure Security Center biztonsági kapcsolattartói adatok megadása.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: rkarlin
ms.openlocfilehash: 1948cee033ecb8b50fdd75513a07766490568600
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337257"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Az Azure Security Center biztonsági kapcsolattartói adatok megadása
Az Azure Security Center javasolni fogja, hogy Ön biztonsági kapcsolattartói adatok megadása az Azure-előfizetése, ha még nem tette. A Microsoft arra használja ezt az információt, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön ügyféladataihoz. MSRC hajt végre, válassza ki a biztonsági figyelése az Azure-hálózat és az infrastruktúra, és harmadik felektől származó threat intelligence és visszaélések panaszok kap.

Egy e-mailben értesítést küld a napi első előfordulása a riasztások, és csak a magas súlyossági szintű riasztások. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatóak. Egy előfizetésen belüli erőforráscsoportok örökli ezeket a beállításokat.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. A **javaslatok**válassza **biztonsági kapcsolattartói adatok megadása**.
   ![Adja meg a biztonsági kapcsolattartó][1]
2. Válassza ki az Azure-előfizetés a adja meg a kapcsolattartási adatokat.
3. Ez megnyitja **E-mail-értesítések**.

   ![Biztonsági kapcsolattartói adatok megadása][2]

   * Adja meg a biztonsági kapcsolattartói e-mail címet vagy címeket vesszővel elválasztva. Nem áll adhat meg e-mail-címek száma korlátozva.
   * Adjon meg egy biztonsági kapcsolattartó nemzetközi telefonszám.
   * A magas súlyossági szintű riasztások e-maileket kapni, kapcsolja be a beállítást **küldése e-mailt küld riasztásokról**.
   * A jövőben kell arra, hogy az előfizetés-tulajdonosokat e-mail értesítések küldéséhez. Ez a beállítás jelenleg szürkén jelenik meg.
   * Válassza ki **mentése** biztonsági kapcsolattartási alkalmazni az előfizetéshez.

## <a name="see-also"></a>Lásd még
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
