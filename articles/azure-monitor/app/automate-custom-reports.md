---
title: Egyéni jelentések automatizálása az Azure Application Insights-adatokkal
description: Egyéni napi/heti/havi jelentések automatizálása az Azure Application Insights-adatokkal
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655123"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Egyéni jelentések automatizálása az Azure Application Insights-adatokkal

Az időszakos jelentések segítenek folyamatosan tájékoztatni a csapatot arról, hogy az üzleti legkritikusabb szolgáltatások hogyan teljesítenek. A fejlesztők, a DevOps/SRE csapatok és azok kezelői az automatikus jelentésekkel megbízhatóan biztosíthatnak elemzéseket anélkül, hogy mindenkinek be kellene jelentkeznie a portálon. Ezek a jelentések is segíthetnek azonosítani a késések, terhelésvagy hibaarányok fokozatos növekedését, amelyek nem váltanak ki riasztási szabályokat.

Minden gazdálkodónak megvannak a maga egyedi jelentési igényei, mint például: 

* A mutatók vagy a jelentés egyéni mutatóinak adott percentilis összesítései.
* Különböző jelentéseket kaphat a különböző közönségek napi, heti és havi adatösszesítéséhez.
* Szegmentálás egyéni attribútumok, például régió vagy környezet szerint. 
* Csoportosítson néhány AI-erőforrást egyetlen jelentésben, még akkor is, ha különböző előfizetésekben vagy erőforráscsoportokban stb.
* A szelektív közönségnek küldött bizalmas mutatókat tartalmazó külön jelentések.
* Jelentések azoknak az érdekelt feleknek, akik esetleg nem férnek hozzá a portál erőforrásaihoz.

> [!NOTE] 
> A heti Application Insights kivonat e-mail nem tette lehetővé a testreszabást, és megszűnik az alább felsorolt egyéni beállítások javára. Az utolsó heti kivonatoló e-mailt 2018. Konfigurálja az alábbi lehetőségek egyikét a hasonló egyéni jelentések levételéhez (használja az alábbi lekérdezést).

## <a name="to-automate-custom-report-emails"></a>Egyéni jelentései automatizálása

