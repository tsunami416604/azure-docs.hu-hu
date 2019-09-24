---
title: Regisztrációjának megszüntetésére szolgáló Azure Sentinel | Microsoft Docs
description: Az Azure Sentinel-példány törlése.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: f636fc9bab5ea77d11ac7944e7aa18cb0e402b8c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240056"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Az Azure Sentinel eltávolítása a bérlőből

Ha már nem szeretné használni az Azure Sentinelt, ez a cikk azt ismerteti, hogyan távolíthatja el a bérlőből.

## <a name="how-to-delete-azure-sentinel"></a>Az Azure Sentinel törlése

A háttérben az Azure Sentinel telepítésekor a **SecurityInsights** megoldás a kiválasztott munkaterületre van telepítve. Ezért először el kell távolítania a **SecurityInsights** -megoldást.

1.  Nyissa meg az **Azure sentinelt**, majd a **konfiguráció**, majd a **munkaterület-beállítások**, majd a **megoldások**menüpontot.

2.  Jelölje `SecurityInsights` ki, majd kattintson rá.

    ![A SecurityInsights-megoldás megkeresése](media/offboard/find-solution.png)

3.  A lap tetején válassza a **Törlés**lehetőséget.

    > [!IMPORTANT]
    > Ha törli a munkaterületet, a munkaterület és az Azure Sentinel el lesz távolítva a bérlőből Azure Monitor.

    ![A SecurityInsights-megoldás törlése](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Mi történik a színfalak mögött?

A megoldás törlésekor az Azure Sentinel akár 48 órát is igénybe vesz a törlési folyamat első fázisának elvégzéséhez.

A leválasztást követően a offboarding folyamat megkezdődik.

**Az összekötők konfigurációját a rendszer törli:**
-   Office 365

-   AWS

-   Microsoft-szolgáltatások biztonsági riasztásai (Azure ATP, Microsoft Cloud App Security beleértve Cloud Discovery Shadow IT Reporting, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Intelligens veszélyforrás-felderítés

-   Gyakori biztonsági naplók (beleértve a CEF-alapú naplókat, a Barracuda-t és a syslog-t) (ha van Azure Security Center, a rendszer ezeket a naplókat továbbra is gyűjti.)

-   Windows biztonsági események (ha van Azure Security Center, ezek a naplók továbbra is gyűjtése történik.)

Az első 48 órán belül az adatkezelési és riasztási szabályok (beleértve a valós idejű Automation-konfigurációt) többé nem lesznek elérhetők vagy lekérdezhető az Azure Sentinelben.

**30 nap elteltével ezeket az erőforrásokat törli:**

-   Incidensek (beleértve a vizsgálati metaadatokat is)

-   Riasztási szabályok

-   Könyvjelzők

A forgatókönyvek, a mentett munkafüzetek, a mentett vadászati lekérdezések és a jegyzetfüzetek nem törlődnek. **Előfordulhat, hogy az eltávolított adatvesztés miatt egyesek megszakadnak. Ezeket manuálisan is eltávolíthatja.**

A szolgáltatás eltávolítása után 30 napos türelmi időszak áll rendelkezésre, amely során újra engedélyezheti a megoldást, és a rendszer visszaállítja az adatait és a riasztási szabályokat, de a konfigurált összekötőket újra kell kapcsolni.

> [!NOTE]
> Ha eltávolítja a megoldást, az előfizetése továbbra is regisztrálva lesz az Azure Sentinel erőforrás-szolgáltatóban. **Manuálisan is eltávolíthatja.**




## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan távolíthatja el az Azure Sentinel szolgáltatást. Ha meggondolja magát, és újra szeretné telepíteni:
- Ismerkedés [Az Azure Sentinel](quickstart-onboard.md)beszerzésével.

