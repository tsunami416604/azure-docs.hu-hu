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
ms.openlocfilehash: b7bb67ec052244689b2775c280ff39a2121da3fa
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201696"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>A biztonsági testhelyzet megerősítése Azure Security Center
Ez a cikk segít megerősíteni a biztonsági helyzetét. A Azure Security Center Figyelési képességeivel gondoskodhat arról, hogy az erőforrás biztonsága minél szigorúbb legyen, és figyelje a szabályzatoknak való megfelelést.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hogyan erősíti meg a biztonsági helyzetét?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonsági helyzet megerősítése olyan proaktív stratégia, amely a szervezeti szabványoknak vagy ajánlott eljárásoknak nem megfelelő rendszerek azonosítására szolgál.

Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. Megtekintheti a problémák teljes listáját, valamint megerősítheti a hálózatot, és javíthatja a kockázatokat a **javaslatok** csempén.

Megtekintheti az erőforrások biztonsági állapotát és az erőforrástípus által felmerülő problémákat:

- A számítógép erőforrásainak és alkalmazásai állapotának figyelésére, valamint a biztonság javítására vonatkozó javaslatok fogadására: [a gépek és alkalmazások védelme Azure Security Center](security-center-virtual-machine-protection.md)
- A hálózati erőforrások, például a virtuális gépek, a hálózati biztonsági csoportok és a végpontok figyelésére, valamint a biztonság javítására vonatkozó javaslatok fogadására vonatkozó további információkért tekintse [meg a hálózat védelme Azure Security Centerban](security-center-network-recommendations.md) című témakört. 
- Az adatok és a tárolási erőforrások, például az SQL-kiszolgálók és a Storage-fiókok figyeléséhez, valamint a biztonság javítására vonatkozó javaslatok fogadásához tekintse meg az [Azure SQL-szolgáltatás és-adatok védelme Azure Security Center](security-center-sql-service-recommendations.md) további információért. 
- Az identitás-és hozzáférés-erőforrások, például az MFA-és a fiók engedélyeinek figyeléséhez, valamint a biztonság javítására vonatkozó javaslatok fogadásához tekintse meg az [identitás és hozzáférés figyelése Azure Security Center](security-center-identity-access.md) további információért című témakört. 
- Az erőforrásokhoz való igény szerinti hozzáférés figyeléséhez tekintse meg a [virtuális gépek hozzáférésének kezelése igény](security-center-just-in-time.md) szerint című részt további információért. 


A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).



![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása a Azure Security Centerban](tutorial-security-policy.md): Megtudhatja, hogyan konfigurálhatja a Azure Security Center biztonsági beállításait.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása Azure Security Centerekkel](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center gyakori kérdések](security-center-faq.md): Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
