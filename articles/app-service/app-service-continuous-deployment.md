---
title: Folyamatos üzembe helyezés az Azure App Service |} Microsoft Docs
description: A cikkből többet tudhat meg arról, hogyan engedélyezheti az Azure App Service szolgáltatásba való folyamatos üzembe helyezést.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: e587edeef1cfa080a81f523f63678a645b514c57
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849491"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés az Azure App Service
Ez a cikk bemutatja, hogyan konfigurálhatja a folyamatos üzembe [Azure App Service](app-service-web-overview.md). App Service lehetővé teszi, hogy a folyamatos üzembe helyezést bitbucket szolgáltatásokhoz, a github webhelyen, és [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) modulba húzza a legújabb frissítéseket a meglévő tárházból valamelyik ezeket a szolgáltatásokat.

Megtudhatja, hogyan konfigurálhatja manuálisan egy felhőalapú-tárházat az Azure portál által fel nem sorolt a folyamatos üzembe helyezés (például [GitLab](https://gitlab.com/)), lásd: [manuális lépéseket követve folyamatos üzembe helyezés beállítása](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Tegye közzé az előkészített tárház támogatott szolgáltatások egyike. A projekt a szolgáltatásokon történő közzétételével kapcsolatos további információért olvassa el az [Adattár létrehozása (GitHub)], [Adattár létrehozása (BitBucket)], illetve a [Bevezetés a VSTS használatába] című cikket.

## <a name="deploy-continuously-from-github"></a>A Githubból folyamatosan központi telepítése

Ahhoz, hogy a Githubon folyamatos üzembe helyezés, az App Service alkalmazás oldalra léphet a [Azure-portálon](https://portal.azure.com).

A bal oldali menüben kattintson **Telepítőközpontot** > **GitHub** > **engedélyezés**. Az engedélyezési útmutatást követve. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Csak kell ahhoz, hogy a GitHub egyszer. Ha a felhasználó már jogosult, kattintson **Folytatás**. A meghatalmazott GitHub-fiók kattintva módosíthat **fiók módosítása**.

![](media/app-service-continuous-deployment/github-continue.png)

Az a **Build szolgáltató** lapon válassza ki azt a build szolgáltatót, majd kattintson > **Folytatás**.

### <a name="option-1-use-app-service-kudu-build-server"></a>1. lehetőség: az App Service Kudu build kiszolgáló használata

Az a **konfigurálása** lapon, válassza ki a szervezet tárház és fiókirodai folyamatosan telepíteni szeretné. Ha befejezte, kattintson a **Folytatás**.

### <a name="option-2-use-vsts-continuous-delivery"></a>2. lehetőség: VSTS folyamatos kézbesítési használata

> [!NOTE]
> Az App Service segítségével hozzon létre a szükséges összeállítása, és felszabadíthatja a definíciók VSTS-fiókjában, az Azure-fiókjába rendelkeznie kell a szerepe **tulajdonos** az Azure-előfizetésben.
>

Az a **konfigurálása** lap a **kód** területen válassza ki a szervezet tárház és fiókirodai folyamatosan telepíteni szeretné. Ha befejezte, kattintson a **Folytatás**.

Az a **konfigurálása** lap a **Build** szakaszban, egy új VSTS-fiókot, vagy adjon meg egy meglévő fiókot. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy meglévő VSTS-fiókkal, amely nem szerepel a használni kívánt, akkor [a VSTS-fiók összekötése az Azure-előfizetéshez](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Az a **teszt** lapon, válassza ki, hogy betöltés tesztek engedélyezése, majd kattintson az **Folytatás**.

Attól függően a [tarifacsomag](/pricing/details/app-service/plans/) az App Service-csomag, szintén megjelenik egy **az átmeneti telepítéséhez** lap. Válasszon hogy [üzembe helyezési engedélyezése](web-sites-staged-publishing.md), majd kattintson a **Folytatás**.

### <a name="finish-configuration"></a>Konfigurálás befejezése

Az a **összegzés** lapon ellenőrizze a beállításokat, majd kattintson **Befejezés**.

Konfiguráció befejezése után új véglegesíti a kijelölt tárházban az App Service alkalmazásba folyamatosan vannak telepítve.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Folyamatosan a bitbucket szolgáltatásokhoz központi telepítése

Ahhoz, hogy a Bitbucketből folyamatos üzembe helyezés, az App Service alkalmazás oldalra léphet a [Azure-portálon](https://portal.azure.com).

A bal oldali menüben kattintson **Telepítőközpontot** > **BitBucket** > **engedélyezés**. Az engedélyezési útmutatást követve. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Csak kell ahhoz, hogy BitBucket egyszer. Ha a felhasználó már jogosult, kattintson **Folytatás**. A meghatalmazott BitBucket fiók kattintva módosíthat **fiók módosítása**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

Az a **konfigurálása** lapon, válassza ki a tárházhoz és a fiókiroda, amelyből folyamatosan telepíteni kívánja. Ha befejezte, kattintson a **Folytatás**.

Az a **összegzés** lapon ellenőrizze a beállításokat, majd kattintson **Befejezés**.

Konfiguráció befejezése után új véglegesíti a kijelölt tárházban az App Service alkalmazásba folyamatosan vannak telepítve.

## <a name="deploy-continuously-from-vsts"></a>A VSTS folyamatosan központi telepítése

Ahhoz, hogy a VSTS folyamatos üzembe helyezés, az App Service alkalmazás oldalra léphet a [Azure-portálon](https://portal.azure.com).

A bal oldali menüben kattintson **Telepítőközpontot** > **VSTS** > **Folytatás**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Az a **Build szolgáltató** lapon válassza ki azt a build szolgáltatót, majd kattintson > **Folytatás**.

### <a name="option-1-use-app-service-kudu-build-server"></a>1. lehetőség: az App Service Kudu build kiszolgáló használata

Az a **konfigurálása** lapon, válassza ki a VSTS fiók, projekt, tárház és fiókirodai folyamatosan telepíteni szeretné. Ha befejezte, kattintson a **Folytatás**.

### <a name="option-2-use-vsts-continuous-delivery"></a>2. lehetőség: VSTS folyamatos kézbesítési használata

> [!NOTE]
> Az App Service segítségével hozzon létre a szükséges összeállítása, és felszabadíthatja a definíciók VSTS-fiókjában, az Azure-fiókjába rendelkeznie kell a szerepe **tulajdonos** az Azure-előfizetésben.
>

Az a **konfigurálása** lap a **kód** területen válassza ki a VSTS fiók, projekt, tárház és fiókirodai folyamatosan telepíteni szeretné. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy meglévő VSTS-fiókkal, amely nem szerepel a használni kívánt, akkor [a VSTS-fiók összekötése az Azure-előfizetéshez](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Az a **konfigurálása** lap a **Build** területén adja meg a VSTS a build feladatok a kiválasztott tárház futtatásához használandó nyelvi keretrendszert. Ha befejezte, kattintson a **Folytatás**.

Az a **teszt** lapon, válassza ki, hogy betöltés tesztek engedélyezése, majd kattintson az **Folytatás**.

Attól függően a [tarifacsomag](/pricing/details/app-service/plans/) az App Service-csomag, szintén megjelenik egy **az átmeneti telepítéséhez** lap. Válasszon hogy [üzembe helyezési engedélyezése](web-sites-staged-publishing.md), majd kattintson a **Folytatás**. 

### <a name="finish-configuration"></a>Konfigurálás befejezése

Az a **összegzés** lapon ellenőrizze a beállításokat, majd kattintson **Befejezés**.

Konfiguráció befejezése után új véglegesíti a kijelölt tárházban az App Service alkalmazásba folyamatosan vannak telepítve.

## <a name="disable-continuous-deployment"></a>Tiltsa le a folyamatos üzembe helyezés

Folyamatos üzembe helyezés letiltása, az App Service alkalmazás oldalra léphet a [Azure-portálon](https://portal.azure.com).

A bal oldali menüben kattintson **Telepítőközpontot** > **GitHub** vagy **VSTS** vagy **BitBucket**  >  **Leválasztása**.

![](media/app-service-continuous-deployment/disable.png)

## <a name="additional-resources"></a>További források

* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [A PowerShell használata az Azure-ban]
* [A Git dokumentációja]
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)
* [Használja az Azure automatikusan létrehozni az ASP.NET 4 alkalmazás telepítése CI/CD folyamat](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[A PowerShell használata az Azure-ban]: /powershell/azureps-cmdlets-docs
[A Git dokumentációja]: http://git-scm.com/documentation

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Bevezetés a VSTS használatába]: https://www.visualstudio.com/docs/vsts-tfs-overview
