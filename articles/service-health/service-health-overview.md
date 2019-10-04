---
title: A Service Health áttekintése |} A Microsoft Docs
description: Személyre szabott információk az Azure-alkalmazások az Azure szolgáltatás jelenlegi és jövőbeli problémák és a karbantartás által érintett módját.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: a6f6288aebe599db794a7f15fc733563d26c66cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067100"
---
# <a name="service-health-overview"></a>A Service Health áttekintése

A Service Health biztosít egy testreszabható irányítópulton, amely nyomon követi a régiókban, ahol használja azokat az Azure-szolgáltatások állapotát. Ezen az irányítópulton követheti a folyamatban lévő szolgáltatással kapcsolatos problémák, a közelgő tervezett karbantartáshoz vagy a megfelelő állapottanácsadási információk aktív események. Amikor események válnak inaktívvá, azok elhelyezett egészségügyi előzményekben legfeljebb 90 napig. Végül használhatja a Szolgáltatásállapot-irányítópult létrehozása és kezelése a service health-riasztásokat, amelyek proaktívan értesíti, amint a szolgáltatással kapcsolatos problémák hatással vannak.

## <a name="service-health-events"></a>A Szolgáltatásállapot-események

A Service Health háromféle hatással lehet az erőforrások hálózatállapot-események nyomon követi:

1. **Szolgáltatási problémák** -kapcsolatos problémák az Azure-szolgáltatások Önt érintő most. 
2. **Tervezett karbantartás** –, amelyek hatással lehetnek a jövőben a szolgáltatások rendelkezésre állásának közelgő karbantartásokról.  
3. **Állapottanácsadási információk** -változások az Azure-szolgáltatások, figyelmet igénylő problémák. Ilyenek például az Azure-szolgáltatások avultak el, vagy ha túllépi a használati kvótát.

> [!NOTE]
> Service Health-események megtekintése, felhasználók olvasói szerepkörrel kell rendelkeznie egy előfizetést.

## <a name="get-started-with-service-health"></a>A Service Health használatának első lépései

Indítsa el a Service Health irányítópultján, válassza a szolgáltatás állapota csempe az irányítópulton. Ha korábban már eltávolította a csempére, vagy egyéni irányítópultot használ, keressen a "További szolgáltatások" a Service Health service (az irányítópult bal alsó).

![A Service Health használatának első lépései](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Tekintse meg az aktuális problémákat, amely hatással van a szolgáltatások

A **szolgáltatási problémák** nézetben látható minden folyamatban lévő probléma az Azure-szolgáltatások, amelyek negatív hatással vannak az erőforrások. Megismerheti, hogy a probléma keletkezésének időpontját, és az érintett szolgáltatásokat és régiókat. A legújabb frissítés az Azure mit tesz a probléma megoldásához megértéséhez is olvashatja. 

![Szolgáltatási probléma kezelése](./media/service-health-overview/azure-service-health-overview-2.png)

Válassza ki a **lehetséges hatás** fülre, és a probléma hatással lehet a saját erőforrásokat adott listájának megtekintéséhez. Ezek az erőforrások megosztása a csapatával a fürt megosztott kötetei szolgáltatás listáját töltheti le.

![Szolgáltatási probléma - hatás kezelése](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Hivatkozások és a letölthető magyarázatok beolvasása 

A problémához, amelyet felhasználhat problémakezelési rendszerében a hivatkozást is kap. PDF- és néha CSV-fájlok számára, akiknek nincs hozzáférésük az Azure Portalra töltheti le.   

![Szolgáltatási probléma - problémakezelés kezelése](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>A Microsoft támogatás

Ha az erőforrás hibás állapotban marad, még akkor is, miután a probléma megoldódott, forduljon az ügyfélszolgálathoz.  A lap jobb oldalán a támogatási hivatkozásokkal.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Rögzítés az Irányítópulton egy személyre szabott egészségügyi térkép

Szolgáltatás állapotának megjelenítése az üzleti szempontból kritikus fontosságú előfizetéseket, régiókat és erőforrástípusok szűrése. Mentse a szűrőt, és a portál irányítópultján egy személyre szabott egészségügyi világtérkép rögzítése. 

![Személyre szabott egészségügyi térkép szűrése](./media/service-health-overview/azure-service-health-overview-6a.png)

![PIN-kódot egy személyre szabott egészségügyi térkép](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Service health-riasztások konfigurálása

A Service Health integrálható az Azure Monitor riasztásra, e-mailek, szöveges üzenetben és webhook-értesítésekkel, ha az üzleti szempontból kiemelt fontosságú erőforrásokat érintő eseményekről. Állítsa be a megfelelő service health esemény egy tevékenységnapló-riasztás. A riasztás átirányítása a megfelelő személyeket a szervezetben Műveletcsoportok használatával. További információkért lásd: [riasztásainak konfigurálása a Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>További lépések

Riasztások beállítása, így állapotbeli probléma értesítést kap. További információkért lásd: [ajánlott eljárások az Azure Service Health-riasztások beállításával kapcsolatos](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
