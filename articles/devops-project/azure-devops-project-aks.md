---
title: 'Oktatóanyag: ASP.NET Core-alkalmazások üzembe helyezése az Azure Kubernetes szolgáltatásba az Azure DevOps-projektekkel'
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. A DevOps-projektek segítségével néhány gyors lépésben üzembe helyezheti a ASP.NET Core alkalmazást az Azure Kubernetes-szolgáltatással (AKS).
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969673"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Oktatóanyag: ASP.NET Core-alkalmazások üzembe helyezése az Azure Kubernetes szolgáltatásba az Azure DevOps-projektekkel

Az Azure DevOps-projektek egyszerűsített élményt nyújt, ahol a meglévő kódot és a Git-tárház, vagy válasszon egy mintaalkalmazás létrehozása folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat az Azure-ba. 

DevOps-projektek is:
* Automatikusan létrehozza az Azure-erőforrásokat, például az Azure Kubernetes-szolgáltatást (AKS).
* Létrehoz és konfigurál egy kiadási folyamatot az Azure DevOps-ban, amely létrehoz egy build- és kiadási folyamatot a CI/CD-hez.
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.
* Lehetővé teszi [az Azure Monitor számára,](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) hogy a tárolók az AKS-fürt tárolószámítási feladatai teljesítményének figyelésére szolgáljon

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A DevOps-projektek használata ASP.NET Core alkalmazás üzembe helyezéséhez az AKS-hez
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások véglegesítése a Gitben, és automatikusan üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>A DevOps-projektek használata ASP.NET Core alkalmazás üzembe helyezéséhez az AKS-hez

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrások, például egy AKS-fürt, az Ön által kiválasztott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be a **DevOps-projektek**kifejezést, majd válassza a **Létrehozás lehetőséget.**

    ![A DevOps-projektek irányítópultja](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza **a .NET**, majd a **Tovább**lehetőséget.

1. Az **Alkalmazáskeretrendszer kiválasztása**csoportban válassza **a ASP.NET Core**lehetőséget.

1. Válassza a **Kubernetes szolgáltatás**lehetőséget, majd a **Tovább**gombot. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez és az AKS-fürt csomópontjainak azonosításához válassza a **Módosítás lehetőséget.**  
    Ez az ablaktábla az Azure-szolgáltatások típusának és helyének konfigurálása különböző beállításokat jeleníti meg.
 
1. Lépjen ki az Azure konfigurációs területéből, és válassza **a Kész gombot.**  
    Néhány perc múlva a folyamat befejeződik. Egy minta ASP.NET Core alkalmazás be van állítva egy Git-tárházban az Azure DevOps-szervezetben, létrejön egy AKS-fürt, egy CI/CD-folyamat végrehajtása, és az alkalmazás telepítve van az Azure-ban. 

    Miután mindez befejeződött, az Azure DevOps Project irányítópultja megjelenik az Azure Portalon. A DevOps-projektek irányítópultját közvetlenül az Azure **Portalösszes erőforrásából** is megkaphatja. 

    Ez az irányítópult betekintést nyújt az Azure DevOps-kódtárházba, a CI/CD-folyamatba és az AKS-fürtbe. Az Azure DevOps-folyamatban további CI/CD-beállításokat is konfigurálhat. A jobb oldalon válassza a **Tallózás** gombot a futó alkalmazás megtekintéséhez.

## <a name="examine-the-aks-cluster"></a>Az AKS-fürt vizsgálata

A DevOps-projektek automatikusan konfigurálnak egy AKS-fürtöt, amelyet felfedezhet és testreszabhat. Az AKS-fürt megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

1. A jobb oldalon válassza ki az AKS szolgáltatást.  
    Megnyílik egy ablaktábla az AKS-fürtszámára. Ebből a nézetből különböző műveleteket hajthat végre, például a tároló állapotának figyelését, a naplók keresését és a Kubernetes-irányítópult megnyitását.

1. A jobb oldalon válassza a **Kubernetes-irányítópult megtekintése**lehetőséget.  
    Szükség esetén a Kubernetes-irányítópult megnyitásához kövesse a lépéseket.

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps-projektek automatikusan konfigurálja a CI/CD-folyamat az Azure DevOps-szervezet. Megvizsgálhatja és testre szabhatja a folyamatot. Ha meg szeretné ismerni, tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

1. A DevOps-projektek irányítópultjának tetején válassza a **Folyamatok összeállítása**lehetőséget.  
    A böngészőlapon az új projekt buildelési folyamata látható.

1. Mutasson az **Állapot** mezőre, majd jelölje ki a három pontot (...).  
    A menü számos lehetőséget jelenít meg, például egy új build várólistára kerülését, a build szüneteltetését és a buildfolyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait.  
    A build különböző feladatokat hajt végre, például a Git-tárház ból származó források lekérése, a függőségek visszaállítása és a központi telepítésekhez használt kimenetek közzététele.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **a Mentés & várólistát,** majd a **Mentés lehetőséget.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ez az ablaktábla a build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  
    DevOps-projektek automatikusan létrehoz egy CI-eseményindítót, és minden véglegesítése a tárújraíráshoz új buildet indít el. Szükség esetén választhatja az ágak felvétele vagy kizárása a CI folyamatból.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

## <a name="examine-the-cd-release-pipeline"></a>A CD-kiadási folyamat vizsgálata

A DevOps-projektek automatikusan létrehozza és konfigurálja az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket. Ezek a lépések közé tartozik egy Azure-szolgáltatás-kapcsolat konfigurálása az Azure DevOps azure-előfizetésében való hitelesítéséhez. Az automatizálás egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure számára. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Válassza **a Build and Release (Létrehozás és kiadás)** lehetőséget, majd a **Kiadások**lehetőséget.  
    A DevOps-projektek létrehozegy kiadási folyamatot az Azure-ba történő központi telepítések kezeléséhez.

1. Jelölje ki a kiadási folyamat melletti három pontot (...), majd kattintson a **Szerkesztés gombra.**  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépésekben megvizsgált buildfolyamat létrehozza a műtermékhez használt kimenetet. 

1. A **Legördülő** ikon jobb oldalán válassza a **Folyamatos üzembe helyezés eseményindító jat**.  
    Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal végrehajtja a központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek. 

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.

1. Jelölje ki a három pontot (...) egy kiadás mellett, majd kattintson a **Megnyitás gombra.**  
    Több menüt is megvizsgálhat, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ebben a nézetben a központi telepítéshez társított kódvéglegesítések láthatók. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza **a Naplók lehetőséget.**  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba 

 > [!NOTE]
 > A következő eljárás a CI/CD-folyamatot egyszerű szövegmódosítással teszteli.

Most már készen áll arra, hogy együttműködjön egy csapattal az alkalmazásában egy CI/CD folyamat használatával, amely automatikusan telepíti a legújabb munkáját a webhelyére. A Git-tárházban minden módosítás elindítja a buildet az Azure DevOps-ban, és egy CD-folyamat végrehajtja az Azure-ba való üzembe helyezést. Kövesse az ebben a szakaszban található eljárást, vagy használjon más technikát a tárműtér módosítási véglegesítéséhez. Klónozhatja például a Git-tártatárját a kedvenc eszközében vagy IDE-jében, majd leküldéses módosításokat a tárházban.

1. Az Azure DevOps menüben válassza a > **Kódfájlok**lehetőséget, majd nyissa meg a tárta. **Code**

1. Nyissa meg a *Nézetek\Kezdőkönyvtárat,* jelölje ki az *Index.cshtml* fájl melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.

1. Módosítsa a fájlt, például adjon hozzá szöveget az egyik div címkén. 

1. A jobb felső sarokban válassza a **Véglegesítés**lehetőséget, majd a **Véglegesítés** lehetőséget a módosítás lenyomásához.  
    Néhány pillanat múlva egy build elindul az Azure DevOps-ban, és egy kiadás fut a módosítások üzembe helyezéséhez. Figyelje a buildállapotát a DevOps-projektek irányítópultján vagy a böngészőben az Azure DevOps-szervezettel.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tesztelés alatt áll, az erőforrások megtisztításával elkerülheti a számlázási díjak felhalmozását. Ha már nincs rájuk szükség, törölheti az aks-fürtöt és az ebben az oktatóanyagban létrehozott kapcsolódó erőforrásokat. Ehhez használja a **Delete** funkciót a DevOps-projektek irányítópulton.

> [!IMPORTANT]
> A következő eljárás véglegesen törli az erőforrásokat. A *Törlés* funkció elpusztítja a projekt által létrehozott adatokat a DevOps-projektekben az Azure-ban és az Azure DevOps-ban, és nem fogja tudni letölteni azokat. Ezt az eljárást csak akkor használja, ha figyelmesen elolvasta az utasításokat.

1. Az Azure Portalon nyissa meg a DevOps-projektek irányítópultját.
2. A jobb felső sarokban válassza a **Törlés**gombot. 
3. A kérdésben válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez.*

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps-projektek használata ASP.NET Core alkalmazás üzembe helyezéséhez az AKS-hez
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások véglegesítése a Gitben, és automatikusan üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

Ha többet szeretne tudni a Kubernetes irányítópult használatáról, olvassa el az:

> [!div class="nextstepaction"]
> [A Kubernetes-irányítópult használata](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
