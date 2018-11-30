---
title: 'Oktatóanyag: ASP.NET Core-alkalmazások üzembe helyezése az Azure Kubernetes Service az Azure DevOps Projects segítségével'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A DevOps Projects telepítheti az ASP.NET Core-alkalmazás és az Azure Kubernetes Service (AKS) néhány gyors lépéssel.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 7980ea439cfd3eaefcaa308795836a909f980043
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620642"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Oktatóanyag: ASP.NET Core-alkalmazások üzembe helyezése az Azure Kubernetes Service az Azure DevOps Projects segítségével

Az Azure DevOps Projects megjelenít egy egyszerűsített felület, ahol a meglévő kódot és a Git-adattár, vagy válasszon egy mintaalkalmazást, hozzon létre egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot az Azure-bA. 

DevOps-projektek is:
* Automatikusan létrehozza az Azure-erőforrások, például az Azure Kubernetes Service (AKS).
* Hoz létre, és konfigurálja a kibocsátási folyamat, amely beállítja a buildelési és kiadási folyamatot a CI/CD Azure devops.
* Az Azure Application Insights-erőforrás, figyelés hoz létre.
* Lehetővé teszi, hogy [-tárolókhoz az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) az AKS-fürtön a tárolóalapú számítási feladatok teljesítményének figyelése

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * ASP.NET Core-alkalmazás üzembe az aks-ben a DevOps Projects segítségével
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat a Git, és automatikusan telepíthet az Azure-bA
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>ASP.NET Core-alkalmazás üzembe az aks-ben a DevOps Projects segítségével

A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. DevOps-projektek Azure-erőforrások, például egy AKS-fürtöt is létrehoz a választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**.

