---
title: Edge-feladatok az Azure Stream Analytics Tools for Visual Studio
description: Ez a cikk ismerteti, hogyan hozhat létre, hibakeresése és a Stream Analytics tools for Visual Studio használatával a Stream Analytics Edge-feladatok létrehozása.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 684690baa63f5ccd65c69e3a1b7e310c2f809e59
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735558"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>A Visual Studio-eszközökkel Stream Analytics Edge-feladatok fejlesztése

Ebben az oktatóanyagban elsajátíthatja, hogyan létrehozásához, hibakereséséhez és a Stream Analytics Edge-feladatok létrehozása a Stream Analytics tools for Visual Studio használatával. Miután létrehozta és a feladat tesztelése, telepíteni kell az eszközöket az Azure Portalra léphet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek:

* Telepítés [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), vagy [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Express kiadás nem támogatott.  

* Kövesse a [telepítési utasításokat](stream-analytics-tools-for-visual-studio-edge-jobs.md) Stream Analytics tools for Visual Studio telepítése.
 
## <a name="create-a-stream-analytics-edge-project"></a>A Stream Analytics Edge-projekt létrehozása 

Válassza ki a Visual Studióban a **fájl** > **új** > **projekt**. Keresse meg a **sablonok** a bal oldali listában > bontsa ki a **Azure Stream Analytics** > **Stream Analytics peremhálózati**  >   **Az Azure Stream Analytics alkalmazás él**. Adja meg a projektet, majd válassza ki a nevét, helyét és megoldás nevét **OK**.

![Új Edge-projektet a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Lekérdezi a projekt létrehozása után lépjen a **Megoldáskezelőben** megtekintése a mappahierarchiában.

![Stream Analytics Edge-feladatok megoldáskezelő-nézet](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Válassza ki a megfelelő előfizetést

1. A Visual studióból **nézet** menüjében válassza **Server Explorer**.  

2. Kattintson a jobb gombbal **Azure** > Válasszon **kapcsolódás a Microsoft Azure-előfizetéshez** > és az Azure-fiókjával jelentkezzen be.

## <a name="define-inputs"></a>Bemenetek megadása

1. A a **Megoldáskezelőben**, bontsa ki a **bemenetek** csomópont megjelenik egy input nevű **EdgeInput.json**. Kattintson duplán a beállítások megtekintéséhez.  

2. Forrás típusa **Data Stream**. Majd beállíthatja a forrás **Edge hubot**, Eseményszerializációs formátum a **Json**, és a kódolási **UTF8**. Másik lehetőségként átnevezheti a **bemeneti Alias**, hagyja, mert ebben a példában. Abban az esetben, ha a bemeneti alias átnevezéséhez használja a lekérdezés meghatározásakor a megadott néven. A beállítások mentéséhez válassza a **Mentés** lehetőséget.  
   ![Stream Analytics-feladat bemeneti konfigurálása](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Kimenetek definiálása

1. Az a **Megoldáskezelőben**, bontsa ki a **kimenetek** csomópont kimenetnek kell megjelennie egy nevű **EdgeOutput.json**. Kattintson duplán a beállítások megtekintéséhez.  

2. Győződjön meg arról, hogy a fogadó kiválasztásához értéke **Edge hubot** > Eseményszerializációs formátum beállítása **Json** > kódolás értékre van állítva, és **UTF8** > és a formátuma  **Tömb**. Másik lehetőségként átnevezheti a **kimeneti Alias**, hagyja, mert ebben a példában. Abban az esetben, ha a kimeneti alias átnevezéséhez használja a lekérdezés meghatározásakor a megadott néven. A beállítások mentéséhez válassza a **Mentés** lehetőséget. 
   ![Stream Analytics-feladat kimeneti konfigurálása](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

A peremhálózati környezetek üzembe helyezett Stream Analytics-feladatok támogatja a legtöbb [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), azonban a következő műveleteket még nem támogatottak az Edge-feladatok: 


|**Kategória**  | **Parancs**  |
|---------|---------|
|Térinformatikai operátorok |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Más operátorokkal | <ul><li>A PARTÍCIÓ SZERINT</li><li>A TÖBB MINT IDŐBÉLYEG</li><li>DISTINCT</li><li>A COUNT operátorban kifejezésparaméternek</li><li>A DATE és TIME függvények mikromásodperces</li><li>JavaScript UDA (Ez a funkció egyelőre előzetes verzióként érhető-feladatok a felhőbe telepített)</li></ul>   |

Edge-feladatok a portálon hoz létre, amikor a fordítóprogram automatikusan figyelmeztet, ha nem használ egy támogatott operátort.

A Visual studióból, a következő transzformációs lekérdezés definiálása a Lekérdezésszerkesztő (**script.asaql fájl**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>A feladat a helyi tesztelése

A lekérdezés teszteléséhez helyileg, akkor érdemes a mintaadatok feltöltése. Töltse le a regisztrációs adatokat is kap a mintaadatokat a [GitHub-adattár](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) , és mentse a helyi számítógépen. 

1. Mintaadatok feltöltése, kattintson a jobb gombbal **EdgeInput.json** fájlt, és válassza a **helyi bemenet hozzáadása**  

2. A felugró ablakban > **Tallózás** a mintaadatokat is abból a helyi elérési út > válassza **mentése**.
   ![A Visual Studio helyi bemeneti konfiguráció](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Nevű fájl **local_EdgeInput.json** automatikusan hozzáadja a bemeneti mappában.  
4. helyileg futtatja az alkalmazást, vagy küldje el az Azure-bA. A lekérdezés teszteléséhez > Válasszon **futtatása helyileg**.  
   ![Stream Analytics-feladat beállításai futtassa a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A parancssori ablakot a feladat állapotát jeleníti meg. Amikor a feladat sikeresen lefutott, az "A Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" projekt mappa elérési úthoz létrehoz egy mappát, amely a következőhöz hasonló "2018-02-23-11-31-42". Keresse meg a mappa elérési útja az eredmények megtekintése a helyi mappában:

   Jelentkezzen be az Azure Portalon is, és győződjön meg arról, hogy létrejött-e a feladatot. 

   ![Stream Analytics feladat eredménye mappa](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Küldje el a feladatot az Azure-bA

1. Mielőtt elküldi a feladatot az Azure-ba, csatlakoznia kell az Azure-előfizetés. Nyissa meg **Server Explorer** > kattintson a jobb gombbal **Azure** > **kapcsolódás a Microsoft Azure-előfizetés** > jelentkezzen be az Azure-előfizetésében.  

2. Küldje el a feladatot, az Azure-ba, lépjen a Lekérdezésszerkesztő > Válasszon **elküldése az Azure-bA**.  

3. Megnyílik egy előugró ablak, ahol kiválaszthatja egy meglévő Edge-feladat frissítése, vagy hozzon létre egy újat. Amikor frissít egy meglévő feladat, azzal lecseréli az összes feladat konfigurációt, ebben a forgatókönyvben, egy új feladatot teszi közzé. Válassza ki **hozzon létre egy új Azure Stream Analytics-feladat** > adjon meg egy nevet az alábbihoz hasonló feladat **MyASAEdgeJob** > Válassza ki a szükséges **előfizetés**, **Erőforráscsoport**, és **hely** > válassza **elküldése**.

   ![Stream Analytics-feladat elküldése az Azure-bA a Visual Studióból](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Most a Stream Analytics Edge-feladat létrehozása után olvassa el a [feladatok futtatása az IoT Edge-oktatóanyag](stream-analytics-edge.md) megtudhatja, hogyan telepítheti az eszközökre. 

## <a name="manage-the-job"></a>A feladat kezeléséhez 

Megtekintheti a feladat és a Server Explorerből feladatábrában állapotát. A **Stream Analytics** a **Server Explorer**, bontsa ki az előfizetést és az erőforráscsoportot, amelybe telepítette az Edge-feladat. Megtekintheti a MyASAEdgejob állapotú **létrehozva**. Bontsa ki a projekt csomópontra, és kattintson duplán az, hogy a feladat nézet megnyitása.

![Server explorer feladat felügyeleti beállítások](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A feladatok megtekintése ablakot biztosít műveleteket, mint a feladat frissítése, a feladat törlése, és nyissa meg a feladat az Azure Portalról.

![Feladatábra és egyéb lehetőségek Visual studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>További lépések

* [További információ az Azure IoT Edge-ben](../iot-edge/about-iot-edge.md)
* [Az IoT Edge-oktatóanyag ASA](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visszajelzés küldése a csapatnak, használja a felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
