---
title: Az Azure App Service folyamatos üzembe helyezés |} A Microsoft Docs
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
ms.openlocfilehash: 4d3f1c66c6403720bf02c80af1d6833dc3cee3f1
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055521"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés az Azure App Service-ben
Ez a cikk bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezés [Azure App Service](app-service-web-overview.md). Az App Service lehetővé teszi, hogy a BitBucket, GitHub, a folyamatos üzembe helyezés és [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) kiindulásként a a legújabb frissítéseket a meglévő adattárból az egyik szolgáltatásba.

Megtudhatja, hogyan konfigurálhatja manuálisan a felhőalapú adattárban nem jeleníti meg az Azure portal a folyamatos üzembe helyezés (például [GitLab](https://gitlab.com/)), lásd: [manuális lépések használatával folyamatos üzembe helyezés beállítása](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Tegye közzé a készített tárház egyik támogatott szolgáltatás. A projekt a szolgáltatásokon történő közzétételével kapcsolatos további információért olvassa el az [Adattár létrehozása (GitHub)], [Adattár létrehozása (BitBucket)], illetve a [Bevezetés a VSTS használatába] című cikket.

## <a name="deploy-continuously-from-github"></a>A Githubról történő folyamatos üzembe helyezés

Az App Service-oldalra lépjen ahhoz, hogy a Githubbal való folyamatos üzembe helyezés, a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **GitHub** > **engedélyezés**. Kövesse az engedélyezési utasításokat. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Csak egyszer engedélyezése a GitHub kell. Ha a felhasználó már jogosult, egyszerűen kattintson **Folytatás**. A hitelesített GitHub-fiókhoz kattintva módosíthat **fiók módosítása**.

![](media/app-service-continuous-deployment/github-continue.png)

Az a **Build szolgáltató** lapon válassza ki a build-szolgáltatót, és kattintson > **Folytatás**.

### <a name="option-1-use-app-service-kudu-build-server"></a>1. lehetőség: használata App Service Kudu-buildelési kiszolgáló

Az a **konfigurálása** lapra, jelölje be a szervezet, tárházat, és az ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

### <a name="option-2-use-vsts-continuous-delivery"></a>2. lehetőség: a VSTS folyamatos készregyártás használata

> [!NOTE]
> Az App Service létrehozná az szükséges, és a kiadási definícióiról a VSTS-fiókban, az Azure-fiókkal kell rendelkeznie szerepe **tulajdonos** az Azure-előfizetésében.
>

Az a **konfigurálása** lap a **kód** területen válassza ki a szervezet, tárházat, és az ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

Az a **konfigurálása** lap a **összeállítása** szakaszt, új VSTS-fiók beállításához, vagy egy meglévő fiókot. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy meglévő VSTS-fiók, amely nem szerepel a használni kívánt, kell [a VSTS-fiók összekapcsolása az Azure-előfizetés](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Az a **teszt** lapon, döntse el, hogy engedélyezze a terhelési teszteket, majd kattintson a **Folytatás**.

Attól függően, a [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/plans/) az App Service-csomag is megjelenhetnek egy **üzembe helyezés átmeneti környezetbe** lapot. Válassza ki, hogy e [üzembe helyezési pontok engedélyezése](web-sites-staged-publishing.md), majd kattintson **Folytatás**.

### <a name="finish-configuration"></a>Konfiguráció befejezéséhez

Az a **összefoglalás** oldalon ellenőrizze a beállításokat, kattintson a **Befejezés**.

Konfiguráció befejezését követően a kijelölt adattárban új véglegesítéseket az App Service alkalmazásba folyamatosan vannak telepítve.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>A BitBucket folyamatos üzembe helyezés

Az App Service-oldalra lépjen ahhoz, hogy a folyamatos üzembe helyezés a BitBucket, a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **BitBucket** > **engedélyezés**. Kövesse az engedélyezési utasításokat. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Csak egyszer a BitBucket-alapú engedélyezésére kell. Ha a felhasználó már jogosult, egyszerűen kattintson **Folytatás**. Módosíthatja a BitBucket engedéllyel rendelkező fiók kattintva **fiók módosítása**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

Az a **konfigurálása** lapra, jelölje be a tárházat és az ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

Az a **összefoglalás** oldalon ellenőrizze a beállításokat, kattintson a **Befejezés**.

Konfiguráció befejezését követően a kijelölt adattárban új véglegesítéseket az App Service alkalmazásba folyamatosan vannak telepítve.

## <a name="deploy-continuously-from-vsts"></a>Folyamatos üzembe helyezés VSTS-ből

Az App Service-oldalra lépjen ahhoz, hogy a folyamatos üzembe helyezés a VSTS, a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **VSTS** > **Folytatás**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Az a **Build szolgáltató** lapon válassza ki a build-szolgáltatót, és kattintson > **Folytatás**.

### <a name="option-1-use-app-service-kudu-build-server"></a>1. lehetőség: használata App Service Kudu-buildelési kiszolgáló

Az a **konfigurálása** lapon, válassza ki a VSTS fiók, projekt, tárház és ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

### <a name="option-2-use-vsts-continuous-delivery"></a>2. lehetőség: a VSTS folyamatos készregyártás használata

> [!NOTE]
> Az App Service létrehozná az szükséges, és a kiadási definícióiról a VSTS-fiókban, az Azure-fiókkal kell rendelkeznie szerepe **tulajdonos** az Azure-előfizetésében.
>

Az a **konfigurálása** lap a **kód** területen válassza ki a VSTS fiók, projekt, tárház és ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy meglévő VSTS-fiók, amely nem szerepel a használni kívánt, kell [a VSTS-fiók összekapcsolása az Azure-előfizetés](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Az a **konfigurálása** lap a **összeállítása** területén adja meg a nyelvi keretrendszerébe, amely vsts-ben a kijelölt tárház az összeállítási feladatok futtatásához csatlakoznia kell. Ha befejezte, kattintson a **Folytatás**.

Az a **teszt** lapon, döntse el, hogy engedélyezze a terhelési teszteket, majd kattintson a **Folytatás**.

Attól függően, a [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/plans/) az App Service-csomag is megjelenhetnek egy **üzembe helyezés átmeneti környezetbe** lapot. Válassza ki, hogy e [üzembe helyezési pontok engedélyezése](web-sites-staged-publishing.md), majd kattintson **Folytatás**. 

### <a name="finish-configuration"></a>Konfiguráció befejezéséhez

Az a **összefoglalás** oldalon ellenőrizze a beállításokat, kattintson a **Befejezés**.

Konfiguráció befejezését követően a kijelölt adattárban új véglegesítéseket az App Service alkalmazásba folyamatosan vannak telepítve.

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

Folyamatos üzembe helyezés letiltása, keresse meg az App Service-oldalra a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **GitHub** vagy **VSTS** vagy **BitBucket**  >  **Leválasztása**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>További források

* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [A PowerShell használata az Azure-ban]
* [A Git dokumentációja]
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)
* [Az Azure használatával automatikusan létrehozhat egy CI/CD-folyamat számára egy ASP.NET 4-alkalmazás üzembe helyezése](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[A PowerShell használata az Azure-ban]: /powershell/azureps-cmdlets-docs
[A Git dokumentációja]: http://git-scm.com/documentation

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Bevezetés a VSTS használatába]: https://www.visualstudio.com/docs/vsts-tfs-overview
