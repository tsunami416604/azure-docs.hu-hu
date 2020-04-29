---
title: Trendek elemzése a Visual Studióban | Microsoft Docs
description: Elemezze, ábrázolja és ismerje meg részletesebben a trendeket a Visual Studio Application Insights Telemetria funkciója segítségével.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: bb94d3596fde541f16edd1b7012f57b89ebf52eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670865"
---
# <a name="analyzing-trends-in-visual-studio"></a>Trendek elemzése a Visual Studióban
Az Application Insights Trends eszköze megjeleníti webalkalmazásai fontos telemetria-eseményeinek időbeli változásait, ezzel segítve a problémák és hibák gyors azonosítását. A Trends részletesebb diagnosztikai adatokhoz nyújt hozzáférést, amelyek révén javíthat alkalmazásai teljesítményén, megkeresheti a kivételek forrását, és mélyebb betekintést nyerhet az egyéni eseményekbe is.

![Példa a Trends ablakára](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>A webapp konfigurálása az Application Insightshoz

Ha még nem tette meg, [konfigurálja a webappot az Application Insightshoz](../../azure-monitor/app/app-insights-overview.md). Ez lehetővé teszi a webapp számára, hogy telemetriát küldjön az Application Insights portálra. A Trends eszköz innen olvassa be a telemetriát.

Az Application Insights Trends a Visual Studio 2015 Update 3 és újabb verziókban érhető el.

## <a name="open-application-insights-trends"></a>Az Application Insights Trends megnyitása
Az Application Insights Trends ablak megnyitása:

* Az Application Insights eszköztárgombjáról válassza az **Explore Telemetry Trends** (Telemetriatrendek megtekintése) lehetőséget, vagy
* A projekt helyi menüjéből válassza az **Application Insights > Explore Telemetry Trends** (Telemetriatrendek megtekintése) lehetőséget, vagy
* A Visual Studio menüsávjából válassza a **View (Nézet) > Other Windows (Egyéb ablakok) > Application Insights Trends** lehetőséget.

A rendszer kérheti egy erőforrás kiválasztását. Kattintson a **Select a resource** (Erőforrás kiválasztása) elemre, jelentkezzen be Azure-előfizetéssel, majd válasszon egy olyan Application Insights-erőforrást a listából, amelynek a telemetriatrendjeit elemezni szeretné.

## <a name="choose-a-trend-analysis"></a>Trendelemzés kiválasztása
![Trendelemzések általános típusainak menüje](./media/visual-studio-trends/app-insights-trends-1-750.png)

Első lépésként válasszon az öt gyakran használt trendelemzési típus közül, amelyek az elmúlt 24 óra adatait fogják elemezni:

* **Teljesítményhibák vizsgálata kiszolgálókérelmeknél** – A szolgáltatásához beérkezett kérelmek, válaszidő szerint csoportosítva
* **Hibák elemzése kiszolgálókérelmekben** – A szolgáltatásához beérkezett kérelmek, HTTP-válaszidő szerint csoportosítva
* **Az alkalmazásban található kivételek vizsgálata** – A szolgáltatásban található kivételek, a kivétel típusa szerint csoportosítva
* **Az alkalmazás-függőségek teljesítményének ellenőrzése** – A szolgáltatása által hívott szolgáltatások, válaszidő szerint csoportosítva
* **Egyéni események vizsgálata** – A szolgáltatáshoz beállított egyéni események, az esemény típusa szerint csoportosítva.

Ezek az előre kialakított elemzések később, a Trends ablakának bal felső sarkában található **View common types of telemetry analysis** (A telemetriai elemzés gyakran használt típusainak megtekintése) gombra kattintva is elérhetőek.

## <a name="visualize-trends-in-your-application"></a>Trendek megjelenítése az alkalmazásban
Az Application Insights Trends szolgáltatása idősoron jeleníti meg az alkalmazás telemetriai adatait. Minden idősorozatos megjelenítés egy, a telemetria egyik tulajdonsága alapján csoportosított, adott időtartományba eső telemetriatípust jelenít meg. Előfordulhat például, hogy az elmúlt 24 órában érkező kiszolgálókérelmeket szeretné megtekinteni, mindezt az azokat küldő fél országa szerint csoportosítva. Ebben a példában a képi megjelenítés buborékai az egyes országokhoz/régiókhoz tartozó kiszolgálókérelmek egy órára vonatkoztatott számát jelképezik.

Az ablak felső részén található vezérlőeszközök segítségével beállíthatja, hogy a telemetriai adatok mely típusait szeretné megtekinteni. Először válassza ki azokat a telemetriai adatok azon típusait, amelyeknek az adataira kíváncsi:

* **Telemetria típus** – kiszolgálói kérelmek, kivételek, függőségek vagy egyéni események
* **Időtartomány** – Bármely időpont az elmúlt 30 perctől az elmúlt 3 napig
* **Csoportosítási szempont** – Kivétel típusa, probléma azonosítója, ország/régió, és továbbiak.

Ezután kattintson a **telemetria elemzése** elemre a lekérdezés futtatásához.

A vizualizáción látható körök közötti navigáció:

* Kattintson egy buborék kiválasztásához. Ezzel frissíti az ablak alján található szűrőket, csak azokat az eseményeket összegezve, amelyekre egy adott időszakban került sor.
* Kattintson duplán a buborékra, és keresse meg a keresési eszközt, és tekintse meg az adott időszak során bekövetkezett összes egyéni telemetria eseményt
* Nyomja le a CTRL billentyűt, és kattintson egy buborékra, ha meg szeretné szüntetni a kijelölését a képi megjelenítésben.

> [!TIP]
> A Trends és a Search eszköz együttműködése segít abban, hogy több ezer telemetriaesemény között is megtalálhassa a szolgáltatás hibáinak okát. Ha például az ügyfelei egyszer csak észreveszik, hogy az alkalmazás lassabban reagál, kezdjen a Trends használatával. Elemezze a szolgáltatáshoz az elmúlt néhány órában beérkezett kérelmeket, válaszidő szerint csoportosítva. Figyelje meg, hogy látja-e a hosszabb időt igénylő kérelmek nagyobb csoportját. Kattintson duplán az érintett buborékra a Search eszköz eléréshez, hogy kiszűrje az érintett kéréseseményeket. A Search-ben feltárhatja ezeknek a kérelmeknek a tartalmát, és a probléma megoldására vonatkozó kódhoz navigálhat.
> 
> 

## <a name="filter"></a>Szűrés
Az ablak alján található szűrővezérlőkkel specifikusabb trendeket is feltárhat. Kattintson az alkalmazni kívánt szűrő nevére. Gyorsan válthat különböző szűrők között, így olyan trendeket is feltárhat, amelyek a telemetria egy adott dimenziójában rejtőznek. Ha egy szűrőt alkalmaz egy dimenzióban (például a kivétel típusa), a más dimenziókban lévő szűrők továbbra is rákattintanak, de szürkén jelennek meg. Szűrő eltávolításához kattintson rá újra. Ctrl+kattintással egy dimenzión belül több szűrőt is kijelölhet.

![Trendszűrők](./media/visual-studio-trends/TrendsFiltering-750.png)

Mi a teendő, ha több szűrőt szeretne alkalmazni? 

1. Alkalmazza az első szűrőt. 
2. Kattintson az első szűrő dimenziójának neve melletti **Apply selected filters and query again** (Kiválasztott szűrők alkalmazása, majd ismételt lekérdezés) gombra. Ez újra lekérdezi a telemetriát azokhoz az eseményekhez, amelyek megfelelnek az első szűrőnek. 
3. Alkalmazza a második szűrőt. 
4. Ismételje addig a folyamatot, amíg nem sikerül azonosítani a kívánt trendeket a telemetriai adatok különböző részhalmazaiban. Megkeresheti például a „GET Home/Index” nevű *és* Németországból érkezett *és* 500-as válaszkóddal rendelkező kiszolgálókérelmeket. 

A szűrés törléséhez kattintson az adott dimenzióhoz tartozó **Remove selected filters and query again** (Kiválasztott szűrők törlése, majd ismételt lekérdezés) gombra.

![Több szűrő](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Rendellenességek keresése
A Trends eszköz arra is képes, hogy megmutassa, mely eseménykörök számítanak rendellenesnek az adott idősorban szereplő többi körhöz képest. A View Type (Nézettípus) legördülő menüben válassza a **Counts in time bucket (highlight anomalies)** (Időtartamra vonatkozó számérték – rendellenességek kiemelése) vagy a **Percentages in time bucket (highlight anomalies)** (Időtartamra vonatkozó százalékérték – rendellenességek kiemelése) lehetőséget. A rendszer pirossal jelöli a rendellenesként értékelt köröket. Az anomáliák olyan buborékokként vannak definiálva, mint az elmúlt két időszakban (48 óra, ha az elmúlt 24 órában stb.) meghaladják a számok/százalékok 2,1-szort.

![A színes pontok rendellenességeket jelölnek.](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> A rendellenességek kiemelése különösen hasznos kis buborékok idősorozataiban található kiugró értékek kimutatásához, amelyek máskülönben ugyanakkora méretűnek tűnnének.  
> 
> 

## <a name="next-steps"></a><a name="next"></a>További lépések
|  |  |
| --- | --- |
| **[Az Application Insights használata a Visual Studióban](../../azure-monitor/app/visual-studio.md)**<br/>Telemetriát kereshet, adatokat tekinthet meg a CodeLensben és konfigurálhatja az Application Insights alkalmazást. Mindezt a Visual Studión belül. |![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Keresés lehetőséget.](./media/visual-studio-trends/34.png) |
| **[További adatok hozzáadása](../../azure-monitor/app/asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. |![Visual Studio](./media/visual-studio-trends/64.png) |
| **[Az Application Insights portál használata](../../azure-monitor/app/overview-dashboard.md)**<br/>Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. |![Visual Studio](./media/visual-studio-trends/62.png) |

