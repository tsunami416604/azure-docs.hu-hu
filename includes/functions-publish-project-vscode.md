---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cfa36331f8f4ad45f3bf8ff32eee7d89c7d8852
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608219"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A Visual Studio Code lehetővé teszi a függvényprojekt közzétételét közvetlenül az Azure-ba. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Alapértelmezés szerint a Visual Studio létrehozza az összes Azure-erőforrások a függvényalkalmazás létrehozásához szükséges. Ezek az erőforrások nevei a függvényalkalmazás nevét, válassza a alapulnak. Ha szüksége a létrehozott erőforrások teljes körű hozzáféréssel rendelkezik, akkor ehelyett [közzététele a speciális beállításokkal](../articles/azure-functions/functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).

Ez a szakasz azt feltételezi, hogy hoz létre egy új függvényalkalmazást az Azure-ban.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. A Visual Studio Code-nyomja le az F1 billentyűt a parancskatalógus megnyitásához. A parancskatalógus keresése és kiválasztása `Azure Functions: Deploy to function app...`.

1. Ha nincs bejelentkezve, kéri **jelentkezzen be Azure**. Emellett **hozzon létre egy ingyenes Azure-fiókkal**. Sikeres bejelentkezés után a böngészőből lépjen vissza a Visual Studio Code-ot. 

1. Ha több előfizetéssel rendelkezik **válasszon ki egy előfizetést** a függvényalkalmazás, majd válassza ki **+ új Függvényalkalmazás létrehozása az Azure-ban**.

1. Írjon be egy globálisan egyedi nevet a függvényalkalmazás azonosításához, majd nyomja le az Enter billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`.

    Amikor lenyomja az ENTER billentyűt, a következő Azure-erőforrások jönnek létre az előfizetésben:

    * **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** : Tartalmazza az összes létrehozott Azure-erőforrások. A név alapján a függvényalkalmazás nevére.
    * **[Storage-fiók](../articles/storage/common/storage-quickstart-create-account.md)** : Egy standard szintű tárfiókot a függvényalkalmazás neve alapján egyedi névvel jön létre.
    * **[Szolgáltatási csomag](../articles/azure-functions/functions-scale.md)** : A használatalapú csomag a kiszolgáló nélküli függvényalkalmazás futtatásához, az USA nyugati régiójában jön létre.
    * **Függvényalkalmazás**: A projekt központi telepítése, és fut, az új függvényalkalmazást.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza ki **nézet kimeneti** erre az értesítésre, létrehozása és üzembe helyezés eredményei megtekintése, beleértve a létrehozott Azure-erőforrások.

1. Térjen vissza a **Azure: Függvények** területre, bontsa ki az új függvényalkalmazást az előfizetéséhez. Bontsa ki a **funkciók**, kattintson a jobb gombbal **HttpTrigger**, és válassza a **függvény URL-Címének másolása**.

    ![Az új HTTP-eseményindító függvény URL-Címének másolása](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
