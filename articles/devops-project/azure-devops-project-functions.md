---
title: 'Oktatóanyag: ASP.NET-alkalmazások telepítése Azure Functionsra Azure DevOps Projects'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A DevOps Projects segítségével néhány gyors lépésben üzembe helyezheti a ASP.NET alkalmazást, hogy Azure Functions.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971560"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Folyamatos üzembe helyezés a Azure Functions a DevOps Projects

Azure DevOps Projects egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást, amellyel folyamatos integrációs (CI) és folyamatos átviteli (CD) folyamatokat hozhat létre az Azure-ba.

DevOps Projects is:

* Automatikusan létrehozza az Azure-erőforrásokat, például a Azure Functions

* Kiadási folyamat létrehozása és konfigurálása az Azure DevOps CI/CD-hez

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
>* ASP.NET-alkalmazás üzembe helyezése az Azure Function DevOps Projects használatával
>* Az Azure DevOps és az Azure-előfizetés konfigurálása
>* Az Azure-függvény vizsgálata
>* A CI-folyamat vizsgálata
>* A CD-folyamat vizsgálata
>* Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban
>* Az erőforrások eltávolítása

A függvények támogatott futtatókörnyezetei jelenleg a **.net** és a **Node. js**. Használjuk. Az oktatóanyaghoz tartozó NET Runtime a Azure Functions üzembe helyezéséhez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. A [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) szolgáltatáshoz ingyenesen hozzájuthat

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>ASP.NET-alkalmazás üzembe helyezése a DevOps Projects használatával Azure Functions

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects Azure-erőforrásokat (például IoTHub) is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget.

1. A keresőmezőbe írja be a **DevOps projects**kifejezést, majd kattintson a **Hozzáadás**gombra.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Válassza a **.net**lehetőséget, majd kattintson a **tovább**gombra. Az **alkalmazás-keretrendszer kiválasztása**területen válassza a **ASP.net** elemet, majd kattintson a **tovább**gombra.

1. Válassza a **függvényalkalmazás** lehetőséget, majd kattintson a **Tovább gombra**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Adja meg az Azure DevOps-projekt nevét.

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet.

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez, valamint az árképzési csomag és a hely azonosításához kattintson a további beállítások elemre. Ez a panel az Azure-szolgáltatások díjszabási szintjeinek és helyének konfigurálására szolgáló különböző lehetőségeket jeleníti meg.

1. Lépjen ki az Azure-beli konfigurációs területen, majd válassza a kész lehetőséget.

1. Néhány perc elteltével a folyamat befejeződött. Egy minta ASP.NET-alkalmazás egy git-tárházban van beállítva az Azure DevOps-szervezetben, egy függvényalkalmazás és Application Insights létrejön, a CI/CD-folyamat végrehajtása folyamatban van, és az alkalmazás üzembe helyezése az Azure-ban történik.

   Az összes befejezése után az Azure DevOps projekt irányítópultja megjelenik a Azure Portal. A DevOps Projects irányítópultot közvetlenül a Azure Portal **összes erőforrásáról** is megtekintheti.

   Ez az irányítópult az Azure DevOps-adattárba, a CI/CD-folyamatba és az Azure-függvénybe is betekintést nyújt. Az Azure DevOps-folyamat további CI/CD-beállításokat is konfigurálhat. A jobb oldalon válassza a **függvényalkalmazás** lehetőséget a megtekintéshez.

## <a name="examine-the-function-app"></a>A függvényalkalmazás vizsgálata

