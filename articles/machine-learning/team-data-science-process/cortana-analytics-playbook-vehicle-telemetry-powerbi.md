---
title: "A Power BI-irányítópulton vehicle állapot-és befolyásoló tényezők szokásait - Azure |} Microsoft Docs"
description: "A Cortana Intelligence szolgáltatásai segítségével a vehicle állapotát, és ki irányítja a valós idejű és prediktív dcu szokásokat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 626987ec0648f9e770499b4a48bc4ca2d175d2b4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Vehicle Telemetriai Analytics megoldás sablon Power BI irányítópult telepítési utasításokat
Ez a forgatókönyv a fejezetek menü hivatkozásokat: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A Vehicle Telemetriai elemzési megoldások bővíthető car kereskedők autó gyártók és biztosítási vállalatok használatát a Cortana Intelligence képességeit. Valós idejű és prediktív elemzések a vehicle állapotát befolyásoló tényezők a felhasználói élmény, a kutatás és fejlesztés javítása érdekében a szokásokat, és marketingkampányok is kapnak. Ezek a részletes útmutatók bemutatják, hogyan konfigurálható a Power BI-jelentéseket és az irányítópult után üzembe helyezéséhez az előfizetésben. 

## <a name="prerequisites"></a>Előfeltételek
* Telepítheti a [Vehicle Telemetriai Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) megoldás. 
* [Telepítse a Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Szerezzen be egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/). Ha nem rendelkezik Azure-előfizetéssel, Ismerkedés az Azure ingyenes előfizetés.
* Nyissa meg a Power BI-fiókkal.

## <a name="cortana-intelligence-suite-components"></a>A Cortana Intelligence suite összetevői
A Vehicle Telemetriai Analytics Megoldássablonban részeként a következő Cortana Intelligence szolgáltatások vannak telepítve az előfizetésben:

* **Az Azure Event Hubs** vehicle telemetriai események több millió ingests az Azure.
* **Az Azure Stream Analytics** biztosít a valós idejű elemzése a vehicle állapotát, és továbbra is fennáll, hogy az adatok a hosszú távú tároló gazdagabb kötegelt elemzéséhez.
* **Az Azure Machine Learning** rendellenességeket észleli a valós idejű, és kötegfeldolgozási prediktív elemzések biztosításához használt.
* **Az Azure HDInsight** átalakítja az adatokat a skála.
* **Az Azure Data Factory** vezénylési, ütemezés, erőforrás-kezelés és figyelés, a köteges feldolgozás folyamatának kezeli.

**A Power BI** Ez a megoldás részletes irányítópult ad valós idejű adatok és a prediktív elemzés képi megjelenítések. 

A megoldás két különböző adatforrásból használ:

* Szimulált vehicle jelek és diagnosztikai adatkészletek
* Vehicle katalógus

A vehicle telematika szimulátor Ez a megoldás részét. Általa kibocsátott diagnosztikai információkat, és azt jelzi, hogy időben állapotát, a vehicle és adott vezetői minták megfelelnek. 

A vehicle katalógus olyan referencia adat, amely leképezhető VINs modellek.

## <a name="power-bi-dashboard-preparation"></a>A Power BI irányítópult előkészítése
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Állítsa be a valós idejű Power BI-irányítópulton

#### <a name="start-the-real-time-dashboard-application"></a>A valós idejű irányítópulton alkalmazás indítása
A telepítés befejezése után kövesse kézi művelet.

1. Töltse le a valós idejű irányítópulton alkalmazás RealtimeDashboardApp.zip, és bontsa ki azt.

2.  Nyissa meg a tömörítetlen mappába, az alkalmazás konfigurációs fájlját RealtimeDashboardApp.exe.config. Cserélje le az appSettings Event Hubs, Azure Blob storage és a kézi művelet utasításokat értékkel rendelkező Azure Machine Learning szolgáltatás kapcsolatok. Mentse a módosításokat.

