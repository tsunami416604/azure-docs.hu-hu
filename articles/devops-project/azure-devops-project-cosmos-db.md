---
title: 'Oktatóanyag: Az Azure DevOps Projects segítségével Azure Cosmos DB által működtetett Node.js-alkalmazások üzembe helyezése'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. DevOps-projektek, üzembe helyezheti a Node.js-alkalmazás Azure Cosmos DB Windows webalkalmazás működteti, néhány gyors lépéssel.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813065"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Az DevOps Projects segítségével Azure Cosmos DB által működtetett Node.js-alkalmazások üzembe helyezése

Az Azure DevOps Projects kínál egy zökkenőmentes élményt, ahol a meglévő kódot és a Git-adattár, vagy válasszon egy mintaalkalmazást, hozzon létre egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot az Azure-bA.

DevOps-projektek is:

* Hozzon létre Azure-erőforrások automatikus, például az Azure Cosmos DB, az Application Insights, az App Service és az App Service-ben.

* Létrehozza és konfigurálja a kibocsátási folyamat az Azure DevOps a CI/CD

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A DevOps Projects segítségével működteti az Azure Cosmos DB Node.js-alkalmazás üzembe helyezése
> * Az Azure DevOps és az Azure-előfizetés konfigurálása
> * Vizsgálja meg az Azure Cosmos DB használatával
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * A változtatások véglegesítése a Git, és automatikusan telepíthet az Azure-bA
> * Az erőforrások törlése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Kap egy ingyenes keresztül [Visual Studio Dev Essentials programhoz](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Node.js-alkalmazás üzembe helyezése a DevOps Projects segítségével

A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. DevOps-projektek Azure-erőforrások, például az Azure Cosmos DB, az Application Insights, az App Service és App Service-csomag is létrehoz a választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. A bal oldali panelen válassza ki a szakaszt **erőforrás létrehozása**.

1. A Keresés mezőbe írja be a **DevOps Projects**, és kattintson a **Hozzáadás**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Válassza ki **Node.js** a modult, és válassza ki, **tovább**. A **válasszon alkalmazási keretrendszert**válassza **szolgáló Express.js**.

1. A szakaszban engedélyezze **adatbázis hozzáadása** a **Cosmos DB** , majd kattintson a **tovább**.

    ![Adatbázis hozzáadása](_img/azure-devops-project-cosmos-db/add-database.png)

    A cosmos DB támogatja a különböző alkalmazás-keretrendszerekkel, mint a **szolgáló Express.js**, **minta Node.js-alkalmazás**, és **Sail.js**. Ebben az oktatóanyagban lehetővé teszi, érdemes lehet **szolgáló Express.js**.

1. Válassza ki az alkalmazás központi telepítése az Azure-szolgáltatások. Rendelkezik különböző szolgáltatások például a Windows-webalkalmazást, a Kubernetes-szolgáltatást és a Web App for containers szolgáltatásban. A jelen oktatóanyag esetében ezzel **Windows webalkalmazás**. Kattintson a **tovább**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Adja meg az Azure DevOps-projekt nevét.

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet.

1. Válassza ki az Azure-előfizetését.

1. Az Azure további konfigurációs beállítások megtekintése, és azonosíthatja a tarifacsomag szintje és a hely, kattintson a további beállításokat. Ezen a panelen a árképzési szint és az Azure-szolgáltatások helyének konfigurálása különböző lehetőségeit jeleníti meg.

1. Lépjen ki az Azure konfigurációs terület, és válassza ki **kész**.

1. Néhány perc múlva a folyamat befejezése. Minta Node.js-alkalmazás beállítása a szervezet Azure DevOps Git-adattár, létrehozott egy Azure Cosmos DB, az App Service, App Service-csomag és az Application Insights, a CI/CD-folyamat végrehajtása és az alkalmazás van üzembe helyezve az Azure-bA.

   Miután ez befejeződött, az Azure Portalon az Azure DevOps-projekt-irányítópult jelenik meg. Ha ellátogat a DevOps Projects irányítópultra közvetlenül a **összes erőforrás** az Azure Portalon.

   Ez az irányítópult itt látható-e be az Azure DevOps kódtárat a CI/CD-folyamat, és az Azure Cosmos DB-ben. További CI/CD-beállításokat konfigurálhatja az Azure DevOps-folyamat. Válassza ki a jobb **Azure Cosmos DB** megtekintéséhez.

## <a name="examine-the-azure-cosmos-db"></a>Vizsgálja meg az Azure Cosmos DB használatával

A DevOps Projects automatikusan konfigurálja a Cosmos DB, amely ismerje meg, és testre szabhatja. Ismerje meg a Cosmos DB-vel, tegye a következőket:

1. Nyissa meg a DevOps Projects-irányítópultot.

    ![DevOps Projects Dashboard](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. A jobb oldalon válassza ki a Cosmos DB. A Cosmos DB-hez készült megjelenik egy panel. Ebben a nézetben, például a műveletek figyelése és a naplók keresése különféle műveleteket hajthat végre.

    ![Függvényalkalmazás](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A DevOps Projects automatikusan konfigurálja a CI/CD-folyamat a szervezet Azure DevOps. Megvizsgálhatja és testre szabhatja a folyamatot. Ismerje meg az azt, tegye a következőket:

1. Nyissa meg a DevOps Projects-irányítópultot.

1. A hivatkozásra a **összeállítása**. Egy böngészőben lap megjeleníti a buildelési folyamat az új projekt.

    ![Felépítés](_img/azure-devops-project-cosmos-db/build.png)

1. Válassza a **Szerkesztés** elemet. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat. A build különböző feladatokat hajt végre, például beolvasásakor forráskódot, a Git-adattár, az alkalmazás egységtesztek futtatásával, és a közzétételi létrehozása a kimenete a telepítéshez használt.

1. Válassza az **Eseményindítókat**. A DevOps Projects automatikusan CI eseményindítót hoz létre, és minden véglegesítéshez az adattárhoz elindul egy új létrehozást. Igény szerint kiválaszthatja, hogy belefoglalja vagy kizárja az ágak a CI folyamat.

1. Válassza a **Megtartást**. A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a nevet, a létrehozási folyamat tulajdonságánál, és válassza **mentése** származó a **várólistára & mentése** legördülő listából.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ezen a panelen a legutóbbi módosítások build auditnaplót jeleníti meg. Az Azure DevOps nyomon követi a végzett módosítások a buildelési folyamat, és lehetővé teszi, hogy verziójának összehasonlítása.

## <a name="examine-the-cd-release-pipeline"></a>Vizsgálja meg a CD-kiadási folyamatok

A DevOps Projects automatikusan létrehozza és konfigurálja az Azure-előfizetéshez az Azure DevOps-szervezet telepítéséhez szükséges lépéseket. Ide tartozik az Azure DevOps hitelesítéséhez az Azure-előfizetéshez az Azure service-kapcsolat beállítása. Az automation is létrehoz egy kiadási folyamatot, amely biztosítja a CD-ről az Azure-bA. További információ a kiadási folyamathoz, tegye a következőket:

1. Keresse meg a **folyamatok |} Kiadások**.

1. Kattintson a **szerkesztése**.

1. Az **Összetevők** alatt válassza az **Elvetést**. A buildelési folyamat, az előző lépésekben vizsgálni összetevő szolgálja ki a kimenetet eredményez.

1. Jobb oldalán a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**. A kibocsátási folyamat engedélyezte a CD-eseményindító, amely végrehajtja a központi telepítés minden alkalommal, amikor egy új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását.

1. Jobb oldalán, válassza ki a szakaszt **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Kattintson a verzió, amely megjeleníti a folyamat. Kattintson a bármilyen környezetben ellenőrizheti a kiadási **összegzése, véglegesítéseket**, kapcsolódó **munkaelemek**.

1. Válassza a **Véglegesítéseket**. Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza ki **megtekinthetők a naplófájlok**. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

> [!NOTE]
> Az alábbi eljárás teszteli a CI/CD-folyamat egy egyszerű szöveges változtatásokat.

Most már készen áll, az alkalmazás a csapat együttműködhet a CI/CD-folyamat, amely automatikusan telepíti a legújabb munkáját az Azure App Service használatával. Minden módosítás a Git-tárház egy build elindítja az Azure DevOps, és a egy CD-folyamat végrehajtja a központi telepítés az Azure-bA. Kövesse az ebben a szakaszban az eljárást, vagy egy másik módszer használatával véglegesítse a módosításokat az adattárhoz. Például a Git-tárház a kedvenc eszköz vagy az IDE klónozhat, és leküldéses átvált ebben a tárházban.

1. Az Azure DevOps menüjében válassza **Adattárakkal |} Fájlok**, és folytassa az adattárhoz.

1. Az adattár már tartalmazza a kódot a létrehozási folyamat során választott alkalmazás nyelv alapján. Nyissa meg a **Application/views/index.pug** fájlt.

1. Válassza ki **szerkesztése**, majd hajtsa végre a módosítás **sor száma 15** . Például módosíthatja, hogy **saját első üzembe helyezés az Azure App Service segítségével Azure Cosmos DB-hez**

1. Jobb felső sarokban, válassza ki a **véglegesítése**, majd válassza ki **véglegesítése** újra, hogy a módosítás leküldése.

     Néhány pillanat múlva build elindítja az Azure DevOps, és egy kiadási üzembe helyezéséhez a módosításokat hajt végre. A DevOps Projects irányítópulton vagy a böngészőben az Azure DevOps vállalattal build állapotának figyelésére.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kapcsolódó erőforrásokat, ha többé már nincs szükség rájuk létrehozott törölheti. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps Projects segítségével működteti az Azure Cosmos DB Node.js-alkalmazás üzembe helyezése
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Vizsgálja meg az Azure Cosmos DB használatával
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat a Git, és automatikusan telepíthet az Azure-bA
> * Az erőforrások eltávolítása