DevOps Projects automatikusan konfigurálja a Function alkalmazást, amelyet felderítheti és testreszabhatja. A Function app megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Projects irányítópultot.

    ![DevOps Projects Dashboard](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. A jobb oldalon válassza ki a Function alkalmazást. Megnyílik egy ablaktábla a Function alkalmazáshoz. Ebből a nézetből különböző műveleteket végezhet el, például a műveletek figyelését, a naplók keresését.

    ![Függvényalkalmazás](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps Projects automatikusan konfigurálja a CI/CD-folyamatot az Azure DevOps-szervezetben. Megvizsgálhatja és testre szabhatja a folyamatot. A következők megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Projects irányítópultot.

1. Kattintson a **Létrehozás**alatt található hivatkozásra. Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

    ![Felépítés](_img/azure-devops-project-functions/build.png)

1. Válassza a **Szerkesztés** elemet. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat. A build különböző feladatokat hajt végre, mint például a forráskód beolvasása a git-tárházból, az alkalmazás létrehozása, az egységhez tartozó tesztek futtatása, valamint az üzembe helyezéshez használt kimenetek közzététele.

1. Válassza az **Eseményindítókat**. A DevOps Projects automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíró értékre, **majd válassza a mentés lehetőséget** a **Mentés & üzenetsor** legördülő menüből.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követheti a felépítési folyamaton végrehajtott változtatásokat, és lehetővé teszi a verziók összehasonlítását.

## <a name="examine-the-cd-release-pipeline"></a>A CD-kiadási folyamat vizsgálata

DevOps Projects automatikusan létrehozza és konfigurálja a szükséges lépéseket az Azure DevOps-szervezet Azure-előfizetéséhez való üzembe helyezéshez. Ezek a lépések az Azure-DevOps Azure-előfizetéshez való hitelesítéséhez szükséges Azure-szolgáltatási kapcsolatok konfigurálását is tartalmazzák. Az Automation egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure-hoz. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Navigáljon a **folyamatokhoz | Kiadások**.

1. Kattintson a **Szerkesztés**gombra.

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet.

1. A **drop** ikon jobb oldalán válassza a **folyamatos üzembe helyezés trigger**lehetőséget. Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Kattintson a kiadásra, amely megjeleníti a folyamatot. Kattintson bármelyik környezetre a kiadási **Összefoglalás, a commit**, a társított **munkaelemek**megtekintéséhez.

1. Válassza a **Véglegesítéseket**. Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **naplók megtekintése**lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

> [!NOTE]
> Az alábbi eljárással a CI/CD-folyamat egy egyszerű szöveg módosításával tesztelhető.

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját az Azure-függvényben. A git-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést. Kövesse az ebben a szakaszban leírt eljárást, vagy használjon másik technikát a tárház változásainak érvényesítéséhez. Megteheti például, hogy a git-tárházat a kedvenc eszközén vagy IDE helyezi, majd leküldi a módosításokat a tárházban.

1. Az Azure DevOps menüben válassza a **Repos | Fájlokat**, majd nyissa meg a tárházat.

1. A tárház már tartalmazza a **SampleFunctionApp** nevű kódot a létrehozási folyamat során kiválasztott alkalmazás nyelve alapján. Nyissa meg az **Application/SampleFunctionApp/Function1. cs** fájlt.

1. Válassza a **Szerkesztés**lehetőséget, majd végezze el a **31. számú sor** módosítását. Frissítheti például a **Hello is. Üdvözli a Azure Functions a DevOps Projects @ no__t-0 használatával

1. A jobb felső sarokban válassza a végrehajtás lehetőséget, majd a módosítás elküldéséhez válassza a **véglegesítés** **újra lehetőséget.**

1. Nyissa meg az **Application/SampleFunctionApp. test/Function1TestRunner. cs** fájlt. 

1. Válassza a **Szerkesztés**lehetőséget, majd végezze el a **21. számú sor**módosítását. Frissítheti például a **Hello is. Üdvözöljük a Azure Functions Azure DevOps Projects @ no__t-0 használatával.

     Néhány pillanat elteltével a buildek az Azure DevOps-ben kezdődnek, és a kiadásokat a rendszer a módosítások üzembe helyezésével hajtja végre. Figyelje a Build állapotát a DevOps Projects irányítópulton vagy a böngészőben az Azure DevOps-szervezettel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott kapcsolódó erőforrásokat törölheti, ha már nincs rájuk szükség. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * ASP.NET Core alkalmazás üzembe helyezése az Azure-függvény DevOps Projects használatával
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Az Azure-függvény vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

