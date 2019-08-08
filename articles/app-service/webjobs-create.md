---
title: Háttérben futó feladatok futtatása webjobs-Azure App Service
description: Ismerje meg, hogyan futtathat webjobs-feladatokat Azure App Service webalkalmazásokban, API-alkalmazásokban vagy mobil alkalmazásokban a háttérben végzett feladatok futtatásához.
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
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 55d040e96b92f73a632fd415220f9cf135335736
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851007"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service

## <a name="overview"></a>Áttekintés
A webjobs olyan [Azure app Service](https://docs.microsoft.com/azure/app-service/) szolgáltatása, amely lehetővé teszi, hogy egy programot vagy parancsfájlt egy webalkalmazás, egy API-alkalmazás vagy egy Mobile App használatával azonos környezetben futtasson. A webjobs használata nem jár további díjszabással.

> [!IMPORTANT]
> A webjobs-feladatok még nem támogatottak a Linux rendszeren való App Service.

Ez a cikk bemutatja, hogyan telepíthet webjobs-feladatokat a [Azure Portal](https://portal.azure.com) használatával egy végrehajtható fájl vagy parancsfájl feltöltéséhez. További információ a webjobs-feladatok a Visual Studióval való fejlesztéséről és üzembe helyezéséről: webjobs- [feladatok üzembe helyezése a Visual Studióval](webjobs-dotnet-deploy-vs.md).

A Azure WebJobs SDK a webjobs használatával számos programozási feladatot egyszerűsítheti. További információ: [Mi a Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

A Azure Functions egy másik módszert biztosít a programok és parancsfájlok futtatásához. A webjobs és a functions közötti összehasonlításért lásd: [választás a flow, a Logic apps, a functions és a webjobs között](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webjobs-típusok

Az alábbi táblázat a *folyamatos* és az *aktivált* webjobs közötti különbségeket ismerteti.


|Folyamatos  |Triggerrel indított  |
|---------|---------|
| Azonnal elindul a Webjobs létrehozásakor. A feladatok befejezésének megtartásához a program vagy a szkript általában egy végtelen hurokon belül végzi a munkáját. Ha a feladattípus véget ért, újraindíthatja. | Csak akkor indul el, ha manuálisan vagy ütemezetten indítja el. |
| Minden olyan példányon fut, amelyen a webalkalmazás fut. Lehetősége van arra is, hogy a Webjobs egyetlen példányra korlátozza. |Egyetlen példányban fut, amelyet az Azure kiválaszt a terheléselosztáshoz.|
| Támogatja a távoli hibakeresést. | Nem támogatja a távoli hibakeresést.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Támogatott fájltípusok parancsfájlokhoz vagy programokhoz

A következő fájltípusok támogatottak:

* . cmd,. bat,. exe (a Windows cmd használatával)
* . ps1 (a PowerShell használatával)
* . sh (bash használatával)
* . php (PHP használatával)
* . a (Python használatával)
* . js (a Node. js használatával)
* . jar (Java használatával)

## <a name="CreateContinuous"></a>Folyamatos Webjobs létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. A [Azure Portal](https://portal.azure.com)nyissa meg a app Service webalkalmazás, az API-alkalmazás vagy a mobile app **app Service** lapját.

2. Válasszaa webjobs elemet.

   ![Select WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. A **webjobs** lapon válassza a **Hozzáadás**lehetőséget.

    ![Webjobs lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a táblázatban megadott **webjobs** -beállítások hozzáadása beállítást.

   ![Webjobs hozzáadása lap](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Név** | myContinuousWebJob | Egy App Service alkalmazáson belül egyedi név. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat olyan speciális karaktereket, mint a "-" és a "_". |
   | **Fájlfeltöltés** | ConsoleApp.zip | A végrehajtható fájlt vagy parancsfájlt tartalmazó *. zip* fájl, valamint a program vagy a parancsfájl futtatásához szükséges összes támogató fájl. A támogatott végrehajtható fájlok vagy parancsfájl-típusok a [támogatott fájltípusok](#acceptablefiles) szakaszban vannak felsorolva. |
   | **Típusa** | Folyamatos | A [webjobs-típusok](#webjob-types) leírását a cikk korábbi részében találja. |
   | **Méretezés** | Több példány | Csak a folyamatos webjobs-feladatok esetében érhető el. Meghatározza, hogy a program vagy a parancsfájl az összes példányon vagy csak egy példányon fusson-e. A több példányon való futtatás lehetősége nem vonatkozik az ingyenes vagy a közös [díjszabási szintekre](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Kattintson az **OK** gombra.

   Az új Webjobs megjelenik a **webjobs** oldalon.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Folyamatos Webjobs leállításához vagy újraindításához kattintson a jobb gombbal a Webjobs a listában, majd kattintson a **Leállítás** vagy az **Indítás**parancsra.

    ![Folyamatos Webjobs leállítása](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>Manuálisan aktivált Webjobs létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. A [Azure Portal](https://portal.azure.com)nyissa meg a app Service webalkalmazás, az API-alkalmazás vagy a mobile app **app Service** lapját.

2. Válasszaa webjobs elemet.

   ![Select WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. A **webjobs** lapon válassza a **Hozzáadás**lehetőséget.

    ![Webjobs lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a táblázatban megadott **webjobs** -beállítások hozzáadása beállítást.

   ![Webjobs hozzáadása lap](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Név** | myTriggeredWebJob | Egy App Service alkalmazáson belül egyedi név. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat olyan speciális karaktereket, mint a "-" és a "_".|
   | **Fájlfeltöltés** | ConsoleApp.zip | A végrehajtható fájlt vagy parancsfájlt tartalmazó *. zip* fájl, valamint a program vagy a parancsfájl futtatásához szükséges összes támogató fájl. A támogatott végrehajtható fájlok vagy parancsfájl-típusok a [támogatott fájltípusok](#acceptablefiles) szakaszban vannak felsorolva. |
   | **Típusa** | Triggerrel indított | A [webjobs-típusok](#webjob-types) leírását a cikk korábbi részében találja. |
   | **Eseményindítók** | Manuális | |

4. Kattintson az **OK** gombra.

   Az új Webjobs megjelenik a **webjobs** oldalon.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. A Webjobs futtatásához kattintson a jobb gombbal a nevére a listában, majd kattintson a **Futtatás**parancsra.
   
    ![WebJobs-feladat futtatása](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Ütemezett Webjobs létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. A [Azure Portal](https://portal.azure.com)nyissa meg a app Service webalkalmazás, az API-alkalmazás vagy a mobile app **app Service** lapját.

2. Válasszaa webjobs elemet.

   ![Select WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. A **webjobs** lapon válassza a **Hozzáadás**lehetőséget.

   ![Webjobs lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a táblázatban megadott **webjobs** -beállítások hozzáadása beállítást.

   ![Webjobs hozzáadása lap](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Név** | myScheduledWebJob | Egy App Service alkalmazáson belül egyedi név. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat olyan speciális karaktereket, mint a "-" és a "_". |
   | **Fájlfeltöltés** | ConsoleApp.zip | A végrehajtható fájlt vagy parancsfájlt tartalmazó *. zip* fájl, valamint a program vagy a parancsfájl futtatásához szükséges összes támogató fájl. A támogatott végrehajtható fájlok vagy parancsfájl-típusok a [támogatott fájltípusok](#acceptablefiles) szakaszban vannak felsorolva. |
   | **Típusa** | Triggerrel indított | A [webjobs-típusok](#webjob-types) leírását a cikk korábbi részében találja. |
   | **Eseményindítók** | Ütemezett | Ahhoz, hogy az ütemezés megbízhatóan működjön, engedélyezze az Always On funkciót. Az Always on csak az alapszintű, a standard és a prémium szintű díjszabásban érhető el.|
   | **CRON kifejezés** | 0 0/20 * * * * | A [cron](#cron-expressions) -kifejezéseket a következő szakasz ismerteti. |

4. Kattintson az **OK** gombra.

   Az új Webjobs megjelenik a **webjobs** oldalon.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB kifejezések

Megadhat egy [NCRONRAB kifejezést](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) a portálon, vagy belefoglalhat egy `settings.job` fájlt a webjobs *. zip* fájl gyökerébe, ahogy az alábbi példában is látható:

```json
{
    "schedule": "0 */15 * * * *"
}
```

További információ: [aktivált Webjobs ütemezése](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

## <a name="ViewJobHistory"></a>A feladatok előzményeinek megtekintése

1. Válassza ki azt a Webjobs, amelyben meg szeretné jeleníteni az előzményeket, majd kattintson a **naplók** gombra.
   
   ![Naplók gomb](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. A **Webjobs részletei** lapon válasszon ki egy időpontot egy Futtatás részleteinek megtekintéséhez.
   
   ![Webjobs részletei](./media/web-sites-create-web-jobs/webjobdetails.png)

3. A **webjobs-Futtatás részletei** lapon a **kimenet váltása** elemre kattintva jelenítse meg a napló tartalmának szövegét.
   
    ![Webes feladatok futtatásának részletei](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Ha egy különálló böngészőablakban szeretné megtekinteni a kimeneti szöveget, válassza a **Letöltés**lehetőséget. A szöveg letöltéséhez kattintson a jobb gombbal a **Letöltés** elemre, és használja a böngésző beállításait a fájl tartalmának mentéséhez.
   
5. Válassza ki az oldal tetején található webjobs navigációs hivatkozást, hogy megnyissa a webjobs-feladatok listáját.

    ![Webjobs morzsa](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Webjobs-feladatok listája az előzmények irányítópultján](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Következő lépések

A Azure WebJobs SDK a webjobs használatával számos programozási feladatot egyszerűsítheti. További információ: [Mi a Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
