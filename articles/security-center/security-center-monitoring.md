---
title: Az Azure Security Center rel való biztonsági állapot megerősítése | Microsoft dokumentumok
description: Ez a cikk segít a biztonsági helyzet megerősítésében az Azure Security Center erőforrásainak figyelésével.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603767"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Biztonsági helyzet megerősítése az Azure Security Centerrel
Ez a cikk segít a biztonsági testhelyzet megerősítésében. Az Azure Security Center figyelési képességeivel győződjön meg arról, hogy az erőforrás-biztonság a lehető legszorosabb, és figyelheti a szabályzatok betartását.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hogyan erősíti a biztonsági testtartást?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonsági helyzet megerősítése olyan proaktív stratégiára utal, amely ellenőrzi az erőforrásokat a szervezeti szabványoknak vagy ajánlott eljárásoknak nem megfelelő rendszerek azonosítására.

Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. A problémák teljes listáját és a hálózat megkeményedésének és a kockázatok elhárításának módjait a Javaslatok csempén **tekintheti meg.**

Az erőforrások biztonsági állapotát és az erőforrástípusonkéntfelmerülő problémákat megtekintheti:

- A számítógép-erőforrások és az alkalmazások állapotának figyeléséhez és a biztonságuk javítására vonatkozó javaslatok fogadásához olvassa el [a Gépek és alkalmazások védelme az Azure Security Centerben című témakört.](security-center-virtual-machine-protection.md)
- A hálózati erőforrások, például a virtuális gépek, a hálózati biztonsági csoportok és a végpontok figyeléséhez és a biztonságuk javítására vonatkozó javaslatok fogadásához további információt [a Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md) című témakörben talál. 
- Az adatok és a tárolási erőforrások, például az SQL-kiszolgálók és a tárfiókok figyeléséhez és a biztonságuk javítására vonatkozó javaslatok fogadásához további információt az [Azure SQL-szolgáltatás és az adatok védelme az Azure Security Centerben](security-center-sql-service-recommendations.md) című témakörben talál. 
- Az identitás figyeléséhez és az erőforrások hoz való hozzáférés, beleértve az MFA és a fiók engedélyeket, és javaslatokat kap a biztonságuk javítására, további információ: [Identitás és hozzáférés figyelése az Azure Security Centerben.](security-center-identity-access.md) 
- Az erőforrásokhoz való just-in-time hozzáférés figyelése, [további információ: A virtuális gép-hozzáférés kezelése a just-in-time](security-center-just-in-time.md) további információ. 


A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).



![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.