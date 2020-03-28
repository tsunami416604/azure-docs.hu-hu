---
title: 'Oktatóanyag: Az Azure Cosmos DB által működtetett Node.js alkalmazások üzembe helyezése Azure DevOps-projektekkel'
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. A DevOps-projektek segítségével néhány gyors lépésben telepítheti az Azure Cosmos DB által működtetett Node.js alkalmazást a Windows Web App ba.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 229b4b9f53ea3866dce1169645f6d6da20827271
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888906"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Az Azure Cosmos DB által működtetett Node.js alkalmazások üzembe helyezése DevOps-projektekkel

Az Azure DevOps-projektek egyszerűsített élményt kínálnak, ahol folyamatos integrációs (CI) és folyamatos üzembe helyezési (CD) folyamatot hozhat létre az Azure-ba. Ehhez használja a meglévő kódot és a Git-tárház (tárház) vagy egy mintaalkalmazás kiválasztásával.

DevOps-projektek is:

* Automatikusan létrehozza az Azure-erőforrásokat, például az Azure Cosmos DB-t, az Azure Application Insightsot, az Azure App Service-t és az App Service-csomagokat

* Ci/CD kiadási folyamat létrehozása és konfigurálása az Azure DevOps-ban

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure Cosmos DB által üzemeltetett Node.js alkalmazás üzembe helyezése DevOps-projektek használatával
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása
> * Az Azure Cosmos DB vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * A módosítások véglegesítése a Gitben, és automatikusan üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

