---
title: A Visual Studio Azure Stream Analytics-eszközök használata
description: Ez a cikk ismerteti az első lépések az Azure Stream Analytics Tools for Visual Studio használatával.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: bcb4302c3a237ec75ff670b322c9d068d8700de9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738513"
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>A Visual Studio Azure Stream Analytics-eszközök használata
A Visual Studio Azure Stream Analytics-eszközökkel is általánosan elérhető. Ezek az eszközök a Stream Analytics-felhasználók a hibaelhárítás, valamint összetett lekérdezéseket írhat gazdagabb élmény engedélyezése, és akár a helyi lekérdezéseket írhat. A Stream Analytics-feladat a Visual Studio-projekt is exportálhatja.

## <a name="introduction"></a>Bevezetés
Ebben az oktatóanyagban elsajátíthatja létrehozására, szerzői, helyi tesztelése, kezelésére és a Stream Analytics-feladatok debug Stream Analytics tools for Visual Studio használatával. A feladat létrehozása után állíthatja be a folyamatos integrációt és a telepítési folyamatot, az Azure-bA a CI/CD Nuget-csomagot a tudnivalókat a további több [Stream Analytics Visual STUDIO eszközök beállításához CI/CD adatcsatorna](stream-analytics-tools-for-visual-studio-cicd.md) cikk.

Ez az oktatóanyag befejezése után fogja tudni:

* Ismerkedjen meg a Stream Analytics tools for Visual Studio.
* Konfigurálhatja és telepítheti a Stream Analytics-feladat.
* A tesztfeladat helyileg helyi mintaadatokkal.
* A figyelés segítségével problémák elhárításához.
* Projektek exportálni a meglévő feladatokat.

