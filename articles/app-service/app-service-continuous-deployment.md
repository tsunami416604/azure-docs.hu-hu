---
title: Folyamatos üzembe helyezés – az Azure App Service |} A Microsoft Docs
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
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 79f544a9cec24ef84ad9e64e80cabe45dbb6d1d6
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257292"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés az Azure App Service-ben
Ez a cikk bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezés [Azure App Service](app-service-web-overview.md). Az App Service lehetővé teszi, hogy a BitBucket, GitHub, a folyamatos üzembe helyezés és [Azure DevOps-szolgáltatásokkal](https://www.visualstudio.com/team-services/) kiindulásként a a legújabb frissítéseket a meglévő adattárból az egyik szolgáltatásba.

Megtudhatja, hogyan konfigurálhatja manuálisan a felhőalapú adattárban nem jeleníti meg az Azure portal a folyamatos üzembe helyezés (például [GitLab](https://gitlab.com/)), lásd: [manuális lépések használatával folyamatos üzembe helyezés beállítása](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Tegye közzé a készített tárház egyik támogatott szolgáltatás. Ezekhez a szolgáltatásokhoz a projekt közzétételével kapcsolatos további információkért lásd: [adattár létrehozása (GitHub)], [adattár létrehozása (BitBucket)], és [Ismerkedés az Azure DevOps-szolgáltatásokkal].

## <a name="deploy-continuously-from-github"></a>A Githubról történő folyamatos üzembe helyezés

Az App Service-oldalra lépjen ahhoz, hogy a Githubbal való folyamatos üzembe helyezés, a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **GitHub** > **engedélyezés**. Kövesse az engedélyezési utasításokat. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Csak egyszer engedélyezése a GitHub kell. Ha a felhasználó már jogosult, egyszerűen kattintson **Folytatás**. A hitelesített GitHub-fiókhoz kattintva módosíthat **fiók módosítása**.

![](media/app-service-continuous-deployment/github-continue.png)

Az a **Build szolgáltató** lapon válassza ki a build-szolgáltatót, és kattintson > **Folytatás**.

### <a name="option-1-use-app-service-kudu-build-server"></a>1. lehetőség: használata App Service Kudu-buildelési kiszolgáló

Az a **konfigurálása** lapra, jelölje be a szervezet, tárházat, és az ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

### <a name="option-2-use-azure-pipelines-preview"></a>2. lehetőség: az Azure-folyamatok (előzetes verzió) használata

> [!NOTE]
> Az App Service a szükséges Azure-folyamatok létrehozására a szervezet az Azure DevOps-szolgáltatásokkal, az Azure-fiókkal kell rendelkeznie szerepe **tulajdonos** az Azure-előfizetésében.
>

Az a **konfigurálása** lap a **kód** területen válassza ki a szervezet, tárházat, és az ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

Az a **konfigurálása** lap a **összeállítása** szakaszt, konfigurálja egy új Azure DevOps-szolgáltatásokkal szervezetet, vagy adjon meg egy már meglévő szervezet. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy már meglévő Azure DevOps-szolgáltatásokkal szervezet, amely nem szerepel a használni kívánt, kell [az Azure DevOps-szolgáltatásokkal szervezet összekapcsolása az Azure-előfizetés](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

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

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Folyamatos üzembe helyezés az Azure-kódtárak (fejlesztési és üzemeltetési szolgáltatások)

Folyamatos üzembe helyezés a származó engedélyezéséhez [Azure Adattárakkal](https://docs.microsoft.com/azure/devops/repos/index), keresse meg az App Service-oldalra a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **Azure Adattárakkal** > **Folytatás**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Az a **Build szolgáltató** lapon válassza ki a build-szolgáltatót, és kattintson > **Folytatás**.

> [!NOTE]
> Ha egy már meglévő Azure DevOps-szolgáltatásokkal szervezet, amely nem szerepel a használni kívánt, kell [az Azure DevOps-szolgáltatásokkal szervezet összekapcsolása az Azure-előfizetés](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### <a name="option-1-use-app-service-kudu-build-server"></a>1. lehetőség: használata App Service Kudu-buildelési kiszolgáló

Az a **konfigurálása** lapra, jelölje be a szervezet az Azure DevOps-szolgáltatásokkal, projekt, tárház és ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>2. lehetőség: használja a folyamatos készregyártás az Azure DevOps-szolgáltatásokkal

> [!NOTE]
> Az App Service a szükséges Azure-folyamatok létrehozására a szervezet az Azure DevOps-szolgáltatásokkal, az Azure-fiókkal kell rendelkeznie szerepe **tulajdonos** az Azure-előfizetésében.
>

Az a **konfigurálása** lap a **kód** területen válassza ki a szervezet az Azure DevOps-szolgáltatásokkal, projekt, tárház és ágat, ahonnan a folyamatos üzembe helyezés. Ha befejezte, kattintson a **Folytatás**.

Az a **konfigurálása** lap a **összeállítása** területén adja meg a nyelvi keretrendszerébe, amely az Azure DevOps-szolgáltatásokkal a kiválasztott tárház az összeállítási feladatok futtatásához csatlakoznia kell. Ha befejezte, kattintson a **Folytatás**.

Az a **teszt** lapon, döntse el, hogy engedélyezze a terhelési teszteket, majd kattintson a **Folytatás**.

Attól függően, a [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/plans/) az App Service-csomag is megjelenhetnek egy **üzembe helyezés átmeneti környezetbe** lapot. Válassza ki, hogy e [üzembe helyezési pontok engedélyezése](web-sites-staged-publishing.md), majd kattintson **Folytatás**. 

### <a name="finish-configuration"></a>Konfiguráció befejezéséhez

Az a **összefoglalás** oldalon ellenőrizze a beállításokat, kattintson a **Befejezés**.

Konfiguráció befejezését követően a kijelölt adattárban új véglegesítéseket az App Service alkalmazásba folyamatosan vannak telepítve.

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

Folyamatos üzembe helyezés letiltása, keresse meg az App Service-oldalra a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **GitHub** vagy **Azure DevOps-szolgáltatásokkal** vagy **BitBucket**  >  **Leválasztása**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>További források

* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [A PowerShell használata az Azure-ban]
* [A Git dokumentációja]
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)
* [Az Azure használatával automatikusan létrehozhat egy CI/CD-folyamat számára egy ASP.NET 4-alkalmazás üzembe helyezése](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[A PowerShell használata az Azure-ban]: /powershell/azureps-cmdlets-docs
[A Git dokumentációja]: https://git-scm.com/documentation

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Ismerkedés az Azure DevOps-szolgáltatásokkal]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
