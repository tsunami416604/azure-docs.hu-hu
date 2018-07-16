---
title: ASP.NET Core-alkalmazás üzembe helyezése az Azure Kubernetes Service (AKS) szolgáltatásban az Azure DevOps Project használatával | VSTS-oktatóanyag
description: A DevOps Project megkönnyíti az Azure-ral való ismerkedést. Az Azure DevOps Projecttel néhány gyors lépésben, könnyedén üzembe helyezheti az ASP.NET Core-alkalmazást az Azure Kubernetes Service (AKS) szolgáltatásban.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 98c585cc0c6136496fe190cd3eceb27fd856af21
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967366"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>Oktatóanyag: ASP.NET Core-alkalmazás üzembe helyezése az Azure Kubernetes Service (AKS) szolgáltatásban az Azure DevOps Project használatával

Az Azure DevOps Projecttel meglévő kódok és a Git-adattár segítségével, vagy valamelyik mintaalkalmazásból egyszerűen hozhat létre egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot az Azure-ban.  A DevOps Project automatikusan létrehoz olyan Azure-erőforrásokat, mint az AKS, létrehoz és konfigurál a CI-hoz való builddefiníciót tartalmazó kiadási folyamatot a VSTS-ben, továbbá beállít egy kiadási definíciót a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * Azure DevOps-projekt létrehozása egy ASP.NET Core-alkalmazáshoz és az AKS-hez
> * A VSTS és egy Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A VSTS CI-builddefiníció vizsgálata
> * A VSTS CD kiadáskezelési definíciójának vizsgálata
> * A VSTS módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>Azure DevOps-projekt létrehozása egy ASP.NET Core-alkalmazáshoz és az AKS-hez

Az Azure DevOps Project létrehoz egy CI-/CD-folyamatot a VSTS-ben.  Létrehozhat egy **új VSTS-** fiókot, vagy használhat egy **meglévő fiókot** is.  Az Azure DevOps Project létrehozza az **Azure-erőforrásokat** is, például egy AKS-fürtöt a választott **Azure-előfizetésben**.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali navigációs sávban válassza az **Erőforrás létrehozása** ikont, majd keresse meg a **DevOps-projektet**.  Válassza a **Létrehozás** elemet.

    ![Folyamatos kézbesítés indítása](_img/azure-devops-project-aks/fullbrowser.png)

1. Válassza a **.NET**, majd a **Tovább** lehetőséget.

1. Az **Alkalmazás-keretrendszer kiválasztásánál** válassza az **ASP.NET Core** lehetőséget, majd kattintson a **Tovább** elemre.

1. Válassza a **Kubernetes-szolgáltatás**, majd a **Tovább** lehetőséget.  

## <a name="configure-vsts-and-an-azure-subscription"></a>A VSTS és egy Azure-előfizetés konfigurálása

1. Hozzon létre egy **új** VSTS-fiókot, vagy válasszon ki egy **meglévő** fiókot.  Válasszon egy **nevet** a VSTS-projektnek.  

1. Válassza ki az **Azure-előfizetését**.

1. A további Azure-konfigurációs beállítások megtekintéséhez kattintson a **Módosítás** hivatkozásra, és határozza meg a **Kubernetes-fürt** **csomópontjainak számát**.  Itt számos lehetőség található az Azure-szolgáltatások típusának és helyének konfigurálásához.
 
1. Lépjen ki az Azure-konfigurációs területről, és válassza a **Kész** lehetőséget.

1. A folyamat befejezése néhány percet vesz igénybe.  A rendszer beállít egy ASP.NET Core-mintaalkalmazást a VSTS-fiókban található adattárban, létrehoz egy AKS-fürtöt, végrehajtja a buildelést és a kiadást, majd üzembe helyezi az alkalmazást az Azure-ban.  

    A befejezést követően az Azure DevOps **Project irányítópultja** betöltődik az Azure Portalon.  Az **Azure DevOps Project irányítópultját** közvetlenül is elérheti az **Azure Portal** **Minden erőforrás** területéről.  

    Ez az irányítópult biztosítja a VSTS-**kódtár**, a **VSTS CI-/CD-folyamat** és az **AKS-fürt** áttekinthetőségét.  További beállításokat is konfigurálhat a VSTS-ben.  Az irányítópult jobb oldalán válassza a **Tallózás** lehetőséget a futó alkalmazás megtekintéséhez.

