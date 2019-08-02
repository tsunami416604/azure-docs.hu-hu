---
title: Az Azure-beli adatüzemek vizuális monitorozása | Microsoft Docs
description: Ismerje meg, hogyan figyelheti vizuálisan az Azure-beli adatüzemeket
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: 53ead1caed47ae442670f0b6bcd54cd84956a759
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720618"
---
# <a name="visually-monitor-azure-data-factories"></a>Az Azure-beli adatüzemek vizuális monitorozása
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

Ebből a rövid útmutatóból megtudhatja, hogyan lehet vizuálisan figyelni Data Factory folyamatokat anélkül, hogy egyetlen sor kódot kellene írnia.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="monitor-data-factory-pipelines"></a>Data Factory folyamatok figyelése

A folyamat-és tevékenység-futtatások egyszerű listanézet-felülettel jeleníthetők meg. Az összes Futtatás a helyi böngésző időzónájában jelenik meg. Módosíthatja az időzónát és az összes dátum és idő mezőt a kijelölt időzónára.  

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
3. Navigáljon a Azure Portal létrehozott adatgyár panelre, és kattintson a "& kezelésének figyelése" csempére, hogy elindítsa a Data Factory vizuális monitorozási élményét.

## <a name="monitor-pipeline-runs"></a>Folyamatfuttatások monitorozása
A listanézetben látható a Data Factory v2 verzióban futó összes folyamat. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. |
| Műveletek | A tevékenységek futtatásához elérhető egyetlen művelet. |
| Futtatás indítása | Folyamat futtatásának kezdési dátuma (hh/nn/éééé, óó: PP: SS AM/PM) |
| Duration | Futtatás időtartama (óó: PP: SS) |
| Kiváltó | Manuális trigger, ütemezett trigger |
| Állapot | Sikertelen, sikeres, folyamatban |
| Paraméterek | Folyamat futtatási paraméterei (név, érték párok) |
| Hiba | Folyamat futási hibája (IF/any) |
| Futtatási azonosító | A folyamat futtatásának azonosítója |

