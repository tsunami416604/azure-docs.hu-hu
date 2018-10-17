---
title: ASP.NET-alkalmazás üzembe helyezése az Azure-beli virtuális gépeken az Azure DevOps Projecttel | Azure DevOps Services-oktatóanyag
description: A DevOps Project megkönnyíti az Azure-ral való ismerkedést. Az Azure DevOps Project segítségével néhány gyors lépésben, könnyen üzembe helyezheti az ASP.NET-alkalmazást egy Azure-beli virtuális gépen.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299134"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>Oktatóanyag: ASP.NET-alkalmazás üzembe helyezése az Azure-beli virtuális gépeken az Azure DevOps Projecttel

Az Azure DevOps Projecttel meglévő kódok és a Git-adattár segítségével, vagy valamelyik mintaalkalmazásból egyszerűen hozhat létre egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot az Azure-ban.  A DevOps Project automatikusan létrehoz olyan Azure-erőforrásokat, mint egy Azure-beli új virtuális gép, létrehoz és konfigurál egy CI-hoz való buildelési folyamatot tartalmazó kiadási folyamatot az Azure DevOpsban, továbbá beállít egy kiadási folyamatot a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * Azure DevOps-projekt létrehozása egy ASP.NET-alkalmazáshoz
> * Az Azure DevOps Services és egy Azure-előfizetés konfigurálása 
> * Az Azure DevOps Services CI-folyamat vizsgálata
> * Az Azure DevOps Services CD-folyamat vizsgálata
> * Az Azure DevOps Services módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>Azure DevOps-projekt létrehozása egy ASP.NET-alkalmazáshoz

