---
title: "Azure SQL Data Warehouse – Az SQL Data Warehouse számítási szintjeinek automatizálása az Azure Functions használatával | Microsoft Docs"
description: "Az Azure Functions használata az adattárház számításainak kezelésére."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: e2138f26a78338406b466bdd2aa6e756e602726d
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>Az SQL DW számítási szintjeinek automatizálása az Azure Functions használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Functionst az Azure SQL Data Warehouse számítási szintjeinek kezelésére. Az architektúrák használata a [rugalmasságra optimalizált][Performance Tiers] SQL Data Warehouse esetén ajánlott.

Az Azure-függvényalkalmazás és az SQL Data Warehouse együttes használatához létre kell hoznia egy közreműködői hozzáféréssel rendelkező [egyszerű szolgáltatásfiókot](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) ugyanahhoz az előfizetéshez, amelyhez az adattárház-példány is tartozik. 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>Időzítőalapú skálázó üzembe helyezése Azure Resource Manager-sablonnal

A sablon üzembe helyezéséhez az alábbi adatokra lesz szüksége:

- Az SQL DW-példányt tartalmazó erőforráscsoport neve
- Az SQL DW-példányt tartalmazó logikai kiszolgáló neve
- Az SQL DW-példány neve
- Az Azure Active Directory bérlőazonosítója (Directory-azonosító)
- Előfizetés azonosítója 
- Egyszerű szolgáltatás alkalmazásazonosítója
- Egyszerű szolgáltatás titkos kulcsa

Ha megvannak a fenti adatok, helyezze üzembe az alábbi sablont:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

A sablon üzembe helyezése után három új erőforrást fog találni: egy ingyenes Azure App Service-csomagot, egy használatalapú Függvényalkalmazás-csomagot, és egy tárfiókot, amely a naplózást és a műveletek üzenetsorát fogja kezelni. Olvassa el a többi szakaszt, ha meg szeretné tudni, hogyan módosíthatja az üzembe helyezett függvényeket az igényeinek megfelelően.

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>A vertikális fel- vagy leskálázás számítási szintjének módosítása

1. Navigáljon a Függvényalkalmazás szolgáltatást. Ha az alapértelmezett értékekkel helyezte üzembe a sablont, a szolgáltatás neve *DWOperations*. A Függvényalkalmazás megnyitása után megfigyelheti, hogy a rendszer öt függvényt helyezett üzembe a Függvényalkalmazás szolgáltatásban. 

   ![Sablonnal üzembe helyezett függvények](media/manage-compute-with-azure-functions/five-functions.png)

2. Válassza ki a *DWScaleDownTrigger* vagy a *DWScaleUpTrigger* elemet attól függően, hogy a vertikális fel- vagy leskálázás idejét szeretné módosítani. A legördülő listában válassza ki az Integrálás lehetőséget.

   ![Integrálás kiválasztása a függvényhez](media/manage-compute-with-azure-functions/select-integrate.png)

3. Jelenleg a *%ScaleDownTime%* vagy a *%ScaleUpTime%* értéknek kell megjelennie. Ezek az értékek azt jelzik, hogy az ütemezés az [Alkalmazásbeállításokban][Application Settings] beállított értékeken alapul. Ezt egyelőre hagyja figyelmen kívül, és módosítsa az ütemezést a kívánt időpontra a következő lépések alapján.

