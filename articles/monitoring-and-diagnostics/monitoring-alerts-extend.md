---
title: (Másolás) Naplóelemzési riasztások kiterjeszti Azure riasztások – áttekintés
description: Áttekintése másolása riasztások a Naplóelemzési az OMS-portálon az Azure-riasztásokat, és részletesen vásárlói címzési gyakori kérdésekre.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6484142eafa8388117c1e96ab31eefeab188e488
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750272"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Terjessze ki a Naplóelemzési riasztások Azure riasztások
Nemrég, amíg Azure Log Analytics tartalmazza a saját riasztási működését, ami proaktív értesítheti a feltételek Naplóelemzési adatok alapján. Riasztási szabályok a Microsoft Operations Management Suite-portálon kezelt meg. Az új riasztások felületet most integrálva van a Microsoft Azure-ban a különböző szolgáltatásokhoz különböző riasztások. Ez **riasztások** alatt Azure figyelése az Azure portálon, és támogatja a tevékenység naplókat, metrikákat, a riasztás és Naplóelemzési és Azure Application Insights naplózza. 

## <a name="benefits-of-extending-your-alerts"></a>A riasztások kiterjesztése előnyei
Létrehozásának és kezelésének, mint az Azure-portálon riasztások több előnye van:

- Eltérően az Operations Management Suite-portálon, ahol csak 250 riasztások létrehozása sikerült, és megtekinthetők, Azure riasztások nincs ilyen-korlátozás van érvényben.
- Az Azure riasztások kezelése, enumerálásához és megtekintése a riasztási típusokat. Korábban sikerült csak ezt teszi Naplóelemzési riasztások esetén.
- A felhasználóknak, hogy csak a figyelést, és riasztást a hozzáférés korlátozható a [Azure figyelő szerepkör](monitoring-roles-permissions-security.md).
- Azure riasztásokat használhat [művelet csoportok](monitoring-action-groups.md). Ez lehetővé teszi, hogy az egyes riasztások egynél több művelettel rendelkezik. SMS, akkor küldjön a telefonhívás, egy Azure Automation-runbook meghívása, egy webhook meghívása és konfigurálja az informatikai szolgáltatás-felügyeleti (ITSM) összekötőt. 

## <a name="process-of-extending-your-alerts"></a>A folyamat, amely a figyelmeztetéseket
Naplóelemzési riasztások áthelyezése Azure riasztások folyamata nem tartalmaz, amely a riasztás definíciója, lekérdezés vagy bármely olyan módon konfiguráció módosítása. Mindössze annyi a változás, szükség, hogy az Azure, akkor minden művelet végrehajtására egy művelet csoport használatával. Ha a művelet csoport már társítva van a riasztás, nem foglalja azokat az Azure kiterjesztésekor.

> [!NOTE]
> Microsoft automatikusan kiterjed Naplóelemzési Azure riasztás létre riasztásokat, kezdve a 2018. május 14., ismétlődő több befejeződéséig. Ha problémába ütközik a létrehozása [művelet csoportok](monitoring-action-groups.md), használjon [következő javítási lépéseket](monitoring-alerts-extend-tool.md#troubleshooting) automatikusan létrehozott művelet csoportok segítségével. 2018. július 5. amíg használhatja ezeket a lépéseket. 
> 

A Naplóelemzési munkaterület Azure kell terjeszteni a riasztások ütemezésekor működik, és nem a módon veszélyeztetheti a konfigurációs mindaddig. Ütemezése, a riasztások előfordulhat, hogy nem érhető el módosításra ideiglenesen, de továbbra is hozzon létre új Azure-riasztások ebben az időszakban. Ha megpróbálja szerkeszteni vagy létrehozni a riasztásokat az Operations Management Suite-portálról, lehetősége van a Naplóelemzési munkaterület hozza létre őket a folytatáshoz. Választhatja azt is, az Azure-portálon az Azure riasztásokból létrehozni őket.

 ![Képernyőkép a beállítás Naplóelemzési vagy Azure riasztásokat a riasztások létrehozásához](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Az Azure Log Analytics riasztások kiterjedő nem függő díj terheli a fiókjához. Azure riasztások használata lekérdezés alapú Naplóelemzési riasztások nem számlázva, keretein belül használatakor, és meghatározott feltételek a [házirend árképzési Azure figyelő](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>A riasztások önkéntesen bővítése
Azure riasztásokat a riasztások kiterjesztéséhez érhető el az Operations Management Suite portálját varázsló használatával, vagy teheti úgy programozottan az API-k használatával. További információkért lásd: [kiterjesztése a riasztásokat az Operations Management Suite-portál és API használatával Azure](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>A riasztások történő bővítése után élmény
Azure riasztásokat a riasztások kiterjednek, miután azok továbbra is elérhetők az Operations Management Suite-felügyeleti portálon nem működnek, mint a előtt.

![Képernyőkép az Operations Management Suite portálját, a riasztások felsorolt](./media/monitor-alerts-extend/PostExtendList.png)

Ha megpróbálja szerkesztheti a meglévő riasztást, vagy hozzon létre egy új riasztás az Operations Management Suite portálját, ekkor automatikusan megnyílik Azure riasztásokat.  

> [!NOTE]
> Győződjön meg arról, hogy az egyéni felhasználók számára, akik hozzáadása vagy szerkesztése riasztások rendelt engedélyeket állít be megfelelően az Azure-ban. Szeretné megtudni, milyen engedélyeket kell biztosítania, lásd: [Azure figyelő és riasztás használatára vonatkozó engedélyek](monitoring-roles-permissions-security.md).  
> 

Továbbra is az értesítések a [napló Analytics API](../log-analytics/log-analytics-api-alerts.md) és [napló Analytics erőforrás sablon](../monitoring/monitoring-solutions-resources-searches-alerts.md). Ha így tesz, meg kell adnia művelet csoportok.

## <a name="next-steps"></a>További lépések

* További tudnivalók az eszközöket, amelyekkel [kezdeményezhet az Azure Naplóelemzés riasztások kiterjedő](monitoring-alerts-extend-tool.md).
* További információ a [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* Megtudhatja, hogyan hozzon létre [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
