---
title: "Power BI-irányítópulton a vehicle állapotát, és ki irányítja az szokásait - Azure |} Microsoft Docs"
description: "A Cortana Intelligence szolgáltatásai segítségével a vehicle állapotát, és ki irányítja a valós idejű és prediktív dcu szokásokat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
ms.openlocfilehash: 39be936520d62cb1c1c28de9bd72f8f489166082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Vehicle telemetriai analytics megoldás sablon Power BI-irányítópulton telepítési utasításokat
Ez **menü** Ez a forgatókönyv a fejezetek mutató hivatkozásokat tartalmaz. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A Vehicle Telemetriai elemzési megoldások hogyan car kereskedők, autó gyártók és biztosítási vállalatok kihasználhatják a Cortana Intelligence ahhoz, hogy a valós idejű képességeit és a vehicle állapotát, és ki irányítja a prediktív elemzések szokásait bővíthető meghajtó továbbfejlesztése területén felhasználói felület, a R & D és marketingkampányok. Ez a dokumentum konfigurálásának a Power BI-jelentéseket és az irányítópult után a megoldást már telepítették az előfizetésében részletes útmutatást tartalmaz. 

## <a name="prerequisites"></a>Előfeltételek
1. Telepítheti a [Telemetriai Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) megoldás  
2. [Telepítse a Microsoft Power BI Desktopba](http://www.microsoft.com/download/details.aspx?id=45331)
3. Egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/). Ha nem rendelkezik Azure-előfizetéssel, ingyenes Azure-előfizetés az első lépései
4. Microsoft Power BI-fiók

## <a name="cortana-intelligence-suite-components"></a>A Cortana Intelligence Suite összetevői
A Vehicle Telemetriai Analytics megoldássablonban részeként a következő Cortana Intelligence szolgáltatások vannak telepítve az előfizetéshez.

* **Az Event Hubs** választásával dolgozhat fel a vehicle telemetriai események több millió az Azure.
* **Stream Analytics** való vehicle egészségügyi valós idejű elemzése és továbbra is fennáll, hogy az adatok a hosszú távú tároló gazdagabb kötegelt elemzéséhez.
* **Gépi tanulás** közüli valós idejű és prediktív dcu kötegfeldolgozási.
* **HDInsight** rendszer elkészítéséhez használja léptékű adatok átalakítása
* **Adat-előállító** vezénylési, ütemezés, erőforrás-kezelés és figyelés, a köteges feldolgozás folyamatának kezeli.

**A Power BI** Ez a megoldás részletes irányítópult ad valós idejű adatok és a prediktív elemzés képi megjelenítések. 

A megoldás az két különböző forrásokból: **vehicle jelek és diagnosztikai adatkészlet szimulált** és **vehicle katalógus**.

A vehicle telematika szimulátor Ez a megoldás részét. Diagnosztikai adatok bocsát ki, és jelzi a vehicle állapota megfelelő, és ki irányítja a minta egy időben. 

A Vehicle katalógus egy hivatkozási adatkészletet tartalmazó VIN az modell-lel

## <a name="power-bi-dashboard-preparation"></a>A Power BI-irányítópulton előkészítése
### <a name="setup-power-bi-real-time-dashboard"></a>A telepítő a Power BI-valós idejű irányítópulton

**A valós idejű irányítópulton alkalmazás** Ha a telepítés befejeződött, kövesse a manuális műveletet utasításokat

