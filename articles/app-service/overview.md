---
title: Áttekintés
description: Ismerje meg, hogy az Azure App Service segítségével miként fejleszthet és üzemeltethet webalkalmazásokat.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 04/30/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: f4b42f2ed43da68daabb3bd334a362071202be42
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073725"
---
# <a name="app-service-overview"></a>Az App Service áttekintése

A *Azure app Service* egy HTTP-alapú szolgáltatás, amellyel webalkalmazásokat, REST API-kat és mobil back-végpontokat üzemeltet. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python. Az alkalmazások Windows-és Linux-alapú környezetekben is könnyedén futnak és méretezhetők. Linux-alapú környezetek esetén lásd: [App Service Linux rendszeren](containers/app-service-linux-intro.md). 

App Service nem csupán az alkalmazáshoz Microsoft Azure, például a biztonság, a terheléselosztás, az automatikus skálázás és az automatizált felügyelet erejét adja hozzá. Kihasználhatja a DevOps képességeit, például a folyamatos üzembe helyezést az Azure DevOps, a GitHubból, a Docker hub-ból és más forrásokból, a csomagok felügyeletéről, az átmeneti környezetekről, az egyéni tartományról és a TLS/SSL-tanúsítványokról. 

Az App Service segítségével csak a felhasznált Azure-beli számítási erőforrásokért fizet. A felhasznált számítási erőforrásokat a _app Service-csomag_ határozza meg, amelyet az alkalmazásaiban futtat. További információ: Azure App Service- [csomagok áttekintése](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Miért előnyös az App Service használata?

A App Service főbb jellemzői:

* **Több nyelv és keretrendszer** – app Service rendelkezik a ASP.net, a ASP.net Core, a Java, a Ruby, a Node.js, a php vagy a Python első osztályú támogatásával. [PowerShell- és egyéb szkripteket vagy futtatható fájlokat](webjobs-create.md) futtathat háttérszolgáltatásként.
* **Felügyelt éles környezet** – app Service automatikusan [kijavításokat végez, és KARBANTARTJA az operációs rendszer és a nyelv keretrendszerét](overview-patch-os-runtime.md) . Töltsön fel időt a nagyszerű alkalmazások írásakor, és hagyja, hogy az Azure aggódnia legyen a platformmal kapcsolatban.
* **DevOps-optimalizálás** – [Folyamatos integrációt és üzembe helyezést](deploy-continuous-deployment.md) állíthat be az Azure DevOps, GitHub, BitBucket, Docker Hub vagy az Azure Container Registry szolgáltatásokhoz. [Teszt- és átmeneti környezetek](deploy-staging-slots.md) segítségével küldheti ki a frissítéseket. Alkalmazásait az App Service-ben az [Azure PowerShell](/powershell/azure/) vagy a [többplatformos parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** - Manuálisan vagy automatikusan is végezhet [felfelé skálázást](manage-scale-up.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
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
> [Python (on Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
