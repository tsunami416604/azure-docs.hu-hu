---
title: 'Oktatóanyag: A ASP.NET Core-alkalmazás üzembe helyezése az Azure-Service Fabric a Azure DevOps Projects használatával'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A DevOps Projects segítségével néhány gyors lépéssel üzembe helyezheti a ASP.NET Core alkalmazást az Azure Service Fabricban.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 5f14164da5cd89cc7d0578e6b64c39d227734d75
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969475"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Oktatóanyag: A ASP.NET Core-alkalmazás üzembe helyezése az Azure-Service Fabric a Azure DevOps Projects használatával

Azure DevOps Projects egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást, amellyel folyamatos integrációs (CI) és folyamatos átviteli (CD) folyamatokat hozhat létre az Azure-ba. 

DevOps Projects is:
* Automatikusan létrehozza az Azure-erőforrásokat, például az Azure Service Fabric-t.
* Létrehoz és konfigurál egy kiadási folyamatot az Azure DevOps, amely egy CI/CD-folyamatot állít be.
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A DevOps Projects használatával hozzon létre egy ASP.NET Core alkalmazást, és telepítse azt a Service Fabric
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * A Git módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>A DevOps Projects használatával hozzon létre egy ASP.NET Core alkalmazást, és telepítse azt a Service Fabric

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects Azure-erőforrásokat (például egy Service Fabric-fürtöt) is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget.

1. A keresőmezőbe írja be a **DevOps projects**kifejezést, majd válassza a **Létrehozás**lehetőséget.

    ![A DevOps Projects irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza a **.net**lehetőséget, majd kattintson a **tovább**gombra.

1. Az **alkalmazás-keretrendszer kiválasztása**területen válassza a **ASP.net Core**lehetőséget, majd kattintson a **tovább**gombra.

1. Válassza ki **Service Fabric fürtöt**, majd kattintson a **tovább**gombra. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez, valamint a csomópont virtuálisgép-méretének és operációs rendszerének a Service Fabric fürthöz való azonosításához kattintson a **módosítás**gombra.  
    Ez a panel az Azure-szolgáltatások típusának és helyének konfigurálására szolgáló különböző beállításokat jeleníti meg.
 
1. Lépjen ki az Azure-beli konfigurációs területen, majd válassza a **kész**lehetőséget.  
    Néhány perc elteltével a folyamat befejeződött. Egy minta ASP.NET Core alkalmazás egy git-tárházban van beállítva az Azure DevOps-szervezetben, létrejön egy Service Fabric-fürt, a CI/CD-folyamat végrehajtása történik, és az alkalmazás üzembe helyezése az Azure-ban történik. 

    Az összes befejezése után a DevOps Projects irányítópult megjelenik a Azure Portalban. A DevOps Projects irányítópultot közvetlenül a Azure Portal **összes erőforrásáról** is megtekintheti. 

    Ez az irányítópult az Azure DevOps Code-tárház, a CI/CD-folyamat és a Service Fabric-fürt láthatóságát ismerteti. További beállításokat is beállíthat a CI/CD-folyamathoz az Azure Reposban. A jobb oldalon kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps Projects automatikusan konfigurálja a CI/CD-folyamatokat az Azure-folyamatokban. Megvizsgálhatja és testre szabhatja a folyamatot. A következők megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps projekt irányítópultját.

1. A DevOps Projects irányítópult tetején válassza a **folyamatok létrehozása**lehetőséget.  
    Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  
    A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve. 

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  
    A DevOps Projects automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

DevOps Projects automatikusan létrehozza és konfigurálja a szükséges lépéseket az Azure DevOps-szervezet Azure-előfizetéséhez való üzembe helyezéshez. Ezek a lépések az Azure-DevOps Azure-előfizetéshez való hitelesítéséhez szükséges Azure-szolgáltatási kapcsolatok konfigurálását is tartalmazzák. Az Automation egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure-hoz. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
    DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A korábban megvizsgált felépítési folyamat az összetevőhöz használt kimenetet hozza létre. 

1. A **drop** ikon jobb oldalán válassza a **folyamatos üzembe helyezés trigger**lehetőséget.  
    Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza a kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
    Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban 

 > [!NOTE]
 > Az alábbi eljárással a CI/CD-folyamat egy egyszerű szöveg módosításával tesztelhető.

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén. A git-tárház minden módosítása elindít egy buildet, és a kiadás üzembe helyezi az Azure-ban végzett módosításokat. Kövesse az ebben a szakaszban leírt eljárást, vagy használjon másik technikát a tárház változásainak érvényesítéséhez. Megteheti például, hogy a git-tárházat a kedvenc eszközén vagy IDE helyezi, majd leküldi a módosításokat a tárházban.

1. Az Azure DevOps menüben válassza ki a **Code** > **Files**elemet, majd nyissa meg a tárházat.

1. Nyissa meg a *Views\Home* könyvtárat, válassza az *index. cshtml* fájl melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.

1. Végezze el a fájl módosítását, például a div-címkék egyikén belüli szöveg hozzáadását. 

1. A jobb felső sarokban válassza a végrehajtás lehetőséget, majd a módosítás elküldéséhez válassza a **véglegesítés** **újra lehetőséget.**  
    Néhány pillanat múlva elindul a Build, majd egy kiadás végrehajtja a módosítások központi telepítését. A Build állapotát a DevOps Projects irányítópulton vagy a böngészőben az Azure DevOps valós idejű naplózásával figyelheti.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a számlázási díjak felmerülését az erőforrások tisztításával. Ha már nincs rá szükség, törölheti az Azure Service Fabric-fürtöt és az ebben az oktatóanyagban létrehozott kapcsolódó erőforrásokat. Ehhez használja a **Törlés** funkciót a DevOps projects irányítópulton.

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrásokat. A *delete* funkció megsemmisíti a projekt által az Azure-ban és az Azure-DevOps található DevOps projects által létrehozott összes adatát, és nem fogja tudni beolvasni. Ezt az eljárást csak akkor használja, ha alaposan elolvasta a kérdéseit.

1. A Azure Portal nyissa meg a DevOps Projects irányítópultot.
1. A jobb felső sarokban válassza a **Törlés**lehetőséget. 
1. A parancssorban válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez* .

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja az Azure CI-/CD-folyamatot. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps Projects használatával hozzon létre egy ASP.NET Core alkalmazást, és telepítse azt a Service Fabric
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

A Service Fabric és a szolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A mikroszolgáltatás-alapú megközelítés használata alkalmazások létrehozásához](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