>[!IMPORTANT]
> A Microsoft javasolja, hogy a frissítés az Azure Data Lake Tools 2.3.3000.4 Visual Studio-verzió vagy újabb. A korábbi verziók már nincs elérhető letöltésre és mára elavult. 
>
>**Mi a teendőm?**
>
>1. Ellenőrizze, hogy ha a Visual Studio egy korábbi, mint az Azure Stream Analytics eszközök 2.3.3000.5 használja. 
>   
>   ![Az eszköz verziószámának ellenőrzése](./media/stream-analytics-tools-for-vs/about-data-lake.png)
> 
>2. Ha 2.3.3000.5 egy korábbi verziója, frissítse az Azure Data Lake Tools Visual Studio a letöltőközpontból érhetők el: 
>    - [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 és 2015](https://www.microsoft.com/en-us/download/details.aspx?id=54630)

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek teljesülését, az oktatóanyag teljesítéséhez szüksége:

* Befejezés legfeljebb "Stream Analytics-feladat létrehozása" lépéseit az oktatóanyag [egy IoT-megoldás létrehozása a Stream Analytics segítségével](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* A Visual Studio 2017, a Visual Studio 2015-öt vagy a Visual Studio 2013 Update 4 telepítése. Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Kövesse a [telepítési utasításokat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) Stream Analytics-eszközök telepítése a Visual Studio.

## <a name="create-a-stream-analytics-project"></a>A Stream Analytics-projekt létrehozása
A Visual Studio válassza **fájl** > **új projekt**. A bal oldali sablonok listáján válassza ki a **Stream Analytics**, majd válassza ki **Azure Stream Analytics alkalmazás**.
A lap alján, adjon meg a projekt **neve**, **hely**, és **megoldásnév** más projektek a.

![Új projekt létrehozása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

A projekt **téren** keletkezett **Megoldáskezelőben**.

![Téren projektben a Megoldáskezelőre](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Válassza ki a megfelelő előfizetés
1. Az a **nézet** menüjében válassza **Server Explorer** a Visual Studióban.

2. Jelentkezzen be az Azure-fiókjával. 

## <a name="define-input-sources"></a>Adja meg a bemeneti források
1. A **Megoldáskezelőben**, bontsa ki a **bemenetek** csomópont, és nevezze át **Input.json** való **EntryStream.json**. Kattintson duplán a **EntryStream.json**.

2. A **bemeneti Alias**, adja meg **EntryStream**. Vegye figyelembe, hogy szolgál, hogy a bemeneti áljel lekérdezés parancsfájl.

3. A **forrástípus**, jelölje be **adatfolyam**.

4. A **forrás**, jelölje be **Eseményközpont**.

5. A **Service Bus Namespace**, jelölje be a **TollData** lehetőséget a legördülő listában.

6. A **Eseményközpont nevét**, jelölje be **bejegyzés**.

7. A **Eseményközpont házirend neve**, jelölje be **RootManageSharedAccessKey** (az alapértelmezett érték).

8. A **esemény szerializálási formátum**, jelölje be **Json**, és a **kódolás**, jelölje be **UTF8**.
   
   A beállítások néznek ki:
   
   ![A bemeneti beállítások](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. A lap alján válassza **mentése** a varázsló befejezéséhez. Most már egy másik bemeneti forrás a kilépési adatfolyam létrehozására is hozzáadhat. Kattintson a jobb gombbal a **bemenetek** csomópontját, és válassza **új elem**.
   
   ![Új elem](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Az előugró ablakban válasszon **Stream Analytics bemeneti**, és módosítsa a **neve** való **ExitStream.json**. Válassza a **Hozzáadás** lehetőséget.
   
    ![Új elem hozzáadása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Kattintson duplán a **ExitStream.json** a projektet, és ugyanazt a mezők kitöltése bejegyzés adatfolyamként lépések végrehajtásával. A **Eseményközpont neveként**, írja be **kilépéshez**, az alábbi képernyőfelvételen látható módon:
   
    ![ExitStream beállítások](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Most már definiált két bemeneti adatfolyamot.
   
   ![Két bemenetét](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Ezután adja hozzá a car regisztrációs adatokat tartalmazó blob elérési referenciaadat-bemenetek.
   
12. Kattintson a jobb gombbal a **bemenetek** a projektet, és kövesse ugyanezt a folyamatot az adatfolyam-bemenet csomópontja. A **forrástípus**, jelölje be **referenciaadatok**, és a **bemeneti Alias**, adja meg **regisztrációs**.
   
    ![Regisztrációs beállítások](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Válassza ki a **tárolási** , amely tartalmazza a beállítás a fiók **TollData**. A tároló neve **TollData**, és a **elérési út mintája** van **registration.json**. A fájlnév nagybetűk különbözőnek számítanak, és kisbetűnek kell lennie.

14. Válassza ki **mentése** a varázsló befejezéséhez.

Most már a bemenetek vannak definiálva.

## <a name="define-output"></a>Adja meg a kimeneti
1. A **Megoldáskezelőben**, bontsa ki a **bemenetek** csomópontot, és kattintson duplán **Output.json**.

2. A **kimeneti Alias**, adja meg **kimeneti**. A **gyűjtése**, jelölje be **SQL-adatbázis**.

3. Az a **adatbázis** nevét, adja meg **TollDataDB**.

4. A **felhasználónév**, adja meg **tolladmin**. A **jelszó**, adja meg **123toll!**. A **tábla**, adja meg **TollDataRefJoin**.

5. Kattintson a **Mentés** gombra.

   ![Kimeneti beállításai](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Stream Analytics-lekérdezés
Ez az oktatóanyag megkísérli kérdésekre több üzleti adatok toll kapcsolódó. A Microsoft összeállított lekérdezések vonatkozó lerövidíti a Stream Analytics használható. Az első Stream Analytics-feladat indítása előtt most megismerkedhet egy egyszerű forgatókönyv és a lekérdezés szintaxisa.

### <a name="introduction-to-stream-analytics-query-language"></a>A Stream Analytics lekérdezési nyelv bemutatása
Tegyük fel, hogy szeretné-e, adjon meg egy téren kiállítási gépjárműveket száma. Mivel ez az adatfolyam események folyamatos, meg kell adnia egy adott időn belül. Módosítsa a kérdés, hogy "hogyan különböző adja meg egy téren kiállítási három percenként?" Ez a mérés gyakran nevezik az átfedésmentes száma.

A Stream Analytics-lekérdezés, amely ezt a kérdést megválaszolja vizsgáljuk meg:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Ahogy látja, a Stream Analytics használ, amely mintegy SQL lekérdezésnyelvet. Adja meg a lekérdezés idővel kapcsolatos szempontok néhány bővítmények hozzáadja.

További részletekért olvassa el [felügyeleti idő](https://msdn.microsoft.com/library/azure/mt582045.aspx) és [Ablakozó](https://msdn.microsoft.com/library/azure/dn835019.aspx) MSDN a lekérdezésben használt szerkezeteket tartalmaz.

Most, hogy az első Stream Analytics lekérdezési írt, tesztelje a TollApp mappában, a következő elérési úton található mintaadatfájlok használatával:

**..\TollApp\TollApp\Data**

Ez a mappa a következő fájlokat tartalmazza:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Kérdés: Sűrítéses egy téren kiállítási száma
A projektben kattintson duplán a **Script.asaql** megnyitni a parancsprogramot a szerkesztőben. A parancsfájl az előző szakaszban beillesztése a szerkesztőbe. A lekérdezés-szerkesztő támogatja az IntelliSense zintaxisszínek és egy hibát jelző.

![Lekérdezés-szerkesztő](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>A Stream Analytics lekérdezések helyileg tesztelése
Először a lekérdezést, hogy van-e bármilyen szintaktikai hiba állíthat össze.

1. A mintaadatok lekérdezése érvényesítéséhez használja helyi mintaadatok kattintson a jobb gombbal a bemeneti, majd válassza **adja hozzá a helyi bemeneti**.
   
   ![Helyi bemenet hozzáadása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. Az előugró ablakban jelölje ki a mintaadatok a helyi elérési utat. Kattintson a **Mentés** gombra.
   
   ![Helyi bemenet hozzáadása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Nevű fájl **local_EntryStream.json** automatikusan hozzáadja a bemeneti adatok mappába.
   
   ![Helyi bemeneti mappa fájlok listája](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Válassza ki **futtassa helyileg** a lekérdezés-szerkesztőben. Vagy nyomja le az F5 billentyűt.
   
   ![Helyileg történő futtatása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   A konzol kimeneti a kimeneti elérési úton található. Nyomja le bármelyik billentyűt, nyissa meg az eredményt mappát.
   
   ![Helyi futtatáskor](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Ellenőrizze az eredményeket a helyi mappában.
   
   ![Helyi mappa eredménye](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>A minta bemenet
Is képes az bemeneti adatokat a következő bemeneti forrás a helyi fájlba. Kattintson a jobb gombbal a bemeneti konfigurációs fájlt, és válassza ki **mintaadatok**. 

![Adatmintavétel](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Vegye figyelembe, hogy segítségével mintavételi csak az event hubs vagy IoT-központok most. Más bemeneti forrás nem támogatottak. Töltse ki a helyi útvonalat, ahová a mintaadatok az előugró párbeszédpanelen. Válassza ki **minta**.

![Mintakonfiguráció adatok](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Megjelenik a folyamatjelző a **kimeneti** ablak. 

![Minta kimeneti adatok](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Egy Azure Stream Analytics-lekérdezés elküldése
1. Az a **Lekérdezésszerkesztő**, jelölje be **küldje el az Azure-bA** a parancsfájl-szerkesztőben.

   ![Küldje el az Azure-bA](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Válassza ki **hozzon létre egy új Azure Stream Analytics-feladat**. A **feladatnév**, adja meg **TollApp**. Válassza ki a megfelelő **előfizetés** a legördülő listában. Válassza ki **nyújt**.

   ![Feladat elküldése](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>A feladat indítása
A feladat megtörtént, és automatikusan megnyílik a feladat megtekintése. 
1. Válassza ki a zöld nyílra kattintva indítsa el a feladatot.

   ![Indítsa el a feladat gomb](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Válassza az alapértelmezett beállítást, majd **Start**.
 
   ![Feladat indítása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Láthatja a feladat állapota módosult **futtató**, és a bemeneti/kimeneti események.

   ![Feladat összegzése és a metrikák](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>A Visual Studio ellenőrzésének az eredménye
1. Nyissa meg a Visual Studio Server Explorer, és kattintson a jobb gombbal a **TollDataRefJoin** tábla.

2. Válassza ki **tábla adatok megjelenítése** a feladat eredményének megtekintéséhez.
   
   ![Tábla adatok megjelenítése](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Nézet feladat metrikák
Néhány egyszerű feladatot statisztikák a **feladat metrikák**. 

![Feladat metrikák](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>A feladat a Server Explorer felsorolása
A **Server Explorer**, jelölje be **Stream Analytics-feladatok** majd **frissítése**. A feladat jelenik meg az **Stream Analytics-feladatok**.

![Feladatok listája](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Nyissa meg a feladat megtekintése
A feladat csomópontot, és kattintson rá duplán a **feladatok** csomópont feladat nézet megnyitásához.

![Feladatok megtekintése](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>A projekt egy meglévő feladat exportálása
A projekt exportálhatja egy meglévő feladat két módja van.
* A **Server Explorer**alatt a **Stream Analytics-feladatok** csomópontot, kattintson a jobb gombbal a projekt csomópont. Válassza ki **új Stream Analytics projekt exportálása**.
   
   ![Új Stream Analytics projekt exportálása](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Megjelenik a létrehozott projekt **Megoldáskezelőben**.
   
    ![Solution Explorer feladat](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* A feladatok nézetben válassza ki a **készítése a projekt**.
   
   ![-Projekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások
 
* Helyi tesztelése nem működik, ha a lekérdezés földrajzi térbeli funkciókkal rendelkezik.
* Szerkesztő támogatás nem használható hozzáadása vagy módosítása a JavaScript UDF.
* Helyi tesztelése nem támogatja a mentése JSON formátumban. 
* Támogatás nem érhető el a Power BI-kimenet és a ADLS-kimenet.



## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Ismerkedés az Azure Stream Analytics segítségével](stream-analytics-get-started.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)


