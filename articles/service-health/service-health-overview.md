---
title: Service Health áttekintése | Microsoft Docs
description: Személyre szabott információk arról, hogy az Azure-alkalmazások milyen hatással vannak a jelenlegi és jövőbeli Azure-szolgáltatásokkal kapcsolatos problémákra és karbantartásra.
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 6a06ae22493292e33c94cc83cba1f17361bce45f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451578"
---
# <a name="service-health-overview"></a>Service Health áttekintése

A Service Health testreszabható irányítópultot biztosít, amely nyomon követi az Azure-szolgáltatások állapotát azokon a régiókban, amelyeken azokat használja. Ezen az irányítópulton nyomon követheti az aktív eseményeket, például a folyamatos szolgáltatással kapcsolatos problémákat, a közelgő tervezett karbantartást vagy az érintett egészségügyi tanácsadókat. Ha az események inaktívvá válnak, a rendszer akár 90 napig helyezi el az állapotát. Végezetül használhatja a Service Health irányítópultot a szolgáltatás állapotával kapcsolatos riasztások létrehozásához és kezeléséhez, amelyek proaktívan értesítik Önt, ha a szolgáltatási problémák érintik Önt.

## <a name="service-health-events"></a>Események Service Health

A Service Health három olyan típusú eseményt követ nyomon, amelyek befolyásolhatják az erőforrásokat:

1. **Szolgáltatásokkal kapcsolatos problémák** – az Azure-szolgáltatások által jelenleg érintett problémák. 
2. **Tervezett karbantartás** – közelgő karbantartás, amely hatással lehet a szolgáltatások rendelkezésre állására a jövőben.  
3. **Egészségügyi tanácsadók** – a figyelmet igénylő Azure-szolgáltatások változásai. Ilyenek például az Azure-szolgáltatások elavulttá váltása, vagy ha túllépi a használati kvótát.

> [!NOTE]
> Service Health események megtekintéséhez a felhasználóknak meg kell [adni az olvasó szerepkört az](../role-based-access-control/role-assignments-portal.md) előfizetésben.

## <a name="get-started-with-service-health"></a>Ismerkedés a Service Health

A Service Health irányítópult elindításához válassza a portál irányítópultján a Service Health csempét. Ha korábban eltávolította a csempét, vagy egyéni irányítópultot használ, keressen rá Service Health szolgáltatásra a "További szolgáltatások" területen (az irányítópulton balra lent).

![Ismerkedés a Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Tekintse meg a szolgáltatásait érintő aktuális problémákat

A **szolgáltatással kapcsolatos problémák** nézet az Azure-szolgáltatásokban az erőforrásokat érintő esetleges problémákat mutatja. Megtudhatja, hogy mikor kezdődött el a probléma, és milyen szolgáltatásokat és régiókat érint. A legújabb frissítést is beolvashatja, hogy megtudja, mit tesz az Azure a probléma megoldásához. 

![Szolgáltatási probléma kezelése](./media/service-health-overview/azure-service-health-overview-2.png)

A **lehetséges hatás** lapon megtekintheti a probléma által befolyásolható erőforrások adott listáját. Letöltheti ezeket az erőforrásokat tartalmazó CSV-listát a csapattal való megosztáshoz.

![Szolgáltatási probléma kezelése – hatás](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Hivatkozások és letölthető magyarázatok beolvasása 

A probléma kezelésére szolgáló hivatkozásra kattintva kérheti le a problémát. A PDF-fájlok és a CSV-fájlok letöltése a megosztáshoz olyan személyekkel is megosztható, akik nem rendelkeznek hozzáféréssel a Azure Portalhoz.   

![Szolgáltatási probléma kezelése – probléma kezelése](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Támogatás a Microsofttól

Forduljon az ügyfélszolgálathoz, ha az erőforrás a probléma megoldása után is rossz állapotban marad.  Használja az oldal jobb oldalán található támogatási hivatkozásokat.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Személyre szabott állapotadatok rögzítése az irányítópulton

Az üzleti szempontból kritikus előfizetések, régiók és erőforrástípusok megjelenítéséhez Service Health szűrheti. Mentse a szűrőt, és rögzítse a személyre szabott állapotú globális térképet a portál irányítópultján. 

![Személyre szabott állapot-hozzárendelés szűrése](./media/service-health-overview/azure-service-health-overview-6a.png)

![Személyre szabott állapot-hozzárendelés rögzítése](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>A szolgáltatás állapotára vonatkozó riasztások konfigurálása

A Service Health integrálható Azure Monitorekkel, hogy e-mailben, szöveges üzenetben és webhook-értesítésekben figyelmeztessen az üzleti szempontból kritikus fontosságú erőforrások hatására. Hozzon létre egy műveletnapló riasztást a megfelelő szolgáltatási állapot eseményéhez. Irányítsa a riasztást a szervezet megfelelő tagjaira a műveleti csoportok használatával. További információ: [riasztások konfigurálása Service Healthhoz](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Következő lépések

Riasztások beállítása, hogy értesítést kapjon az állapottal kapcsolatos problémákról. További információ: [ajánlott eljárások Azure Service Health riasztások beállításához](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
