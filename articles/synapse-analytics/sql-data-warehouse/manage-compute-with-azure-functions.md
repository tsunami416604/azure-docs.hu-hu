---
title: 'Oktatóanyag: számítások kezelése Azure Functions'
description: Az Azure functions használata az SQL-készlet számítási feladatainak kezeléséhez az Azure szinapszis Analyticsben.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 60e79ecd4148829c38b237c0e28d60796e84ac01
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543656"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>A számítási erőforrások kezelése Azure Functions használatával az Azure szinapszis Analytics SQL-készletben

Ez az oktatóanyag a Azure Functions használatával kezeli a számítási erőforrásokat egy SQL-készlethez az Azure szinapszis Analyticsben.

Az Azure-függvényalkalmazás SQL-készlettel való használatához létre kell hoznia egy [egyszerű szolgáltatásnevet](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az SQL Pool-példánnyal azonos előfizetéshez tartozó közreműködői hozzáféréssel.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Időzítő alapú skálázás üzembe helyezése Azure Resource Manager sablonnal

A sablon üzembe helyezéséhez a következő információk szükségesek:

- Azon erőforráscsoport neve, amelyben az SQL-készlet példánya szerepel
- Annak a kiszolgálónak a neve, amelyen az SQL-készlet példánya található
- Az SQL-készlet példányának neve
- Az Azure Active Directory bérlőazonosítója (Directory-azonosító)
- Előfizetés azonosítója
- Egyszerű szolgáltatás alkalmazásazonosítója
- Egyszerű szolgáltatás titkos kulcsa

Ha már rendelkezik a fenti információkkal, telepítse a következő sablont:

[![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

A sablon üzembe helyezésekor három új erőforrást talál: egy ingyenes Azure App Service csomagot, egy fogyasztási alapú függvényalkalmazás tervet, valamint egy olyan Storage-fiókot, amely kezeli a naplózást és a műveleti várólistát. Olvassa el a többi szakaszt, ha meg szeretné tudni, hogyan módosíthatja az üzembe helyezett függvényeket az igényeinek megfelelően.

## <a name="change-the-compute-level"></a>A számítási szint módosítása

1. Navigáljon a Függvényalkalmazás szolgáltatást. Ha az alapértelmezett értékekkel helyezte üzembe a sablont, a szolgáltatás neve *DWOperations*. A Függvényalkalmazás megnyitása után megfigyelheti, hogy a rendszer öt függvényt helyezett üzembe a Függvényalkalmazás szolgáltatásban.

   ![Sablonnal üzembe helyezett függvények](./media/manage-compute-with-azure-functions/five-functions.png)

2. Válassza ki a *DWScaleDownTrigger* vagy a *DWScaleUpTrigger* elemet attól függően, hogy a vertikális fel- vagy leskálázás idejét szeretné módosítani. A legördülő menüben válassza az integrálás lehetőséget.

   ![Integrálás kiválasztása a függvényhez](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Jelenleg a *%ScaleDownTime%* vagy a *%ScaleUpTime%* értéknek kell megjelennie. Ezek az értékek azt jelzik, hogy az ütemezés az [Alkalmazásbeállításokban](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) beállított értékeken alapul. Egyelőre figyelmen kívül hagyhatja ezt az értéket, és a következő lépések alapján módosíthatja az ütemtervet a kívánt időpontra.

4. Az ütemezési területen adja meg az SQL Data Warehouse vertikális felskálázásának gyakoriságát CRON-kifejezésként.

   ![Függvény ütemezésének módosítása](./media/manage-compute-with-azure-functions/change-schedule.png)

   A `schedule` értéke egy [CRON-kifejezés](https://en.wikipedia.org/wiki/Cron#CRON_expression), amely az alábbi hat mezőt tartalmazza:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Például a *"0 30 9 * * * 1-5"* egy-egy triggert tükröz minden hétköznapon 9 órakor. További információért tekintse meg az Azure Functions [ütemezési példákat](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) ismertető leírását.

## <a name="change-the-time-of-the-scale-operation"></a>A skálázási művelet időpontjának módosítása

1. Navigáljon a Függvényalkalmazás szolgáltatást. Ha az alapértelmezett értékekkel helyezte üzembe a sablont, a szolgáltatás neve *DWOperations*. A Függvényalkalmazás megnyitása után megfigyelheti, hogy a rendszer öt függvényt helyezett üzembe a Függvényalkalmazás szolgáltatásban.

2. Válassza ki a *DWScaleDownTrigger* vagy a *DWScaleUpTrigger* elemet attól függően, hogy a vertikális fel- vagy leskálázás számítási értékét szeretné módosítani. A függvények kiválasztásakor az ablaktáblán az *index.js* fájlnak kell megjelennie.

   ![A függvényhez tartozó eseményindító számítási szintjének módosítása](././media/manage-compute-with-azure-functions/index-js.png)

3. Módosítsa a *ServiceLevelObjective* értékét a kívánt szintre, és kattintson a mentés gombra. Ez az érték az a számítási szint, amelyet az adatraktár-példány az integráció szakaszban meghatározott ütemterv alapján fog méretezni.

## <a name="use-pause-or-resume-instead-of-scale"></a>Szüneteltetés vagy folytatás használata méretezés helyett

Alapértelmezés szerint jelenleg a *DWScaleDownTrigger* és a *DWScaleUpTrigger* függvények vannak bekapcsolva. Ha a felfüggesztés és a folytatás funkciót szeretné használni helyettük, engedélyezze a *DWPauseTrigger* vagy a *DWResumeTrigger* függvényt.

1. Lépjen a Függvények ablaktáblára.

   ![Függvények ablaktábla](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Kattintson az engedélyezni kívánt eseményindítók melletti csúszkás kapcsolóra.

3. Lépjen az adott eseményindító *Integrálás* lapjára az ütemezés módosításához.

   > [!NOTE]
   > A skálázási eseményindítók és a szüneteltetési/folytatási eseményindítók közötti funkcionális különbség a várólistára küldött üzenet. További információkért lásd: [új trigger függvény hozzáadása](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Új eseményindító függvény hozzáadása

A sablon jelenleg csak két méretezési függvényt tartalmaz. Ezekkel a függvényekkel a nap folyamán csak egyszer és egyszer lehet méretezni. Ha részletesebb szabályozásra van szükség, például napi többszöri méretezésre vagy a hétvégén eltérő skálázási viselkedésre van szüksége, újabb triggert kell hozzáadnia.

1. Hozzon létre egy új üres függvényt. Kattintson a *+* függvények helye melletti gombra a Function sablon panel megjelenítéséhez.

   ![Új függvény létrehozása](./media/manage-compute-with-azure-functions/create-new-function.png)

2. A nyelv területen válassza a *JavaScript*lehetőséget, majd válassza a *TimerTrigger*lehetőséget.

   ![Új függvény létrehozása](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nevezze el a függvényt, és állítsa be az ütemezést. A kép azt mutatja be, hogyan lehet függvényeket elindítani minden szombaton éjfélkor (pénteken késő este).

   ![Vertikális leskálázás szombaton](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Másolja ki az *index.js* tartalmát a többi eseményindító függvény egyikéből.

   ![Az index.js másolása](././media/manage-compute-with-azure-functions/index-js.png)

5. Állítsa a műveleti változót a kívánt viselkedésre a következőképpen:

   ```JavaScript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>Összetett ütemezés

Ez a szakasz röviden bemutatja, hogy mire van szükség a szüneteltetési, a folytatási és a méretezési funkciók összetettebb ütemezésének megkezdéséhez.

### <a name="example-1"></a>1\. példa

Vertikális felskálázás 8:00-kor DW600 értékre, és vertikális leskálázás 20:00-kor DW200 értékre, minden nap.

| Függvény  | Ütemezés     | Művelet                                |
| :-------- | :----------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| 2. függvény | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>2\. példa

Napi méretezés 08:00 és DW1000 között, a leskálázás egyszer, kor DW600 16:00-kor, és 10 – DW200.

| Függvény  | Ütemezés     | Művelet                                |
| :-------- | :----------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| 2. függvény | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| 3. függvény | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>3\. példa

Vertikális felskálázás 8:00-kor DW1000 értékre, és vertikális leskálázás 16:00-kor DW600 értékre a hétköznapokon. Szüneteltetés péntek 23:00-kor, folytatás hétfő reggel 7:00-kor.

| Függvény  | Ütemezés       | Művelet                                |
| :-------- | :------------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| 2. függvény | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| 3. függvény | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| 4. függvény | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>További lépések

További információk az [időzítő által aktivált](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure-függvényekről.

Az SQL Pool [Samples repository](https://github.com/Microsoft/sql-data-warehouse-samples)kifizetése.