3. Futtassa az alkalmazást RealtimeDashboardApp.exe. A bejelentkezési ablakban adja meg a Power BI érvényes hitelesítő adatokat. 

   ![A Power BI-bejelentkezés ablak](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Válassza ki **elfogadása**. Az alkalmazás elindul.

   ![A Power BI irányítópult engedélyek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Jelentkezzen be a Power BI webhelyén, és hozzon létre egy valós idejű irányítópulton.

Most már készen áll a Power BI-irányítópultot konfigurálásához.  

### <a name="configure-power-bi-reports"></a>A Power BI-jelentések konfigurálása
A valós idejű jelentéseket és az irányítópult körülbelül 30-45 percig tarthat a befejezéséhez. 

1. Keresse meg a [Power BI](http://powerbi.com) weblapot, és jelentkezzen be.

    ![A Power BI bejelentkezési oldal](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Új adatkészlet jön létre a Power bi-ban. Válassza ki a **ConnectedCarsRealtime** adatkészlet.

    ![ConnectedCarsRealtime adatkészlet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Üres jelentés mentése, nyomja le a Ctrl + S.

    ![Üres jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Adja meg a jelentés nevét **Vehicle Telemetriai elemzés valós idejű - jelentések**.

    ![Jelentés neve](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Valós idejű jelentéseket
Ez a megoldás három valós idejű jelentéseket vannak:

* A művelet járművekről gyűjtött
* Karbantartási igénylő járművekről gyűjtött
* Vehicle egészségügyi statisztikák

Beállíthatja, mind a hármat a valós idejű jelentéseket, vagy megszüntetheti a után fel. Majd folytassa a következő szakasszal kötegelt jelentések konfigurálásához. Azt javasoljuk, hogy hozzon létre minden három jelentéseket a megoldás a valós idejű elérési út teljes insights megjelenítéséhez.  

### <a name="vehicles-in-operation-report"></a>A művelet a jelentés járművekről gyűjtött
1. Kattintson duplán a **lap 1**, és adjon neki **műveletben járművekről gyűjtött**.

    ![A művelet járművekről gyűjtött](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. Az a **mezők** lapon jelölje be **vin**. Az a **képi megjelenítések** lapon jelölje be a **kártya** képi megjelenítés.  

    A **kártya** képi megjelenítés létrehozása a következő ábrán látható módon:

    ![Válassza ki a vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

4. Az a **mezők** lapon jelölje be **Város** és **vin**. Az a **képi megjelenítések** lapon jelölje be a **térkép** képi megjelenítés. A csomóponthúzási **vin** való a **értékek** területen. A csomóponthúzási **Város** való a **jelmagyarázat** területen. 

    ![Kártya képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. Az a **képi megjelenítések** lapon jelölje be a **formátum** szakasz. Válassza ki **cím**, és módosítsa **szöveg** való **városonként műveletben járművekről gyűjtött**.

    ![A művelet városonként járművekről gyűjtött](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    A végső képi megjelenítés a következőképpen néz ki:

    ![Végső képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

7. Az a **mezők** lapon jelölje be **Város** és **vin**. Az a **képi megjelenítések** lapon jelölje be a **fürtözött oszlopdiagram** képi megjelenítés. A csomóponthúzási **Város** való a **tengely** területen. A csomóponthúzási **vin** való a **érték** területen.

8. Rendezés a diagram **vin száma**.

    ![Vin száma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. A diagram **cím** való **városonként műveletben járművekről gyűjtött**. 

10. Válassza ki a **formátum** szakaszt, és válassza ki **adatok színek**. Változás **az összes megjelenítése** való **a**.

    ![Adatok színek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Az egyes város színének módosítása a szín jel kiválasztásával.

    ![Szín módosítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

13. Az a **képi megjelenítések** lapon jelölje be a **fürtözött oszlopdiagram** képi megjelenítés. Az a **mezők** lapján húzza **Város** számára a **tengely** területen. A csomóponthúzási **modell** való a **jelmagyarázat** területen. A csomóponthúzási **vin** való a **érték** területen.

    ![Fürtözött oszlopdiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    A diagram az alábbi képen néz ki:

    ![Renderelés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. A képi megjelenítések átrendezését, úgy, hogy, az oldal az alábbihoz hasonlóan:

    ![A képi megjelenítések irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Sikeresen konfigurálta a "Járművekről gyűjtött a művelet" valós idejű jelentést. A következő valós idejű jelentést hozhat létre, vagy leállás, és konfigurálja az irányítópulton. 

### <a name="vehicles-requiring-maintenance-report"></a>Járművekről gyűjtött igénylő karbantartási jelentés

1. Válassza ki ![Hozzáadás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) új jelentés hozzáadásához. Nevezze át **járművekről gyűjtött igénylő karbantartási**.

    ![Karbantartási igénylő járművekről gyűjtött](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. Az a **mezők** lapon jelölje be **vin**. Az a **képi megjelenítések** lapon jelölje be a **kártya** képi megjelenítés.

    ![Vin kártya képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Az adathalmazban nevű mező **MaintenanceLabel**. Ez a mező lehet értéke "0" vagy "1". Az alapérték a gépi tanulási modell, amely ki van építve a megoldás részeként. Integrálva van a valós idejű elérési úttal. "1" érték azt jelzi, hogy a vehicle igényel karbantartást. 

3. Hozzáadása egy **szint Lapszűrő** a karbantartást igénylő járművekről gyűjtött adatok megjelenítése: 

   a. Húzza a **MaintenanceLabel** mezőről **szint Lapszűrők**.
  
      ![Az oldal szintjének szűrők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Alján **lap szint szűrők MaintenanceLabel**, jelölje be **alapszintű szűrési**.

      ![Alapszintű szűrése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Adja meg a szűrő értéket **1**.

      ![Szűrő értéke](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

5. Az a **képi megjelenítések** lapon jelölje be a **fürtözött oszlopdiagram** képi megjelenítés. 

    ![Vin kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Fürtözött oszlopdiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. Az a **mezők** lapján húzza **modell** számára a **tengely** területen. A csomóponthúzási **vin** való a **érték** területen. Ezután szűrje a képi megjelenítés által **vin száma**. A diagram **cím** való **járművekről gyűjtött karbantartási igénylő modell**. 

7. Az a **mezők** ![mezők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) szakasza a **képi megjelenítések** lapján húzza **vin** való **szín telítettségét**.

    ![Szín telítettségét](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. Az a **formátum** területen módosítsa **adatok színek** a képi megjelenítés a: 

    a. Módosítsa a **minimális** a szín **F2C812**.

    b. Módosítsa a **maximális** a szín **FF6300**.

    ![Új adatok színek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Az új színeit az alábbi példában látható:

    ![Új színeit](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

10. Az a **képi megjelenítések** lapon jelölje be **fürtözött oszlopdiagram**. A csomóponthúzási **vin** való a **érték** területen. A csomóponthúzási **Város** való a **tengely** területen. Rendezés a diagram **vin száma**. A diagram **cím** való **járművekről gyűjtött városonként karbantartási igénylő**.

    ![Karbantartási városonként igénylő járművekről gyűjtött](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

12. Az a **képi megjelenítések** lapon jelölje be a **többsoros kártya** képi megjelenítés. A csomóponthúzási **modell** és **vin** való a **mezők** területen.

    ![Többsoros kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. A képi megjelenítések átrendezését, úgy, hogy, a végső jelentés az alábbi példához hasonló: 

    ![Átcsoportosított végső jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Sikeresen konfigurálta a "Járművekről gyűjtött igénylő karbantartás" valós idejű jelentést. A következő valós idejű jelentést hozhat létre, vagy leállás, és konfigurálja az irányítópulton. 

### <a name="vehicle-health-statistics-report"></a>Vehicle állapotfigyelő Szinkronizálásistatisztika-jelentésről

1. Válassza ki ![Hozzáadás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) új jelentés hozzáadásához. Nevezze át **járművekről gyűjtött állapotstatisztika**. 

2. Az a **képi megjelenítések** lapon jelölje be a **mérőműszer** képi megjelenítés. Húzza **sebesség** számára a **érték**, **minimális érték**, és **maximális érték** területeket.

   ![Járművekről gyűjtött egészségügyi statisztikák](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. Az a **érték** területen módosítsa az alapértelmezett összesítése **sebesség** való **átlagos**.

4. Az a **minimális érték** területen módosítsa az alapértelmezett összesítése **sebesség** való **minimális**.

5. Az a **maximális érték** területen módosítsa az alapértelmezett összesítése **sebesség** való **maximális**.

   ![Sebességének értékei](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Nevezze át a **mérőműszer cím** való **sebesség átlagos**.

   ![Kijelző](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.  

    Ezután adjon hozzá egy **mérőműszer** a **motor olaj átlagos**, **üzemanyag átlagos**, és **motor átlagos**.  

8. Módosítsa az alapértelmezett összesítés minden mérőműszer, mint amilyet az előző lépésben a mezők a **sebesség átlagos** fel tudja mérni.

    ![További mérőműszer](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.

10. Az a **képi megjelenítések** lapon jelölje be a **sor- és fürtözött oszlopdiagram** képi megjelenítés. A csomóponthúzási **Város** való **megosztott tengely**. Húzza **tirepressure**, **engineoil**, és **sebesség** számára a **oszlopok értékeinek** területen. Az összesítési típus a **átlagos**. 

11. A csomóponthúzási **engineTemperature** való a **sor értékek** területen. A összesítési típus a **átlagos**. 

    ![Oszlop és sor értékek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. A diagram **cím** való **átlagos sebességét, kulcsszava nyomás, motor olaj- és motor**.  

    ![Vonal- és fürtözött oszlopdiagram cím](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.

14. Az a **képi megjelenítések** lapon jelölje be a **Treemap** képi megjelenítés. A csomóponthúzási **modell** való a **csoport** területen. A csomóponthúzási **MaintenanceProbability** való a **értékek** területen.

15. A diagram **cím** való **karbantartási igénylő Vehicle modellek**.

    ![Treemap cím](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.

17. Az a **képi megjelenítések** lapon jelölje be a **100 százalékos halmozott sáv-** képi megjelenítés. A csomóponthúzási **Város** való a **tengely** területen. A csomóponthúzási **MaintenanceProbability** és **RecallProbability** való a **érték** területen.

    ![Tengely és érték területek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. Az a **formátum** szakaszban jelölje be **adatok színek**. Állítsa be a **MaintenanceProbability** értékre szín **F2C80F**.

19. A diagram **cím** való **Vehicle karbantartási valószínűség & városonként visszaírási**.

    ![100 %-os halmozott sáv diagrammá cím](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.

21. Az a **képi megjelenítések** lapon jelölje be a **területdiagram** képi megjelenítés. A csomóponthúzási **modell** való a **tengely** területen. A csomóponthúzási **engineOil**, **tirepressure**, **sebesség**, és **MaintenanceProbability** való a **értékek** területen. Az összesítési típus a **átlagos**. 

    ![Aggregáció típusa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. A diagram **cím** való **átlagos motor olaj, a terhelés, a sebesség és a karbantartás valószínűség megunja modell**.

    ![Diagramcím terület](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Jelölje ki a hozzáadni az új képi megjelenítés üres területet.

24. Az a **képi megjelenítések** lapon jelölje be a **pontdiagram diagram** képi megjelenítés. A csomóponthúzási **modell** való a **részletek** és **jelmagyarázat** területek. A csomóponthúzási **üzemanyag** számára a **X tengely** területen. Módosítsa a összevonása a **átlagos**. A csomóponthúzási **engineTemperature** számára a **Y tengely** területen. Módosítsa a összevonása a **átlagos**. A csomóponthúzási **vin** való a **mérete** területen.

    ![Részletek, jelmagyarázat, a tengely és a méret területek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. A diagram **cím** való **átlagos üzemanyag, valamint a modell és a modell szerint vin száma engineTemperature átlaga**.

    ![Pontdiagram diagram címét.](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    A végső jelentés a következőképpen néz ki:

    ![Végső jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>PIN-kód képi megjelenítéseket készíthet a jelentésekben, a valós idejű irányítópulton
1. Üres irányítópult létrehozása mellett a plusz jel kiválasztásával **irányítópultok**. Adja meg a nevét **Vehicle Telemetriai elemzések irányítópultján**.

    ![Irányítópult plusz jel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Az előző jelentések az irányítópultot a képi megjelenítéseket rögzítheti. 

    ![PIN-kód szimbólum irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Ha minden három jelentések vannak rögzítve az irányítópulton, az alábbi példához hasonló formában. A jelentések nem hozott létre, ha az irányítópult eltérő lehet. 

    ![Irányítópultot, amelynek jelentései](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Sikeresen létrehozta a valós idejű irányítópulton. Továbbra is CarEventGenerator.exe és RealtimeDashboardApp.exe hajtható végre, mert az irányítópulton látható működés közbeni frissítések. A következő lépések körülbelül 10 – 15 percet befejezéséhez.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Állítsa be a Power BI kötegelt feldolgozásra irányítópult
> [!NOTE]
> A végpont kötegfeldolgozási folyamat végrehajtásának befejeződését, és létrehozott egy év értékelésével feldolgozni (az a telepítés sikeres befejezése) körülbelül két órás szükséges időt. Várjon, amíg a feldolgozás befejeződik, mielőtt továbblép a következő lépéseket. 
> 
> 

### <a name="download-the-power-bi-designer-file"></a>A Power BI designer-fájl letöltése

1. Egy előre konfigurált Power BI designer fájl tartalmazzák a telepítési kézi művelet utasításokat. Keressen a "2. Állítsa be a Power bi kötegelt feldolgozásra irányítópult."

2. Töltse le a Power BI sablon itt nevű kötegfeldolgozási irányítópult **ConnectedCarsPbiReport.pbix**.

3. Mentse a fájlt helyileg.

### <a name="configure-power-bi-reports"></a>A Power BI-jelentések konfigurálása

1. Nyissa meg a Tervező fájlt **ConnectedCarsPbiReport.pbix** a Power BI Desktop használatával. Ha még nem rendelkezik azt, telepítse a Power BI Desktop a a [Power BI Desktop telepítési](http://www.microsoft.com/download/details.aspx?id=45331) webhelyet.

2. Válassza ki **lekérdezések szerkesztése**.

    ![Lekérdezések szerkesztése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Kattintson duplán a **forrás**.

    ![Forrás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Frissítse a kiszolgáló kapcsolati karakterláncot az Azure SQL-kiszolgálót, amely a központi telepítésének részeként lett kiépítve. Keresse meg az Azure SQL adatbázis kézi művelet utasításokat:

    * Server: somethingsrv.database.windows.net
    * Adatbázis: connectedcar
    * Felhasználónév: felhasználónév
    * Jelszó: Az SQL Server jelszó kezelheti az Azure portálról.

5. Hagyja **adatbázis** , **connectedcar**.

    ![Adatbázis](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Kattintson az **OK** gombra.

7. A **Windows hitelesítő adat** lapon alapértelmezettként van beállítva. Módosítsa úgy, hogy **adatbázis-hitelesítő adatok** kiválasztásával a **adatbázis** jobb fülre.

8. Adja meg a **felhasználónév** és **jelszó** az Azure SQL-adatbázis, a telepítés során megadott.

    ![Adatbázis hitelesítő adatai](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Kattintson a **Csatlakozás** gombra.

10. Ismételje meg az előző lépést minden, a jobb oldali ablaktáblában található három fennmaradó lekérdezések. Frissítse az adatforrás kapcsolati részleteit.

11. Válassza ki **zárja be, és betölti**. A Power BI Desktop-fájl adatkészletek SQL adatbázistáblák csatlakoznak.

12. Válassza ki **bezárása** kattintva zárja be a Power BI Desktop-fájlba.

    ![Bezárás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Válassza ki **mentése** menti a módosításokat. 

Ezzel beállította a jelentések, amelyek megfelelnek a kötegfeldolgozási elérési útja a megoldásban. 

## <a name="upload-to-powerbicom"></a>Töltse fel a powerbi.com webhelyre
1. Lépjen a [Power BI web portal](http://powerbi.com), és jelentkezzen be.

2. Válassza ki **adatok**.

3. A Power BI Desktop-fájl feltöltése. Válassza ki **adatok beolvasása** > **fájlok beolvasása** > **helyi fájl**.

4. Ugrás a **ConnectedCarsPbiReport.pbix**.

5. Miután a fájl feltöltése, térjen vissza a Power BI munkaterület. A DataSet adatkészlet, jelentést és egy üres irányítópult jön létre.  

6. Új irányítópult PIN-kód diagramok nevű **Vehicle Telemetriai elemzések irányítópultján** Power BI-ban. Válassza ki a korábban létrejött, és folytassa a üres irányítópult a **jelentések** szakasz. Válassza ki az újonnan feltöltött jelentést.  

    ![Új Power BI-irányítópulton](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    A jelentésben van hat lapok:

    1. oldal: Vehicle sűrűség  
    2. lap: Valós idejű vehicle állapota  
    3. oldal: Agresszív vezérelt járművekről gyűjtött   
    4. lap: Visszaírt járművekről gyűjtött  
    Lap 5: Hatékony vezérelt járművekről gyűjtött üzemanyag  
    6. lap: Contoso motorok embléma  

    ![A Power BI-jelentés hat Pages](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. A **lap 3**, PIN-kód a következőket:  

    a. **Vin száma**  

   ![Vin lap 3 száma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Járművekről gyűjtött agresszív vezérlik modell – diagram vízesés egyes szintjei** 

   ![3. oldal diagram 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. A **lap 5**, PIN-kód a következőket: 

    a. **Vin száma**

   ![Lap 5 diagram 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Tíz járművekről gyűjtött modell: csoportosított oszlopdiagram**

   ![Lap 5 diagram 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. A **lap 4**, PIN-kód a következőket:  

    a. **Vin száma** 

   ![Lap 4 diagram 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Visszaírt járművekről gyűjtött városonként, a modell: Treemap**

   ![Lap 4 diagram 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. A **lap 6**, PIN-kód a következőket:  

    * **Contoso motorok embléma**

    ![Contoso motorok embléma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Az irányítópult rendezése  

1. Nyissa meg az irányítópultot.

2. Minden egyes diagram mutasson. Nevezze át a következő végzett irányítópult példa megadott elnevezési alapján minden egyes diagram:

   ![Irányítópult-szervezet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Helyezze át a diagramok körülbelül keresse meg az alábbi irányítópult példára hasonlít:

    ![Átcsoportosított irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Ebben a dokumentumban említett összes jelentés létrehozása után utolsó befejezett irányítópult néz az alábbi példa: 

   ![Végső irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Sikeresen létrehozta a jelentések és ahhoz, hogy az irányítópult valós idejű, a prediktív, és a kötegelt áttekinthetik a vehicle állapotát, és ki irányítja a szokásokat.  
