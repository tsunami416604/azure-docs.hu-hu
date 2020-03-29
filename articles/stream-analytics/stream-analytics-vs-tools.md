---
title: Az Azure Stream Analytics-feladatok megtekintése a Visual Studióban
description: Ismerje meg, hogyan tekintheti meg, indíthatja el és állíthatja le, tesztelheti a kapcsolatokat, ellenőrizheti az eredményeket, és hogyan exportálhatja az Azure Stream Analytics-feladatokat a Visual Studio használatával.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ad35ed342dfd40b98d61919749479ec5612cdd4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369626"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Az Azure Stream Analytics-feladatok megtekintése a Visual Studióval

A Visual Studio Azure Stream Analytics-eszközei megkönnyítik a fejlesztők számára, hogy közvetlenül az IDE-ből kezeljék streamanalytics-feladataikat. Az Azure Stream Analytics eszközeivel a következőket teheti:
- [Új munkahelyek létrehozása](stream-analytics-quick-create-vs.md)
- Feladatok indítása, leállítása és [figyelése](stream-analytics-monitor-jobs-use-vs.md)
- Feladat eredmények ellenőrzése
- Meglévő feladatok exportálása projektbe
- A bemeneti és kimeneti kapcsolatok tesztelése
- [Lekérdezések futtatása helyileg](stream-analytics-vs-tools-local-run.md)

Ismerje meg, hogyan telepítheti az [Azure Stream Analytics-eszközöket a Visual Studio számára.](stream-analytics-tools-for-visual-studio-install.md)

## <a name="explore-the-job-view"></a>A feladatnézet felfedezése

A feladatnézet segítségével a Visual Studio Azure Stream Analytics-feladataival kommunikálhat.

### <a name="open-the-job-view"></a>A feladatnézet megnyitása

1. A **Kiszolgálókezelőben**válassza a **Stream Analytics-feladatok lehetőséget,** majd a **Frissítés**lehetőséget. A feladatnak meg kell jelennie a **Stream Analytics-feladatok alatt.**

    ![A Stream Analytics kiszolgálókezelőinek listája](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Bontsa ki a feladatcsomópontot, és kattintson duplán a **Feladatnézet** csomópontra egy feladatnézet megnyitásához.
    
   ![Bővített feladatcsomópont](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Feladatok indítása és leállítása

Az Azure Stream Analytics-feladatok teljes mértékben kezelhetők a Visual Studio feladatnézetéből. A vezérlőkkel elindíthat, leállíthat vagy törölhet egy feladatot.
    
   ![Stream Analytics-feladatvezérlők](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Feladat eredmények ellenőrzése

A Visual Studio Stream Analytics-eszközei jelenleg támogatják az Azure Data Lake Storage és a blob storage kimeneti előnézetét. Az eredmény megtekintéséhez egyszerűen kattintson duplán a feladatdiagram kimeneti csomópontjára a **Feladat nézetben,** és adja meg a megfelelő hitelesítő adatokat.

   ![Stream Analytics-feladatblob-kimenet](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Feladatok exportálása projektbe

Egy meglévő feladatot kétféleképpen exportálhat egy projektbe.

1. A **Kiszolgálókezelőben**a Stream Analytics-feladatok csomópont alatt kattintson a jobb gombbal a feladatcsomópontra. Válassza **az Exportálás új streamelemzési projektbe lehetőséget.**
    
   ![Feladat exportálása projektbe](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    A létrehozott projekt megjelenik a **Megoldáskezelőben.**
    
   ![A megoldáskezelő](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. A feladatnézetben válassza a **Projekt létrehozása lehetőséget.**
    
   ![Projekt létrehozása feladatnézetből](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Kapcsolatok tesztelése

A bemeneti és kimeneti kapcsolatok a **Feladatnézetből** tesztelhetők, ha a **Kapcsolat tesztelése** legördülő menüben kiválasztegy beállítást.

   ![Kapcsolat tesztelése legördülő menü](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

A **tesztkapcsolat** eredményei a **Kimenet** ablakban jelennek meg.

   ![A kapcsolat teszteredményei](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics-feladatok figyelése és kezelése a Visual Studio használatával](stream-analytics-monitor-jobs-use-vs.md)
* [Rövid útmutató: Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Oktatóanyag: Azure Stream Analytics-feladat üzembe helyezése CI/CD-vel az Azure Pipelines használatával](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)
