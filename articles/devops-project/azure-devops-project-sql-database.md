---
title: 'Oktatóanyag: Az ASP.NET-alkalmazás és az Azure SQL Database-kód telepítése az Azure DevOps Projects használatával'
description: A DevOps Projects megkönnyíti az Azure használatának első lépéseit. A DevOps Projects üzembe helyezheti az ASP.NET-alkalmazás és az Azure SQL Database-kódot néhány gyors lépéssel.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 0d05a2f3de92791572f0a5e6313777b5388af3df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845222"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Oktatóanyag: Az ASP.NET-alkalmazás és az Azure SQL Database-kód telepítése az Azure DevOps Projects használatával

Az Azure DevOps Projects megjelenít egy egyszerűsített felület, ahol a meglévő kódot és a Git-adattár, vagy válasszon egy mintaalkalmazást, hozzon létre egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot az Azure-bA. 

DevOps-projektek is:
* Azure-erőforrások, például egy Azure SQL database automatikusan létrehozza.
* Hoz létre, és konfigurálja a kibocsátási folyamat, amely tartalmazza a folyamatos integrációhoz buildelési folyamat Azure folyamatokban.
* CD beállítja a kibocsátási folyamat. 
* Az Azure Application Insights-erőforrás, figyelés hoz létre.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure DevOps Projects használata az ASP.NET-alkalmazás és az Azure SQL Database-kód
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA
> * Csatlakozás az Azure SQL database 
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Hozzon létre egy projektet egy ASP.NET-alkalmazás és a egy Azure SQL database fejlesztési és üzemeltetési projektek

A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects is létrehoz az Azure-erőforrások, például egy Azure SQL database tetszőleges Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**.

