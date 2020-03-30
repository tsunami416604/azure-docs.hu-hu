---
title: Azure Stream Analytics-integráció az Azure Machine Learningdel
description: Ez a cikk ismerteti, hogyan lehet gyorsan beállítani egy egyszerű Azure Stream Analytics-feladat, amely integrálja az Azure Machine Learning, egy felhasználó által definiált függvény használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067091"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Hangulatelemzés végrehajtása az Azure Stream Analytics és az Azure Machine Learning Studio segítségével (klasszikus)

Ez a cikk ismerteti, hogyan lehet gyorsan beállítani egy egyszerű Azure Stream Analytics-feladat, amely integrálja az Azure Machine Learning Studio (klasszikus). A Cortana Intelligence Gallery Machine Learning hangulatelemzési modelljével elemezheti a streamelési szöveges adatokat, és valós időben határozhatja meg a hangulatpontszámot. A Cortana Intelligence Suite használatával anélkül végezheti el ezt a feladatot, hogy aggódnia kellene a hangulatelemzési modell létrehozásának bonyolultsága miatt.

> [!TIP]
> Erősen ajánlott [azure Machine Learning UDF-ek](machine-learning-udf.md) használata az Azure Machine Learning Studio (klasszikus) UDF helyett a jobb teljesítmény és megbízhatóság érdekében.

A cikkből tanultakat az alábbi forgatókönyvekre alkalmazhatja:

* Valós idejű hangulat elemzése a Twitter-adatok streamelésén.
* Az ügyfélszolgálattal folytatott ügyfélbeszélgetések rekordjainak elemzése.
* A fórumokon, blogokban és videókban található megjegyzések értékelése. 
* Sok más valós idejű, prediktív pontozási forgatókönyv.

Egy valós forgatókönyv esetén az adatokat közvetlenül egy Twitter-adatfolyamból kapná meg. Az oktatóanyag egyszerűsítése érdekében úgy van megírva, hogy a Streaming Analytics-feladat tweeteket kap egy CSV-fájlból az Azure Blob storage-ban. Létrehozhat saját CSV-fájlt, vagy használhat minta CSV-fájlt, ahogy az az alábbi képen látható:

