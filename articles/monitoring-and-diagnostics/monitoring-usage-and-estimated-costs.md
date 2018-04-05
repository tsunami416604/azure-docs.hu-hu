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
ms.date: 04/02/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f87705ebbdd14b1bbf7cade481a7dbe7dd3d5131
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Használati és becsült költségei figyelése

Az Azure portál, a figyelő központban a **használati és a becsült költség** lap történő nyújt segítséget a core monitoring – például a szolgáltatások használatának megértéséhez [riasztási, metrikákat, értesítések](https://azure.microsoft.com/pricing/details/monitor/), [Analytics jelentkezzen](https://azure.microsoft.com/pricing/details/log-analytics/) és [az Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). A felhasználó előtt április 2018 elérhető árképzési csomagok esetén is magában foglalja a Insights keresztül vásárolta a Naplóelemzési használati és Analytics kínálnak.

Ezen a lapon felhasználók megtekinthetik az alábbi források használatát az elmúlt 31 napra vonatkozó előfizetésenként modulokkal részletezési hogy használati trendje ezen időszakon át összesíti. Nagy mennyiségű adat lekéréses együtt, és ez a becslés szükség van, az oldal betöltése várjon türelemmel.
Íme egy példa, figyelési használati és becsült költségeit:

![Használati és a becsült költség portál képernyőképe](./media/monitoring-usage-and-estimated-costs/001.png)

A havi használati oszlopban lévő hivatkozásra kattintva megnyílik a használati trendeket az elmúlt 31 napos időszak trendjét ábrázoló diagramot:

![Egy csomópont képernyőkép része 671.47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

Íme egy másik hasonló használati ráadásul a költségek összefoglaló, ebben az esetben az új április 2018 fogyasztás alapján árképzési modelljével előfizetésre. Figyelje meg minden csomópont-alapú számlázási és, hogy adatfeldolgozást és a megőrzési hiánya a Naplóelemzési, és az Application Insights mostantól egy új közös mérő jelentett.

![Használati és a becsült költség portál képernyőképe](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Új árképzési modellt

2018. április, az új figyelési árképzési modellt jelent meg.  Ez funkciókat árképzési felhő mobilbarát, fogyasztás alapján. Csak kell fizetnie a valóban használt funkciókért, csomópont-alapú kötelezettségvállalás nélkül. Információk az új árképzési modell [riasztási, metrikákat, értesítések](https://azure.microsoft.com/pricing/details/monitor/), [Naplóelemzési](https://azure.microsoft.com/pricing/details/log-analytics/) és [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Ügyfelek bevezetése Naplóelemzési vagy az Application Insights után 2018. április 2. az új árképzési modellt lehetőség. Azok számára, akik már használja ezeket a szolgáltatásokat az új árképzési modellt való áthelyezése nem kötelező megadni.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Az új árképzési modellt hatásának értékelése

Az új árképzési modellt különböző hatások lesz a figyelési használati minták alapján minden ügyfél esetében. Az ügyfelek már használja a Naplóelemzési vagy az Application Insights. április 2 2018, mielőtt a **használati és a becsült költség** lap figyelőben Azure lehetőséget nyújt az becsléséhez bármi is módosul a költségek, ha az új árképzési modellt helyezi át, és biztosítja a előfizetés áthelyezi az új modell módja. A legtöbb felhasználó esetén az új árképzési modellt előnyös, de az ügyfelek főként magas használati mintákat vagy magasabb költségű régiókban, nem lehet, ez a helyzet.

A kijelölt az előfizetések költségeit becsült megjelenítéséhez a **használati és a becsült költség** lapján kattintson a lap tetején kék szalagcím. Legyen a legjobb, ha ez egy előfizetés egyszerre, mert ez a szint, ahol az új árképzési modellt kell megoldania.

![Árképzési modellt válassza képernyőképe](./media/monitoring-usage-and-estimated-costs/004.png)

Most jelenik meg ezen a lapon, a zöld transzparens hasonló verziója:

![Árképzési modellt válassza képernyőképe](./media/monitoring-usage-and-estimated-costs/005.png)

Itt jelenik meg egy másik a mérőszámok – a mérőszámok, amelyek megfelelnek az új árképzési modellt. Például adatok adatfeldolgozást mérőszámok, mint

1. Betekintést és Analytics\Overage csomópontonként
2. Betekintést és Analytics\Included csomópontonként
3. Alkalmazásadatok Insights\Basic keretét
4. Alkalmazásadatok Insights\Included

mostantól egy új közös adatok adatfeldolgozást mérő nevű **megosztott Services\Data adatfeldolgozást** az új árképzési modellt nem tartozik adatát csomópont-alapú hozzárendelések.

Megjelenik egy másik módosítás a Naplóelemzési okozhatnak adatok, vagy magasabb költségek régiók elemzések alkalmazás nem jelenik meg az új területi való megfelelően tükrözik, például **"(USA nyugati régiója központi) adatfeldolgozást**.

> [!NOTE]
> Ha rendelkezik az Operations Management Suite (OMS) előfizetéssel, Naplóelemzés és az Application Insights adatok adatfeldolgozást lefoglalt területet az egyes csomópontok vásárolt jelenik meg. Ez a fiók szintjén (nem pedig az előfizetés) van érvényben, mivel ez a becslés nem mutatja be a megoldást. További részleteket a képviselőjét új árképzési modell részletesebb leírását ebben az esetben.

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Amikor az új árképzési modellt helyezi át a módosításokat

Előfizetés helyezi át az új árképzési modellt fogja módosítani a tarifacsomag az egyes Naplóelemzési új / GB-os réteget, és hogy elmozdul-e bármely (hívott "pergb2018" az Azure Resource Manager). Az áthelyezés is módosul Application Insights-erőforrások vállalati tervben az alapszintű csomag. Ezek a hatásait látható a fenti költség becslése. 

## <a name="moving-to-the-new-pricing-model"></a>Az új árképzési modellt áthelyezése

Ha úgy döntött, az előfizetéshez tartozó új árképzési modellt elfogadására, kattintson a **díjszabási modell kiválasztása** beállítás tetején a **használati és a becsült költség** lap:

![Az új árképzési modellt képernyőfelvétel egy becsült költségét megfigyeléséhez](./media/monitoring-usage-and-estimated-costs/006.png)

Ekkor megnyílik a **díjszabási modell kiválasztása** lapon minden az előző oldalon megtekintett előfizetések listázása:

![Árképzési modellt kijelölés képernyőképe](./media/monitoring-usage-and-estimated-costs/007.png)

Előfizetés áthelyezése az új árképzési modellt, akkor csak jelölje be a jelölőnégyzetet és kattintson a **mentése**.  Áthelyezheti vissza a régebbi árképzési modellt azonos módon. Ne feledje, hogy az előfizetés tulajdonosa vagy közreműködője engedélyek is szükségesek, a díjszabási modell módosításához.