---
title: A Power BI-irányítópult állapotát és a vezetési szokásokat – Azure |} A Microsoft Docs
description: A Cortana Intelligence képességeit használatával valós idejű és prediktív elemzéseket az állapotát és a vezetési szokásokat.
services: machine-learning
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: e6601093577eb9e3dfba4ed27e1e0510cad17de7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421049"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Járműtelemetria Telemetriai Analytics megoldás sablon Power BI-irányítópult beállítási útmutatásai
Ez a menü a fejezetek a forgatókönyv mutató hivatkozásokat tartalmaz: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A jármű-Telemetria Analytics megoldás ezen adása hogyan autó autókereskedők, autó és biztosítási cégek képesek használni a Cortana Intelligence képességeit. Azok állapotát és a vezetési szokásokat javítása a felhasználói élmény, a kutatás és fejlesztés és a marketing jellegű kampányok valós idejű és prediktív elemzéseket szerezheti be. Ezen részletes utasítások alapján bemutatják, hogyan konfigurálhatja a Power BI-jelentések és az irányítópult a megoldás az előfizetésében üzembe helyezése után. 

## <a name="prerequisites"></a>Előfeltételek
* Üzembe helyezése a [Járműtelemetria elemzése](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) megoldás. 
* [Power BI Desktop telepítése](http://www.microsoft.com/download/details.aspx?id=45331).
* Szerezzen be egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/). Ha nem rendelkezik Azure-előfizetéssel, első lépések az ingyenes Azure-előfizetéssel.
* Nyissa meg a Power BI-fiókkal.

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence suite összetevői
A Járműtelemetriai analitikai Megoldássablonját részeként a következő a Cortana Intelligence szolgáltatásai vannak telepítve az előfizetésében:

* **Az Azure Event Hubs** jármű-telemetria-eseményeinek millió feltölti az Azure-bA.
* **Az Azure Stream Analytics** állapotát valós idejű elemzéseket nyújt, és továbbra is fennáll az adatokat pedig hosszú távú tárolással a részletesebb kötegelt feldolgozáshoz.
* **Az Azure Machine Learning** valós időben észleli a rendellenességeket, és révén prediktív előrejelzéseket kínál a kötegelt feldolgozás segítségével.
* **Az Azure HDInsight** alakítja át a nagy mennyiségű adat.
* **Az Azure Data Factory** kezeli a vezénylési, ütemezését, erőforrás-kezelés és a kötegelt feldolgozási folyamat figyelését.

**Power bi-ban** biztosít a megoldás egy informatív Irányítópulton az adatok és a prediktív elemzési vizualizációt. 

A megoldás két különböző adatforrásból használja:

* Szimulált jármű jelek és diagnosztikai adatkészletek
* Járműtelemetria-katalógus

Jármű-telematika szimulátort Ez a megoldás része. Ez bocsát ki a diagnosztikai adatokat, és azt jelzi, hogy időben felel meg a jármű és a vezetési adott állapotát. 

A járműtelemetria-katalógus egy referencia-adatkészlet, amely modellek VINs van leképezve.

## <a name="power-bi-dashboard-preparation"></a>A Power BI irányítópult előkészítése
### <a name="set-up-the-power-bi-real-time-dashboard"></a>A Power BI valós idejű irányítópult beállítása

#### <a name="start-the-real-time-dashboard-application"></a>A valós idejű irányítópult-alkalmazás elindításához
Az üzembe helyezés befejezése után kövesse manuális műveletet.

1. Töltse le a valós idejű irányítópult alkalmazás RealtimeDashboardApp.zip, és bontsa ki azt.

1.  A kicsomagolt mappában nyissa meg az alkalmazás konfigurációs fájljában RealtimeDashboardApp.exe.config. Cserélje le a appSettings az Event Hubs, Azure Blob storage és Azure Machine Learning szolgáltatás kapcsolatok azokra az értékekre a manuális műveletet utasítások. Mentse a módosításokat.

