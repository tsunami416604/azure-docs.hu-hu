---
title: Azure Management – Azure-cégirányítási
description: Az Azure-alkalmazások felügyeleti területeinek és az Azure felügyeleti eszközök tartalmaira mutató hivatkozásokat mutató erőforrások áttekintése.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75980904"
---
# <a name="overview-of-management-services-in-azure"></a>Az Azure felügyeleti szolgáltatásainak áttekintése

Az Azure-beli cégirányítási az Azure Management egyik szempontja. Ez a cikk az erőforrások Azure-beli üzembe helyezéséhez és karbantartásához szükséges felügyeleti területeket ismerteti.

Felügyelet alatt azokat a feladatokat és folyamatokat értjük, amelyekre szükség van az üzleti alkalmazások és az azokat támogató erőforrások kezeléséhez. Az Azure számos olyan szolgáltatással és eszközzel rendelkezik, amelyek együttesen biztosítják a teljes körű felügyeletet. Ezek a szolgáltatások nem csak az Azure-ban, hanem más felhők és a helyszíni erőforrásokhoz is szolgálnak. A különböző eszközök és az együttműködésük megértése az első lépés a teljes felügyeleti környezet kialakításában.

A következő ábra azt illusztrálja, hogy milyen felügyeleti területekre van szükség az alkalmazások és erőforrások kezeléséhez. Ezek a különböző területek lehet tekinteni, mint egy életciklus. Minden terület szükséges folyamatos egymásutánban élettartama alatt egy erőforrás. Ez az erőforrás-életciklus a kezdeti üzembe helyezéssel kezdődik, a folyamatos működéssel, és végül a megszüntetéskor.

![Managementi szakterületek az Azure-ban](../monitoring/media/management-overview/management-capabilities.png)

Egyetlen Azure-szolgáltatás sem felel meg teljesen egy adott felügyeleti terület követelményeinek. Ehelyett minden valósul meg több szolgáltatás együtt dolgozik. Egyes szolgáltatások, például az Application Insights, célzott figyelési funkciókat biztosítanak a webalkalmazások számára. Mások, például az Azure Monitor naplói, más szolgáltatások felügyeleti adatait tárolják. Ez a funkció lehetővé teszi a különböző szolgáltatások által gyűjtött különböző típusú adatok elemzését.

A következő szakaszok röviden ismertetik az egyes felügyeleti területeket, és az ezek kezelésére hivatott Azure-szolgáltatások részletes ismertetésére mutató hivatkozásokat adnak meg.

## <a name="monitor"></a>Figyelés

A figyelés az adatok gyűjtése és elemzése az erőforrások teljesítményének, állapotának és rendelkezésre állásának naplózása érdekében. A hatékony figyelési stratégia segít megérteni az összetevők működését, és növelni az üzemidőt az értesítésekkel. Olvassa el a Figyelés áttekintését, amely az [Azure-alkalmazások és -erőforrások figyelése](../monitoring/monitoring-overview.md)során használt különböző szolgáltatásokat ismerteti.

## <a name="configure"></a>Konfigurálás

A konfigurálás az erőforrások kezdeti üzembe helyezésére és konfigurálására, valamint a folyamatos karbantartásra vonatkozik.
Ezeknek a feladatoknak az automatizálása lehetővé teszi a redundancia kiküszöbölését, minimalizálva az időt és energiát, valamint növelve a pontosságot és a hatékonyságot. Az [Azure Automation](../automation/automation-intro.md) tartalmazza azon szolgáltatások nagy részét, amelyek a konfigurációs feladatok automatizálásához szükségesek. Míg a runbookok kezelik a folyamatautomatizálást, a konfiguráció és a frissítéskezelés segít a konfiguráció kezelésében.

## <a name="govern"></a>Irányítás

Az irányítás azokat a mechanizmusok és folyamatokat biztosítja, amelyekre az Azure-beli alkalmazások és erőforrások szabályozásához van szükség. Magában foglalja a kezdeményezések megtervezését és a stratégiai prioritások meghatározását.
Az Azure-ban az irányítás implementálásának feladatát nagyrészt két szolgáltatás végzi el. [Az Azure Policy](./policy/overview.md) lehetővé teszi, hogy hozzon létre, rendeljen hozzá, és az erőforrások szabályok kényszerítése házirendeket. Ez a funkció a vállalati szabványoknak megfelelően tartja ezeket az erőforrásokat. [Az Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) lehetővé teszi a felhőhasználat és az Azure-erőforrások és más felhőszolgáltatók kiadásainak nyomon követését.

## <a name="secure"></a>Biztonságos

Az erőforrások és az adatok biztonságának kezelése. A biztonsági program magában foglalja a fenyegetések felmérését, az adatok gyűjtését és elemzését, valamint az alkalmazások és erőforrások megfelelőségét. A biztonsági figyelést és a fenyegetéselemzést az [Azure Security Center](../security-center/security-center-intro.md)biztosítja, amely egységes biztonsági felügyeletet és fejlett veszélyforrások elleni védelmet tartalmaz a hibrid felhőbeli számítási feladatok ban. Az Azure-erőforrások védelmével kapcsolatos átfogó információkért és útmutatásért tekintse [meg az Azure-biztonság](../security/fundamentals/overview.md) – bevezetés című témakört.

## <a name="protect"></a>Védelem

A védelem azt a területet teszi lehetővé, hogy az alkalmazások és az adatok továbbra is elérhetők maradnak, még akkor is, ha a kimaradások nem tartoznak az Ön hatáskörébe. Az Azure-ban erről a védelemről két szolgáltatás gondoskodik. Az [Azure Backup](../backup/backup-introduction-to-azure-backup.md) helyreállítható biztonsági másolatokat készít az adatokról, és azokat a felhőben vagy helyszíni gépeken tárolja. [Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) biztosítja az üzletmenet folytonosságát és azonnali helyreállítást egy katasztrófa során.

## <a name="migrate"></a>Migrate (Áttelepítés)

A migrálás a helyszíni gépen futó számítási feladatok átvitelét jelenti az Azure-fehőbe.
[Az Azure Migrate](../migrate/migrate-overview.md) egy olyan szolgáltatás, amely segít felmérni a helyszíni virtuális gépek Azure-ba való migrálási alkalmasságát. Az Azure Site Recovery virtuális gépeket telepít át [a helyszíni](../site-recovery/migrate-tutorial-on-premises-azure.md) vagy az Amazon Web [Services szolgáltatásból.](../site-recovery/migrate-tutorial-aws-azure.md) [Az Azure Database Migration](../dms/dms-overview.md) segítséget nyújt az adatbázis-források Azure Data-platformokra történő áttelepítésében.
