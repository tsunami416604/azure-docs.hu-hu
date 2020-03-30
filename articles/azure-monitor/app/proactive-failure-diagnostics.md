---
title: Intelligens észlelés – hibaanomáliák az Application Insights ban | Microsoft dokumentumok
description: Figyelmezteti a webalkalmazássikertelen kérelmek sebességének szokatlan változásaira, és diagnosztikai elemzést biztosít. Nincs szükség konfigurációra.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: e1c07fca3a4eee19e56c313a889e5b86ce2b4c42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671749"
---
# <a name="smart-detection---failure-anomalies"></a>Intelligens észlelés – hibaanomáliák
[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) automatikusan figyelmezteti Önt közel valós időben, ha a webalkalmazás a sikertelen kérelmek arányának rendellenes növekedését tapasztalja. Szokatlan ultrást észlel a sikertelenként jelentett HTTP-kérelmek vagy függőségi hívások arányában. A kérelmek esetében a sikertelen kérelmek általában 400-as vagy magasabb válaszkódokkal rendelkeznek. A probléma osztályozása és diagnosztizálása érdekében a hibák és a kapcsolódó alkalmazásadatok jellemzőinek elemzése a riasztás részleteiben található. További diagnózis esetén az Application Insights-portálra mutató hivatkozások is találhatók. A szolgáltatásnem igényel sem beállítás, sem konfiguráció, mivel gépi tanulási algoritmusok segítségével előre jelzi a normál hibaarány.

