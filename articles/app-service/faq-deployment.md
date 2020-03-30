---
title: Gyakori kérdések a telepítésről – Azure App Service | Microsoft dokumentumok
description: Válaszok az Azure App Service Web Apps szolgáltatásának üzembe helyezésével kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671683"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Az Azure-beli Web Apps üzembe helyezéssel kapcsolatos gyakori kérdései

Ez a cikk választ ad az Azure App Service [Web Apps szolgáltatásának](https://azure.microsoft.com/services/app-service/web/)üzembe helyezési problémáival kapcsolatos gyakori kérdésekre .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Én csak most kezdődött el az App Service webes alkalmazásokat. Hogyan tehetem közzé a kódomat?

Íme néhány lehetőség a webalkalmazás kódjának közzétételére:

*   Üzembe helyezés a Visual Studio használatával. Ha rendelkezik a Visual Studio-megoldással, kattintson a jobb gombbal a webalkalmazás-projektre, majd válassza **a Közzététel parancsot.**
*   Telepítés FTP-ügyfél használatával. Az Azure Portalon töltse le annak a webalkalmazásnak a közzétételi profilját, amelybe telepíteni szeretné a kódot. Ezután töltse fel a fájlokat a \site\wwwroot mappába ugyanazzal a közzétételi profilOS FTP-hitelesítő adatokkal.

További információt az [Alkalmazás telepítése az App Service szolgáltatásba](deploy-local-git.md)című témakörben talál.

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Hibaüzenet jelenik meg, amikor a Visual Studióból próbálok telepíteni. Hogyan oldhatom meg ezt a hibát?

Ha a következő üzenet jelenik meg, előfordulhat, hogy az SDK egy régebbi verzióját használja: "Hiba a "YourResourceName" erőforrás-erőforrás-telepítés során a "YourResourceGroup" erőforráscsoportban: MissingRegistrationForLocation: Az előfizetés nincs regisztrálva a "components" típusú erőforrástípust az "USA középső részén" található. Regisztráljon újra erre a szolgáltatóra, hogy hozzáférhessen ehhez a helyhez." 

A hiba elhárításához frissítsen a [legújabb SDK-ra.](https://azure.microsoft.com/downloads/) Ha ezt az üzenetet látja, és a legújabb SDK-val rendelkezik, nyújtson be támogatási kérelmet.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hogyan telepíthetek egy ASP.NET alkalmazást a Visual Studio-ból az App Service-be?
<a id="deployasp"></a>

Az oktatóanyag [az első ASP.NET webalkalmazás létrehozása az Azure-ban öt perc alatt](app-service-web-get-started-dotnet.md) bemutatja, hogyan telepíthet egy ASP.NET webalkalmazást egy webalkalmazásba az App Service-ben a Visual Studio használatával.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Melyek a különböző típusú központi telepítési hitelesítő adatok?

Az App Service kétféle hitelesítő adatot támogat a helyi Git-telepítéshez és az FTP/S központi telepítéshez. A telepítési hitelesítő adatok konfigurálásáról az [App Service telepítési hitelesítő adatainak konfigurálása](deploy-configure-credentials.md)című témakörben talál további információt.

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Mi az App Service webalkalmazás fájl- vagy könyvtárstruktúrája?

Az App Service-alkalmazás fájlszerkezetéről a [Fájlstruktúra az Azure-ban](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)című témakörben talál további információt.

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hogyan oldható meg az "FTP-hiba 550 - Nincs elég hely a lemezen", amikor megpróbálom FTP fájlokat?

Ha ez az üzenet jelenik meg, akkor valószínű, hogy lemezkvótába fut a webalkalmazás szolgáltatási csomagjában. Előfordulhat, hogy a lemezterület igényei nek megfelelően magasabb szolgáltatási szintre kell skáláznia. Az árképzési tervekről és az erőforráskorlátokról az [App Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/)című témakörben talál további információt.

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hogyan állíthatom be a folyamatos üzembe helyezést az App Service webapphoz?

Folyamatos üzembe helyezést több erőforrásból is beállíthat, például az Azure DevOps, a OneDrive, a GitHub, a Bitbucket, a Dropbox és más Git-adattárakból. Ezek a lehetőségek a portálon érhetők el. [Az App Service folyamatos üzembe helyezése](deploy-continuous-deployment.md) hasznos oktatóanyag, amely bemutatja a folyamatos üzembe helyezés beállítását.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hogyan háríthatók el a GitHub és a Bitbucket folyamatos üzembe helyezésével kapcsolatos problémák?

A GitHubvagy a Bitbucket folyamatos üzembe helyezésével kapcsolatos problémák vizsgálatával kapcsolatos segítségért olvassa el [a Folyamatos telepítés vizsgálata .](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Nem tudok FTP-t küldeni a webhelyemen, és közzétenni a kódomat. Hogyan oldhatom meg a problémát?

FTP-problémák megoldása:

1. Ellenőrizze, hogy a megfelelő állomásnevet és hitelesítő adatokat adja-e meg. A különböző típusú hitelesítő adatokról és azok használatáról a [Telepítési hitelesítő adatok című témakörben](https://github.com/projectkudu/kudu/wiki/Deployment-credentials)talál részletes információt.
2. Ellenőrizze, hogy a tűzfal nem blokkolja-e az FTP-portokat. A portok nak a következő beállításokkal kell rendelkezniük:
    * FTP-vezérlőkapcsolati port: 21
    * FTP-adatkapcsolat iportja: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hogyan tehetem közzé a kódomat az App Service-ben?

Az Azure-gyorsindítás célja, hogy segítsen az alkalmazás üzembe helyezésében a központi telepítési verem és az Ön által választott módszer használatával. A gyorsútmutató használatához az Azure Portalon nyissa meg az alkalmazásszolgáltatást, a **Telepítés**csoportban válassza a **Gyorsútmutató lehetőséget.**

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Miért indul újra néha az alkalmazás az App Service-be való telepítés után?

Ha többet szeretne tudni arról, hogy milyen körülmények között vezethet újraindításegy alkalmazásközponti telepítés, olvassa el a [Központi telepítés és a futásidejű problémák című témakört.](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts") A cikk leírása szerint az App Service fájlokat telepít a wwwroot mappába. Soha nem indítja újra közvetlenül az alkalmazást.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hogyan integrálhatom az Azure DevOps-kódot az App Service szolgáltatásba?

Az Azure DevOps használatával két lehetőség közül választhat:

*   Git-projekt használata. Az App Service-en keresztül a Deployment Center használatával csatlakozhat.
*   Használja a Team Foundation verzióvezérlés (TFVC) projektet. Üzembe helyezése az App Service buildügynök használatával.

Mindkét beállítás folyamatos kódtelepítése a meglévő fejlesztői munkafolyamatoktól és a bejelentkezési eljárásoktól függ. További információval a következő cikkek szolgálnak: 

*   [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure-webhelyen](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Azure DevOps-szervezet beállítása, hogy egy webalkalmazásra telepíthessen](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hogyan vehetem üzembe az alkalmazást az App Service szolgáltatásban FTP vagy FTPS szolgáltatással?

Ha tudni szeretné, hogy miként telepíti az FTP-t vagy az FTPS szolgáltatást az App Service szolgáltatásba, [olvassa el az Alkalmazás telepítése az App Service szolgáltatásba FTP/S használatával című témakört.](deploy-ftp.md)
