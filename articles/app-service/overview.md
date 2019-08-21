---
title: App Service áttekintése – Azure | Microsoft Docs
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
ms.openlocfilehash: 8a7268ebae80df5a0004e824641e24b979b9c79f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637713"
---
# <a name="app-service-overview"></a>App Service – áttekintés

A *Azure app Service* egy HTTP-alapú szolgáltatás, amellyel webalkalmazásokat, REST API-kat és mobil back-végpontokat üzemeltet. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python. Az alkalmazások Windows-és Linux-alapú környezetekben is könnyedén futnak és méretezhetők. Linux-alapú környezetek esetén lásd: [App Service Linux rendszeren](containers/app-service-linux-intro.md). 

App Service nem csupán az alkalmazáshoz Microsoft Azure, például a biztonság, a terheléselosztás, az automatikus skálázás és az automatizált felügyelet erejét adja hozzá. DevOps-képességeket is biztosít, mint például a folyamatos üzembe helyezés az Azure DevOpsból, a GitHubról, a Docker Hubról és más forrásokból, a csomagkezelés, az átmeneti környezetek, az egyéni tartomány és az SSL-tanúsítványok. 

Az App Service segítségével csak a felhasznált Azure-beli számítási erőforrásokért fizet. A felhasznált számítási erőforrásokat a _app Service-csomag_ határozza meg, amelyet az alkalmazásait futtat. További információ: Azure App Service- [csomagok áttekintése](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Miért előnyös az App Service használata?

A App Service főbb jellemzői:

* **Több nyelv és keretrendszer** – app Service rendelkezik a ASP.net, a ASP.net Core, a Java, a Ruby, a Node. js, a PHP és a Python támogatásával. [PowerShell- és egyéb szkripteket vagy futtatható fájlokat](webjobs-create.md) futtathat háttérszolgáltatásként.
* **DevOps-optimalizálás** – [Folyamatos integrációt és üzembe helyezést](deploy-continuous-deployment.md) állíthat be az Azure DevOps, GitHub, BitBucket, Docker Hub vagy az Azure Container Registry szolgáltatásokhoz. [Teszt- és átmeneti környezetek](deploy-staging-slots.md) segítségével küldheti ki a frissítéseket. Alkalmazásait az App Service-ben az [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy a [többplatformos parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](manage-scale-up.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS-platformokhoz és helyszíni adatokhoz** – Több mint 50, nagyvállalati rendszerekhez (például SAP), SaaS-szolgáltatásokhoz (például Salesforce), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz) készült [összekötő](../connectors/apis-list.md) közül választhat. Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](app-service-hybrid-connections.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/en-us/trustcenter). [Azure Active Directory](configure-authentication-provider-aad.md) vagy közösségi bejelentkezés ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md), és [Microsoft](configure-authentication-provider-microsoft.md)) használatával hitelesítheti a felhasználókat. [IP-címkorlátozásokat](app-service-ip-restrictions.md) hozhat létre és [kezelheti a szolgáltatásidentitásokat](overview-managed-identity.md).
* **Alkalmazássablonok** – Az [Azure Marketplace](https://azure.microsoft.com/marketplace/) alkalmazássablon-listájának széles kínálatából választhat, például a WordPresst, a Joomlát vagy a Drupalt.
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.
* Az **API-és mobil funkciók** – app Service kulcsrakész CORS-támogatást biztosít a REST API-forgatókönyvekhez, és egyszerűbbé teszi a Mobile apps-forgatókönyveket a hitelesítés, az offline adatszinkronizálás, a leküldéses értesítések és egyebek engedélyezésével.
* **Kiszolgáló nélküli kód** – Kódrészleteteket vagy szkripteket futtathat igény szerint anélkül, hogy kifejezetten felügyelnie vagy kezelnie kellene az infrastruktúrát, és csak a kódja által ténylegesen használt számítási időért kell fizetnie (további információ: [Azure Functions](/azure/azure-functions/)).

App Service mellett az Azure más szolgáltatásokat is kínál, amelyek a webhelyek és a webalkalmazások üzemeltetésére használhatók. A legtöbb esetben App Service a legjobb választás.  A mikroszolgáltatási architektúra esetében fontolja meg a [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) használatát. Ha a kódot futtató virtuális gépek nagyobb mértékű felügyeletére van szüksége, akkor érdemes megfontolnia az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát. További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](overview-compare.md).

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