1. Futtassa az alkalmazást RealtimeDashboardApp.exe. A bejelentkezési ablakban adja meg a Power BI érvényes hitelesítő adatait. 

   ![A Power BI bejelentkezési ablak](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
1. Válassza ki **fogadja el**. Az alkalmazás elindul.

   ![A Power BI-irányítópult engedélyek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

1. Jelentkezzen be a Power BI-webhelyén, és a egy valós idejű irányítópult létrehozása.

Most már készen áll a Power BI-irányítópult konfigurálása.  

### <a name="configure-power-bi-reports"></a>A Power BI-jelentések konfigurálása
A valós idejű jelentések és az irányítópult nagyjából 30-45 percet igénybe befejezéséhez. 

1. Keresse meg a [Power BI](http://powerbi.com) weblapon, és jelentkezzen be.

    ![A Power BI bejelentkezési oldal](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

1. Egy új adatkészlet jön létre a Power bi-ban. Válassza ki a **ConnectedCarsRealtime** adatkészlet.

    ![ConnectedCarsRealtime adatkészlet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

1. Üres jelentés mentése, nyomja le a Ctrl + S.

    ![Üres jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

1. Adja meg a jelentés nevét **jármű Telemetriai Analytics valós idejű - jelentések**.

    ![Jelentés neve](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Valós idejű jelentések
Ez a megoldás három valós idejű jelentések találhatók:

* A művelet járművek
* Karbantartási igénylő járművek
* Járműtelemetria egészségügyi statisztikák

Mindhárom a jelentések is beállíthat, vagy bármelyik szakaszában után állítsa le. Majd folytassa a következő szakaszban, a batch-jelentések konfigurálása. Azt javasoljuk, hogy a megoldás a valós idejű elérési útjának teljes elemzési megjelenítése az összes három jelentések létrehozása.  

### <a name="vehicles-in-operation-report"></a>Járművek művelet jelentés
1. Kattintson duplán a **1. oldal**, és nevezze át **műveletben járművek**.

    ![A művelet járművek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

1. Az a **mezők** lapon jelölje be **vin**. Az a **Vizualizációk** lapon jelölje be a **kártya** vizualizációt.  

    A **kártya** Vizualizáció jön létre az alábbi ábrán látható módon:

    ![Válassza ki a vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

1. Az a **mezők** lapon jelölje be **Város** és **vin**. Az a **Vizualizációk** lapon jelölje be a **térkép** vizualizációt. Húzza **vin** , a **értékek** területen. Húzza **Város** , a **jelmagyarázat** területen. 

    ![Kártyavizualizáció](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

1. Az a **Vizualizációk** lapon jelölje be a **formátum** szakaszban. Válassza ki **cím**, és módosítsa **szöveg** való **járművek városonként művelet**.

    ![Járművek műveletben város szerint](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    A végleges Vizualizáció az alábbi példához hasonlóan néz ki:

    ![Végső Vizualizáció](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

1. Az a **mezők** lapon jelölje be **Város** és **vin**. Az a **Vizualizációk** lapon jelölje be a **csoportosított oszlopdiagramot** vizualizációt. Húzza **Város** , a **tengely** területen. Húzza **vin** , a **érték** területen.

1. A diagram rendezéséhez **vin száma**.

    ![Vin száma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

1. Módosítsa a diagram **cím** való **járművek városonként műveletben**. 

1. Válassza ki a **formátum** szakaszt, és válassza ki **Adatszínek**. Változás **az összes megjelenítése** való **a**.

    ![Adatszínek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

1. Egy adott városban színének módosítása a szín szimbólum kiválasztásával.

    ![Színének módosítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

1. Az a **Vizualizációk** lapon jelölje be a **csoportosított oszlopdiagramot** vizualizációt. Az a **mezők** mezőjébe húzza **Város** , a **tengely** területen. Húzza **modell** , a **jelmagyarázat** területen. Húzza **vin** , a **érték** területen.

    ![Fürtözött oszlopdiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    A diagram a következő képhez hasonlóan néz ki:

    ![Renderelés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

1. A képi megjelenítések átrendezheti a, hogy az oldal az alábbihoz hasonlóan néz ki:

    ![Irányítópult-Vizualizációk](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Sikeresen konfigurálta a "Járművek a művelet a" jelentés. A következő valós idejű jelentés hozhat létre, vagy megállhat itt, és konfigurálja az irányítópulton. 

### <a name="vehicles-requiring-maintenance-report"></a>Járművek igénylő karbantartási jelentés

1. Válassza ki ![Hozzáadás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) hozzáadása egy új jelentést. Nevezze át az **járművek igénylő karbantartási**.

    ![Karbantartási igénylő járművek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

1. Az a **mezők** lapon jelölje be **vin**. Az a **Vizualizációk** lapon jelölje be a **kártya** vizualizációt.

    ![Vin kártyavizualizáció](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Az adathalmazban mezőnév **MaintenanceLabel**. Ez a mező rendelkezhet értéke "0" vagy "1". Az érték a gépi tanulási modellt, a megoldás részeként üzembe helyezett állítja be. Integrálva van a valós idejű elérési úttal. Az értéket "1" azt jelzi, hogy a jármű karbantartást igényel. 

1. Hozzáadása egy **szintű szűrő** hogy adatokat jelenítsen meg a karbantartási igénylő járművek: 

   a. Húzza a **MaintenanceLabel** mezőt **oldal Látványelemszint szűrői**.
  
      ![Lapszintű szűrők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Alsó részén **oldal szintű szűrők MaintenanceLabel**válassza **alapszintű szűrés**.

      ![Alapszintű szűrés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Állítsa a szűrő értékét **1**.

      ![Szűrőérték](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

1. Az a **Vizualizációk** lapon jelölje be a **csoportosított oszlopdiagramot** vizualizációt. 

    ![Vin kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Fürtözött oszlopdiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

1. Az a **mezők** mezőjébe húzza **modell** , a **tengely** területen. Húzza **vin** , a **érték** területen. Ezután szűrje a Vizualizáció által **vin száma**. Módosítsa a diagram **cím** való **karbantartási igénylő modell által járművek**. 

1. Az a **mezők** ![mezők-image](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) szakaszában a **Vizualizációk** mezőjébe húzza **vin** való **színtelítettség**.

    ![Színtelítettség](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

1. Az a **formátum** területén módosíthatja **Adatszínek** a Vizualizáció: 

    a. Módosítsa a **minimális** színekkel **F2C812**.

    b. Módosítsa a **maximális** színekkel **FF6300**.

    ![Új adatszínek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Az új Vizualizáció színeit keresse meg az alábbi példához hasonlóan:

    ![Új Vizualizáció színeit](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

1. Az a **Vizualizációk** lapon jelölje be **csoportosított oszlopdiagramot**. Húzza **vin** , a **érték** területen. Húzza **Város** , a **tengely** területen. A diagram rendezéséhez **vin száma**. Módosítsa a diagram **cím** való **város által végzett rendszeres karbantartást igénylő járművek**.

    ![Járművek igénylő karbantartási város szerint](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

1. Az a **Vizualizációk** lapon jelölje be a **többsoros kártya** vizualizációt. Húzza **modell** és **vin** , a **mezők** területen.

    ![Többsoros kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

1. Összes Vizualizáció átrendezése, így a kész jelentésnek az alábbihoz hasonlóan néz ki: 

    ![Átrendezett formájára végleges jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Sikeresen konfigurálta a "Járművek igénylő karbantartási" valós idejű jelentés. A következő valós idejű jelentés hozhat létre, vagy megállhat itt, és konfigurálja az irányítópulton. 

### <a name="vehicle-health-statistics-report"></a>Járműtelemetria egészségügyi Szinkronizálásistatisztika-jelentésről

1. Válassza ki ![Hozzáadás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) hozzáadása egy új jelentést. Nevezze át az **járművek egészségügyi statisztikák**. 

1. Az a **Vizualizációk** lapon jelölje be a **mérőműszer** vizualizációt. Húzza **sebesség** , a **érték**, **minimális érték**, és **maximális érték** területeket.

   ![Járművek egészségügyi statisztikák](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

1. Az a **érték** területen módosítsa az alapértelmezett összesítés a **sebesség** való **átlagos**.

1. Az a **minimális érték** területen módosítsa az alapértelmezett összesítés a **sebesség** való **minimális**.

1. Az a **maximális érték** területen módosítsa az alapértelmezett összesítés a **sebesség** való **maximális**.

   ![Sebesség értékek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

1. Nevezze át a **mérőműszer cím** való **sebesség átlagos**.

   ![Kijelző](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.  

    Ehhez hasonlóan hozzáadása egy **mérőműszer** a **motor olaj átlagos**, **ösztönzésének átlagos**, és **motor hőmérsékleti átlag**.  

1. Az alapértelmezett összesítés egyes mérőműszer az előző lépésekben megismert mezőinek módosítsa a **sebesség átlagos** mérőműszer.

    ![További mérőműszer](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.

1. Az a **Vizualizációk** lapon jelölje be a **vonal- és fürtözött oszlopdiagram** vizualizációt. A csomóponthúzási **Város** való **közös tengelyhez**. Húzza **tirepressure**, **engineoil**, és **sebesség** , a **oszlopértékek** területen. Módosítsa az összesítési típusát **átlagos**. 

1. A csomóponthúzási **engineTemperature** , a **Sorértékek** területen. Az az összesítés típusa módosítható **átlagos**. 

    ![Oszlop- és Sorértékek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

1. Módosítsa a diagram **cím** való **átlagos sebességét, kulcsszava nyomás, motor olaj és motor hőmérséklet**.  

    ![Vonal- és fürtözött oszlopdiagram címét](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.

1. Az a **Vizualizációk** lapon jelölje be a **fatérkép-diagram** vizualizációt. Húzza **modell** , a **csoport** területen. Húzza **MaintenanceProbability** , a **értékek** területen.

1. Módosítsa a diagram **cím** való **karbantartási igénylő jármű modellek**.

    ![Fatérkép-diagram címe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.

1. Az a **Vizualizációk** lapon jelölje be a **100 %-ig halmozott sáv diagram** vizualizációt. Húzza **Város** , a **tengely** területen. Húzza **MaintenanceProbability** és **RecallProbability** , a **érték** területen.

    ![Tengely és az érték](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

1. Az a **formátum** szakaszban jelölje be **Adatszínek**. Állítsa be a **MaintenanceProbability** színt, az érték **F2C80F**.

1. Módosítsa a diagram **cím** való **jármű karbantartási valószínűség & városonként visszaírási**.

    ![100 %-ig halmozott sáv diagram címe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.

1. Az a **Vizualizációk** lapon jelölje be a **területdiagram** vizualizációt. Húzza **modell** , a **tengely** területen. Húzza **engineOil**, **tirepressure**, **sebesség**, és **MaintenanceProbability** , a **értékek** terület. Módosítsa az összesítési típusát **átlagos**. 

    ![Összesítés típusa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

1. Módosítsa a diagram **cím** való **motor olaj, kulcsszava nyomás, gyorsabb és karbantartási valószínűség átlagos modell**.

    ![Területre a diagram címe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

1. Válassza ki az üres területen adjon hozzá egy új vizualizációt.

1. Az a **Vizualizációk** lapon jelölje be a **pontdiagram** vizualizációt. Húzza **modell** , a **részletek** és **jelmagyarázat** területeket. Húzza **ösztönzésének** , a **x tengely** területen. Módosítsa az összesítést a **átlagos**. A csomóponthúzási **engineTemperature** , a **y tengely** területen. Módosítsa az összesítést a **átlagos**. Húzza **vin** , a **mérete** területen.

    ![Részletek, jelmagyarázat, tengely és mérete területek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

1. Módosítsa a diagram **cím** való **üzemanyag, engineTemperature átlagát, és a modell által vin száma, átlagos**.

    ![Pontdiagram diagram címe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    A kész jelentésnek az alábbihoz hasonlóan néz ki:

    ![Utolsó jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>A jelentések a valós idejű irányítópulton Vizualizációk rögzítése
1. Hozzon létre egy üres irányítópult melletti a plusz jel kiválasztásával **irányítópultok**. Adja meg a nevét **jármű-Telemetria Analytics-irányítópult**.

    ![Irányítópult plusz jel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

1. Az előző jelentések vizualizációja az irányítópulton rögzítheti. 

    ![Irányítópult rögzítése szimbólum](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Az összes három jelentés rögzítve az irányítópulton, ha az alábbi példához hasonlóan kell kinéznie. Minden jelentés nem hozott létre, ha az irányítópult eltérő lehet. 

    ![A jelentések irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

A valós idejű irányítópult létrehozása sikerült. Végrehajtási CarEventGenerator.exe és RealtimeDashboardApp.exe továbbra is, mivel élő frissítések láthatja az irányítópulton. Az alábbi lépéseket a befejezéséhez körülbelül 10 – 15 percet igénybe vehet.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>A Power BI kötegelt feldolgozási irányítópult beállítása
> [!NOTE]
> A teljes körű kötegelt feldolgozási folyamat végrehajtása befejeződött, és a egy teljes év alatt létrehozott adatok feldolgozásához (a sikeres telepítésről szóló) körülbelül két órás vesz igénybe. Várjon, amíg a feldolgozás befejezését, mielőtt folytatná a következő lépéseket:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>A Power BI designer-fájl letöltése

1. Egy előre beállított entitástervező Power BI-fájl az üzembe helyezési kézi művelet utasítások része. Keressen a "2. Állítsa be a Power bi kötegelt feldolgozási irányítópult."

1. Töltse le a Power bi-ban sablon itt nevű kötegelt feldolgozás irányítópult **ConnectedCarsPbiReport.pbix**.

1. Mentse a fájlt helyileg.

### <a name="configure-power-bi-reports"></a>A Power BI-jelentések konfigurálása

1. Nyissa meg a Tervező fájlt **ConnectedCarsPbiReport.pbix** a Power BI Desktop használatával. Ha még nem rendelkezik azt, telepítse a Power BI Desktop a [Power BI Desktop telepítése](http://www.microsoft.com/download/details.aspx?id=45331) webhelyén.

1. Válassza ki **lekérdezések szerkesztése**.

    ![Lekérdezések szerkesztése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

1. Kattintson duplán a **forrás**.

    ![Forrás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

1. A kiszolgáló kapcsolati karakterlánc frissítése az Azure SQL-kiszolgálóval, amelyek a központi telepítés részeként van kiépítve. Keresse meg a manuális műveletet utasítások az Azure SQL-adatbázis alapján:

    * Server: somethingsrv.database.windows.net
    * Adatbázis: connectedcar
    * Felhasználónév: felhasználónév
    * Jelszó: Az SQL Server-jelszó kezelheti az Azure Portalról.

1. Hagyja **adatbázis** , **connectedcar**.

    ![Adatbázis](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

1. Kattintson az **OK** gombra.

1. A **Windows hitelesítő adat** lapon alapértelmezés szerint van kiválasztva. Módosítsa a következőre **adatbázis-hitelesítő adatok** kiválasztásával a **adatbázis** lap jobb oldalán.

1. Adja meg a **felhasználónév** és **jelszó** , az Azure SQL database, a telepítés során megadott.

    ![Adatbázis-hitelesítő adatok](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

1. Kattintson a **Csatlakozás** gombra.

1. Ismételje meg az előző lépést minden, a jobb oldali ablaktáblában található három fennmaradó lekérdezések. Frissítse az adatforrás kapcsolatának részleteit.

1. Válassza ki **zárja be és betöltése**. A Power BI Desktop-fájl adatkészletek csatlakozik az SQL database-táblák.

1. Válassza ki **bezárásához** gombra kattintva zárja be a Power BI Desktop-fájlt.

    ![Bezárás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

1. Válassza ki **mentése** menti a módosításokat. 

Most már konfigurálta a jelentések, amelyek megfelelnek a kötegelt feldolgozás elérési útja a megoldásban. 

## <a name="upload-to-powerbicom"></a>Töltse fel a powerbi.com webhelyen
1. Nyissa meg a [Power BI webes portál](http://powerbi.com), és jelentkezzen be.

1. Válassza az **Adatok lekérése** lehetőséget.

1. A Power BI Desktop-fájl feltöltése. Válassza ki **adatok lekérése** > **fájlok lekérése** > **helyi fájl**.

1. Lépjen a **ConnectedCarsPbiReport.pbix**.

1. Miután feltöltötte a fájlt, lépjen vissza a Power BI-munkaterület. Egy adatkészletet, jelentést és egy üres irányítópult jönnek létre az Ön számára.  

1. PIN-kód diagramok egy új irányítópulton nevű **jármű-Telemetria Analytics-irányítópult** a Power bi-ban. Válassza ki az üres irányítópult, amely korábban lett létrehozva, és folytassa a a **jelentések** szakaszban. Válassza ki az újonnan feltöltött jelentést.  

    ![Új Power BI-irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    A jelentés hat oldalból áll:

    1. oldal: Jármű sűrűség  
    Lap 2: Valós idejű állapotát  
    3. oldal: Agresszív driven járművek   
    4. oldal: Visszahívott szoftvertermék járművek  
    5. oldal: Ösztönzésének hatékonyan driven járművek  
    6. oldal: Contoso Motors embléma  

    ![Power BI-jelentés hat oldalak](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

1. A **3. oldal**, rögzítheti az alábbi tartalommal:  

    a. **Vin száma**  

   ![Vin lap 3 száma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Járművek agresszív tárolásuk vízesésdiagram-modell** 

   ![3. oldal diagram 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

1. A **5. oldal**, rögzítheti az alábbi tartalommal: 

    a. **Vin száma**

   ![5. oldal diagram 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Modell által tíz járművek: fürtözött oszlopdiagram**

   ![5. oldal diagramra 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

1. A **4. oldal**, rögzítheti az alábbi tartalommal:  

    a. **Vin száma** 

   ![7. 4. oldal diagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Város, szerint visszaírt járművek modell: fatérkép-diagram**

   ![8. 4. oldal diagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

1. A **6. oldal**, rögzítheti az alábbi tartalommal:  

    * **Contoso Motors embléma**

    ![Contoso Motors embléma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Az irányítópult rendszerezése  

1. Nyissa meg az irányítópultot.

1. Minden egyes diagram fölé. Nevezze át az alábbi kész irányítópultot példában megadott elnevezési alapján minden egyes diagram:

   ![Irányítópult-szervezet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
1. Helyezze át a diagramok körülbelül keresse meg az alábbi irányítópult példához hasonlóan:

    ![Átrendezett formájára irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

1. Miután létrehozta az ebben a dokumentumban említett összes jelentést, a végső kész irányítópultot az alábbi példához hasonlóan néz ki: 

   ![Utolsó irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Sikeresen létrehozta a jelentések és az irányítópult csempéire kattintva valós idejű, prediktív, és a batch betekintés állapotát és a vezetési szokásokat.  
