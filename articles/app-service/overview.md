---
title: Az App Service áttekintése – Azure |} A Microsoft Docs
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
ms.custom: seodec18
ms.openlocfilehash: 5f3fed9255b86696b0e71c70adcd7ff23c0573d9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352444"
---
# <a name="app-service-overview"></a>App Service – áttekintés

*Az Azure App Service* egy szolgáltatás REST API-k, a webalkalmazások üzemeltetéséhez és mobilháttereket. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python. Alkalmazások futnak, és a Windows és Linux-alapú környezetekben is méretezheti. Linux-alapú környezetek esetén tekintse meg az App Service-ben a [Linuxon futó App Service](containers/app-service-linux-intro.md). 

App Service-ben nem csak az a Microsoft Azure ad hozzá az alkalmazásokhoz, például a biztonsági, terheléselosztási, automatikus skálázást, és automatikus kezelés. DevOps-képességeket is biztosít, mint például a folyamatos üzembe helyezés az Azure DevOpsból, a GitHubról, a Docker Hubról és más forrásokból, a csomagkezelés, az átmeneti környezetek, az egyéni tartomány és az SSL-tanúsítványok. 

Az App Service segítségével csak a felhasznált Azure-beli számítási erőforrásokért fizet. Határozza meg a számítási erőforrások a _App Service-csomag_ futtatása az alkalmazásokat. További információkért lásd: [Azure App Service-csomagok áttekintése](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Miért előnyös az App Service használata?

Az App Service legfontosabb funkcióival a következők:

* **Több nyelv és keretrendszer** – ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP vagy Python elsőosztályú támogatással rendelkezik az App Service-ben. [PowerShell- és egyéb szkripteket vagy futtatható fájlokat](webjobs-create.md) futtathat háttérszolgáltatásként.
* **DevOps-optimalizálás** – [Folyamatos integrációt és üzembe helyezést](deploy-continuous-deployment.md) állíthat be az Azure DevOps, GitHub, BitBucket, Docker Hub vagy az Azure Container Registry szolgáltatásokhoz. [Teszt- és átmeneti környezetek](deploy-staging-slots.md) segítségével küldheti ki a frissítéseket. Alkalmazásait az App Service-ben az [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy a [többplatformos parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](web-sites-scale.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS-platformokhoz és helyszíni adatokhoz** – Több mint 50, nagyvállalati rendszerekhez (például SAP), SaaS-szolgáltatásokhoz (például Salesforce), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz) készült [összekötő](../connectors/apis-list.md) közül választhat. Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](app-service-hybrid-connections.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/en-us/trustcenter). [Azure Active Directory](configure-authentication-provider-aad.md) vagy közösségi bejelentkezés ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md), és [Microsoft](configure-authentication-provider-microsoft.md)) használatával hitelesítheti a felhasználókat. [IP-címkorlátozásokat](app-service-ip-restrictions.md) hozhat létre és [kezelheti a szolgáltatásidentitásokat](overview-managed-identity.md).
* **Alkalmazássablonok** – Az [Azure Marketplace](https://azure.microsoft.com/marketplace/) alkalmazássablon-listájának széles kínálatából választhat, például a WordPresst, a Joomlát vagy a Drupalt.
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.
* **API- és mobilszolgáltatások** – az App Service kulcsrakész CORS-támogatást biztosít a RESTful API-forgatókönyvekhez, valamint egyszerűsíti a mobilalkalmazásokhoz kapcsolódó környezetek engedélyezésével, offline adatszinkronizálással, leküldéses értesítéseket és hitelesítést.
* **Kiszolgáló nélküli kód** – Kódrészleteteket vagy szkripteket futtathat igény szerint anélkül, hogy kifejezetten felügyelnie vagy kezelnie kellene az infrastruktúrát, és csak a kódja által ténylegesen használt számítási időért kell fizetnie (további információ: [Azure Functions](/azure/azure-functions/)).

App Service-ben mellett az Azure webhelyek és webalkalmazások üzemeltetéséhez használt egyéb szolgáltatások kínál. A legtöbb esetben az App Service szolgáltatás a legjobb választás.  A mikroszolgáltatási architektúra esetében fontolja meg a [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) használatát. Ha a kódot futtató virtuális gépek nagyobb mértékű felügyeletére van szüksége, akkor érdemes megfontolnia az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát. További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](overview-compare.md).

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
> [Python (Linuxon)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
