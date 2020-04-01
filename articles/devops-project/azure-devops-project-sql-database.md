---
title: 'Oktatóanyag: Telepítse a ASP.NET alkalmazást és az Azure SQL Database-kódot az Azure DevOps-projektek használatával'
description: A DevOps-projektek megkönnyítik az Azure-ral való ismerkedést. A DevOps-projektek segítségével néhány gyors lépésben üzembe helyezheti ASP.NET alkalmazását és az Azure SQL Database-kódot.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971504"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Oktatóanyag: Telepítse a ASP.NET alkalmazást és az Azure SQL Database-kódot az Azure DevOps-projektek használatával

Az Azure DevOps-projektek egyszerűsített élményt nyújt, ahol a meglévő kódot és a Git-tárház, vagy válasszon egy mintaalkalmazás létrehozása folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat az Azure-ba. 

DevOps-projektek is:
* Automatikusan létrehozza az Azure-erőforrásokat, például egy Azure SQL-adatbázist.
* Létrehoz és konfigurál egy kiadási folyamatot az Azure Pipelines, amely tartalmazza a ci buildelési folyamat.
* Kiadási folyamat beállítása a CD-hez. 
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure DevOps-projektek használata a ASP.NET alkalmazás és az Azure SQL Database-kód üzembe helyezéséhez
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba
> * Csatlakozás az Azure SQL-adatbázishoz 
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Projekt létrehozása a DevOps-projektekben egy ASP.NET alkalmazáshoz és egy Azure SQL-adatbázishoz

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrások, például egy Azure SQL-adatbázis, az Ön által kiválasztott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be a **DevOps-projektek**kifejezést, majd válassza a **Létrehozás lehetőséget.**

    ![A DevOps-projektek irányítópultja](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza **a .NET**, majd a **Tovább**lehetőséget.

1. Az **Alkalmazáskeret kiválasztása**csoportban válassza **a ASP.NET**lehetőséget.

1. Válassza **az Adatbázis hozzáadása**lehetőséget, majd a **Tovább**gombot.  
    Az alkalmazáskeretrendszer, amelyet egy előző lépésben választott, meghatározza az Itt elérhető Azure-szolgáltatásüzembe helyezési cél típusát. 
    
1. Válassza a **Tovább lehetőséget.**

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetési szolgáltatásokat.  
    Ha további Azure-konfigurációs beállításokat szeretne megtekinteni, és azonosítani szeretné a felhasználónevet az **Adatbázis-kiszolgáló bejelentkezési adatai** szakaszában, válassza a **Módosítás**lehetőséget. Tárolja el a felhasználónevet az oktatóanyag további lépéseihez. Ha ezt a nem kötelező lépést hajtja végre, lépjen ki az Azure konfigurációs területről, mielőtt a **Kész**lehetőséget választaná.
 
1. Válassza a **Done** (Kész) lehetőséget.  
    Néhány perc múlva a folyamat befejeződik, és a DevOps-projektek irányítópultja megnyílik az Azure Portalon. Az irányítópultra közvetlenül az Azure **Portalösszes erőforrásából** is navigálhat. A jobb oldalon válassza a **Tallózás** gombot a futó alkalmazás megtekintéséhez.
    
## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps-projektek automatikusan konfigurálja a teljes CI/CD-folyamat az Azure Repos. Megvizsgálhatja és testre szabhatja a folyamatot. Az Azure DevOps buildelési folyamattal való ismerkedéshez tegye a következőket:

1. A DevOps-projektek irányítópultjának tetején válassza a **Folyamatok összeállítása**lehetőséget.  
    A böngészőlapon az új projekt buildelési folyamata látható.

1. Mutasson az **Állapot** mezőre, majd jelölje ki a három pontot (...).  
    A menü számos lehetőséget jelenít meg, például egy új build várólistára kerülését, a build szüneteltetését és a buildfolyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait.  
    A build különböző feladatokat hajt végre, például a Git-tárházból származó források lekérése, a függőségek visszaállítása és a központi telepítésekhez használt kimenetek közzététele.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **a Mentés & várólistát,** majd a **Mentés lehetőséget.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ez az ablaktábla a build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure Pipelines nyomon követi a buildelőfolyamat minden változását, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  
    DevOps-projektek automatikusan létrehoz egy CI-eseményindítót, és minden véglegesítést a tárház hoz egy új buildet. Szükség esetén választhatja az ágak felvétele vagy kizárása a CI folyamatból.

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

1. A **Legördülő** ikon jobb oldalán válassza a **Folyamatos üzembe helyezés eseményindító jat**.  
    Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal végrehajtja a központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek. 

    DevOps-projektek beállít egy véletlenszerű SQL-jelszót, és használja azt a kiadási folyamat.
    
1. A bal oldalon válassza a **Változók**lehetőséget. 

   > [!NOTE]
   > Csak akkor hajtsa végre a következő lépést, ha módosította az SQL Server jelszavát. Egyetlen jelszóváltozó van.
  
1. Az **Érték** mező mellett jelölje ki a lakat ikont, írja be az új jelszót, majd kattintson a **Mentés gombra.**

1. A bal oldalon válassza a **Feladatok**lehetőséget, majd válassza ki a környezetet.  
    A feladatok azok a tevékenységek, amelyeket a központi telepítési folyamat végrehajt, és fázisokba vannak csoportosítva. Ez a kiadási folyamat egyetlen fázissal rendelkezik, amely tartalmazza az *Azure App Service telepítése* és az Azure SQL Database *Deployment* feladat.

1. Válassza ki az *Azure SQL-feladat végrehajtása,* és vizsgálja meg a különböző tulajdonságokat, amelyek az SQL-központi telepítéshez használt.  
    A **Központi telepítési csomag**csoportban a feladat SQL *DACPAC* fájlt használ.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.

1. Jelölje ki a három pontot (...) egy kiadás mellett, majd kattintson a **Megnyitás gombra.**  
     Több menüt is megvizsgálhat, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**.  
     Ebben a nézetben a központi telepítéshez társított kódvéglegesítések láthatók. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza **a Naplók lehetőséget.**  
     A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba 

 > [!NOTE]
 > Az alábbi eljárás a CI/CD-folyamatot egyszerű szövegmódosítással teszteli. Az SQL központi telepítési folyamatának teszteléséhez szükség esetén sql server sémát módosíthat a táblában.

Most már készen áll arra, hogy együttműködjön egy csapattal az alkalmazásában egy CI/CD folyamat használatával, amely automatikusan telepíti a legújabb munkáját a webhelyére. A Git-tárházban minden módosítás elindítja a buildet az Azure DevOps-ban, és egy CD-folyamat végrehajtja az Azure-ba való üzembe helyezést. Kövesse az ebben a szakaszban található eljárást, vagy használjon más technikát a tárház módosítási véglegesítéséhez. A kódmódosítások elindítják a CI/CD folyamatot, és automatikusan telepítik a módosításokat az Azure-ba.

1. A bal oldali ablaktáblában válassza a **Kód**lehetőséget, majd nyissa meg a tárházat.

1. Nyissa meg a *SampleWebApplication\Views\Home* könyvtárat, jelölje ki az *Index.cshtml* fájl melletti három pontot (...), és válassza a **Szerkesztés**lehetőséget. 

1. Módosítsa a fájlt, például adjon hozzá szöveget az egyik div címkén. 

1. A jobb felső sarokban válassza a **Véglegesítés**lehetőséget, majd a **Véglegesítés** lehetőséget a módosítás lenyomásához.  
    Néhány pillanat múlva egy build elindul az Azure DevOps-ban, és egy kiadás fut a módosítások üzembe helyezéséhez. Figyelje a buildállapotát a DevOps-projektek irányítópulton vagy a böngészőben az Azure DevOps-szervezettel.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="connect-to-the-azure-sql-database"></a>Csatlakozás az Azure SQL-adatbázishoz

Az Azure SQL-adatbázishoz való csatlakozáshoz megfelelő engedélyekre van szükség.

1. A DevOps-projektek irányítópulton válassza az **SQL Database** lehetőséget az SQL-adatbázis felügyeleti lapjának megugrásához.
   
1. Válassza **a Kiszolgálótűzfal beállítása**lehetőséget, majd az Ügyfél IP **hozzáadása**lehetőséget. 

1. Kattintson a **Mentés** gombra.  
    Az ügyfél IP-címe most már hozzáfér az SQL Server Azure-erőforráshoz.

1. Lépjen vissza az **SQL Database** ablaktáblára. 

1. A jobb oldalon válassza ki a kiszolgáló nevét az **SQL Server**konfigurációs lapjára való navigáláshoz.

1. Válassza **a Jelszó alaphelyzetbe állítása**lehetőséget, adja meg az SQL Server rendszergazdai bejelentkezésének jelszavát, majd kattintson a **Mentés gombra.**  
    Ügyeljen arra, hogy tartsa ezt a jelszót használni később az oktatóanyag.

    Most már használhatja az ügyféleszközöket, például az SQL Server Management Studio vagy a Visual Studio az SQL Server hez és az Azure SQL-adatbázishoz való csatlakozáshoz. A kapcsolódáshoz használja a **Kiszolgáló neve** tulajdonságot.

    Ha nem módosította az adatbázis felhasználónevét, amikor először konfigurálta a projektet a DevOps-projektekben, a felhasználóneve az e-mail cím helyi része. Ha például az e-mail címe *johndoe\@microsoft.com,* a felhasználóneve *johndoe.*

   > [!NOTE]
   > Ha módosítja az SQL bejelentkezés jelszavát, módosítania kell a jelszót a kiadási folyamat változójában, a "Cd-folyamat vizsgálata" című szakaszban leírtak szerint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tesztelés alatt áll, az erőforrások megtisztításával elkerülheti a számlázási díjak felhalmozását. Ha már nincs rájuk szükség, törölheti az Azure SQL-adatbázis és a kapcsolódó erőforrásokat, amelyek et ebben az oktatóanyagban létrehozott. Ehhez használja a **Delete** funkciót a DevOps-projektek irányítópulton.

> [!IMPORTANT]
> A következő eljárás véglegesen törli az erőforrásokat. A *Törlés* funkció elpusztítja a projekt által létrehozott adatokat a DevOps-projektekben az Azure-ban és az Azure DevOps-ban, és nem fogja tudni letölteni azokat. Ezt az eljárást csak akkor használja, ha figyelmesen elolvasta az utasításokat.

1. Az Azure Portalon nyissa meg a DevOps-projektek irányítópultját.
2. A jobb felső sarokban válassza a **Törlés**gombot. 
3. A kérdésben válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez.*

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure DevOps-projektek használata a ASP.NET alkalmazás és az Azure SQL Database-kód üzembe helyezéséhez
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-repos-okban, és automatikusan telepítse őket az Azure-ba
> * Csatlakozás az Azure SQL-adatbázishoz 
> * Az erőforrások eltávolítása

A CI/CD-folyamatról a következő témakörben olvashat bővebben:

> [!div class="nextstepaction"]
> [A többlépcsős folyamatos üzembe helyezési (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
