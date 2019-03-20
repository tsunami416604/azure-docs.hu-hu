---
title: Az Azure Application Insights-adatokat tartalmazó egyéni jelentések automatizálása
description: Az Azure Application Insights-adatok egyéni napi/heti/havi jelentések automatizálása
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 37de2de18656f0f8ba85495b3fd72315e0bd885b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113078"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Az Azure Application Insights-adatokat tartalmazó egyéni jelentések automatizálása

Rendszeres jelentés gondoskodnak a csapat hogyan teljesítenek a kritikus fontosságú üzleti szolgáltatásaik információkat. A fejlesztők, fejlesztési és üzemeltetési/SRE csoportokat és a vezetőik számára hatékony munkavégzést megbízhatóan továbbítása anélkül, hogy mindenki számára a portál bejelentkezés insights automatizált jelentésekkel. Ezek a jelentések is segít azonosítani a fokozatosan növeli a kis késleltetésű, vagy meghibásodik, díjszabás, amely nem indíthat bármelyik riasztási szabályok.

Minden egyes vállalat a jelentéskészítési olyan egyedi igényekkel rendelkezik, mint például: 

* Adott PERCENTILIS összesítések metrikákat, vagy egy jelentést az egyéni metrikákat.
* Napi, heti és havi-összesítésben az adatok több jelentés rendelkezik a különböző célközönségek számára.
* Például a régiót, vagy a környezet egyéni attribútumok szerint Szegmentálás. 
* Bizonyos AI-erőforrások egy csoportba egyetlen jelentésben, még akkor is, ha azok lehet különböző előfizetésekhez vagy erőforrás csoportok stb.
* Szelektív közönség számára küldött bizalmas mérőszámokat tartalmazó különálló jelentéseket.
* Jelentések az érdekelt felekkel, akik nem rendelkeznek a portálon erőforrások eléréséhez.

> [!NOTE] 
> Az Application Insights Heti összefoglaló e-mail nem engedélyezte a testreszabás, és ezt megszüntetjük az alábbi egyéni lehetőségek értéke. 2018. június 11. az elmúlt Heti összefoglaló e-mailben küldi el. Állítsa be a hasonló egyéni jelentések (használja az alábbi javasolt lekérdezés) az alábbi lehetőségek közül.

## <a name="to-automate-custom-report-emails"></a>Egyéni jelentések e-mailek automatizálása

