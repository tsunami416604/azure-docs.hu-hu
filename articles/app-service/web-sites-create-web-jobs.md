---
title: "Háttérfeladatok futtatása a webjobs-feladatok az Azure App Service-ben"
description: "Ismerje meg a WebJobs használata az Azure App Service web apps, az API-alkalmazások vagy a mobilalkalmazások háttér feladatok futtatásához."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga
ms.openlocfilehash: 1594a27d50df9abdac27b342ad18363b974c8972
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Háttérfeladatok futtatása a webjobs-feladatok az Azure App Service-ben

## <a name="overview"></a>Áttekintés
Webjobs-feladatok csak a [Azure App Service](https://docs.microsoft.com/azure/app-service/) , amely lehetővé teszi, hogy egy program vagy parancsfájl egy webalkalmazást, API-alkalmazás vagy mobilalkalmazás azonos környezetében futnak. Nincs webjobs-feladatok használandó további költség nélkül.

Ez a cikk bemutatja, hogyan telepíthetők a webjobs-feladatok segítségével a [Azure-portálon](https://portal.azure.com) végrehajtható vagy parancsfájlokat feltöltéséhez. Fejlesztés és WebJobs telepítése a Visual Studio használatával kapcsolatos információkért lásd: [telepítése webjobs-feladatok Visual Studio használatával](websites-dotnet-deploy-webjobs.md).

Az Azure WebJobs SDK számos programozási feladatok egyszerűbbé teheti a webjobs-feladatok is használható. További információkért lásd: [Mi az a WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Az Azure Functions egy másik megoldást programok és parancsfájlok futtatásához. Webjobs-feladatok és a funkciók között, lásd: [válasszon a folyamatot, a Logic Apps, a funkciók és a webjobs-feladatok](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webjobs-feladat típusa

A következő táblázat ismerteti a közötti különbségeket *folyamatos* és *indított* webjobs-feladatok.


|Folyamatos  |Elindítva  |
|---------|---------|
| A webjobs-feladat létrehozása után azonnal elindul. Tartani a feladat befejezési, hogy a program vagy parancsfájl általában e ki végtelen ciklus belül teendőit. Ha a feladat befejezéséhez, újraindíthatja azt. | Csak akkor, amikor elindul a manuálisan vagy ütemezés szerint kezdődik. |
| A webes alkalmazást futtató összes példánya fut. Opcionálisan korlátozhatja a webjobs-feladat egyetlen példányra. |Által a kijelölt Azure egyetlen példányán fut terheléselosztás.|
| Támogatja a távoli hibakeresés. | Nem támogatja a távoli hibakeresés.|

> [!NOTE]
> A webes alkalmazás időkorlát 20 perc inaktivitás után is. Az scm (telepítés) vagy a web app lapra a portál csak kérelmek az időzítő alaphelyzetbe állítása. A tényleges helyet kérelmek nem az időzítő alaphelyzetbe. Ha az ütemezett webjobs-feladatok engedélyezése vagy az alkalmazás fut a folyamatos **mindig a** annak érdekében, hogy a webjobs-feladatok futtatása megbízhatóan. Ez a funkció érhető el kizárólag a Basic, Standard és Premium [tarifacsomagok](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>A Programok telepítése és parancsfájlok támogatott fájltípusok

A következő fájltípusokat támogatja:

* .cmd, .bat, .exe (a Windows parancssori eszközzel)
* .ps1 (a PowerShell használatával)
* .SH (a Bash használatával)
* .php (a PHP használatával)
* .PY (Python használatával)
* .js (Node.js segítségével)
* a .JAR fájlt (a Java)

## <a name="CreateContinuous"></a>Egy folyamatos webjobs-feladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az a [Azure-portálon](https://portal.azure.com), navigáljon a **App Service** az App Service webalkalmazásba, API-alkalmazás vagy mobilalkalmazás oldalán.

2. Válassza ki **WebJobs**.

   ![Válassza ki a webjobs-feladatok](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Az a **WebJobs** lapon jelölje be **Hozzáadás**.

    ![Webjobs-feladat lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **hozzáadása webjobs-feladat** a táblázatban megadott beállításokat.

   ![Webjobs-feladat-weblap hozzáadása](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Name (Név)** | myContinuousWebJob | Egy App Service-alkalmazást belül egyedi neve. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat különleges karaktereket eltérő "-" és "_". |
   | **Fájl feltöltése** | ConsoleApp.zip | A *.zip* fájlt, amely a végrehajtható fájlt vagy parancsfájlt, valamint a program vagy parancsfájl futtatásához szükséges támogató fájlokat tartalmazza. A támogatott végrehajtható fájl vagy parancsfájl fájltípusokat jelennek meg a [támogatott fájltípusok](#acceptablefiles) szakasz. |
   | **Típus** | Folyamatos | A [webjobs-feladat típusok](#webjob-types) az ebben a cikkben leírt. |
   | **Méretezés** | Többpéldányos | Csak folyamatos webjobs-feladatok esetén érhető el. Meghatározza, hogy a program vagy parancsfájl fut-e az összes példányt vagy egy példányt. Több példánya futhat a beállítás nem érvényes az ingyenes vagy közös [tarifacsomagok](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Kattintson az **OK** gombra.

   Az új webjobs-feladat jelenik meg a **WebJobs** lap.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Leállítani, vagy indítsa újra a folyamatos webjobs-feladat, kattintson a jobb gombbal a webjobs-feladat, a listában, és kattintson a **leállítása** vagy **Start**.

    ![Egy folyamatos webjobs-feladat leállítása](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>Manuálisan indított webjobs-feladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az a [Azure-portálon](https://portal.azure.com), navigáljon a **App Service** az App Service webalkalmazásba, API-alkalmazás vagy mobilalkalmazás oldalán.

2. Válassza ki **WebJobs**.

   ![Válassza ki a webjobs-feladatok](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Az a **WebJobs** lapon jelölje be **Hozzáadás**.

    ![Webjobs-feladat lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **hozzáadása webjobs-feladat** a táblázatban megadott beállításokat.

   ![Webjobs-feladat-weblap hozzáadása](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Name (Név)** | myTriggeredWebJob | Egy App Service-alkalmazást belül egyedi neve. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat különleges karaktereket eltérő "-" és "_".|
   | **Fájl feltöltése** | ConsoleApp.zip | A *.zip* fájlt, amely a végrehajtható fájlt vagy parancsfájlt, valamint a program vagy parancsfájl futtatásához szükséges támogató fájlokat tartalmazza. A támogatott végrehajtható fájl vagy parancsfájl fájltípusokat jelennek meg a [támogatott fájltípusok](#acceptablefiles) szakasz. |
   | **Típus** | Elindítva | A [webjobs-feladat típusok](#webjob-types) az ebben a cikkben leírt. |
   | **Eseményindítók** | Manuális | |

4. Kattintson az **OK** gombra.

   Az új webjobs-feladat jelenik meg a **WebJobs** lap.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. A webjobs-feladat futtatásához kattintson a jobb gombbal a nevét a listában, és kattintson a **futtatása**.
   
    ![Webjobs-feladat futtatása](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Ütemezett webjobs-feladat létrehozása

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Az a [Azure-portálon](https://portal.azure.com), navigáljon a **App Service** az App Service webalkalmazásba, API-alkalmazás vagy mobilalkalmazás oldalán.

2. Válassza ki **WebJobs**.

   ![Válassza ki a webjobs-feladatok](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Az a **WebJobs** lapon jelölje be **Hozzáadás**.

   ![Webjobs-feladat lap](./media/web-sites-create-web-jobs/wjblade.png)

3. Használja a **hozzáadása webjobs-feladat** a táblázatban megadott beállításokat.

   ![Webjobs-feladat-weblap hozzáadása](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Beállítás      | Mintaérték   | Leírás  |
   | ------------ | ----------------- | ------------ |
   | **Name (Név)** | myScheduledWebJob | Egy App Service-alkalmazást belül egyedi neve. Betűvel vagy számmal kell kezdődnie, és nem tartalmazhat különleges karaktereket eltérő "-" és "_". |
   | **Fájl feltöltése** | ConsoleApp.zip | A *.zip* fájlt, amely a végrehajtható fájlt vagy parancsfájlt, valamint a program vagy parancsfájl futtatásához szükséges támogató fájlokat tartalmazza. A támogatott végrehajtható fájl vagy parancsfájl fájltípusokat jelennek meg a [támogatott fájltípusok](#acceptablefiles) szakasz. |
   | **Típus** | Elindítva | A [webjobs-feladat típusok](#webjob-types) az ebben a cikkben leírt. |
   | **Eseményindítók** | Ütemezett | Az ütemezést működik megbízhatóan, engedélyezze az Always On funkciót. Always On érhető kizárólag a Basic, Standard és prémium tarifacsomag szükséges.|
   | **CRON-kifejezés** | 0 0/20 * * * * | [CRON-kifejezés](#cron-expressions) a következő szakaszban ismertetjük. |

4. Kattintson az **OK** gombra.

   Az új webjobs-feladat jelenik meg a **WebJobs** lap.

   ![Webjobs-feladatok listája](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>CRON-kifejezés

A [CRON-kifejezés](https://en.wikipedia.org/wiki/Cron) hat mezők tevődik össze: `{second} {minute} {hour} {day} {month} {day of the week}`.  Néhány példa:

* 15 percenként:`0 */15 * * * *`
* Minden órában (Ez azt jelenti, hogy mindig, amikor a perc száma 0):`0 0 * * * *` 
* Minden órában a Reggel 9 a délután 5 óra:`0 0 9-17 * * *` 
* A 9:30 AM minden nap:`0 30 9 * * *`
* A 9:30 AM minden hétköznap:`0 30 9 * * 1-5`

A CRON-kifejezés adja meg a portálon, vagy tartalmaznak egy `settings.job` fájl a webjobs-feladat gyökerében *.zip* fájlt, az alábbi példában látható módon:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> A webjobs-feladat, a Visual Studio eszközből üzembe helyezésekor megjelölni a `settings.job` tulajdonságokat a fájl **másolhatja, ha újabb**.

## <a name="ViewJobHistory"></a>A feladat előzményeinek megtekintése

1. Válassza ki a webjobs-feladat előzményeinek megtekintéséhez, és válassza ki a kívánt a **naplók** gombra.
   
   ![Naplók gomb](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Az a **webjobs-feladat részletei** lapon, válassza ki az egyik futtassa a részletes egyszerre.
   
   ![Webjobs-feladat részletei](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Az a **webjobs-feladat futtatása részletek** lapon jelölje be **váltása kimeneti** a naplók tartalmáról szöveg.
   
    ![Webes feladat futtatása részletei](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Megtekintéséhez válasszon egy másik ablakban a kimeneti szöveget **letöltése**. A szöveg betűméretét letöltéséhez kattintson a jobb gombbal **letöltése** és menteni a fájl tartalmát a böngésző beállításait használja.
   
5. Válassza ki a **WebJobs** navigációs hivatkozásra kattintva webjobs-feladatok listája a lap tetején.

    ![Webjobs-feladat navigációs](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Webjobs-feladatok előzményeinek irányítópulton listája](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Következő lépések

Az Azure WebJobs SDK számos programozási feladatok egyszerűbbé teheti a webjobs-feladatok is használható. További információkért lásd: [Mi az a WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
