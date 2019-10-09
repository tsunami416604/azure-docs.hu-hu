---
title: Az Azure-beli adatüzemek vizuális monitorozása | Microsoft Docs
description: Ismerje meg, hogyan figyelheti vizuálisan az Azure-beli adatüzemeket
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: f7c27bde3806684045bc43f8ff99eefb14c8d04a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029140"
---
# <a name="visually-monitor-azure-data-factories"></a>Az Azure Data Factoryk vizuális monitorozása
Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. A felhőben adatvezérelt munkafolyamatokat hozhat létre az adatáthelyezés és az adatátalakítások előkészítéséhez és automatizálásához. A Azure Data Factory használatával a következőket teheti:

- Különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatokat hozhat létre és ütemezhet.
- Az adatok feldolgozása/átalakítása számítási szolgáltatásokkal, például a Azure HDInsight Hadoop, a Spark, a Azure Data Lake Analytics és a Azure Machine Learning használatával.
- A kimeneti adatokat olyan adattárakban teheti közzé, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

Ebből a rövid útmutatóból megtudhatja, hogyan lehet vizuálisan figyelni Data Factory folyamatokat anélkül, hogy egyetlen sor kódot kellene írnia.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="monitor-data-factory-pipelines"></a>Data Factory folyamatok figyelése

A folyamat-és tevékenység-futtatások egyszerű listanézet-felülettel jeleníthetők meg. Az összes Futtatás a böngésző helyi időzónájában jelenik meg. Ha megváltoztatja az időzónát, az összes dátum/idő mező a kiválasztotthoz van igazítva.  

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. A Data Factory felhasználói felület jelenleg csak a két böngészőben támogatott.
2. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
3. Nyissa meg a Azure Portal a létrehozott adatgyár paneljét. Válassza ki a **figyelő & kezelése** csempét, hogy elindítsa a Data Factory vizuális figyelésének élményét.

## <a name="monitor-pipeline-runs"></a>Folyamatfuttatások monitorozása
A listanézet megjeleníti a Data Factory folyamatokhoz tartozó egyes folyamatokat. Ezek az oszlopok a következők:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve |
| Műveletek | A tevékenységek futtatásához elérhető egyetlen művelet |
| Futtatás indítása | A folyamat futtatásának kezdési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Duration | Futtatás időtartama (óó: PP: SS) |
| Aktiválta: | Manuális trigger vagy ütemezett trigger |
| State | **Sikertelen**, **sikeres**vagy **folyamatban** |
| Paraméterek | A folyamat futtatásának paraméterei (név/érték párok) |
| Hiba | Folyamat futási hibája (ha van) |
| Futtatási azonosító | A folyamat futtatásának azonosítója |

