---
title: 'Oktatóanyag: Az Azure Functions az Azure SQL Data Warehouse számítási kezelése |} Microsoft Docs'
description: Az Azure Functions használata az adattárház számításainak kezelésére.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 48428ef329de4719a25afd20c21ac102bba540a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Azure-függvények kezelése számítási erőforrásokat az Azure SQL Data Warehouse

Ez az oktatóanyag az Azure Functions használja az Azure SQL Data Warehouse az adatok a számítási erőforrások kezeléséhez.

Az Azure-függvényalkalmazás és az SQL Data Warehouse együttes használatához létre kell hoznia egy közreműködői hozzáféréssel rendelkező [egyszerű szolgáltatásfiókot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) ugyanahhoz az előfizetéshez, amelyhez az adattárház-példány is tartozik. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Időzítő alapú méretezést, Azure Resource Manager-sablon telepítése

A sablon telepítéséhez, a következő információk szükségesek:

- Az SQL DW-példányt tartalmazó erőforráscsoport neve
- Az SQL DW-példányt tartalmazó logikai kiszolgáló neve
- Az SQL DW-példány neve
- Az Azure Active Directory bérlőazonosítója (Directory-azonosító)
- Előfizetés azonosítója 
- Egyszerű szolgáltatás alkalmazásazonosítója
- Egyszerű szolgáltatás titkos kulcsa

Miután a fenti adatokat, a sablon telepítéséhez:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Ha a sablon telepítése után, látnia kell a három új erőforrások: egy ingyenes Azure App Service-csomagot, a fogyasztás alapján függvény alkalmazás tervek és a storage-fiók, amely a naplózási és a műveleti várólista kezeli. Olvassa el a többi szakaszt, ha meg szeretné tudni, hogyan módosíthatja az üzembe helyezett függvényeket az igényeinek megfelelően.

## <a name="change-the-compute-level"></a>A számítási szintjének módosítása

1. Navigáljon a Függvényalkalmazás szolgáltatást. Ha az alapértelmezett értékekkel helyezte üzembe a sablont, a szolgáltatás neve *DWOperations*. A Függvényalkalmazás megnyitása után megfigyelheti, hogy a rendszer öt függvényt helyezett üzembe a Függvényalkalmazás szolgáltatásban. 

   ![Sablonnal üzembe helyezett függvények](media/manage-compute-with-azure-functions/five-functions.png)

2. Válassza ki a *DWScaleDownTrigger* vagy a *DWScaleUpTrigger* elemet attól függően, hogy a vertikális fel- vagy leskálázás idejét szeretné módosítani. A legördülő menüben válassza ki az integráció.

   ![Integrálás kiválasztása a függvényhez](media/manage-compute-with-azure-functions/select-integrate.png)

3. Jelenleg a *%ScaleDownTime%* vagy a *%ScaleUpTime%* értéknek kell megjelennie. Ezek az értékek azt jelzik, hogy az ütemezés az [Alkalmazásbeállításokban][Application Settings] beállított értékeken alapul. Most figyelmen kívül hagyhatja ezt az értéket, és módosítani az előnyben részesített idő alapján a következő lépéseket.