Szüksége van egy Azure-előfizetésre, amelyet ingyenesen átvehet a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) szolgáltatáson keresztül.

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>A Node.js alkalmazás üzembe helyezéséhez használja a DevOps-projekteket

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrások, például az Azure Cosmos DB, Application Insights, App Service és App Service-csomagok, az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be a **DevOps-projektek**kifejezést, és válassza **a Hozzáadás lehetőséget.**

   ![Fejlesztői műveletek ablaktáblája](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Válassza **a Node.js parancsot** futásidőként, majd kattintson a **Tovább**gombra. Az **Alkalmazáskeret kiválasztása**csoportban válassza **az Express.js lehetőséget.**

1. Engedélyezze az **Adatbázis hozzáadása** **cosmos DB-hoz**című szakaszt, majd válassza a **Tovább**lehetőséget.

    ![Adatbázis hozzáadása](_img/azure-devops-project-cosmos-db/add-database.png)

    Az Azure DevOps-projektek különböző alkalmazáskeretrendszereket támogatnak, például **az Express.js,** **a Sample Node.js alkalmazást**és a **Sail.js alkalmazást.** Ebben az oktatóanyagban **az Express.js programot használjuk.**

1. Válasszon ki egy Azure-szolgáltatást az alkalmazás üzembe helyezéséhez, majd válassza a **Tovább**gombot. A lehetőségek közé tartozik a Windows Web App, az Azure Kubernetes szolgáltatás és az Azure Web App tárolókhoz. Ebben az oktatóanyagban a **Windows Web App alkalmazást használjuk.**

## <a name="configure-azure-devops-and-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Adja meg az Azure DevOps-projekt nevét.

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet.

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez vagy a tarifacsomag és a hely azonosításához válassza a **További beállítások lehetőséget.** Ez az ablaktábla az Azure-szolgáltatások tarifacsomagjának és helyének konfigurálása különböző lehetőségeket jeleníti meg.

1. Lépjen ki az Azure konfigurációs területéből, és válassza **a Kész gombot.**

1. A folyamat néhány perc múlva befejeződik. A minta Node.js alkalmazás van beállítva egy Git-tárházban az Azure DevOps-szervezetben. Ezután létrejönnek az Azure Cosmos DB, az App Service, az App Service-csomag és az Application Insights-erőforrások, valamint egy CI/CD-folyamat. Az alkalmazás ezután üzembe kerül az Azure-ban.

   Miután ezek a folyamatok befejeződtek, az Azure DevOps Project irányítópultja megjelenik az Azure Portalon. A DevOps-projektek irányítópultját közvetlenül az Azure **Portalösszes erőforrásából** is megkaphatja.

   Ez az irányítópult betekintést nyújt az Azure DevOps-kódtárba, a CI/CD-folyamatba és az Azure Cosmos DB adatbázisába. Az Azure DevOps-folyamatban további CI/CD-beállításokat is konfigurálhat. Az irányítópult jobb oldalán válassza az **Azure Cosmos DB-t** ezeka beállítások megtekintéséhez.

## <a name="examine-azure-cosmos-db"></a>Az Azure Cosmos DB vizsgálata

A DevOps-projektek automatikusan konfigurálják az Azure Cosmos DB-t, amelyet feltárhat és testreszabhat. Az Azure Cosmos DB megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

    ![DevOps-projektek irányítópultja](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. A jobb oldalon válassza az Azure Cosmos DB.At right, select Azure Cosmos DB. Megnyílik egy ablaktábla az Azure Cosmos DB számára. Ebből a nézetből különböző műveleteket hajthat végre, például figyelési műveleteket és naplók keresését.

    ![Az Azure Cosmos DB ablaktábla](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps-projektek automatikusan konfigurálja a CI/CD-folyamat az Azure DevOps-szervezet. Megvizsgálhatja és testre szabhatja a folyamatot. Ha meg szeretné ismerni, tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

1. Jelölje ki a hivatkozást a **Build (Build)** csoportban. A böngészőlapon az új projekt buildelési folyamata látható.

    ![Létrehozás i üvegtábla](_img/azure-devops-project-cosmos-db/build.png)

1. Válassza a **Szerkesztés** elemet. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait. A build különböző feladatokat hajt végre, például a forráskód beolvasása a Git-tárházból, az alkalmazás létrehozása, az egységtesztek futtatása és a központi telepítésekhez használt kimenetek közzététele.

1. Válassza **az Eseményindítók lehetőséget.** DevOps-projektek automatikusan létrehoz egy CI-eseményindítót, és minden véglegesítése a tárújraíráshoz új buildet indít el. Választhat, hogy az ágakat is kivonja vagy kizárja a CI folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, majd válassza a **Mentés & várólista** legördülő menüjében a **Mentés** lehetőséget.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ez az ablaktábla a build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

## <a name="examine-the-cd-release-pipeline"></a>A CD-kiadási folyamat vizsgálata

A DevOps-projektek automatikusan létrehozza és konfigurálja az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket. Ezek a lépések közé tartozik egy Azure-szolgáltatás-kapcsolat konfigurálása az Azure DevOps azure-előfizetésében való hitelesítéséhez. Az automatizálás egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure számára. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Nyissa meg a **Folyamatok lehetőséget,** és válassza **a Kiadások lehetőséget.**

1. Válassza a **Szerkesztés** elemet.

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépésekben megvizsgált buildfolyamat létrehozza a műtermékhez használt kimenetet.

1. A **Legördülő** ikontól jobbra válassza a **Folyamatos üzembe helyezés eseményindító lehetőséget.** Ez a kiadási folyamat engedélyezte a folyamatos üzembe helyezési eseményindítót, amely minden alkalommal végrehajtja a központi telepítést, amikor új buildösszetevő érhető el. Letilthatja az eseményindítót, hogy a központi telepítések manuálisan hajtsanak végre.

1. A jobb oldalon válassza a **Kiadások megtekintése szakaszt** a kiadások előzményeinek megjelenítéséhez.

1. Válassza ki a folyamatot megjelenítő kiadást. A kiadási összefoglaló, véglegesítés vagy a kapcsolódó munkaelemek ellenőrzéséhez jelöljön ki bármilyen környezetet.

1. Válassza a **Véglegesítéseket**. Ebben a nézetben a központi telepítéshez társított kódvéglegesítések láthatók. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza **a Naplók megtekintése**lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Kódmódosítások véglegesítése és a CI/CD-folyamat végrehajtása

> [!NOTE]
> A következő eljárás a CI/CD-folyamatot egyszerű szövegmódosítással teszteli.

Most már készen áll arra, hogy együttműködjön egy csapattal az alkalmazásban egy CI/CD-folyamat használatával, amely a legújabb munkáját telepíti az App Service-be. A Git-tárházban minden módosítás elindítja a buildet az Azure DevOps-ban, és egy CD-folyamat végrehajtja az Azure-ba való üzembe helyezést. Kövesse az ebben a szakaszban található eljárást, vagy használjon más technikát a tárműtér módosítási véglegesítéséhez. Klónozhatja például a Git-tártatárját a kedvenc eszközében vagy IDE-jében, majd leküldéses módosításokat a tárházban.

1. Az Azure DevOps menüben válassza a **Repos parancsot,** majd **a Fájlok parancsot.** Akkor menj a repo-hoz.

1. A tárműtár már tartalmaz kódot a létrehozási folyamatban kiválasztott alkalmazásnyelv alapján. Nyissa meg az **Application/views/index.pug** fájlt.

1. Válassza **a Szerkesztés**lehetőséget, majd módosítsa a **15-ös sort.** Módosíthatja például "Az első üzembe helyezés az Azure Cosmos DB által működtetett Azure App Service-re" lehetőségre.

1. A jobb felső sarokban válassza a **Véglegesítés**lehetőséget, majd a **Véglegesítés gombra** a módosítás leküldéses lehetőségéhez.

     Néhány másodperc múlva egy build elindul az Azure DevOps-ban, és egy kiadás fut a módosítások üzembe helyezéséhez. Figyelje a buildállapotát a DevOps-projektek irányítópultján vagy a böngészőben az Azure DevOps-szervezettel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a létrehozott kapcsolódó erőforrásokat, amikor már nincs rájuk szüksége. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Cosmos DB által üzemeltetett Node.js alkalmazás üzembe helyezése DevOps-projektek használatával
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * Az Azure Cosmos DB vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások véglegesítése a Gitben, és automatikusan üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

További információt és a következő lépéseket [a többlépcsős folyamatos üzembe helyezési (CD) folyamat ának megadása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) című témakörben talál.


