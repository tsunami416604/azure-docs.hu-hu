---
title: Azure Log Analytics-adatok importálása a Power BI-bA |} Microsoft Docs
description: A Power BI-alapú üzleti analytics felhőszolgáltatás, amely gazdag képi megjelenítések és jelentéseket biztosít a különböző adatok elemzése a Microsoft.  Ez a cikk ismerteti, hogyan konfigurálhatja a Naplóelemzési adatok importálása a Power BI-ba, és konfigurálja úgy, hogy automatikusan frissítse.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 6d7f8f89f90223dc5dd186a63b3912a13910cb34
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Azure Log Analytics-adatok importálása a Power BI-bA


[A Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) , amely gazdag képi megjelenítések és jelentéseket biztosít a különböző adatok elemzése a Microsoft felhőalapú üzleti analytics szolgáltatás.  Így kihasználhatja a szolgáltatások suchas combing különböző forrásokból származó adatok és a webes és mobil eszközök megosztása a Naplóelemzési napló keresés eredményeit tudja importálni a Power BI-adatkészletet.

## <a name="overview"></a>Áttekintés
A Naplóelemzési munkaterület adatokat importálhat a Power bi-ba, létrehozhat egy adatkészlet Naplóelemzési a napló keresési lekérdezés alapján a Power BI-ban.  A lekérdezés futtatása az adatkészlet frissítésekor.  Ezután az adatkészlet adatait használó Power BI-jelentéseket hozhat létre.  Sikerült létrehozni az adatkészletet a Power bi-ban, a lekérdezés exportálása Log Analyticshez való [Power Query (M) nyelvi](https://msdn.microsoft.com/library/mt807488.aspx).  Használhatja a Power BI Desktop-lekérdezés létrehozásához és közzétételéhez adatkészletként Power bi-ban.  Ez a folyamat részleteit az alábbiakban található.

![Naplóelemzési Power bi-bA](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Az exportálási lekérdezés
Először hozzon létre egy [naplófájl-keresési](log-analytics-log-search-new.md) , amely visszaadja az adatokat a Naplóelemzési, hogy szeretné-e feltölteni a Power BI-adatkészlet.  Majd exportálja a lekérdezés [Power Query (M) nyelvi](https://msdn.microsoft.com/library/mt807488.aspx) Power BI Desktop használhatják.

1. Hozzon létre a naplófájl-keresési Naplóelemzési a DataSet adatokat nyerhet ki.
2. Ha a naplófájl-keresési portál használata esetén kattintson **Power BI**.  Az Analytics portál használata, válassza ki a **exportálása** > **Power BI lekérdezés (M)**.  A lekérdezés mindkét lehetőség exportálása szövegfájlba nevű **PowerBIQuery.txt**. 

    ![Naplófájl-keresési exportálása](media/log-analytics-powerbi/export-logsearch.png) ![Naplófájl-keresési exportálása](media/log-analytics-powerbi/export-analytics.png)

3. Nyissa meg a szöveges fájlt, és a tartalom másolása.

## <a name="import-query-into-power-bi-desktop"></a>Lekérdezés importálása a Power BI Desktop
A Power BI Desktop egy asztali alkalmazás, amely lehetővé teszi az adatkészleteket és jelentéseket a Power bi-bA közzétehető létrehozásához.  Hozzon létre egy lekérdezést a Power Query-nyelv Log Analytics-ból exportált is használja. 

1. Telepítés [Power BI Desktop](https://powerbi.microsoft.com/desktop/) Ha nincs telepítve a számítógépen, és nyissa meg az alkalmazást.
2. Válassza ki **adatok beolvasása** > **üres lekérdezés** , megnyílik egy új lekérdezés.  Válassza ki **speciális szerkesztő** és az exportált fájl tartalmának beillesztése a lekérdezést. Kattintson a **Done** (Kész) gombra.

    ![A Power BI Desktop-lekérdezés](media/log-analytics-powerbi/desktop-new-query.png)

5. A lekérdezés futtatása, és megjeleníti az eredményeket.  Az Azure eléréséhez megadott hitelesítő adatok kérheti.  
6. Adjon meg egy leíró nevet a lekérdezéshez.  Az alapértelmezett érték **lekérdezés1**. Kattintson a **zárja be, és alkalmazni** adatkészlet hozzáadása a jelentéshez.

    ![A Power BI Desktop neve](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>A Power bi-bA közzététele
Ha közzéteszi a Power bi-ba, létrejön a DataSet adatkészlet és a jelentést.  Ha a Power BI Desktop jelentést hoz létre, majd ez közzéteszi az adatait.  Ha nem, majd egy üres jelentés jön létre.  Módosíthatja a jelentést a Power bi-ban, vagy hozzon létre egy újat a DataSet adatkészlet alapján.

8. Hozzon létre egy jelentést, az adatok alapján.  Használjon [Power BI Desktop dokumentáció](https://docs.microsoft.com/power-bi/desktop-report-view) Ha még nem ismeri a azt.  Amikor készen áll a elküldi a Power bi-ba, kattintson **közzététel**.  Amikor a rendszer kéri, válassza ki a cél a Power BI-fiókjába.  Ha egy megadott helyre szem előtt nincs használni **saját munkaterület**.

    ![A Power BI Desktop közzététele](media/log-analytics-powerbi/desktop-publish.png)

3. A közzétételi befejeztével kattintson **nyissa meg a Power BI-ban** az új adatkészlet rendelkező Power BI megnyitásához.


### <a name="configure-scheduled-refresh"></a>Ütemezett frissítés konfigurálása
A Power BI-ban létrehozott adatkészlet ugyanazokat az adatokat a Power BI Desktop korábban látott fog rendelkezni.  Frissítse az adatkészletet rendszeresen futtassa újból a lekérdezést, és töltse ki a legfrissebb adatokat Log Analyticshez való kell.  

1. Kattintson a munkaterületen, ahol feltöltött a jelentést, és válassza ki a **adatkészletek** menü. Válassza ki a helyi menü az új adatkészlet mellett, majd **beállítások**. A **adatforrás hitelesítő adatai** rendelkeznie kell egy üzenetet, hogy a hitelesítő adatok érvénytelenek.  Ennek oka az, még ha az adatok frissítésekor használandó adatkészlet hitelesítő adatai még nem biztosított.  Kattintson a **hitelesítő adatok szerkesztése** és adja meg azokat a Log Analyticshez való hozzáférés.

    ![A Power BI ütemezése](media/log-analytics-powerbi/powerbi-schedule.png)

5. A **ütemezetten frissíteni** kapcsolja be a **tartsa adatait naprakészen**.  Igény szerint módosíthatja a **frissítési gyakoriság** és a frissítés futtatásához egy vagy több meghatározott alkalommal.

    ![A Power BI frissítése](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) Power bi szolgáltatásba exportálhatók lekérdezések összeállításához.
* További információ [Power BI](http://powerbi.microsoft.com) Naplóelemzési export alapuló képi megjelenítések létrehozásához.