## <a name="examine-the-aks-cluster"></a>Az AKS-fürt vizsgálata

Az Azure DevOps Project automatikusan konfigurál egy AKS-fürtöt.  Megvizsgálhatja és testre szabhatja a fürtöt.  Az AKS megismeréséhez kövesse az alábbi lépéseket.

1. Lépjen az **Azure DevOps Project irányítópultjára**.

1. A DevOps-projekt irányítópultjának jobb oldalán válassza a **Kubernetes-szolgáltatást**.

1. Megjelenik az AKS-fürt panelje.  Ebben a nézetben számos műveletet elvégezhet, például **monitorozhatja a tároló állapotát**, **naplókat kereshet**, vagy megnyithatja a **Kubernetes-irányítópultot**.

1. A képernyő jobb oldalán válassza **a Kubernetes-irányítópult megtekintését**.  Másik lehetőségként követheti a lépéseket a Kubernetes-irányítópult megnyitásához.

## <a name="examine-the-vsts-ci-build-definition"></a>A VSTS CI-builddefiníció vizsgálata

Az Azure DevOps Project automatikusan konfigurál egy teljes VSTS CI-/CD-folyamatot a VSTS-fiókban.  Megvizsgálhatja és testre szabhatja a folyamatot.  A VSTS-builddefiníció megismeréséhez kövesse az alábbi lépéseket.

1. Lépjen az **Azure DevOps Project irányítópultjára**.

1. Az **Azure DevOps Project irányítópultjának** **tetején** válassza a **Folyamatok létrehozását**.  Ez a hivatkozás megnyit egy böngészőlapot, és megnyitja az új projekt VSTS-builddefinícióját.

1. Helyezze az egérmutatót a builddefiníció jobb oldalára, az **Állapot** mező mellé. Válassza a megjelenő **három pontot**.  Ez a művelet megnyitja a menüt, ahol több tevékenységet is végrehajthat, például **várakozási sorba helyezhet egy új buildet**, **szüneteltethet egy buildet** vagy **szerkesztheti a builddefiníciót**.

1. Válassza a **Szerkesztés** elemet.

1. Ebben a nézetben **vizsgálja meg a különböző, a builddefinícióhoz tartozó feladatokat**.  A build különböző feladatokat hajt végre, például erőforrásokat olvas be a VSTS Git-adattárból, visszaállítja a függőségeket és közzéteszi az üzembe helyezésekhez használt kimeneteket.

1. A builddefiníció tetején válassza a **builddefiníció nevét**.

1. Módosítsa a builddefiníció **nevét** egy közérthetőbb névre.  Válassza a **mentésre és várakozási sorba helyezésre** szolgáló elemet, majd a **Mentést**.

1. A builddefiníció neve alatt válassza az **Előzményeket**.  Ekkor megjelenik a build legutóbbi módosításainak naplója.  A VSTS nyomon követi a builddefiníció módosításait, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  Az Azure DevOps-projekt automatikusan létrehozott egy CI-eseményindítót, és az adattárban történő minden véglegesítés egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  A forgatókönyv alapján megadhat szabályzatokat bizonyos számú build megtartására vagy eltávolítására.

## <a name="examine-the-vsts-cd-release-management-definition"></a>A VSTS CD kiadáskezelési definíciójának vizsgálata

Az Azure DevOps Project automatikusan létrehozza és konfigurálja a VSTS-fiókból az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket.  A lépések közé tartozik az Azure-szolgáltatás kapcsolatának konfigurálása a VSTS Azure-előfizetésben történő hitelesítéséhez.  Az automatizálás is létrehoz egy VSTS-kiadási definíciót, és a kiadási definíció biztosítja a CD-t az Azure számára.  A VSTS-kiadási definíció megismeréséhez kövesse az alábbi lépéseket.

1. Válassza ki a **Létrehozás és kiadást**, majd a **Kiadásokat**.  Az Azure DevOps Project létrehozott egy VSTS-kiadási definíciót, amely az Azure-ban történő üzembe helyezéseket kezeli.

1. A böngésző bal oldalán válassza a kiadási definíció melletti **három pontot**, majd a **Szerkesztést**.

1. A kiadási definíció tartalmaz egy **folyamatot**, amely meghatározza a kiadási folyamatot.  Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált builddefiníció létrehozza az összetevőhöz használt kimenetet. 