1. A Keresés mezőbe írja be a **DevOps Projects**, majd válassza ki **létrehozás**.

    ![A DevOps Projects-irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza ki **.NET**, majd válassza ki **tovább**.

1. A **válasszon alkalmazási keretrendszert**válassza **ASP.NET Core**.

1. Válassza ki **Kubernetes-szolgáltatást**, majd válassza ki **tovább**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez, és válassza ki az AKS-fürtöt a csomópontok számának azonosításához **módosítás**.  
    Ezen a panelen típusát és helyét az Azure-szolgáltatások konfigurálása különböző lehetőségeit jeleníti meg.
 
1. Lépjen ki az Azure konfigurációs terület, és válassza ki **kész**.  
    Néhány perc múlva a folyamat befejezése. Egy mintául szolgáló ASP.NET Core-alkalmazást a Git-adattár a szervezet Azure DevOps állítható be, egy AKS-fürt létrehozása, a CI/CD-folyamat végrehajtása és az alkalmazás van üzembe helyezve az Azure-bA. 

    Miután ez befejeződött, az Azure Portalon az Azure DevOps-projekt-irányítópult jelenik meg. Ha ellátogat a DevOps Projects irányítópultra közvetlenül a **összes erőforrás** az Azure Portalon. 

    Ezt az irányítópultot az Azure DevOps-kódtár a CI/CD-folyamat és az AKS-fürt rálátást biztosít. További CI/CD-beállításokat konfigurálhatja az Azure DevOps-folyamat. Válassza ki a jobb **Tallózás** a futó alkalmazás megtekintéséhez.

## <a name="examine-the-aks-cluster"></a>Az AKS-fürt vizsgálata

A DevOps Projects automatikusan beállítja az AKS-fürt, ismerje meg, és testre szabhatja. Ismerkedjen meg az AKS-fürtöt, tegye a következőket:

1. Nyissa meg a DevOps Projects-irányítópultot.

1. A jobb oldalon válassza az AKS szolgáltatás.  
    Az AKS-fürtöt megnyílik egy panel. Ebben a nézetben hajtsa végre különböző műveleteket, például a tároló állapotának monitorozása, naplók keresése és a Kubernetes-irányítópult megnyitása.

1. Válassza ki a jobb **megtekintése a Kubernetes-irányítópult**.  
    Ha szeretné kövesse a Kubernetes-irányítópult megnyitásához.

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A DevOps Projects automatikusan konfigurálja a CI/CD-folyamat a szervezet Azure DevOps. Megvizsgálhatja és testre szabhatja a folyamatot. Ismerje meg az azt, tegye a következőket:

1. Nyissa meg a DevOps Projects-irányítópultot.

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
    Egy böngészőben lap megjeleníti a buildelési folyamat az új projekt.

1. Mutasson a **állapot** mezőben, majd válassza ki a három pontra (...).  
    Menü megjelenítése több beállítások, például az üzenetsor új build, készítsen felfüggesztetéssel és a buildelési folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat, hajt végre, például beolvasásakor a Git-tárházban, a függőségek visszaállítását és közzétételi kimenete-forrásokat az központi telepítésére használatos.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ezen a panelen a legutóbbi módosítások build auditnaplót jeleníti meg. Az Azure DevOps nyomon követi a végzett módosítások a buildelési folyamat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
    A DevOps Projects automatikusan CI eseményindítót hoz létre, és minden véglegesítéshez az adattárhoz elindul egy új létrehozást. Igény szerint kiválaszthatja, hogy belefoglalja vagy kizárja az ágak a CI folyamat.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

## <a name="examine-the-cd-release-pipeline"></a>Vizsgálja meg a CD-kiadási folyamatok

A DevOps Projects automatikusan létrehozza és konfigurálja az Azure-előfizetéshez az Azure DevOps-szervezet telepítéséhez szükséges lépéseket. Ide tartozik az Azure DevOps hitelesítéséhez az Azure-előfizetéshez az Azure service-kapcsolat beállítása. Az automation is létrehoz egy kiadási folyamatot, amely biztosítja a CD-ről az Azure-bA. További információ a kiadási folyamathoz, tegye a következőket:

1. Válassza ki **készítése és kiadása**, majd válassza ki **kiadásokban**.  
    DevOps-projektek az Azure-bA központi telepítések felügyeletéhez szükséges kiadási folyamatot hoz létre.

1. A kibocsátási folyamat melletti három pontra (...), majd válassza ki és **szerkesztése**.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A buildelési folyamat, az előző lépésekben vizsgálni összetevő szolgálja ki a kimenetet eredményez. 

1. Jobb oldalán a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**.  
    A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely végrehajtja a központi telepítés minden alkalommal, amikor egy új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. Válassza ki a jobb **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Egy kiadási melletti három pontra (...), majd válassza ki és **nyílt**.  
    Áttekintheti a több menük, például a kiadás összegzését, a társított munkaelemekhez és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA 

 > [!NOTE]
 > Az alábbi eljárás teszteli a CI/CD-folyamat egy egyszerű szöveges változtatásokat.

Most már készen áll, az alkalmazás a csapat együttműködhet egy CI/CD-folyamat, amely automatikusan telepíti a legújabb munkáját a webhely használatával. Minden módosítás a Git-tárház egy build elindítja az Azure DevOps, és a egy CD-folyamat végrehajtja a központi telepítés az Azure-bA. Kövesse az ebben a szakaszban az eljárást, vagy egy másik módszer használatával véglegesítse a módosításokat az adattárhoz. Például a Git-tárház a kedvenc eszköz vagy az IDE klónozhat, és leküldéses átvált ebben a tárházban.

1. Az Azure DevOps menüjében válassza **kód** > **fájlok**, és folytassa az adattárhoz.

1. Nyissa meg a *Views\Home* könyvtár, válassza a három pontra (...), a *Index.cshtml* fájlt, és válassza ki **szerkesztése**.

1. Módosítja a fájlt, például a valamilyen szöveget a div címkék egyikében. 

1. Jobb felső sarokban, válassza ki a **véglegesítése**, majd válassza ki **véglegesítése** újra, hogy a módosítás leküldése.  
    Néhány pillanat múlva build elindítja az Azure DevOps, és egy kiadási üzembe helyezéséhez a módosításokat hajt végre. A DevOps Projects irányítópulton vagy a böngészőben az Azure DevOps vállalattal build állapotának figyelésére.

1. A kiadás befejezése után frissítse az alkalmazásba és ellenőrizze a módosításokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a halmoz fel díjak szerint az erőforrások törlése. Ha már nincs szüksége, törölheti az AKS-fürt és a kapcsolódó erőforrásokat, ebben az oktatóanyagban létrehozott. Ehhez használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrást. A *törlése* funkció tárolt a DevOps Projects, Azure-beli és az Azure DevOps a projekt által létrehozott adatok megsemmisülnek, és nem lehet beolvasni. Ezzel az eljárással csak azután alaposan elolvasta a megjelenő utasításokat.

1. Az Azure Portalon nyissa meg a DevOps Projects-irányítópultot.
2. Jobb felső sarokban, válassza ki a **törlése**. 
3. Amikor a rendszer kéri, válassza ki a **Igen** való *véglegesen törli* az erőforrásokat.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * ASP.NET Core-alkalmazás üzembe az aks-ben a DevOps Projects segítségével
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat a Git, és automatikusan telepíthet az Azure-bA
> * Az erőforrások eltávolítása

A Kubernetes-irányítópult használatával kapcsolatos további tudnivalókért lásd:

> [!div class="nextstepaction"]
> [A Kubernetes-irányítópult használata](https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard)