1. A Keresés mezőbe írja be a **DevOps Projects**, majd válassza ki **létrehozás**.

    ![A DevOps Projects-irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza ki **.NET**, majd válassza ki **tovább**.

1. A **válasszon alkalmazási keretrendszert**válassza **ASP.NET**.

1. Válassza ki **adatbázis hozzáadása**, majd válassza ki **tovább**.  
    Az alkalmazási keretrendszer, amely az előző lépésben kiválasztott szabja meg, amely innen érhető el az Azure szolgáltatás üzembe helyezési cél típusát. 
    
1. Kattintson a **Tovább** gombra.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetés szolgáltatásait.  
    Igény szerint további Azure-konfigurációs beállítások megtekintése, és azonosíthatja a szereplő felhasználónév a **adatbázis-kiszolgálói bejelentkezés adatai** szakaszban kiválaszthatja **módosítás**. A későbbi lépésekben tartozó felhasználónév Store ebben az oktatóanyagban. Ha ez a lépés nem kötelező végrehajtani, lépjen ki az Azure konfigurációs terület kiválasztása előtt **kész**.
 
1. Válassza a **Done** (Kész) lehetőséget.  
    Néhány perc múlva a folyamat befejezése és a DevOps Projects-irányítópult megnyitása az Azure Portalon. Közvetlenül az irányítópulton is elérheti **összes erőforrás** az Azure Portalon. Válassza ki a jobb **Tallózás** a futó alkalmazás megtekintéséhez.
    
## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A DevOps Projects egy teljes CI/CD-folyamat automatikusan konfigurálja az Azure-Adattárakkal. Megvizsgálhatja és testre szabhatja a folyamatot. Ismerje meg az Azure DevOps-build folyamattal, tegye a következőket:

1. Válassza ki a DevOps Projects irányítópult tetején lévő **folyamatok alakíthatók ki**.  
    Egy böngészőben lap megjeleníti a buildelési folyamat az új projekt.

1. Mutasson a **állapot** mezőben, majd válassza ki a három pontra (...).  
    Menü megjelenítése több beállítások, például az üzenetsor új build, készítsen felfüggesztetéssel és a buildelési folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat, hajt végre, például beolvasásakor a Git-tárházban, a függőségek visszaállítását és közzétételi kimenete-forrásokat az központi telepítésére használatos.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ezen a panelen a legutóbbi módosítások build auditnaplót jeleníti meg. Az Azure folyamatok nyomon követi a végzett módosítások a buildelési folyamat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
    DevOps-projektek automatikus CI eseményindítót hoz létre, és a tárházban minden véglegesítéshez elindul egy új létrehozást. Igény szerint kiválaszthatja, hogy belefoglalja vagy kizárja az ágak a CI folyamat.

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

1. Jobb oldalán a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**.  
    A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely végrehajtja a központi telepítés minden alkalommal, amikor egy új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

    A DevOps Projects állít be egy véletlenszerű SQL-jelszó, és a kibocsátási folyamat használja.
    
1. Válassza a bal oldali **változók**. 

   > [!NOTE]
   > Csak akkor, ha az SQL Server jelszót módosította, hajtsa végre a következő lépéssel. Nincs egyetlen jelszó változót.
  
1. Mellett a **érték** mezőbe, kattintson a lakat ikonra, adja meg az új jelszót, és válassza **mentése**.

1. Válassza a bal oldali **feladatok**, majd válassza ki a környezetben.  
    Feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat, és fázisban vannak csoportosítva. A kibocsátási folyamat rendelkezik egy fázisban, amely tartalmaz egy *Azure App Service üzembe helyezése* és *Azure SQL Database-telepítés* feladat.

1. Válassza ki a *Azure SQL végrehajtása* feladat, és vizsgálja meg az SQL-telepítéshez használt különböző tulajdonságait.  
    A **központi telepítési csomag**, a feladat használja egy *SQL adatrétegbeli ALKALMAZÁSCSOMAGOT* fájlt.

1. Válassza ki a jobb **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Egy kiadási melletti három pontra (...), majd válassza ki és **nyílt**.  
     Áttekintheti a több menük, például a kiadás összegzését, a társított munkaelemekhez és a teszteket.

1. Válassza a **Véglegesítéseket**.  
     Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  
     A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA 

 > [!NOTE]
 > Az alábbi eljárás teszteli a CI/CD-folyamat, és egyszerű szöveges módosul. Az SQL-üzembe helyezési folyamat teszteléséhez, igény szerint módosítsa a tábla egy SQL Server-sémát is felvehető.

Most már készen áll, az alkalmazás a csapat együttműködhet egy CI/CD-folyamat, amely automatikusan telepíti a legújabb munkáját a webhely használatával. Minden módosítás a Git-tárház egy build elindítja az Azure DevOps, és a egy CD-folyamat végrehajtja a központi telepítés az Azure-bA. Kövesse az ebben a szakaszban az eljárást, vagy egy másik módszer használatával véglegesítse a módosításokat a tárházba. A kód módosításait a CI/CD-folyamat kezdeményezése, és automatikusan telepítheti őket a módosításokat az Azure-bA.

1. A bal oldali panelen válassza ki a **kód**, és folytassa a tárházhoz.

1. Nyissa meg a *SampleWebApplication\Views\Home* könyvtár, válassza a három pontra (...), a *Index.cshtml* fájlt, és válassza ki **szerkesztése**. 

1. Módosítja a fájlt, például a valamilyen szöveget a div címkék egyikében. 

1. Jobb felső sarokban, válassza ki a **véglegesítése**, majd válassza ki **véglegesítése** újra, hogy a módosítás leküldése.  
    Néhány pillanat múlva build elindítja az Azure DevOps, és egy kiadási üzembe helyezéséhez a módosításokat hajt végre. A DevOps Projects információközpontban vagy a böngészőben az Azure DevOps vállalattal build állapotának figyelésére.

1. A kiadás befejezése után frissítse az alkalmazás ellenőrizheti a módosításokat.

## <a name="connect-to-the-azure-sql-database"></a>Csatlakozás az Azure SQL database

Az Azure SQL adatbázishoz való kapcsolódáshoz megfelelő engedélyek szükségesek.

1. A DevOps Projects irányítópultján válassza ki a **SQL Database** , nyissa meg az SQL database felügyeleti oldalára.
   
1. Válassza ki **kiszolgálótűzfal beállítása**, majd válassza ki **ügyfél IP-cím hozzáadása**. 

1. Kattintson a **Mentés** gombra.  
    Az ügyfél IP-Címét most már rendelkezik az SQL Server Azure-erőforrás elérésére.

1. Lépjen vissza a **SQL Database** ablaktáblán. 

1. Jobb válassza ki a kiszolgáló nevét, nyissa meg a konfigurációs oldalára **SQL Server**.

1. Válassza ki **jelszó alaphelyzetbe állítása**, az SQL Server-rendszergazdai bejelentkezési jelszót adjon meg, és válassza **mentése**.  
    Mindenképp ezt a jelszót használja az oktatóanyag későbbi részében.

    Most már igény szerint használhat ügyféleszközökkel például az SQL Server Management Studio vagy a Visual Studio csatlakozni az SQL Server és az Azure SQL database. A kapcsolódáshoz használja a **Kiszolgáló neve** tulajdonságot.

    A DevOps Projects a projekt első konfigurációja nem módosította az adatbázis felhasználónevének, ha a felhasználónév része a helyi az e-mail-címét. Például, ha az e-mail-cím *johndoe\@microsoft.com*, a felhasználónév *johndoe*.

   > [!NOTE]
   > Ha az SQL-bejelentkezési jelszó megváltoztatása, módosítania kell a jelszót a kibocsátási folyamat változót, a "Vizsgálja meg a CD-folyamat" szakaszban leírtak szerint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a halmoz fel díjak szerint az erőforrások törlése. Ha már nincs szüksége, törölheti az Azure SQL database és a kapcsolódó erőforrásokat, ebben az oktatóanyagban létrehozott. Ehhez használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrást. A *törlése* funkció tárolt a DevOps Projects, Azure-beli és az Azure DevOps a projekt által létrehozott adatok megsemmisülnek, és nem lehet beolvasni. Ezzel az eljárással csak azután alaposan elolvasta a megjelenő utasításokat.

1. Az Azure Portalon nyissa meg a DevOps Projects-irányítópultot.
2. Jobb felső sarokban, válassza ki a **törlése**. 
3. Amikor a rendszer kéri, válassza ki a **Igen** való *véglegesen törli* az erőforrásokat.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure DevOps Projects használata az ASP.NET-alkalmazás és az Azure SQL Database-kód
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Véglegesítse a módosításokat az Azure-Adattárakkal, és automatikusan telepíthet az Azure-bA
> * Csatlakozás az Azure SQL database 
> * Az erőforrások eltávolítása

A CI/CD-folyamat kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos készregyártás (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