4. Az ütemezési területen adja meg az SQL Data Warehouse vertikális felskálázásának gyakoriságát CRON-kifejezésként. 

  ![Függvény ütemezésének módosítása](media/manage-compute-with-azure-functions/change-schedule.png)

  A `schedule` értéke egy [CRON-kifejezés](http://en.wikipedia.org/wiki/Cron#CRON_expression), amely az alábbi hat mezőt tartalmazza: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Például *"0 30 9 ** 1-5"* tükrözné eseményindító minden hétköznap, 9:30 -kor. További információért tekintse meg az Azure Functions [ütemezési példákat][schedule examples] ismertető leírását.


## <a name="change-the-time-of-the-scale-operation"></a>Az időt a méretezési művelet

1. Navigáljon a Függvényalkalmazás szolgáltatást. Ha az alapértelmezett értékekkel helyezte üzembe a sablont, a szolgáltatás neve *DWOperations*. A Függvényalkalmazás megnyitása után megfigyelheti, hogy a rendszer öt függvényt helyezett üzembe a Függvényalkalmazás szolgáltatásban. 

2. Válassza ki a *DWScaleDownTrigger* vagy a *DWScaleUpTrigger* elemet attól függően, hogy a vertikális fel- vagy leskálázás számítási értékét szeretné módosítani. A függvények kiválasztásakor az ablaktáblán az *index.js* fájlnak kell megjelennie.

   ![A függvényhez tartozó eseményindító számítási szintjének módosítása](media/manage-compute-with-azure-functions/index-js.png)

3. Módosítsa a *ServiceLevelObjective* értékét a kívánt szintre, és kattintson a mentés gombra. Ez az érték a számítási szintet, az az adatraktár példánya a integráció szakaszban meghatározott ütemezés szerint lesz skálázva.

## <a name="use-pause-or-resume-instead-of-scale"></a>Szüneteltetés vagy folytatás használata méretezés helyett 

Alapértelmezés szerint jelenleg a *DWScaleDownTrigger* és a *DWScaleUpTrigger* függvények vannak bekapcsolva. Ha a felfüggesztés és a folytatás funkciót szeretné használni helyettük, engedélyezze a *DWPauseTrigger* vagy a *DWResumeTrigger* függvényt.

1. Lépjen a Függvények ablaktáblára.

   ![Függvények ablaktábla](media/manage-compute-with-azure-functions/functions-pane.png)



2. Kattintson az engedélyezni kívánt eseményindítók melletti csúszkás kapcsolóra.

3. Lépjen az adott eseményindító *Integrálás* lapjára az ütemezés módosításához.

   > [!NOTE]
   > A méretezési eseményindítók és a szüneteltet/eseményindítók közötti funkcionális különbség a várólistára küldött üzenet. További információkért lásd: [hozzáadása egy új funkció][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Új eseményindító függvény hozzáadása

A sablon jelenleg csak két méretezési függvényt tartalmaz. Ezeket a funkciókat, a nap folyamán csak méretezheti le egyszer, és egyszer fel. Részletesebben vezérelhető, például naponta több alkalommal skálázás vagy másik skálázási viselkedés gyakorló a hétvégeken, az kell hozzáadnia egy másik eseményindító.

1. Hozzon létre egy új üres függvényt. Válassza ki a *+* közelében a funkciók helyét a függvény sablon ablaktábla megjelenítése gombra.

   ![Új függvény létrehozása](media/manage-compute-with-azure-functions/create-new-function.png)

2. A Nyelv területen válassza a *Javascript* elemet, majd a *TimerTrigger* elemet.

   ![Új függvény létrehozása](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nevezze el a függvényt, és állítsa be az ütemezést. A kép azt mutatja be, hogyan lehet függvényeket elindítani minden szombaton éjfélkor (pénteken késő este).

   ![Vertikális leskálázás szombaton](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Másolja ki az *index.js* tartalmát a többi eseményindító függvény egyikéből.

   ![Az index.js másolása](media/manage-compute-with-azure-functions/index-js.png)

5. Állítsa be a művelet változót a kívánt viselkedés az alábbiak szerint:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Összetett ütemezés

Ez a szakasz röviden bemutatja szükséges Szünet ütemezése összetett, folytatásához, illetve léptékezési képességekre.

### <a name="example-1"></a>1. példa:

Vertikális felskálázás 8:00-kor DW600 értékre, és vertikális leskálázás 20:00-kor DW200 értékre, minden nap.

| Függvény  | Ütemezés     | Művelet                                |
| :-------- | :----------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| 2. függvény | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>2. példa 

8 órakor történő DW1000, felskálázott napi du. 4: DW600 egyszerre csökkentheti, vagy a DW200 10 du. csökkentheti.

| Függvény  | Ütemezés     | Művelet                                |
| :-------- | :----------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| 2. függvény | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| 3. függvény | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>3. példa: 

Vertikális felskálázás 8:00-kor DW1000 értékre, és vertikális leskálázás 16:00-kor DW600 értékre a hétköznapokon. Szüneteltetés péntek 23:00-kor, folytatás hétfő reggel 7:00-kor.

| Függvény  | Ütemezés       | Művelet                                |
| :-------- | :------------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| 2. függvény | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| 3. függvény | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| 4. függvény | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>További lépések

További információk az [időzítő által aktivált](../azure-functions/functions-create-scheduled-function.md) Azure-függvényekről.

Tekintse meg az SQL Data Warehouse [mintaadattárát](https://github.com/Microsoft/sql-data-warehouse-samples).



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function