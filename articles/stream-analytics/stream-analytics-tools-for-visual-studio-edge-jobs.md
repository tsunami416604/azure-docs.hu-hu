---
title: Azure Stream Analytics Edge-feladatok a Visual Studióban
description: Ez a cikk azt ismerteti, hogyan lehet a Visual studióhoz készült Stream Analytics Tools használatával IoT Edge-feladatokon elkészíteni és létrehozni a Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ec4a4041378ce94ae70ba7a88b3fef80f7dcd193
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925025"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Stream Analytics Edge-feladatok fejlesztése a Visual Studio Tools használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Visual studióhoz készült Stream Analytics-eszközöket. Megtudhatja, hogyan hozhat létre és végezhet hibakeresést Stream Analytics Edge-feladatokat. A feladatok létrehozása és tesztelése után megtekintheti a Azure Portal, és üzembe helyezheti azt az eszközökön. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek:

* Telepítse a [Visual studio 2019](https://visualstudio.microsoft.com/downloads/), a [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)vagy a [Visual Studio 2013 Update 4 frissítést](https://www.microsoft.com/download/details.aspx?id=45326). Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott.  

* Stream Analytics Tools for Visual Studio telepítéséhez kövesse a [telepítési utasításokat](stream-analytics-tools-for-visual-studio-edge-jobs.md) .
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge-projekt létrehozása 

A Visual Studióban válassza a **fájl** > **új** > **projekt**lehetőséget. Nyissa meg a **sablonok** listáját a bal oldalon > bontsa ki a **Azure stream Analytics** > **stream Analytics Edge** > **Azure stream Analytics Edge alkalmazást**. Adja meg a projekt nevét, helyét és a megoldás nevét, majd kattintson **az OK gombra**.

![Új Stream Analytics Edge-projekt a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

A projekt létrehozása után navigáljon a **megoldáskezelő** a mappa-hierarchia megtekintéséhez.

![Stream Analytics Edge-feladatokhoz tartozó Solution Explorer-nézet](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Válassza ki a megfelelő előfizetést

1. A Visual Studio **nézet** menüjében válassza a **Server Explorer**lehetőséget.  

2. Kattintson a jobb gombbal az **azure** > válassza a **kapcsolódás Microsoft Azure előfizetés** > lehetőséget, majd jelentkezzen be az Azure-fiókjával.

## <a name="define-inputs"></a>Bemenetek definiálása

1. A **megoldáskezelő**bontsa ki a **bemenetek** csomópontot, és megjelenik egy **EdgeInput. JSON**nevű bemenet. Kattintson ide duplán a beállítások megtekintéséhez.  

2. Forrás típusának beállítása **adatfolyamként**. Ezután állítsa a forrás – **Edge hubot**, az esemény szerializálási formátumát **JSON**-ra, és az **UTF8**kódolással. Szükség esetén átnevezheti a **bemeneti aliast**, így a példaként is elhagyhatja azt. Ha átnevezi a bemeneti aliast, használja a lekérdezés definiálásakor megadott nevet. A beállítások mentéséhez válassza a **Mentés** lehetőséget.  
   ![Stream Analytics a feladatok bemeneti konfigurációját](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Kimenetek meghatározása

1. A **megoldáskezelő**bontsa ki a **kimenetek** csomópontot, és a **EdgeOutput. JSON**nevű kimenetet kell látnia. Kattintson ide duplán a beállítások megtekintéséhez.  

2. Győződjön meg arról, hogy az **Edge hub**kiválasztásához a fogadót állítja be, az esemény szerializálási formátumát **JSON**-re állítja, a kódolást **UTF8**-ra állítja, és beállítja a Format **Array** Szükség esetén átnevezheti a **kimeneti aliast**is, így a példaként is elhagyhatja a következőt:. Ha átnevezi a kimeneti aliast, használja a lekérdezés definiálásakor megadott nevet. A beállítások mentéséhez válassza a **Mentés** lehetőséget. 
   ![Stream Analytics a feladatok kimeneti konfigurációját](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

A Stream Analytics IoT Edge környezetekben üzembe helyezett Stream Analytics feladatok támogatják a legtöbb [stream Analytics lekérdezési nyelvi referenciát](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). A Stream Analytics Edge-feladatok esetében azonban a következő műveletek még nem támogatottak: 


|**Kategória**  | **Parancs**  |
|---------|---------|
|Egyéb operátorok | <ul><li>PARTICIONÁLÁS</li><li>IDŐBÉLYEG</li><li>JavaScript UDF</li><li>Felhasználó által definiált összesítések (UDA)</li><li>GetMetadataPropertyValue</li><li>Több mint 14 összesítés használata egyetlen lépésben</li></ul>   |

Amikor létrehoz egy Stream Analytics Edge-feladatot a portálon, a fordító automatikusan figyelmeztetést küld, ha nem használ támogatott kezelőt.

A Visual Studióban adja meg a következő átalakítási lekérdezést a lekérdezés-szerkesztőben (**script. asaql fájl**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>A feladatok helyi tesztelése

A lekérdezés helyi teszteléséhez fel kell töltenie a mintaadatok. Mintaadatok beszerzéséhez töltse le a regisztrációs adatait a [GitHub-adattárból](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) , és mentse a helyi számítógépre. 

1. A mintaadatok feltöltéséhez kattintson a jobb gombbal a **EdgeInput. JSON** fájlra, és válassza a **helyi bemenet hozzáadása** elemet.  

2. Az előugró ablakban > **böngészhet** a helyi útvonalon található mintaadatok között > válassza a **Mentés**lehetőséget.
   ![helyi beviteli konfiguráció a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. A rendszer automatikusan hozzáadja a **local_EdgeInput. JSON** nevű fájlt a bemenetek mappájához.  
4. Futtathatja helyileg, vagy elküldheti az Azure-ba. A lekérdezés teszteléséhez válassza a **helyi Futtatás**lehetőséget.  
   ![Stream Analytics a feladatok futtatásának lehetőségei a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A parancssori ablak megjeleníti a feladatok állapotát. Ha a feladatot sikeresen futtatják, egy olyan mappát hoz létre, amely a "Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" nevű Project mappa elérési útjának "2018-02-23-11-31-42" nevű mappáját fogja kinézni. Az eredmények a helyi mappában való megtekintéséhez navigáljon a mappa elérési útjára:

   Bejelentkezhet a Azure Portalba, és ellenőrizheti, hogy a rendszer létrehozza-e a feladatot. 

   ![Stream Analytics feladatok eredményének mappája](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>A feladatok elküldése az Azure-ba

1. Mielőtt elküldi a feladatot az Azure-ba, csatlakoznia kell az Azure-előfizetéséhez. Nyissa meg a **Server explorert** > kattintson a jobb gombbal az **azure** > **kapcsolódás Microsoft Azure előfizetéshez** > Jelentkezzen be az Azure-előfizetésbe.  

2. A feladatoknak az Azure-ba való elküldéséhez navigáljon a lekérdezés-szerkesztő > válassza a **Küldés az Azure**-ba lehetőséget.  

3. Megnyílik egy előugró ablak. Válasszon egy létező Stream Analytics Edge-feladatot, vagy hozzon létre újat. Ha egy meglévő feladatot frissít, akkor az a feladatok összes beállítását lecseréli, ebben a forgatókönyvben egy új feladatot fog közzétenni. Válassza az **új Azure stream Analytics-feladatok létrehozása** lehetőséget > adja meg a feladatokhoz hasonló nevet, például **MyASAEdgeJob** > Válassza ki a szükséges **előfizetést**, **erőforráscsoportot**és **helyet** > válassza a **Küldés**lehetőséget.

   ![Stream Analytics-feladatok elküldése az Azure-ba a Visual studióból](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Most már létrehozta az Stream Analytics Edge-feladatot. A [futtatási feladatok IoT Edge oktatóanyagban](stream-analytics-edge.md) megtudhatja, hogyan helyezheti üzembe azt az eszközein. 

## <a name="manage-the-job"></a>A feladatok kezelése 

A feladatok és a feladatütemezés állapota a Server Explorerben tekinthető meg. **Stream Analytics** a **Server Explorerben**bontsa ki az előfizetést és az erőforráscsoportot, ahol üzembe helyezte az stream Analytics Edge-feladatot. A MyASAEdgejob megtekintheti a **létrehozott**állapottal. Bontsa ki a feladatok csomópontot, és kattintson rá duplán a feladatok nézet megnyitásához.

![A Server Explorer-feladatok felügyeleti lehetőségei](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A feladatok nézet ablak olyan műveleteket tesz lehetővé, mint például a feladatok frissítése, a feladatok törlése, valamint a feladatoknak a Azure Portalból való megnyitása.

![Feladatütemezés és egyéb beállítások a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Következő lépések

* [További információ a Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA IoT Edge oktatóanyag](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visszajelzés küldése a csapatnak a kérdőív használatával](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
