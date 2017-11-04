---
title: "A webalkalmazások áttekintése | Microsoft Docs"
description: "Ismerje meg, hogy az Azure App Service segítségével miként fejleszthet és üzemeltethet webalkalmazásokat."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>A webalkalmazások áttekintése

*Az Azure App Service Web Apps* (vagy csak a Web Apps) szolgáltatás REST API-k, a webalkalmazások üzemeltetéséhez, és a mobileszköz vissza ezzel véget ér. A kedvenc nyelven fejleszthet, a .NET, a .NET Core, Java, Ruby, Node.js, PHP vagy Python kell azt. Futtathatja és alkalmazások méretezése a Windows vagy Linux rendszerű virtuális gépek könnyű (lásd: [App Service Linux](containers/app-service-linux-intro.md)). 

Webes alkalmazások nem csak a kiemelt Microsoft Azure ad hozzá az alkalmazás, például a biztonság, a terheléselosztás, az automatikus skálázást, és automatizált kezelés. Akkor is kihasználhatják annak DevOps képességeit, például a VSTS, a Githubon, Docker Hub, és más adatforrások felügyeleti csomag, a folyamatos üzembe helyezés átmeneti, az egyéni tartomány és az SSL-tanúsítványokat. 

Az App Service kell fizetnie az Azure számítási erőforrásokat használ. Határozza meg a számítási erőforrásokat használ a _App Service-csomag_ , futtassa a Web Apps szolgáltatást. További információkért lásd: [App Service-csomagok az Azure Web Apps](azure-web-sites-web-hosting-plans-in-depth-overview.md).

Az alábbi ötperces videó bemutatja az Azure App Service Web Apps szolgáltatást.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>Miért érdemes használni a webalkalmazásokat?
Az alábbiakban néhány kulcsszolgáltatása, az App Service Web Apps:

* **Több nyelv és keretrendszer** -webalkalmazások rendelkezik kiváló támogatást nyújt az ASP.NET, az ASP.NET Core, Java, Ruby, Node.js, PHP és Python. Is futtathatja [PowerShell és egyéb parancsfájlokat vagy a végrehajtható fájlok](web-sites-create-web-jobs.md) regisztrációja, mivel a szolgáltatások háttérben.
* **DevOps optimalizálás** -beállítása [folyamatos integrációt és telepítést](app-service-continuous-deployment.md) a Visual Studio Team Services, GitHub, BitBucket, Docker-központ vagy az Azure Tárolószolgáltatás. [Teszt- és átmeneti környezetek](web-sites-staged-publishing.md) segítségével küldheti ki a frissítéseket. Kezelheti az alkalmazásokat, a Web Apps használatával [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy a [platformfüggetlen parancssori felület (CLI)](/cli/azure/install-azure-cli).
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](web-sites-scale.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Kapcsolatok SaaS platformokhoz és helyszíni adatokhoz** -több mint 50 választhat [összekötők](../connectors/apis-list.md) nagyvállalati rendszerekhez (például SAP), SaaS-szolgáltatásokhoz (például Salesforce), valamint internetes szolgáltatásokhoz (például a Facebookhoz). Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](../biztalk-services/integration-hybrid-connection-overview.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/TrustCenter/). A felhasználók hitelesítésére [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) vagy a közösségi bejelentkezés ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md), és [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Hozzon létre [IP-címkorlátozások](app-service-ip-restrictions.md) és [szolgáltatás identitáskezelést](app-service-managed-service-identity.md).
* **Alkalmazássablonok** -alkalmazás sablonjainak kiterjedt alkalmazássablon-listájáról válassza ki a [Azure piactér](https://azure.microsoft.com/marketplace/), például a WordPress, a Joomla vagy a Drupal.
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.
* **API és a mobil szolgáltatások** -webalkalmazások kulcsrakész CORS támogatást nyújt a RESTful API forgatókönyvek és egyszerűbbé teszi a mobilalkalmazás forgatókönyvek hitelesítést, offline adatszinkronizálás, leküldéses értesítéseket és további engedélyezésével.
* **Kiszolgáló nélküli kód** - futtassa egy kódrészletet vagy igény szerinti parancsfájl anélkül, hogy explicit módon kiosztania vagy infrastruktúra kezelése, és csak azért fizessen a számítási idejét a kód ténylegesen használ (lásd: [Azure Functions](/azure/azure-functions/)).

Az App Service-webalkalmazásokon kívül az Azure más szolgáltatásokat is kínál, amelyek használhatók webhelyek és webalkalmazások üzemeltetésére. A legtöbb forgatókönyvhöz a Webalkalmazások a legjobb választás.  Mikroszolgáltatási architektúra esetében érdemes lehet [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Ha a virtuális gépeket, amennyit a kódja fut. a teljesebb körű vezérlése van szüksége, fontolja meg a [Azure virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines/). További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Következő lépések

Az első webalkalmazás létrehozásához.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

