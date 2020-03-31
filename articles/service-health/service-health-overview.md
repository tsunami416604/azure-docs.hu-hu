---
title: A szolgáltatás állapotának áttekintése | Microsoft dokumentumok
description: Személyre szabott információk arról, hogy az Azure-alkalmazásokat hogyan befolyásolják az Azure-szolgáltatások jelenlegi és jövőbeli problémái és karbantartása.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898442"
---
# <a name="service-health-overview"></a>Szolgáltatásállapot – áttekintés

A Service Health egy testreszabható irányítópultot biztosít, amely nyomon követi az Azure-szolgáltatások állapotát azokban a régiókban, ahol használja őket. Ezen az irányítópulton nyomon követheti az aktív eseményeket, például a folyamatban lévő szolgáltatási problémákat, a közelgő tervezett karbantartást vagy a megfelelő egészségügyi tanácsokat. Amikor az események inaktívvá válnak, akár 90 napig is bekerülnek az ön kórtörténetébe. Végül a Service Health irányítópultsegítségével hozhat létre és kezelhet szolgáltatásállapot-riasztásokat, amelyek proaktív módon értesítik, ha a szolgáltatási problémák érintik Önt.

## <a name="service-health-events"></a>Szolgáltatás-állapotesemények

A Service Health négy féle állapoteseményt követ nyomon, amelyek hatással lehetnek az erőforrásokra:

1. **Szolgáltatási problémák** – Problémák az Azure-szolgáltatások, amelyek hatással vannak jelenleg. 
2. **Tervezett karbantartás** – Közelgő karbantartás, amely befolyásolhatja a szolgáltatások jövőbeli elérhetőségét.  
3. **Egészségügyi tanácsok** – Az Azure-szolgáltatások, amelyek a figyelmet. Ilyenek például az Azure-funkciók elavult, vagy ha túllépi a használati kvótát.
4. **Biztonsági tanácsok (előzetes verzió)** – Az Azure-szolgáltatások rendelkezésre állását befolyásoló, biztonsággal kapcsolatos értesítések.

> [!NOTE]
> A Szolgáltatásállapot-események megtekintéséhez a felhasználóknak meg kell [adni az olvasó szerepkört](../role-based-access-control/role-assignments-portal.md) egy előfizetésen.

## <a name="get-started-with-service-health"></a>Ismerkedés a Szolgáltatásállapotszolgáltatással

A Service Health irányítópult elindításához válassza a Szolgáltatásállapot csempét a portál irányítópultján. Ha korábban már eltávolította a csempét, vagy egyéni irányítópultot használ, keressen rá a Szolgáltatásállapot-szolgáltatásra a "További szolgáltatások" (az irányítópult bal alsó sarkában) című részben.

![Ismerkedés a Szolgáltatásállapotszolgáltatással](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>A szolgáltatásokat érintő aktuális problémák megtekintése

A **Szolgáltatás-problémák** nézet az Azure-szolgáltatásokban az erőforrásokat befolyásoló, folyamatban lévő problémákat jeleníti meg. Megértheti, hogy mikor kezdődött a probléma, és milyen szolgáltatásokat és régiókat érint. A legújabb frissítést is elolvashatja, hogy tisztában van azzal, hogy az Azure mit tesz a probléma megoldásához. 

![Szolgáltatási probléma kezelése](./media/service-health-overview/azure-service-health-overview-2.png)

Válassza a **Lehetséges hatás** lapot a saját erőforrásainak a probléma által esetlegesen érintett konkrét listájának megtekintéséhez. Letöltheti ezeknek az erőforrásoknak a CSV-listáját, amelyet megoszthat a csapatával.

![Szolgáltatási probléma kezelése - Hatás](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Linkeket és letölthető magyarázatokat kaphat 

A problémakezelő rendszerben használható probléma hivatkozását is beszerezheti. Pdf- és néha CSV-fájlokat tölthet le, amelyeket megoszthat olyan okkal, akik nem férnek hozzá az Azure Portalhoz.   

![Szolgáltatási probléma kezelése - Problémakezelés](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Támogatás beszerezni a Microsofttól

Lépjen kapcsolatba az ügyfélszolgálattal, ha az erőforrás a probléma megoldása után is rossz állapotban van.  Használja az oldal jobb oldalán található támogatási hivatkozásokat.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Személyre szabott állapottérkép rögzítése az irányítópulton

A Szolgáltatás állapotának szűrése az üzleti legkritikusabb előfizetések, régiók és erőforrástípusok megjelenítéséhez. Mentse a szűrőt, és rögzítse az állapotokat mutató, személyre szabott világtérképet a portál irányítópultján. 

![Személyre szabott állapottérkép szűrése](./media/service-health-overview/azure-service-health-overview-6a.png)

![Személyre szabott egészségügyi térkép rögzítése](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Szolgáltatásállapot-riasztások konfigurálása

A Service Health integrálható az Azure Monitorral, hogy e-mailekkel, szöveges üzenetekkel és webhook-értesítésekkel figyelmeztesse Önt, ha az üzleti legkritikusabb erőforrásokat érinti. Hozzon létre egy tevékenységnapló-riasztást a megfelelő szolgáltatásállapot-eseményhez. A riasztás tág antolhatja a riasztást a szervezet megfelelő embereinek a műveletcsoportok használatával. További információ: [Riasztások konfigurálása a szolgáltatás állapotához](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>További lépések

Állítsa be a riasztásokat, hogy értesítést kapjon az egészségügyi problémákról. További információt az [Azure Service Health Alerts beállításával kapcsolatos gyakorlati tanácsok című témakörben talál.](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s) 
