---
title: Állapotfigyelő szolgáltatás áttekintése |} Microsoft Docs
description: Személyre szabott információk az Azure-alkalmazásokban az Azure szolgáltatás jelenlegi és jövőbeli problémák és karbantartási által érintett hogyan.
services: Resource health
documentationcenter: ''
author: rboucher
manager: ''
editor: ''
ms.assetid: ''
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: robb
ms.openlocfilehash: f7fd27dc9be161059dc62071fee33374106e18ef
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061281"
---
# <a name="service-health"></a>Service Health
Szolgáltatás állapota egy testre szabható irányítópultot, amely nyomon követi a régiókban, ahol használhatja azokat az Azure-szolgáltatások állapotáról biztosít. Az irányítópulton nyomon követheti a folyamatban lévő szolgáltatásokkal kapcsolatos problémákról, küszöbönálló tervezett karbantartási vagy vonatkozó egészségügyi tanácsadók aktív események. Események inaktív állapotba, ha azok elhelyezett állapotfigyelő előzményekben 90 napig. Végezetül segítségével a szolgáltatás irányítópult létrehozása és kezelése a szolgáltatás állapotát, de riasztások proaktív értesítést küldenek, ha a szolgáltatás problémák vannak érinti Önt.

## <a name="service-health-events"></a>Szolgáltatás állapotával kapcsolatos események
Szolgáltatás állapotát követi nyomon, hogy háromféle állapotával kapcsolatos események, amely hatással lehet az erőforrások:
1. **Problémák szolgáltatás** -Azure-szolgáltatás most előforduló problémákat. 
2. **A tervezett karbantartások** -, amelyek hatással lehetnek a jövőben a szolgáltatások rendelkezésre állásának közelgő karbantartásokról.  
3. **Állapotfigyelő tanácsadók** -Azure-szolgáltatások figyelmet igénylő változásairól. Például ha az Azure-funkció elavult, vagy ha az túllépi a memóriahasználati kvóta.

## <a name="get-started-with-service-health"></a>Ismerkedés a szolgáltatás állapota
A szolgáltatás állapota irányítópult elindításához válassza ki a szolgáltatás állapotát a portál irányítópultján csempéjén. Ha korábban eltávolította a csempe, vagy egyéni irányítópult használata, keresse meg a "További szolgáltatások" szolgáltatás Állapotfigyelő szolgáltatás (az irányítópulton bal alsó).

![Ismerkedés a szolgáltatás állapota](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Aktuális problémák, amely hatással van a szolgáltatások lásd:
A **problémák szolgáltatás** a nézet jeleníti meg a folyamatban lévő problémákat, az Azure-szolgáltatásokat, hogy az erőforrások vannak hatással. Ha a probléma ekkor kezdődött, és milyen szolgáltatásokat és régiók érintett tudja értelmezni. A legújabb frissítés megértéséhez Azure tevékenységeit a probléma megoldásához is olvasható. 

![Szolgáltatási probléma kezelése](./media/service-health-overview/azure-service-health-overview-2.png)

Válassza ki a **célgyűjtemény** fülre, és Ön a tulajdonosa, előfordulhat, hogy a probléma által érintett erőforrások adott listája látható. Az alábbi forrásokból megoszthatja munkatársaival CSV listája töltheti le.

![Szolgáltatási probléma - hatás kezelése](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Hivatkozások és a letölthető magyarázata 
Egy hivatkozást a hibát a probléma felügyeleti rendszer használata a kérheti le. PDF- és egyes esetekben CSV-fájlok megosztása nem férnek hozzá az Azure-portálon személyek töltheti le.   

![Szolgáltatási probléma - problémakezelés kezelése](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Segítségre van szüksége a Microsofttól
Ha az erőforrás hibás állapotban marad, ha a probléma megoldódott, forduljon a támogatási szolgálathoz.  A lap jobb támogatása hivatkozásokkal.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Rögzítés az irányítópulton való személyre szabott egészségügyi térképre
Szűrés jelenítse meg az üzleti szempontból kritikus fontosságú előfizetések, régiók és erőforrástípusok szolgáltatás állapotát. A szűrő mentéséhez, és a PIN kódok egy személyre szabott egészségügyi globális leképezés a portál Irányítópultjára. 

![Szűrő személyre szabott egészségügyi leképezése](./media/service-health-overview/azure-service-health-overview-6a.png)

![PIN-kód egy személyre szabott egészségügyi térkép](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Állapotfigyelő szolgáltatásriasztások konfigurálása
Azure figyelő riasztást, e-maileket, a szöveges üzenetek és a webhook értesítések, ha az üzleti szempontból kulcsfontosságú erőforrások érintett integrálható a szolgáltatás állapotát. Állítsa be a megfelelő szolgáltatásra állapotesemény napló tevékenység riasztást. Riasztás átirányíthatja az illetékes személyek férhessenek hozzá a szervezet művelet csoportok használatával. További információkért lásd: [riasztásainak konfigurálása szolgáltatásának állapota](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

# <a name="next-steps"></a>További lépések
Riasztások beállítása, így ügynökállapottal kapcsolatos hibákkal értesítést kap. További információkért lásd: [riasztások konfigurálása a szolgáltatás állapotát](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
