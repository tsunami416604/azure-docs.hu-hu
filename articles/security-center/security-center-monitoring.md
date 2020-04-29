---
title: A biztonsági helyzet megerősítése a Azure Security Centerkal | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan erősítheti meg a biztonsági helyzetét a Azure Security Center erőforrásainak monitorozásával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77603767"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Biztonsági helyzet megerősítése az Azure Security Centerrel
Ez a cikk segít megerősíteni a biztonsági helyzetét. A Azure Security Center Figyelési képességeivel gondoskodhat arról, hogy az erőforrás biztonsága minél szigorúbb legyen, és figyelje a szabályzatoknak való megfelelést.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hogyan erősíti meg a biztonsági helyzetét?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonsági helyzet megerősítése olyan proaktív stratégia, amely a szervezeti szabványoknak vagy ajánlott eljárásoknak nem megfelelő rendszerek azonosítására szolgál.

Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. Megtekintheti a problémák teljes listáját, valamint megerősítheti a hálózatot, és javíthatja a kockázatokat a **javaslatok** csempén.

Megtekintheti az erőforrások biztonsági állapotát és az erőforrástípus által felmerülő problémákat:

- A számítógép erőforrásainak és alkalmazásai állapotának figyelésére, valamint a biztonság javítására vonatkozó javaslatok fogadására: [a gépek és alkalmazások védelme Azure Security Center](security-center-virtual-machine-protection.md)
- A hálózati erőforrások, például a virtuális gépek, a hálózati biztonsági csoportok és a végpontok figyelésére, valamint a biztonság javítására vonatkozó javaslatok fogadására vonatkozó további információkért tekintse [meg a hálózat védelme Azure Security Centerban](security-center-network-recommendations.md) című témakört. 
- Az adatok és a tárolási erőforrások, például az SQL-kiszolgálók és a Storage-fiókok figyeléséhez, valamint a biztonság javítására vonatkozó javaslatok fogadásához tekintse meg az [Azure SQL-szolgáltatás és-adatok védelme Azure Security Center](security-center-sql-service-recommendations.md) további információért. 
- Az identitás-és hozzáférés-erőforrások, például az MFA-és a fiók engedélyeinek figyeléséhez, valamint a biztonság javítására vonatkozó javaslatok fogadásához tekintse meg az [identitás és hozzáférés figyelése Azure Security Center](security-center-identity-access.md) további információért című témakört. 
- Az erőforrások igény szerinti elérésének figyeléséhez tekintse meg a [virtuális gépek hozzáférésének kezelése](security-center-just-in-time.md) az igény szerinti kezeléssel című részt további információért. 


A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).



![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.