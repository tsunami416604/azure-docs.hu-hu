---
title: Azure Stream Analytics integráció a Azure Machine Learning
description: Ez a cikk azt ismerteti, hogyan lehet gyorsan beállítani egy egyszerű Azure Stream Analytics feladatot, amely egy felhasználó által definiált függvény használatával integrálja Azure Machine Learning.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: seodec18
ms.openlocfilehash: c683cfeadcc13e5112a4687e18db9338d3574cd3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459600"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic-preview"></a>A Azure Stream Analytics és Azure Machine Learning Studio (klasszikus) (előzetes verzió) szolgáltatással kapcsolatos érzelmek elemzése
Ez a cikk azt ismerteti, hogyan lehet gyorsan beállítani egy egyszerű Azure Stream Analytics-feladatot, amely integrálja Azure Machine Learning Studio (klasszikus). A Cortana Intelligence Gallery a Machine Learning hangulat elemzési modelljét használja a folyamatos szöveges adatok elemzéséhez, és valós időben határozza meg az érzelmek pontszámát. A Cortana Intelligence Suite használata lehetővé teszi a feladat elvégzését anélkül, hogy aggódnia kellene a hangulati elemzési modell kialakításának bonyolult felépítésével.

A jelen cikkből megismerheti a következő forgatókönyveket:

* Valós idejű érzelmek elemzése a Twitter-adatok folyamatos átviteléhez.
* Az ügyfél-csevegések rekordjainak elemzése a támogatási munkatársakkal.
* Megjegyzések kiértékelése fórumokon, blogokon és videókon. 
* Számos más valós idejű, prediktív pontozási forgatókönyv.

A valós forgatókönyvekben közvetlenül a Twitter-adatfolyamból szerezheti be az adatait. Az oktatóanyag leegyszerűsítése érdekében úgy van írva, hogy a streaming Analytics-feladatok az Azure Blob Storage-ban található CSV-fájlból származó tweeteket kapjanak. Létrehozhatja saját CSV-fájlját, vagy használhat minta CSV-fájlt is, ahogy az a következő képen is látható:

