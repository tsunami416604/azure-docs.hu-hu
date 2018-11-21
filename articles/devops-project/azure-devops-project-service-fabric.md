---
title: 'Oktatóanyag: Az ASP.NET Core-alkalmazás telepítése Azure Service Fabric az Azure DevOps Projects használatával'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A DevOps Projects az ASP.NET Core-alkalmazást telepíthet az Azure Service fabric néhány gyors lépéssel.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2bba5d54c2b6298c2dd8059d47e5975ad3f176c8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264760"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Oktatóanyag: Az ASP.NET Core-alkalmazás telepítése Azure Service Fabric az Azure DevOps Projects használatával

Az Azure DevOps Projects megjelenít egy egyszerűsített felület, ahol a meglévő kódot és a Git-adattár, vagy válasszon egy mintaalkalmazást, hozzon létre egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot az Azure-bA. 

DevOps-projektek is:
* Azure-erőforrások, például az Azure Service Fabric automatikusan létrehozza.
* Hoz létre, és konfigurálja a kibocsátási folyamat, amely beállítja a CI/CD-folyamat az Azure devops.
* Az Azure Application Insights-erőforrás, figyelés hoz létre.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * ASP.NET Core-alkalmazás létrehozása és üzembe helyezése a Service Fabric az DevOps Projects segítségével
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * A Git módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabricc"></a>ASP.NET Core-alkalmazás létrehozása és üzembe helyezése Service Fabricc az DevOps Projects segítségével

A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. DevOps-projektek Azure-erőforrások, például egy Service Fabric-fürt is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**.

