---
title: Azure Security Centerben a partnermegoldások kezelése csatlakoztatott |} Microsoft Docs
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
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
ms.locfileid: "23891026"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Az Azure Security Centerrel összekapcsolt partnermegoldások kezelése
Ez a cikk bemutatja, hogyan kezelése és figyelése az Azure Security Centerben csatlakoztatott biztonsági megoldásokat.

## <a name="monitoring-partner-solutions"></a>Partnermegoldások figyelése
Csatlakoztatott biztonsági megoldások állapotának figyelésére, és hajtsa végre az alapvető felügyeleti:

1. A **Security Center – áttekintés**, jelölje be **biztonsági megoldások**.

  ![Válassza ki a biztonsági megoldások][1]

  A **kapcsolódó megoldások** rész biztonsági megoldásokat, amely csatlakozik a Security Center és az egyes megoldások egészségügyi állapotára vonatkozó információkat tartalmaz.

  ![Partneri megoldások][2]

   Egy partneri megoldás állapota lehet:

   * Kifogástalan (zöld) – nincs az állapottal kapcsolatos probléma.
   * Nem megfelelő (piros) – azonnali figyelmet igénylő állapottal kapcsolatos probléma.
   * Ügynökállapottal kapcsolatos hibákkal (narancs) – a megoldás leállt az állapotát.
   * Nem jelentett (szürke) – a megoldás még nem jelentett semmit, még a megoldás állapota lehet nem jelentett, ha nemrég csatlakozott, és még telepítés, illetve nem egészségügyi adatok elérhető.

   > [!NOTE]
   > Állapotadatok nem érhető el, ha a Security Center jeleníti meg, a dátum és idő az utolsó esemény jelzi, hogy a megoldás jelentéskészítési-e, vagy nem érkezett. Ha egészségügyi adatok nem érhető el, és nincs riasztás az elmúlt 14 napon belül érkezik, a Security Center azt jelzi, hogy a megoldás nem megfelelő vagy nem jelentéskészítési.
   >
   >

2. Válassza ki **NÉZET** további információért és a beállítások, mely tartalmazza:

  - **Megoldáskonzol**. Megnyitja a kezelhetőséget, a megoldáshoz.
  - **Virtuális gép hivatkozás**. Megnyílik a összekapcsolás tartalmazó panel. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
  - **Megoldás törlése**.
  - **Konfigurálása**.

   ![Partneri megoldás részletei][3]

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, kezelése és figyelése a Security Center csatlakoztatott biztonsági megoldásokat. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [A biztonsági megoldások áttekintése](security-center-partner-integration.md) – megtudhatja, hogyan csatlakozhat, és kezelheti a biztonsági megoldások.
* [Csatlakozás a Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) – megtudhatja, hogyan csatlakozhat a riasztások ATA.
* [Csatlakozás az Azure Active Directory (AD) identitás védelmi ](security-center-aadip-integration.md) – megtudhatja, hogyan csatlakozhat a Azure AD Identity Protection riasztások.
* [Partnerek és a megoldások integrációs](security-center-partner-integration.md) -integrálása más biztonsági megoldások áttekintése.
* [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
