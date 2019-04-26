---
title: 'Oktatóanyag: Az ASP.NET-alkalmazás telepítése Azure-beli virtuális gépek az Azure DevOps Projects használatával'
description: A DevOps Projects megkönnyíti az első lépései az Azure-ban, és az ASP.NET-alkalmazás üzembe helyezése az Azure-beli virtuális gépek néhány gyors lépéssel.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 05643f342d51d99645d3c9204d6e63adcf2a0a73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546480"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Oktatóanyag: Az ASP.NET-alkalmazás telepítése Azure-beli virtuális gépek az Azure DevOps Projects használatával

Az Azure DevOps Projects megjelenít egy egyszerűsített felület, ahol a meglévő kódot és a Git-adattár, vagy válasszon egy mintaalkalmazást, hozzon létre egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot az Azure-bA. 

DevOps-projektek is:
* Automatikusan létrehozza az Azure-erőforrások, például egy új Azure virtuális gép (VM).
* Hoz létre, és konfigurálja a kibocsátási folyamat az Azure DevOps, amely tartalmazza a folyamatos integrációhoz buildelési folyamat.
* CD beállítja a kibocsátási folyamat. 
* Az Azure Application Insights-erőforrás, figyelés hoz létre.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az ASP.NET-alkalmazás üzembe helyezése a DevOps Projects segítségével
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Az ASP.NET-alkalmazás üzembe helyezése a DevOps Projects segítségével

A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects például a virtuális gépek Azure-erőforrások is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **új**.

