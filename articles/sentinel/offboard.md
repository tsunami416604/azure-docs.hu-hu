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
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: a45f273078a622de5e256457fc45b6cb6cae512f
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464140"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Az Azure Sentinel eltávolítása a bérlőből

Ha már nem szeretné használni az Azure Sentinelt, ez a cikk azt ismerteti, hogyan távolíthatja el a bérlőből.

## <a name="how-to-delete-azure-sentinel"></a>Az Azure Sentinel törlése

A háttérben az Azure Sentinel telepítésekor a **SecurityInsights** megoldás a kiválasztott munkaterületre van telepítve. Ezért először el kell távolítania a **SecurityInsights** -megoldást.

1.  Nyissa meg az **Azure sentinelt**, majd a **konfiguráció**, majd a **munkaterület-beállítások**, majd a **megoldások**menüpontot.

2.  Válassza a `SecurityInsights` lehetőséget, majd kattintson rá.

    ![A SecurityInsights-megoldás megkeresése](media/offboard/find-solution.png)

3.  A lap tetején válassza a **Törlés**lehetőséget.

    > [!IMPORTANT]
    > Ha eltávolítja a munkaterületet, az hatással lehet a munkaterületet használó más megoldásokra és adatforrásokra, beleértve a Azure Monitor is. A munkaterületet használó megoldások ellenőrzéséhez tekintse meg a [telepített figyelési megoldások listázása](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)című témakört. Tekintse meg a betöltött [adatmennyiség megismerése](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume)című témakört, amelyből megtudhatja, hogy a rendszer hogyan tölti be a megoldásokat a munkaterületre.

    ![A SecurityInsights-megoldás törlése](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Mi történik a színfalak mögött?

A megoldás törlésekor az Azure Sentinel akár 48 órát is igénybe vesz a törlési folyamat első fázisának elvégzéséhez.

A leválasztást követően a offboarding folyamat megkezdődik.

**Az összekötők konfigurációját a rendszer törli:**
-   Office 365

-   AWS

-   Microsoft-szolgáltatások biztonsági riasztásai (Azure ATP, Microsoft Cloud App Security beleértve Cloud Discovery Shadow IT Reporting, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Fenyegetések felderítése

-   Gyakori biztonsági naplók (beleértve a CEF-alapú naplókat, a Barracuda-t és a syslog-t) (ha van Azure Security Center, a rendszer ezeket a naplókat továbbra is gyűjti.)

-   Windows biztonsági események (ha van Azure Security Center, ezek a naplók továbbra is gyűjtése történik.)

Az első 48 órán belül az adatkezelési és riasztási szabályok (beleértve a valós idejű Automation-konfigurációt) többé nem lesznek elérhetők vagy lekérdezhető az Azure Sentinelben.

**30 nap elteltével ezeket az erőforrásokat törli:**

-   Incidensek (beleértve a vizsgálati metaadatokat is)

-   Riasztási szabályok

-   könyvjelzők

A forgatókönyvek, a mentett munkafüzetek, a mentett vadászati lekérdezések és a jegyzetfüzetek nem törlődnek. **Előfordulhat, hogy az eltávolított adatvesztés miatt egyesek megszakadnak. Ezeket manuálisan is eltávolíthatja.**

A szolgáltatás eltávolítása után 30 napos türelmi időszak áll rendelkezésre, amely során újra engedélyezheti a megoldást, és a rendszer visszaállítja az adatait és a riasztási szabályokat, de a konfigurált összekötőket újra kell kapcsolni.

> [!NOTE]
> Ha eltávolítja a megoldást, az előfizetése továbbra is regisztrálva lesz az Azure Sentinel erőforrás-szolgáltatóban. **Manuálisan is eltávolíthatja.**




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan távolíthatja el az Azure Sentinel szolgáltatást. Ha meggondolja magát, és újra szeretné telepíteni:
- Ismerkedés [Az Azure Sentinel](quickstart-onboard.md)beszerzésével.

