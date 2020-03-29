---
title: Azure Stream Analytics Edge-feladatok a Visual Studióban
description: Ez a cikk bemutatja, hogyan hozhat létre, debug, és hozza létre a Stream Analytics IoT Edge-feladatok a Stream Analytics eszközök visual studio használatával.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354566"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Stream Analytics Edge-feladatok fejlesztése a Visual Studio eszközeivel

Ebben az oktatóanyagban megtudhatja, hogyan használhatja a Stream Analytics-eszközöket a Visual Studio számára. Megtudhatja, hogyan hozhat létre, debugolhat és hozhat létre Stream Analytics Edge-feladatokat. Miután létrehozta és tesztelte a feladatot, az Azure Portalon üzembe helyezheti azt az eszközökre. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő előfeltételekre van szükség:

* Telepítse a [Visual Studio 2019,](https://visualstudio.microsoft.com/downloads/) [a Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)vagy [a Visual Studio 2013 4.](https://www.microsoft.com/download/details.aspx?id=45326) Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az expressz kiadás nem támogatott.  

* A Stream Analytics-eszközök Visual Studio-hoz történő telepítéséhez kövesse a [telepítési utasításokat.](stream-analytics-tools-for-visual-studio-edge-jobs.md)
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge-projekt létrehozása 

A Visual Studio alkalmazásban válassza az**Új** > **projekt** **fájlja** > lehetőséget. Keresse meg a bal oldali **Sablonok** listát, > bővítse ki az **Azure Stream Analytics** > **Edge** > Azure Stream Analytics Edge**alkalmazást.** Adja meg a projekt nevét, helyét és megoldásnevét, és válassza az **OK gombot.**

![Új Stream Analytics Edge projekt a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

A projekt létrehozása után keresse meg a **Megoldáskezelőt** a mappahierarchia megtekintéséhez.

![A Stream Analytics Edge-feladat Megoldáskezelő nézete](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Válassza ki a megfelelő előfizetést

1. A Visual Studio **Nézet** menüben válassza a **Kiszolgálókezelő**lehetőséget.  

2. Kattintson a jobb gombbal az **Azure** > válassza **a Csatlakozás a Microsoft Azure-előfizetéshez** >, majd jelentkezzen be Azure-fiókjával.

## <a name="define-inputs"></a>A bemenetek meghatározása

1. A **Solution Explorer**programban bontsa ki a **Bemeneti csomópontot,** és egy **EdgeInput.json**nevű bemenetnek kell lennie. Kattintson duplán a beállítások megtekintéséhez.  

2. Forrástípus beállítása **adatfolyamra**. Ezután állítsa a Forrás t **Edge Hub**, Event Serialization Format (Eseményszerializálási formátum **) és**a Kódolást **UTF8 -ra.** Szükség esetén átnevezheti a **bemeneti aliast,** hagyjuk úgy, ahogy ebben a példában. Abban az esetben, ha átnevezi a bemeneti aliast, használja a megadott nevet a lekérdezés definiálásakor. A beállítások mentéséhez válassza a **Mentés** lehetőséget.  
   ![Stream Analytics-feladat beviteli konfigurációja](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Kimenetek meghatározása

1. A **Solution Explorer**alkalmazásban bontsa ki a **Kimenetek csomópontot,** és egy **EdgeOutput.json**nevű kimenetnek kell lennie. Kattintson duplán a beállítások megtekintéséhez.  

2. Ügyeljen arra, hogy a Sink beállítással válassza **az Edge Hub**elemet, állítsa az Eseményszerializálási formátumot **Json-ra,** állítsa a kódolást **UTF8-ra**, és állítsa be a Format **Array**parancsot. Szükség esetén átnevezheti a **Kimeneti aliast**, hagyjuk úgy, ahogy ebben a példában. Abban az esetben, ha átnevezi a kimeneti aliast, használja a megadott nevet a lekérdezés definiálásakor. A beállítások mentéséhez válassza a **Mentés** lehetőséget. 
   ![Stream Analytics-feladat kimeneti konfigurációja](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

A Stream Analytics IoT Edge környezetekben telepített Stream Analytics-feladatok támogatják a [Stream Analytics lekérdezési nyelvének legtöbb hivatkozását.](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396) A streamanalytics-es edge-feladatok azonban még nem támogatottak a következő műveletek: 


|**Kategória**  | **Parancs**  |
|---------|---------|
|Egyéb gazdasági szereplők | <ul><li>PARTÍCIÓ</li><li>IDŐBÉLYEG FELETT</li><li>JavaScript UDF</li><li>Felhasználó által definiált aggregátumok (UDA)</li><li>GetMetadataPropertyValue</li><li>Több mint 14 aggregátum használata egy lépésben</li></ul>   |

Amikor létrehoz egy Stream Analytics Edge-feladatot a portálon, a fordító automatikusan figyelmezteti, ha nem támogatott operátort használ.

A Visual Studio alkalmazásban adja meg a következő átalakítási lekérdezést a lekérdezésszerkesztőben (**script.asaql fájl**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>A feladat helyi tesztelése

A lekérdezés helyi teszteléséhez töltse fel a mintaadatokat. Mintaadatokat a Regisztrációs adatok nak a [GitHub-tárházból](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) való letöltésével és a helyi számítógépre való mentésével szerezhet be. 

1. A mintaadatok feltöltéséhez kattintson a jobb gombbal az **EdgeInput.json** fájlra, és válassza **a Helyi bevitel hozzáadása parancsot**  

2. Az előugró ablakban > **Tallózás a** mintaadatok at a helyi elérési úton > válassza a **Mentés**lehetőséget .
   ![Helyi bemeneti konfiguráció a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. A **local_EdgeInput.json** nevű fájl automatikusan hozzáadódik a bemeneti mappához.  
4. Futtathatja helyileg, vagy elküldheti az Azure-ba. A lekérdezés teszteléséhez válassza a **Futtatás helyileg**lehetőséget.  
   ![A Stream Analytics feladatfuttatási beállításai a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A parancssorablak a feladat állapotát mutatja. Ha a feladat sikeresen fut, létrehoz egy mappát, amely a "2018-02-23-11-31-42" mappához hasonlóan jelenik meg a projekt mappaelérési útvonalán:"Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Keresse meg a mappa elérési útját a helyi mappában lévő eredmények megtekintéséhez:

   Az Azure Portalra is bejelentkezhet, és ellenőrizheti, hogy a feladat létrejött-e. 

   ![Stream Analytics-feladat eredménymappája](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>A feladat elküldése az Azure-ba

1. Mielőtt elküldi a feladatot az Azure-ba, csatlakoznia kell az Azure-előfizetéshez. Nyissa meg **a Server Explorer** > jobb gombbal kattintson az **Azure** > **Connect microsoft Azure-előfizetésre,** > jelentkezzen be Azure-előfizetéséhez.  

2. A feladat Azure-ba való elküldéséhez keresse meg a lekérdezésszerkesztőt, > válassza a **Küldés az Azure-ba**lehetőséget.  

3. Megnyílik egy előugró ablak. Válasszon egy meglévő Stream Analytics Edge-feladat frissítéséhez, vagy hozzon létre egy újat. Egy meglévő feladat frissítésekénél az összes feladatkonfigurációt felülírja, ebben az esetben egy új feladatot tesz közzé. Válassza **az Új Azure Stream Analytics feladat létrehozása** lehetőséget, > adjon meg egy nevet a feladatnak, például a **MyASAEdgeJob** > válassza ki a szükséges **előfizetést,** **erőforráscsoportot**és **helyet** > válassza a **Küldés lehetőséget.**

   ![Stream Analytics-feladat küldése az Azure-ba a Visual Studio-ból](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Most a Stream Analytics Edge-feladat létrejött. Tekintse meg a [futtatási feladatok ioT Edge-oktatóanyag,](stream-analytics-edge.md) hogy megtanulják, hogyan telepítheti azt az eszközökre. 

## <a name="manage-the-job"></a>A feladat kezelése 

A feladat állapotát és a feladatdiagramot a Kiszolgálókezelőből tekintheti meg. A Server **Explorer** **Stream Analytics** szolgáltatásából bontsa ki az előfizetést és azt az erőforráscsoportot, amelyben a Stream Analytics Edge-feladatot telepítette. Megtekintheti a MyASAEdgejob állapottal **Created**. Bontsa ki a feladatcsomópontot, és kattintson rá duplán a feladatnézet megnyitásához.

![Server Explorer feladatkezelési beállításai](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A feladatnézet ablakban olyan műveleteket biztosít, mint például a feladat frissítése, a feladat törlése és a feladat megnyitása az Azure Portalról.

![Feladatdiagram és egyéb beállítások a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>További lépések

* [További információ az Azure IoT Edge-ről](../iot-edge/about-iot-edge.md)
* [ASA az IoT Edge oktatóanyagán](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visszajelzés küldése a csapatnak ezzel a felméréssel](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
