---
title: Erőforrás biztonsági higiéniai az Azure Security Centerben |} A Microsoft Docs
description: Ez a cikk segít az Azure Security Centerben, az erőforrás biztonsági higiéniai figyelés megkezdésében.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/21/2018
ms.author: rkarlin
ms.openlocfilehash: 361fc2c6931dcfaed062bd39b75be7bef059b467
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122066"
---
# <a name="monitoring-resource-security-hygiene-in-azure-security-center"></a>Az Azure Security Center figyelési erőforrás biztonsági higiéniai
Ez a cikk segít az Azure Security Center figyelési funkcióknak használatához ellenőrizze, hogy az erőforrások biztonságának, szoros, lehetséges, és a figyelő szabályzatoknak való megfelelőségét.

## <a name="what-is-resource-security-hygiene"></a>Mi az erőforrás biztonsági higiéniai?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. Erőforrás biztonsági higiéniai kellene proaktív stratégiát, amely naplózza az erőforrásokat az, hogy nem felel meg a vállalati szabványoknak vagy ajánlott eljárások melyik utal.

## <a name="resource-security-hygiene"></a>Erőforrás biztonsági épsége
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. Problémák és erősíti a hálózaton, és javíthatja a kockázat, hogy hogyan teljes listáját megtekintheti a **javaslatok** csempére.

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

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