![Folyamatfuttatások monitorozása](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Tevékenységfuttatások monitorozása
A listanézetben láthatók az egyes folyamatokhoz tartozó tevékenység-végrehajtások. Kattintson a " **műveletek"** oszlop alatt a **"tevékenység futtatása** " ikonra az egyes folyamatokhoz tartozó tevékenységek futtatásának megtekintéséhez. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Műveletnév | A folyamaton belüli tevékenység neve. |
| Tevékenységtípus | A tevékenység típusa, például másolás, HDInsightSpark, HDInsightHive stb. |
| Futtatás indítása | Tevékenység futtatásának kezdési dátuma (hh/nn/éééé, óó: PP: SS AM/PM) |
| Duration | Futtatás időtartama (óó: PP: SS) |
| Állapot | Sikertelen, sikeres, folyamatban |
| Input (Bemenet) | A tevékenység bemeneteit leíró JSON-tömb |
| Output | A tevékenység kimeneteit leíró JSON-tömb |
| Hiba | Tevékenység-futtatási hiba (ha/vagy) |

![Tevékenységfuttatások monitorozása](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> A folyamat-és tevékenység-futtatások listájának frissítéséhez kattintson a felül található **frissítés** ikonra. Az automatikus frissítés jelenleg nem támogatott.

![Frissítés](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Válassza ki a figyelni kívánt adatelőállítót
Vigye a kurzort a bal felső sarokban található **Data Factory** ikonra. A "nyíl" ikonra kattintva megtekintheti a figyelni kívánt Azure-előfizetések és adatfeldolgozók listáját.

![Adat-előállító kiválasztása](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>A listanézet konfigurálása

### <a name="apply-rich-ordering-and-filtering"></a>Gazdag rendezés és szűrés alkalmazása

A sorrendi folyamat a következő oszlopok futtatásával fut az indítási és a szűrési folyamat futtatásakor: desc/ASC.

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. A lehetőségek közé tartoznak a gyors szűrők az "elmúlt 24 órában", a "múlt hét", az utolsó 30 nap, vagy egy egyéni dátum időpontjának kiválasztása. |
| Futtatás indítása | Folyamat futtatásának kezdési dátuma (idő) |
| Futtatás állapota | Futtatások állapot szerinti szűrése – sikeres, sikertelen, folyamatban |

![Szűrés](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Oszlopok hozzáadása vagy eltávolítása
Kattintson a jobb gombbal a listanézet fejlécre, és válassza ki azokat az oszlopokat, amelyeket meg szeretne jeleníteni a listanézetban.

![Oszlopok](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Oszlopszélességek igazítása
A listanézet oszlopszélességének növelése és csökkentése az oszlop fejlécének fölé húzva

## <a name="promote-user-properties-to-monitor"></a>A figyelni kívánt felhasználói tulajdonságok előléptetése

Bármely folyamat tevékenység tulajdonságát előléptetheti felhasználói tulajdonságként, így a figyelésre alkalmas entitás lesz. Például előléptetheti a folyamat másolási tevékenységének **forrás** -és **cél** tulajdonságait felhasználói tulajdonságokként. Az **Automatikus létrehozás** lehetőség kiválasztásával a **forrás** és a **cél** felhasználói tulajdonságokat is létrehozhatja egy másolási tevékenységhez.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Felhasználói tulajdonságokként legfeljebb 5 folyamattal rendelkező tevékenység-tulajdonságokat lehet előléptetni.

Miután létrehozta a felhasználói tulajdonságokat, megfigyelheti őket a figyelési lista nézeteiben. Ha a másolási tevékenység forrása egy tábla neve, akkor a forrástábla nevét a tevékenység-futtatási lista nézet oszlopában figyelheti.

![A tevékenység-futtatások listája felhasználói tulajdonságok nélkül](media/monitor-visually/monitor-user-properties-image2.png)

![Oszlopok hozzáadása a felhasználói tulajdonságok tevékenység-futtatási listához](media/monitor-visually/monitor-user-properties-image3.png)

![Tevékenység-futtatási lista a felhasználói tulajdonságok oszlopaival](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Tevékenységek újrafuttatása egy folyamaton belül

Mostantól újra futtathatja a tevékenységeket egy folyamaton belül. Kattintson a **tevékenység futtatása** elemre, és válassza ki a folyamat azon tevékenységét, amelyből a folyamatot újra szeretné futtatni.

![Tevékenységfuttatások megtekintése](media/monitor-visually/rerun-activities-image1.png)

![Tevékenység futtatásának kiválasztása](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Újrafuttatási előzmények megtekintése

A lista nézetben megtekintheti az összes folyamat futtatási előzményeit.

![Előzmények megtekintése](media/monitor-visually/rerun-history-image1.png)

Egy adott folyamat futtatásához is megtekintheti az újrafuttatási előzményeket.

![Folyamat futtatási előzményeinek megtekintése](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-nézetek

Gantt-nézetek használatával gyorsan megjelenítheti a folyamatokat és a tevékenységek futtatását. Megtekintheti a folyamatok Gantt-nézetét a folyamatokban létrehozott jegyzetek/címkék alapján.

![Gantt-diagram](media/monitor-visually/gantt1.png)

![Gantt-diagram megjegyzései](media/monitor-visually/gantt2.png)

A sáv hossza tájékoztatja a folyamat időtartamát. A sávra kattintva további részleteket is megtudhat.

![Gantt-diagram időtartama](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Interaktív túrák
Kattintson a bal alsó sarokban található "információs ikon" elemre, majd az "irányított túrák" elemre kattintva részletes útmutatást kaphat a folyamat és a tevékenységek futtatásának figyeléséhez.

![Interaktív túrák](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Visszajelzés
Kattintson a "visszajelzés" ikonra, hogy visszajelzést küldjön a különböző funkciókról vagy az esetlegesen felmerülő problémákról.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Riasztások

Data Factory a támogatott mérőszámokra vonatkozó riasztásokat is kiemelheti. Válassza a **figyelő-> riasztások & metrikák** lehetőséget a Data Factory figyelő lapon a kezdéshez.

![](media/monitor-visually/alerts01.png)

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Új riasztás létrehozásához kattintson az **új riasztási szabály** elemre.

    ![](media/monitor-visually/alerts02.png)

1.  Adja meg a szabály nevét, és válasszaki a riasztás súlyosságát.

    ![](media/monitor-visually/alerts03.png)

1.  Válassza ki a riasztási feltételeket.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Adja meg a riasztási logikát. Riasztást hozhat létre a kijelölt metrikához az összes folyamathoz és a kapcsolódó tevékenységekhez. Kiválaszthat egy adott tevékenység típusát, a tevékenység nevét, a folyamat nevét vagy a hiba típusát is.

    ![](media/monitor-visually/alerts06.png)

1.  **E-mail/SMS/leküldéses/hangalapú** értesítések konfigurálása a riasztáshoz. Hozzon létre vagy válasszon ki egy meglévő **műveleti csoportot** a riasztási értesítésekhez.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Hozzon létre egy riasztási szabályt.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>További lépések

A folyamatok figyelésével és kezelésével kapcsolatos további információkért lásd: [folyamatok figyelése és kezelése programozott](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) módon.