![CsV-fájlban megjelenített tweetek mintája](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A streaming analytics-feladat, amely létrehoz létre alkalmazza a hangulatelemzési modell, mint egy felhasználó által definiált függvény (UDF) a minta szöveges adatokat a blob store. A kimenet (a hangulatelemzés eredménye) egy másik CSV-fájlban lévő blobtárolóba van írva. 

Az alábbi ábra bemutatja ezt a konfigurációt. Mint megjegyezte, egy reálisabb forgatókönyv, lecserélheti blob storage streamelési Twitter-adatok egy Azure Event Hubs bemenet. Ezenkívül az összesített vélemény [Microsoft Power BI](https://powerbi.microsoft.com/) valós idejű megjelenítését is elkészítheti.    

![A Stream Analytics Machine Learning-integráció áttekintése](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-előfizetés.
* Egy CSV fájl, benne néhány adattal. Letöltheti a korábban bemutatott fájlt a [GitHubról,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)vagy létrehozhatja saját fájlját. Ebben a cikkben feltételezzük, hogy a GitHubról származó fájlt használja.

A cikkben bemutatott feladatok elvégzéséhez magas szinten tegye a következőket:

1. Hozzon létre egy Azure-tárfiókot és egy blob storage-tárolót, és töltsön fel egy CSV-formátumú bemeneti fájlt a tárolóba.
3. Adjon hozzá egy hangulatelemzési modellt a Cortana Intelligence Gallery-ből az Azure Machine Learning Studio (klasszikus) munkaterületéhez, és telepítse ezt a modellt webszolgáltatásként a Machine Learning-munkaterületen.
5. Hozzon létre egy Stream Analytics-feladatot, amely függvényként hívja meg ezt a webszolgáltatást a szövegbevitel hangulatának meghatározásához.
6. Indítsa el a Stream Analytics-feladatot, és ellenőrizze a kimenetet.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Tárolótároló létrehozása és a CSV-bemeneti fájl feltöltése
Ehhez a lépéshez bármilyen CSV-fájlt használhat, például a GitHubon elérhetőt.

1. Az Azure Portalon kattintson **az Erőforrás** > **tárterület-fiók****Storage** > létrehozása elemre.

2. Adjon meg`samldemo` egy nevet ( a példában). A név csak kisbetűket és számokat használhat, és egyedinek kell lennie az Azure-ban. 

3. Adjon meg egy meglévő erőforráscsoportot, és adjon meg egy helyet. A hely, azt javasoljuk, hogy az ebben az oktatóanyagban létrehozott összes erőforrás használja ugyanazt a helyet.

    ![tárfiók részleteinek biztosítása](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Az Azure Portalon válassza ki a tárfiókot. A tárfiók panelen kattintson a **Tárolók** elemre, majd a ** + &nbsp;Tároló** elemre a blobstorage létrehozásához.

    ![Blob storage-tároló létrehozása bevitelhez](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Adja meg a tároló`azuresamldemoblob` nevét (a példában), és ellenőrizze, hogy az **Access típusa** Blob értékre **van-e**állítva. Ha végzett, kattintson az **OK** gombra.

    ![blobtároló részleteinek megadása](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. A **Tárolók** panelen válassza ki az új tartályt, amely megnyitja a késet a tárolóhoz.

7. Kattintson a **Feltöltés** gombra.

    !["Feltöltés" gomb egy tárolóhoz](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. A **Blob feltöltése** panelen töltse fel a korábban letöltött **sampleinput.csv** fájlt. **Blob-típus**esetén válassza **a Blokkblob** lehetőséget, és állítsa a blokk mérete 4 MB-ra, ami elegendő ehhez az oktatóanyaghoz.

9. Kattintson a penge alján található **Feltöltés** gombra.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>A hangulatelemzési modell hozzáadása a Cortana Intelligence Galériából

Most, hogy a mintaadatok egy blobban vannak, engedélyezheti a hangulatelemzési modellt a Cortana Intelligenciatárban.

1. Lépjen a Cortana Intelligenciatár [prediktív hangulatelemzési](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) modelllapjára.  

2. Kattintson **a Megnyitás a stúdióban**gombra.  
   
   ![Stream Analytics Machine Learning, nyissa meg a Machine Learning Studio-t](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Jelentkezzen be a munkaterületre való ugráshoz. Válasszon ki egy helyet.

4. Kattintson a lap alján a **Futtatás** gombra. A folyamat fut, ami körülbelül egy percet vesz igénybe.

   ![kísérlet futtatása a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. A folyamat sikeres futtatása után válassza a lap alján a **Webszolgáltatás telepítése** lehetőséget.

   ![kísérlet telepítése a Machine Learning Studióban webszolgáltatásként](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Annak ellenőrzéséhez, hogy a hangulatelemzési modell készen áll-e a **használatra,** kattintson a Teszt gombra. Adjon meg olyan szövegbevitelt, mint például a "Szeretem a Microsoftot". 

   ![tesztkísérlet a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Ha a teszt működik, a következő példához hasonló eredmény jelenik meg:

   ![teszteredmények a Machine Learning Studióban](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Az **Alkalmazások oszlopban** kattintson az **Excel 2010-es vagy korábbi munkafüzethivatkozásra** egy Excel-munkafüzet letöltéséhez. A munkafüzet tartalmazza az API-kulcsot és azt az URL-címet, amely a Stream Analytics-feladat beállításához szükséges.

    ![Stream Analytics Machine Learning, gyors pillantás](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>A Machine Learning-modellt használó Stream Analytics-feladat létrehozása

Most már létrehozhat egy Stream Analytics-feladatot, amely beolvassa a minta tweeteket a CSV-fájlból a blob storage-ban. 

### <a name="create-the-job"></a>A feladat létrehozása

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)  

2. Kattintson az >  **Erőforrás-internet** > **-adatfolyam-elemzési****feladat**létrehozása elemre. 

3. Nevezze el `azure-sa-ml-demo`a feladatot , adjon meg egy előfizetést, adjon meg egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, és válassza ki a feladat helyét.

   ![beállítások megadása az új Stream Analytics-feladathoz](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>A feladat bemenetének konfigurálása
A feladat a blob storage-ba korábban feltöltött CSV-fájlból kapja meg a bemenetet.

1. A feladat létrehozása után a **feladattopológia** a feladat panelen, kattintson a **bemenetek** lehetőséget.    

2. A **Bemeneti panelen** kattintson a **Stream Input** >Blob storage hozzáadása**elemre.**

3. Töltse ki a **Blob Storage** panelt az alábbi értékekkel:

   
   |Mező  |Érték  |
   |---------|---------|
   |**Bemeneti alias** | Használja a `datainput` nevet, és válassza **a Blob storage kiválasztása az előfizetésből**       |
   |**Tárfiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Konténer**  | A korábban létrehozott tároló`azuresamldemoblob`kiválasztása ( )        |
   |**Eseményszerializációs formátum**  |  **CSV** kiválasztása       |

   ![Az új Stream Analytics-feladatbevitel beállításai](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Kattintson a **Mentés** gombra.

### <a name="configure-the-job-output"></a>A feladat kimenetének konfigurálása
A feladat eredményeket küld ugyanannak a blob tárolónak, ahol bemenetet kap. 

1. A feladat panel **Feladattopológia** területén kattintson a **Kimenetek** lehetőségre.  

2. A **Kimenetek** panelen kattintson a**Blob storage** **hozzáadása** > `datamloutput`gombra, majd adjon hozzá egy kimenetet az aliassal. 

3. Töltse ki a **Blob Storage** panelt az alábbi értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   |**Kimeneti alias** | Használja a `datamloutput` nevet, és válassza **a Blob storage kiválasztása az előfizetésből**       |
   |**Tárfiók**  |  Válassza ki a korábban létrehozott tárfiókot.  |
   |**Konténer**  | A korábban létrehozott tároló`azuresamldemoblob`kiválasztása ( )        |
   |**Eseményszerializációs formátum**  |  **CSV** kiválasztása       |

   ![Az új Stream Analytics-feladatkimenet beállításai](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Kattintson a **Mentés** gombra.   


### <a name="add-the-machine-learning-function"></a>A Machine Learning függvény hozzáadása 
Korábban közzétett egy Machine Learning-modellt egy webszolgáltatásban. Ebben a forgatókönyvben, amikor a Stream Analysis feladat fut, küld minden egyes minta tweet a bemeneti a webszolgáltatás hangulatelemzésre. A Machine Learning webszolgáltatás`positive`egy `neutral`vélemény `negative`( , , vagy ) értéket ad vissza, és annak valószínűségét, hogy a tweet pozitív. 

Az oktatóanyag ezen szakaszában definiálegy függvényt a Stream Analysis feladatban. A függvény meghívható, hogy küldjön egy csipog a webszolgáltatás, és a válasz vissza. 

1. Győződjön meg arról, hogy rendelkezik az Excel-munkafüzetben korábban letöltött webszolgáltatás URL-cím- és API-kulcsa.

2. Navigáljon a feladatpanelre > **Functions** > **+ Add** > **AzureML**

3. Töltse ki az **Azure Machine Learning függvénypanelt** az alábbi értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   | **Függvény aliasa** | Használja a `sentiment` nevet, és **válassza az Azure Machine Learning funkció beállításaimanuálisan,** amely lehetőséget ad az URL-cím és a kulcs megadására.      |
   | **Url**| Illessze be a webszolgáltatás URL-címét.|
   |**Kulcs** | Illessze be az API-kulcsot. |
  
   ![A Machine Learning-függvény Stream Analytics-feladathoz való hozzáadásának beállításai](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Kattintson a **Mentés** gombra.

### <a name="create-a-query-to-transform-the-data"></a>Lekérdezés létrehozása az adatok átalakításához

A Stream Analytics deklaratív, SQL-alapú lekérdezést használ a bevitel vizsgálatához és feldolgozásához. Ebben a szakaszban hozzon létre egy lekérdezést, amely beolvassa az egyes tweetek a bemeneti, majd meghívja a Machine Learning függvényt a hangulatelemzés. A lekérdezés ezután elküldi az eredményt a megadott kimenetre (blob storage).

1. Térjen vissza a feladatáttekintő panelhez.

2.  A **Feladattopológia csoportban**kattintson a **Lekérdezés** mezőre.

3. Írja be a következő lekérdezést:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    A lekérdezés meghívja a korábban`sentiment`létrehozott ( ) függvényt, hogy hangulatelemzést végezzen a bemeneti tweetek minden egyes tweetén. 

4. Kattintson a **Mentés** gombra a lekérdezés mentéséhez.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Most már elindíthatja a Stream Analytics feladatot.

### <a name="start-the-job"></a>A feladat indítása
1. Térjen vissza a feladatáttekintő panelhez.

2. Kattintson a panel tetején található **Indítás** gombra.

3. A **kezdési feladatban**válassza az **Egyéni**lehetőséget, majd egy nappal azt megelőzően, hogy feltöltötte a CSV-fájlt a blobstorage-ba. Ha elkészült, kattintson a **Start**gombra.  


### <a name="check-the-output"></a>Ellenőrizze a kimenetet
1. Hagyja, hogy a feladat néhány percig fusson, amíg nem látja a tevékenységet a **Figyelés** mezőben. 

2. Ha rendelkezik olyan eszközzel, amelyet általában a blobstorage tartalmának `azuresamldemoblob` vizsgálatára használ, használja ezt az eszközt a tároló vizsgálatához. Másik lehetőségként tegye a következő lépéseket az Azure Portalon:

    1. A portálon keresse `samldemo` meg a tárfiókot, `azuresamldemoblob` és a fiókon belül keresse meg a tárolót. Két fájl jelenik meg a tárolóban: a mintatweeteket tartalmazó fájlt és a Stream Analytics-feladat által létrehozott CSV-fájlt.
    2. Kattintson a jobb gombbal a létrehozott fájlra, és válassza a **Letöltés parancsot.** 

   ![CSV-feladat kimenetének letöltése a Blob storage-ból](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Nyissa meg a létrehozott CSV-fájlt. A következő példához hasonló példát láthat:  
   
   ![Stream Analytics Machine Learning, CSV nézet](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Metrikák megtekintése
Az Azure Machine Learning funkcióval kapcsolatos metrikákat is megtekintheti. A következő, funkcióval kapcsolatos metrikák jelennek meg a feladat panel **figyelése** mezőjében:

* **A függvénykérések** a Machine Learning webszolgáltatásnak küldött kérelmek számát jelzi.  
* **A függvényesemények** a kérelemben lévő események számát jelzi. Alapértelmezés szerint a Machine Learning webszolgáltatás minden egyes kérése legfeljebb 1000 eseményt tartalmaz.  


## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Rest API és gépi tanulás integrálása](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)



