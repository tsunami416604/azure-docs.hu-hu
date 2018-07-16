---
title: CI-/CD-folyamat létrehozása meglévő kódhoz az Azure DevOps Projecttel | VSTS-oktatóanyag
description: A DevOps Project megkönnyíti az Azure-ral való ismerkedést. A segítségével néhány gyors lépésben a saját kódját és GitHub-adattárát használhatja az alkalmazás a választott Azure-szolgáltatásban történő elindításához.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 8c92b45cd3949e56515286c963b035e3c449835b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967405"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>CI-/CD-folyamat létrehozása meglévő kódhoz az Azure DevOps Projecttel

Az Azure DevOps Projecttel meglévő kódok és a Git-adattár segítségével, vagy valamelyik mintaalkalmazásból egyszerűen hozhat létre egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot az Azure-ban.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * Azure DevOps-projekt létrehozása
> * A GitHub-adattárhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * A VSTS és egy Azure-előfizetés konfigurálása 
> * A GitHub módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * A VSTS CI-/CD-folyamat vizsgálata
> * Az Azure Application Insights monitorozásának konfigurálása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.
* Hozzáférhet egy GitHub- vagy egy külső Git-adattárhoz, amely .NET-, Java-, PHP-, Node-, Python- vagy statikus webkódokat tartalmaz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az Azure DevOps Project létrehoz egy CI-/CD-folyamatot a VSTS-ben.  Létrehozhat egy **új VSTS-** fiókot, vagy használhat egy **meglévő fiókot** is.  Az Azure DevOps Project az **Azure-erőforrásokat** is létrehozza a választott **Azure-előfizetésben**.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali navigációs sávban válassza az **+ Új** ikont, majd keresse meg a **DevOps-projektet**.  Válassza a **Létrehozás** elemet.

    ![Folyamatos kézbesítés indítása](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza a **Saját kód használatát**.  Ha elkészült, válassza a **Tovább** lehetőséget.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>A GitHub-adattárhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása

1. Válassza a **GitHub** lehetőséget.  Választhat egy **külső Git-adattárat** is.  Válassza ki az alkalmazást tartalmazó **adattárat** és **ágat**.

1. Válassza ki a **webes keretrendszert**.  Ha elkészült, válassza a **Tovább** lehetőséget.

    ![.NET-keretrendszer](_img/azure-devops-project-github/webframework.png)

1. Az előző lépésekben kiválasztott alkalmazás-keretrendszer meghatározza az Azure-szolgáltatás üzembehelyezési céljának itt elérhető típusát.  Válassza ki a tetszőleges **célszolgáltatást**.  Ha elkészült, válassza a **Tovább** lehetőséget.

## <a name="configure-vsts-and-an-azure-subscription"></a>A VSTS és egy Azure-előfizetés konfigurálása 

1. Hozzon létre egy **új** VSTS-fiókot, vagy válasszon ki egy **meglévő** fiókot.  Válasszon egy **nevet** a VSTS-projektnek.  Válassza ki az **Azure-előfizetését**, a **helyet**, és adjon egy **nevet** az alkalmazásnak.  Ha elkészült, kattintson a **Kész** gombra.

    ![VSTS-adatok megadása](_img/azure-devops-project-github/vstsazureinfo.png)

1. Néhány percen belül betöltődik a **projekt irányítópultja** az Azure Portalon.  A rendszer beállít egy mintaalkalmazást a VSTS-fiókban található adattárban, végrehajtja a buildelést, és üzembe helyezi az alkalmazást az Azure-ban.  Ez az irányítópult biztosítja a GitHub-**kódtár**, a **VSTS CI-/CD-folyamat** és az **Azure-ban található alkalmazás** áttekinthetőségét.  Az irányítópult jobb oldalán válassza a **Tallózást** a futó alkalmazás megtekintéséhez.

    ![Irányítópult nézet](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Az Azure DevOps Project automatikusan konfigurálja a CI-buildet és a kiadási eseményindítót.  A kód a GitHubon vagy egy másik külső adattárban marad.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>A GitHub módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban 

Most már készen áll, hogy egy csapattal közösen dolgozzon az alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.  A GitHub-adattár bármely módosítása egy buildet indít el a VSTS-ben, és egy VSTS-kiadáskezelési definíció üzembe helyezést hajt végre az Azure-ban.

1.  Módosítsa az alkalmazást, és **véglegesítse** a GitHub-adattár módosítását.
2.  Néhány pillanat múlva a VSTS-ben elindul egy build.  Monitorozhatja a build állapotát a DevOps-projekt irányítópultján, vagy a böngészőben, a VSTS-fiók segítségével.
3.  Miután a build kész, **frissítse az alkalmazást** a böngészőben, és ellenőrizze, hogy megjelentek-e a módosítások.

## <a name="examine-the-vsts-cicd-pipeline"></a>A VSTS CI-/CD-folyamat vizsgálata

Az Azure DevOps-projekt automatikusan konfigurálta a teljes VSTS CI-/CD-folyamatot a VSTS-fiókban.  Vizsgálja meg és szükség szerinti szabja testre a folyamatot.  A VSTS-build és a kiadási definíciók megismeréséhez kövesse az alábbi lépéseket.

1. Az Azure DevOps Project irányítópultjának **tetején** válassza a **Folyamatok létrehozását**.  Ez a hivatkozás megnyit egy böngészőlapot, és megnyitja az új projekt VSTS-builddefinícióját.

1. Helyezze az egérmutatót a builddefiníció jobb oldalára, az **Állapot** mező mellé. Válassza a megjelenő **három pontot**.  Ez a művelet megnyitja a menüt, ahol több tevékenységet is végrehajthat, például várakozási sorba helyezhet egy új buildet, szüneteltethet egy buildet, vagy szerkesztheti a builddefiníciót.

1. Válassza a **Szerkesztés** elemet.

1. Ebben a nézetben **vizsgálja meg a különböző, a builddefinícióhoz tartozó feladatokat**.  A build különböző feladatokat hajt végre, például erőforrásokat olvas be a Git-adattárból, visszaállítja a függőségeket, és közzéteszi az üzembe helyezésekhez használt kimeneteket.

1. A builddefiníció tetején válassza a **builddefiníció nevét**.

1. Módosítsa a builddefiníció **nevét** egy közérthetőbb névre.  Válassza a **mentésre és várakozási sorba helyezésre** szolgáló elemet, majd a **Mentést**.

1. A builddefiníció neve alatt válassza az **Előzményeket**.  Ekkor megjelenik a build legutóbbi módosításainak naplója.  A VSTS nyomon követi a builddefiníció módosításait, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  Az Azure DevOps-projekt automatikusan létrehozott egy CI-eseményindítót, és az adattárban történő minden véglegesítés egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  A forgatókönyv alapján megadhat szabályzatokat bizonyos számú build megtartására vagy eltávolítására.

1. Válassza ki a **Létrehozás és kiadást**, majd a **Kiadásokat**.  Az Azure DevOps Project létrehozott egy VSTS-kiadási definíciót, amely az Azure-ban történő üzembe helyezéseket kezeli.

1. A böngésző bal oldalán válassza a kiadási definíció melletti **három pontot**, majd a **Szerkesztést**.

1. A kiadási definíció tartalmaz egy **folyamatot**, amely meghatározza a kiadási folyamatot.  Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált builddefiníció létrehozza az összetevőhöz használt kimenetet. 

1. Az **Elvetés** ikontól jobbra lévő területen válassza ki a **folyamatos üzembehelyezési eseményindítót**.  Ez a kiadási definíció egy olyan engedélyezett CD-eseményindítóval rendelkezik, amely minden alkalommal végrehajtja az üzembe helyezést, amikor elérhetővé válik egy új buildösszetevő.  Ha szeretné, letilthatja az eseményindítót, így az üzembe helyezéseket manuálisan kell majd végrehajtani. 

1. A böngésző bal oldalán válassza a **Feladatokat**.  A feladatok az üzembehelyezési folyamat által végrehajtott tevékenységek.  Ebben a példában létrejött egy feladat az **Azure App Service** üzembe helyezéséhez.

1. A böngésző jobb oldalán válassza a **Kiadások megtekintését**.  Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza az egyik kiadás mellett a **három pontot**, majd a **Megnyitást**.  Ebben a nézetben több menüt is megtekinthet, például a kiadás összegzését, a társított munkaelemeket vagy a teszteket.

1. Válassza a **Véglegesítéseket**.  Ez a nézet az adott üzembe helyezéshez hozzárendelt kódvéglegesítéseket jeleníti meg. 

1. Válassza a **Naplókat**.  A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban.  Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát.  Az Azure DevOps Project automatikusan konfigurált egy Application Insights-erőforrást az alkalmazáshoz.  Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. Lépjen az **Azure DevOps Project** irányítópultjára az Azure Portalon.  Az irányítópult jobb alsó részén kattintson az alkalmazáshoz tartozó **Application Insights** hivatkozásra.

1. Megnyílik az **Application Insights** panel az Azure Portalon.  Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Válassza az **Időtartomány**, majd az **Elmúlt óra** lehetőséget.  Válassza a **Frissítést** az eredmények szűréséhez.  Ekkor megjelenik az utolsó 60 perc minden tevékenysége.  Az időtartományból való kilépéshez válassza az **x** lehetőséget.

1. Válassza a **Riasztások**, majd a **+ Metrikariasztás hozzáadása** lehetőséget.  

1. Adja meg a riasztás **nevét**.

1. Válassza a **forrásmódosításhoz** a legördülő menüt.  Válassza ki az **App Service-erőforrást**.

1. Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik.  Válassza a **Metrika** legördülő menüt a különböző riasztásmetrikák megtekintéséhez.  Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Jelölje be a jelölőnégyzetet az **e-mailes értesítés a tulajdonosoknak, közreműködőknek és olvasóknak történő küldéséhez**.  További műveleteket is elvégezhet, ha a riasztás működésbe lép egy Azure-beli logikai alkalmazás végrehajtásakor.

1. A riasztás létrehozásához kattintson az **OK** gombra.  Néhány pillanat múlva a riasztás aktívként jelenik meg az irányítópulton.  **Lépjen ki** a Riasztások területéről, és lépjen vissza az **Application Insights panelre**.

1. Válassza a **Rendelkezésre állás**, majd a **+ Teszt hozzáadása** lehetőséget. 

1. Adja meg a **teszt nevét**, ezután válassza a **Létrehozást**.  Létrejön egy egyszerű ping-teszt az alkalmazás rendelkezésre állásának ellenőrzéséhez.  Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az ebben a rövid útmutatóban létrehozott Azure App Service-t és a kapcsolódó erőforrásokat az Azure DevOps Project irányítópultján található **Törléssel**.

## <a name="next-steps"></a>További lépések

Amikor ebben az oktatóanyagban konfigurálta a CI-/CD-folyamatot, automatikusan létrejött egy build és egy kiadási definíció a VSTS-projektben. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási definíciókat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure DevOps-projekt létrehozása
> * A GitHub-adattárhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * A VSTS és egy Azure-előfizetés konfigurálása 
> * A GitHub módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * A VSTS CI-/CD-folyamat vizsgálata
> * Az Azure Application Insights monitorozásának konfigurálása

Tekintse át az oktatóanyagokat a VSTS-folyamattal kapcsolatos további részletekért:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
