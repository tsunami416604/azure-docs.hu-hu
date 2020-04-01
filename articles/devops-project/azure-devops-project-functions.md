---
title: 'Oktatóanyag: ASP.NET alkalmazások üzembe helyezése az Azure Functions ben az Azure DevOps-projektekkel'
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. A DevOps-projektek segítségével néhány gyors lépésben üzembe helyezheti ASP.NET alkalmazását az Azure Functionsben.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971560"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Az Azure Functions üzembe helyezése devops-projektekkel

Az Azure DevOps-projektek egyszerűsített élményt nyújt, ahol a meglévő kódot és a Git-tárház, vagy válasszon egy mintaalkalmazás létrehozása folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat az Azure-ba.

DevOps-projektek is:

* Automatikusan létrehozza az Azure-erőforrásokat, például az Azure Functionst

* Kiadási folyamat létrehozása és konfigurálása az Azure DevOps for CI/CD-ben

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
>* ASP.NET alkalmazás üzembe helyezése ASP.NET az Azure-függvényben
>* Az Azure DevOps és egy Azure-előfizetés konfigurálása
>* Az Azure függvény vizsgálata
>* A CI-folyamat vizsgálata
>* A CD-folyamat vizsgálata
>* Módosítások véglegesítése a Gitben, és automatikusan üzembe helyezése az Azure-ban
>* Az erőforrások eltávolítása

