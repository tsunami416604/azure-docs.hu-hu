---
title: Csatlakoztatott partnermegoldások kezelését az Azure Security Centerben |} A Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan tekintheti át az Azure Security Centerben az Azure-előfizetésében integrált partnermegoldások biztonsági állapotát.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 3d4c9c79ff90ee816719f0179fbb5096b0035854
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057270"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Az Azure Security Centerrel összekapcsolt partneri megoldások kezelése
Ez a cikk végigvezeti hogyan kezelheti és figyelheti a csatlakoztatott biztonsági megoldások az Azure Security Centerben.

## <a name="monitoring-partner-solutions"></a>Partnermegoldások figyelése
Csatlakoztatott biztonsági megoldások állapotának figyelése, és végezze el az alapszintű felügyeleti:

1. A **Security Center – áttekintés**válassza **biztonsági megoldások**.

  ![Válassza ki a biztonsági megoldások][1]

  A **csatlakoztatott megoldások** szakasz tartalmazza a Security Center és az egyes megoldások állapotával kapcsolatos információk csatlakozó biztonsági megoldásokat.

  ![Partneri megoldások][2]

   Egy partneri megoldás állapota lehet:

   * Kifogástalan (zöld) – nincs az állapottal kapcsolatos probléma.
   * Nem megfelelő (piros) – azonnali figyelmet igénylő állapottal kapcsolatos probléma.
   * Állapotbeli problémák (narancs) – a megoldás már le van állítva állapotára.
   * Nem jelentett (szürke) – a megoldás még nem jelentett semmit, mégis megoldás állapota lehet nem jelentett, ha azt a nemrégiben csatlakoztatott és még telepítés, vagy nem egészségügyi érhetők el adatok.

   > [!NOTE]
   > Állapotadatok nem érhető el, ha a Security Center a dátum és idő az utolsó esemény jelzi, hogy a megoldás e jelentést vagy sem fogadott jeleníti meg. Ha nem egészségügyi érhetők el adatok és riasztások az elmúlt 14 napon belül érkezik, a Security Center azt jelzi, hogy a megoldás nem megfelelőnek vagy nem jelentéskészítési.
   >
   >

2. Válassza ki **NÉZET** további információért és a beállítások, amely tartalmazza:

  - **Megoldáskonzol**. Megnyílik a kezelést biztosít a megoldáshoz.
  - **Virtuális Géphez csatolni**. Az alkalmazások összekapcsolása panel megnyílik. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
  - **Megoldás törlése**.
  - **Konfigurálása**.

   ![Partneri megoldás részletei][3]

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan kezelhet és figyelhet csatlakoztatott biztonsági megoldásokat a Security Centerben. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági megoldások áttekintése](security-center-partner-integration.md) – ismerje meg, hogyan csatlakozhat, és a biztonsági megoldások kezelése.
* [Csatlakozás a Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) – ismerje meg, hogyan csatlakozhat a riasztások az ATA.
* [Az Azure Active Directory (AD) Identity Protection csatlakoztatásának ](security-center-aadip-integration.md) – ismerje meg, hogyan csatlakozhat a riasztások az Azure AD Identity Protection.
* [Partner-és megoldásintegráció](security-center-partner-integration.md) -más biztonsági megoldások integrálása áttekintése.
* [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
