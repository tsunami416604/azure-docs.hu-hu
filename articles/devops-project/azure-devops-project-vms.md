---
title: 'Oktatóanyag: A ASP.NET alkalmazás üzembe helyezése az Azure virtuális gépeken az Azure DevOps-projektek használatával'
description: A DevOps-projektek segítségével első lépésként elindíthatja az Azure-t, és néhány gyors lépésben üzembe helyezheti ASP.NET alkalmazását az Azure virtuális gépeire.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b03fb27c46d4fd925b91b8927fdd50ff4912aaec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969562"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Oktatóanyag: A ASP.NET alkalmazás üzembe helyezése az Azure virtuális gépeken az Azure DevOps-projektek használatával

Az Azure DevOps-projektek egyszerűsített élményt nyújt, ahol a meglévő kódot és a Git-tárház, vagy válasszon egy mintaalkalmazás létrehozása folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat az Azure-ba. 

DevOps-projektek is:
* Automatikusan létrehozza az Azure-erőforrásokat, például egy új Azure virtuális gépet (VM).
* Létrehoz és konfigurál egy kiadási folyamatot az Azure DevOps-ban, amely tartalmazza a CI buildfolyamatát.
* Kiadási folyamat beállítása a CD-hez. 
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A DevOps-projektek használata a ASP.NET alkalmazás üzembe helyezéséhez
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>A DevOps-projektek használata a ASP.NET alkalmazás üzembe helyezéséhez

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrások, például a virtuális gépek az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali ablaktáblában válassza az **Új**lehetőséget.

