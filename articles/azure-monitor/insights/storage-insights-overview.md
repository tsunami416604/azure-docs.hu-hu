---
title: Azure Storage-szolgáltatások figyelése Azure Monitor a Storage szolgáltatásban (előzetes verzió) | Microsoft Docs
description: Ez a cikk az Azure Storage-fiókokkal kapcsolatos teljesítmény-és kihasználtsági problémák gyors megismerését biztosító Storage-rendszergazdáknak szóló Azure Monitor ismerteti.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: magoedte
ms.openlocfilehash: 1ab49d17ebf10a1ae0277768ddbd1878195d835d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212759"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>A Storage szolgáltatás figyelése Azure Monitor a Storage-ban (előzetes verzió)

A Azure Monitor for Storage (előzetes verzió) az Azure Storage-fiókok átfogó figyelését teszi lehetővé az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. A tárolási kapacitást és a teljesítményt kétféleképpen tekintheti meg, közvetlenül egy Storage-fiókból vagy nézetből megtekintheti Azure Monitor a Storage-fiókok különböző csoportjai között. 

Ennek a cikknek a segítségével megismerheti a Storage-Azure Monitor (előzetes verzió) funkcióit, így a nagy mennyiségű tárolási fiókok állapotáról és teljesítményéről, valamint a hozzáférési pontokra koncentrálhat, valamint a késések diagnosztizálására, a szabályozásra, és a rendelkezésre állással kapcsolatos problémák.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>A Storage Azure Monitor bemutatása (előzetes verzió)

A tapasztalatok megismerése előtt meg kell ismernie, hogyan jeleníti meg és jeleníti meg az információkat. Akár közvetlenül egy Storage-fiókból, akár a Azure Monitorból választja ki a tárolási funkciót, a Storage Azure Monitor egységes felhasználói élményt nyújt. 

A kombinált szolgáltatás a következőket biztosítja:

* A tárolási szolgáltatás vagy az API-művelet állapota alapján a rendelkezésre állás pillanatképét bemutató **méretezési perspektívában** a kihasználtság a tárolási szolgáltatás által fogadott kérelmek teljes számát, valamint az átlagos időpontot jelző késést mutatja. a tárolási szolgáltatás vagy az API-művelet típusa folyamatban van a kérelmek feldolgozásához. A kapacitást blob, fájl, tábla és üzenetsor alapján is megtekintheti.

* Egy adott Storage-fiók **elemzésének részletezése** a problémák diagnosztizálásához, illetve a kategóriánkénti rendelkezésre állás, a teljesítmény, a hibák és a kapacitások részletes elemzéséhez. Bármelyik lehetőség kiválasztásával részletesen áttekintheti a metrikákat.  

* **Testreszabható** , ahol megváltoztathatja, hogy mely metrikákat szeretné megtekinteni, módosítani vagy beállítani a határértékekhez igazított küszöbértékeket, és a mentést saját munkafüzetként. A munkafüzetben található diagramok az Azure-irányítópulton rögzíthetők.  

Ez a funkció nem igényli, hogy bármit engedélyezzen vagy konfiguráljan, a Storage-fiókokból származó tárolási metrikák alapértelmezés szerint lesznek gyűjtve. Ha nem ismeri az Azure Storage-ban elérhető metrikákat, tekintse meg az Azure Storage-metrikák leírását és definícióját az [Azure Storage-metrikák](../../storage/common/storage-metrics-in-azure-monitor.md)áttekintésével.

>[!NOTE]
>A szolgáltatáshoz való hozzáférés díjmentes, és a [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/) lapon leírtak szerint csak az Ön által konfigurált vagy engedélyezett Azure monitor alapvető funkciókért kell fizetnie.

