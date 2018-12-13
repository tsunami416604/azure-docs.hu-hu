---
title: Üzembe helyezés – gyakori kérdések – az Azure App Service |} A Microsoft Docs
description: Üzembe az Azure App Service Web Apps szolgáltatásával kapcsolatos gyakori kérdésekre adott válaszok.
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
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 833b655246035fa3eb9a820316a95b73eed5b2bd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262112"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Üzembe helyezés – gyakori kérdések az Azure Web Apps esetében

Ez a cikk gyakori kérdések (GYIK) választ rendelkezik kapcsolatban felmerülő üzembehelyezési hibák a [Azure App Service Web Apps funkciójával](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>E vagyok még csak most ismerkedik az App Service web Apps alkalmazások. Hogyan tehetők közzé a kódomat?

Íme néhány lehetőség a közzététele a webalkalmazás kódját:

*   Üzembe helyezése a Visual Studio használatával. Ha a Visual studióban, kattintson a jobb gombbal a webes projektre, és válassza **közzététel**.
*   Az FTP-ügyfél központi telepítését. Az Azure Portalon a webalkalmazás üzembe helyezése a kódokat a kívánt a közzétételi profil letöltése. Ezt követően a fájlok feltöltése \site\wwwroot közzétételi profil FTP azonos hitelesítő adatok használatával.

További információkért lásd: [telepítse az alkalmazást az App Service-ben](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Egy hibaüzenet jelenik meg a Visual Studióból üzembe látható. Hogyan oldhatom fel ez?

Ha a következő üzenetet látja, akkor előfordulhat, hogy használja az SDK egy régebbi verziója: "Hiba"YourResourceName"erőforrás 'YourResourceGroup' erőforráscsoportban üzembe helyezés során: MissingRegistrationForLocation: Az előfizetés nincs regisztrálva az erőforrástípus "összetevő" a következő helyen: "USA középső RÉGIÓJA". Regisztrálja újra a szolgáltató annak érdekében, hogy ezen a helyen érheti el." 

A probléma megoldásához frissítse a [legújabb SDK](https://azure.microsoft.com/downloads/). Ha az üzenet, és a legújabb SDK rendelkezik, küldjön egy támogatási kérést.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hogyan helyezhetek üzembe egy ASP.NET alkalmazás a Visual Studio App Service-ben?
<a id="deployasp"></a>

Az oktatóanyag [hozzon létre az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban](app-service-web-get-started-dotnet.md) bemutatja, hogyan helyezhet üzembe egy ASP.NET-alkalmazás az App Service webalkalmazásba a Visual Studio 2017 használatával.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Mik azok az üzembe helyezési hitelesítő adatokat a különböző típusú?

App Service-ben FTP/S telepítéséhez és a Git helyi üzemelő példányának kétféle típusú hitelesítő adatokat támogatja. Üzembe helyezési hitelesítő adatok konfigurálásával kapcsolatos további információkért lásd: [üzembe helyezési hitelesítő adatok konfigurálása az App Service](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Mi az App Service webalkalmazás fájl vagy könyvtár szerkezete?

Az App Service-alkalmazás fájl szerkezete kapcsolatos információkért lásd: [struktúra fájlt az Azure-ban](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hogyan oldhatom fel a "FTP hiba – 550 itt nem elég hely a lemezen" FTP-fájlok meg?

Ha ezt az üzenetet látja, valószínű, hogy futtatja, lemezkvóta a service-csomagban a webalkalmazás számára. Szüksége lehet a vertikális felskálázás a lemezterület-szükséglet alapján magasabb szolgáltatási szintre. Csomagok és erőforráskorlátok díjszabással kapcsolatos további információkért lásd: [App Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hogyan állíthatok be a folyamatos üzembe helyezés az App Service webalkalmazás?

Beállíthat folyamatos üzembe helyezést az több erőforrás, beleértve az Azure DevOps, onedrive vállalati verzió, GitHub, Bitbucket, Dropbox és más Git-tárházakat. Ezek a lehetőségek érhetők el a portálon. [Folyamatos üzembe helyezés az App Service-](app-service-continuous-deployment.md) hasznos oktatóanyag, amely azt ismerteti, hogyan állítható be a folyamatos üzembe helyezés van.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hogyan háríthatom el a problémákat a GitHub és Bitbucket folyamatos üzembe helyezéssel?

Segítségre van szüksége a GitHub vagy bitbucket-alapú folyamatos üzembe helyezéssel kapcsolatos problémák kivizsgálása, lásd: [vizsgálja a folyamatos üzembe helyezés](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>E nem FTP-ról saját helyre, és tegye közzé saját kód. Hogyan oldhatom fel ez?

FTP-problémák megoldása:

1. Győződjön meg arról, hogy a helyes állomásnevet és hitelesítő adatok megadása. Különböző típusú hitelesítő adatok és a használatukkal kapcsolatos részletes információkért lásd: [üzembe helyezési hitelesítő adatok](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Győződjön meg arról, hogy az FTP-portok nem blokkolja tűzfal. A portok rendelkeznie kell ezeket a beállításokat:
    * FTP-vezérlőkapcsolati port: 21
    * FTP-adatkapcsolati port: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hogyan tehetők közzé saját kód App Service-ben?

Az Azure-Gyorssablonok segítséget az alkalmazás üzembe helyezése az üzembe helyezési vermek és a választott módszerrel tervezték. A rövid útmutatóhoz az Azure Portal használatához nyissa meg az app service-ben a **központi telepítési**, jelölje be **rövid**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Miért nem az alkalmazás időnként telepítést követően újraindítható az App Service?

A körülmények, amelyben egy alkalmazás központi telepítésének azt eredményezheti, hogy a számítógép újraindítását kapcsolatos további információkért lásd: [memóriafelhasználás problémákat és üzembe helyezési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). A cikk azt ismerteti, mivel az App Service fájlok a wwwroot mappája üzembe helyezi. Ez soha nem közvetlenül újraindítja az alkalmazást.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hogyan integrálhatja az Azure DevOps-kódot az App Service?

A folyamatos üzembe helyezés az Azure DevOps két lehetősége van:

*   Használjon egy Git-projektet. Csatlakozhat az App Service-n keresztül az üzembe helyezési lehetőségek, hogy az adattárban.
*   A Team Foundation verzió Control (TFVC) projekt használja. Telepítheti a fordító-ügynökhöz az App Service használatával.

Folyamatos kód üzembe helyezése mindkét ezek a beállítások a meglévő fejlesztői munkafolyamatokba, és hogy az eljárások függ. További információval a következő cikkek szolgálnak: 

*   [Az alkalmazás Azure-webhelyen való folyamatos üzembe helyezés végrehajtása](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Úgy is, egy webalkalmazás üzembe helyezése az Azure DevOps szervezet beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hogyan használhatom FTP- vagy FTPS az alkalmazás üzembe helyezése az App Service-ben?

Az App Service-ben a webes alkalmazás üzembe helyezése FTP- vagy FTPS használatával kapcsolatos információkért lásd: [telepítse az alkalmazást az App Service-ben FTP/S használatával](app-service-deploy-ftp.md).