Is [programozott módon lekérdezheti az Application Insights](https://dev.applicationinsights.io/) ütemezés szerint egyéni jelentések készítése az adatokat. A következő beállítások segítségével gyorsan használatba vehető:

* [Jelentéskészítés a Microsoft Flow automatizálása](automate-with-flow.md)
* [A Logic Apps jelentések automatizálása](automate-with-logic-apps.md)
* Használja az "Application Insights ütemezett összefoglaló" [Azure-függvény](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) sablon a figyelés forgatókönyvben. Ez a funkció a SendGrid használatával az e-mailben kézbesítéséhez. 

    ![Azure-függvény sablon](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Heti összefoglaló e-mailek mintalekérdezés
A következő lekérdezés látható csatlakoztatása egy heti kivonat e-mailt a jelentés a több adatkészlet között. Szükség szerint testre szabni, és használhatja a heti jelentés automatizálhatja a fent felsorolt lehetőségek közül.   

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

## <a name="application-insights-scheduled-digest-report"></a>Application Insights ütemezett összefoglaló jelentés

1. Az Azure Portalon, válassza ki a **erőforrás létrehozása** > **számítási** > **Függvényalkalmazás**.

   ![Hozzon létre egy Azure-erőforrás Függvényalkalmazás képernyőképe](./media/automate-custom-reports/function-app-01.png)

2. Adja meg a megfelelő adatokat az alkalmazást, és válassza a _létrehozás_. (Az application Insights _a_ kötelező, csak akkor, ha figyelemmel szeretné követni az új Függvényalkalmazást az Application insights segítségével)

   ![Hozzon létre egy Azure Resource Függvényalkalmazás beállításai képernyőkép](./media/automate-custom-reports/function-app-02.png)

3. Az új Függvényalkalmazás üzembe helyezés befejezése után jelölje ki a **erőforrás megnyitása**.

4. Válassza ki **új függvény**.

   ![Hozzon létre egy új funkció képernyőképe](./media/automate-custom-reports/function-app-03.png)

5. Válassza ki a  **_Application Insights ütemezett összefoglaló sablon_**.

   ![Új Application Insights Függvénysablon képernyőképe](./media/automate-custom-reports/function-app-04.png)

6. Adjon meg egy megfelelő címzett e-mail címet a jelentést, majd válassza a **létrehozás**.

   ![A funkció beállításai képernyőkép](./media/automate-custom-reports/function-app-05.png)

7. Válassza ki a **Függvényalkalmazás** > **platformfunkciók** > **Alkalmazásbeállítások**.

    ![Az Azure Függvényalkalmazást beállításai képernyőkép](./media/automate-custom-reports/function-app-07.png)

8. Három új Alkalmazásbeállítások létrehozása a megfelelő megfelelő értékekre ``AI_APP_ID``, ``AI_APP_KEY``, és ``SendGridAPI``. Kattintson a **Mentés** gombra.

     ![A funkció integrációt felületének képernyőképe](./media/automate-custom-reports/function-app-08.png)
    
    (A AI_ értékek területen találhatók az Application Insights-erőforrás, amelyekről jelentést szeretne készíteni az API-hozzáférés. Egy Application Insights API-kulcs nem rendelkezik, van-e lehetőség **API-kulcs létrehozása**.)
    
   * AI_APP_ID = Application ID
   * AI_APP_KEY API-kulcs =
   * SendGridAPI SendGrid API-kulcs =

     > [!NOTE]
     > Ha a SendGrid-fiók nem rendelkezik, hozzon létre egyet. SendGrid-dokumentáció az Azure Functions [Itt](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Ha csak szeretné, amely minimális elmagyarázza, és a SendGrid beállítása, és ez a cikk végén található elérhető API-kulcs létrehozása. 

9. Válassza ki **integráció** és a kimenetek kattintson **SendGrid ($return)**.

     ![Kimenet képernyőképe](./media/automate-custom-reports/function-app-09.png)

10. Alatt a **SendGridAPI kulcs Alkalmazásbeállítása**, válassza ki az újonnan létrehozott tartozó alkalmazásbeállítást **SendGridAPI**.

     ![Futtassa a Függvényalkalmazás képernyőképe](./media/automate-custom-reports/function-app-010.png)

11. Futtassa, és tesztelje a Függvényalkalmazást.

     ![Teszt képernyőképe](./media/automate-custom-reports/function-app-11.png)

12. Ellenőrizze az e-mailek, győződjön meg arról, hogy az üzenet sikeresen küldött/fogadott.

     ![E-mail tárgya sor képernyőképe](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>A SendGrid az Azure-ral

Ezeket a lépéseket csak akkor érvényesek, ha még nem rendelkezik konfigurált SendGrid-fiókjából.

1. Az Azure portálon válassza az **erőforrás létrehozása** keressen **SendGrid Email Delivery** > kattintson **létrehozás** >, és töltse ki a SendGrid-specifikus utasításokat létrehozása. 

     ![Képernyőkép a SendGrid-erőforrás létrehozása](./media/automate-custom-reports/function-app-13.png)

2. A SendGrid-fiókok létrehozása után válassza ki a **kezelés**.

     ![Képernyőkép a beállítások az API-kulcs](./media/automate-custom-reports/function-app-14.png)

3. Ez elindítja a SendGrid-helyhez. Válassza ki **beállítások** > **API-kulcsok**.

     ![Létrehozhat és megjeleníthet a API-kulcs alkalmazás képernyőképe](./media/automate-custom-reports/function-app-15.png)

4. API-kulcs létrehozása > Válasszon **Létrehozás & nézet** (tekintse át annak megállapításához, hogy milyen szintű engedélyekkel a megfelelő API-kulcs korlátozott hozzáférés a SendGrid-dokumentációt. Teljes hozzáférés ki van jelölve itt kizárólag példa céljából.)

   ![Teljes hozzáférés képernyőképe](./media/automate-custom-reports/function-app-16.png)

5. A teljes kulcsot másolja, az értéke, mire van szüksége a Függvényalkalmazások között értékeként SendGridAPI

   ![Másolja ki az API-kulcs képernyőképe](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>További lépések

* További információ a létrehozásával [elemzési lekérdezések](../../azure-monitor/log-query/get-started-queries.md).
* Tudjon meg többet [programozott módon az Application Insights-adatok lekérdezése](https://dev.applicationinsights.io/)
* További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról
* Tudjon meg többet [Microsoft Flow](https://ms.flow.microsoft.com).