1. A keresőmezőbe írja be a **DevOps-projektek**kifejezést, majd válassza a **Létrehozás lehetőséget.**

    ![A DevOps-projektek irányítópultja](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza **a .NET**, majd a **Tovább**lehetőséget.

1. Az **Alkalmazáskeretrendszer kiválasztása**csoportban válassza **a ASP.NET**lehetőséget, majd a **Tovább**lehetőséget.  
    Az alkalmazáskeretrendszer, amelyet egy előző lépésben választott, meghatározza az Itt elérhető Azure-szolgáltatásüzembe helyezési cél típusát. 

1. Jelölje ki a virtuális gépet, majd kattintson a **Tovább gombra.**

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetési szolgáltatásokat.  
    Szükség esetén **kiválaszthatja a Módosítás** lehetőséget, majd további konfigurációs részleteket adhat meg, például az Azure-erőforrások helyét.
 
1. Adjon meg egy virtuális gép nevet, felhasználónevet és jelszót az új Azure virtuálisgép-erőforráshoz, majd válassza a **Kész**gombot.  
    Néhány perc múlva az Azure virtuális gép készen áll. Egy minta ASP.NET alkalmazás van beállítva egy tárházban az Azure DevOps-szervezet, a build és a kiadás végrehajtása, és az alkalmazás telepítve van az újonnan létrehozott Azure virtuális gép. 

    Miután elkészült, a DevOps-projektek irányítópultja megjelenik az Azure Portalon. Az irányítópultra közvetlenül az Azure **Portalösszes erőforrásából** is navigálhat. 

    Az irányítópult betekintést nyújt az Azure DevOps-kódtárjába, a CI/CD-folyamatba és az Azure-ban futó alkalmazásba.   

    ![Irányítópult nézet](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps-projektek automatikusan konfigurálja a CI build és kiadás eseményindító, amely telepíti a kód módosításait a tárújbóli. További beállításokat is konfigurálhat az Azure DevOpsban. A futó alkalmazás megtekintéséhez válassza a **Tallózás gombot.**
    
## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata
 
DevOps-projektek automatikusan konfigurált egy CI/CD-folyamat az Azure-folyamatokban. Megvizsgálhatja és testre szabhatja a folyamatot. A buildfolyamat tal való ismerkedéshez tegye a következőket:

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

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

A DevOps-projektek automatikusan létrehozza és konfigurálja az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket. Ezek a lépések közé tartozik egy Azure-szolgáltatás-kapcsolat konfigurálása az Azure DevOps azure-előfizetésében való hitelesítéséhez. Az automatizálás egy CD-folyamatot is létrehoz, amely biztosítja a CD-t az Azure virtuális géphez. Ha többet szeretne megtudni az Azure DevOps CD-folyamatról, tegye a következőket:

1. Válassza **a Build and Release (Létrehozás és kiadás)** lehetőséget, majd a **Kiadások**lehetőséget.  
    A DevOps-projektek létrehozegy kiadási folyamatot az Azure-ba történő központi telepítések kezeléséhez.

1. Jelölje ki a kiadási folyamat melletti három pontot (...), majd kattintson a **Szerkesztés gombra.**  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépésekben megvizsgált buildfolyamat létrehozza a műtermékhez használt kimenetet. 

1. A **Legördülő** ikon mellett válassza a **Folyamatos üzembe helyezés idomáreseménye lehetőséget.**  
    Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal végrehajtja a központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek. 

1. A bal oldalon válassza a **Feladatok**lehetőséget, majd válassza ki a környezetet.  
    A feladatok azok a tevékenységek, amelyeket a központi telepítési folyamat végrehajt, és fázisokba vannak csoportosítva. Ez a kiadási folyamat két fázisban történik:
    * Az első fázis tartalmaz egy Azure Resource Group deployment feladatot, amely két dolgot tesz:
      * A virtuális gép központi telepítésének konfigurálása
      * Hozzáadja az új virtuális gép egy Azure DevOps-telepítési csoporthoz. Az Azure DevOps-ban lévő virtuálisgép-üzembehelyezési csoport kezeli az üzembehelyezési célgépek logikai csoportjait.
    * A második fázisban az IIS Web App Manage feladat létrehoz egy IIS-webhelyet a virtuális számítógépen. Egy második IIS Web App-üzembe helyezési feladat jön létre a hely telepítéséhez.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.

1. Jelölje ki a három pontot (...) egy kiadás mellett, majd kattintson a **Megnyitás gombra.**  
    Több menüt is megvizsgálhat, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ebben a nézetben a központi telepítéshez társított kódvéglegesítések láthatók. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza **a Naplók lehetőséget.**  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba 

Most már készen áll arra, hogy együttműködjön egy csapattal az alkalmazásában egy CI/CD folyamat használatával, amely automatikusan telepíti a legújabb munkáját a webhelyére. A Git-tárházban minden módosítás elindítja a buildet az Azure DevOps-ban, és egy CD-folyamat végrehajtja az Azure-ba való üzembe helyezést. Kövesse az ebben a szakaszban található eljárást, vagy használjon más technikát a tárműtér módosítási véglegesítéséhez. A kódmódosítások elindítják a CI/CD folyamatot, és automatikusan telepítik a módosításokat az IIS-webhelyen az Azure virtuális gépen.

1. A bal oldali ablaktáblában válassza a **Kód**lehetőséget, majd nyissa meg a tárpántot.

1. Nyissa meg a *Nézetek\Kezdőkönyvtárat,* jelölje ki az *Index.cshtml* fájl melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.

1. Módosítsa a fájlt, például adjon hozzá szöveget az egyik div címkén. 

1. A jobb felső sarokban válassza a **Véglegesítés**lehetőséget, majd a **Véglegesítés** lehetőséget a módosítás lenyomásához.  
    Néhány pillanat múlva egy build elindul az Azure DevOps-ban, és egy kiadás fut a módosítások üzembe helyezéséhez. Figyelje a buildállapotát a DevOps-projektek irányítópulton vagy a böngészőben az Azure DevOps-szervezettel.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát. DevOps-projektek automatikusan konfigurálja az Application Insights-erőforrást az alkalmazáshoz. Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. Az Azure Portalon nyissa meg a DevOps-projektek irányítópultját. 

1. A jobb alsó sarokban válassza ki az **alkalmazáselemzési** hivatkozást az alkalmazáshoz.  
    Megnyílik **az Application Insights** ablaktábla. Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

    ![Az Application Insights ablaktábla](_img/azure-devops-project-github/appinsights.png) 

1. Válassza **az Időtartomány**lehetőséget, majd az **Utolsó óra**lehetőséget. Az eredmények szűréséhez válassza a **Frissítés**lehetőséget.  
    Most már megtekintheti az összes tevékenységet az elmúlt 60 percből. 
    
1. Az időtartományból való kilépéshez válassza az **x**lehetőséget.

1. Válassza **a Riasztások**lehetőséget, majd a **Metrikariasztás hozzáadása**lehetőséget. 

1. Adja meg a riasztás nevét.

1. A **Metrika** legördülő listában vizsgálja meg a különböző riasztási mutatókat.  
    Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik. Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Jelölje be az **Értesítés e-mailben tulajdonosok, közreműködők és olvasók** jelölőnégyzetet.  
    Szükség esetén további műveleteket hajthat végre, ha egy riasztás jelenik meg egy Azure-logikai alkalmazás végrehajtásával.

1. A riasztás létrehozásához válassza az **OK gombot.**  
    Néhány pillanat múlva a riasztás aktívként jelenik meg az irányítópulton. 

1. Lépjen ki a **Riasztások** területről, és lépjen vissza az **Application Insights** ablaktáblára.

1. Válassza **az Elérhetőség**lehetőséget, majd a Teszt **hozzáadása**lehetőséget. 

1. Adjon meg egy tesztnevet, majd válassza **a Létrehozás gombot.**  
    Létrejön egy egyszerű ping-teszt az alkalmazás rendelkezésre állásának ellenőrzéséhez. Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tesztelés alatt áll, az erőforrások megtisztításával elkerülheti a számlázási díjak felhalmozását. Ha már nincs rájuk szükség, törölheti az Azure virtuális gép és a kapcsolódó erőforrásokat, amelyek et ebben az oktatóanyagban létrehozott. Ehhez használja a **Delete** funkciót a DevOps Project irányítópultján. 

> [!IMPORTANT]
> A következő eljárás véglegesen törli az erőforrásokat. A *Törlés* funkció elpusztítja a projekt által létrehozott adatokat a DevOps-projektekben az Azure-ban és az Azure DevOps-ban, és nem fogja tudni letölteni azokat. Ezt az eljárást csak akkor használja, ha figyelmesen elolvasta az utasításokat.

1. Az Azure Portalon nyissa meg a DevOps-projektek irányítópultját.
1. A jobb felső sarokban válassza a **Törlés**gombot. 
1. A kérdésben válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez.*

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps-projektek használata a ASP.NET alkalmazás üzembe helyezéséhez
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

A CI/CD-folyamatról a következő témakörben olvashat bővebben:

> [!div class="nextstepaction"]
> [A többlépcsős folyamatos üzembe helyezési (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
