---
title: Stream Analytics Edge-feladatok az Azure Stream Analytics Tools for Visual Studio
description: Ez a cikk ismerteti, hogyan hozhat létre, hibakeresése és a Stream Analytics tools for Visual Studio használatával feladatok létrehozása a Stream Analytics az IoT Edge-ben.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 1601bf6c73d9f3450959773c85385bc8ef907a66
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329964"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>A Visual Studio-eszközökkel Stream Analytics Edge-feladatok fejlesztése

Ebben az oktatóanyagban elsajátíthatja, hogyan lehet Stream Analytics tools for Visual Studio használata. Megismerheti, hogyan hozhat létre, hibakeresése és a Stream Analytics Edge-feladatok létrehozása. Miután létrehozta és a feladat tesztelése, telepíteni kell az eszközöket az Azure Portalra léphet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek:

* Telepítés [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), vagy [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Express kiadás nem támogatott.  

* Kövesse a [telepítési utasításokat](stream-analytics-tools-for-visual-studio-edge-jobs.md) Stream Analytics tools for Visual Studio telepítése.
 
## <a name="create-a-stream-analytics-edge-project"></a>A Stream Analytics Edge-projekt létrehozása 

Válassza ki a Visual Studióban a **fájl** > **új** > **projekt**. Keresse meg a **sablonok** a bal oldali listában > bontsa ki a **Azure Stream Analytics** > **Stream Analytics peremhálózati**  >   **Az Azure Stream Analytics alkalmazás él**. Adja meg a projektet, majd válassza ki a nevét, helyét és megoldás nevét **OK**.

![Új Stream Analytics Edge-projektet a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

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

2. Állítsa be a fogadó kiválasztásához **Edge hubot**, Eseményszerializációs formátum beállítása **Json**, beállíthatja a kódolás **UTF8**, és állítsa be a formátum **tömb**. Másik lehetőségként átnevezheti a **kimeneti Alias**, hagyja, mert ebben a példában. Abban az esetben, ha a kimeneti alias átnevezéséhez használja a lekérdezés meghatározásakor a megadott néven. A beállítások mentéséhez válassza a **Mentés** lehetőséget. 
   ![Stream Analytics-feladat kimeneti konfigurálása](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

A Stream Analytics az IoT Edge-környezetekben üzembe helyezett Stream Analytics-feladatok támogatja a legtöbb [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). A következő műveletek azonban még nem támogatottak a Stream Analytics Edge-feladatok: 


|**Kategória**  | **Parancs**  |
|---------|---------|
|Más operátorokkal | <ul><li>A PARTÍCIÓ SZERINT</li><li>A TÖBB MINT IDŐBÉLYEG</li><li>JavaScript UDF</li><li>Felhasználó által definiált összesítések (UDA)</li><li>GetMetadataPropertyValue</li><li>Legfeljebb 14 összesítést használó egyetlen lépésben</li></ul>   |

A portálon egy Stream Analytics peremhálózati feladatot hoz létre, amikor a fordítóprogram automatikusan figyelmeztet, ha, de nem egy támogatott operátort használja.

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
4. helyileg futtatja az alkalmazást, vagy küldje el az Azure-bA. A lekérdezés teszteléséhez válassza **futtatása helyileg**.  
   ![Stream Analytics-feladat beállításai futtassa a Visual Studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A parancssori ablakot a feladat állapotát jeleníti meg. Amikor a feladat sikeresen lefutott, az "A Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" projekt mappa elérési úthoz létrehoz egy mappát, amely a következőhöz hasonló "2018-02-23-11-31-42". Keresse meg a mappa elérési útja az eredmények megtekintése a helyi mappában:

   Jelentkezzen be az Azure Portalon is, és győződjön meg arról, hogy létrejött-e a feladatot. 

   ![Stream Analytics feladat eredménye mappa](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Küldje el a feladatot az Azure-bA

1. Mielőtt elküldi a feladatot az Azure-ba, csatlakoznia kell az Azure-előfizetés. Nyissa meg **Server Explorer** > kattintson a jobb gombbal **Azure** > **kapcsolódás a Microsoft Azure-előfizetés** > jelentkezzen be az Azure-előfizetésében.  

2. Küldje el a feladatot, az Azure-ba, lépjen a Lekérdezésszerkesztő > Válasszon **elküldése az Azure-bA**.  

3. Megjelenik egy előugró ablak. Válasszon egy meglévő Stream Analytics Edge-feladat frissítése, vagy hozzon létre egy újat. Amikor frissít egy meglévő feladat, azzal lecseréli az összes feladat konfigurációt, ebben a forgatókönyvben, publikálom a kódtárat egy új feladatot. Válassza ki **hozzon létre egy új Azure Stream Analytics-feladat** > adjon meg egy nevet az alábbihoz hasonló feladat **MyASAEdgeJob** > Válassza ki a szükséges **előfizetés**, **Erőforráscsoport**, és **hely** > válassza **elküldése**.

   ![Stream Analytics-feladat elküldése az Azure-bA a Visual Studióból](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Most már a Stream Analytics Edge-feladat létrehozása. Olvassa el a [feladatok futtatása az IoT Edge-oktatóanyag](stream-analytics-edge.md) megtudhatja, hogyan telepítheti az eszközökre. 

## <a name="manage-the-job"></a>A feladat kezeléséhez 

Megtekintheti a feladat és a Server Explorerből feladatábrában állapotát. A **Stream Analytics** a **Server Explorer**, bontsa ki az előfizetést és az erőforráscsoportot, amelybe telepítette a Stream Analytics Edge-feladatban. Megtekintheti a MyASAEdgejob állapotú **létrehozva**. Bontsa ki a projekt csomópontra, és kattintson duplán az, hogy a feladat nézet megnyitása.

![Server explorer feladat felügyeleti beállítások](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A feladatok megtekintése ablakot biztosít műveleteket, mint a feladat frissítése, a feladat törlése, és nyissa meg a feladat az Azure Portalról.

![Feladatábra és egyéb lehetőségek Visual studióban](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>További lépések

* [További információ az Azure IoT Edge-ben](../iot-edge/about-iot-edge.md)
* [Az IoT Edge-oktatóanyag ASA](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visszajelzés küldése a csapatnak, használja a felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
