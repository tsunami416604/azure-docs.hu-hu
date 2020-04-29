---
title: Vizuális monitorozás Azure Data Factory
description: Ismerje meg, hogyan figyelheti vizuálisan az Azure-beli adatüzemeket
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419442"
---
# <a name="visually-monitor-azure-data-factory"></a>Vizuális monitorozás Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Miután létrehozta és közzétett egy folyamatot Azure Data Factoryban, társíthatja egy triggerhez, vagy manuálisan elindíthatja az ad hoc futtatást. Az összes folyamatot a Azure Data Factory felhasználói élményben natív módon figyelheti. A figyelési élmény megnyitásához válassza a **figyelő & kezelés** csempét a [Azure Portal](https://portal.azure.com/)adatgyár paneljén. Ha már szerepel az ADF UX-ben, kattintson a bal oldali oldalsávon látható **figyelés** ikonra.

Az összes adatfeldolgozó-Futtatás a böngésző helyi időzónájában jelenik meg. Ha megváltoztatja az időzónát, az összes dátum/idő mező a kiválasztotthoz van igazítva.

## <a name="monitor-pipeline-runs"></a>Folyamatfuttatások monitorozása

Az alapértelmezett figyelési nézet a folyamat futtatásának listáját tartalmazza a kiválasztott időszakban. A következő oszlopok jelennek meg:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve |
| Műveletek | A folyamat részleteinek megtekintését, megszakítását vagy újrafuttatását lehetővé tevő ikonok |
| Futtatás indítása | A folyamat futtatásának kezdési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Időtartam | Futtatás időtartama (óó: PP: SS) |
| Aktiválta: | A folyamatot elindító trigger neve |
| status | **Sikertelen**, **sikeres**, **folyamatban**, **megszakítva**vagy **várólistán** lévő |
| Széljegyzetek | Egy folyamathoz társított szűrhető Címkék  |
| Paraméterek | A folyamat futtatásának paraméterei (név/érték párok) |
| Hiba | Ha a folyamat nem sikerült, a futtatási hiba |
| Futtatási azonosító | A folyamat futtatásának azonosítója |

![A figyelési folyamat futtatási listájának nézete](media/monitor-visually/pipeline-runs.png)

A folyamat-és tevékenység-futtatások listájának frissítéséhez manuálisan kell kijelölnie a **frissítés** gombot. Az AutoFrissítés jelenleg nem támogatott.