A függvények támogatott futásideje jelenleg a **.NET** és a **Node.js.** Használjuk. NET-futási idő az oktatóanyag üzembe helyezéséhez az Azure Functions. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. A Visual Studio [Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével egyet ingyenesen beszerezhet

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>ASP.NET alkalmazás üzembe helyezése ASP.NET azure Functions ben

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrások, például egy IoTHub, az Ön által kiválasztott Azure-előfizetésben.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be a **DevOps-projektek**kifejezést, majd kattintson a **Hozzáadás**gombra.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Válassza **a .NET**, majd a **Tovább**lehetőséget. Az **Alkalmazáskeretrendszer kiválasztása**csoportban válassza **a ASP.NET,** majd kattintson a **Tovább**gombra.

1. Válassza **a Függvényalkalmazás,** majd a **Tovább**lehetőséget.

## <a name="configure-azure-devops-and-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Adja meg az Azure DevOps-projekt nevét.

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet.

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez és a tarifacsomag és a hely azonosításához kattintson a További beállítások elemre. Ez az ablaktábla különböző lehetőségeket jelenít meg az Azure-szolgáltatások díjszabási szintjének és helyének konfigurálásához.

1. Lépjen ki az Azure konfigurációs területről, és válassza a Kész lehetőséget.

1. Néhány perc múlva a folyamat befejeződik. Egy minta ASP.NET alkalmazás be van állítva egy Git-tárházban az Azure DevOps-szervezetben, egy függvényalkalmazás és az Application Insights jön létre, egy CI/CD-folyamat jön létre, és az alkalmazás telepítve van az Azure-ban.

   Miután mindez befejeződött, az Azure DevOps Project irányítópultja megjelenik az Azure Portalon. A DevOps-projektek irányítópultját közvetlenül az Azure **Portalösszes erőforrásából** is megkaphatja.

   Ez az irányítópult betekintést nyújt az Azure DevOps-kódtárházba, a CI/CD-folyamatba és az Azure-függvénybe. Az Azure DevOps-folyamatban további CI/CD-beállításokat is konfigurálhat. A jobb oldalon válassza a **Funkcióalkalmazás** lehetőséget a megtekintéshez.

## <a name="examine-the-function-app"></a>A Függvényalkalmazás vizsgálata

A DevOps-projektek automatikusan konfigurálják a függvényalkalmazást, amelyet felfedezhet és testreszabhat. A függvényalkalmazás megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

    ![DevOps-projektek irányítópultja](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. A jobb oldalon válassza ki a függvényalkalmazást. Megnyílik egy ablaktábla a függvényalkalmazáshoz. Ebből a nézetből különböző műveleteket hajthat végre, például műveletek figyelését, naplók keresését.

    ![Függvényalkalmazás](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps-projektek automatikusan konfigurálja a CI/CD-folyamat az Azure DevOps-szervezet. Megvizsgálhatja és testre szabhatja a folyamatot. Ha meg szeretné ismerni, tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

1. Kattintson a **Build (Build)** csoportban található hivatkozásra. A böngészőlapon az új projekt buildelési folyamata látható.

    ![Felépítés](_img/azure-devops-project-functions/build.png)

1. Válassza a **Szerkesztés** elemet. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait. A build különböző feladatokat hajt végre, például a forráskód beolvasása a Git-tárházból, az alkalmazás létrehozása, az egységtesztek futtatása és a központi telepítésekhez használt kimenetek közzététele.

1. Válassza **az Eseményindítók lehetőséget.** DevOps-projektek automatikusan létrehoz egy CI-eseményindítót, és minden véglegesítése a tárújraíráshoz új buildet indít el. Szükség esetén választhatja az ágak felvétele vagy kizárása a CI folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, majd válassza a **Mentés & várólista** legördülő menüjében a **Mentés** lehetőséget.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ez az ablaktábla a build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követheti a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

## <a name="examine-the-cd-release-pipeline"></a>A CD-kiadási folyamat vizsgálata

A DevOps-projektek automatikusan létrehozza és konfigurálja az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket. Ezek a lépések közé tartozik egy Azure-szolgáltatás-kapcsolat konfigurálása az Azure DevOps azure-előfizetésében való hitelesítéséhez. Az automatizálás egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure számára. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Navigálás a **folyamatokhoz | Kiadások**.

1. Kattintson a **Szerkesztés gombra.**

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépésekben megvizsgált buildfolyamat létrehozza a műtermékhez használt kimenetet.

1. A **Legördülő** ikon jobb oldalán válassza a **Folyamatos üzembe helyezés eseményindító jat**. Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal végrehajtja a központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.

1. Kattintson a kiadás, amely megjeleníti a csővezeték. Kattintson bármelyik környezetre, hogy ellenőrizze a kiadás **összefoglaló, véglegesíti**, kapcsolódó **munkaelemek**.

1. Válassza a **Véglegesítéseket**. Ebben a nézetben a központi telepítéshez társított kódvéglegesítések láthatók. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza **a Naplók megtekintése**lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

> [!NOTE]
> A következő eljárás a CI/CD-folyamatot egyszerű szövegmódosítással teszteli.

Most már készen áll arra, hogy együttműködjön egy csapattal az alkalmazásában egy CI/CD-folyamat használatával, amely automatikusan telepíti a legújabb munkáját az Azure-függvénybe. A Git-tárházban minden módosítás elindítja a buildet az Azure DevOps-ban, és egy CD-folyamat végrehajtja az Azure-ba való üzembe helyezést. Kövesse az ebben a szakaszban található eljárást, vagy használjon más technikát a tárműtér módosítási véglegesítéséhez. Klónozhatja például a Git-tártatárját a kedvenc eszközében vagy IDE-jében, majd leküldéses módosításokat a tárházban.

1. Az Azure DevOps menüben válassza **a Repos | Fájlokat**, majd nyissa meg a tárta.

1. A tárház már tartalmazza a **SampleFunctionApp** nevű kódot a létrehozási folyamatban kiválasztott alkalmazásnyelv alapján. Nyissa meg az **Application/SampleFunctionApp/Function1.cs** fájlt.

1. Válassza **a Szerkesztés**lehetőséget, majd módosítsa a **31-es sort.** Például, akkor frissítse a **Hello ott! Üdvözli az Azure Functions devops-projektek használatával**

1. A jobb felső sarokban válassza a **Véglegesítés**lehetőséget, majd a **Véglegesítés** lehetőséget a módosítás lenyomásához.

1. Nyissa meg az **Application/SampleFunctionApp.Test/Function1TestRunner.cs** fájlt. 

1. Válassza **a Szerkesztés**lehetőséget, majd módosítsa a **21-es sort.** Például, akkor frissítse a **Hello ott! Üdvözli az Azure Functions az Azure DevOps-projektek használatával!**

     Néhány pillanat múlva egy build elindul az Azure DevOps-ban, és egy kiadás fut a módosítások üzembe helyezéséhez. Figyelje a buildállapotát a DevOps-projektek irányítópultján vagy a böngészőben az Azure DevOps-szervezettel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti a létrehozott kapcsolódó erőforrásokat, ha már nincs rájuk szüksége. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps-projektek használatával üzembe helyezhet egy ASP.NET Core alkalmazást az Azure-függvénybe
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * Az Azure függvény vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások véglegesítése a Gitben, és automatikusan üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