Az [Application Insights-adatok programozott lekérdezésével](https://dev.applicationinsights.io/) egyéni jelentéseket hozhat létre ütemezés szerint. Az alábbi lehetőségek segíthetnek a gyors kezdésben:

* [Jelentések automatizálása a Microsoft Flow segítségével](automate-with-flow.md)
* [Jelentések automatizálása a Logic Apps alkalmazásokkal](automate-with-logic-apps.md)
* Használja az "Application Insights ütemezett kivonatolás" [Azure-függvénysablont](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) a figyelési forgatókönyvben. Ez a funkció a SendGrid segítségével kézbesíti az e-mailt. 

    ![Azure függvénysablon](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Lekérdezés mintalekérdezése heti kivonatoló e-mailhez
A következő lekérdezés azt mutatja, hogy egy heti kivonatoló e-mail hez hasonló jelentés hez több adatkészlet között csatlakozik. Szükség szerint testreszabhatja, és a fent felsorolt beállítások bármelyikével használhatja a heti jelentések automatizálásához.   

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

## <a name="application-insights-scheduled-digest-report"></a>Az Application Insights ütemezett kivonatolási jelentése

1. Az Azure Portalon válassza az > **Erőforrás-számítási** > **függvény alkalmazás** **létrehozása**lehetőséget.

   ![Azure Resource Function App képernyőképének létrehozása](./media/automate-custom-reports/function-app-01.png)

2. Adja meg az alkalmazáshoz megfelelő adatokat, és válassza a _Létrehozás gombot._ (Az Application Insights _On_ csak akkor szükséges, ha az új függvényalkalmazást az Application Insights segítségével szeretné figyelni)

   ![Azure Resource Function Alkalmazásbeállítások képernyőképének létrehozása](./media/automate-custom-reports/function-app-02.png)

3. Miután az új Függvényalkalmazás befejezte a központi telepítést, válassza **az Ugrás az erőforrásra**lehetőséget.

4. Válassza az **Új függvény lehetőséget.**

   ![Új függvény képernyőképlétrehozása](./media/automate-custom-reports/function-app-03.png)

5. Válassza ki az **_Application Insights ütemezett kivonatolási sablonját._**

     > [!NOTE]
     > Alapértelmezés szerint a függvényalkalmazások a 2.x-es futásidejű verzióval jönnek létre. Az Application Insights ütemezett kivonatolási sablonjának használatához meg kell [céloznia az Azure Functions 1.x-es verzióját.](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1.x**  ![futásidejű képernyőkép](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Új függvény alkalmazáselemzési sablonképernyő-képe](./media/automate-custom-reports/function-app-04.png)

6. Adja meg a jelentéshez a megfelelő címzett e-mail címét, és válassza a **Létrehozás gombot.**

   ![Funkcióbeállítások képernyőkép](./media/automate-custom-reports/function-app-05.png)

7. Válassza ki a **Function App** > **Platform funkcióit** > **alkalmazásbeállítások**.

    ![Az Azure Function Alkalmazás beállításaiképernyőkép](./media/automate-custom-reports/function-app-07.png)

8. Hozzon létre három új ``AI_APP_ID``alkalmazásbeállítást a megfelelő megfelelő értékekkel és ``AI_APP_KEY`` ``SendGridAPI``a . Kattintson a **Mentés** gombra.

     ![Funkció integrációs felület képernyőkép](./media/automate-custom-reports/function-app-08.png)
    
    (A AI_ értékek az API Access az Application Insights-erőforrás, amelyről jelentést szeretne jelenteni. Ha nem rendelkezik Application Insights API-kulccsal, lehetőség van az **API-kulcs létrehozására.)**
    
   * AI_APP_ID = Alkalmazásazonosító
   * AI_APP_KEY = API-kulcs
   * SendGridAPI =SendGrid API-kulcs

     > [!NOTE]
     > Ha nem rendelkezik SendGrid-fiókkal, létrehozhat egyet. A SendGrid dokumentációja az Azure Functionshez [itt](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid)található. Ha csak egy minimális magyarázatot szeretne a SendGrid beállítására és egy API-kulcs létrehozására a cikk végén. 

9. Válassza **az Integrálás** lehetőséget, és a Kimenetek csoportban kattintson a **SendGrid ($return)** elemre.

     ![Kimeneti képernyőkép](./media/automate-custom-reports/function-app-09.png)

10. A **SendGridAPI billentyűalkalmazás-beállítás alatt**válassza ki a **SendGridAPI**újonnan létrehozott alkalmazásbeállítását.

     ![A Függvényalkalmazás képernyőképnek a futtatása](./media/automate-custom-reports/function-app-010.png)

11. Futtassa és tesztelje a Függvényalkalmazást.

     ![Képernyőkép tesztelése](./media/automate-custom-reports/function-app-11.png)

12. Ellenőrizze az e-mailben, hogy az üzenet küldése/fogadása sikeresvolt-e.

     ![Az e-mail tárgysorának képernyőképe](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid az Azure-ral

Ezek a lépések csak akkor érvényesek, ha még nincs konfigurálva SendGrid-fiók.

1. Az Azure portalon válassza **az Erőforrás-keresés létrehozása** **a SendGrid e-mail kézbesítési** > kattintson a **Create** > gombra, és töltse ki a SendGrid-specifikus létrehozási utasításokat. 

     ![SendGrid-erőforrás létrehozása képernyőkép](./media/automate-custom-reports/function-app-13.png)

2. A SendGrid-fiókok csoportban a Létrehozás után válassza **a Kezelés**lehetőséget.

     ![Beállítások API-kulcs képernyőképe](./media/automate-custom-reports/function-app-14.png)

3. Ez elindítja sendgrid honlapján. Válassza a **Beállítások** > **API-kulcsok lehetőséget.**

     ![API-kulcsalkalmazás képernyőképének létrehozása és megtekintése](./media/automate-custom-reports/function-app-15.png)

4. Hozzon létre egy API-kulcsot, > válassza **a Create & View** (Kérjük, tekintse át a SendGrid dokumentációját a korlátozott hozzáférésről, hogy megállapíthassa, milyen szintű engedélyek felelnek meg az API-kulcsnak. A Teljes hozzáférés itt csak célokra van kiválasztva.)

   ![Teljes hozzáférésű képernyőkép](./media/automate-custom-reports/function-app-16.png)

5. Másolja a teljes kulcsot, ez az érték az, amire szüksége van a Függvényalkalmazás beállításaiban, mint a SendGridAPI értéke

   ![API-kulcs másolása képernyőkép](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>További lépések

* További információ az [Analytics-lekérdezések](../../azure-monitor/log-query/get-started-queries.md)létrehozásáról.
* További információ az [Application Insights-adatok programozott lekérdezéséről](https://dev.applicationinsights.io/)
* További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról
* További információ a [Microsoft Flow szolgáltatásról.](https://ms.flow.microsoft.com)
