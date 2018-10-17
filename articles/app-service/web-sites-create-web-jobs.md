---
title: Háttérfeladatok futtatása a WebJobs Azure App Service-ben
description: Útmutató a webjobs funkció használata háttérfeladatok futtatásához az Azure App Service web apps, API-alkalmazások vagy mobilalkalmazások.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga;msangapu;david.ebbo;suwatch;pbatum;naren.soni;
ms.openlocfilehash: 901cf32557e0a437e938ceb50ecd500c69c8c3be
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364030"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Háttérfeladatok futtatása a WebJobs Azure App Service-ben

## <a name="overview"></a>Áttekintés
Webjobs-feladatok csak a [Azure App Service](https://docs.microsoft.com/azure/app-service/) , amely lehetővé teszi egy program vagy parancsprogram futtatását egy webalkalmazás, API-alkalmazás vagy mobilalkalmazás ugyanabban a környezetben. Nincs WebJobs használatával további költség nélkül.

Ez a cikk bemutatja, hogyan helyezhet üzembe a WebJobs használatával a [az Azure portal](https://portal.azure.com) végrehajtható vagy parancsfájlokat feltölteni. Fejlesztés és a WebJobs üzembe helyezése a Visual Studio használatával kapcsolatos információkért lásd: [WebJobs üzembe helyezése a Visual Studio használatával](websites-dotnet-deploy-webjobs.md).

Számos programozási feladatok egyszerűbbé tétele az Azure WebJobs SDK használható WebJobs-feladatokkal. További információkért lásd: [Mi a WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Az Azure Functions egy másik módot nyújt programok és szkriptek futtatásához. Webjobs-feladatok és a Functions közötti összehasonlításáért lásd: [Flow, Logic Apps, Functions és WebJobs közötti](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webjobs-feladat típusa

A következő táblázat ismerteti a közötti különbségeket *folyamatos* és *aktivált* webjobs-feladatok.


|Folyamatos  |Triggerrel indított  |
|---------|---------|
| Kezdődik meg azonnal a webjobs-feladat jön létre. Ahhoz, hogy biztosítsa a feladat befejezési, a program vagy parancsfájl általában működik a végtelen ciklus belül. Ha a feladat, újraindíthatja azt. | Elindul, csak akkor, ha manuálisan vagy ütemezés szerint aktiválódik. |
| A webes alkalmazást futtató összes példánya fut. Egyetlen példány igény szerint korlátozhatja a webjobs-feladatot. |Az Azure által az egyetlen példányán fut a terheléselosztás.|
| Támogatja a távoli hibakeresésről. | Nem támogatja a távoli hibakeresésről.|

> [!NOTE]
> Webes alkalmazás időkorlát 20 perc inaktivitás után is. Csak a tényleges webalkalmazáshoz kérelmeket az időzítő alaphelyzetbe állítása. Az alkalmazás konfigurációját megtekintése az Azure Portalon, vagy a speciális eszközök helyhez-kérelem indítására (https:// < alkalmazás_neve >. scm.azurewebsites.net) nem az időzítő alaphelyzetbe. Ha az alkalmazás futása folyamatos vagy ütemezett webjobs-feladatok engedélyezése **Always On** annak érdekében, hogy a webjobs-feladatok megbízhatóan fusson. Ez a funkció csak a Basic, Standard és prémium szintű érhető el [tarifacsomagok](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>Szkriptek vagy programok használható fájltípusok

A következő fájltípusokat támogatja:

* .cmd, .bat, .exe (Windows cmd segítségével)
* .ps1 (a PowerShell használatával)
* .SH (Bash használatával)
* .php (a PHP használatával)
* .PY (a Python használatával)
* .js (Node.js használatával)
* a .jar (a Java használatával)

## <a name="CreateContinuous"></a> Egy folyamatos webjobs-feladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az a [az Azure portal](https://portal.azure.com), lépjen a a **App Service-ben** az App Service-webalkalmazás, API-alkalmazást, vagy az lapján.

2. Válassza ki **WebJobs**.

   ![Select WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Az a **WebJobs** lapon jelölje be **Hozzáadás**.

    ![Webjobs-feladat lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **a webjobs-feladat hozzáadása** a táblázatban megadott beállításokat.

   ![Webjobs-feladat oldal hozzáadása](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Name (Név)** | myContinuousWebJob | Az App Service-alkalmazáson belül egyedi neve. Betűvel vagy számmal kell kezdődnie, és más, nem tartalmazhat különleges karaktereket "-" és "_". |
   | **Fájl feltöltése** | ConsoleApp.zip | A *.zip* fájlt, amely tartalmazza a végrehajtható fájlt vagy parancsfájlt, valamint minden kiegészítő fájlt, a program vagy parancsfájl futtatásához szükséges. A támogatott végrehajtható vagy parancsfájlokat fájltípusokat szerepelnek a [támogatott fájltípusok](#acceptablefiles) szakaszban. |
   | **Típus** | Folyamatos | A [webjobs-feladat típusok](#webjob-types) korábban ebben a cikkben ismertetett. |
   | **Méretezés** | Többpéldányos Teljesítménymodulnál | Csak a folyamatos webjobs-feladatok számára érhető el. Meghatározza, hogy a program vagy parancsfájl fut-e az összes példányra, vagy egy példányt. Beállítás elérhető, több példányon nem vonatkozik az ingyenes és közös [tarifacsomagok](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Kattintson az **OK** gombra.

   Az új webjobs-feladat megjelenik-e a **WebJobs** lapot.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Egy folyamatos webjobs-feladat újraindítása vagy leállítása, kattintson a jobb gombbal a webjobs-feladatot a listában, és kattintson a **leállítása** vagy **Start**.

    ![Egy folyamatos webjobs-feladat leállítása](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> A manuálisan aktivált webjobs-feladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az a [az Azure portal](https://portal.azure.com), lépjen a a **App Service-ben** az App Service-webalkalmazás, API-alkalmazást, vagy az lapján.

2. Válassza ki **WebJobs**.

   ![Select WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Az a **WebJobs** lapon jelölje be **Hozzáadás**.

    ![Webjobs-feladat lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **a webjobs-feladat hozzáadása** a táblázatban megadott beállításokat.

   ![Webjobs-feladat oldal hozzáadása](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Name (Név)** | myTriggeredWebJob | Az App Service-alkalmazáson belül egyedi neve. Betűvel vagy számmal kell kezdődnie, és más, nem tartalmazhat különleges karaktereket "-" és "_".|
   | **Fájl feltöltése** | ConsoleApp.zip | A *.zip* fájlt, amely tartalmazza a végrehajtható fájlt vagy parancsfájlt, valamint minden kiegészítő fájlt, a program vagy parancsfájl futtatásához szükséges. A támogatott végrehajtható vagy parancsfájlokat fájltípusokat szerepelnek a [támogatott fájltípusok](#acceptablefiles) szakaszban. |
   | **Típus** | Triggerrel indított | A [webjobs-feladat típusok](#webjob-types) korábban ebben a cikkben ismertetett. |
   | **Eseményindítók** | Manuális | |

4. Kattintson az **OK** gombra.

   Az új webjobs-feladat megjelenik-e a **WebJobs** lapot.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. A webjobs-feladat futtatásához kattintson a jobb gombbal a listában a nevét, majd kattintson **futtatása**.
   
    ![WebJobs-feladat futtatása](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Az ütemezett webjobs-feladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az a [az Azure portal](https://portal.azure.com), lépjen a a **App Service-ben** az App Service-webalkalmazás, API-alkalmazást, vagy az lapján.

2. Válassza ki **WebJobs**.

   ![Select WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Az a **WebJobs** lapon jelölje be **Hozzáadás**.

   ![Webjobs-feladat lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **a webjobs-feladat hozzáadása** a táblázatban megadott beállításokat.

   ![Webjobs-feladat oldal hozzáadása](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Name (Név)** | myScheduledWebJob | Az App Service-alkalmazáson belül egyedi neve. Betűvel vagy számmal kell kezdődnie, és más, nem tartalmazhat különleges karaktereket "-" és "_". |
   | **Fájl feltöltése** | ConsoleApp.zip | A *.zip* fájlt, amely tartalmazza a végrehajtható fájlt vagy parancsfájlt, valamint minden kiegészítő fájlt, a program vagy parancsfájl futtatásához szükséges. A támogatott végrehajtható vagy parancsfájlokat fájltípusokat szerepelnek a [támogatott fájltípusok](#acceptablefiles) szakaszban. |
   | **Típus** | Triggerrel indított | A [webjobs-feladat típusok](#webjob-types) korábban ebben a cikkben ismertetett. |
   | **Eseményindítók** | Ütemezett | Megbízható működés az ütemezéshez, az Always On funkció engedélyezéséhez. Always On csak a Basic, Standard és prémium szintű díjcsomagok árából érhető el.|
   | **CRON-kifejezés** | 0 0/20 * * * * | [CRON-kifejezések](#cron-expressions) a következő szakasz ismerteti. |

4. Kattintson az **OK** gombra.

   Az új webjobs-feladat megjelenik-e a **WebJobs** lapot.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>CRON-kifejezések

Adhat meg egy [CRON-kifejezés](../azure-functions/functions-bindings-timer.md#cron-expressions) a portálon, vagy egy `settings.job` fájlt, a webjobs-feladat *.zip* fájlt, az alábbi példában látható módon:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Amikor telepít egy webjobs-feladatot a Visual Studióból, jelölje meg a `settings.job` tulajdonságokat a fájl **másolás, ha újabb**.

## <a name="ViewJobHistory"></a> A feladatelőzmények megtekintéséhez

1. Válassza ki a webjobs-feladat előzményeinek megtekintéséhez, és válassza ki a **naplók** gombra.
   
   ![Naplók gomb](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Az a **webjobs-feladat részletei** lapon, válassza ki egyszerre több Futtatás részleteinek megtekintéséhez.
   
   ![Webjobs-feladat részletei](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Az a **webjobs-feladat futtatása részletek** lapon jelölje be **váltógomb kimeneti** a szöveg a napló tartalmának megtekintéséhez.
   
    ![Webjobs-feladat futtatásának részletei](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Válassza ki a szöveget egy külön böngészőablakot a megtekintéséhez **letöltése**. A szöveg maga letöltéséhez kattintson a jobb gombbal **letöltése** , és mentse a fájl tartalmát a böngésző beállításai segítségével.
   
5. Válassza ki a **WebJobs** Ugrás a webjobs-feladatok listája az oldal tetején lévő navigációs hivatkozás.

    ![Webjobs-feladat navigációs](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Webjobs-feladatok listája az előzmények irányítópultja](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Következő lépések

Számos programozási feladatok egyszerűbbé tétele az Azure WebJobs SDK használható WebJobs-feladatokkal. További információkért lásd: [Mi a WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
