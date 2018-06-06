---
title: (Másolás) Naplóelemzési riasztások kiterjeszti Azure riasztások – áttekintés |} Microsoft Docs
description: Áttekintése másolása riasztások a Naplóelemzési az OMS-portálon az Azure-riasztásokat, és részletesen vásárlói címzési gyakori kérdésekre.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: vinagara
ms.openlocfilehash: 296bdc6939ef8ab67656e5805cd603b50bce211e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763153"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Terjessze ki a Naplóelemzési riasztások Azure riasztások
Nemrég, amíg Azure Log Analytics tartalmazza a saját riasztási működését, ami proaktív értesítheti a feltételek Naplóelemzési adatok alapján.  Forráskörnyezetben végzett kezelési riasztási szabályok [Microsoft művelet felügyeleti csomaggal (OMS) portal](../operations-management-suite/operations-management-suite-overview.md). Az új riasztások felületet most integrálva van a riasztási élmény különböző szolgáltatásban a Microsoft Azure-ban. Az új felület áll rendelkezésre, mert **riasztások** alatt Azure figyelése az Azure portálon, és támogatja a tevékenység naplókat, metrikákat, a riasztás és Naplóelemzés és az Application Insights naplózza. 

## <a name="benefits-of-extending-your-alerts"></a>A riasztások kiterjesztése előnyei
Létrehozásának és kezelésének a riasztások az Azure port, például számos előnye van:

- Eltérően az az OMS-portálon, ahol csak 250 riasztások nem sikerült létrehozni és várólistákból. az Azure-riasztások Ez a korlátozás nincs jelen
- Az Azure riasztásokból a riasztási típusokat kezelhető, előnyeiről, és megtekinthetők; nem csak a Naplóelemzési riasztást küld, mint korábban
- Felhasználók csak a figyelést és riasztást használatával való hozzáférés szabályozása [Azure figyelő szerepkör](monitoring-roles-permissions-security.md)
- Az Azure riasztások használatára [művelet csoportok](monitoring-action-groups.md), amely lehetővé teszi, hogy az egyes riasztások SMS, beleértve a több művelet küldése a telefonhívás, egy Automation-Runbook meghívása, egy Webhook meghívása, egy ITSM összekötő, és több konfigurálása. 

## <a name="process-of-extending-your-alerts"></a>A folyamat, amely a figyelmeztetéseket
A Naplóelemzési riasztások áthelyezése Azure riasztásokat, a folyamat nem **nem** a riasztás definíciója, lekérdezés vagy konfiguráció az bármely módon módosítani. Mindössze annyi a változás, szükség, hogy Azure, az összes műveletek, például az e-mailben értesítést, a webhook hívás, egy Automation-runbook fut, vagy a ITSM eszköz csatlakozni készül művelet csoport használata. Ha a művelet csoport már társítva van a riasztás - ezek fognak szerepelni az Azure kiterjesztésekor.

> [!NOTE]
> A Microsoft automatikusan kiterjed Azure riasztás indítása a Naplóelemzési létrehozott riasztások **2018. május 14.** ismétlődő sorozat befejeződéséig. Ha olyan problémák merülnek létrehozásakor [művelet csoportok](monitoring-action-groups.md), felhasználó használhatja- [felsorolt javítási lépéseket](monitoring-alerts-extend-tool.md#troubleshooting) keretein belül **2018 július 5** automatikusan létrehozott művelet csoportok segítségével. 
> 

Naplóelemzési munkaterület riasztások ütemezett kiterjesztése az Azure-ba, amikor azok továbbra is használhatók, és a rendszer **nem** valamilyen módon veszélyeztetheti a konfigurációt. Ütemezése, a riasztások nem érhető el módosítása/szerkesztésre ideiglenesen; azonban ebben az időszakban hozható létre új Azure riasztásokat továbbra is. Ha megpróbálja szerkeszteni vagy létrehozni a riasztásokat az OMS-portálon, hogy a beállítást, így folytathatja a hozza létre őket a Naplóelemzési munkaterület vagy az Azure-értesítések az Azure portálon.

 ![Ütemezett időszak alatt a riasztások átirányítja az Azure-bA felhasználói művelet](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> az Azure Log Analytics riasztások kiterjedő nem függő díj terheli a fiók és a használati Azure riasztások lekérdezés alapú Naplóelemzési riasztások alapján nem számlázzuk keretein belül használatakor, és meghatározott feltételek [házirend árképzési Azure-figyelő](https://azure.microsoft.com/pricing/details/monitor/)  

A riasztások kiterjesztése előtt ez a dátum szerint áthelyezheti őket Azure riasztások önkéntesen engedélyezés előnyeit élvezheti.

### <a name="how-to-voluntarily-extend-your-alerts"></a>A riasztások önkéntesen bővítése
A riasztások Azure riasztások kiterjesztéséhez befejezheti a feladatot a munkaterület két módszer vannak megadva.  Ez elvégezhető vagy az OMS-portálon vagy programozottan egy új API-val elérhető varázsló.  További információkért lásd: [riasztások kiterjeszti az OMS-portálon és API-t használó Azure](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>A riasztások történő bővítése után élmény
Után Azure riasztásokat a riasztások kiterjednek, azok továbbra is elérhető a felügyeleti OMS-portálon nem működnek, mint a korábban.<br><br> ![Az Azure-ba történő kiterjesztése után értesítések listázása OMS-portálon](./media/monitor-alerts-extend/PostExtendList.png)

Ha megpróbálja szerkesztheti a meglévő riasztást, vagy hozzon létre egy új riasztás az OMS-portálon, ekkor automatikusan megnyílik Azure riasztásokat.  

> [!NOTE]
> Fontos annak érdekében, hogy az egyéni felhasználók számára, akik hozzáadása vagy szerkesztése riasztások rendelt engedélyeket állít be megfelelően az Azure-ban.  Tekintse át, [Azure figyelő és riasztás használatára vonatkozó engedélyek](monitoring-roles-permissions-security.md) tudni, hogy milyen engedélyeket kell biztosítania.  
> 

Riasztás létrehozása továbbra is működnek a [napló Analytics API](../log-analytics/log-analytics-api-alerts.md) és [napló Analytics erőforrás sablon](../monitoring/monitoring-solutions-resources-searches-alerts.md), csak egy kisebb változtatni adott kell alkalmazni – művelet csoportok kell szerepelni.

## <a name="next-steps"></a>További lépések

* További tudnivalók az eszközöket, amelyekkel [kezdeményezhet az Azure Naplóelemzés riasztások kiterjedő](monitoring-alerts-extend-tool.md)
* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* Megtudhatja, hogyan hozzon létre [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
