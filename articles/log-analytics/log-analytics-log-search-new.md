---
title: "A keresések jelentkezzen be Azure Naplóelemzés |} Microsoft Docs"
description: "A napló keresési adatokat lekérdezni a Naplóelemzési van szüksége.  Ez a cikk ismerteti a keresések Naplóelemzési használt új naplófájl és alapfogalmakat, amelyeket meg kell ismernie előtt hozzon létre egyet."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>A Naplóelemzési keres ismertetése napló

A napló keresési adatokat lekérdezni a Naplóelemzési van szüksége.  Adatok a portál most elemzése, hogy egy riasztási szabály, amely egy adott esemény értesíti konfigurálásához, vagy a napló Analytics API-jával adatok beolvasása szüksége lesz egy naplófájl-keresési adhatja meg a kívánt adatokat.  Ez a cikk ismerteti a napló keresés használata a Naplóelemzési és fogalmat tisztában kell lennie azzal előtt hozzon létre egyet. Tekintse meg a [további lépések](#next-steps) szakaszban talál részletes információt létrehozása és szerkesztése a napló keresések és a lekérdezési nyelv a hivatkozásokat.

## <a name="where-log-searches-are-used"></a>Ha a napló keresések használnak

A különböző módon, hogy szüksége lesz napló keresések Naplóelemzési közé tartoznak a következők:

- **Portálok.** Adatok elemzése interaktív elvégezhető a tárházat az Azure portálon vagy a [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587).  Ez lehetővé teszi, hogy a lekérdezés szerkesztése és a különböző formátumok és a képi megjelenítések eredményének kiértékelése.  Az Ön által létrehozott irányuló legtöbb lekérdezésnek a portálok egyikében elindul, és majd másolása után ellenőrizte, hogy az elvárt módon működik.
- **A riasztási szabályok.** [Riasztási szabályok](log-analytics-alerts.md) proaktív módon az adatokat a munkaterületen a problémák azonosításához.  Minden riasztási szabály, amely rendszeres időközönként automatikusan fut egy napló keresési alapul.  Annak meghatározásához, ha a riasztás létrejöjjön ellenőrzik az eredményeket.
- **Nézetek.**  A felhasználó irányítópultok szereplő adatok képi megjelenítések hozhat létre [adatforrásnézet-tervezőből](log-analytics-view-designer.md).  Napló keresések adja meg az adatok által használt [csempék](log-analytics-view-designer-tiles.md) és [képi megjelenítés részek](log-analytics-view-designer-parts.md) az egyes nézetek.  Részletezhető le a képi megjelenítés részeiből a napló lapon további elemzés elvégzéséhez az adatokon.
- **Exportálás.**  Adatainak exportálásához a Naplóelemzési munkaterület Excelbe vagy [Power BI](log-analytics-powerbi.md), létrehozhat egy napló keresést, az adatok exportálása meghatározásához.
- **PowerShell.** Egy PowerShell-parancsfájlt futtathatja a parancssorban vagy egy Azure Automation-runbook által használt [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) Naplóelemzési adatok lekérése.  Ehhez a parancsmaghoz szükséges az adatok beolvasása a lekérdezést.
- **Napló Analytics API.**  A [Naplóelemzési jelentkezzen keresési API-JÁNAK](log-analytics-log-search-api.md) lehetővé teszi, hogy a REST API-ügyfél adatok lekérése a munkaterületen.  Az API-kérelem Naplóelemzési beolvasása az adatok meghatározásához futtatott lekérdezés tartalmazza.

![Napló-keresések](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics-adatok
-Lekérdezés összeállításához indítása meghatározása a táblák a keresett adatokkal rendelkeznek. Minden egyes [adatforrás](log-analytics-data-sources.md) és [megoldás](../operations-management-suite/operations-management-suite-solutions.md) tárolja az adatokat a Naplóelemzési munkaterület dedikált táblájában.  Minden adatforrás és a megoldás dokumentációja tartalmazza, az általa létrehozott adattípus neve és azok leírását tulajdonságát.  Sok lekérdezések csak egyetlen táblázatok adatait, de mások segítségével számos lehetőséget több tábla adatait tartalmazza.

![Táblák](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>A lekérdezés írása
Napló középpontjában megkeresi a Naplóelemzési van [egy részletes lekérdező nyelv](https://docs.loganalytics.io/) , amely lehetővé teszi beolvasása és elemezhetik a tárházból az sokféleképpen.  Ugyanezen a nyelven lekérdezés használt [Application Insights](../application-insights/app-insights-analytics.md).  Tudnivalók a lekérdezés írása fontos a napló keresések létrehozása a Naplóelemzési.  Bemutatjuk az alapvető lekérdezések általában megkezdi, majd előrehaladás összetettebb funkciók, a követelmények még bonyolultabbá válnak.

Lekérdezés alapvető szerkezete operátorok a függőleges vonás elválasztott sorozatát követ forrástábla `|`.  Akkor is láncolt együtt pontosítsa az adatokat, és hajtsa végre a speciális funkciók több operátor.

Tegyük fel, hogy a felső tíz számítógépeken a legtöbb hibaesemények megkereshető az elmúlt nap során.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Vagy a számítógépek, amelyek az elmúlt nap során a szívverés nem volt található szeretne.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Mi a helyzet a sor diagramot processzorhasználata az elmúlt hét számítógépenként?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

A következő gyors mintákat tekintheti meg, függetlenül attól, milyen típusú adatok dolgozik, a lekérdezés szerkezete hasonlít.  Oszthatja azt a különböző lépéseket, ahol az eredményül kapott egy parancs keresztül küldött adatok a folyamat a következő paranccsal.

Adatok között a Naplóelemzési munkaterület az előfizetésen belül is lekérdezheti.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Az az Azure Log Analytics lekérdezési nyelv oktatóanyagok és nyelvi referencia többek között a teljes dokumentációjáért lásd: a [Azure Log Analytics lekérdezési nyelv dokumentáció](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Következő lépések

- További tudnivalók a [létrehozásához és szerkesztéséhez használt portálok jelentkezzen keresések](log-analytics-log-search-portals.md).
- Tekintse meg a [útmutató a lekérdezések írásáról](log-analytics-tutorial-viewdata.md) az új lekérdezés nyelven.
