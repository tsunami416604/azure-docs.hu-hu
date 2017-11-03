---
title: "A Naplóelemzési adatok exportálása a Power bi-bA |} Microsoft Docs"
description: "A Power BI-alapú üzleti analytics felhőszolgáltatás, amely gazdag képi megjelenítések és jelentéseket biztosít a különböző adatok elemzése a Microsoft.  A Naplóelemzési folyamatosan adatait exportálhatja az OMS-tárház Power BI-ba így kihasználhatja a képi megjelenítések és elemzésére szolgáló eszközöket.  Ez a cikk ismerteti a lekérdezések konfigurálása a szolgáltatáshoz, amely rendszeres időközönként a Power BI automatikusan exportálja."
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
ms.date: 07/24/2017
ms.author: bwren
ms.openlocfilehash: 271747e25f319c76195ec643025d24c6b7cdc9c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="export-log-analytics-data-to-power-bi"></a>A Naplóelemzési adatok exportálása a Power bi-bA

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a folyamatot a Power bi-bA Naplóelemzési adatexportálási nem fog többé működni.  A frissítés előtt létrehozott meglévő ütemezések a program letiltja. Látni fogja emellett már nem képes az előzetes verziójú funkciók, beállítások, a Power BI exportálási funkció bekapcsolása a szolgáltatás teljesen kiadott a frissített munkaterületek. 
>
> Frissítés után Azure Naplóelemzés használ ugyanannak a platformnak Application Insights, és Ön ugyanazzal az eljárással Naplóelemzési lekérdezések exportálásáról a Power bi-ba, [a folyamat az Application Insights lekérdezések exportálásáról a Power bi-bA](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Exportálás a lekérdezést az Analytics-konzol használatával, ha a cikkben leírtak szerint is, vagy választhat a **Power BI** gombra a keresési napló portálon a képernyő tetején.
>
> Felhasználók kell használni a Power BI exportálás capabilitiy frissített munkaterületek Azure-ban a munkaterület erőforrás elérésére. Hozzáférés, nélkül felhasználók hibaüzenetet fog látni egy a lekérdezést, hogy nem rendelkeznek hozzáféréssel a Power BI desktopba importálásakor.



[A Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) , amely gazdag képi megjelenítések és jelentéseket biztosít a különböző adatok elemzése a Microsoft felhőalapú üzleti analytics szolgáltatás.  A Naplóelemzési automatikusan adatait exportálhatja az OMS-tárház Power BI-ba így kihasználhatja a képi megjelenítések és elemzésére szolgáló eszközöket.

A Power BI Log Analyticshez való konfigurálásakor, az eredmények exportálása a Power BI megfelelő adatkészletek napló lekérdezéseket hozhat létre.  A lekérdezés és exportálás a dataset naprakészen tartása a legújabb Naplóelemzési gyűjtött adatok meghatározó ütemezés szerint automatikusan fut tovább.

![Naplóelemzési Power bi-bA](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>A Power BI ütemezése
A *Power BI ütemezés* adatok exportálja az OMS-tárház Power bi-ban, és egy ütemezést, amely meghatározza, milyen gyakran fut a Keresés a dataset naprakészen tartása a megfelelő DataSet napló keresést tartalmazza.

A dataset mezőinek fog egyezni a rekordokat a naplóban keresés által visszaadott tulajdonságait.  Ha a keresés különböző típusú rekordot visszaadja a dataset fogja tartalmazni az egyes belefoglalt bejegyzéstípust tulajdonságokat.  

> [!NOTE]
> Nyers adatok figyelésekor parancsokkal, mint bármely összevonás végrehajtását visszaadó napló keresési lekérdezés használata ajánlott eljárás [mérték](log-analytics-search-reference.md#measure).  Is elvégezheti bármely összevonásának és a számítások a Power bi-ban a nyers adatok.
>
>

## <a name="connecting-oms-workspace-to-power-bi"></a>A Power bi-hoz kapcsolódó OMS-munkaterület
Exportálás előtt fel Naplóelemzési a Power bi-ba, csatlakoznia kell az OMS-munkaterület az alábbi eljárást követve a Power BI-fiókjába.  

1. Az OMS-konzolon kattintson a **beállítások** csempére.
2. Válassza ki **fiókok**.
3. Az a **munkaterület információk** szakaszban kattintson **Power BI-fiókjához csatlakozhat**.
4. Adja meg a hitelesítő adatait a Power BI-fiókjába.

## <a name="create-a-power-bi-schedule"></a>A Power BI ütemezés létrehozása
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

## <a name="viewing-and-removing-power-bi-schedules"></a>A Power BI ütemezések megtekintése és eltávolítása
A következő eljárással meglévő Power BI ütemezések listájának megtekintése.

1. Az OMS-konzolon kattintson a **beállítások** csempére.
2. Válassza ki **a Power BI**.

Az ütemezés adatait, valamint a száma, amelyek az elmúlt héten az ütemezés futtatása és a legutóbbi szinkronizálás állapotának jelennek meg.  Ha a szinkronizálás hibát jelzett, akkor a hivatkozásra rekordok napló keresése hibaüzenettel tartalmazza a hiba adatait.

Egy ütemezés eltávolításához kattintson a a **X** a a **eltávolítása oszlop**.  Ütemezés szerint letilthatja kiválasztásával **ki**.  Ütemezésének módosítása távolítsa el azt, majd hozza létre újra az új beállításokkal.

![A Power BI ütemezése](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>A minta forgatókönyv
A következő szakasz végigvezeti a Power BI ütemezés létrehozása és a hozzá tartozó adatkészlet használatával hozzon létre egy egyszerű jelentést egy példát.  Ebben a példában állítja be a számítógépek minden teljesítményadat exportálja a Power bi-ba, és akkor jön létre egy vonaldiagramot a processzorhasználat megjelenítéséhez.

### <a name="create-log-search"></a>Naplófájl-keresési létrehozása
Először hozzon létre egy napló keresési azt szeretnénk, hogy az adatkészlet küldendő adatok.  A jelen példában használjuk a számítógépek névvel rendelkező összes teljesítményadatokat visszaadó *srv*.  

![A Power BI ütemezése](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>A Power BI keresési létrehozása
Elemre kattint a **Power BI** gombra kattintva nyissa meg a Power BI párbeszédpanelt, és adja meg a szükséges adatokat.  Azt szeretnénk, hogy ez a keresés óránként egyszer futtatni, és hozzon létre egy nevű adatkészlet *Contoso telj*.  Már van a keresés meg van nyitva, amely azt szeretnénk, ha az adatokat hoz létre, mert azt hagyja meg az alapértelmezett *használata aktuális keresési lekérdezés* a **mentett keresési**.

![A Power BI-keresés](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Ellenőrizze a Power BI-keresés
Győződjön meg arról, hogy az ütemezés megfelelően létrehozott, a Microsoft Power BI keresések alatt listájának megtekintése a a **beállítások** az OMS-irányítópulton csempére.  A Microsoft Várjon egy pár percet, és frissítse a nézetet, mindaddig, amíg a rendszer jelzi, hogy a történt szinkronizálás.

![A Power BI-keresés](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>A Power BI-ban az adatforrás ellenőrzése
Azt a fiók be tudjon jelentkezni [powerbi.microsoft.com](http://powerbi.microsoft.com/) görgessen **adatkészletek** a bal oldali ablaktábla alján.  Láthatja, hogy a *Contoso telj* dataset szerepel, amely azt jelzi, hogy az exportálás sikeresen lefutott.

![A Power BI-adatkészlet](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Az adatkészlet-alapú jelentés létrehozása
Válassza azt a **Contoso telj** adatkészlet majd kattintson a **eredmények** a a **mezők** a mezőket az adatkészlethez tartozó megtekintéséhez a jobb oldali ablaktáblán.  Hozzon létre egy sor diagramját processzorhasználat számítógépenként, a következő műveleteket végezzük.

1. Válassza ki a sor diagram képi megjelenítés.
2. A csomóponthúzási **ObjectName** való **jelentés szintű szűrő** , és ellenőrizze, **processzor**.
3. A csomóponthúzási **CounterName** való **jelentés szintű szűrő** , és ellenőrizze, **processzoridő %**.
4. A csomóponthúzási **ellenértéknek** való **értékek**.
5. A csomóponthúzási **számítógép** való **jelmagyarázat**.
6. A csomóponthúzási **TimeGenerated** való **tengely**.

Láthatja, hogy az eredményül kapott vonaldiagram jelenik meg, amely az adathalmaz adatait.

![A Power BI vonaldiagram](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>A jelentés mentése
Azt a képernyő tetején a Mentés gombra kattintva mentse a jelentést, és ellenőrizze, hogy most már szerepel a jelentések szakaszban, a bal oldali ablaktáblán.

![A Power BI-jelentések](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Következő lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) Power bi szolgáltatásba exportálhatók lekérdezések összeállításához.
* További információ [Power BI](http://powerbi.microsoft.com) Naplóelemzési export alapuló képi megjelenítések létrehozásához.