Az Azure DevOps Project létrehoz egy CI-/CD-folyamatot az Azure-ban.  Létrehozhat egy **új Azure DevOps Services**-szervezetet, vagy használhat egy **meglévő szervezetet** is.  Az Azure DevOps Project létrehozza az **Azure-erőforrásokat** is, például virtuális gépeket a választott **Azure-előfizetésben**.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali navigációs sávban válassza az **+ Új** ikont, majd keresse meg a **DevOps Project** elemet.  Válassza a **Létrehozás** elemet.

    ![Folyamatos kézbesítés indítása](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza a **.NET**, majd a **Tovább** lehetőséget.

1. Az **Alkalmazás-keretrendszer kiválasztásánál** válassza az **ASP.NET** lehetőséget, majd kattintson a **Tovább** elemre. 

1. Az előző lépésekben kiválasztott alkalmazás-keretrendszer meghatározza az Azure-szolgáltatás üzembehelyezési céljának itt elérhető típusát.  Válassza a **Virtuális gép**, majd a **Tovább** lehetőséget.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Az Azure DevOps Services és egy Azure-előfizetés konfigurálása

1. Hozzon létre egy **új** Azure DevOps Services-szervezetet, vagy válasszon egy **meglévő** szervezetet.  Válasszon egy **nevet** az Azure DevOps-projektnek.  

1. Válassza ki **Azure-előfizetésének szolgáltatásait**.  Ha szeretné, kattinthat a **Módosítás** hivatkozásra is, majd további konfigurációs részleteket adhat meg, például módosíthatja az Azure-erőforrások helyét.
 
1. Adja meg a **virtuális gép nevét**, a **felhasználónevet** és a **jelszót** az új Azure-beli virtuális géphez, majd válassza a **Kész** lehetőséget.

1. A virtuális gép néhány percen belül használatra kész.  A rendszer beállít egy ASP.NET-mintaalkalmazást az Azure DevOps Services-szervezethez tartozó adattárban, végrehajtja a buildelést és a kiadást, majd üzembe helyezi az alkalmazást az újonnan létrehozott Azure-beli virtuális gépen.  

    A befejezést követően az Azure DevOps **Project irányítópultja** betöltődik az Azure Portalon.  Az **Azure DevOps Project irányítópultját** közvetlenül is elérheti az **Azure Portal** **Minden erőforrás** területéről.  

    Ez az irányítópult biztosítja az Azure DevOps Services-**kódtár**, az **Azure CI-/CD-folyamat** és az **Azure-ban futó alkalmazás** áttekinthetőségét.    

    ![Irányítópult nézet](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Az Azure DevOps Project automatikusan konfigurálja a CI-buildet és a kiadási eseményindítót, amely automatikusan üzembe helyezi az adattárban a kód módosításait.  További beállításokat is konfigurálhat az Azure DevOpsban.  Az irányítópult jobb oldalán válassza a **Tallózást** a futó alkalmazás megtekintéséhez.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Az Azure DevOps Services CI-folyamat vizsgálata
 
Az Azure DevOps Project automatikusan konfigurál egy teljes Azure CI-/CD-folyamatot az Azure DevOps Services-szervezetben.  Megvizsgálhatja és testre szabhatja a folyamatot.  Az Azure DevOps Services buildelési folyamatának megismeréséhez kövesse az alábbi lépéseket.

1. Az **Azure DevOps Project irányítópultjának** **tetején** válassza a **Folyamatok létrehozása** lehetőséget.  Ez a hivatkozás megnyit egy böngészőlapot, és megnyitja az új projekt Azure DevOps Services buildelési folyamatát.

1. Helyezze az egérmutatót a buildelési folyamat jobb oldalára, az **Állapot** mező mellé. Válassza a megjelenő **három pontot**.  Ez a művelet megnyitja a menüt, ahol több tevékenységet is végrehajthat, például **várakozási sorba helyezhet egy új buildet**, **szüneteltethet egy buildet** vagy **szerkesztheti a buildelési folyamatot**.

1. Válassza a **Szerkesztés** elemet.

1. Ebben a nézetben **vizsgálja meg a buildelési folyamathoz tartozó különböző feladatokat**.  A build különböző feladatokat hajt végre, például erőforrásokat olvas be az Azure DevOps Services Git-adattárából, visszaállítja a függőségeket, és közzéteszi az üzembe helyezésekhez használt kimeneteket.

1. A buildelési folyamat tetején válassza a **buildelési folyamat nevét**.

1. Módosítsa a buildelési folyamat **nevét** egy közérthetőbb névre.  Válassza a **mentésre és várakozási sorba helyezésre** szolgáló elemet, majd a **Mentést**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  Ekkor megjelenik a build legutóbbi módosításainak naplója.  Az Azure DevOps Services nyomon követi a buildelési folyamat módosításait, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  Az Azure DevOps Project automatikusan létrehozott egy CI-eseményindítót, és az adattárban történő minden véglegesítés egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  A forgatókönyv alapján megadhat szabályzatokat bizonyos számú build megtartására vagy eltávolítására.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Az Azure DevOps Services CD-folyamat vizsgálata

Az Azure DevOps Project automatikusan létrehozza és konfigurálja az Azure DevOps Services-szervezetből az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket.  A lépések közé tartozik az Azure-szolgáltatás kapcsolatának konfigurálása az Azure DevOps Services Azure-előfizetésben történő hitelesítéséhez.  Az automatizálás létrehoz egy Azure DevOps Services CD-folyamatot is, amely biztosítja a CD-t az Azure számára.  Az Azure DevOps Services CD-folyamat megismeréséhez kövesse az alábbi lépéseket.

1. Válassza ki a **Létrehozás és kiadást**, majd a **Kiadásokat**.  Az Azure DevOps Project létrehozott egy Azure DevOps Services-kiadási folyamatot, amely az Azure-ban történő üzembe helyezéseket kezeli.

1. A böngésző bal oldalán válassza a kiadási folyamat melletti **három pontot**, majd a **Szerkesztés** elemet.

1. A kiadási folyamat tartalmaz egy **folyamatot**, amely meghatározza a kiadási folyamatot.  Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált buildelési folyamat létrehozza az összetevőhöz használt kimenetet. 

1. Az **Elvetés** ikon jobb oldalán válassza a (villámként megjelenő) **Folyamatos üzembehelyezési eseményindító** **ikont**.  Ez a kiadási folyamat egy engedélyezett CD-eseményindítóval rendelkezik.  Az eseményindító minden egyes alkalommal elindít egy üzembe helyezést, amikor elérhetővé válik egy új buildösszetevő.  Ha szeretné, letilthatja az eseményindítót, így az üzembe helyezéseket manuálisan kell majd végrehajtani. 

1. A böngésző bal oldalán válassza a **Feladatokat**, majd válassza ki a **környezetet**.  

1. A feladatok azok a tevékenységek, amelyeket az üzembehelyezési folyamat hajt végre, és ezek **Fázisok** szerint vannak csoportosítva.  Ehhez a kiadási folyamathoz két **Fázis** tartozik.  Az első fázis egy **Azure-erőforráscsoport üzembehelyezési** feladatát tartalmazza, amely konfigurálja a virtuális gépet az üzembe helyezéshez, és hozzáadja az új virtuális gépet az **Azure DevOps-üzembehelyezési csoporthoz**.  Az Azure DevOps-ban lévő virtuálisgép-üzembehelyezési csoport kezeli az **üzembehelyezési célgépek** logikai csoportjait.

1. A második fázisban létrejött egy **IIS-webalkalmazást kezelő** feladat, amely egy IIS-webhelyet hoz létre a virtuális gépen.  Létrejött egy második **IIS-webalkalmazás-üzembehelyezési** feladat a webhely üzembe helyezéséhez.

1. A böngésző jobb oldalán válassza a **Kiadások megtekintését**.  Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza az egyik kiadás mellett a **három pontot**, majd a **Megnyitást**.  Ebben a nézetben több menüt is megtekinthet, például a **kiadás összegzését**, **a társított munkaelemeket** vagy a **Teszteket**.

1. Válassza a **Véglegesítéseket**.  Ez a nézet az adott üzembe helyezéshez hozzárendelt kódvéglegesítéseket jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban.  Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Az Azure DevOps Services módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban 

Most már készen áll, hogy egy csapattal közösen dolgozzon az alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.  Az Azure DevOps Services Git-adattárának bármely módosítása egy buildet indít el az Azure DevOps Services-ben, és egy Azure DevOps Services CD-folyamat üzembe helyezést hajt végre az Azure-beli virtuális gépen.  Kövesse az alábbi lépéseket, vagy használjon egyéb módszereket az adattár módosításainak véglegesítéséhez.  A kód módosításai elindítják a CI-/CD-folyamatot, és automatikusan üzembe helyezik az új módosításokat az Azure-beli virtuális gépen található IIS-webhelyen.

1. Az Azure DevOps Services menüből válassza a **Kód** lehetőséget, és lépjen az adattárához.

1. Lépjen a **Views\Home** könyvtárra, válassza az **Index.cshtml** fájl melletti **három pontot**, majd a **Szerkesztést**.

1. Módosítsa a fájlt, például az egyik **div címkén** belüli szöveget.  A jobb felső részen kattintson a **Véglegesítés** elemre.  Ismét válassza a **Véglegesítést** a módosítás leküldéséhez. 

1. Néhány pillanat múlva az **Azure DevOps Servicesben elindul egy build**, majd a rendszer egy kiadást hajt végre a módosítások üzembe helyezéséhez.  Monitorozhatja a **build állapotát** a DevOps Project irányítópultján, vagy a böngészőben, az Azure DevOps Services-szervezet segítségével.

1. Miután a kiadás befejeződött, **frissítse az alkalmazást** a böngészőben, és ellenőrizze, hogy megjelentek-e a módosítások.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát.  Az Azure DevOps Project automatikusan konfigurált egy Application Insights-erőforrást az alkalmazáshoz.  Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. Lépjen az **Azure DevOps Project** irányítópultjára az **Azure Portalon**.  Az irányítópult jobb alsó részén kattintson az alkalmazáshoz tartozó **Application Insights** hivatkozásra.

1. Megnyílik az **Application Insights** panel az Azure Portalon.  Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Válassza az **Időtartomány**, majd az **Elmúlt óra** lehetőséget.  Válassza a **Frissítést** az eredmények szűréséhez.  Ekkor megjelenik az utolsó 60 perc minden tevékenysége.  Az időtartományból való kilépéshez válassza az **x** lehetőséget.

1. A **Riasztásokat** és az egyéb hasznos hivatkozásokat az irányítópult tetején találja.  Válassza a **Riasztások**, majd a **+ Metrikariasztás hozzáadása** lehetőséget.

1. Adja meg a riasztás **nevét**.

1. Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik.  Válassza a **Metrika** legördülő menüt a különböző riasztásmetrikák megtekintéséhez.  Konfigurálhat például **ASP.NET-kérésvégrehajtási időt** egy ASP.NET-alkalmazáshoz.  Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Jelölje be a jelölőnégyzetet az **e-mailes értesítés a tulajdonosoknak, közreműködőknek és olvasóknak történő küldéséhez**.  További műveleteket is elvégezhet, ha a riasztás működésbe lép egy Azure-beli logikai alkalmazás végrehajtásakor.

1. A riasztás létrehozásához kattintson az **OK** gombra.  Néhány pillanat múlva a riasztás aktívként jelenik meg az irányítópulton.  **Lépjen ki** a Riasztások területéről, és lépjen vissza az **Application Insights panelre**.

1. Válassza a **Rendelkezésre állás**, majd a **+ Teszt hozzáadása** lehetőséget. 

1. Adja meg a **teszt nevét**, ezután válassza a **Létrehozást**.  Ez létrehoz egy egyszerű ping-tesztet az alkalmazás rendelkezésre állásának ellenőrzéséhez.  Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

 > [!NOTE]
 > Az alábbi lépések véglegesen törlik az erőforrásokat.  Ezt a funkciót csak a megjelenő üzenet alapos átolvasása után használja.

Ha éppen tesztel, eltávolíthatja az erőforrást a költségek elkerülése érdekében.  Ha már nincs rájuk szükség, törölheti az ebben az oktatóanyagban létrehozott Azure-beli virtuális gépet és a kapcsolódó erőforrásokat az Azure DevOps Project irányítópultján található **Törléssel**.  **Legyen óvatos**, mivel a törlési funkció megsemmisíti az Azure DevOps Projecttel létrehozott adatokat az Azure-ban és az Azure DevOpsban is, és később nem lesznek lekérhetők.

1. Az **Azure Portalról** lépjen át az **Azure DevOps Projectre**.
2. Az irányítópult **jobb felső** részén válassza a **Törlést**.  A megjelenő üzenet elolvasása után válassza az **Igen** lehetőséget az erőforrások **végleges törléséhez**.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb projektek sablonjaként is használhatja.  Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure DevOps-projekt létrehozása egy ASP.NET-alkalmazáshoz
> * Az Azure DevOps Services és egy Azure-előfizetés konfigurálása 
> * Az Azure DevOps Services CI-folyamat vizsgálata
> * Az Azure DevOps Services CD-folyamat vizsgálata
> * Az Azure DevOps Services módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

Tekintse át a következő oktatóanyagokat az Azure CI-/CD-folyamattal kapcsolatos további részletekért:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