![CSV-fájlban látható minta tweetek](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Az Ön által létrehozott streaming Analytics-feladat felhasználói függvényként (UDF) alkalmazza a hangulati elemzési modellt a blob-tárolóban található mintaszöveg-adatokon. A kimenet (a hangulat elemzése eredményét) ugyanabba a blob-tárolóba írja a rendszer, amely egy másik CSV-fájlban van. 

A következő ábra ezt a konfigurációt mutatja be. Ahogy azt egy reálisabb forgatókönyv esetében is megjegyezte, a blob Storage-t egy Azure Event Hubs bemenetről származó Twitter-adatokkal helyettesítheti. Emellett létrehozhat egy [Microsoft Power bi](https://powerbi.microsoft.com/) valós idejű vizualizációt az összesített hangulatból.    

![Stream Analytics Machine Learning integráció áttekintése](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-előfizetés.
* Egy CSV-fájl, amelyben bizonyos adatfájlok szerepelnek. Letöltheti a [githubról](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)korábban bemutatott fájlt, vagy létrehozhatja a saját fájlját is. Ez a cikk azt feltételezi, hogy a fájlt használja a GitHubról.

A cikkben bemutatott feladatok végrehajtásához a következő műveleteket kell végrehajtania:

1. Hozzon létre egy Azure Storage-fiókot és egy blob Storage-tárolót, és töltsön fel egy CSV formátumú bemeneti fájlt a tárolóba.
3. Vegyen fel egy hangulati elemzési modellt a Cortana Intelligence Gallery az Azure Machine Learning Studio (klasszikus) munkaterületre, és telepítse a modellt webszolgáltatásként a Machine Learning munkaterületen.
5. Hozzon létre egy olyan Stream Analytics feladatot, amely függvényként hívja meg ezt a webszolgáltatást, hogy meghatározza a szöveg bevitelének hangulatát.
6. Indítsa el a Stream Analytics feladatot, és vizsgálja meg a kimenetet.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Storage-tároló létrehozása és a CSV-bemeneti fájl feltöltése
Ebben a lépésben bármilyen CSV-fájlt használhat, például a GitHubról elérhetőt.

1. A Azure Portal kattintson az **erőforrás létrehozása** > **Storage** > Storage- **fiók**elemre.

2. Adjon meg egy nevet (a példában`samldemo`). A név csak kisbetűket és számokat használhat, és egyedinek kell lennie az Azure-ban. 

3. Válasszon egy meglévő erőforráscsoportot, és válasszon helyet. A helyhez azt javasoljuk, hogy az oktatóanyagban létrehozott összes erőforrás ugyanazt a helyet használja.

    ![adja meg a Storage-fiók adatait](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. A Azure Portal válassza ki a Storage-fiókot. A Storage-fiók panelen kattintson a **tárolók** elemre, majd a blob Storage létrehozásához kattintson **+&nbsp;tároló** elemre.

    ![BLOB Storage-tároló létrehozása a bevitelhez](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Adja meg a tároló nevét (a példában szereplő`azuresamldemoblob`), és győződjön meg arról, hogy a **hozzáférési típus** **blob**értékre van beállítva. Ha végzett, kattintson az **OK** gombra.

    ![BLOB-tároló részleteinek megadása](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. A **tárolók** panelen jelölje ki az új tárolót, amely megnyitja az adott tárolóhoz tartozó panelt.

7. Kattintson a **Feltöltés** gombra.

    ![Tároló "feltöltés" gombja](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. A **blob feltöltése** panelen töltse fel a korábban letöltött **sampleinput. csv** fájlt. A **blob típusa**beállításnál válassza a **blob blokkolása** lehetőséget, és állítsa a blokk méretét 4 MB-ra, ami elegendő ehhez az oktatóanyaghoz.

9. Kattintson a panel alján található **feltöltés** gombra.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adja hozzá az Cortana Intelligence Gallery

Most, hogy a mintaadatok egy blobban vannak, a Cortana Intelligence Galleryban engedélyezheti a hangulat elemzési modelljét.

1. Lépjen az Cortana Intelligence Gallery [prediktív hangulat elemzési modell](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) lapjára.  

2. Kattintson **a Megnyitás a Studióban**elemre.  
   
   ![Stream Analytics Machine Learning, nyissa meg Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. A munkaterületre való ugráshoz jelentkezzen be. Válasszon ki egy helyet.

4. Kattintson a lap alján található **Futtatás** gombra. A folyamat fut, ami körülbelül egy percet vesz igénybe.

   ![kísérlet futtatása Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. A folyamat sikeres futtatása után válassza a **webszolgáltatások üzembe helyezése** elemet az oldal alján.

   ![kísérlet üzembe helyezése Machine Learning Studio webszolgáltatásként](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Annak ellenőrzéséhez, hogy a hangulat-elemzési modell készen áll-e a használatra, kattintson a **teszt** gombra. Adja meg a szövegbeviteli adatokat, például a "szeretem a Microsoftot". 

   ![tesztelési kísérlet Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Ha a teszt működik, az alábbi példához hasonló eredmény jelenik meg:

   ![tesztelési eredmények Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Az **alkalmazások** oszlopban kattintson az **Excel 2010 vagy korábbi munkafüzet** hivatkozásra az Excel-munkafüzet letöltéséhez. A munkafüzet tartalmazza azt az API-kulcsot és URL-címet, amelyre később szükség lesz a Stream Analytics feladatokhoz.

    ![Stream Analytics Machine Learning, gyors áttekintés](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>A Machine Learning modellt használó Stream Analytics-feladatok létrehozása

Most létrehozhat egy Stream Analytics feladatot, amely a blob Storage-ban lévő CSV-fájlból beolvassa a minta tweeteket. 

### <a name="create-the-job"></a>A feladat létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).  

2. Kattintson **az erőforrás létrehozása** > **eszközök internetes hálózata** > **stream Analytics feladatokra**. 

3. Nevezze el a feladatot `azure-sa-ml-demo`, adjon meg egy előfizetést, adjon meg egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, és válassza ki a feladatokhoz tartozó helyet.

   ![új Stream Analytics feladatokhoz tartozó beállítások megadása](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>A feladatok bevitelének konfigurálása
A feladatot a rendszer a blob Storage-ba korábban feltöltött CSV-fájlból olvassa be.

1. A feladatnak a létrehozása után a feladatok paneljén, **a feladatok panelen** kattintson a **bemenetek** lehetőségre.    

2. A **bemenetek** panelen kattintson a **stream-bemenet hozzáadása** >**blob Storage** elemre.

3. Töltse ki a **blob Storage** panelt a következő értékekkel:

   
   |Mező  |Value (Díj)  |
   |---------|---------|
   |**Bemeneti alias** | Használja a `datainput` nevet, és válassza a **blob Storage kiválasztása az előfizetésből** lehetőséget.       |
   |**Storage-fiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Tároló**  | Válassza ki a korábban létrehozott tárolót (`azuresamldemoblob`)        |
   |**Eseményszerializációs formátum**  |  **CSV** kiválasztása       |

   ![Új Stream Analytics-feladathoz tartozó beállítások](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Kattintson a **Mentés** gombra.

### <a name="configure-the-job-output"></a>A feladatok kimenetének konfigurálása
A művelet az eredményeket ugyanarra a blob-tárolóra küldi, ahol a bemenet bekerül. 

1. A feladatok paneljén a **feladatok topológiájában** kattintson a **kimenetek** lehetőségre.  

2. A **kimenetek** panelen kattintson a >**blob Storage** **hozzáadása** elemre, majd adjon hozzá egy kimenetet az alias `datamloutput`. 

3. Töltse ki a **blob Storage** panelt a következő értékekkel:

   |Mező  |Value (Díj)  |
   |---------|---------|
   |**Kimeneti alias** | Használja a `datamloutput` nevet, és válassza a **blob Storage kiválasztása az előfizetésből** lehetőséget.       |
   |**Storage-fiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Tároló**  | Válassza ki a korábban létrehozott tárolót (`azuresamldemoblob`)        |
   |**Eseményszerializációs formátum**  |  **CSV** kiválasztása       |

   ![Új Stream Analytics feladatok kimenetének beállításai](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Kattintson a **Mentés** gombra.   


### <a name="add-the-machine-learning-function"></a>A Machine Learning függvény hozzáadása 
Korábban egy Machine Learning modellt tett közzé egy webszolgáltatásban. Ebben az esetben a stream Analysis-feladatok futtatásakor a rendszer az összes minta tweetet elküldi a webszolgáltatásnak az érzelmek elemzéséhez. A Machine Learning webszolgáltatása egy érzelem (`positive`, `neutral`vagy `negative`) értéket ad vissza, és a csiripelés valószínűsége pozitív. 

Az oktatóanyag ezen szakaszában definiál egy függvényt a stream Analysis feladatban. A függvény meghívható úgy, hogy csipogt küldjön a webszolgáltatásnak, és visszakapja a választ. 

1. Győződjön meg arról, hogy rendelkezik az Excel-munkafüzetben korábban letöltött webszolgáltatás URL-címével és API-kulcsával.

2. Navigáljon a feladathoz > **függvények** > + > **AzureML** **hozzáadása**

3. Töltse ki a **Azure Machine learning függvény** panelt a következő értékekkel:

   |Mező  |Value (Díj)  |
   |---------|---------|
   | **Függvény aliasa** | Használja a `sentiment` nevet, és válassza a **Azure Machine learning-függvény beállításainak megadása manuálisan** lehetőséget, amely lehetővé teszi az URL-cím és a kulcs megadását.      |
   | **URL-cím**| Illessze be a webszolgáltatás URL-címét.|
   |**Kulcs** | Illessze be az API-kulcsot. |
  
   ![Machine Learning függvény Stream Analytics feladathoz való hozzáadásának beállításai](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Kattintson a **Mentés** gombra.

### <a name="create-a-query-to-transform-the-data"></a>Lekérdezés létrehozása az adatátalakításhoz

Stream Analytics egy deklaratív, SQL-alapú lekérdezést használ a bevitel vizsgálatára és feldolgozására. Ebben a szakaszban egy olyan lekérdezést hoz létre, amely beolvassa az egyes tweeteket a bemenetből, majd meghívja a Machine Learning függvényt az érzelmek elemzésének elvégzéséhez. A lekérdezés ezután elküldi az eredményt a megadott kimenetre (blob Storage).

1. Térjen vissza a feladatok áttekintés paneljére.

2.  A **feladatok topológiája**területen kattintson a **lekérdezés** mezőre.

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

    A lekérdezés meghívja a korábban létrehozott függvényt (`sentiment`), hogy elvégezze az elemzést a bemenet minden tweetén. 

4. Kattintson a **Mentés** gombra a lekérdezés mentéséhez.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Most már elindíthatja a Stream Analytics feladatot.

### <a name="start-the-job"></a>A feladat indítása
1. Térjen vissza a feladatok áttekintés paneljére.

2. A panel tetején kattintson a **Start** gombra.

3. A **kezdési feladatban**válassza az **Egyéni**lehetőséget, majd válasszon egy napot, mielőtt feltölti a CSV-fájlt a blob Storage-ba. Ha elkészült, kattintson a **Start**gombra.  


### <a name="check-the-output"></a>A kimenet keresése
1. A feladatok futtatásának engedélyezése néhány percig, amíg meg nem jelenik a tevékenység a **figyelés** mezőben. 

2. Ha olyan eszközzel rendelkezik, amelyet általában a blob Storage tartalmának vizsgálatára használ, használja az eszközt az `azuresamldemoblob` tároló vizsgálatához. Másik lehetőségként hajtsa végre a következő lépéseket a Azure Portalban:

    1. A portálon keresse meg a `samldemo` Storage-fiókot, és a fiókon belül keresse meg a `azuresamldemoblob` tárolót. A tárolóban két fájl látható: Ez a fájl tartalmazza a minta tweeteket és a Stream Analytics feladatokból létrehozott CSV-fájlt.
    2. Kattintson a jobb gombbal a létrehozott fájlra, majd válassza a **Letöltés**lehetőséget. 

   ![CSV-feladatok kimenetének letöltése a blob Storage-ból](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Nyissa meg a létrehozott CSV-fájlt. A következő példához hasonlóan jelenik meg:  
   
   ![Stream Analytics Machine Learning, CSV-nézet](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Metrikák megtekintése
Azure Machine Learning függvényekkel kapcsolatos metrikákat is megtekintheti. A következő függvényhez kapcsolódó mérőszámok a feladat paneljének **figyelés** mezőjében jelennek meg:

* A **függvények kérései** a Machine learning webszolgáltatásnak küldött kérések számát jelzik.  
* A **függvény eseményei** a kérelemben szereplő események számát jelzik. Alapértelmezés szerint minden Machine Learning webszolgáltatásra irányuló kérelem legfeljebb 1 000 eseményt tartalmaz.  


## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [REST API és Machine Learning integrálása](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)



