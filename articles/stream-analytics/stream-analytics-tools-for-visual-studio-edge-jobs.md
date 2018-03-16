---
title: "A folyamatos integrációt és telepítést folyamat létrehozásához használja a Stream Analytics Visual Studio eszközök |} Microsoft Docs"
description: "Az oktatóanyag segítséget nyújt a Stream Analytics fejleszthet, hibakeresés, és a Stream Analytics peremhálózati feladatok létrehozása a Visual Studio eszközök."
keywords: "a Visual studio NuGet, a DevOps, a peremhálózati feladatok, a Stream analytics"
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/13/2018
ms.author: sujie
ms.openlocfilehash: 9362b201fbabc9f8f43647dfd8ac62986b5b6790
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="develop-stream-analytics-edge-jobs-by-using-visual-studio-tools"></a>Stream Analytics peremhálózati feladatok fejlesztéséhez a Visual Studio eszközök használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan használja a Stream Analytics tools for Visual Studio, hibakeresés, és a Stream Analytics peremhálózati feladatok létrehozása. Miután hoz létre, és tesztelje a feladat, lépjen az Azure portálra, telepíteni kell az eszközöket. 

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek teljesülését, az oktatóanyag teljesítéséhez szüksége:

* Telepítés [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015-öt](https://www.visualstudio.com/vs/older-downloads/), vagy [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott.  

* Kövesse a [telepítési utasításokat](stream-analytics-tools-for-visual-studio-edge-jobs.md) Stream Analytics-eszközök telepítése a Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>A Stream Analytics Edge-projekt létrehozása 

Válassza ki a Visual Studio eszközből **fájl** > **új** > **projekt**. Keresse meg a **sablonok** a bal oldali listában > bontsa ki a **Azure Stream Analytics** > **Stream Analytics peremhálózati**  >   **Az Azure Stream Analytics él alkalmazás**. Adja meg a projektet, majd válassza ki a nevét, helyét és a megoldás nevét **OK**.

![Új Edge-projekt](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

A projekt lekérdezi a létrehozása után nyissa meg a **Megoldáskezelőben** megtekintése a mappahierarchiában.

![Solution explorer megtekintése](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Válassza ki a megfelelő előfizetés

1. A Visual Studio **nézet** menü **Server Explorer**.  

2. Kattintson a jobb gombbal **Azure** > Válasszon **kapcsolódás a Microsoft Azure-előfizetés** >, majd jelentkezzen be az Azure-fiókjával.

## <a name="define-inputs"></a>Bemenetek megadása

1. Az a **Megoldáskezelőben**, bontsa ki a **bemenetek** csomópont kell megjelennie nevű bemeneti **EdgeInput.json**. A beállítások megtekintéséhez kattintson rá duplán.  

2. Győződjön meg arról, hogy forrástípus beállításai **adatfolyam** > forrás beállítása **peremhálózati Hub** > esemény szerializálási formátuma **Json** > ,éskódolás**UTF8**. Másik lehetőségként át lehet nevezni a **bemeneti Alias**, most hagyja, mert ehhez a példához. Abban az esetben, ha a bemeneti áljel átnevezéséhez használja a lekérdezés meghatározásakor a megadott néven. A beállítások mentéséhez válassza a **Mentés** lehetőséget.  
   ![Bemeneti konfiguráció](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Kimenetek meghatározása

1. Az a **Megoldáskezelőben**, bontsa ki a **kimenetek** csomópont kimenetet kell látnia egy nevű **EdgeOutput.json**. A beállítások megtekintéséhez kattintson rá duplán.  

2. Győződjön meg arról, hogy a fogadó jelölje be van állítva **peremhálózati Hub** > esemény szerializálási formátuma **Json** > kódolás értékre van állítva, és **UTF8** > formátuma és  **A tömb**. Másik lehetőségként át lehet nevezni a **kimeneti Alias**, most hagyja, mert ehhez a példához. Abban az esetben, ha a kimeneti alias átnevezéséhez használja a lekérdezés meghatározásakor a megadott néven. A beállítások mentéséhez válassza a **Mentés** lehetőséget. 
   ![Kimeneti konfiguráció](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Átalakítás megadása

A peremhálózati környezetek telepítése a Stream Analytics-feladatok támogatja a legtöbb [Stream Analytics lekérdezési nyelv hivatkozás](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), azonban a következő műveleteket még nem támogatottak a peremhálózati feladatok: 


|**Kategória**  | **Parancs**  |
|---------|---------|
|A földrajzi operátorok |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Egyéb operátorok | <ul><li>A PARTÍCIÓ ÁLTAL</li><li>TIMESTAMP TÍPUSÚ OVER ÁLTAL</li><li>KÜLÖNBÖZŐ</li><li>A COUNT operátorban található kifejezés paraméter</li><li>A dátum és időpont függvényeinek mikroszekundum</li><li>JavaScript uda-Értékeket (Ez a szolgáltatás még mindig van a felhőbe telepíteni feladatok előzetes verzió)</li></ul>   |

Ha egy peremhálózati feladatot hoz létre a portálon, a fordítóprogram automatikusan figyelmeztet, ha nem használ egy támogatott operátort.

A Visual studióból, adja meg a következő átalakító lekérdezés a lekérdezés-szerkesztő (**script.asaql fájl**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>A projekt helyi tesztelése

A lekérdezés tesztelése helyileg, akkor kell töltse fel a mintaadatokat. Mintaadatokat kérhető le a regisztrációs adatok letöltése a [GitHub-tárházban](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) , és mentse a helyi számítógépen. 

1. Töltse fel a mintaadatok, > kattintson a jobb gombbal **EdgeInput.json** fájl > Válasszon **helyi bemenet hozzáadása**  

2. Az előugró ablakban > **Tallózás** a helyi elérési útról a mintaadatok > Válasszon **mentése**.
   ![Helyi bemeneti konfiguráció](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Nevű fájl **local_EdgeInput.json** automatikusan hozzáadja a bemeneti adatok mappába.  
4. futtassa helyileg, vagy küldje el az Azure-bA. A lekérdezés tesztelése > Válasszon **futtassa helyileg**.  
   ![Futtatási beállítások](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. A parancssori ablakot a feladat állapotát jeleníti meg. Amikor a feladat sikeresen lefutott, a projekt mappájának elérési "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" létrehoz egy mappát, amely a következőhöz hasonló "2018-02-23-11-31-42". Keresse meg a mappa elérési útja az eredmények megtekintése a helyi mappában:

   Jelentkezzen be az Azure-portálon is, és győződjön meg arról, hogy létrejött-e a feladatot. 

   ![Eredmény mappa](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Az Azure-bA a feladat elküldéséhez

1. Mielőtt az Azure-bA a feladat elküldése, csatlakoznia kell az Azure-előfizetéshez. Nyissa meg **Server Explorer** > kattintson a jobb gombbal **Azure** > **kapcsolódás a Microsoft Azure-előfizetés** > jelentkezzen be az Azure-előfizetéshez.  

2. Elküldeni a feladatot az Azure-ba, keresse meg a lekérdezés-szerkesztő > Válasszon **küldje el a következőt az Azure-bA**.  

3. Megjelenik egy előugró ablak, választhat, ha egy meglévő biztonsági feladat frissítése, vagy hozzon létre egy újat. Amikor frissít egy meglévő feladat, azzal lecseréli az összes feladat konfigurációt, ebben a forgatókönyvben, egy új feladat fog közzétenni. Válassza ki **hozzon létre egy új Azure Stream Analytics-feladat** > adjon meg egy nevet az alábbihoz hasonló munkahelye **MyASAEdgeJob** > válassza a kötelező **előfizetés**, **Erőforráscsoport**, és **hely** > Válasszon **nyújt**.

   ![Küldje el az Azure-bA](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Most a Stream Analytics peremhálózati feladat létrehozása után olvassa el a [feladatok futtatása az IoT-Edge oktatóanyag](stream-analytics-edge.md) megtudhatja, hogyan telepítheti az eszközökre. 

## <a name="manage-the-job"></a>A feladat kezeléséhez 

A feladat és a feladat ábra a Server Explorer állapotát tekintheti meg. Az a **Server Explorer** > **Stream Analytics** > bontsa ki az előfizetés és az erőforráscsoport, amelybe telepítette a peremhálózati feladat > tekintheti meg a MyASAEdgejob állapotú **Létrehozott**. A feladat csomópontot, és úgy, hogy a feladat nézet megnyitásához kattintson rá duplán.

![Server explorer beállításai](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A feladatok megtekintése ablakot biztosít műveletek, például a frissítés, a feladat törli a feladatot, a feladat megnyitása az Azure portál stb.

![Feladat ábra és egyéb beállítások](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>További lépések

* [További információ az Azure Iot oldal](../iot-edge/how-iot-edge-works.md)
* [Az IoT-Edge oktatóanyag ASA](../iot-edge/tutorial-deploy-stream-analytics.md)
* [A csapat a felmérés használatával küldhet visszajelzést](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
