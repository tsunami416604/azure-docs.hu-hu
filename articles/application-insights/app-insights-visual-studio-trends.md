---
title: Trendek elemzése a Visual Studióban | Microsoft Docs
description: Elemezhet, megjeleníthet és felfedezhet trendeket a Visual Studióban található Application Insights telemetriáival.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7de545870b87d4b221bba8eba32652a00c9ad2b2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139913"
---
# <a name="analyzing-trends-in-visual-studio"></a>Trendek elemzése a Visual Studióban
Az Application Insights Trends eszköze megjeleníti webalkalmazásai fontos telemetria-eseményeinek időbeli változásait, ezzel segítve a problémák és hibák gyors azonosítását. Azáltal, hogy részletesebb diagnosztikai információhoz ad hozzáférést, a Trends segíthet az alkalmazás teljesítményének növelésében, a kivételek okának megtalálásában, valamint az egyéni eseményeire vonatkozó háttérismeretek feltárásában.

![Példa a Trends ablakára](./media/app-insights-visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>A webapp konfigurálása az Application Insightshoz

Ha még nem tette meg, [konfigurálja a webappot az Application Insightshoz](app-insights-overview.md). Ez lehetővé teszi a webapp számára, hogy telemetriát küldjön az Application Insights portálra. A Trends eszköz innen olvassa be a telemetriát.

Az Application Insights Trends a Visual Studio 2015 Update 3 és újabb verziókban érhető el.

## <a name="open-application-insights-trends"></a>Az Application Insights Trends megnyitása
Az Application Insights Trends ablak megnyitása:

* Az Application Insights eszköztárgombjáról válassza az **Explore Telemetry Trends** (Telemetriatrendek megtekintése) lehetőséget, vagy
* A projekt helyi menüjéből válassza az **Application Insights > Explore Telemetry Trends** (Telemetriatrendek megtekintése) lehetőséget, vagy
* A Visual Studio menüsorából válassza a **View > Other Windows > Application Insights Trends** (Nézet > Egyéb ablakok > Application Insights Trends) lehetőséget.

A rendszer kérheti egy erőforrás kiválasztását. Kattintson a **Select a resource** (Erőforrás kiválasztása) elemre, jelentkezzen be Azure-előfizetéssel, majd válasszon egy olyan Application Insights-erőforrást a listából, amelynek a telemetriatrendjeit elemezni szeretné.

## <a name="choose-a-trend-analysis"></a>Trendelemzés kiválasztása
![Trendelemzések általános típusainak menüje](./media/app-insights-visual-studio-trends/app-insights-trends-1-750.png)

Első lépésként válasszon egyet az öt általános trendelemzési típus közül, amelyek mindegyike az elmúlt 24 órából származó adatokat elemzi:

* **Teljesítményhibák vizsgálata kiszolgálókérelmeknél** – A szolgáltatásához beérkezett kérelmek, válaszidő szerint csoportosítva
* **Hibák elemzése kiszolgálókérelmekben** – A szolgáltatásához beérkezett kérelmek, HTTP-válaszidő szerint csoportosítva
* **Az alkalmazásban található kivételek vizsgálata** – A szolgáltatásban található kivételek, a kivétel típusa szerint csoportosítva
* **Az alkalmazás-függőségek teljesítményének ellenőrzése** – A szolgáltatása által hívott szolgáltatások, válaszidő szerint csoportosítva
* **Egyéni események vizsgálata** – A szolgáltatáshoz beállított egyéni események, az esemény típusa szerint csoportosítva.

Ezeket az előzetesen összeállított elemzéseket később a Trends ablak bal felső sarkában található **View common types of telemetry analysis** (Telemetriaelemzés általános típusainak megtekintése) gombbal érheti el.

## <a name="visualize-trends-in-your-application"></a>Trendek megjelenítése az alkalmazásban
Az Application Insights Trends idősorozatos megjelenítést hoz létre az alkalmazás telemetriája alapján. Minden idősorozatos megjelenítés egy, a telemetria egyik tulajdonsága alapján csoportosított, adott időtartományba eső telemetriatípust jelenít meg. Előfordulhat például, hogy szeretné megtekinteni az elmúlt 24 óra kiszolgálókérelmeit a kiindulási ország szerint csoportosítva. Ebben a példában a képi megjelenítés buborékai az egyes országokhoz/régiókhoz tartozó kiszolgálókérelmek egy órára vonatkoztatott számát jelképezik.

Használja az ablak tetején található vezérlőket annak módosításához, hogy milyen típusú telemetriákat szeretne megjeleníteni. Először válassza ki az Önt érdeklő telemetriatípust:

* **Telemetria típusa** -kiszolgálókérelmek, kivételek, függőségek vagy egyéni események
* **Időtartomány** – Bármely időpont az elmúlt 30 perctől az elmúlt 3 napig
* **Csoportosítási szempont** – Kivétel típusa, probléma azonosítója, ország/régió, és továbbiak.

Ezután kattintson az **Analyze Telemetry** (Telemetria elemzése) lehetőségre a lekérdezés futtatásához.

Navigáció a képi megjelenítésben található buborékok között:

* Kattintson egy buborék kiválasztásához. Ezzel frissíti az ablak alján található szűrőket, csak azokat az eseményeket összegezve, amelyekre egy adott időszakban került sor.
* Kattintson duplán egy buborékra a Search eszköz eléréséhez és azoknak az egyéni telemetriaeseményeknek a megtekintéséhez, amelyek az adott időszakban következtek be.
* Nyomja le a CTRL billentyűt, és kattintson egy buborékra, ha meg szeretné szüntetni a kijelölését a képi megjelenítésben.

> [!TIP]
> A Trends és a Search eszköz együttműködése segít abban, hogy több ezer telemetriaesemény között is megtalálhassa a szolgáltatás hibáinak okát. Ha például az ügyfelei egyszer csak észreveszik, hogy az alkalmazás lassabban reagál, kezdjen a Trends használatával. Elemezze a szolgáltatáshoz az elmúlt néhány órában beérkezett kérelmeket, válaszidő szerint csoportosítva. Ellenőrizze, hogy talál-e lassú kérelmekből álló, szokatlanul nagy fürtöt. Kattintson duplán az érintett buborékra a Search eszköz eléréshez, hogy kiszűrje az érintett kéréseseményeket. A Search-ben feltárhatja ezeknek a kérelmeknek a tartalmát, és a probléma megoldására vonatkozó kódhoz navigálhat.
> 
> 

## <a name="filter"></a>Szűrés
Az ablak alján található szűrővezérlőkkel specifikusabb trendeket is feltárhat. Szűrő alkalmazásához kattintson a szűrő nevére. Gyorsan válthat különböző szűrők között, így olyan trendeket is feltárhat, amelyek a telemetria egy adott dimenziójában rejtőznek. Ha egy dimenzióban szűrőt alkalmaz, például a kivétel típusára vonatkozót, a többi dimenzióban található szűrőkre akkor is rá tud kattintani, ha kiszürkítve jelennek meg. Szűrő használatának a visszavonásához kattintson újra rá. Ctrl+kattintással egy dimenzión belül több szűrőt is kijelölhet.

![Trendszűrők](./media/app-insights-visual-studio-trends/TrendsFiltering-750.png)

Mi a teendő, ha több szűrőt szeretne alkalmazni? 

1. Alkalmazza az első szűrőt. 
2. Kattintson az **Apply selected filters and query again** (Kiválasztott szűrők alkalmazása és újbóli lekérdezés) gombra az első szűrő dimenziónevénél. Ez újra lekérdezi a telemetriát azokhoz az eseményekhez, amelyek megfelelnek az első szűrőnek. 
3. Alkalmazza a második szűrőt. 
4. Ismételje meg a folyamatot a trendek kimutatásához a telemetria adott alkészleteiben. Megkeresheti például a „GET Home/Index” nevű *és* Németországból érkezett *és* 500-as válaszkóddal rendelkező kiszolgálókérelmeket. 

Szűrő visszavonásához kattintson a **Remove selected filters and query again** (Kiválasztott szűrők eltávolítása és újbóli lekérdezés) gombra a dimenziónál.

![Több szűrő](./media/app-insights-visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Rendellenességek keresése
A Trends eszköz képes kiemelni azon események buborékait, amelyek az ugyanazon idősorozatban található buborékokkal összehasonlítva rendellenesnek mutatkoznak. A View Type (Nézettípus) legördülő menüben válassza a **Counts in time bucket (highlight anomalies)** (Időtartamra vonatkozó számérték – rendellenességek kiemelése) vagy a **Percentages in time bucket (highlight anomalies)** (Időtartamra vonatkozó százalékérték – rendellenességek kiemelése) lehetőséget. A piros buborékok rendellenesek. A rendellenességek olyan szám- vagy százalékértékkel rendelkező buborékok, amelyek 2,1-szeresen meghaladják a számok/százalékok elmúlt két időtartományra jellemző standard szórását (48 óra, ha az utolsó 24 órát nézi stb.).

![A színes pontok rendellenességeket jeleznek.](./media/app-insights-visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> A rendellenességek kiemelése különösen hasznos kis buborékok idősorozataiban található kiugró értékek kimutatásához, amelyek máskülönben ugyanakkora méretűnek tűnnének.  
> 
> 

## <a name="next"></a>Következő lépések
|  |  |
| --- | --- |
| **[Az Application Insights használata a Visual Studióban](app-insights-visual-studio.md)**<br/>Telemetriát kereshet, adatokat tekinthet meg a CodeLensben és konfigurálhatja az Application Insights alkalmazást. Mindezt a Visual Studión belül. |![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Keresés lehetőséget.](./media/app-insights-visual-studio-trends/34.png) |
| **[További adatok hozzáadása](app-insights-asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. |![Visual Studio](./media/app-insights-visual-studio-trends/64.png) |
| **[Az Application Insights-portál használata](app-insights-dashboards.md)**<br/>Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. |![Visual Studio](./media/app-insights-visual-studio-trends/62.png) |

