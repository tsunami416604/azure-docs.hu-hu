---
title: Az Azure Management áttekintése – Azure-irányítás
description: Az Azure-alkalmazások és-erőforrások kezelésével kapcsolatos területek áttekintése, amelyek az Azure felügyeleti eszközein található tartalmakra mutató hivatkozásokat tartalmaznak.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980904"
---
# <a name="overview-of-management-services-in-azure"></a>Az Azure-beli felügyeleti szolgáltatások áttekintése

Az Azure-beli irányítás az Azure-felügyelet egyik aspektusa. Ez a cikk az erőforrások Azure-beli üzembe helyezésére és karbantartására szolgáló különböző felügyeleti területeket ismerteti.

Felügyelet alatt azokat a feladatokat és folyamatokat értjük, amelyekre szükség van az üzleti alkalmazások és az azokat támogató erőforrások kezeléséhez. Az Azure számos szolgáltatással és eszközzel működik együtt a teljes körű felügyelet biztosításához. Ezek a szolgáltatások nem csak az Azure-beli erőforrásokhoz, hanem más felhőben és a helyszínen is elérhetők. A teljes felügyeleti környezet kialakításának első lépése a különböző eszközök és azok együttműködésének ismertetése.

A következő ábra azt illusztrálja, hogy milyen felügyeleti területekre van szükség az alkalmazások és erőforrások kezeléséhez. Ezek a különböző területek életciklusként is megtekinthetők. Minden területnek folyamatos egymásutánban kell lennie egy erőforrás élettartama alatt. Ez az erőforrás-életciklus a kezdeti üzembe helyezéssel, a folyamatos művelettel, végül pedig a kivonással kezdődik.

![Az Azure-beli felügyeleti szabályok](../monitoring/media/management-overview/management-capabilities.png)

Egyetlen Azure-szolgáltatás sem tölti ki teljesen az adott felügyeleti terület követelményeit. Ehelyett több szolgáltatás is működik együtt. Egyes szolgáltatások, például a Application Insights, a webalkalmazások számára biztosítanak célként szolgáló figyelési funkciókat. Mások, például Azure Monitor naplók, más szolgáltatásokhoz tartozó felügyeleti adattárakat tárolnak. Ez a funkció lehetővé teszi a különböző szolgáltatások által gyűjtött különböző típusú adatok elemzését.

A következő szakaszok röviden ismertetik az egyes felügyeleti területeket, és az ezek kezelésére hivatott Azure-szolgáltatások részletes ismertetésére mutató hivatkozásokat adnak meg.

## <a name="monitor"></a>Figyelés

A monitorozás az adatok összegyűjtésére és elemzésére szolgál az erőforrások teljesítményének, állapotának és rendelkezésre állásának naplózása érdekében. Egy hatékony figyelési stratégia segíti az összetevők működésének megértését és az értesítésekkel való üzemidő növelését. A monitoring áttekintése, amely az [Azure-alkalmazások és-erőforrások figyeléséhez](../monitoring/monitoring-overview.md)használt különböző szolgáltatásokat fedi le.

## <a name="configure"></a>Konfigurálás

A configure az erőforrások kezdeti üzembe helyezésére és konfigurálására, valamint a folyamatos karbantartásra utal.
Ezeknek a feladatoknak az automatizálásával elkerülhető a redundancia, ami minimalizálja az időt és a fáradságot, és növeli a pontosságot és a hatékonyságot. Az [Azure Automation](../automation/automation-intro.md) tartalmazza azon szolgáltatások nagy részét, amelyek a konfigurációs feladatok automatizálásához szükségesek. Míg a runbookok kezeli a folyamatok automatizálását, a konfigurációt és az Update Management segítséget a konfiguráció kezelésében.

## <a name="govern"></a>Irányítás

Az irányítás azokat a mechanizmusok és folyamatokat biztosítja, amelyekre az Azure-beli alkalmazások és erőforrások szabályozásához van szükség. Magában foglalja a kezdeményezések megtervezését és a stratégiai prioritások meghatározását.
Az Azure-ban az irányítás implementálásának feladatát nagyrészt két szolgáltatás végzi el. [Azure Policy](./policy/overview.md) lehetővé teszi a szabályzat-definíciók létrehozását, hozzárendelését és kezelését az erőforrások szabályainak érvényesítéséhez. Ez a szolgáltatás a vállalati szabványoknak megfelelően tartja meg ezeket az erőforrásokat. A [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) segítségével nyomon követheti az Azure-erőforrások és más felhőalapú szolgáltatók Felhőbeli használatát és kiadásait.

## <a name="secure"></a>Biztonságos

Kezelheti erőforrásai és adatai biztonságát. A biztonsági program magában foglalja a fenyegetések felmérését, az adatok gyűjtését és elemzését, valamint az alkalmazások és erőforrások megfelelőségét. A biztonsági monitorozási és a veszélyforrások elemzését [Azure Security Center](../security-center/security-center-intro.md)biztosítja, amely magában foglalja az egységes biztonsági felügyeletet és az összetett veszélyforrások elleni védelmet a hibrid Felhőbeli számítási feladatokban. Lásd: az Azure [Security bemutatása](../security/fundamentals/overview.md) – átfogó információk és útmutatás az Azure-erőforrások biztonságossá tételéhez.

## <a name="protect"></a>Védelem

A védelem arra utal, hogy az alkalmazások és az adatszolgáltatások elérhetők maradnak, még a szabályozáson kívüli kimaradások esetén is. Az Azure-ban erről a védelemről két szolgáltatás gondoskodik. Az [Azure Backup](../backup/backup-introduction-to-azure-backup.md) helyreállítható biztonsági másolatokat készít az adatokról, és azokat a felhőben vagy helyszíni gépeken tárolja. A [Azure site Recovery](../site-recovery/site-recovery-overview.md) az üzletmenet folytonosságát és az azonnali helyreállítást biztosítja a katasztrófák során.

## <a name="migrate"></a>Migrate (Áttelepítés)

A migrálás a helyszíni gépen futó számítási feladatok átvitelét jelenti az Azure-fehőbe.
A [Azure Migrate](../migrate/migrate-overview.md) egy olyan szolgáltatás, amely segít a helyszíni virtuális gépek Azure-ba való áttelepítésének értékelésében. [A Azure site Recovery a](../site-recovery/migrate-tutorial-on-premises-azure.md) virtuális gépeket a helyszíni rendszerből vagy [Amazon Web Servicesból](../site-recovery/migrate-tutorial-aws-azure.md)telepíti át. Az [Azure Database Migration](../dms/dms-overview.md) segítséget nyújt az adatbázis-források Azure-beli adatplatformokra való áttelepítésében.