* Töltse le a valós idejű irányítópulton alkalmazás RealtimeDashboardApp.zip, és bontsa ki azt.
*  A tömörítetlen mappába nyissa meg az alkalmazás konfigurációs fájljában "RealtimeDashboardApp.exe.config", a név felülírandó appSettings az értékek manuális művelet utasításokat, és mentse a módosításokat az Eventhub, a Blob Storage és a gépi tanulás szolgáltatás kapcsolatokhoz.
* Futtassa az alkalmazást RealtimeDashboardApp.exe. A bejelentkezési ablak előugró ablak, érvényes Power bi hitelesítő adatok megadása és kattintson a **elfogadás** gombra. Ezt követően az alkalmazás futása elindul.

   ![Bejelentkezés a Power bi-hoz](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![A Power BI-irányítópulton engedélyek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* Jelentkezzen be a Power bi webhelyen, és hozza létre a valós idejű irányítópulton.

Most készen áll a Power BI-irányítópultot konfigurálása ahhoz, hogy a valós idejű gazdag megjelenítésekkel és prediktív elemzések a vehicle állapotát, és ki irányítja a szokásokat. A jelentések létrehozása és konfigurálása az Irányítópulton egy órával körülbelül 45 percet vesz igénybe. 

### <a name="configure-power-bi-reports"></a>A Power BI-jelentések konfigurálása
A valós idejű jelentéseket és az irányítópult körülbelül 30-45 percig tarthat a befejezéséhez. Keresse meg a [http://powerbi.com](http://powerbi.com) és a bejelentkezés.

![Bejelentkezés a Power bi-hoz](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Új adatkészlet jön létre a Power bi-ban. Kattintson a **ConnectedCarsRealtime** adatkészlet.

![Be van jelölve csatlakoztatott autók valós idejű adatkészlet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Mentés a üres jelentés használatával **Ctrl + s**.

![Üres jelentés mentése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Adja meg a jelentés neve *Vehicle Telemetriai elemzés valós idejű - jelentések*.

![Adja meg a jelentés neve.](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Valós idejű jelentéseket
Ez a megoldás három valós idejű jelentés áll:

1. A művelet járművekről gyűjtött
2. Karbantartási igénylő járművekről gyűjtött
3. Járművekről gyűjtött egészségügyi statisztikák

Ha szeretné, konfigurálja az összes három valós idejű jelentéseket vagy leállítása után fel, és folytassa a következő szakaszban a kötegelt jelentések konfigurálása. Azt javasoljuk, hogy a három jelentések megjelenítéséhez a teljes elemzéseket a megoldás a valós idejű elérési út létrehozása.  

### <a name="1-vehicles-in-operation"></a>1. A művelet járművekről gyűjtött
Kattintson duplán a **1. oldal** és adjon neki "Műveletben járművekről gyűjtött"  
    ![Autók - csatlakoztatott járművekről gyűjtött műveletben](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Válassza ki **vin** mezőjét a **mezők** , és válassza a típusú képi megjelenítés **"Kártyás"**.  

Kártya képi megjelenítés létrehozása ábrán látható módon.  
    ![Csatlakoztatott autók - válassza vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Válassza ki **Város** és **vin** mezők. Módosíthatja a képi megjelenítés **"Térkép"**. A csomóponthúzási **vin** az értékek területére. A csomóponthúzási **Város** a mezők **jelmagyarázat** területen.   
    ![Csatlakoztatott autók - kártya képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

Válassza ki **formátum** szakasz **képi megjelenítések**, kattintson a **cím** , és módosítsa a **szöveg** való **"járművekről gyűjtött műveletben "városonként**.  
    ![Autók - csatlakoztatott járművekről gyűjtött városonként műveletben](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

Végső képi megjelenítés keres az ábrán látható módon.    
    ![Csatlakoztatott autók - végső képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Válassza ki **Város** és **vin**, a képi megjelenítés típus a **fürtözött oszlopdiagram**. Győződjön meg arról **Város** mezőjét **tengely terület** és **vin** a **érték-terület**  

Rendezés a diagram által **"Vin száma"**  
    ![Csatlakoztatott autók - vin száma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Módosítsa a diagram **cím** való **"Műveletben városonként járművekről gyűjtött"**  

Kattintson a **formátum** területen, majd válassza ki **adatok színek**, kattintson a **"A"** való **összes megjelenítése**  
    ![Csatlakoztatott autók - színek adatok megjelenítése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Egyes város színének módosítása a szín ikonra kattintva.  
    ![Csatlakoztatott autók - módosítás színek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Válassza ki **fürtözött oszlopdiagram** képi megjelenítéseket, a képi megjelenítés húzza **Város** mezőjét **tengely** területen **modell** a **Jelmagyarázat** terület és **vin** a **érték** területen.  
    ![Csatlakoztatott autók - csoportosított oszlopdiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Csatlakoztatott autók - megjelenítése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

Ezen a lapon minden képi megjelenítés átrendezését, az ábrán látható módon.  
    ![Csatlakoztatott autók - képi megjelenítések](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Sikeresen konfigurálta a "Műveletben járművekről gyűjtött" valós idejű jelentést. A következő valós idejű jelentést készít vagy leállás, és konfigurálja az irányítópult lépne. 

### <a name="2-vehicles-requiring-maintenance"></a>2. Karbantartási igénylő járművekről gyűjtött
Kattintson a ![Hozzáadás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) hozzáadása egy új jelentést, nevezze át **"Járművekről gyűjtött igénylő karbantartási"**

![Csatlakoztatott autók - karbantartási igénylő járművekről gyűjtött](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Válassza ki **vin** mezőben, majd a képi megjelenítés típus a **kártya**.  
    ![Csatlakoztatott autók - Vin kártya képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Tudunk adatkészlet "MaintenanceLabel" nevű mező. Ez a mező is rendelkezik értéke "0" vagy "1"." Azt állítja be az Azure Machine Learning modell megoldás részeként üzembe, és integrálva van a valós idejű elérési útja. Az "1" érték azt jelzi, hogy a vehicle igényel karbantartást. 

Hozzáadása egy **oldal szintjének** karbantartási megköveteli, az járművekről gyűjtött adatok jelennek meg a szűrő: 

1. Húzza a **"MaintenanceLabel"** mezőjét a **szint Lapszűrők**.  
   ![Csatlakoztatott autók - oldal szintjének szűrők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. Kattintson a **alapszintű szűrési** menü MaintenanceLabel lap szint szűrő alsó részén található.  
   ![Csatlakoztatott autók - alapvető szűrése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. Állítsa be az szűrő értékét **"1"**    
   ![Csatlakoztatott autók - szűrő értéke](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Válassza ki **fürtözött oszlopdiagram** a képi megjelenítések  
![Csatlakoztatott autók - Vind kártya képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Csatlakoztatott autók - csoportosított oszlopdiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Húzza a mező **modell** történő **tengely** területen **Vin** való **érték** területen. Ezután szűrje a képi megjelenítés által **vin száma**.  Módosítsa a diagram **cím** való **"Járművekről gyűjtött karbantartási igénylő modell"**  

Húzza **vin** a mezők **szín telítettségét** jelen **mezők** ![mezők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) szakasza **aképimegjelenítés**lap  
![Csatlakoztatott autók - szín telítettségét](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Változás **adatok színek** a képi megjelenítéseket **formátum** szakasz  
Módosítsa a minimális színhez: **F2C812**  
A maximális színhez módosítása: **FF6300**  
![Autók - szín módosítások csatlakoztatva](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Csatlakoztatott autók - új színeit](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Válassza ki **fürtözött oszlopdiagram** a képi megjelenítések, húzza **vin** mezőjét a **érték** terület, húzza **Város** mezőjét a **Tengely** területen. Rendezés a diagram által **"Vin száma"**. Módosítsa a diagram **cím** való **"Járművekről gyűjtött városonként karbantartási megkövetelése"**   
![Csatlakoztatott autók - karbantartási városonként igénylő járművekről gyűjtött](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Válassza ki **többsoros kártya** képi megjelenítéseket, a képi megjelenítés húzza **modell** és **vin** azokat a **mezők** területen.  
![Csatlakoztatott autók - többsoros kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

A képi megjelenítés mindegyikét átrendezése, a végső jelentés a következőképpen néz ki:  
![Csatlakoztatott autók - többsoros kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Sikeresen konfigurálta a "Járművekről gyűjtött igénylő karbantartás" valós idejű jelentést. A következő valós idejű jelentést készít vagy leállás, és konfigurálja az irányítópult lépne. 

### <a name="3-vehicles-health-statistics"></a>3. Járművekről gyűjtött egészségügyi statisztikák
Kattintson a ![Hozzáadás](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) adja hozzá az új jelentést, nevezze át, hogy **"Járművekről gyűjtött állapotstatisztika"**  

Válassza ki **mérőműszer** képi megjelenítés a képi megjelenítések, majd húzza a **sebesség** mezőjét a **, minimális érték, maximális érték** területeket.  
![Csatlakoztatott autók - többsoros kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Módosítsa az alapértelmezett összesítése **sebesség** a **terület érték** való **átlagos** 

Módosítsa az alapértelmezett összesítése **sebesség** a **minimális terület** való **minimális**

Módosítsa az alapértelmezett összesítése **sebesség** a **maximális terület** való **maximális**

![Csatlakoztatott autók - többsoros kártya](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Nevezze át a **mérőműszer cím** való **"Átlagos sebesség"** 

![Csatlakoztatott autók - mérőműszer](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Kattintson az üres területen adja hozzá az új képi megjelenítést.  

Hasonlóképpen adja hozzá a **mérőműszer** a **motor olaj átlagos**, **üzemanyag átlagos**, és **átlagos motor mérsékelt**.  

Módosítsa az alapértelmezett összesítés minden mérőműszer megfelelően a fent ismertetett mezők **"Átlagos sebesség"** fel tudja mérni.

![Csatlakoztatott autók - mérőműszer](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést.

Válassza ki **sor- és fürtözött oszlopdiagram** a képi megjelenítések, majd húzza **Város** mezőjét a **megosztott tengely**, húzza **sebesség**, **tirepressure és engineoil mezők** történő **oszlopok értékeinek** terület, az összesítési típus a **átlagos**. 

Húzza a **engineTemperature** mezőjét a **sor értékek** terület, összesítési típus a **átlagos**. 

![Csatlakoztatott autók - képi megjelenítések mezők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

A diagram **cím** való **"Átlagos sebességét, kulcsszava nyomás, motor olaj- és motor"**.  

![Csatlakoztatott autók - képi megjelenítések mezők](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést.

Válassza ki **Treemap** képi megjelenítés a képi megjelenítések, húzza a **modell** történő mezőjét a **csoport** területen, majd húzza a mező **MaintenanceProbability** azokat a **értékek** területen.

A diagram **cím** való **"Vehicle modellek karbantartási igénylő"**.

![Csatlakoztatott autók - diagram cím módosítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést.

Válassza ki **100 százalékos halmozott sáv-** a képi megjelenítés, húzza a **Város** történő mezőjét a **tengely** területen, majd húzza a **MaintenanceProbability**, **RecallProbability** a mezők a **érték** területen.

![Csatlakoztatott autók - hozzáadása új képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Kattintson a **formátum**, jelölje be **adatok színek**, és állítsa be a **MaintenanceProbability** értékre szín **"F2C80F"**.

Módosítsa a **cím** a diagram **"Probability a Vehicle karbantartási & visszahívása által város"**.

![Csatlakoztatott autók - hozzáadása új képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést.

Válassza ki **területdiagram** a képi megjelenítés a képi megjelenítések, húzza a **modell** történő mezőjét a **tengely** területen, majd húzza a **engineOil, tirepressure, sebesség és a MaintenanceProbability** a mezők a **értékek** területen. Az összesítési típus a **"Átlagos"**. 

![Csatlakoztatott autók - összesítési típusának módosítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Módosítsa a diagram címét **"Átlagos motor olaj, nyomás, a sebesség és a karbantartás valószínűség megunja modell"**.

![Csatlakoztatott autók - diagram cím módosítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Kattintson az üres területen adja hozzá az új képi megjelenítést:

1. Válassza ki **pontdiagram diagram** képi megjelenítések a képi megjelenítés.
2. Húzza a **modell** történő mezőjét a **részletek** és **jelmagyarázat** területen.
3. Húzza a **üzemanyag** mezőben az a **x tengely** területen módosítsa a összevonása a **átlagos**.
4. Húzza **engineTemparature** történő **y tengely terület**, módosítsa a összevonása a **átlagos**
5. Húzza a **vin** történő mezőjét a **mérete** területen.

![Csatlakoztatott autók - hozzáadása új képi megjelenítés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

A diagram **cím** való **"Üzemanyag átlagok, motor hőmérséklet modell"**.

![Csatlakoztatott autók - diagram cím módosítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

A végső jelentést fog megjelenni, alább látható módon.

![Csatlakoztatott autók végleges jelentés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>PIN-kód képi megjelenítéseket készíthet a jelentésekben, a valós idejű irányítópulton
Hozzon létre egy üres irányítópult irányítópultok mellett a plusz ikonra kattintva. "Vehicle Telemetriai elemzések irányítópultján" is neve

![Csatlakoztatott autók-irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

A képi megjelenítés a fenti jelentésekben az irányítópulton rögzítheti. 

![Csatlakoztatott autók-irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Az irányítópult kell hasonlítania az három jelentések létrehozása és a megfelelő vizuális vannak rögzítve az irányítópulton. Ha nem hozott létre a jelentéseket, az irányítópult másképp sikerült. 

![Csatlakoztatott autók-irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Gratulálunk! Sikeresen létrehozta a valós idejű irányítópulton. Továbbra is CarEventGenerator.exe és RealtimeDashboardApp.exe hajtható végre, mert az élő frissítések meg kell jelennie az irányítópulton. Nagyjából 10 – 15 percet kell vennie az alábbi lépések elvégzéséhez.

## <a name="setup-power-bi-batch-processing-dashboard"></a>A telepítő a Power BI kötegelt feldolgozásra irányítópult
> [!NOTE]
> A végpontok közötti kötegfeldolgozási adatcsatorna végrehajtásának befejeződését, és érdemes év létrehozott adatok feldolgozása körülbelül két órás (az a telepítés sikeres befejezése) vesz igénybe. Ezért Várjon, amíg a feldolgozás a következő lépések végrehajtása előtt. 
> 
> 

**A Power BI designer-fájl letöltése**

* Egy előre konfigurált Power BI designer fájl tartalmazzák a telepítési utasításokat a manuális műveletet a
* Keresse meg a 2. Telepítő Power bi kötegelt feldolgozásra irányítópult letöltheti a Power bi sablon itt nevű kötegfeldolgozási irányítópult **ConnectedCarsPbiReport.pbix**.
* Mentse helyileg

**A Power BI-jelentések konfigurálása**

* Nyissa meg a Tervező fájlt "**ConnectedCarsPbiReport.pbix**" Power BI Desktop használatával. Ha még nem rendelkezik, telepítse a Power BI Desktop a [Power BI Desktop telepítés](http://www.microsoft.com/download/details.aspx?id=45331). 
* Kattintson a **lekérdezések szerkesztése**.

![A Power BI lekérdezés szerkesztése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* Kattintson duplán a **forrás**.

![A Power BI-forrás beállítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* Frissítse a kiszolgáló kapcsolati karakterláncot az Azure SQL-kiszolgálót, amely a központi telepítésének részeként lett kiépítve.  A manuális műveletet utasításokat a hely 

    4. Azure SQL Database
    
    * Server: somethingsrv.database.windows.net
    * Adatbázis: connectedcar
    * Felhasználónév: felhasználónév
    * Jelszó: Kezelheti az SQL server jelszó Azure-portálon

* Hagyja **adatbázis** , *connectedcar*.

![A Power BI-adatbázis beállítása](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* Kattintson az **OK** gombra.
* Látni fogja **Windows hitelesítő adatok** lapon alapértelmezés szerint kiválasztva, módosítsa úgy, hogy **adatbázis-hitelesítő adatok** kattintva **adatbázis** jobb fülre.
* Adja meg a **felhasználónév** és **jelszó** , az Azure SQL Database, a telepítés során megadott.

![Adja meg az adatbázis hitelesítő adatai](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* Kattintson a **csatlakozás**
* Ismételje meg a fenti lépéseket minden olyan jobb oldali ablaktáblában található három fennmaradó lekérdezések, és frissítse az az adatforrás kapcsolati részleteit.
* Kattintson a **zárja be, és betölti**. A Power BI Desktop-fájl adatkészletek SQL Azure Database-táblázatok kapcsolódnak.
* **Bezárás** Power BI Desktop-fájlba.

![Zárja be a Power BI desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* Kattintson a **mentése** gombra a módosítások mentéséhez. 

Ezzel beállította a kötegelt feldolgozásra elérési útjára a megoldás megfelelő jelentéseihez. 

## <a name="upload-to-powerbicom"></a>Töltse fel a *powerbi.com webhelyre*
1. Keresse meg a Power bi-ban webes portál http://powerbi.com és a bejelentkezés.
2. Kattintson a **adatok beolvasása**  
3. A Power BI Desktop-fájl feltöltése.  
4. Töltse fel, kattintson a **adatok beolvasása -> fájlok Get -> helyi fájl**  
5. Keresse meg a **"**ConnectedCarsPbiReport.pbix**"**  
6. A fájl a feltöltést követően nyílik vissza a Power BI munkaterület a.  

A DataSet adatkészlet, jelentést és egy üres irányítópult meg hozható létre.  

Új irányítópult PIN-kód diagramok nevű **Vehicle Telemetriai elemzések irányítópultján** a **Power BI**. A fenti létrehozott üres irányítópult, és keresse meg a **jelentések** szakaszban kattintson az újonnan feltöltött jelentés.  

![Vehicle Telemetriai Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**Vegye figyelembe a jelentés hat lapok:**  
1. oldal: Vehicle sűrűség  
2. lap: Valós idejű vehicle állapota  
3. oldal: Agresszív vezérelt járművekről gyűjtött   
4. lap: Visszaírt járművekről gyűjtött  
Lap 5: Hatékony vezérelt járművekről gyűjtött üzemanyag  
6. lap: Contoso-embléma  

![Csatlakoztatott autók Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**A lap 3**, PIN-kód a következő:  

1. VIN száma  
   ![Csatlakoztatott autók Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. Járművekről gyűjtött agresszív vezérlik modell – diagram vízesés egyes szintjei  
   ![Vehicle Telemetria - PIN-kód diagramok 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Az oldal 5**, PIN-kód a következő: 

1. Vin száma    
   ![Vehicle Telemetria - 5 PIN-kód diagramok](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. Üzemanyag-a hatékony járművekről gyűjtött modell: csoportosított oszlopdiagram  
   ![Vehicle Telemetria - PIN-kód diagramok 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**A lap 4**, PIN-kód a következő:  

1. Vin száma  
   ![Vehicle Telemetria - PIN-kód diagramok 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. Visszaírt járművekről gyűjtött városonként, a modell: Treemap  
   ![Vehicle Telemetria - PIN-kód diagramok 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**A lap 6**, PIN-kód a következő:  

1. Contoso motorok embléma  
   ![Vehicle Telemetria - PIN-kód diagramok 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Az irányítópult rendezése**  

1. Keresse meg az irányítópulton
2. Minden egyes diagram és az alapján, az alábbi képen teljes irányítópult megadott elnevezési átnevezése mutasson. Is helyezheti a diagramok az alábbi irányítópult tűnik.  
   ![Vehicle Telemetria - irányítópult 2 rendszerezése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Vehicle Telemetriai Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Ha a jelentések hozott létre, ahogy azt korábban említettük, a dokumentum, a végső befejezett irányítópult a következő ábra kell hasonlítania. 

![Vehicle Telemetria - irányítópult 2 rendszerezése](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Gratulálunk! Sikeresen létrehozta a jelentések és az irányítópult valós idejű, a prediktív kapnak, és áttekinthetik a vehicle állapotát, és ki irányítja a batch-szokásokat.  