4. Az ütemezési területen adja meg az SQL Data Warehouse vertikális felskálázásának gyakoriságát CRON-kifejezésként. 

  ![Függvény ütemezésének módosítása](media/manage-compute-with-azure-functions/change-schedule.png)

  A `schedule` értéke egy [CRON-kifejezés](http://en.wikipedia.org/wiki/Cron#CRON_expression), amely az alábbi hat mezőt tartalmazza: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Például a *„0 30 9 * * 1-5”* egy olyan eseményindítót jelöl, amely minden hétköznap 9:30-kor fut. További információért tekintse meg az Azure Functions [ütemezési példákat][schedule examples] ismertető leírását.


### <a name="change-the-scale-up-or-scale-down-time"></a>A vertikális fel- vagy leskálázás időpontjának módosítása

1. Navigáljon a Függvényalkalmazás szolgáltatást. Ha az alapértelmezett értékekkel helyezte üzembe a sablont, a szolgáltatás neve *DWOperations*. A Függvényalkalmazás megnyitása után megfigyelheti, hogy a rendszer öt függvényt helyezett üzembe a Függvényalkalmazás szolgáltatásban. 

2. Válassza ki a *DWScaleDownTrigger* vagy a *DWScaleUpTrigger* elemet attól függően, hogy a vertikális fel- vagy leskálázás számítási értékét szeretné módosítani. A függvények kiválasztásakor az ablaktáblán az *index.js* fájlnak kell megjelennie.

   ![A függvényhez tartozó eseményindító számítási szintjének módosítása](media/manage-compute-with-azure-functions/index-js.png)

3. Módosítsa a *ServiceLevelObjective* értékét a kívánt szintre, és kattintson a mentés gombra. A rendszer erre a számítási szintre fogja méretezni az adattárház-példányt, az Integrálás szakaszban meghatározott ütemezés alapján.

### <a name="use-pause-or-resume-instead-of-scale"></a>Szüneteltetés vagy folytatás használata méretezés helyett 

Alapértelmezés szerint jelenleg a *DWScaleDownTrigger* és a *DWScaleUpTrigger* függvények vannak bekapcsolva. Ha a felfüggesztés és a folytatás funkciót szeretné használni helyettük, engedélyezze a *DWPauseTrigger* vagy a *DWResumeTrigger* függvényt.

1. Lépjen a Függvények ablaktáblára.

   ![Függvények ablaktábla](media/manage-compute-with-azure-functions/functions-pane.png)



2. Kattintson az engedélyezni kívánt eseményindítók melletti csúszkás kapcsolóra.

3. Lépjen az adott eseményindító *Integrálás* lapjára az ütemezés módosításához.

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>Új eseményindító függvény hozzáadása

A sablon jelenleg csak két méretezési függvényt tartalmaz. Ez azt jelenti, hogy egy nap csak egyszer van lehetősége vertikálisan felskálázni, és egyszer leskálázni. Ha részletesebb szabályozást szeretne, például napi többszöri vertikális leskálázást vagy más skálázási viselkedést a hétvégeken, létre kell hoznia még egy eseményindítót.

1. Hozzon létre egy új üres függvényt. Válassza a Függvények elem közelében található *+* gombot a függvénysablonok ablaktáblájának megjelenítéséhez.

   ![Új függvény létrehozása](media/manage-compute-with-azure-functions/create-new-function.png)

2. A Nyelv területen válassza a *Javascript* elemet, majd a *TimerTrigger* elemet.

   ![Új függvény létrehozása](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nevezze el a függvényt, és állítsa be az ütemezést. A kép azt mutatja be, hogyan lehet függvényeket elindítani minden szombaton éjfélkor (pénteken késő este).

   ![Vertikális leskálázás szombaton](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Másolja ki az *index.js* tartalmát a többi eseményindító függvény egyikéből.

   ![Az index.js másolása](media/manage-compute-with-azure-functions/index-js.png)

5. Állítsa a műveleti változót a kívánt viselkedésre a következőképpen:

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


### <a name="complex-scheduling"></a>Összetett ütemezés

Ez a szakasz röviden ismerteti, hogy mire van szükség a szüneteltetés, a folytatás és a méretezési képességek összetettebb ütemezéséhez.

#### <a name="example-1"></a>1. példa:

Vertikális felskálázás 8:00-kor DW600 értékre, és vertikális leskálázás 20:00-kor DW200 értékre, minden nap.

| Függvény  | Ütemezés     | Művelet                                |
| :-------- | :----------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| 2. függvény | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>2. példa 

Vertikális felskálázás 8:00-kor DW1000 értékre, vertikális leskálázás egyszer 16:00-kor DW600 értékre és 22:00-kor DW200 értékre, minden nap.

| Függvény  | Ütemezés     | Művelet                                |
| :-------- | :----------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| 2. függvény | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| 3. függvény | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>3. példa: 

Vertikális felskálázás 8:00-kor DW1000 értékre, és vertikális leskálázás 16:00-kor DW600 értékre a hétköznapokon. Szüneteltetés péntek 23:00-kor, folytatás hétfő reggel 7:00-kor.

| Függvény  | Ütemezés       | Művelet                                |
| :-------- | :------------- | :--------------------------------------- |
| 1. függvény | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| 2. függvény | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| 3. függvény | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| 4. függvény | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Következő lépések

További információk az [időzítő által aktivált](../azure-functions/functions-create-scheduled-function.md) Azure-függvényekről.

Tekintse meg az SQL Data Warehouse [mintaadattárát](https://github.com/Microsoft/sql-data-warehouse-samples).



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function

[Performance Tiers]: performance-tiers.md
