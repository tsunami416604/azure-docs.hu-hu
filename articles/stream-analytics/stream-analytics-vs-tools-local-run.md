---
title: Tesztelje az Azure Stream Analytics lekérdezéseit helyileg a Visual Studióban
description: Ez a cikk ismerteti, hogyan tesztelheti a lekérdezéseket helyileg az Azure Stream Analytics-eszközök a Visual Studio.This article describes how to test queries local with Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834909"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>A Test Stream Analytics lekérdezései helyileg a Visual Studióval

Az Azure Stream Analytics-eszközök kela Visual Studio számára a Stream Analytics-feladatok helyi teszteléséhez mintaadatokkal vagy [élő adatokkal.](stream-analytics-live-data-local-testing.md) 

Ebből a [rövid útmutatóból](stream-analytics-quick-create-vs.md) megtudhatja, hogyan hozhat létre Stream Analytics-feladatot a Visual Studióval.

## <a name="test-your-query"></a>A lekérdezés tesztelése

Az Azure Stream Analytics-projektben kattintson duplán a **Script.asaql** elemre a parancsfájl szerkesztőben való megnyitásához. A lekérdezés összeállításával ellenőrizheti, hogy vannak-e szintaktikai hibák. A lekérdezésszerkesztő támogatja az IntelliSense-t, a szintaktikai színezést és a hibajelölőt.

![Lekérdezésszerkesztő](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Helyi bemenet hozzáadása

Ha a lekérdezést helyi statikus adatokalapján szeretné érvényesíteni, kattintson a jobb gombbal a bemenetre, és válassza **a Helyi bemenet hozzáadása parancsot.**
   
![Helyi bemenet hozzáadása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Az előugró ablakban válassza ki a mintaadatokat a helyi elérési útról, és **mentse a lehetőséget.**
   
![Helyi bemenet hozzáadása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
A **local_EntryStream.json** nevű fájl automatikusan hozzáadódik a bemeneti mappához.
   
![Helyi bemeneti mappa fájllistája](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Válassza a **Futtatás helyileg** lehetőséget a lekérdezésszerkesztőben. Vagy nyomja meg az F5 billentyűt.
   
![Futtatás helyileg](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A kimenet közvetlenül a Visual Studio-ból tekinthető meg táblázatformátumban.

![Kimenet táblázat formátumban](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

A kimeneti útvonalat a konzol kimenetéről találja. Az eredménymappa megnyitásához nyomja meg bármelyik billentyűt.
   
![Helyi futtatás](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Ellenőrizze az eredményeket a helyi mappában.
   
![Helyi mappa eredménye](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Mintabevitel
A bemeneti forrásokból egy helyi fájlba is gyűjthet mintabemeneti adatokat. Kattintson a jobb gombbal a bemeneti konfigurációs fájlra, és válassza **a Mintaadatok parancsot.** 

![Adatmintavétel](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Csak az Event Hubs vagy az IoT Hubs adatfolyam-továbbítási. Más bemeneti források nem támogatottak. Az előugró párbeszédpanelen töltse ki a helyi elérési utat a mintaadatok mentéséhez, és válassza a **Minta**lehetőséget.

![Mintaadatok konfigurálása](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
A folyamat a **Kimenet** ablakban látható. 

![Mintaadatok kimenete](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Az Azure Stream Analytics-feladatok megtekintése a Visual Studióval](stream-analytics-vs-tools.md)
* [Élő adatok helyi tesztelése az Azure Stream Analytics-eszközök kel a Visual Studio számára (előzetes verzió)](stream-analytics-live-data-local-testing.md)
* [Oktatóanyag: Azure Stream Analytics-feladat üzembe helyezése CI/CD-vel az Azure DevOps használatával](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)
