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
ms.openlocfilehash: ba3cf3e77e5414804e881e0cddb151fb14fb7fd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669673"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A Visual Studio Code lehetővé teszi a függvényprojekt közzétételét közvetlenül az Azure-ba. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Alapértelmezés szerint a Visual Studio létrehozza a Function alkalmazás létrehozásához szükséges összes Azure-erőforrást. Ezeknek az erőforrásoknak a neve a kiválasztott Function app-név alapján történik. Ha teljes hozzáférésre van szüksége a létrehozott erőforrásokhoz, a [speciális beállításokkal tehet közzé](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options)helyet.

Ez a szakasz azt feltételezi, hogy új Function-alkalmazást hoz létre az Azure-ban.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki `Azure Functions: Deploy to function app...`a következőt:.

1. Ha nincs bejelentkezve, a rendszer felszólítja, hogy jelentkezzen be az **Azure**-ba. **Létrehozhat egy ingyenes Azure-fiókot**is. Miután sikeresen bejelentkezett a böngészőből, lépjen vissza a Visual Studio Code-ba. 

1. Ha több előfizetéssel rendelkezik, **válasszon egy** előfizetést a Function alkalmazáshoz, majd válassza az **+ új Függvényalkalmazás létrehozása az Azure-ban**lehetőséget.

1. Írjon be egy globálisan egyedi nevet a függvényalkalmazás azonosításához, majd nyomja le az Enter billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`.

    Az ENTER billentyű lenyomásakor a következő Azure-erőforrások jönnek létre az előfizetésben:

    * **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** : Az összes létrehozott Azure-erőforrást tartalmazza. A név a Function alkalmazás nevén alapul.
    * **[Storage-fiók](../articles/storage/common/storage-quickstart-create-account.md)** : A standard Storage-fiók egy egyedi névvel jön létre, amely a Function alkalmazás nevén alapul.
    * **[Üzemeltetési terv](../articles/azure-functions/functions-scale.md)** : Az USA nyugati régiójában létrejön egy használati terv, amely a kiszolgáló nélküli Function alkalmazást üzemelteti.
    * **Function alkalmazás**: A projekt üzembe helyezése és futtatása ebben az új Function alkalmazásban történik.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is.

1. Vissza az **Azure-ban: Funkciók** területen bontsa ki az új Function alkalmazást az előfizetés alatt. Bontsa ki a **függvények**elemet, kattintson a jobb gombbal a **HttpTrigger**elemre, majd válassza a **függvény URL-címének másolása**

    ![A függvény URL-címének másolása az új HTTP-triggerhez](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
