---
title: Az Azure Stream Analytics-feladatok megtekintése a Visual Studióban
description: Ez a cikk ismerteti a Stream Analytics-feladatok megtekintése a Visual Studióban.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: dba0474e83c886edcad1101083e45ff39ee222dd
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300223"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése

Az Azure Stream Analytics tools for Visual Studio megkönnyíti a fejlesztők közvetlenül az IDE-ből a Stream Analytics-feladatok kezelésére. Az Azure Stream Analytics tools a következőket teheti:
- [Új feladat létrehozása](stream-analytics-quick-create-vs.md)
- Indítsa el, állítsa le, és [feladatok figyelése](stream-analytics-monitor-jobs-use-vs.md)
- Feladat eredményeinek ellenőrzése
- Meglévő feladatok exportálása-projektbe
- Bemeneti és kimeneti kapcsolatok tesztelése
- [Lekérdezések futtatása helyileg](stream-analytics-vs-tools-local-run.md)

Ismerje meg, hogyan [az Azure Stream Analytics tools for Visual Studio telepítése](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Felfedezése a feladatnézetben

A feladat nézet segítségével dolgozhat a Visual Studióból az Azure Stream Analytics-feladatok.

### <a name="open-the-job-view"></a>A feladat nézet megnyitása

1. A **Server Explorer**válassza **Stream Analytics-feladatok** majd **frissítése**. A feladat meg kell jelennie a **Stream Analytics-feladatok**.

    ![Stream Analytics server explorer listája](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Bontsa ki a projekt csomópontra, majd kattintson duplán a a **Feladatnézetben** csomóponton a feladat nézet megnyitásához.
    
   ![Kibontott projekt csomópont](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Elindíthatja és leállíthatja a feladatot

Az Azure Stream Analytics-feladatok teljes mértékben felügyelhető a feladatok nézetben a Visual Studióban. A vezérlők használatával indítása, leállítása vagy egy feladat törlése.
    
   ![Stream Analytics-feladat vezérlők](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Feladat eredményeinek ellenőrzése

Stream Analytics tools for Visual Studio kimeneti előzetes jelenleg támogatja az Azure Data Lake Storage Storage és a blob storage. Eredmény megtekintéséhez, egyszerűen kattintson duplán a feladatábrában kimeneti csomópontján **Feladatnézetben** , és adja meg a megfelelő hitelesítő adatokat.

   ![Stream Analytics-feladat blob kimenete](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>A projekt exportálási feladatokhoz

Kétféleképpen exportálhat egy meglévő feladat egy projektet.

1. A **Server Explorer**, a Stream Analytics-feladatok csomópont alatt, kattintson a jobb gombbal a projekt csomópontra. Válassza ki **exportálás a Stream Analytics új projekt**.
    
   ![Feladat exportálása a projekthez](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Megjelenik a létrehozott projekt **Megoldáskezelőben**.
    
   ![Megoldáskezelő](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. A feladatok nézetben jelölje ki **készítése a projekt**.
    
   ![Projekt létrehozása a feladat megtekintése](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Kapcsolatok tesztelése

A bemeneti és kimeneti kapcsolatok tesztelhető a **feladat nézet** az egyik lehetőség kiválasztásával a **kapcsolat tesztelése** legördülő listából.

   ![Teszt kapcsolat legördülő menü](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

A **kapcsolat tesztelése** eredmények megjelennek a **kimeneti** ablak.

   ![Tesztkapcsolat eredménye](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>További lépések

* [Figyelése és felügyelete az Azure Stream Analytics-feladatok a Visual Studio használatával](stream-analytics-monitor-jobs-use-vs.md)
* [Gyors útmutató: Hozzon létre egy Stream Analytics-feladatot a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Oktatóanyag: Az Azure Stream Analytics-feladat CI/CD Azure folyamatok használatával való üzembe helyezése](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)
