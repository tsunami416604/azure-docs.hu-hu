---
title: Központi telepítés – gyakori kérdések az Azure web Apps |} Microsoft Docs
description: Az Azure App Service Web Apps szolgáltatásának a telepítéssel kapcsolatos gyakori kérdésekre adott válaszok.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 54c99cc9dde6a9706c75401dd75b98f8f5de7cec
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068070"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Központi telepítés – gyakori kérdések az Azure Web Apps

Ez a cikk gyakran ismételt kérdések (GYIK) választ tartalmaz központi telepítési problémái a [az Azure App Service Web Apps szolgáltatásának](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>I vagyok csak Ismerkedés az App Service web Apps alkalmazások. Hogyan tehetők közzé a kódot?

Az alábbiak közzétételéhez a webes alkalmazás kódja:

*   Telepítse a Visual Studio használatával. Ha a Visual Studio megoldás, kattintson a jobb gombbal a webes projektet, és válassza **közzététel**.
*   Az FTP-ügyfél központi telepítését. Töltse le a közzétételi profil a kódot a telepíteni kívánt webalkalmazás az Azure-portálon. Ezt követően töltse fel a fájlokat a \site\wwwroot közzétételi profil FTP ugyanazon hitelesítő adatokkal.

További információkért lásd: [telepítse az alkalmazást az App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Szeretnék egy hibaüzenet jelenik meg, a Visual Studio telepítése közben. Hogyan lehet elhárítani ezt?

Ha a következő üzenet jelenik meg, akkor előfordulhat, hogy használja az SDK-t a régebbi verziója: "hiba történt az erőforrás"YourResourceName"erőforráscsoport"YourResourceGroup"központi telepítése során: MissingRegistrationForLocation: az előfizetés nincs regisztrálva a az erőforrás típusa "összetevők" a következő helyen: "USA középső RÉGIÓJA". Regisztrálja újra a szolgáltatóhoz tartozó ahhoz, hogy rendelkezik engedéllyel a helyhez." 

Ez a hiba elhárításához váltson a [SDK legújabb](https://azure.microsoft.com/downloads/). Ha ez az üzenet megjelenik, és az SDK legújabb rendelkezik, a támogatási kérelem elküldéséhez.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hogyan telepítsék központilag az ASP.NET alkalmazás az App Service a Visual Studio?
<a id="deployasp"></a>

Az oktatóanyag [az első ASP.NET-webalkalmazás létrehozása az Azure-ban öt perc múlva](app-service-web-get-started-dotnet.md) bemutatja, hogyan egy webalkalmazást az App Service-ben az ASP.NET webalkalmazás telepíteni a Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Mik azok a különféle üzembe helyezési hitelesítő adatok?

App Service kétféle típusú hitelesítő adatok helyi Git-telepítés és az FTP/S központi telepítés támogatja. Üzembe helyezési hitelesítő adatok konfigurálásával kapcsolatos további információkért lásd: [telepítési hitelesítő adatok beállítása az App Service](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Mi az az App Service webalkalmazásba a fájl vagy könyvtár szerkezete?

Az App Service-alkalmazás fájlstruktúrájával kapcsolatos információkért lásd: [struktúra fájlt az Azure-ban](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hogyan hárítható "FTP hiba 550 - ott nincs elég hely a lemezen" Amikor megpróbálok FTP-fájlok?

Ez az üzenet jelenik meg, akkor valószínű, hogy fut a lemezkvóta a service-csomag a webalkalmazás. Növelheti a lemezterület-szükséglet alapján magasabb szolgáltatásréteg módosítania. Tervek és erőforrás-korlátozások árazással kapcsolatos további információkért lásd: [App Service díjszabás](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hogyan állíthatom be a folyamatos üzembe helyezést az App Service-webalkalmazás?

Beállíthat több erőforrást, beleértve a Visual Studio Team Services, OneDrive, GitHub, Bitbucket, Dropbox és egyéb Git adattárak folyamatos üzembe helyezést. Ezek a lehetőségek érhetők el a portálon. [Az App Service folyamatos üzembe helyezés](app-service-continuous-deployment.md) egy hasznos oktatóanyag, amely azt ismerteti, hogyan állíthatja be a folyamatos üzembe helyezést.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hogyan hibaelhárítása a folyamatos üzembe helyezés a Githubról, majd Bitbucket problémái?

Folyamatos üzembe helyezés, GitHub vagy Bitbucket problémákat vizsgál, lásd: [vizsgálja a folyamatos üzembe helyezés](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Tudom a hely nem FTP és a kód közzététele. Hogyan lehet elhárítani ezt?

FTP-problémák megoldása:

1. Győződjön meg arról, hogy a megfelelő állomásnév és a hitelesítő adatok megadása. Részletes információ a különböző típusú hitelesítő adatokat és a használatukat: [üzembe helyezési hitelesítő adatok](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Győződjön meg arról, hogy az FTP-portot nem blokkolja tűzfal. A portnak ezeket a beállításokat kell rendelkeznie:
    * FTP-vezérlőkapcsolati port: 21
    * FTP-adatok csatlakozási portja: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hogyan tehetők közzé a kódot az App Service?

Az Azure gyors üzembe helyezési segítséget az alkalmazás telepítéséhez a központi telepítés verem és a választott módszerrel tervezték. A gyors üzembe helyezés, használja az Azure portálon, keresse fel **beállítások** > **alkalmazástelepítés**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Miért nem saját alkalmazás néha telepítést követően újraindítható az App Service?

Című témakörben olvashat a körülmények között, amely alatt az alkalmazás központi telepítése újraindítást eredményezhet, [futásidejű problémák és telepítési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Mivel a cikk azt ismerteti, telepíti az App Service fájlok a wwwroot mappába. Soha ne közvetlenül újraindul az alkalmazás.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Hogyan integrálja a Visual Studio Team Services kódot az App Service?

A Visual Studio Team Services használata a folyamatos üzembe helyezés két lehetőség közül választhat:

*   Használjon egy Git-projektet. A központi telepítési beállításokat, hogy a tárházban keresztül kapcsolódik App szolgáltatáson keresztül.
*   A Team Foundation verzió vezérlő (TFVC) projekt használja. A build ügynök az App Service segítségével telepítheti.

Folyamatos kód telepítésének mindkét ezeket a beállításokat attól függ, meglévő fejlesztői munkafolyamatok és a bejelentkezési eljárásokat. További információval a következő cikkek szolgálnak: 

*   [Folyamatos üzembe helyezés, az alkalmazás egy Azure-webhelyen történő megvalósítása](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Webalkalmazás telepítheti központilag a Visual Studio Team Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hogyan használjam FTP vagy ftps-t szeretné telepíteni az alkalmazást az App Service?

FTP- vagy FTPS használatával történő telepítése a webes alkalmazás az App Service kapcsolatos információkért lásd: [FTP/S segítségével telepítheti az alkalmazást az App Service](app-service-deploy-ftp.md).