1. Az **Elvetés** ikon jobb oldalán válassza a (villámként megjelenő) **Folyamatos üzembehelyezési eseményindító** **ikont**.  Ez a kiadási definíció egy engedélyezett CD-eseményindítóval rendelkezik.  Az eseményindító minden alkalommal létrehoz egy üzemelő példányt, amikor elérhetővé válik egy új buildösszetevő.  Ha szeretné, letilthatja az eseményindítót, így az üzembe helyezéseket manuálisan kell majd végrehajtani. 

1. A böngésző jobb oldalán válassza a **Kiadások megtekintését**.  Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza az egyik kiadás mellett a **három pontot**, majd a **Megnyitást**.  Ebben a nézetben több menüt is megtekinthet, például a **kiadás összegzését**, **a társított munkaelemeket** vagy a **Teszteket**.

1. Válassza a **Véglegesítéseket**.  Ez a nézet az adott üzembe helyezéshez hozzárendelt kódvéglegesítéseket jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez összehasonlíthatja a kiadásokat.

1. Válassza a **Naplókat**.  A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban.  Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>A VSTS módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban 

 > [!NOTE]
 > Az alábbi lépések a CI-/CD-folyamatot a webalkalmazás egy egyszerű szöveges módosításával tesztelik.

Most már készen áll, hogy egy csapattal közösen dolgozzon az alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.  A VSTS Git-adattárának bármely módosítása egy buildet indít el a VSTS-ben, és egy VSTS-kiadáskezelési definíció üzembe helyezi a módosításokat az Azure-ban.  Kövesse az alábbi lépéseket, vagy használjon egyéb módszereket az adattár módosításainak véglegesítéséhez.  Például **klónozhatja** a Git-adattárat a kedvenc eszközében vagy az IDE-ben, majd leküldheti a módosításokat az adattárba.

1. A VSTS menüből válassza a **Kód**, majd a **Fájlok** lehetőséget, és lépjen az adattárához.
1. Lépjen a **Views\Home** könyvtárra, válassza az **Index.cshtml** fájl melletti **három pontot**, majd a **Szerkesztést**.

1. Módosítsa a fájlt, például az egyik **div címkén** belüli szöveget.  A jobb felső részen kattintson a **Véglegesítés** elemre.  Ismét válassza a **Véglegesítést** a módosítás leküldéséhez. 

1. Néhány pillanat múlva a **VSTS-ben elindul egy build**, majd a rendszer egy kiadást hajt végre a módosítások üzembe helyezéséhez.  Monitorozhatja a **build állapotát** a DevOps-projekt irányítópultján, vagy a böngészőben, a VSTS-fiók segítségével.

1. Miután a kiadás befejeződött, **frissítse az alkalmazást** a böngészőben, és ellenőrizze, hogy megjelentek-e a módosítások.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

 > [!NOTE]
 > Az alábbi lépések véglegesen törlik az erőforrásokat.  Ezt a funkciót csak a megjelenő üzenet alapos átolvasása után használja.

Ha éppen tesztel, eltávolíthatja az erőforrásokat a költségek elkerülése érdekében.  Ha már nincs rájuk szükség, törölheti az ebben az oktatóanyagban létrehozott Azure Kubernetes-fürtöt és a kapcsolódó erőforrásokat az Azure DevOps Project irányítópultján található **Törléssel**.  **Legyen óvatos**, mivel a Törlés funkció megsemmisíti az Azure DevOps Projecttel létrehozott adatokat az Azure-ban és a VSTS-ben is, és később nem lesznek lekérhetők.

1. Az **Azure Portalról** lépjen át az **Azure DevOps Projectre**.
2. Az irányítópult **jobb felső** részén válassza a **Törlést**.  A megjelenő üzenet elolvasása után válassza az **Igen** lehetőséget az erőforrások **végleges törléséhez**.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási definíciókat. Ezt a CI-/CD-mintát egyéb projektek sablonjaként is használhatja.  Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure DevOps-projekt létrehozása egy ASP.NET Core-alkalmazáshoz és az AKS-hez
> * A VSTS és egy Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A VSTS CI-builddefiníció vizsgálata
> * A VSTS CD kiadáskezelési definíciójának vizsgálata
> * A VSTS módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Az erőforrások eltávolítása

További információk a Kubernetes-irányítópult használatával kapcsolatban:

> [!div class="nextstepaction"]
> [A Kubernetes-irányítópult használata](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