1. A Keresés mezőbe írja be a **DevOps Projects**, majd válassza ki **létrehozás**.

    ![A DevOps Projects-irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza ki **.NET**, majd válassza ki **tovább**.

1. Alatt **válassza ki az alkalmazás keretrendszer**, jelölje be **ASP.NET**, majd válassza ki **tovább**.  
    Az alkalmazási keretrendszer, amely az előző lépésben kiválasztott szabja meg, amely innen érhető el az Azure szolgáltatás üzembe helyezési cél típusát. 

1. A virtuális gépet, majd válassza ki és **tovább**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetés szolgáltatásait.  
    Másik lehetőségként kiválaszthatja **módosítása** és írja be a konfigurációs részleteket, például az Azure-erőforrások helyét.
 
1. Adja meg a virtuális gép nevét, a felhasználónevet és jelszót az új Azure-beli virtuálisgép-erőforrás, és válassza **kész**.  
    Néhány perc elteltével az Azure virtuális gép készen áll. Egy ASP.NET-mintaalkalmazás be van állítva a tárházban, a szervezetben az Azure DevOps-build és kiadás hajtja végre, és az alkalmazás központi telepítése az újonnan létrehozott Azure virtuális géphez. 

    Miután elkészült, a DevOps Projects irányítópult jelenik meg az Azure Portalon. Közvetlenül az irányítópulton is elérheti **összes erőforrás** az Azure Portalon. 

    Az irányítópult biztosít az Azure DevOps kódtár, a CI/CD-folyamat és az Azure-ban futó alkalmazását.   

    ![Irányítópult nézet](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projects automatikusan konfigurálja a CI-build és kiadás eseményindító, amely üzembe helyezi a kódot vált a tárház. További beállításokat is konfigurálhat az Azure DevOpsban. A futó alkalmazás megtekintéséhez jelölje ki **Tallózás**.
    
## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata
 
A DevOps Projects automatikusan konfigurálja a CI/CD-folyamat Azure folyamatokban. Megvizsgálhatja és testre szabhatja a folyamatot. Ismerkedjen meg a buildelési folyamat, tegye a következőket:

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

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

A DevOps Projects automatikusan létrehozza és konfigurálja az Azure-előfizetéshez az Azure DevOps-szervezet telepítéséhez szükséges lépéseket. Ide tartozik az Azure DevOps hitelesítéséhez az Azure-előfizetéshez az Azure service-kapcsolat beállítása. Az automation is létrehoz egy CD-folyamat, amely biztosít a CD az Azure virtuális gépen. További információ az Azure DevOps CD-folyamat, tegye a következőket:

1. Válassza ki **készítése és kiadása**, majd válassza ki **kiadásokban**.  
    DevOps-projektek az Azure-bA központi telepítések felügyeletéhez szükséges kiadási folyamatot hoz létre.

1. A kibocsátási folyamat melletti három pontra (...), majd válassza ki és **szerkesztése**.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A buildelési folyamat, az előző lépésekben vizsgálni összetevő szolgálja ki a kimenetet eredményez. 

1. Mellett a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**.  
    A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely végrehajtja a központi telepítés minden alkalommal, amikor egy új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. Válassza a bal oldali **feladatok**, majd válassza ki a környezetben.  
    Feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat, és vannak csoportosítva, fázisban történik. A kibocsátási folyamat két fázisban történik:
    * Az első fázis tartalmaz egy Azure erőforráscsoport-telepítés a feladat, amely két dolgot eredményez:
      * Konfigurálja a virtuális gép üzembe helyezéshez
      * Az Azure DevOps-üzembe helyezési csoport ad hozzá az új virtuális Gépet. Az Azure DevOps, a virtuális gép üzembe helyezési csoport logikai üzembe helyezési célgéphez kezeli.
    * A második fázisban egy IIS webes alkalmazás kezelheti a feladat egy IIS-webhelyen a virtuális Gépet hoz létre. Második IIS webes alkalmazás üzembe helyezése feladat jön létre a hely telepítéséhez.

1. Válassza ki a jobb **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Egy kiadási melletti három pontra (...), majd válassza ki és **nyílt**.  
    Áttekintheti a több menük, például a kiadás összegzését, a társított munkaelemekhez és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA 

Most már készen áll, az alkalmazás a csapat együttműködhet egy CI/CD-folyamat, amely automatikusan telepíti a legújabb munkáját a webhely használatával. Minden módosítás a Git-tárház egy build elindítja az Azure DevOps, és a egy CD-folyamat végrehajtja a központi telepítés az Azure-bA. Kövesse az ebben a szakaszban az eljárást, vagy egy másik módszer használatával véglegesítse a módosításokat az adattárhoz. A kód módosításait a CI/CD-folyamat kezdeményezése, és automatikusan telepítheti őket a módosításokat az IIS-webhelyet az Azure virtuális gépen.

1. A bal oldali panelen válassza ki a **kód**, és folytassa az adattárhoz.

1. Nyissa meg a *Views\Home* könyvtár, válassza a három pontra (...), a *Index.cshtml* fájlt, és válassza ki **szerkesztése**.

1. Módosítja a fájlt, például a valamilyen szöveget a div címkék egyikében. 

1. Jobb felső sarokban, válassza ki a **véglegesítése**, majd válassza ki **véglegesítése** újra, hogy a módosítás leküldése.  
    Néhány pillanat múlva build elindítja az Azure DevOps, és egy kiadási üzembe helyezéséhez a módosításokat hajt végre. A DevOps Projects információközpontban vagy a böngészőben az Azure DevOps vállalattal build állapotának figyelésére.

1. A kiadás befejezése után frissítse az alkalmazás ellenőrizheti a módosításokat.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát. A DevOps Projects automatikusan konfigurálja az Application Insights-erőforrás, az alkalmazás. Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. Az Azure Portalon nyissa meg a DevOps Projects-irányítópultot. 

1. A bal alsó részen válassza a **Application Insights** hivatkozást az alkalmazáshoz.  
    A **Application Insights** panel nyílik meg. Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

    ![Az Application Insights panel](_img/azure-devops-project-github/appinsights.png) 

1. Válassza ki **időtartomány**, majd válassza ki **utolsó óra**. Az eredmények szűréséhez válassza **frissítés**.  
    Most már megtekintheti az összes tevékenységet az elmúlt 60 perc. 
    
1. Az időtartomány megjelenítéséből **x**.

1. Válassza ki **riasztások**, majd válassza ki **metrikariasztás hozzáadása**. 

1. Adjon meg egy nevet a riasztáshoz.

1. Az a **metrika** legördülő listában, vizsgálja meg a különböző riasztási metrikákat.  
    Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik. Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Válassza ki a **értesítendő e-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak keresztül** jelölőnégyzetet.  
    Szükség esetén további műveleteket is végrehajthat, ha egy riasztás jelenik meg úgy, hogy végrehajtja az Azure logic app.

1. Válassza ki **OK** a riasztás létrehozásához.  
    Néhány pillanat múlva a riasztás az irányítópulton aktívként jelenik meg. 

1. Kilépés a **riasztások** területet, és térjen vissza a **Application Insights** ablaktáblán.

1. Válassza ki **rendelkezésre állási**, majd válassza ki **Hozzáadás teszt**. 

1. Adja meg a teszt nevét, és válassza ki **létrehozás**.  
    Létrejön egy egyszerű ping-teszt az alkalmazás rendelkezésre állásának ellenőrzéséhez. Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a halmoz fel díjak szerint az erőforrások törlése. Ha már nincs szüksége, törölheti az Azure virtuális gép és a kapcsolódó erőforrásokat, ebben az oktatóanyagban létrehozott. Ehhez használja a **törlése** funkciókat a DevOps-projekt irányítópultján. 

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrást. A *törlése* funkció tárolt a DevOps Projects, Azure-beli és az Azure DevOps a projekt által létrehozott adatok megsemmisülnek, és nem lehet beolvasni. Ezzel az eljárással csak azután alaposan elolvasta a megjelenő utasításokat.

1. Az Azure Portalon nyissa meg a DevOps Projects-irányítópultot.
1. Jobb felső sarokban, válassza ki a **törlése**. 
1. Amikor a rendszer kéri, válassza ki a **Igen** való *véglegesen törli* az erőforrásokat.

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az ASP.NET-alkalmazás üzembe helyezése a DevOps Projects segítségével
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

A CI/CD-folyamat kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos készregyártás (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
