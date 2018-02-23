---
title: "Az Azure szolgáltatás állapota áttekintése |} Microsoft Docs"
description: "Személyre szabott információk az Azure-alkalmazásokban az Azure szolgáltatás jelenlegi és jövőbeli problémák és karbantartási által érintett hogyan."
services: Resource health
documentationcenter: 
author: rboucher
manager: 
editor: 
ms.assetid: 
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/07/2017
ms.author: robb
ms.openlocfilehash: 5f2b68e800f484a64e79f965fc855aebe090186a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-health"></a>Azure Service Health
Azure szolgáltatás állapota megfelelő időben és személyre szabott kapcsolatos információkat biztosít, ha az Azure-szolgáltatásokban problémák hatással van a szolgáltatások.  Emellett segítséget nyújt küszöbönálló tervezett karbantartási előkészítése.

## <a name="service-health-events"></a>Szolgáltatás állapotával kapcsolatos események
Szolgáltatás állapotát követi nyomon, hogy háromféle állapotával kapcsolatos események, amely hatással lehet az erőforrások:
1. **Problémák szolgáltatás** -Azure-szolgáltatás most előforduló problémákat. 
2. **A tervezett karbantartások** -, amelyek hatással lehetnek a jövőben a szolgáltatások rendelkezésre állásának közelgő karbantartásokról.  
3. **Állapotfigyelő tanácsadók** -Azure-szolgáltatások figyelmet igénylő változásairól. Például ha az Azure-funkció elavult, vagy ha az túllépi a memóriahasználati kvóta.

    ![Szolgáltatás állapotával kapcsolatos események](./media/service-health-overview/azure-service-health-overview-7.png)

## <a name="get-started-with-service-health"></a>Ismerkedés a szolgáltatás állapota
A szolgáltatás állapota irányítópult elindításához válassza ki a szolgáltatás állapotát a portál irányítópultján csempéjén. 

![Ismerkedés a szolgáltatás állapota](./media/service-health-overview/azure-service-health-overview-1.png)

Ha korábban eltávolította a csempe, vagy egyéni irányítópult használata, keresse meg a szolgáltatás Állapotfigyelő szolgáltatás "**minden szolgáltatás**" (az irányítópulton bal felső).

![Ismerkedés a szolgáltatás állapota](./media/service-health-overview/azure-service-health-overview-1a.png)

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
![Szűrő személyre szabott egészségügyi térkép](./media/service-health-overview/azure-service-health-overview-6a.png)
![PIN-kód egy személyre szabott egészségügyi térkép](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Szolgáltatás állapota riasztások konfigurálása
Az Azure szolgáltatás állapota Azure figyelő riasztást, e-maileket, a szöveges üzenetek és a webhook értesítések, ha az üzleti szempontból kulcsfontosságú erőforrások érintett integrálható. Állítsa be a megfelelő szolgáltatásának állapota esemény napló tevékenység riasztást. Riasztás átirányíthatja az illetékes személyek férhessenek hozzá a szervezet művelet csoportok használatával. További információkért lásd: [riasztásainak konfigurálása szolgáltatásának állapota](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

 
