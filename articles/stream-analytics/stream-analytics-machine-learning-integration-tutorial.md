---
title: Az Azure Machine Learning az Azure Stream Analytics-integráció
description: Ez a cikk bemutatja, hogyan gyorsan beállíthat egy egyszerű Azure Stream Analytics-feladatot, amely integrálható az Azure Machine Learning, a felhasználó által definiált függvény használatával.
services: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 2169c3a41991b0b49a4324c16ea079f5943fad0b
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685752"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Hangulatelemzés végrehajtása az Azure Stream Analytics és az Azure Machine Learning használatával
Ez a cikk bemutatja, hogyan gyorsan beállíthat egy egyszerű Azure Stream Analytics-feladatot, amely integrálható az Azure Machine Learning. Segítségével elemzési modell Machine Learning vélemények a Cortana Intelligence-katalógusban a streamadatok szöveg elemzése, és döntse el, valós idejű értelmezhetőségi pontszámot. A Cortana Intelligence Suite használatával teszi lehetővé a feladatnak a róluk szóló véleményeket elemzési modellek létrehozásához jainak részleteivel kellene bajlódnia.

Alkalmazhat témák Ez a cikk a például a következő forgatókönyvekre:

* A streamelési Twitter-adatok valós idejű vélemények elemzése.
* A támogatási személyzet Csevegés rögzíti az ügyfél elemzése.
* Fórumok, blogok és videókhoz fűzött megjegyzések kiértékelése. 
* Sok más valós idejű, prediktív pontozási forgatókönyvek.

A való életből vett helyzet számíthat az adatok közvetlenül a Twitter adatstream. Az oktatóanyag leegyszerűsítése írás, hogy a Streaming Analytics-feladat beolvassa a tweeteket CSV-fájlból az Azure Blob storage-ban. Létrehozhat saját CSV-fájl, vagy használhat egy CSV-mintafájlt, az alábbi képen látható módon:

