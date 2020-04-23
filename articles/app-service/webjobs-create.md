---
title: Háttérfeladatok futtatása a WebJobs alkalmazással
description: Ismerje meg, hogyan futtathat webjobs-feladatokat az Azure App Service-ben. Válasszon a különböző szkriptformátumok közül, és futtassa őket CRON kifejezésekkel.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 028551f04b2e44e9456e2f7343159ad9b52fd25f
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085144"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Háttérfeladatok futtatása webfeladatokkal az Azure App Service-ben

Ez a cikk bemutatja, hogyan telepítheti a WebJobs az [Azure Portal](https://portal.azure.com) on egy végrehajtható fájl vagy parancsfájl feltöltéséhez. A WebJobs visual studióval történő fejlesztéséről és központi telepítéséről a [WebJobs telepítése a Visual Studióhasználatával című](webjobs-dotnet-deploy-vs.md)témakörben talál további információt.

## <a name="overview"></a>Áttekintés
A WebJobs az [Azure App Service](index.yml) egyik szolgáltatása, amely lehetővé teszi egy program vagy parancsfájl futtatását ugyanabban a példányban, mint egy webalkalmazás, API-alkalmazás vagy mobilalkalmazás. A WebJobs használata nem jár többletköltséggel.

> [!IMPORTANT]
> A WebJobs még nem támogatott linuxos App Service esetén.

Az Azure WebJobs SDK webjobs-szal számos programozási feladat egyszerűsítésére használható. További információ: [Mi a WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

Az Azure Functions egy másik módja a programok és parancsfájlok futtatásának. A WebJobs és a Functions összehasonlítását a [Flow, a Logic Apps, a Functions és a WebJobs közötti választás (Választás a Folyamat, A Logikai alkalmazások, a Függvények és a WebJobs) között (Választás a Folyamat, A logikai alkalmazások, a Függvények és a WebJobs) elemre](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)vonatkozóan talál.

## <a name="webjob-types"></a>WebJob-típusok

Az alábbi táblázat a *folyamatos* és az aktivált webfeladatok közötti *különbségeket* ismerteti.


|Folyamatos  |Kiváltott  |
|---------|---------|
| A WebJob létrehozásakor azonnal elindul. Annak érdekében, hogy a feladat ne érjön véget, a program vagy a parancsfájl általában végtelen hurokban végzi munkáját. Ha a feladat nem ér véget, újraindíthatja azt. | Csak akkor indul el, ha manuálisan vagy ütemezés szerint aktiválódik. |
| Fut az összes példány, amelyen a webalkalmazás fut. A WebJob ot szükség esetén egyetlen példányra korlátozhatja. |Egyetlen példányon fut, amelyet az Azure kiválaszt a terheléselosztáshoz.|
| Támogatja a távoli hibakeresést. | Nem támogatja a távoli hibakeresést.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Parancsfájlok és programok támogatott fájltípusai

A rendszer a következő fájltípusokat támogatja:

* .cmd, .bat, .exe (Windows cmd használatával)
* .ps1 (powershell használatával)
* .sh (Bash használatával)
* .php (PHP használatával)
* .py (Python használatával)
* .js (a Node.js használatával)
* .jar (Java használatával)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>Folyamatos webfeladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az App Service-webapp, az API-alkalmazás vagy a mobilalkalmazás **App** Service-lapját.

2. Válassza a **WebJobs lehetőséget**.

   ![Webfeladatok kiválasztása](./media/web-sites-create-web-jobs/select-webjobs.png)

2. A **WebJobs** lapon válassza a **Hozzáadás gombot.**

    ![WebJob lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **Webhelyfeladat hozzáadása** beállításokat a táblázatban megadottak szerint.

   ![WebJob hozzáadása lap](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Név** | myContinuousWebJob | Egy App Service-alkalmazásban egyedi név. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat a "-" és a "_" karakteren kívül más speciális karaktereket. |
   | **Fájl feltöltése** | ConsoleApp.zip | A végrehajtható vagy parancsfájlt tartalmazó *.zip* fájl, valamint a program vagy parancsfájl futtatásához szükséges segédfájlok. A támogatott végrehajtható vagy parancsfájltípusok a [Támogatott fájltípusok](#acceptablefiles) szakaszban találhatók. |
   | **Típus** | Folyamatos | A [WebJob-típusokat](#webjob-types) a cikk korábbi cikke ismerteti. |
   | **Méretezés** | Többpéldányos példány | Csak folyamatos webfeladatok esetén érhető el. Azt határozza meg, hogy a program vagy parancsfájl az összes példányon vagy csak egy példányon fut-e. A több példányon való futtatás lehetősége nem vonatkozik az ingyenes vagy megosztott [tarifacsomagokra.](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) | 

4. Kattintson az **OK** gombra.

   Az új WebJob megjelenik a **WebJobs** lapon.

   ![WebJobs-ok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Folyamatos Webfeladat leállításához vagy újraindításához kattintson a jobb gombbal a listában lévő WebJob parancsra, és kattintson a **Leállítás** vagy **a Start parancsra.**

    ![Folyamatos webfeladat leállítása](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>Manuálisan aktivált webfeladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az App Service-webapp, az API-alkalmazás vagy a mobilalkalmazás **App** Service-lapját.

2. Válassza a **WebJobs lehetőséget**.

   ![Webfeladatok kiválasztása](./media/web-sites-create-web-jobs/select-webjobs.png)

2. A **WebJobs** lapon válassza a **Hozzáadás gombot.**

    ![WebJob lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **Webhelyfeladat hozzáadása** beállításokat a táblázatban megadottak szerint.

   ![WebJob hozzáadása lap](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Név** | myTriggeredWebJob | Egy App Service-alkalmazásban egyedi név. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat a "-" és a "_" karakteren kívül más speciális karaktereket.|
   | **Fájl feltöltése** | ConsoleApp.zip | A végrehajtható vagy parancsfájlt tartalmazó *.zip* fájl, valamint a program vagy parancsfájl futtatásához szükséges segédfájlok. A támogatott végrehajtható vagy parancsfájltípusok a [Támogatott fájltípusok](#acceptablefiles) szakaszban találhatók. |
   | **Típus** | Kiváltott | A [WebJob-típusokat](#webjob-types) a cikk korábbi cikke ismerteti. |
   | **Eseményindítók** | Kézi | |

4. Kattintson az **OK** gombra.

   Az új WebJob megjelenik a **WebJobs** lapon.

   ![WebJobs-ok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. A WebJob futtatásához kattintson a jobb gombbal a nevére a listában, és válassza a **Futtatás (Futtatás) parancsra.**
   
    ![Webfeladat futtatása](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>Ütemezett webfeladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az App Service-webapp, az API-alkalmazás vagy a mobilalkalmazás **App** Service-lapját.

2. Válassza a **WebJobs lehetőséget**.

   ![Webfeladatok kiválasztása](./media/web-sites-create-web-jobs/select-webjobs.png)

2. A **WebJobs** lapon válassza a **Hozzáadás gombot.**

   ![WebJob lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **Webhelyfeladat hozzáadása** beállításokat a táblázatban megadottak szerint.

   ![WebJob hozzáadása lap](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Név** | myScheduledWebJob | Egy App Service-alkalmazásban egyedi név. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat a "-" és a "_" karakteren kívül más speciális karaktereket. |
   | **Fájl feltöltése** | ConsoleApp.zip | A végrehajtható vagy parancsfájlt tartalmazó *.zip* fájl, valamint a program vagy parancsfájl futtatásához szükséges segédfájlok. A támogatott végrehajtható vagy parancsfájltípusok a [Támogatott fájltípusok](#acceptablefiles) szakaszban találhatók. |
   | **Típus** | Kiváltott | A [WebJob-típusokat](#webjob-types) a cikk korábbi cikke ismerteti. |
   | **Eseményindítók** | Ütemezett | Az ütemezés megbízható működéséhez engedélyezze az Always On funkciót. A Mindig bekapcsolva csak az alapszintű, a standard és a prémium szintű tarifacsomagban érhető el.|
   | **CRON-kifejezés** | 0 0/20 * * * * | [A CRON-kifejezéseket](#ncrontab-expressions) a következő szakasz ismerteti. |

4. Kattintson az **OK** gombra.

   Az új WebJob megjelenik a **WebJobs** lapon.

   ![WebJobs-ok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB kifejezések

Megadhat egy [NCRONTAB kifejezést](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) a portálon, vagy a WebJob `settings.job` *.zip* fájl gyökerében lévő fájlt is megadhat, ahogy az a következő példában látható:

```json
{
    "schedule": "0 */15 * * * *"
}
```

További információ: [Az aktivált webfeladat ütemezése](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)című témakörben olvashat.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>A feladatelőzmények megtekintése

1. Jelölje ki azt a WebJob-ot, amelynek előzményeit meg szeretné tekinteni, majd kattintson a **Naplók** gombra.
   
   ![Naplók gomb](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. A **WebJob részletei** lapon válassza ki az egy futtatás részleteinek megtekintéséhez.
   
   ![Webjob részletei](./media/web-sites-create-web-jobs/webjobdetails.png)

3. A **WebJob Futtatás részletei** lapján válassza a **Kimenet váltása** lehetőséget a napló tartalmának megtekintéséhez.
   
    ![A webfeladat futtatásának részletei](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Ha a kimeneti szöveget külön böngészőablakban szeretné megtekinteni, válassza a **Letöltés**lehetőséget. Magához a szöveghez kattintson a jobb gombbal a **letöltésre,** és a böngésző beállításaival mentse a fájl tartalmát.
   
5. A lap tetején található **WebJobs-navigációs** sávhivatkozással nyissa meg a WebJobs-alkalmazások listáját.

    ![WebJob zsemlemorzsa](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![WebJobs-ok listája az előzmények irányítópultján](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a>Következő lépések

Az Azure WebJobs SDK webjobs-szal számos programozási feladat egyszerűsítésére használható. További információ: [Mi a WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)