1. A Keresés mezőbe írja be a **DevOps Projects**, majd válassza ki **létrehozás**.

    ![A DevOps Projects-irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza ki **.NET**, majd válassza ki **tovább**.

1. Alatt **válasszon alkalmazási keretrendszert**válassza **ASP.NET Core**, majd válassza ki **tovább**.

1. Válassza ki **Service Fabric-fürt**, majd válassza ki **tovább**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintése, és azonosíthatja a csomópont virtuális gépének mérete és a Service Fabric-fürt operációs rendszerének **módosítás**.  
    Ezen a panelen típusát és helyét az Azure-szolgáltatások konfigurálása különböző lehetőségeit jeleníti meg.
 
1. Lépjen ki az Azure konfigurációs terület, és válassza ki **kész**.  
    Néhány perc múlva a folyamat befejezése. Egy mintául szolgáló ASP.NET Core-alkalmazást a Git-adattár a szervezet Azure DevOps állítható be, egy Service Fabric-fürtöt, a CI/CD-folyamat végrehajtása és az alkalmazás van üzembe helyezve az Azure-bA. 

    Miután ez befejeződött, a DevOps Projects irányítópult jelenik meg az Azure Portalon. Ha ellátogat a DevOps Projects irányítópultra közvetlenül a **összes erőforrás** az Azure Portalon. 

    Ezt az irányítópultot az Azure DevOps kódtár, a CI/CD-folyamat és a Service Fabric-fürt rálátást biztosít. A CI/CD-folyamat az Azure-kódtárak további beállításokat is konfigurálhat. Válassza ki a jobb **Tallózás** a futó alkalmazás megtekintéséhez.

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A DevOps Projects automatikusan konfigurálja a CI/CD-folyamat Azure folyamatokban. Megvizsgálhatja és testre szabhatja a folyamatot. Ismerje meg az azt, tegye a következőket:

1. Nyissa meg a fejlesztési és üzemeltetési Projectss irányítópultot.

1. Válassza ki a DevOps Projects irányítópult tetején lévő **folyamatok alakíthatók ki**.  
    Egy böngészőben lap megjeleníti a buildelési folyamat az új projekt.

1. Mutasson a **állapot** mezőben, majd válassza ki a három pontra (...).  
    Menü megjelenítése több beállítások, például az üzenetsor új build, készítsen felfüggesztetéssel és a buildelési folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat, hajt végre, például beolvasásakor a Git-tárházban, a függőségek visszaállítását és közzétételi kimenete-forrásokat az központi telepítésére használatos.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve. 

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ezen a panelen a legutóbbi módosítások build auditnaplót jeleníti meg. Az Azure DevOps nyomon követi a végzett módosítások a buildelési folyamat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
    A DevOps Projects automatikusan CI eseményindítót hoz létre, és minden véglegesítéshez az adattárhoz elindul egy új létrehozást. Igény szerint kiválaszthatja, hogy belefoglalja vagy kizárja az ágak a CI folyamat.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

A DevOps Projects automatikusan létrehozza és konfigurálja az Azure-előfizetéshez az Azure DevOps-szervezet telepítéséhez szükséges lépéseket. Ide tartozik az Azure DevOps hitelesítéséhez az Azure-előfizetéshez az Azure service-kapcsolat beállítása. Az automation is létrehoz egy kiadási folyamatot, amely biztosítja a CD-ről az Azure-bA. További információ a kiadási folyamathoz, tegye a következőket:

1. Válassza ki **készítése és kiadása**, majd válassza ki **kiadásokban**.  
    DevOps-projektek az Azure-bA központi telepítések felügyeletéhez szükséges kiadási folyamatot hoz létre.

1. A kibocsátási folyamat melletti három pontra (...), majd válassza ki és **szerkesztése**.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A build vizsgálni, korábban hozza létre egyetlen folyamat a kimeneti összetevő szolgálja ki. 

1. Jobb oldalán a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**.  
    A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely végrehajtja a központi telepítés minden alkalommal, amikor egy új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. Válassza ki a jobb **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Egy kiadási melletti három pontra (...), majd válassza ki és **nyílt**.  
    Áttekintheti a több menük, például a kiadás összegzését, a társított munkaelemekhez és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat a Git, és automatikusan telepíthet az Azure-bA 

 > [!NOTE]
 > Az alábbi eljárás teszteli a CI/CD-folyamat egy egyszerű szöveges változtatásokat.

Most már készen áll, az alkalmazás a csapat együttműködhet egy CI/CD-folyamat, amely automatikusan telepíti a legújabb munkáját a webhely használatával. Minden módosítás a Git-tárház egy build elindul, és egy kiadást telepíti a módosításokat az Azure-bA. Kövesse az ebben a szakaszban az eljárást, vagy egy másik módszer használatával véglegesítse a módosításokat az adattárhoz. Például a Git-tárház a kedvenc eszköz vagy az IDE klónozhat, és leküldéses átvált ebben a tárházban.

1. Az Azure DevOps menüjében válassza **kód** > **fájlok**, és folytassa az adattárhoz.

1. Nyissa meg a *Views\Home* könyvtár, válassza a három pontra (...), a *Index.cshtml* fájlt, és válassza ki **szerkesztése**.

1. Módosítja a fájlt, például a valamilyen szöveget a div címkék egyikében. 

1. Jobb felső sarokban, válassza ki a **véglegesítése**, majd válassza ki **véglegesítése** újra, hogy a módosítás leküldése.  
    Néhány pillanat múlva build kezdődik, és üzembe helyezéséhez a módosításokat a kiadási végrehajtja. Figyelheti a build állapotát a DevOps Projects irányítópulton vagy a böngészőben az Azure DevOps valós idejű naplózása.

1. A kiadás befejezése után frissítse az alkalmazásba és ellenőrizze a módosításokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a halmoz fel díjak szerint az erőforrások törlése. Ha már nincs szüksége, törölheti az Azure Service Fabric-fürt és a kapcsolódó erőforrásokat, ebben az oktatóanyagban létrehozott. Ehhez használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrást. A *törlése* funkció tárolt a DevOps Projects, Azure-beli és az Azure DevOps a projekt által létrehozott adatok megsemmisülnek, és nem lehet beolvasni. Ezzel az eljárással csak azután alaposan elolvasta a megjelenő utasításokat.

1. Az Azure Portalon nyissa meg a DevOps Projects-irányítópultot.
1. Jobb felső sarokban, válassza ki a **törlése**. 
1. Amikor a rendszer kéri, válassza ki a **Igen** való *véglegesen törli* az erőforrásokat.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja az Azure CI-/CD-folyamatot. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * ASP.NET Core-alkalmazás létrehozása és üzembe helyezése a Service Fabric az DevOps Projects segítségével
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat a Git, és automatikusan telepíthet az Azure-bA
> * Az erőforrások eltávolítása

A Service Fabric és a mikroszolgáltatások kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [A mikroszolgáltatás-alapú megközelítés használata alkalmazások létrehozásához](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