![minta tweeteket egy CSV-fájl](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A Streaming Analytics-feladat által létrehozott alkalmazza a róluk szóló véleményeket elemzési modellek, felhasználó által definiált függvény (UDF) szöveget a mintaadatokat a blob-tárolóból. A kimenet (a hangulatelemzés eredménye) ugyanarra a blob-tároló egy másik CSV-fájl írása. 

Az alábbi ábra ezt a konfigurációt mutatja be. Feljegyzett, ha valószerűbb forgatókönyvet szeretne, Twitter-adatok az Azure Event Hubs bemeneti streamelési is cserélje le a blob storage-bA. Ezen kívül is létrehozhatja a [Microsoft Power BI](https://powerbi.microsoft.com/) összesített vélemények valós idejű vizualizációját.    

![Stream Analytics a Machine Learning-integráció áttekintése](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-előfizetés.
* CSV-fájl, az adatokat. Letöltheti a fájlt a korábban bemutatott [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), vagy létrehozhat egy saját fájlt. Ebben a cikkben azt feltételezzük, hogy a GitHub-fájlt használ.

Magas szinten a feladatokat, ebben a cikkben bemutatott, tegye a következőket:

1. Hozzon létre egy Azure storage-fiók és a egy blob storage-tárolóba, és a egy CSV formátumú bemeneti fájl feltöltése a tárolóba.
3. A Cortana Intelligence-katalógusban a róluk szóló véleményeket elemzési modellek hozzáadása az Azure Machine Learning-munkaterületet, és ez a modell üzembe webszolgáltatásként, amely a Machine Learning-munkaterület.
5. Hozzon létre egy Stream Analytics-feladat, amely meghívja a webszolgáltatás függvényében, hogy megállapítsuk a bemeneti szövegben a róluk szóló véleményeket.
6. Indítsa el a Stream Analytics-feladatot, és a kimenet ellenőrzése.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Hozzon létre egy storage-tárolót, és a fürt megosztott kötetei szolgáltatás bemeneti fájl feltöltése
Ehhez a lépéshez, például a githubról elérhető bármely CSV-fájl is használhatja.

1. Az Azure Portalon kattintson a **erőforrás létrehozása** > **tárolási** > **tárfiók**.

2. Adjon meg egy nevet (`samldemo` a példában). Használhatja, hogy a név csak kisbetűket és számokat, és az Azure-ban egyedinek kell lennie. 

3. Adjon meg egy meglévő erőforráscsoportot, és adjon meg egy helyet. A helyen azt javasoljuk, hogy ebben az oktatóanyagban létrehozott összes erőforrást használja ugyanazt a helyet.

    ![Adja meg a tárfiók részletei](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Az Azure Portalon válassza ki a tárfiókot. A storage-fiók panelén kattintson **tárolók** majd  **+ &nbsp;tároló** létrehozása a blob storage-bA.

    ![blobtároló létrehozása](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Adja meg a tároló nevét (`azuresamldemoblob` a példában), és ellenőrizze, hogy **típus eléréséhez** értékre van állítva **Blob**. Ha végzett, kattintson az **OK** gombra.

    ![Adja meg a blob-tároló részletei](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. Az a **tárolók** panelen jelölje ki az új tárolót, és megnyílik a panel a tároló.

7. Kattintson a **Feltöltés** gombra.

    ![Egy tároló "Feltöltés" gomb](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Az a **blob feltöltése** panelen, a feltöltési a **sampleinput.csv** korábban letöltött fájl. A **Blob típusát**válassza **blokkblob** , és állítsa a blokkméret 4 MB, amely elegendő ehhez az oktatóanyaghoz.

9. Kattintson a **feltöltése** gombra a panel alján.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>A Cortana Intelligence-katalógusban a róluk szóló véleményeket elemzési modellek hozzáadása

Most, hogy a mintaadatokat egy blobba, engedélyezheti a vélemények elemzése modell Cortana Intelligence-katalógusban.

1. Nyissa meg a [vélemények prediktív elemzési modellek](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) lap a Cortana Intelligence-katalógusban.  

2. Kattintson a **Megnyitás a Studióban**.  
   
   ![Stream Analytics Machine Learning, nyissa meg a Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Jelentkezzen be a munkaterületen. Válasszon ki egy helyet.

4. Kattintson a **futtatása** az oldal alján. A folyamat fut, amely körülbelül egy percig tart.

   ![Futtassa a kísérletet a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Miután a folyamat végrehajtása sikeresen befejeződött, válassza ki a **webszolgáltatás üzembe helyezése** az oldal alján.

   ![kísérlet a Machine Learning Studióban üzembe webszolgáltatásként](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Ellenőrizze, hogy a vélemény elemzési modellek használatra kész, kattintson a **teszt** gombra. Adja meg például a "Tetszik a Microsoft" bemeneti szöveg. 

   ![teszt kísérlet a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Ha a teszt működik, az alábbi példához hasonló eredmény jelenik meg:

   ![vizsgálati eredmények, a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Az a **alkalmazások** oszlopot, kattintson a **Excel 2010 vagy korábbi munkafüzet** egy Excel-munkafüzet letöltése hivatkozásra. A munkafüzet tartalmaz, az API-kulcsot és az URL-címet, amelyekre később szüksége a Stream Analytics-feladat beállításához.

    ![Stream Analytics Machine Learning, gyors áttekintés](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Hozzon létre egy Stream Analytics-feladat, amely a Machine Learning-modell

Mostantól létrehozhat egy Stream Analytics-feladatot, amely a minta tweetek beolvassa a blob storage-ban CSV-fájlból. 

### <a name="create-the-job"></a>A feladat létrehozása

1. Nyissa meg az [Azure Portal](https://portal.azure.com).  

2. Kattintson a **erőforrás létrehozása** > **IOT-** > **Stream Analytics-feladat**. 

3. Nevezze el a feladat `azure-sa-ml-demo`, adja meg az előfizetés, adjon meg egy meglévő erőforráscsoportot vagy hozzon létre egy új, és válassza ki a feladat helyét.

   ![új Stream Analytics-feladat beállításainak megadása](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>A feladat bemenetének konfigurálásához
A feladat lekéri a bemeneti korábban a blobtárolóba feltöltött CSV-fájlból.

1. A feladat létrehozását követően, a **Feladattopológia** feladat panelen kattintson a **bemenetek** lehetőséget.    

2. Az a **bemenetek** panelen kattintson a **Stream bemenet hozzáadása** >**Blob storage-ban**

3. Töltse ki a **Blob Storage** panel ezekkel az értékekkel:

   
   |Mező  |Érték  |
   |---------|---------|
   |**Bemeneti áljel** | A nevet használja `datainput` válassza **válassza ki a blob storage az előfizetésből**       |
   |**Storage-fiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Tároló**  | Válassza ki a korábban létrehozott tárolót (`azuresamldemoblob`)        |
   |**Eseményszerializációs formátum**  |  Válassza ki **CSV**       |

   ![Új feladat bemenete beállításai](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-the-job-output"></a>Feladat kimenetének konfigurálása
A feladat küldése eredmények ugyanezen a blobtárolón belül hol lekérdezi a bemeneti. 

1. A **Feladattopológia** feladat panelen kattintson a **kimenetek** lehetőséget.  

2. Az a **kimenetek** panelen kattintson a **Hozzáadás** >**a Blob storage-**, majd adja hozzá a kimenetben az alias `datamloutput`. 

3. Töltse ki a **Blob Storage** panel ezekkel az értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   |**Kimeneti alias** | A nevet használja `datamloutput` válassza **válassza ki a blob storage az előfizetésből**       |
   |**Storage-fiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Tároló**  | Válassza ki a korábban létrehozott tárolót (`azuresamldemoblob`)        |
   |**Eseményszerializációs formátum**  |  Válassza ki **CSV**       |

   ![Új feladat kimenetének beállításai](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Kattintson a **Save** (Mentés) gombra.   


### <a name="add-the-machine-learning-function"></a>A Machine Learning-függvény hozzáadása 
A Machine Learning-modellek korábban egy web Service közzé. Ebben a forgatókönyvben a Stream állapotelemzési feladat futtatásakor küld minden minta tweet hangulatelemzés for a web Service a bemeneti. A Machine Learning web service adja vissza a róluk szóló véleményeket (`positive`, `neutral`, vagy `negative`) és a egy pozitív Twitter-üzenetet valószínűségét. 

Az oktatóanyag ezen szakaszában adja meg a Stream állapotelemzési feladat egy függvényt. A függvény tweetet küldhet a web Service és a válasz visszakapásához meghívható. 

1. Győződjön meg arról, hogy a web service URL-CÍMÉT és API-kulcsát az Excel-munkafüzet a korábban letöltött.

2. Keresse meg a feladat panelt > **funkciók** > **+ Hozzáadás** > **AzureML**

3. Töltse ki a **Azure Machine Learning-függvény** panel ezekkel az értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   | **Függvény aliasa** | A nevet használja `sentiment` , és válassza ki **adja meg az Azure Machine Learning-függvények beállításainak manuális** következőkhöz adja meg az URL-cím és a kulcs lehetőség.      |
   | **URL-cím**| Illessze be a web service URL-CÍMÉT.|
   |**Kulcs** | Illessze be az API-kulcsot. |
  
   ![A Machine Learning-függvény hozzáadása a Stream Analytics-feladat beállításai](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. Kattintson a **Save** (Mentés) gombra.

### <a name="create-a-query-to-transform-the-data"></a>Hozzon létre egy lekérdezést az adatok átalakításához

Stream Analytics egy deklaratív, SQL-alapú lekérdezést használ a bemeneti adatok vizsgálata és dolgozza fel. Ebben a szakaszban létrehozott lekérdezés, amely minden tweetnek beolvassa a bemeneti, majd meghívja a Machine Learning-függvény hangulatelemzés végrehajtásához. A lekérdezés az eredmény ezután elküldi a kimenetet, hogy a megadott (a blob storage).

1. A feladat áttekintése panelre való visszatéréshez.

2.  A **Feladattopológia**, kattintson a **lekérdezés** mezőbe.

3. Adja meg a következő lekérdezést:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    A lekérdezés hívja meg a korábban létrehozott függvényhez (`sentiment`) annak érdekében, hogy a vélemény elemzését, a bemeneti adatok minden tweetnek. 

4. Kattintson a **mentése** a lekérdezés mentéséhez.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Most elkezdheti a Stream Analytics-feladat.

### <a name="start-the-job"></a>A feladat indítása
1. A feladat áttekintése panelre való visszatéréshez.

2. Kattintson a **Start** a panel tetején.

3. Az a **indítási feladat**, jelölje be **egyéni**, majd amikor a CSV-fájl feltöltése a blob storage megelőző napot. Ha elkészült, kattintson a **Start**.  


### <a name="check-the-output"></a>A kimenet ellenőrzése
1. A feladat néhány percet, amíg megjelenik a tevékenység futtatása lehetővé teszik a **figyelés** mezőbe. 

2. Ha egy eszköz, amellyel normál esetben a blob storage tartalmának vizsgálata, eszközzel, hogy megvizsgálja a `azuresamldemoblob` tároló. Másik lehetőségként kövesse az alábbi lépéseket az Azure Portalon:

    1. A portálon keresse meg a `samldemo` tárolási fiók és a fiókban található a `azuresamldemoblob` tároló. Látja a tárolót a két fájlt: a minta tweetek tartalmazó fájlt, és a Stream Analytics-feladat által létrehozott CSV-fájl.
    2. Kattintson a jobb gombbal a létrehozott fájlt, majd **letöltése**. 

   ![Fürt megosztott kötetei szolgáltatás feladat kimenetének letöltése a Blob storage-ból](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Nyissa meg a létrehozott CSV-fájl. Hiba a következő példához hasonlóan jelenik meg:  
   
   ![Stream Analytics Machine Learning, a fürt megosztott kötetei szolgáltatás megtekintése](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Metrikák megtekintése
Az Azure Machine Learning-függvény kapcsolatos metrikákat is megtekintheti. Jelennek meg a következő függvény kapcsolatos metrikákat a **figyelés** mezőbe a feladat panelen:

* **Kérelmek függvény** egy Machine Learning webszolgáltatás felé küldött kérések számát jelzi.  
* **Események függvény** azt jelzi, hogy a kérelemben szereplő események száma. Alapértelmezés szerint az egyes kérések egy Machine Learning web Service legfeljebb 1000 eseményeket tartalmazza.  


## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Integrálása a REST API-t és a gépi tanulás](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)



