---
title: Használati és Azure figyelő becsült költségét figyelése |} Microsoft Docs
description: A áttekintése használatával Azure figyelő használati és a becsült költség lap
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: 08991565d56ffbf7d798944f108a1b86e4463c58
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Használati és becsült költségei figyelése

Az Azure portál, a figyelő központban a **használati és a becsült költség** lap ismerteti az alapvető figyelési szolgáltatásokat, mint használatát [riasztási, metrikákat, értesítések](https://azure.microsoft.com/pricing/details/monitor/), [Azure Naplóelemzés ](https://azure.microsoft.com/pricing/details/log-analytics/), és [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). A felhasználó előtt április 2018 árképzési csomagok esetén is magában foglalja a Insights keretében vásárolt Naplóelemzési használati és elemzés kínálnak.

Ezen a lapon felhasználók megtekinthetik az erőforrás-használat az elmúlt 31 napra vonatkozó előfizetésenként összesíteni. Részletezés modulok megjelenítése a használati trendeket a 31 napos időszakon át. Nagy mennyiségű adatot kell érkezett együtt ez a becslés, ezért a lap betöltése várjon türelemmel.

Ez a példa bemutatja a figyelési használati és becsült költségeit:

![Használati és a becsült költség portál képernyőképe](./media/monitoring-usage-and-estimated-costs/001.png)

A havi használati oszlop diagram, amely a használati trendeket szemlélteti a legutóbbi 31 napos időszak során megnyitásához kattintson a hivatkozásra:

![Keret csomópontonként eszközterület-diagram képernyőképe](./media/monitoring-usage-and-estimated-costs/002.png)

Íme egy másik hasonló használati és költség összegzése. Ez a példa bemutatja egy előfizetést az új április 2018 fogyasztás alapján díjszabási modell. Megjegyzés: a csomópont alapján történő számlázáshoz hiánya. Egy új közös mérő adatfeldolgozást és Naplóelemzés és az Application Insights adatmegőrzési most jelentett.

![Használati és a becsült költség portál képernyőfelvétel - április 2018 díjszabása](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Új árképzési modellt

A április 2018 egy [jelent meg új figyelési árképzési modellt](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Ez funkciókat árképzési felhő mobilbarát, fogyasztás alapján. Csak kell fizetnie a valóban használt funkciókért, csomópont-alapú kötelezettségvállalás nélkül. Információk az új árképzési modell [riasztási, metrikákat, értesítések](https://azure.microsoft.com/pricing/details/monitor/), [Naplóelemzési](https://azure.microsoft.com/pricing/details/log-analytics/) és [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Ügyfelek bevezetése Naplóelemzési vagy az Application Insights után 2018. április 2. az új árképzési modellt lehetőség. Az olyan ügyfelek, akik már használja ezeket a szolgáltatásokat az új árképzési modellt való áthelyezése nem kötelező.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Az új árképzési modellt hatásának értékelése
Az új árképzési modellt különböző hatások lesz a minden ügyfél, a figyelési használati minták alapján. Felhasználók, akik 2018. április 2., mielőtt a Naplóelemzési vagy az Application Insights használta éppen a **használati és a becsült költség** lap figyelőben Azure becslése bármi is módosul a költségek, ha azok helyezze át az új árképzési modellt. Ez biztosítja a előfizetés áthelyezi az új modell. A legtöbb felhasználó esetén az új árképzési modellt előnyös lesz. Az ügyfelek főként magas használati mintákat vagy magasabb költségű régiókban ez nem lehet az eset.

Az előfizetésekhez a költségek, a választott becsült megjelenítéséhez a **használati és a becsült költség** lapon, válassza ki a lap tetején kék szalagcím. Célszerű ehhez az egy előfizetés egyszerre, mert ez a szint, ahol az új árképzési modellt kell megoldania.

![A figyelő használati és új árképzési modellt a képernyőfelvételen látható becsült költségei](./media/monitoring-usage-and-estimated-costs/004.png)

Az új lap zöld fejléccel előzetes lap hasonló verzióját jeleníti meg:

![A figyelő használati és aktuális árképzési modellt a képernyőfelvételen látható becsült költségei](./media/monitoring-usage-and-estimated-costs/005.png)

A lapon is látható, hogy az új árképzési modellt mérőszámok különböző készlete. Ez a lista példája:

- Betekintést és Analytics\Overage csomópontonként
- Betekintést és Analytics\Included csomópontonként
- Alkalmazásadatok Insights\Basic keretét
- Alkalmazásadatok Insights\Included

Az új árképzési modellt adatát csomópont-alapú hozzárendelések nem rendelkezik. Ezért ezek adatok adatfeldolgozást mérőszámok van egyesítve, egy új közös adatok adatfeldolgozást mérő nevű **megosztott Services\Data adatfeldolgozást**. 

Nincs másik változtatást Naplóelemzési vagy magasabb költségű régiókban Application Insights okozhatnak. A nagy költségű területek adatait az új területi való jelenik meg. Példa: **(US nyugati központi) adatfeldolgozást**.

> [!NOTE]
> Az előfizetésenként becsült költségeit nem tényező be a fiók szintű / csomópont jogosultságok az Operations Management Suite (OMS) előfizetés. Ebben az esetben az új árképzési modellt részletesebb információt a részleteket a képviselőjét.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Új árképzési modellt és az Operations Management Suite előfizetés jogosultságok

Az ügyfelek, akik vásárolt a Microsoft Operations Management Suite E1 és E2 jogosultak a csomópont adatok adatfeldolgozást jogosultságok beállítása [Naplóelemzési](https://www.microsoft.com/cloud-platform/operations-management-suite) és [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). A Naplóelemzési munkaterület vagy az Application Insights-erőforrások ezeket a jogosultságokat kap, a megadott előfizetés: 

- Az előfizetés árképzési modellt a előtti. április 2018 modellben kell maradnia.
- Napló Analytics munkaterületek használja a "csomópontonként (OMS)" IP-címek.
- Application Insights-erőforrások használja a "Vállalati" árképzési terv.

A szervezet által megvásárolt, csomagban csomópontok számától függően néhány áthelyezni az új árképzési modellt előfizetések előnyös lehet, de ehhez alapos megfontolás szükséges. Általában tanácsos egyszerűen a fent leírt módon előtti-április 2018 modell maradnak.

> [!WARNING]
> Ha a szervezet megvásárolja a Microsoft Operations Management Suite E1 és E2, általában célszerű megtartani az előfizetések a előtti-április 2018 árképzési modellt. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Ha az új árképzési modellt áthelyezendő változások

Az új árképzési modellt egyszerűbbé teszi a Naplóelemzési és az Application Insights árképzési beállítások csak egyetlen réteg (vagy terv). Előfizetés helyezi át az új árképzési modellt lesz:

- Ez a tarifacsomag módosítása minden Naplóelemzési új / GB-os réteghez (úgynevezett "pergb2018" az Azure Resource Manager)
- A vállalati terv bármely Application Insights-erőforrások az alapszintű csomag értékre módosul.

Költség becslése eredő ezeket a módosításokat jeleníti meg.

> [!WARNING]
> Fontos megjegyzés: Ha használhatja az Azure Resource Manager vagy a PowerShell telepítése Itt [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) vagy [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) egy előfizetésben, hogy áttelepítette az új árképzési modellt. Ha adja meg a "pergb2018" eltérő árképzési szint/terv Naplóelemzési vagy a "Basic" Application Insights, nem pedig a megadása érvénytelen árképzési szint/tervet, mert a telepítés sikeres lesz **, de csak az érvényes fogja használni árképzési szint/terv**. 
>

## <a name="moving-to-the-new-pricing-model"></a>Az új árképzési modellt áthelyezése

Ha úgy döntött, az előfizetéshez tartozó új árképzési modellt elfogadására, válassza ki a **díjszabási modell kiválasztása** beállítás tetején a **használati és a becsült költség** lap:

![A figyelő használati és új árképzési modellt a képernyőfelvételen látható becsült költségei](./media/monitoring-usage-and-estimated-costs/006.png)

A **díjszabási modell kiválasztása** oldal jelenik meg. Ez minden olyan előfizetést, amely az előző oldalon megtekintett listáját jeleníti meg:

![Árképzési modellt kijelölés képernyőképe](./media/monitoring-usage-and-estimated-costs/007.png)

Előfizetés áthelyezése az új árképzési modellt, egyszerűen jelölje be a jelölőnégyzetet, és válassza ki **mentése**. Áthelyezheti vissza a régebbi árképzési modellt azonos módon. Ne feledje, hogy az előfizetés tulajdonosa, vagy közreműködői engedélyekkel kell módosítani a árképzési modellt.