![Frissítés gomb](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Tevékenységfuttatások monitorozása

Ha a tevékenységek futtatását szeretné megtekinteni az egyes folyamatokhoz, válassza a **műveletek** oszlop **Megtekintés tevékenység futtatása** ikonját. A listanézet azokat a tevékenység-futtatásokat jeleníti meg, amelyek megfelelnek az egyes folyamatoknak.

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Tevékenységnév | A folyamaton belüli tevékenység neve |
| Művelettípus | A tevékenység típusa, például **Másolás**, **ExecuteDataFlow**vagy **AzureMLExecutePipeline** |
| Műveletek | Ikonok, amelyekkel megtekintheti a JSON bemeneti adatait, a JSON kimeneti adatait vagy a tevékenységekre vonatkozó részletes figyelési tapasztalatokat | 
| Futtatás indítása | A tevékenység futtatásának kezdési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Időtartam | Futtatás időtartama (óó: PP: SS) |
| status | **Sikertelen**, **sikeres**, **folyamatban**vagy **megszakítva** |
| Integration Runtime | Integration Runtime a tevékenység futtatása |
| Felhasználói tulajdonságok | A tevékenység felhasználó által definiált tulajdonságai |
| Hiba | Ha a tevékenység nem sikerült, a futtatási hiba |
| Futtatási azonosító | A tevékenység futtatásának azonosítója |

![Figyelési tevékenység futtatási listájának megjelenítése](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>A figyelni kívánt felhasználói tulajdonságok előléptetése

Minden folyamat tevékenység tulajdonságának előléptetése felhasználói tulajdonságként, hogy az a figyelő entitás legyen. Például előléptetheti a folyamat másolási tevékenységének **forrás** -és **cél** tulajdonságait felhasználói tulajdonságokként. Válassza az **Automatikus létrehozás** lehetőséget, ha a **forrás** és a **cél** felhasználói tulajdonságokat elő szeretné állítani egy másolási tevékenységhez.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Felhasználói tulajdonságokként legfeljebb öt folyamat tevékenység-tulajdonságokat lehet előléptetni.

Miután létrehozta a felhasználói tulajdonságokat, megfigyelheti őket a figyelési lista nézeteiben. Ha a másolási tevékenység forrása egy tábla neve, akkor a forrástábla neve a tevékenység futtatásához tartozó listanézet oszlopával figyelhető meg.

![A tevékenység-futtatások listája felhasználói tulajdonságok nélkül](media/monitor-visually/monitor-user-properties-image2.png)

![Oszlopok hozzáadása a felhasználói tulajdonságokhoz a tevékenység-futtatási listához](media/monitor-visually/monitor-user-properties-image3.png)

![Tevékenység-futtatási lista a felhasználói tulajdonságok oszlopaival](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>A listanézet konfigurálása

### <a name="order-and-filter"></a>Sorrend és szűrés

Annak a bekapcsolása, hogy a folyamat futása a Futtatás kezdő időpontja szerint csökkenő vagy növekvő legyen. A folyamat a következő oszlopok használatával fut:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | Szűrés a folyamat nevével. |
| Futtatás indítása |  Határozza meg a folyamat futási idejének időtartományát. A lehetőségek közé tartoznak a gyors szűrők az **elmúlt 24 órában**, a **múlt héten**és az **elmúlt 30 napban** , illetve az egyéni dátum és idő kiválasztásához. |
| Futtatás állapota | Szűrő futtatása állapot szerint: **sikeres**, **sikertelen**, **várólistára helyezett**, **megszakított**vagy **folyamatban**. |
| Széljegyzetek | Szűrés az egyes folyamatokra alkalmazott címkék alapján |
| Futtatás | Szűrés, hogy meg szeretné-e jeleníteni a reran-folyamatokat |

![Szűrési beállítások](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Oszlopok hozzáadása vagy eltávolítása
Kattintson a jobb gombbal a listanézet fejlécre, és válassza ki azokat az oszlopokat, amelyeket meg szeretne jeleníteni a listanézetban.

![Oszlopok beállításai](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Oszlopszélességek igazítása
Növelje és csökkentse a listanézet oszlopszélességét az oszlop fejlécének fölé húzva.

## <a name="rerun-activities-inside-a-pipeline"></a>Tevékenységek újrafuttatása egy folyamaton belül

A tevékenységeket egy folyamaton belül futtathatja újra. Válassza a **tevékenységek megtekintése futtatása**lehetőséget, majd válassza ki azt a tevékenységet a folyamaton belül, amelyből a folyamatot újra szeretné futtatni.

![Tevékenységfuttatások megtekintése](media/monitor-visually/rerun-activities-image1.png)

![Tevékenység futtatásának kiválasztása](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Újrafuttatás sikertelen tevékenységből

Ha egy tevékenység meghiúsul, időtúllépés vagy megszakítva, a folyamat a **sikertelen tevékenységből**való újrafuttatásával újrafuttathatja a folyamatot.

![Sikertelen művelet újrafuttatása](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Újrafuttatási előzmények megtekintése

A lista nézetben megtekintheti az összes folyamat futtatási előzményeit.

![Előzmények megtekintése](media/monitor-visually/rerun-history-image1.png)

Egy adott folyamat futtatásához is megtekintheti az újrafuttatási előzményeket.

![Folyamat futtatási előzményeinek megtekintése](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-nézetek

Gantt-nézetek használatával gyorsan megjelenítheti a folyamatokat és a tevékenységek futtatását.

![Példa Gantt-diagramra](media/monitor-visually/gantt1.png)

Megtekintheti a folyamatok Gantt-nézetét vagy a folyamatokban létrehozott megjegyzéseket/címkéket.

![Gantt-diagram megjegyzései](media/monitor-visually/gantt2.png)

A sáv hossza tájékoztatja a folyamat időtartamát. A sáv kiválasztásával további részleteket is megtudhat.

![Gantt-diagram időtartama](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Interaktív túrák
Válassza ki az **információs** ikont a bal alsó sarokban. Ezután válassza az **interaktív túrák** lehetőséget, hogy részletes útmutatást kapjon a folyamat és a tevékenységek futtatásának figyeléséhez.

![Interaktív túrák](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Riasztások

Data Factory a támogatott mérőszámokra vonatkozó riasztásokat is kiemelheti. A kezdéshez válassza a riasztások **figyelése** > **& mérőszámok** lehetőséget a Data Factory figyelése lapon.

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
