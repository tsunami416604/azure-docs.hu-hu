---
title: Az Azure Machine Learning Azure Stream Analytics-integráció
description: Ez a cikk ismerteti, hogyan gyorsan beállíthat egy egyszerű Azure Stream Analytics-feladat, amely az Azure Machine Learning szolgáltatásban egy felhasználó által definiált függvény használatával.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 63648dfe02a0b5ed00d0a7206a6aabbe200f94c4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527912"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Azure Stream Analytics és az Azure Machine Learning segítségével véleményeket elemzések végrehajtását
Ez a cikk ismerteti, hogyan gyorsan beállíthat egy egyszerű Azure Stream Analytics-feladat, amely az Azure Machine Learning. Akkor használhatja Machine Learning véleményeket analytics a Cortana Intelligence Gallery a streamadatok szöveg elemzésére és valós időben a céggel kapcsolatos véleményeket pontszám meghatározásához. A Cortana Intelligence Suite használata lehetővé teszi ennek a feladatnak anélkül, hogy a menő a céggel kapcsolatos véleményeket elemzési modell létrehozásának bemutatása.

Amiről tanulni az ebben a cikkben az ehhez hasonló helyzeteknek alkalmazhatja:

* Valós idejű véleményeket folyamatos Twitter-adatok elemzése.
* A felhasználói rekordok elemzése csevegés a támogató személyzete számára.
* Megjegyzések a videók, fórumok és blogok kiértékelése. 
* Sok más valós idejű, a prediktív pontozási forgatókönyvek.

Egy valós forgatókönyv esetén az adatok közvetlenül a Twitter adatfolyam visszajelzést kap. Az oktatóanyag leegyszerűsítése írás, hogy a Streaming Analytics-feladat Twitter-üzeneteket lekérdezi az Azure Blob storage CSV-fájlból. Létrehozhat saját CSV-fájl, vagy egy CSV-mintafájlt, a következő ábrán látható módon használhatja:

![a CSV-fájlban szereplő minta Twitter-üzenetek](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A Streaming Analytics-feladat az Ön által létrehozott lesz a céggel kapcsolatos véleményeket elemzési modell egy felhasználói függvény (UDF) rajta a mintaadatokra szöveget a blob-tárolóból. A kimenet (a céggel kapcsolatos véleményeket elemzés eredménye) ugyanarra a blob-tároló egy másik CSV-fájl írása. 

A következő ábra bemutatja, ezt a konfigurációt. Amint több reális forgatókönyvek esetében a blob storage lecserélheti Twitter-adatok az Azure Event Hubs bemeneti adatfolyam. Emellett sikerült készít egy [Microsoft Power BI](https://powerbi.microsoft.com/) valós idejű megjelenítésével kapcsolatos a összesített céggel kapcsolatos véleményeket.    

![Stream Analytics a Machine Learning integrációjának áttekintése](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy a következő:

* Aktív Azure-előfizetés.
* Néhány adatot a CSV-fájlból. Letöltheti a fájlt a korábban bemutatott [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), vagy létrehozhat saját fájlt. Ebben a cikkben feltételezzük, hogy a fájl a Githubról használ.

Magas szinten a feladatokat, ebben a cikkben bemutatott, tegye a következőket:

1. Hozzon létre egy Azure storage-fiókot és egy blob storage tárolót, és egy CSV-formátumú bemeneti fájl feltöltése a tárolóba.
3. A Cortana Intelligence Gallery a céggel kapcsolatos véleményeket elemzési modell felvétele az Azure Machine Learning munkaterülettel, és ez a modell rendszerbe állítása a Machine Learning-munkaterület webszolgáltatásként.
5. Hozzon létre egy Stream Analytics-feladat, amely behívja a webszolgáltatás függvényében annak meghatározására, a bemeneti szöveg céggel kapcsolatos véleményeket.
6. Indítsa el a Stream Analytics-feladat, és ellenőrizze a kimeneti.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Hozzon létre egy tárolót, és a bemeneti CSV-fájl feltöltése
Ezt a lépést minden CSV-fájl, például a rendelkezésre álló a Githubból is használhatja.

1. Az Azure portálon kattintson **hozzon létre egy erőforrást** > **tárolási** > **tárfiók**.

2. Adjon meg egy nevet (`samldemo` a példában). A név csak kisbetűket és számokat használható, és Azure között egyedinek kell lennie. 

3. Adjon meg egy létező erőforráscsoportot, és adjon meg egy helyet. Hely azt javasoljuk, hogy ebben az oktatóanyagban létrehozott összes erőforrást használja-e ugyanazon a helyen.

    ![Adja meg a tárfiókadatok](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Az Azure portálon válassza ki a tárfiókot. A storage-fiók panelen kattintson **tárolók** majd  **+ &nbsp;tároló** blob-tároló létrehozásához.

    ![A blob-tároló létrehozása](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Adja meg a tároló nevét (`azuresamldemoblob` a példában), és ellenőrizze, hogy **hozzáférési típus** értéke **Blob**. Ha végzett, kattintson az **OK** gombra.

    ![Adja meg a blob-tároló adatait](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. Az a **tárolók** panelen jelölje ki az új tárolót, amely tároló panel nyílik meg.

7. Kattintson a **Feltöltés** gombra.

    ![Egy tároló "Feltöltés" gomb](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Az a **feltöltése a blob** panelen, a feltöltés a **sampleinput.csv** korábban letöltött fájlban. A **Blob-típusú**, jelölje be **blokkblob** , és a blokkméret 4 MB, amely elegendő-e ez az oktatóanyag.

9. Kattintson a **feltöltése** gomb a panel alján.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adja hozzá a céggel kapcsolatos véleményeket elemzési modell a Cortana Intelligence Gallery

Most, hogy a mintaadatokat egy blobba, engedélyezheti a céggel kapcsolatos véleményeket elemzési modellek a Cortana Intelligence Gallery.

1. Lépjen a [véleményeket prediktív elemzési modell](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) a Cortana Intelligence Gallery lapján.  

2. Kattintson a **Megnyitás a Studióban**.  
   
   ![A Stream Analytics Machine Learning, nyissa meg a Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Jelentkezzen be a munkaterületen. Válasszon ki egy helyet.

4. Kattintson a **futtatása** az oldal alján. A folyamat fut, amely egy percet vesz igénybe.

   ![Futtassa a kísérletben a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Miután a folyamat sikeresen lefutott, válassza ki **webes szolgáltatás telepítése** az oldal alján.

   ![egy webszolgáltatás telepítése a kísérletben a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Ellenőrizze, hogy a céggel kapcsolatos véleményeket elemzési modell használatra kész, kattintson a **teszt** gombra. Adja meg például a "Tetszik Microsoft" bemeneti szöveget. 

   ![teszt kísérletben a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    A vizsgálat működik, az alábbi példához hasonló eredményt jelenik meg:

   ![a vizsgálati eredmények a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Az a **alkalmazások** oszlop, kattintson a **Excel 2010 vagy korábbi munkafüzet** egy Excel-munkafüzet letöltésére mutató hivatkozás. A munkafüzet tartalmazza az API-kulcsot és az URL-címet, akkor később be kell állítania a Stream Analytics-feladat.

    ![Stream Analytics-Machine Learning, gyors áttekintő](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>A gépi tanulási modellt használó Stream Analytics-feladat létrehozása

Mostantól létrehozhat egy Stream Analytics-feladat, a minta Twitter-üzeneteket olvasó a CSV-fájlból a blob Storage tárolóban. 

### <a name="create-the-job"></a>A feladat létrehozása

1. Nyissa meg az [Azure Portal](https://portal.azure.com).  

2. Kattintson a **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **Stream Analytics-feladat**. 

3. A feladat neve `azure-sa-ml-demo`, adja meg az előfizetés, adjon meg egy meglévő erőforráscsoportot vagy hozzon létre egy újat, és válassza ki azt a helyet, a feladat.

   ![új Stream Analytics-feladat beállításainak megadása](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>A feladat bemeneti konfigurálása
A feladat lekérdezi a bemeneti blob-tároló korábban feltöltött CSV-fájlból.

1. A feladat létrehozása után, a **feladat topológia** a feladat panelen kattintson a **bemenetek** lehetőséget.    

2. Az a **bemenetek** panelen kattintson a **adatfolyam-bemenet hozzáadása** >**Blob-tároló**

3. Töltse ki a **Blob Storage** panel ezekkel az értékekkel:

   
   |Mező  |Érték  |
   |---------|---------|
   |**A bemeneti alias** | A nevet használja `datainput` válassza **válasszon blob-tároló az előfizetésből**       |
   |**Storage-fiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Tároló**  | Válassza ki a korábban létrehozott tároló (`azuresamldemoblob`)        |
   |**Esemény szerializálási formátum**  |  Válassza ki **CSV**       |

   ![Új feladat bemeneti beállításai](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-the-job-output"></a>A feladat kimenetére konfigurálása
A feladat küldése eredmények ugyanazt a blob-tároló ahol lekérdezi a bemeneti. 

1. A **feladat topológia** a feladat panelen kattintson a **kimenetek** lehetőséget.  

2. Az a **kimenetek** panelen kattintson **Hozzáadás** >**Blob-tároló**, majd adja hozzá a egy kimenet `datamloutput`. 

3. Töltse ki a **Blob Storage** panel ezekkel az értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   |**A kimeneti alias** | A nevet használja `datamloutput` válassza **válasszon blob-tároló az előfizetésből**       |
   |**Storage-fiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Tároló**  | Válassza ki a korábban létrehozott tároló (`azuresamldemoblob`)        |
   |**Esemény szerializálási formátum**  |  Válassza ki **CSV**       |

   ![Új feladat kimenet beállításai](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Kattintson a **Save** (Mentés) gombra.   


### <a name="add-the-machine-learning-function"></a>A Machine Learning-függvény hozzáadása 
Korábban közzétett a gépi tanulási modell webszolgáltatáshoz. Ebben a forgatókönyvben a adatfolyam állapotelemzési feladat futtatásakor küldené minden egyes minta tweetet a céggel kapcsolatos véleményeket elemzés a webszolgáltatás bemenete. A Machine Learning webszolgáltatás adja vissza a céggel kapcsolatos véleményeket (`positive`, `neutral`, vagy `negative`) és egy pozitív tweetet valószínűségét. 

Az oktatóanyag ezen részében adja meg az adatfolyam állapotelemzési feladat egy függvényt. A függvény küldjön egy tweetet, a webszolgáltatás és a válasz segítségnyújtáshoz hívható meg. 

1. Győződjön meg arról, hogy a webes szolgáltatás URL-CÍMÉT és API-kulcsát az Excel-munkafüzetben korábban letöltött.

2. Keresse meg a feladat panelen > **funkciók** > **+ Hozzáadás** > **AzureML**

3. Töltse ki a **Azure Machine Learning-függvény** panel ezekkel az értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   | **Függvény aliasa** | A nevet használja `sentiment` válassza **adja meg Azure Machine Learning-függvény beállítások manuálisan** amely felkínálja az URL-címet és egy kulcs.      |
   | **URL-cím**| Illessze be a webalkalmazás URL-címe.|
   |**Kulcs** | Illessze be az API-kulcsot. |
  
   ![A Machine Learning-függvény hozzáadása a Stream Analytics-feladat beállításai](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. Kattintson a **Save** (Mentés) gombra.

### <a name="create-a-query-to-transform-the-data"></a>Az adatok átalakítására lekérdezés létrehozása

A Stream Analytics lekérdezéssel deklaratív, az SQL-alapú vizsgálja meg a bemeneti és dolgozza fel. Ebben a szakaszban egy lekérdezést, amely a bemeneti olvassa be az egyes tweetet, majd meghívja az véleményeket elemzés végrehajtásához a Machine Learning függvény hoz létre. A lekérdezés az eredmény ezután elküldi a kimeneti (blob-tároló) definiálását.

1. Térjen vissza a feladathoz – áttekintés panelen.

2.  A **feladat topológia**, kattintson a **lekérdezés** mezőbe.

3. Adja meg a következő lekérdezést:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    A lekérdezés meghívja a korábban létrehozott függvényt (`sentiment`) ahhoz, hogy a bemeneti adatok minden tweetet véleményeket elemzést. 

4. Kattintson a **mentése** a lekérdezés mentéséhez.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Most elindíthatja a Stream Analytics-feladat.

### <a name="start-the-job"></a>A feladat indítása
1. Térjen vissza a feladathoz – áttekintés panelen.

2. Kattintson a **Start** a panel tetején.

3. Az a **indítási feladat**, jelölje be **egyéni**, majd válassza ki az előtt, amikor a CSV-fájl feltöltése a blob storage egy nap. Amikor elkészült, kattintson a **Start**.  


### <a name="check-the-output"></a>A kimeneti ellenőrzése
1. A pár percet, amíg megjelenik a tevékenység futtatása feladat lehetővé teszik a **figyelés** mezőbe. 

2. Ha egy eszköz, amellyel normál esetben a blob-tároló tartalmának vizsgálata, hogy vizsgálata eszközzel a `azuresamldemoblob` tároló. Alternatív megoldásként hajtsa végre az alábbi lépéseket az Azure-portálon:

    1. Keresse meg a portál a `samldemo` tárolási fiókot, és a fiókban található a `azuresamldemoblob` tároló. Megjelenik a tároló két fájlt: a minta Twitter-üzeneteket tartalmazó fájlt, és a Stream Analytics-feladat által létrehozott CSV-fájlból.
    2. Kattintson a jobb gombbal a létrehozott fájl, és válassza ki **letöltése**. 

   ![A Blob-tároló CSV-feladat kimeneti letöltése](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Nyissa meg a létrehozott CSV-fájlt. Megjelenik az alábbihoz hasonlót:  
   
   ![Stream Analytics Machine Learning, CSV megtekintése](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Nézet metrikák
Azure Machine Learning-függvény vonatkozó metrikáinak is megtekintheti. A következő függvény kapcsolódó metrikák jelennek meg a **figyelés** mezőbe a feladat panelen:

* **Kérelmek működéséhez** a Machine Learning webszolgáltatásba küldött kérelmek számát jelzi.  
* **Események működéséhez** a kérelemben szereplő események számát jelzi. Alapértelmezés szerint a Machine Learning webszolgáltatásba az egyes kérelmek legfeljebb 1000 eseményeket tartalmazza.  


## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Integrálható a REST API-t és a gépi tanulás](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)



