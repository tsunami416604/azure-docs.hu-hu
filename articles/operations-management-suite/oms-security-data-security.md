---
title: "Adatbiztonság az Operations Management Suite biztonsági és auditálási megoldásban | Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan zajlik az adatok kezelése az Operations Management Suite biztonsági és auditálási megoldásban."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Adatbiztonság az Operations Management Suite biztonsági és auditálási megoldásban
Annak érdekében, hogy segítse az ügyfeleket a fenyegetések kiküszöbölésében, felderítésében, illetve a rájuk való válaszadásban, az [Operations Management Suite (OMS) biztonsági és auditálási megoldás](operations-management-suite-overview.md) adatokat gyűjt, illetve dolgoz fel az Ön erőforrásairól, többek között a következőkről:

* Biztonsági eseménynapló
* A Windows esemény-nyomkövetés (ETW) eseményei
* AppLocker naplózási események
* A Windows tűzfal naplója
* Advanced Threat Analytics-események
* A biztonsági alapkonfiguráció értékelése
* Kártevőirtó-felmérés eredménye
* Frissítések/javítások felmérésének eredménye
* Az ügynökön kifejezetten engedélyezett Syslogs-streamek

Fontos kötelességünknek tekintjük ezen adatok védelmét és biztonságát. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.
Ez a cikk bemutatja, hogyan kezeli az OMS biztonsági és auditálási megoldás az adatokat, illetve hogyan gondoskodik a védelmükről.

## <a name="data-sources"></a>Adatforrások
Az OMS biztonsági és auditálási megoldás elemzi azokról a virtuális gépekről és fizikai számítógépekről származó adatokat, amelyekre telepítve van az OMS-ügynök. Az OMS biztonsági és auditálási megoldás konfigurációs információkat gyűjthet a biztonsági eseményekről, például a Windows-eseményekről, az auditnaplókról, az IIS-naplókról és a syslog-üzenetekről. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója.  

## <a name="data-protection"></a>Adatvédelem
**Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. 

**Hozzáférés az adatokhoz**: a biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása érdekében a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által összegyűjtött vagy elemzett információkhoz. Betartjuk a [Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) és az [adatvédelmi nyilatkozat](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. A biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása érdekében a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által összegyűjtött vagy elemzett információkhoz. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. A saját adataihoz fűződő összes jog az Ön tulajdonában marad.

**Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

> [!NOTE]
> Az adatok helye az OMS-munkaterület szintjén állítható be a munkaterület létrehozása során, az OMS biztonsági és auditálási megoldás kezdeti konfigurációs folyamatának részeként.
> 
> 

## <a name="see-also"></a>Lásd még:
Ebből a dokumentumból megtudta, hogyan kezeli az OMS az adatokat, és hogyan gondoskodik a védelmükről. Ha többet szeretne megtudni az OMS biztonsági és a naplózási megoldásról, tekintse meg a következő cikkeket:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)

