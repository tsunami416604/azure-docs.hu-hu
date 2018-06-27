---
title: Azure Application Insights-adatokat tartalmazó egyéni jelentéseket automatizálásához
description: Egyéni napi vagy heti vagy havi jelentéseket Azure Application Insights adatokkal automatizálásához
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c8cff54c67ab2c9c3d09f9261617b6312cc4434a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025819"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights-adatokat tartalmazó egyéni jelentéseket automatizálásához

Rendszeres jelentés védheti, hogy egy csoport, hogyan történt a kritikus fontosságú üzleti szolgáltatásaik tájékoztatást. A fejlesztők, DevOps/SRE csoportok és a vezetőik produktív munkavégzést megbízhatóan továbbítása anélkül, hogy bárki bejelentkezhet a portál insights automatizált jelentéseket. Ezek a jelentések segítségével is azonosítható, fokozatos növekszik késések fordulnak elő, a betöltés vagy sikertelen díjszabás, amely nem indíthatnak bármely riasztási szabályok.

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
* Használja az "Application Insights ütemezett kivonatoló" [Azure függvény](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) sablon figyelés forgatókönyvben. Ez a funkció SendGrid kézbesíti az e-mailt. 

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

## <a name="application-insights-scheduled-digest-report"></a>Application Insights ütemezett kivonatoló jelentés

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrást** > **számítási** > **függvény App**.

   ![Hozzon létre egy Azure-erőforrás függvény alkalmazás képernyőképe](./media/automate-custom-reports/function-app-01.png)

2. Adja meg a megfelelő adatokat az alkalmazáshoz, és válassza ki _létrehozása_. (Az application Insights _a_ szükség, csak akkor, ha az új függvény-alkalmazás az Application insights szolgáltatással figyelni kívánt)

   ![Hozzon létre egy Azure-erőforrás függvény Alkalmazásbeállítások képernyőképe](./media/automate-custom-reports/function-app-02.png)

3. Az új függvény alkalmazás központi telepítés befejezése után válassza ki a **forrást**.

4. Válassza ki **új függvény**.

   ![Hozzon létre egy új funkció képernyőképe](./media/automate-custom-reports/function-app-03.png)

5. Válassza ki a  **_Application Insights ütemezett kivonatoló sablon_**.

   ![Új függvény Application Insights sablon képernyőképe](./media/automate-custom-reports/function-app-04.png)

6. Adjon meg egy megfelelő címzett e-mail címet a jelentést, és válassza ki a **létrehozása**.

   ![Függvény beállítások képernyőképe](./media/automate-custom-reports/function-app-05.png)

7. Válassza ki a **függvény App** > **Platform funkciói** > **Alkalmazásbeállítások**.

    ![Az Azure függvény alkalmazás beállítások képernyőképe](./media/automate-custom-reports/function-app-07.png)

8. Három új Alkalmazásbeállítások létrehozása a megfelelő megfelelő értékekkel ``AI_APP_ID``, ``AI_APP_KEY``, és ``SendGridAPI``. Kattintson a **Mentés** gombra.

     ![Függvény integrációs felületének képernyőképe](./media/automate-custom-reports/function-app-08.png)
    
    (A AI_ értékek található szeretne jelentést készíteni az Application Insights-erőforrás API-hozzáférést. Ha nincs Application Insights API-kulcs, nincs lehetőség **API-kulcs létrehozása**.)
    
    * AI_APP_ID azonosítója =
    * AI_APP_KEY API-kulcs =
    * SendGridAPI SendGrid API-kulcs =

    > [!NOTE]
    > Ha nincs a SendGrid-fiókja, létrehozhat egyet. A SendGrid tartozó dokumentáció az Azure Functions [Itt](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-sendgrid). Ha csak szeretné, hogy a minimális magyarázatát, hogy hogyan kell beállítania a SendGrid, és ez a cikk végén elérhető API-kulcs létrehozása. 

9. Válassza ki **integráció** és a kimeneti kattintson **SendGrid ($return)**.

     ![Kimeneti képernyőképe](./media/automate-custom-reports/function-app-09.png)

10. Az a **SendGridAPI kulcs Alkalmazásbeállítás**, válassza ki az újonnan létrehozott alkalmazás-beállítás a **SendGridAPI**.

     ![Futtassa a függvény alkalmazás képernyőképe](./media/automate-custom-reports/function-app-010.png)

11. Futtassa, és a alkalmazás tesztelése a függvény.

     ![Teszt képernyőképe](./media/automate-custom-reports/function-app-11.png)

12. Ellenőrizze az e-mailt arról, hogy az üzenet nem küldhető/fogadható sikeresen.

     ![E-mail tárgya sor képernyőképe](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Az Azure-ral SendGrid

Ezeket a lépéseket csak akkor alkalmazza, ha már a SendGrid konfigurált fiók nem rendelkezik.

1. Az Azure portálon válassza **hozzon létre egy erőforrást** keressen **SendGrid e-mailben kézbesítésre** > kattintson **létrehozása** >, és töltse ki a SendGrid-specifikus kimenő utasításokat létrehozása. 

     ![Hozzon létre a SendGrid erőforrás képernyőképe](./media/automate-custom-reports/function-app-13.png)

2. A SendGrid-fiókok létrehozását követően válassza ki a **kezelése**.

     ![Képernyőkép a beállítások az API-kulcs](./media/automate-custom-reports/function-app-14.png)

3. A SendGrid tartozó hely fog elindulni. Válassza ki **beállítások** > **API-kulcsokat**.

     ![Hozzon létre, és tekintse meg az API-kulcs alkalmazás képernyőképe](./media/automate-custom-reports/function-app-15.png)

4. API-kulcs létrehozása > Válasszon **Létrehozás & nézet** (olvassa el a SendGrid meg a korlátozott hozzáférésű határozhatja meg, milyen szintű engedélyek az API-kulcs megfelelő. Teljes hozzáférés elemet itt példa jelleggel.)

   ![Teljes hozzáférés képernyőképe](./media/automate-custom-reports/function-app-16.png)

5. A teljes kulcsot másolja, az értéke, mire van szüksége az függvény alkalmazás beállításaiban értékeként a SendGridAPI

   ![Másolja az API-kulcs képernyőképe](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>További lépések

* További tudnivalók: létrehozása [elemzési lekérdezések](app-insights-analytics-using.md).
* További információ [programozott módon az Application Insights adatainak lekérdezése](https://dev.applicationinsights.io/)
* További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról
* További információ [Microsoft Flow](https://ms.flow.microsoft.com).
