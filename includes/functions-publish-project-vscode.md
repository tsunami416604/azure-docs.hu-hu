---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ec0425ff2188ecf1816d5f5841394c8e32f301d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744235"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A Visual Studio Code lehetővé teszi a függvényprojekt közzétételét közvetlenül az Azure-ba. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

A cikk azt feltételezi, hogy Ön új függvényalkalmazást hoz létre. 

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. Az a **Azure: Függvények** területen válassza ki az üzembe helyezés, a Függvényalkalmazás ikon.

    ![A függvényalkalmazás beállításai](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ha nincs bejelentkezve, kéri **jelentkezzen be Azure**. Emellett **hozzon létre egy ingyenes Azure-fiókkal**. Sikeres bejelentkezés után a böngészőből lépjen vissza a Visual Studio Code-ot. 

1. Ha több előfizetéssel rendelkezik **válasszon ki egy előfizetést** a függvényalkalmazás, majd válassza ki **+ új Függvényalkalmazás létrehozása az Azure-ban**.

1. Írjon be egy globálisan egyedi nevet a függvényalkalmazás azonosításához, majd nyomja le az Enter billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`.

1. Válassza a **+ Create New Resource Group** (Új erőforráscsoport létrehozása) lehetőséget, adjon meg egy erőforráscsoport-nevet (pl. `myResourceGroup`), majd nyomja le az Enter billentyűt. Használhat meglévő erőforráscsoportot is.

1. Válasszon **+ létrehozás új Tárfiók**, egy globálisan egyedi nevet az új tárfiók a függvényalkalmazást, és nyomja le az Enter által használt típus. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat.

1. Válasszon ki egy helyet egy, a közelben, vagy a függvények által elért más szolgáltatások közelében található [régióban](https://azure.microsoft.com/regions/).

    Amikor lenyomja az ENTER billentyűt, a következő Azure-erőforrások jönnek létre az előfizetésben:

    * **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)**: Tartalmazza az összes létrehozott Azure-erőforrások. A név alapján a függvényalkalmazás nevére.
    * **[Storage-fiók](../articles/storage/common/storage-quickstart-create-account.md)**: Egy standard szintű tárfiókot a függvényalkalmazás neve alapján egyedi névvel jön létre.
    * **[Szolgáltatási csomag](../articles/azure-functions/functions-scale.md)**: A használatalapú csomag a kiszolgáló nélküli függvényalkalmazás futtatásához, az USA nyugati régiójában jön létre.
    * **Függvényalkalmazás**: A projekt központi telepítése, és fut, az új függvényalkalmazást.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza ki **nézet kimeneti** erre az értesítésre, létrehozása és üzembe helyezés eredményei megtekintése, beleértve a létrehozott Azure-erőforrások.

1. Térjen vissza a **Azure: Függvények** területre, bontsa ki az új függvényalkalmazást az előfizetéséhez. Bontsa ki a **funkciók**, kattintson a jobb gombbal **HttpTrigger**, és válassza a **függvény URL-Címének másolása**.

    ![Az új HTTP-eseményindító függvény URL-Címének másolása](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
