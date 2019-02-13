---
title: Erősítse meg biztonsági állapotát az Azure Security Centerrel |} A Microsoft Docs
description: Ez a cikk segít erősítse meg biztonsági állapotát az Azure Security Center az erőforrások monitorozásával.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112257"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Erősítse meg biztonsági állapotát az Azure Security Centerrel
Ez a cikk segít erősítse meg biztonsági állapotát. Az Azure Security Center figyelési funkcióknak használatához ellenőrizze, hogy az erőforrások biztonságának, szoros, lehetséges, és a figyelő szabályzatoknak való megfelelőségét.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hogyan erősítse meg biztonsági állapotát?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. Biztonsági helyzetét megerősítése arra utal, hogy a proaktív stratégiát, amely naplózza az erőforrásokat az azonosítani azokat a vállalati szabványoknak vagy ajánlott eljárások nem felel meg.

Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. Problémák és erősíti a hálózaton, és javíthatja a kockázat, hogy hogyan teljes listáját megtekintheti a **javaslatok** csempére.

Az erőforrások és az esetleges problémákat, erőforrás-típusonként biztonsági állapotát tekintheti meg:

- A számítógép-erőforrások és az alkalmazások állapotának figyelésére, és azok biztonsági javítására vonatkozó javaslatokat kapni: [védelmére a gépek és az alkalmazások az Azure Security Centerben](security-center-virtual-machine-protection.md)
- A hálózati erőforrások, például a virtual machines, a hálózati biztonsági csoportok és a végpontok monitorozása és azok biztonsági javítására vonatkozó javaslatokat kapni: [hálózat védelme az Azure Security Center](security-center-network-recommendations.md) további információk. 
- Figyelheti az adatok és a tárolási erőforrások, például az SQL Server-kiszolgálók és a storage-fiókok esetében, és azok biztonsági javaslatai megjelenik, lásd: [védelme az Azure SQL-szolgáltatás és az Azure Security Center adatokat](security-center-sql-service-recommendations.md) további információ . 
- Figyelheti az identitás- és hozzáférés-erőforrások, beleértve a többtényezős hitelesítés és a fiók engedélyeit, és azok biztonsági javítására vonatkozó javaslatokat kapni: [identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md) további információt. 
- Igény szerinti hozzáférés az erőforrásokhoz való figyeléséről lásd: [szerinti virtuálisgép-hozzáférés kezelése](security-center-just-in-time.md) további információt. 


A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).



![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md): Megtudhatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
