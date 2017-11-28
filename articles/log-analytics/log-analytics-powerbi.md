---
title: "Azure Log Analytics-adatok importálása a Power BI-bA |} Microsoft Docs"
description: "A Power BI-alapú üzleti analytics felhőszolgáltatás, amely gazdag képi megjelenítések és jelentéseket biztosít a különböző adatok elemzése a Microsoft.  Ez a cikk ismerteti, hogyan konfigurálhatja a Naplóelemzési adatok importálása a Power BI-ba, és konfigurálja úgy, hogy automatikusan frissítse."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: bwren
ms.openlocfilehash: 163ac33af43a8cb7a23742f6336efca5fe7c4b4e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Azure Log Analytics-adatok importálása a Power BI-bA


[A Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) , amely gazdag képi megjelenítések és jelentéseket biztosít a különböző adatok elemzése a Microsoft felhőalapú üzleti analytics szolgáltatás.  Így kihasználhatja a szolgáltatások suchas combing különböző forrásokból származó adatok és a webes és mobil eszközök megosztása a Naplóelemzési napló keresés eredményeit tudja importálni a Power BI-adatkészletet.

Ez a cikk részletesen Log Analytics-adatok importálása a Power BI-ba, és ütemezése úgy, hogy automatikusan frissítse.  Különböző folyamatok megtalálhatóak egy [frissítése](#upgraded-workspace) és egy [örökölt](#legacy-workspace) munkaterületen.

## <a name="upgraded-workspace"></a>Frissített munkaterület


Importálja az adatokat egy [Naplóelemzési munkaterület frissítése](log-analytics-log-search-upgrade.md) a Power bi-ba, Naplóelemzési a napló keresési lekérdezés alapján a Power BI-ban létrehozhat egy adatkészlet.  A lekérdezés futtatása az adatkészlet frissítésekor.  Ezután az adatkészlet adatait használó Power BI-jelentéseket hozhat létre.  Sikerült létrehozni az adatkészletet a Power bi-ban, a lekérdezés exportálása Log Analyticshez való [Power Query (M) nyelvi](https://msdn.microsoft.com/library/mt807488.aspx).  Használhatja a Power BI Desktop-lekérdezés létrehozásához és közzétételéhez adatkészletként Power bi-ban.  Ez a folyamat részleteit az alábbiakban található.

![Naplóelemzési Power bi-bA](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Az exportálási lekérdezés
Először hozzon létre egy [naplófájl-keresési](log-analytics-log-search-new.md) , amely visszaadja az adatokat a Naplóelemzési, hogy szeretné-e feltölteni a Power BI-adatkészlet.  Majd exportálja a lekérdezés [Power Query (M) nyelvi](https://msdn.microsoft.com/library/mt807488.aspx) Power BI Desktop használhatják.

1. Hozzon létre a naplófájl-keresési Naplóelemzési a DataSet adatokat nyerhet ki.
2. Ha a naplófájl-keresési portál használata esetén kattintson **Power BI**.  Az Analytics portál használata, válassza ki a **exportálása** > **Power BI lekérdezés (M)**.  A lekérdezés mindkét lehetőség exportálása szövegfájlba nevű **PowerBIQuery.txt**. 

    ![Naplófájl-keresési exportálása](media/log-analytics-powerbi/export-logsearch.png) ![Naplófájl-keresési exportálása](media/log-analytics-powerbi/export-analytics.png)

3. Nyissa meg a szöveges fájlt, és a tartalom másolása.

### <a name="import-query-into-power-bi-desktop"></a>Lekérdezés importálása a Power BI Desktop
A Power BI Desktop egy asztali alkalmazás, amely lehetővé teszi az adatkészleteket és jelentéseket a Power bi-bA közzétehető létrehozásához.  Hozzon létre egy lekérdezést a Power Query-nyelv Log Analytics-ból exportált is használja. 

1. Telepítés [Power BI Desktop](https://powerbi.microsoft.com/desktop/) Ha nincs telepítve a számítógépen, és nyissa meg az alkalmazást.
2. Válassza ki **adatok beolvasása** > **üres lekérdezés** , megnyílik egy új lekérdezés.  Válassza ki **speciális szerkesztő** és az exportált fájl tartalmának beillesztése a lekérdezést. Kattintson a **Done** (Kész) gombra.

    ![A Power BI Desktop-lekérdezés](media/log-analytics-powerbi/desktop-new-query.png)

5. A lekérdezés futtatása, és megjeleníti az eredményeket.  Az Azure eléréséhez megadott hitelesítő adatok kérheti.  
6. Adjon meg egy leíró nevet a lekérdezéshez.  Az alapértelmezett érték **lekérdezés1**. Kattintson a **zárja be, és alkalmazni** adatkészlet hozzáadása a jelentéshez.

    ![A Power BI Desktop neve](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>A Power bi-bA közzététele
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

## <a name="legacy-workspace"></a>A hagyományos munkaterület
A Power BI konfigurálása során egy [örökölt Naplóelemzési munkaterület](log-analytics-powerbi.md), az eredmények exportálása a Power BI megfelelő adatkészletek napló lekérdezések létrehozása.  A lekérdezés és exportálás a dataset naprakészen tartása a legújabb Naplóelemzési gyűjtött adatok meghatározó ütemezés szerint automatikusan fut tovább.

![Naplóelemzési Power bi-bA](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>A Power BI ütemezése
A *Power BI ütemezés* adatok exportálja az OMS-tárház Power bi-ban, és egy ütemezést, amely meghatározza, milyen gyakran fut a Keresés a dataset naprakészen tartása a megfelelő DataSet napló keresést tartalmazza.

A dataset mezőinek fog egyezni a rekordokat a naplóban keresés által visszaadott tulajdonságait.  Ha a keresés különböző típusú rekordot visszaadja a dataset fogja tartalmazni az egyes belefoglalt bejegyzéstípust tulajdonságokat.  

### <a name="connecting-oms-workspace-to-power-bi"></a>A Power bi-hoz kapcsolódó OMS-munkaterület
Exportálás előtt fel Naplóelemzési a Power bi-ba, csatlakoznia kell az OMS-munkaterület az alábbi eljárást követve a Power BI-fiókjába.  

1. Az OMS-konzolon kattintson a **beállítások** csempére.
2. Válassza ki **fiókok**.
3. Az a **munkaterület információk** szakaszban kattintson **Power BI-fiókjához csatlakozhat**.
4. Adja meg a hitelesítő adatait a Power BI-fiókjába.

### <a name="create-a-power-bi-schedule"></a>A Power BI ütemezés létrehozása
Minden az adatkészlethez az alábbi eljárást követve a Power BI ütemezés létrehozása.

1. Az OMS-konzolon kattintson a **naplófájl-keresési** csempére.
2. Adja meg egy új lekérdezést, vagy válasszon ki egy mentett keresést, amely visszaadja az adatokat az exportálni kívánt **Power BI**.  
3. Kattintson a **Power BI** gombra, nyissa meg a lap tetején a **Power BI** párbeszédpanel.
4. Adja meg az adatokat a következő táblázat, majd kattintson a **mentése**.

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |Neve az ütemezés azonosításához, ha a Power BI ütemezések listájának megtekintése. |
| Mentett keresés |A napló keresés futtatásához.  Válassza ki az aktuális lekérdezés, vagy válassza ki a korábban mentett keresési a legördülő listából. |
| Ütemezés |Milyen gyakran szeretné a mentett keresés futtatásához, és exportálhatja a Power BI-adatkészlet.  Az érték 15 perc és 24 óra között kell lennie. |
| A DataSet neve |A Power bi-ban a DataSet adatkészlet neve.  Rendszer hozható létre, ha még nem létezik és frissítése, ha már létezik. |

### <a name="viewing-and-removing-power-bi-schedules"></a>A Power BI ütemezések megtekintése és eltávolítása
A következő eljárással meglévő Power BI ütemezések listájának megtekintése.

1. Az OMS-konzolon kattintson a **beállítások** csempére.
2. Válassza ki **a Power BI**.

Az ütemezés adatait, valamint a száma, amelyek az elmúlt héten az ütemezés futtatása és a legutóbbi szinkronizálás állapotának jelennek meg.  Ha a szinkronizálás hibát jelzett, akkor a hivatkozásra rekordok napló keresése hibaüzenettel tartalmazza a hiba adatait.

Egy ütemezés eltávolításához kattintson a a **X** a a **eltávolítása oszlop**.  Ütemezés szerint letilthatja kiválasztásával **ki**.  Ütemezésének módosítása távolítsa el azt, majd hozza létre újra az új beállításokkal.

![A Power BI ütemezése](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>A minta forgatókönyv
A következő szakasz végigvezeti a Power BI ütemezés létrehozása és a hozzá tartozó adatkészlet használatával hozzon létre egy egyszerű jelentést egy példát.  Ebben a példában állítja be a számítógépek minden teljesítményadat exportálja a Power bi-ba, és akkor jön létre egy vonaldiagramot a processzorhasználat megjelenítéséhez.

#### <a name="create-log-search"></a>Naplófájl-keresési létrehozása
Először hozzon létre egy napló keresési azt szeretnénk, hogy az adatkészlet küldendő adatok.  A jelen példában használjuk a számítógépek névvel rendelkező összes teljesítményadatokat visszaadó *srv*.  

![A Power BI ütemezése](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>A Power BI keresési létrehozása
Elemre kattint a **Power BI** gombra kattintva nyissa meg a Power BI párbeszédpanelt, és adja meg a szükséges adatokat.  Azt szeretnénk, hogy ez a keresés óránként egyszer futtatni, és hozzon létre egy nevű adatkészlet *Contoso telj*.  Már van a keresés meg van nyitva, amely azt szeretnénk, ha az adatokat hoz létre, mert azt hagyja meg az alapértelmezett *használata aktuális keresési lekérdezés* a **mentett keresési**.

![A Power BI-keresés](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Ellenőrizze a Power BI-keresés
Győződjön meg arról, hogy az ütemezés megfelelően létrehozott, a Microsoft Power BI keresések alatt listájának megtekintése a a **beállítások** az OMS-irányítópulton csempére.  A Microsoft Várjon egy pár percet, és frissítse a nézetet, mindaddig, amíg a rendszer jelzi, hogy a történt szinkronizálás.  Általában kell ütemezni, az adatkészlet automatikus frissítését.

![A Power BI-keresés](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>A Power BI-ban az adatforrás ellenőrzése
Azt a fiók be tudjon jelentkezni [powerbi.microsoft.com](http://powerbi.microsoft.com/) görgessen **adatkészletek** a bal oldali ablaktábla alján.  Láthatja, hogy a *Contoso telj* dataset szerepel, amely azt jelzi, hogy az exportálás sikeresen lefutott.

![A Power BI-adatkészlet](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Az adatkészlet-alapú jelentés létrehozása
Válassza azt a **Contoso telj** adatkészlet majd kattintson a **eredmények** a a **mezők** a mezőket az adatkészlethez tartozó megtekintéséhez a jobb oldali ablaktáblán.  Hozzon létre egy sor diagramját processzorhasználat számítógépenként, a következő műveleteket végezzük.

1. Válassza ki a sor diagram képi megjelenítés.
2. A csomóponthúzási **ObjectName** való **jelentés szintű szűrő** , és ellenőrizze, **processzor**.
3. A csomóponthúzási **CounterName** való **jelentés szintű szűrő** , és ellenőrizze, **processzoridő %**.
4. A csomóponthúzási **ellenértéknek** való **értékek**.
5. A csomóponthúzási **számítógép** való **jelmagyarázat**.
6. A csomóponthúzási **TimeGenerated** való **tengely**.

Láthatja, hogy az eredményül kapott vonaldiagram jelenik meg, amely az adathalmaz adatait.

![A Power BI vonaldiagram](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>A jelentés mentése
Azt a képernyő tetején a Mentés gombra kattintva mentse a jelentést, és ellenőrizze, hogy most már szerepel a jelentések szakaszban, a bal oldali ablaktáblán.

![A Power BI-jelentések](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Következő lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) Power bi szolgáltatásba exportálhatók lekérdezések összeállításához.
* További információ [Power BI](http://powerbi.microsoft.com) Naplóelemzési export alapuló képi megjelenítések létrehozásához.
