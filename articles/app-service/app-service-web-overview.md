---
title: A webalkalmazások áttekintése | Microsoft Docs
description: Ismerje meg, hogy az Azure App Service segítségével miként fejleszthet és üzemeltethet webalkalmazásokat.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 79828193e283f0dcb80035cae0c11b050a1639ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935645"
---
# <a name="web-apps-overview"></a>A webalkalmazások áttekintése

Az *Azure App Service Web Apps* (vagy röviden Web Apps) webalkalmazásokat, REST API-kat és mobilháttereket üzemeltető szolgáltatás. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python. Egyszerűen futtathat és méretezhet alkalmazásokat Windows-alapú környezetekben. Linux-alapú környezetek esetén lásd: [App Service Linux rendszeren](containers/app-service-linux-intro.md). 

A Web Apps nem csak arra szolgál, hogy a Microsoft Azure funkcióival egészítse ki az alkalmazását, mint például a biztonság, a terheléselosztás, az automatikus skálázás és az automatikus kezelés. DevOps-képességeket is biztosít, mint például a folyamatos üzembe helyezés VSTS-ről, a GitHubról, a Docker Hubról és már forrásokból, a csomagkezelés, az átmeneti környezetek, az egyéni tartomány és az SSL-tanúsítványok. 

Az App Service segítségével csak a felhasznált Azure-beli számítási erőforrásokért fizet. Az Ön által használt számítási erőforrásokat az _App Service-csomag_ határozza meg, amelyen a Web Appst futtatja. További információk: [App Service-csomagok az Azure Web Appsben](azure-web-sites-web-hosting-plans-in-depth-overview.md).

## <a name="why-use-web-apps"></a>Miért érdemes használni a webalkalmazásokat?

Az App Service Web Apps néhány főbb jellemzője:

* **Több nyelv és keretrendszer** – A Web Apps első osztályú támogatást nyújt a következőkhöz: ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP és Python. [PowerShell- és egyéb szkripteket vagy futtatható fájlokat](web-sites-create-web-jobs.md) futtathat háttérszolgáltatásként.
* **DevOps-optimalizálás** – [Folyamatos integrációt és üzembe helyezést](app-service-continuous-deployment.md) állíthat be a Visual Studio Team Services, GitHub, BitBucket, Docker Hub vagy az Azure Container Registry szolgáltatásokhoz. [Teszt- és átmeneti környezetek](web-sites-staged-publishing.md) segítségével küldheti ki a frissítéseket. A Web Appsben az [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy a [többplatformos parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével kezelheti az alkalmazásait.
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](web-sites-scale.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS-platformokhoz és helyszíni adatokhoz** – Több mint 50, nagyvállalati rendszerekhez (például SAP), SaaS-szolgáltatásokhoz (például Salesforce), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz) készült [összekötő](../connectors/apis-list.md) közül választhat. Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](../biztalk-services/integration-hybrid-connection-overview.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/en-us/trustcenter). [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) vagy közösségi bejelentkezés ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md), és [Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)) használatával hitelesítheti a felhasználókat. [IP-címkorlátozásokat](app-service-ip-restrictions.md) hozhat létre és [kezelheti a szolgáltatásidentitásokat](app-service-managed-service-identity.md).
* **Alkalmazássablonok** – Az [Azure Marketplace](https://azure.microsoft.com/marketplace/) alkalmazássablon-listájának széles kínálatából választhat, például a WordPresst, a Joomlát vagy a Drupalt.
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.
* **API- és mobilszolgáltatások** – A Web Apps azonnal használatba vehető CORS-támogatást kínál a RESTful API-forgatókönyvekhez, valamint egyszerűsíti a mobilalkalmazásokhoz kapcsolódó forgatókönyveket a hitelesítés engedélyezésével, offline adatszinkronizálással, leküldéses értesítésekkel és továbbiakkal.
* **Kiszolgáló nélküli kód** – Kódrészleteteket vagy szkripteket futtathat igény szerint anélkül, hogy kifejezetten felügyelnie vagy kezelnie kellene az infrastruktúrát, és csak a kódja által ténylegesen használt számítási időért kell fizetnie (további információ: [Azure Functions](/azure/azure-functions/)).

Az App Service-webalkalmazásokon kívül az Azure más szolgáltatásokat is kínál, amelyek használhatók webhelyek és webalkalmazások üzemeltetésére. A legtöbb forgatókönyvhöz a Webalkalmazások a legjobb választás.  A mikroszolgáltatási architektúra esetében fontolja meg a [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) használatát. Ha a kódot futtató virtuális gépek nagyobb mértékű felügyeletére van szüksége, akkor érdemes megfontolnia az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát. További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>További lépések

Hozza létre első webalkalmazását.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linuxon)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
