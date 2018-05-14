---
title: Azure Application Insights-adatokat tartalmazó egyéni jelentéseket automatizálásához
description: Egyéni napi vagy heti vagy havi jelentéseket Azure Application Insights adatokkal automatizálásához
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights-adatokat tartalmazó egyéni jelentéseket automatizálásához

Rendszeres jelentés védheti, hogy egy csoport, hogyan történt a kritikus fontosságú üzleti szolgáltatásaik tájékoztatást. A fejlesztők, DevOps/SRE csoportok és a vezetőik produktív munkavégzést megbízhatóan továbbítása anélkül, hogy mindenki bejelentkezés a portálra insights automatizált jelentéseket. Ezek a jelentések segítségével is azonosítható, fokozatos növekszik késések fordulnak elő, a betöltés vagy sikertelen díjszabás, amely nem indíthatnak bármely riasztási szabályok.

Minden vállalati a jelentéskészítési olyan egyedi igényekkel rendelkezik, mint például: 

* Adott PERCENTILIS összesítések metrikákat, vagy a jelentés egyéni metrikák.
* Napi, heti és havi-összesítésben adatok különböző jelentések különböző rendelkezik.
* Szegmentálás egyéni attribútumok például régió, illetve a környezetben. 
* Egyes AI erőforrásokat egy csoportba az egyetlen jelentést, még akkor is, ha azok lehet különböző előfizetésekhez vagy erőforrás csoportok stb.
* Különálló jelentéseket tartalmazó szelektív célközönség küldött bizalmas metrikákat.
* Jelentések érdekelt felek, akik nem rendelkeznek a portál erőforrásokhoz való hozzáférést.

> [!NOTE] 
> A heti Application Insights kivonatoló e-mailek nem engedélyezte a testreszabás, és megszűnik az alább felsorolt egyéni beállítások helyett. Az utolsó heti kivonatoló e-mailt küldünk a 2018. június 11. Állítsa be a következők egyikére szerezhetők be hasonló egyéni jelentések (használja a lekérdezés alább javasolt).

## <a name="to-automate-custom-report-emails"></a>Egyéni jelentések e-mailek automatizálásához

Is [programozott módon lekérdezni az Application Insights](https://dev.applicationinsights.io/) adatok ütemezés szerint egyéni jelentések készítéséhez. A következő beállítások segítségével gyorsan:

* [Jelentéskészítés a Microsoft Flow automatizálásához](app-insights-automate-with-flow.md)
* [Automatizálása a Logic Apps jelentések](automate-with-logic-apps.md)
* Használja az "Application Insights ütemezett kivonatoló" [Azure függvény](https://azure.microsoft.com/services/functions/) sablon figyelés forgatókönyvben. Ez a funkció SendGrid kézbesíti az e-mailt. 

    ![Az Azure-függvény sablon](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Heti kivonatoló e-mailek mintalekérdezés
A következő lekérdezés jeleníti meg, a heti kivonatoló e-mailek például a jelentés több adatkészletet keresztül csatlakozni. Testre szabhatja, szükség szerint, és a heti jelentés automatizálhatja a fent felsorolt beállításokkal együtt használja azt.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>További lépések

- További tudnivalók: létrehozása [elemzési lekérdezések](app-insights-analytics-using.md).
- További információ [programozott módon az Application Insights adatainak lekérdezése](https://dev.applicationinsights.io/)
- További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról
- További információ [Microsoft Flow](https://ms.flow.microsoft.com).


