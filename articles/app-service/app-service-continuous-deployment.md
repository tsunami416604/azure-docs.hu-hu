---
title: "Folyamatos üzembe helyezés az Azure App Service |} Microsoft Docs"
description: "A cikkből többet tudhat meg arról, hogyan engedélyezheti az Azure App Service szolgáltatásba való folyamatos üzembe helyezést."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: dariagrigoriu
ms.openlocfilehash: ef5924607868bcb3dc35e279539b78d5a0e17c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-to-azure-app-service"></a>Az Azure App Service szolgáltatásba való folyamatos üzembe helyezés
Ez az oktatóanyag bemutatja, hogyan konfigurálása a folyamatos üzembe helyezés munkafolyamatonként a [Azure Web Apps](app-service-web-overview.md). App Service integrációja bitbucket szolgáltatásokhoz, a github webhelyen, és [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) lehetővé teszi, hogy hol Azure kéri le a legújabb frissítéseket a projektből a folyamatos üzembe helyezés munkafolyamat közzéteszi az ilyen szolgáltatást. A folyamatos üzembe helyezés jó megoldás lehet olyan projektek esetén, amelyeknél többszöri és gyakori közreműködői változtatást kell integrálni.

Megtudhatja, hogyan konfigurálhatja manuálisan egy felhőalapú-tárházat az Azure portál által fel nem sorolt a folyamatos üzembe helyezés (például [GitLab](https://gitlab.com/)), lásd: [manuális lépéseket követve folyamatos üzembe helyezés beállítása](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="overview"></a>Folyamatos üzembe helyezés engedélyezése
Folyamatos üzembe helyezés engedélyezése:

1. Tegye közzé az alkalmazástartalmakat azon az adattáron, amelyiket a folyamatos üzembe helyezéshez fog használni.  
    A projekt a szolgáltatásokon történő közzétételével kapcsolatos további információért olvassa el az [Adattár létrehozása (GitHub)], [Adattár létrehozása (BitBucket)], illetve a [Bevezetés a VSTS használatába] című cikket.
2. Az [Azure Portal] az alkalmazás menüpaneljén kattintson az **ALKALMAZÁSTELEPÍTÉS > Telepítési lehetőségek** elemre. Kattintson a **Forrás kiválasztása** elemre, majd válassza ki a központi telepítés forrását.  
   
    ![](./media/app-service-continuous-deployment/cd_options.png)
   
   > [!NOTE]
   > Tekintse meg ezt az [oktatóanyagot](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App) az App Service-környezethez szükséges VSTS-fiók beállításához.
   > 
   > 
3. Fejezze be az engedélyezési munkafolyamatot.
4. A **Központi telepítés forrása** panelen válassza ki azt a projektet és ágat, ahonnan a telepítést el szeretné végezni. Ha végzett, kattintson az **OK** gombra.
   
    ![](./media/app-service-continuous-deployment/github_option.png)
   
   > [!NOTE]
   > Ha engedélyezve van a folyamatos üzembe helyezés a GitHubbal vagy a BitBuckettel, mind a nyilvános, mind a magánjellegű projektek megjelennek.
   > 
   > 
   
    Az App Service létrehozza a kiválasztott adattárral a társítást, lekéri a megadott ágból a fájlokat, és fenntartja az adattár egy klónját az App Service-alkalmazás számára. Miután az Azure Portal alatt konfigurálta a VSTS folyamatos üzembe helyezését, az integráció az App Service [Kudu telepítési motorját](https://github.com/projectkudu/kudu/wiki) fogja használni, amely automatizálja a buildelési és üzembe helyezési feladatokat minden `git push` esetén. Nem szükséges külön beállítani a VSTS-ben a folyamatos üzembe helyezést. Miután a folyamat befejeződött, a **Telepítési lehetőségek**alkalmazáspanelje egy olyan aktív környezetet jelenít meg, amely azt jelzi, hogy az üzembe helyezés sikeres volt.
5. Az alkalmazás sikeres üzembe helyezésének ellenőrzéséhez kattintson az **URL-címre** az Azure Portalon, az alkalmazás paneljének a tetején.
6. Ha szeretné ellenőrizni, hogy a folyamatos üzembe helyezés a kiválasztott adattárból történik-e, küldjön egy módosítást az adattárba. Miután befejeződik a módosítás, az alkalmazásnak frissülnie kell, hogy tükrözze a módosításokat. Az alkalmazás **Telepítési lehetőségek** paneljén ellenőrizheti, hogy sikerült-e lekérni a frissítést.

## <a name="VSsolution"></a>Egy Visual Studio-megoldás folyamatos üzembe helyezése
Egy Visual Studio-megoldás Azure App Service-be való küldése ugyanolyan könnyű, mint lekérni egy egyszerű index.html fájlt. Az App Service üzembe helyezési folyamata leegyszerűsíti az összes részletet, beleértve a NuGet-függőségek visszaállítását és az alkalmazás bináris fájljainak létrehozását. A kódfenntartás forrásvezérlési ajánlott eljárásait elegendő csak a saját Git-adattárában követni, a többiről való gondoskodik az App Service-környezet.

A Visual Studio-megoldás az App Service-be való beküldésének a lépései megegyeznek az [előző szakaszban](#overview) találhatókkal, feltéve, hogy az alábbiak szerint konfigurálta a megoldást és az adattárat:

* Használja a Visual Studio forrásvezérlési lehetőségét, hogy létrehozzon egy, az alábbi képhez hasonló `.gitignore` fájlt, vagy manuálisan adjon hozzá egy `.gitignore` fájlt az adattár gyökérkönyvtárához a [.gitignore sample](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore) példához hasonló tartalommal.
  
  ![](./media/app-service-continuous-deployment/VS_source_control.png)
* Adja hozzá az egész megoldás könyvtárfáját az adattárhoz az adattár gyökérkönyvtárában található .sln fájllal együtt.

Miután a leírtak szerint beállította az adattárat, és konfigurálta az Azure-ban az alkalmazást az egyik online Git-adattárból való folyamatos közzétételre, az ASP.NET-alkalmazását helyileg fejlesztheti a Visual Studióban, és folyamatosan üzembe helyezheti a kódját úgy, hogy egyszerűen módosításokat küld az online Git-adattárba.

## <a name="disableCD"></a>Folyamatos üzembe helyezés letiltása
Folyamatos üzembe helyezés letiltása:

1. Az [Azure Portal] az alkalmazás menüpaneljén kattintson az **ALKALMAZÁSTELEPÍTÉS > Telepítési lehetőségek** elemre. Majd kattintson a **Leválasztás** elemre a **Telepítési lehetőségek** panelen.
   
    ![](./media/app-service-continuous-deployment/cd_disconnect.png)
2. Miután a megerősítést kérő üzenetre **Igen** választ adott, visszatérhet az alkalmazás paneljéhez, majd kattintson az **ALKALMAZÁSTELEPÍTÉS > Telepítési lehetőségek** elemre, ha egy további forrásból szeretné beállítani a közzétételt.

## <a name="additional-resources"></a>További források
* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [A PowerShell használata az Azure-ban]
* [A Macre és Linuxra készült Azure parancssori eszközök használata]
* [A Git dokumentációja]
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)
* [Használja az Azure automatikusan létrehozni az ASP.NET 4 alkalmazás telepítése CI/CD folyamat](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

[Azure Portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[A PowerShell használata az Azure-ban]: /powershell/azureps-cmdlets-docs
[A Macre és Linuxra készült Azure parancssori eszközök használata]:../cli-install-nodejs.md
[A Git dokumentációja]: http://git-scm.com/documentation

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Bevezetés a VSTS használatába]: https://www.visualstudio.com/docs/vsts-tfs-overview