![A figyelési folyamat futtatási listájának nézete](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Tevékenységfuttatások monitorozása
A listanézet azokat a tevékenység-futtatásokat jeleníti meg, amelyek megfelelnek az egyes folyamatoknak. Az egyes folyamatokhoz tartozó tevékenységek futtatásának megtekintéséhez válassza a **műveletek** oszlop alatt a **tevékenység futtatása** ikont. A listanézet a következő oszlopokat tartalmazza:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Tevékenység neve | A folyamaton belüli tevékenység neve |
| Tevékenység típusa | A tevékenység típusa, például **Másolás**, **HDInsightSpark**vagy **HDInsightHive** |
| Futtatás indítása | A tevékenység futtatásának kezdési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Duration | Futtatás időtartama (óó: PP: SS) |
| State | **Sikertelen**, **sikeres**vagy **folyamatban** |
| Input (Bemenet) | A tevékenység bemeneteit leíró JSON-tömb |
| Output | A tevékenység kimeneteit leíró JSON-tömb |
| Hiba | Tevékenység-futtatási hiba (ha van ilyen) |

![Figyelési tevékenység futtatási listájának megjelenítése](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> A folyamat-és tevékenység-futtatások listájának frissítéséhez a felül található **frissítés** gombra kell kattintania. Az automatikus frissítés jelenleg nem támogatott.

![Frissítés gomb](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Válassza ki a figyelni kívánt adatelőállítót
Vigye a kurzort a bal felső sarokban található **Data Factory** ikonra. A nyíl ikonra kattintva megtekintheti a figyelni kívánt Azure-előfizetések és adatüzemek listáját.

![Válassza ki az adatelőállítót](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>A listanézet konfigurálása

### <a name="apply-rich-ordering-and-filtering"></a>Gazdag rendezés és szűrés alkalmazása

A megrendelési folyamat a Futtatás kezdési időpontja szerint csökkenő/növekvő sorrendben fut. A folyamat a következő oszlopok használatával fut:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. A lehetőségek közé tartoznak a gyors szűrők az **elmúlt 24 órában**, a **múlt héten**és az **elmúlt 30 napban**. Vagy válasszon ki egy egyéni dátumot és időpontot. |
| Futtatás indítása | A folyamat futtatásának kezdési dátuma és időpontja. |
| Futtatás állapota | Szűrő futtatása állapot szerint: **Sikeres**, **sikertelen**vagy **folyamatban van**. |

![Szűrési beállítások](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Oszlopok hozzáadása vagy eltávolítása
Kattintson a jobb gombbal a listanézet fejlécre, és válassza ki azokat az oszlopokat, amelyeket meg szeretne jeleníteni a listanézetban.

![Oszlopok beállításai](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Oszlopszélességek igazítása
Növelje és csökkentse a listanézet oszlopszélességét az oszlop fejlécének fölé húzva.

## <a name="promote-user-properties-to-monitor"></a>A figyelni kívánt felhasználói tulajdonságok előléptetése

Bármely folyamat tevékenység tulajdonságát előléptetheti felhasználói tulajdonságként, így a figyelésre alkalmas entitás lesz. Például előléptetheti a folyamat másolási tevékenységének **forrás** -és **cél** tulajdonságait felhasználói tulajdonságokként. Az **Automatikus létrehozás** lehetőség kiválasztásával a **forrás** és a **cél** felhasználói tulajdonságokat is létrehozhatja egy másolási tevékenységhez.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Felhasználói tulajdonságokként legfeljebb öt folyamat tevékenység-tulajdonságokat lehet előléptetni.

Miután létrehozta a felhasználói tulajdonságokat, megfigyelheti őket a figyelési lista nézeteiben. Ha a másolási tevékenység forrása egy tábla neve, akkor a forrástábla neve a tevékenység futtatásához tartozó listanézet oszlopával figyelhető meg.

![A tevékenység-futtatások listája felhasználói tulajdonságok nélkül](media/monitor-visually/monitor-user-properties-image2.png)

![Oszlopok hozzáadása a felhasználói tulajdonságokhoz a tevékenység-futtatási listához](media/monitor-visually/monitor-user-properties-image3.png)

![Tevékenység-futtatási lista a felhasználói tulajdonságok oszlopaival](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Tevékenységek újrafuttatása egy folyamaton belül

Mostantól újra futtathatja a tevékenységeket egy folyamaton belül. Válassza a **tevékenységek megtekintése futtatása**lehetőséget, majd válassza ki azt a tevékenységet a folyamaton belül, amelyből a folyamatot újra szeretné futtatni.

![Tevékenységfuttatások megtekintése](media/monitor-visually/rerun-activities-image1.png)

![Tevékenység futtatásának kiválasztása](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Újrafuttatási előzmények megtekintése

A lista nézetben megtekintheti az összes folyamat futtatási előzményeit.

![Előzmények megtekintése](media/monitor-visually/rerun-history-image1.png)

Egy adott folyamat futtatásához is megtekintheti az újrafuttatási előzményeket.

![Folyamat futtatási előzményeinek megtekintése](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-nézetek

Gantt-nézetek használatával gyorsan megjelenítheti a folyamatokat és a tevékenységek futtatását. Megtekintheti a folyamatok Gantt-nézetét a folyamatokban létrehozott jegyzetek/címkék alapján.

![Példa Gantt-diagramra](media/monitor-visually/gantt1.png)

![Gantt-diagram megjegyzései](media/monitor-visually/gantt2.png)

A sáv hossza tájékoztatja a folyamat időtartamát. A sáv kiválasztásával további részleteket is megtudhat.

![Gantt-diagram időtartama](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Interaktív túrák
Válassza ki az **információs** ikont a bal alsó sarokban. Ezután válassza az **interaktív túrák** lehetőséget, hogy részletes útmutatást kapjon a folyamat és a tevékenységek futtatásának figyeléséhez.

![Interaktív túrák](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Visszajelzés
Válassza a **visszajelzés** ikont, hogy visszajelzést küldjön a különböző funkciókról vagy az esetlegesen felmerülő problémákról.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Riasztások

Data Factory a támogatott mérőszámokra vonatkozó riasztásokat is kiemelheti. A kezdéshez válassza a **figyelő** > **riasztások & metrikák** lehetőséget a Data Factory figyelése lapon.

![A adatgyár-figyelő lapja](media/monitor-visually/alerts01.png)

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Új riasztás létrehozásához válassza az **új riasztási szabály** lehetőséget.

    ![Új riasztási szabály gomb](media/monitor-visually/alerts02.png)

1.  Adja meg a szabály nevét, és válassza ki a riasztás súlyosságát.

    ![A szabály nevének és súlyosságának mezői](media/monitor-visually/alerts03.png)

1.  Válassza ki a riasztási feltételeket.

    ![A célként megadott feltételekhez tartozó mező](media/monitor-visually/alerts04.png)

    ![Feltételek listája](media/monitor-visually/alerts05.png)

1.  Adja meg a riasztási logikát. Riasztást hozhat létre a kijelölt metrikához az összes folyamathoz és a kapcsolódó tevékenységekhez. Kiválaszthat egy adott tevékenység típusát, a tevékenység nevét, a folyamat nevét vagy a hiba típusát is.

    ![A riasztási logika konfigurálásának lehetőségei](media/monitor-visually/alerts06.png)

1.  Az e-mailek, SMS-, leküldéses és hangalapú értesítések konfigurálása a riasztáshoz. Hozzon létre egy műveleti csoportot, vagy válasszon ki egy meglévőt a riasztási értesítésekhez.

    ![Az értesítések konfigurálásának beállításai](media/monitor-visually/alerts07.png)

    ![Értesítések hozzáadásának beállításai](media/monitor-visually/alerts08.png)

1.  Hozzon létre egy riasztási szabályt.

    ![Riasztási szabály létrehozásának lehetőségei](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>További lépések

A folyamatok figyelésével és kezelésével kapcsolatos információkért tekintse meg a [folyamatok programozott figyelése és kezelése](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) című cikket.
