---
title: Az Azure Data Factory vizuális figyelése
description: Ismerje meg, hogyan figyelheti vizuálisan az Azure-adatgyárakat
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 85b1d6b532ba11819947558226291e62af6b5119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690935"
---
# <a name="visually-monitor-azure-data-factory"></a>Az Azure Data Factory vizuális figyelése

Miután létrehozott és közzétett egy folyamatot az Azure Data Factoryban, társíthatja azt egy eseményindítóval, vagy manuálisan elindíthategy ad hoc futtatást. Az Azure Data Factory felhasználói élményében az összes folyamat natív módon figyelheti. A figyelési élmény megnyitásához válassza a **Figyelő & kezelése** csempét az Azure [Portal](https://portal.azure.com/)adatgyári paneljén. Ha már az ADF-felhasználói felületen van, kattintson a bal oldali oldalsáv **Monitor** ikonjára.

Minden adat-előállító futtatás a böngésző helyi időzónájában jelenik meg. Ha módosítja az időzónát, az összes dátum-idő mező a kijelölt mezőhöz igazodik.

## <a name="monitor-pipeline-runs"></a>Folyamatfuttatások monitorozása

Az alapértelmezett figyelési nézet a kijelölt időszakban futó folyamatfuttatások listája. A következő oszlopok jelennek meg:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A csővezeték neve |
| Műveletek | A folyamat megtekintését, megszakítását vagy újrafuttatását lehetővé tévő ikonok |
| Indítás futtatása | A csővezeték futtatásának kezdő dátuma és időpontja (HH/DD/ÉÉÉÉ, ÓÓ:MM:SS AM/PM) |
| Időtartam | Futás időtartama (Óó:PP:SS) |
| Által kiváltott | A folyamatot indító eseményindító neve |
| status | **Sikertelen**, **sikerült**, **folyamatban,** **megszakítva**vagy **várólistán** |
| Széljegyzetek | Csővezetékhez társított szűrhető címkék  |
| Paraméterek | A folyamat futtatásának paraméterei (név/értékpárok) |
| Hiba | Ha a folyamat nem sikerült, a futtatási hiba |
| Futtatási azonosító | A folyamatfutás azonosítója |

![Listanézet a figyelési folyamat futtatásaihoz](media/monitor-visually/pipeline-runs.png)

A folyamat- és tevékenységfuttatások listájának frissítéséhez manuálisan kell kiválasztania a **Frissítés** gombot. Az automatikus frissítés jelenleg nem támogatott.

![Frissítés gomb](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Tevékenységfuttatások monitorozása

Az egyes folyamatfuttatások tevékenységfutásának megtekintéséhez válassza a Műveletek oszlopban a **Tevékenységfuttatások megtekintése** ikont. **Actions** A listanézet az egyes folyamatfuttatásoknak megfelelő tevékenységfuttatásokat jeleníti meg.

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Tevékenységnév | A folyamaton belüli tevékenység neve |
| Művelettípus | A tevékenység típusa, például **Másolás**, **ExecuteDataFlow**vagy **AzureMLExecutePipeline** |
| Műveletek | Ikonok, amelyek lehetővé teszik a JSON bemeneti információk, A JSON kimeneti információk vagy a részletes tevékenységspecifikus figyelési élmények megtekintését | 
| Indítás futtatása | A tevékenység futtatásának kezdő dátuma és időpontja (HH/DD/ÉÉÉÉ, ÓÓ:MM:SS AM/PM) |
| Időtartam | Futás időtartama (Óó:PP:SS) |
| status | **Sikertelen**, **sikerült**, **folyamatban**van , vagy **megszakítva** |
| Integrációs futásidejű | Melyik integrációs futásidejű en futott a tevékenység? |
| Felhasználói tulajdonságok | A tevékenység felhasználó által definiált tulajdonságai |
| Hiba | Ha a tevékenység nem sikerült, a futtatási hiba |
| Futtatási azonosító | A tevékenységfuttatás azonosítója |

![Listanézet a tevékenységfuttatások figyeléséhez](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>A figyelésre szolgáló felhasználói tulajdonságok előléptetése

A folyamatfolyamatok tulajdonságát felhasználói tulajdonságként léptetheti elő, hogy az egy figyelt entitássá válik. Például a folyamat ban lévő másolási tevékenység **forrás** és **cél** tulajdonságait felhasználói tulajdonságként reklámozhatja. Válassza az **Automatikus létrehozás** lehetőséget a másolási tevékenység **forrás-** és **célfelhasználói** tulajdonságainak létrehozásához.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Legfeljebb öt folyamattevékenység-tulajdonság ot léptethet elő felhasználói tulajdonságként.

Miután létrehozta a felhasználói tulajdonságokat, figyelheti őket a figyelési lista nézetekben. Ha a másolási tevékenység forrása táblanév, a forrástábla nevét oszlopként figyelheti a listanézetben a tevékenységfuttatások esetében.

![A tevékenység felhasználói tulajdonságok nélkül futtatja a listát](media/monitor-visually/monitor-user-properties-image2.png)

![A felhasználói tulajdonságok oszlopainak hozzáadása a tevékenységfuttatások listájához](media/monitor-visually/monitor-user-properties-image3.png)

![A tevékenység fut lista a felhasználói tulajdonságok oszlopaival](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>A listanézet konfigurálása

### <a name="order-and-filter"></a>Rendelés és szűrés

Ha be van-e kapcsolva, hogy a folyamatfuttatások csökkenő vagy növekvő sorrendben legyenek-e a futtatási kezdési időnek megfelelően. A szűrőfolyamat a következő oszlopok használatával fut:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | Szűrje a folyamat neve alapján. |
| Indítás futtatása |  Határozza meg a megjelenített folyamatfuttatások időtartományát. A beállítások közé tartoznak az **Elmúlt 24 óra**, **A múlt hét**és az **Elmúlt 30 nap** gyorsszűrői, illetve egyéni dátum és idő kiválasztása. |
| Futtatás állapota | A szűrő állapota szerint fut: **Sikeres**, **Sikertelen**, **Várólistán,** **Megszakítva**vagy **Folyamatban**. |
| Széljegyzetek | Szűrés az egyes folyamatokra alkalmazott címkék szerint |
| Futtatás | Annak szűrése, hogy szeretné-e látni az újrafutózált folyamatokat |

![Szűrési lehetőségek](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Oszlopok hozzáadása vagy eltávolítása
Kattintson a jobb gombbal a listanézet fejlécére, és válassza ki a listanézetben megjeleníteni kívánt oszlopokat.

![Oszlopok beállításai](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Oszlopszélesség beállítása
A listanézetoszlop-szélesség növelése és csökkentése az oszlopfejléc fölé egérgombolással.

## <a name="rerun-activities-inside-a-pipeline"></a>Tevékenységek újrafuttatása folyamaton belül

A folyamatokon belül újrafuttathatja a tevékenységeket. Válassza a **Tevékenységfuttatások megtekintése**lehetőséget, majd válassza ki azt a tevékenységet a folyamatban, amelytől a folyamatot újra szeretné futtatni.

![Tevékenységfuttatások megtekintése](media/monitor-visually/rerun-activities-image1.png)

![Tevékenységfuttatás kiválasztása](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Ismétlés sikertelen tevékenységből

Ha egy tevékenység meghibásodik, idővel megjár, vagy megszakad, újra futtathatja a folyamatot a sikertelen tevékenységből, ha a sikertelen tevékenységből való ismétlés lehetőséget **választja.**

![Sikertelen ismétlődő tevékenység](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Ismétlési előzmények megtekintése

Megtekintheti az újrafuttatási előzményeket az összes folyamatfuttatáshoz a listanézetben.

![Előzmények megtekintése](media/monitor-visually/rerun-history-image1.png)

Megtekintheti egy adott folyamat futtatása újrafuttatási előzményeit is.

![Folyamatfuttatás előzményeinek megtekintése](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt nézetek

Gantt-nézetek használatával gyorsan vizualizálhatja a folyamatokat és a tevékenységfuttatásokat.

![Gantt-diagram példa](media/monitor-visually/gantt1.png)

Megtekintheti a Gantt nézet et folyamatonként vagy csoportonként a folyamatokon létrehozott jegyzetek/címkék alapján.

![Gantt-diagram-jegyzetek](media/monitor-visually/gantt2.png)

A sáv hossza tájékoztatja a csővezeték időtartamát. A további részletek megtekintéséhez a sávra is kijelölve.

![Gantt-diagram időtartama](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Vezetett túrák
Válassza az **Információ ikont** a bal alsó sarokban. Ezután válassza **az Irányított túrák** lehetőséget, ha részletes útmutatást szeretne kapni a folyamat és a tevékenységfuttatások figyeléséhez.

![Vezetett túrák](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Riasztások

A Data Factory támogatott metrikákra vonatkozó riasztásokat kaphat. A kezdéshez válassza a > **Riasztások figyelése & metrikák at** a Data Factory figyelési oldalon. **Monitor**

![Adatgyár figyelő lapja](media/monitor-visually/alerts01.png)

A funkció hétperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Új riasztási szabály létrehozásához válassza az **Új riasztási szabályt.**

    ![Új riasztási szabály gomb](media/monitor-visually/alerts02.png)

1.  Adja meg a szabály nevét, és válassza ki a riasztás súlyosságát.

    ![A szabály nevének és súlyosságának mezői](media/monitor-visually/alerts03.png)

1.  Válassza ki a riasztási feltételeket.

    ![A célfeltételek mezője](media/monitor-visually/alerts04.png)

    ![A kritériumok listája](media/monitor-visually/alerts05.png)

1.  Konfigurálja a riasztási logikát. A kiválasztott metrikához riasztást hozhat létre az összes folyamathoz és a megfelelő tevékenységekhez. Kiválaszthat egy adott tevékenységtípust, tevékenységnevet, folyamatnevet vagy hibatípust is.

    ![A riasztási logika konfigurálásának beállításai](media/monitor-visually/alerts06.png)

1.  Konfigurálja az e-mail, SMS, push és hangértesítéseket a riasztáshoz. Hozzon létre egy műveletcsoportot, vagy válasszon egy meglévőt a riasztási értesítésekhez.

    ![Az értesítések konfigurálásának lehetőségei](media/monitor-visually/alerts07.png)

    ![Értesítés hozzáadásának lehetőségei](media/monitor-visually/alerts08.png)

1.  Hozza létre a riasztási szabályt.

    ![Riasztási szabály létrehozásának lehetőségei](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>További lépések

A folyamatok figyeléséről és kezeléséről a [Folyamatok figyelése és kezelése programozott módon](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) című cikkben olvashat.