>[!NOTE]
>A tárolóhoz Azure Monitor nem támogatja az [általános célú v1-fiókokat](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Nézet Azure Monitor

Azure Monitor a tranzakció, a késés és a kapacitás részleteit megtekintheti az előfizetésében található több Storage-fiókból, valamint segítséget nyújthat a teljesítmény, a kapacitási problémák és a hibák azonosításához.

Ha szeretné megtekinteni a Storage-fiókok kihasználtságát és rendelkezésre állását az összes előfizetésében, hajtsa végre a következő lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **figyelő** elemet a Azure Portal bal oldali paneljén, majd az **áttekintések** szakaszban válassza a **Storage-fiókok (előzetes verzió)** lehetőséget.

    ![Több Storage-fiók nézet](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Áttekintő munkafüzet

A kiválasztott előfizetéshez tartozó **áttekintő** munkafüzetben a tábla interaktív tárolási metrikákat és szolgáltatás rendelkezésre állási állapotát jeleníti meg legfeljebb 10 Storage-fiókhoz az előfizetésben. Az eredményeket az alábbi legördülő listából kiválasztott beállítások alapján szűrheti:

* **Előfizetések** – csak a Storage-fiókkal rendelkező előfizetések jelennek meg.  

* **Storage-fiókok** – alapértelmezés szerint a 10 Storage-fiók előre ki van választva. Ha a hatókör-választóban az összes vagy több Storage-fiókot választja, a rendszer legfeljebb 200 Storage-fiókot ad vissza. Ha például összesen 573 Storage-fiókkal rendelkezett a kiválasztott három előfizetésben, csak 200-fiókok jelennek meg. 

* **Időtartomány** – alapértelmezés szerint az utolsó 4 órányi információt jeleníti meg a megfelelő beállítások alapján.

A legördülő lista alatti számláló csempe összesíti az előfizetésben lévő Storage-fiókok teljes számát, és azt, hogy hány összeg van kiválasztva. A munkafüzetben szereplő oszlopok feltételes színkódolással vagy intenzitástérképei rendelkeznek a tranzakciós metrikák vagy hibák jelentéséhez. A legmélyebb szín a legmagasabb és a világosabb szín a legalacsonyabb értékeken alapul. A hiba-alapú oszlopokban az érték piros színnel, a metrikus oszlopokhoz pedig kék színű.

Válasszon ki egy értéket az oszlopok **rendelkezésre állása**, a **E2E késése**, a **kiszolgáló késése**és a **tranzakciós hiba típusa/hibái** egy olyan jelentésre, amely megfelel az adott típusú tárolási mérőszámoknak, amelyek megfelelnek a kiválasztott oszlopnak. Storage-fiók. Az egyes kategóriákhoz tartozó munkafüzetekkel kapcsolatos további információkért tekintse meg az alábbi [részletes tárolási munkafüzetek](#detailed-storage-workbooks) szakaszt. 

>[!NOTE]
>A jelentésben megjelenő hibákkal kapcsolatos részletekért tekintse meg a [Válasz típusa sémát](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) , és keressen olyan válaszokat, mint például a **ServerOtherError**, a **ClientOtherError**, a **ClientThrottlingError**. A kiválasztott Storage-fiókoktól függően, ha több mint három típusú hibát jelentettek, az összes többi hiba **más**kategóriába tartozik.

Az alapértelmezett **rendelkezésre állási** küszöbérték:

* Figyelmeztetés – 99%
* Kritikus – 90%

Ha meg szeretné határozni a rendelkezésre állási küszöbértéket a megfigyelés vagy a követelmények eredményei alapján, tekintse át [a rendelkezésre állási küszöbérték módosítása](#modify-the-availability-threshold)című részét. 

### <a name="capacity-workbook"></a>Kapacitás munkafüzet

Válassza ki a **kapacitást** a lap tetején, és megnyílik a **kapacitás** munkafüzet. Azt mutatja, hogy a fiókban használt összes tárterület, valamint a fiókban lévő egyes adatszolgáltatások által használt kapacitás mennyisége a használaton kívüli és a felhasznált tárolók azonosításának megkönnyítésére szolgál.

![Több Storage-fiók kapacitása munkafüzet](./media/storage-insights-overview/storage-account-capacity-02.png) 

A munkafüzet oszlopaihoz feltételes színkódolás vagy intenzitástérképei van, amely kék értékű kapacitási metrikákat jelent. A legmélyebb szín a legmagasabb és a világosabb szín a legalacsonyabb értékeken alapul.

Amikor kiválaszt egy értéket a munkafüzet egyik oszlopában, a Storage-fiók **Kapacitási** munkafüzetét részletezi. A részletezési jelentéssel kapcsolatos további részletekért lásd az alábbi [részletes tárolási munkafüzetek](#detailed-storage-workbooks) szakaszt. 

## <a name="view-from-a-storage-account"></a>Megtekintés egy Storage-fiókból

Azure Monitor for VMs közvetlenül egy Storage-fiókból való elérése:

1. A Azure Portal válassza a Storage-fiókok lehetőséget.

2. A listából válassza ki a Storage-fiókot. A figyelés szakaszban válassza az áttekintések (előzetes verzió) lehetőséget.

    ![Kiválasztott Storage-fiók – Áttekintés lap](./media/storage-insights-overview/storage-account-direct-overview-01.png)

A Storage-fiók **áttekintő** munkafüzetében számos tárolási teljesítménymutató látható, amelyek segítségével gyorsan elemezheti a következőket:

* A tárolási szolgáltatás állapota azonnal kiderül, hogy a vezérlőn kívüli probléma hatással van-e a tárolási szolgáltatásra abban a régióban, amelyre a rendszer telepíti, amely az **Összefoglalás** oszlopban szerepel.

* A tárolási kapacitással, a rendelkezésre állással, a tranzakciókkal és a késéssel kapcsolatos legfontosabb adatokat bemutató interaktív teljesítmény-diagramok.  

* Metrika és állapotjelző csempék kiemelve a szolgáltatás rendelkezésre állását, a tárolási szolgáltatáshoz tartozó tranzakciók teljes számát, a E2E késését és a kiszolgáló késését.

A **hibák**, a **teljesítmény**, a **rendelkezésre állás**és a **kapacitás** egyik gombjának kiválasztásával megnyílik a megfelelő munkafüzet. 

![Kiválasztott Storage-fiók – Áttekintés lap](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Részletes tárolási munkafüzetek

Azt jelzi, hogy az oszlopok **rendelkezésre állása**, a **E2E késése**, a **kiszolgáló késése**és a **tranzakciós hiba típusa/hibái** a több Storage-fiók **áttekintő** munkafüzetében vannak-e megadva, vagy bármelyik gomb kiválasztásával A **hibák**, a **teljesítmény**, a **rendelkezésre állás**és a **kapacitás** egy adott Storage-fiókból származó **áttekintő** munkafüzetből, amelyek mindegyike az adott kategóriára szabott, interaktív tárterülettel kapcsolatos információt szolgáltat.  

* A **rendelkezésre állás** megnyitja a **rendelkezésre állási** munkafüzetet. Megjeleníti az Azure Storage szolgáltatás aktuális állapotát, amely egy tábla, amely a Storage-fiókban definiált adatszolgáltatás által kategorizált egyes objektumok rendelkezésre állási állapotát jeleníti meg egy, a kiválasztott időtartományt jelképező trendvonal és a rendelkezésre állási trend diagram a fiók minden adatszolgáltatása.  

    ![Példa a rendelkezésre állási jelentésre](./media/storage-insights-overview/storage-account-availability-01.png)

* A **E2E késés** és a **kiszolgáló késése** megnyitja a **teljesítmény** munkafüzetet. Olyan összesítő állapotot tartalmaz, amely megjeleníti az E2E késését és a kiszolgáló késését, a E2E és a kiszolgáló késését, valamint egy táblázatot, amely az API által a Storage-fiókban definiált adatszolgáltatás által kategorizált sikeres hívások késését jeleníti meg.

    ![Példa a teljesítmény jelentésére](./media/storage-insights-overview/storage-account-performance-01.png)

* A rácsban felsorolt hibák valamelyikének kiválasztásával nyissa meg a **hiba** munkafüzetet. A jelentés az összes többi ügyféloldali hiba metrikus csempéit jeleníti meg, kivéve a leírt és a sikeres kérelmeket, az ügyfél-szabályozási hibákat, a ClientOtherError attribútumra jellemző tranzakció- **válasz típusú** dimenzió metrikájának teljesítménymutatóját. és két tábla – az **API-név** és a tranzakciók a **Válasz típusa szerint**.

   ![Hiba jelentés – példa](./media/storage-insights-overview/storage-account-failures-01.png)

* A **kapacitás** megnyitható a **kapacitás** munkafüzetben. Megjeleníti a tárolóban lévő minden egyes tárolási adatobjektumhoz használt tárterület teljes mennyiségét a csempék és a diagram között, valamint azt, hogy a fiók hány adatobjektumot tárol.  

    ![Kiválasztott Storage-fiók kapacitásának lapja](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>PIN-kód és exportálás

A metrikus szakaszok bármelyikét rögzítheti egy Azure-irányítópultra, ha a szakasz jobb felső sarkában található gombostű ikonra kattint.

![Metrikus szakasz rögzítése az irányítópulton – példa](./media/storage-insights-overview/workbook-pin-example.png)

A többszörös előfizetés és a Storage-fiók **áttekintése** vagy **kapacitása** munkafüzetek támogatják az eredmények Excel-formátumban való exportálását, ha a lefelé mutató nyíl ikonra kattint a gombostű ikontól jobbra.

![Munkafüzet-rács eredményeinek exportálása példa](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Azure Monitor testreszabása tárolóhoz (előzetes verzió)

Ez a szakasz azokat a gyakori forgatókönyveket ismerteti, amelyekkel a munkafüzet szerkeszthető az adatelemzési igények támogatásához:

* A munkafüzet hatóköre az adott előfizetési vagy Storage-fiók (ok) mindig kiválasztásához
* A rács metrikáinak módosítása
* A rendelkezésre állási küszöbérték módosítása
* Szín megjelenítésének módosítása

A testreszabásokat egy egyéni munkafüzetbe menti a rendszer, hogy megakadályozza a közzétett munkafüzet alapértelmezett konfigurációjának felülírását. A munkafüzetek egy erőforráscsoporthoz lesznek mentve, vagy az Ön számára magánjellegű **saját jelentések** szakaszban, vagy a **megosztott jelentések** szakaszban, amely mindenki számára elérhető az erőforráscsoporthoz való hozzáféréssel. Az Egyéni munkafüzet mentése után nyissa meg a munkafüzet-katalógust, és indítsa el.

![A munkafüzet-katalógus indítása a parancssáv alapján](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Előfizetés-vagy Storage-fiók meghatározása

A több előfizetés és a Storage-fiók **áttekintését** , illetve a **kapacitás** -munkafüzetek hatókörét beállíthatja egy adott előfizetéshez vagy a Storage-fiókhoz minden futtatáskor, és végrehajthatja a következő lépéseket.

1. Válassza a **figyelő** lehetőséget a portálon, majd válassza ki a **Storage-fiókok (előzetes verzió)** elemet a bal oldali ablaktáblán.

2. Az **Áttekintés** munkafüzet parancssáv területén válassza a **Szerkesztés**lehetőséget.

3. Válasszon az **előfizetések** legördülő listából egy vagy több olyan előfizetést, amelyet alapértelmezettként szeretne használni. Ne feledje, hogy a munkafüzet összesen 10 előfizetés kiválasztását támogatja.  

4. Válassza ki a **Storage-fiókok** legördülő lista egy vagy több olyan fiókját, amelyet alapértelmezettként szeretne használni. Ne feledje, hogy a munkafüzet összesen 200 Storage-fiók kiválasztását támogatja. 

5. A parancssorból válassza a **Mentés másként** lehetőséget, hogy mentse a munkafüzet másolatát a testreszabott beállításokkal, majd kattintson a **Szerkesztés kész** lehetőségre az olvasási módba való visszatéréshez.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Metrikák és színek módosítása a munkafüzetben

Az előre elkészített munkafüzetek metrikus adatokat tartalmaznak, és lehetősége van a vizualizációk bármelyikének módosítására vagy eltávolítására, valamint a csapat konkrét igényeinek megfelelően testre szabására. 

A példánkban a több előfizetés és a Storage-fiók kapacitása munkafüzettel dolgozunk, és bemutatjuk, hogyan:

* Metrika eltávolítása
* Szín megjelenítésének módosítása

Ugyanazokat a módosításokat hajthatja végre az előre elkészített **hibák**, a **teljesítmény**, a **rendelkezésre állási**és a **Kapacitási** munkafüzetek egyikén is.

1. Válassza a **figyelő** lehetőséget a portálon, majd válassza ki a **Storage-fiókok (előzetes verzió)** elemet a bal oldali ablaktáblán.

2. Válassza **ki a** kapacitást a kapacitás munkafüzetre való váltáshoz és a parancssorból válassza a **Szerkesztés** elemet a parancssorból.

    ![A munkafüzet módosításához válassza a szerkesztés lehetőséget.](./media/storage-insights-overview/workbook-edit-workbook.png)

3. A metrikák szakasz mellett válassza a **Szerkesztés**lehetőséget. 

    ![A kapacitás munkafüzet metrikáinak módosításához válassza a szerkesztés lehetőséget.](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. El fogjuk távolítani a **fiókhoz használt kapacitás idővonalának** oszlopát, ezért a mérőszámok rácson válassza az **oszlop beállításai** lehetőséget.

    ![Oszlopbeállítások szerkesztése](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Az **oszlop beállításainak szerkesztése** panelen válassza az **oszlopok** szakaszban a **Microsoft. Storage/Storageaccounts-Capacity-UsedCapacity Timeline $ lehetőséget. A fiók felhasználta a kapacitás idővonalát $** , és a legördülő lista **oszlopainak megjelenítéséhez** válassza a **rejtett**lehetőséget. 

6. A módosítás véglegesítéhez válassza a **Mentés és bezárás** lehetőséget.

Most változtassuk meg a jelentés kapacitási metrikáinak színtémáját, hogy a zöld helyett a zöld színt használják.

1. Válassza ki az **oszlop beállításait** a metrikák rácsban.

2. Az **oszlop beállításainak szerkesztése** panelen válassza az **oszlopok** szakaszban a **Microsoft. Storage/storageaccounts-Capacity-UsedCapacity $ | Microsoft. Storage/storageaccounts/blobservices-Capacity-BlobCapacity $ | Microsoft. Storage/ storageaccounts/fileservices-Capacity-FileCapacity $ | Microsoft. Storage/storageaccounts/queueservices-Capacity-QueueCapacity $ | Microsoft. Storage/storageaccounts/tableservices-Capacity-TableCapacity $** . A legördülő lista **színpalettáján**válassza a **zöld**lehetőséget.

3. A módosítás véglegesítéhez válassza a **Mentés és bezárás** lehetőséget.

4. A parancssorból válassza a **Mentés másként** lehetőséget, hogy mentse a munkafüzet másolatát a testreszabott beállításokkal, majd kattintson a **Szerkesztés kész** lehetőségre az olvasási módba való visszatéréshez.  

### <a name="modify-the-availability-threshold"></a>A rendelkezésre állási küszöbérték módosítása

Ebben a példában a Storage-fiók kapacitása munkafüzettel dolgozunk, és bemutatjuk, hogyan módosítható a rendelkezésre állási küszöbérték. Alapértelmezés szerint a csempe és a rács jelentési százalékának rendelkezésre állása a 90-as és a 99-os küszöbértéket tartalmazó minimális küszöbértékkel van konfigurálva. A rendelkezésre **állási százalék** minimális küszöbértékét a rendelkezésre állás%-ában, az **API** -k és a 85% közötti értékre változtatjuk, ami azt jelenti, hogy az állapot kritikusra változik, ha a küszöbérték kevesebb, mint 85 százalék. 

1. Válassza ki a **Storage-fiókokat** a portálon, majd válasszon ki egy Storage-fiókot a listából.

2. A bal oldali ablaktáblán válassza az adatok **(előzetes verzió)** lehetőséget.

3. A munkafüzetben válassza a **rendelkezésre állás** lehetőséget a rendelkezésre állási munkafüzetre való áttéréshez, majd válassza a **Szerkesztés** lehetőséget a parancssáv mezőben. 

4. Görgessen le a lap aljára, és a bal oldali oldalon a **rendelkezésre állás az API** -rács mellett lehetőség mellett válassza a **Szerkesztés**lehetőséget.

    ![Rendelkezésre állás szerkesztése az API Name Grid-beállítások alapján](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Válassza az **oszlop beállításai** lehetőséget, majd az **oszlop szerkesztése** panelen az **oszlopok** szakaszban válassza a **rendelkezésre állás (%) lehetőséget. (Küszöbértékek + formázott)** .

6. Módosítsa a **kritikus** állapot értékét a **90** – **85** értékre, majd kattintson a **Mentés és bezárás**gombra.

    ![A rendelkezésre állási küszöbérték értékének módosítása a kritikus állapothoz](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. A parancssorból válassza a **Mentés másként** lehetőséget, hogy mentse a munkafüzet másolatát a testreszabott beállításokkal, majd kattintson a **Szerkesztés kész** lehetőségre az olvasási módba való visszatéréshez.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz a Azure Monitor for Storage (előzetes verzió) használata során felmerülő gyakori problémák diagnosztizálását és hibaelhárítását ismerteti. Az alábbi lista segítségével megkeresheti az adott hibához kapcsolódó információkat.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Teljesítmény-, kapacitás-vagy rendelkezésre állási problémák megoldása

A Azure Monitor for Storage (előzetes verzió) szolgáltatással azonosított tárterülettel kapcsolatos problémák elhárításához tekintse meg az Azure Storage [hibaelhárítási útmutatóját](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance).  

## <a name="next-steps"></a>További lépések

* A [metrikai riasztások](../platform/alerts-metric.md) és a [szolgáltatás állapotára vonatkozó értesítések](../../service-health/alerts-activity-log-service-notifications.md) konfigurálása automatizált riasztások beállításához a problémák észlelése érdekében.

* Ismerkedjen meg a forgatókönyvekkel, amelyek támogatják az új és a meglévő jelentések testreszabását, valamint az [interaktív jelentések Azure monitor-munkafüzetekkel való létrehozását](../app/usage-workbooks.md)ismertető áttekintést.

* Az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és hibaelhárítására vonatkozó részletes útmutató a Storage Analytics és egyéb eszközök használatáról: [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