Ez a funkció minden olyan webalkalmazáshoz működik, amely a felhőben vagy a saját kiszolgálóin üzemelteti az alkalmazáskérelmeket vagy függőségi adatokat. Ha például van egy feldolgozói szerepköre, amely [meghívja a TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) vagy [a TrackDependency() ( trackdependency) ( trackdependency)](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)(

Az Application Insights beállítása a [projekthez](../../azure-monitor/app/app-insights-overview.md), és ha az alkalmazás létrehoz egy bizonyos minimális mennyiségű adatot, intelligens észlelése hiba anomáliák 24 órát vesz igénybe, hogy megtanulják a szokásos viselkedését az alkalmazás, mielőtt be van kapcsolva, és riasztásokat küldhet.

Íme egy mintariasztás:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

A riasztás részletei a következőket fogják mondani:

* A hibaarány az alkalmazás normál viselkedéséhez képest.
* Hány felhasználó érintett - így tudja, hogy mennyit kell aggódnia.
* A hibákhoz kapcsolódó jellemző minta. Ebben a példában van egy adott válaszkód, kérelem neve (művelet) és az alkalmazás verziója. Ez azonnal megmondja, hol kezdje el keresni a kódot. Más lehetőségek lehetnek egy adott böngésző vagy ügyfél operációs rendszer.
* A kivétel, a naplónyomkövetések és a függőségi hiba (adatbázisok vagy más külső összetevők), amelyek úgy tűnik, hogy a jellemzett hibákhoz vannak társítva.
* Közvetlenül az Application Insights ban lévő adatokra vonatkozó keresésekre mutató hivatkozások.

## <a name="benefits-of-smart-detection"></a>Az intelligens észlelés előnyei
A szokásos [metrikariasztások](../../azure-monitor/app/alerts.md) azt adják, hogy probléma lehet. De smart detection elindítja a diagnosztikai munkát az Ön számára, végző sok az elemzés egyébként meg kell csinálni magadnak. Ön kap az eredményeket szépen csomagolt, segít, hogy gyorsan a probléma gyökerét.

## <a name="how-it-works"></a>Működés
Az intelligens észlelés figyeli az alkalmazástól kapott adatokat, és különösen a hibaarányokat. Ez a szabály megszámolja `Successful request` azoknak a kérelmeknek a számát, amelyeknél a tulajdonság hamis, és a függőségi hívások számát, amelyekhez a `Successful call` tulajdonság hamis. A kérelmek, alapértelmezés `Successful request == (resultCode < 400)` szerint (kivéve, ha írt egyéni kódot, hogy [szűrje,](../../azure-monitor/app/api-filtering-sampling.md#filtering) vagy hozzon létre saját [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) hívások). 

Az alkalmazás teljesítménye tipikus viselkedési mintával rendelkezik. Egyes kérések vagy függőségi hívások nagyobb valószínűségre lesznek kitéve a sikertelenségnek, mint mások; és a teljes hibaarány a terhelés növekedésével emelkedhet. Az intelligens detektálás gépi tanulással találja meg ezeket az anomáliákat.

Ahogy az adatok a webalkalmazásból kerülnek be az Application Insightsba, az intelligens észlelés összehasonlítja az aktuális viselkedést az elmúlt napokban látott mintákkal. Ha a korábbi teljesítményhez képest a sikertelenségi arány rendellenes növekedését észlelik, elemzés indul el.

Elemzés aktiválásakor a szolgáltatás fürtelemzést hajt végre a sikertelen kérésen, hogy megpróbálja azonosítani a hibákat jellemző értékmintát. 

A fenti példában az elemzés megállapította, hogy a legtöbb hiba egy adott eredménykódról, a kérelem névről, a kiszolgáló URL-állomásáról és a szerepkörpéldányról szól. 

Amikor a szolgáltatás ezekkel a hívásokkal van instrumentálva, az analizátor megkeresi a kivételt és a függőségi hibát, amely az általa azonosított fürtben lévő kérelmekhez kapcsolódik, valamint egy példát a kérelmekhez társított nyomkövetési naplókra.

Az eredményül kapott elemzést a rendszer riasztásként küldi el Önnek, kivéve, ha úgy állította be, hogy ne.

A [manuálisan beállított riasztásokhoz](../../azure-monitor/app/alerts.md)hasonlóan a rendszer is megvizsgálhatja az elindított riasztás állapotát, amely a probléma megoldása esetén megoldható. Konfigurálja a riasztási szabályokat az Application Insights-erőforrás Riasztások lapján. Más riasztásoktól eltérően azonban nem kell beállítania vagy konfigurálnia az intelligens észlelést. Ha szeretné, letilthatja, vagy módosíthatja a cél e-mail címét.

### <a name="alert-logic-details"></a>Riasztási logika részletei

A riasztások által aktivált saját gépi tanulási algoritmus, így nem tudjuk megosztani a pontos megvalósítási részleteket. Ezzel azt mondta, megértjük, hogy néha többet kell tudni arról, hogyan működik a mögöttes logika. Az elsődleges tényezők, amelyek értékelik annak megállapítására, hogy egy riasztást kell aktiválni a következők: 

* A kérelmek/függőségek hibaszázalékának elemzése 20 perces gördülő időablakban.
* Az elmúlt 20 perc hibaszázalékának összehasonlítása az elmúlt 40 perc és az elmúlt hét nap arányával, és olyan jelentős eltéréseket keresve, amelyek meghaladják az X-szer ezt a szórást.
* Adaptív korlát használata a minimális hiba százalék, amely az alkalmazás kérelmek/függőségek mennyiségétől függően változik.
* Van logika, amely automatikusan megoldja az eltöltött riasztásfigyelő feltétel, ha a probléma már nem észlelhető 8-24 órán keresztül.

## <a name="configure-alerts"></a>Riasztások konfigurálása

Letilthatja az intelligens észlelési riasztási szabályt a portálról vagy az Azure Resource Manager használatával ([lásd a sablonpéldát).](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

Ez a riasztási szabály egy "Application Insights Intelligens észlelés" nevű [társított műveletcsoporttal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) jön létre, amely e-mail- és webhook-műveleteket tartalmaz, és további műveletek indításához kiterjeszthető, amikor a riasztás aktiválódik.

> [!NOTE]
> Az ebből a riasztási szabályból küldött e-mail-értesítések alapértelmezés szerint az előfizetés figyelési olvasóés közreműködői szerepkörökhöz társított felhasználók számára kerülnek elküldésre. További információ erről [itt](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)érhető el .
> Az ebből a riasztási szabályból küldött értesítések a [közös riasztási sémát követik.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)
>

Nyissa meg a Riasztások lapot. Hiba anomáliák riasztási szabályok szerepelnek, valamint a manuálisan beállított riasztások, és láthatja, hogy jelenleg a riasztási állapotban van.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Kattintson a riasztásra a konfigurálásához.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

Figyelje meg, hogy letilthatja vagy törölheti a hibaanomáliák riasztási szabályt, de nem hozhat létre egy másikat ugyanazon az Application Insights-erőforráson.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Példa a hibaanomáliák riasztáswebhook hasznos

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Riasztás osztályozása és diagnosztizálása

A riasztás azt jelzi, hogy a sikertelen kérelmek arányának rendellenes növekedése észlelhető. Valószínű, hogy probléma van az alkalmazással vagy annak környezetével.

További vizsgálathoz kattintson a "Részletek megtekintése az Application Insights"-ban a linkeket ezen az oldalon fog egyenesen egy [keresési oldalra](../../azure-monitor/app/diagnostic-search.md) szűrve a vonatkozó kérelmek, kivétel, függőség, vagy nyomkövetés. 

Megnyithatja az [Azure Portalon,](https://portal.azure.com)keresse meg az alkalmazás hoz létre az Application Insights-erőforrást, és nyissa meg a Hibák lapot.

A "Hibák diagnosztizálása" gombra kattintva további részleteket kaphat, és megoldhatja a problémát.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

A kérelmek százalékos arányából és az érintett felhasználók számából eldöntheti, hogy mennyire sürgős a probléma. A fenti példában a 78,5%-os hibaarány a 2,2%-os normál arányhoz képest azt jelzi, hogy valami rossz történik. Másrészt csak 46 felhasználót érintett. Ha ez az alkalmazás, akkor képes lenne felmérni, hogy mennyire komoly ez.

Sok esetben gyorsan diagnosztizálhatja a problémát a kérelem nevéből, a kivételből, a függőségi hibából és a megadott nyomkövetési adatokból.

Ebben a példában volt egy kivétel az SQL-adatbázisból a kérelemkorlát elérése miatt.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Legutóbbi riasztások áttekintése

Kattintson **a Riasztások** az Application Insights erőforrás lapon a legutóbbi elindított riasztások:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Mi a különbség ...
A hibaanomáliák intelligens észlelése kiegészíti az Application Insights más hasonló, de különálló szolgáltatásait.

* [Metrikariasztások](../../azure-monitor/app/alerts.md) vannak beállítva, és a metrikák széles skáláját figyelheti, például a CPU-kihasználtság, a kérelem aránya, oldal betöltési idő, és így tovább. Ezek segítségével figyelmeztetheti például, ha további erőforrásokat kell hozzáadnia. Ezzel szemben a hibaészlelések intelligens észlelése a kritikus metrikák (jelenleg csak sikertelen kérelmek aránya) egy kis tartományát fedi le, amelynek célja, hogy közel valós időben értesítse Önt, ha a webalkalmazás sikertelen kérelmek aránya növekszik a webalkalmazás normál viselkedéséhez képest. A metrikariasztásoktól eltérően az intelligens észlelési adatok automatikusan beállítják és frissíti a küszöbértékeket a viselkedés változásaiban. Az intelligens észlelés elindítja a diagnosztikai munkát is, így időt takaríthat meg a problémák megoldásában.

* [A teljesítményanomáliák intelligens észlelése](proactive-performance-diagnostics.md) gépi intelligenciát is használ a metrikák szokatlan mintáinak felderítéséhez, és nincs szükség ön általi konfigurációra. A hibaanomáliák intelligens felismerésével ellentétben azonban a teljesítményanomáliák intelligens észlelése a használati sokpontos szegmensek olyan szegmenseinek megkeresése, amelyek rosszul kiszolgálásra kerülhetnek - például egy adott típusú böngésző bizonyos oldalai. Az elemzést naponta végezzük, és ha bármilyen eredményt talál, akkor valószínűleg sokkal kevésbé sürgős, mint egy riasztás. Ezzel szemben a hibaanomáliák elemzését a rendszer folyamatosan elvégzi a bejövő alkalmazásadatokon, és perceken belül értesítést kap, ha a kiszolgáló meghibásodási aránya a vártnál nagyobb.

## <a name="if-you-receive-a-smart-detection-alert"></a>Ha intelligens észlelési riasztást kap
*Miért kaptam ezt a figyelmeztetést?*

* A sikertelen kérelmek arányának rendellenes növekedését észleltük az előző időszak normál alapértékéhez képest. A hibák és a kapcsolódó alkalmazásadatok elemzése után úgy gondoljuk, hogy van egy probléma, amelyet meg kell vizsgálnia.

*Az értesítés azt jelenti, hogy határozottan problémám van?*

* Igyekszünk riasztást az alkalmazás zavara vagy leépülése, de csak ön tudja teljes mértékben megérteni a szemantika és az alkalmazásra vagy a felhasználókra gyakorolt hatás.

*Szóval, a jelentkezési adataimat nézed?*

* Nem. A szolgáltatás teljesen automatikus. Csak ön kapja meg az értesítéseket. Az adatok [private](../../azure-monitor/app/data-retention-privacy.md)privátak.

*Elő kell fizetnem erre a riasztásra?*

* Nem. Minden alkalmazás, amely elküldi a kérelem adatait rendelkezik az intelligens észlelési riasztási szabály.

*Leiratkozhatok, vagy helyette a munkatársaimnak küldhetem az értesítéseket?*

* Igen, A riasztási szabályokban kattintson az intelligens észlelési szabálykonfigurálására. Letilthatja a riasztást, vagy módosíthatja a riasztás címzettjeit.

*Elvesztettem az e-mailt. Hol találom az értesítéseket a portálon?*

* A tevékenységnaplókban. Az Azure-ban nyissa meg az Application Insights-erőforrást az alkalmazáshoz, majd válassza a Tevékenységnaplók lehetőséget.

*Néhány figyelmeztető jelzés ismert problémákról szól, és nem akarom megkapni őket.*

* Használhatja [a riasztási műveletszabályok](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules) elnyomása funkciót.

## <a name="next-steps"></a>További lépések
Ezek a diagnosztikai eszközök segítenek az alkalmazásból származó adatok vizsgálatában:

* [Metrikus felfedező](../../azure-monitor/app/metrics-explorer.md)
* [Kereséskezelő](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – hatékony lekérdezési nyelv](../../azure-monitor/log-query/get-started-portal.md)

Az intelligens észlelések automatikusak. De talán szeretne még néhány riasztást beállítani?

* [Manuálisan konfigurált metrikariasztások](../../azure-monitor/app/alerts.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
