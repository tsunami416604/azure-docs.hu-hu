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
ms.topic: get-started-article
ms.date: 10/28/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d366fc36e2731be34d70fd4e4fa082370c452d63


---
# <a name="web-apps-overview"></a>A webalkalmazások áttekintése
Az *App Service Web Apps* egy teljes körűen felügyelt számítógépes platform, amely webhelyek és webalkalmazások üzemeltetéséhez van optimalizálva. Ez a [szolgáltatásként kínált platform](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) a Microsoft Azure ajánlata, melynek segítségével az üzleti logikára koncentrálhat, miközben az Azure intézkedik az alkalmazások futtatásához és méretezéséhez szükséges infrastruktúráról.

Az alábbi ötperces videó bemutatja az Azure App Service Web Apps szolgáltatást.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>Mi az az App Service-webalkalmazás?
Az App Service-ben egy *webalkalmazás* az Azure által egy webhely vagy webalkalmazás üzemeltetéséhez biztosított számítási erőforrások összessége.  

A számítási erőforrások lehetnek egy megosztott vagy egy dedikált virtuális gépen (VM) a kiválasztott tarifacsomagtól függően. Az alkalmazás kódja egy felügyelt virtuális gépen fut, amely el van különítve az egyéb ügyfelektől.

A kód bármilyen nyelven vagy keretrendszerben lehet, amelyet támogat az [Azure App Service](../app-service/app-service-value-prop-what-is.md). Ilyen például az ASP.NET, a Node.js, a Java, a PHP vagy a Python. Olyan parancsfájlokat is futtathat, amelyek a [PowerShellt és más parancsfájl-készítő nyelveket](web-sites-create-web-jobs.md#acceptablefiles) használnak egy webalkalmazásban.

Példák általános alkalmazás-forgatókönyvekre, amelyekhez használhatók a Webalkalmazások: [Webalkalmazások forgatókönyvei](https://azure.microsoft.com/documentation/scenarios/web-app/) és a **Forgatókönyvek és javaslatok** szakasz itt: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Miért érdemes használni a webalkalmazásokat?
Az App Service néhány kulcsszolgáltatása, amely a Webalkalmazások szolgáltatásra is vonatkozik:

* **Több nyelv és keretrendszer** – Az App Service első osztályú támogatást kínál az ASP.NET, Node.js, Java, PHP és Python nyelvekhez. Futtathat [PowerShell és egyéb parancsfájlokat vagy futtatható fájlokat](web-sites-create-web-jobs.md) is az App Service virtuális gépeken.
* **DevOps optimalizálás** – Beállíthat [folyamatos integrációt és üzembe helyezést](app-service-continuous-deployment.md) a Visual Studio Team Services, GitHub vagy BitBucket szolgáltatásokhoz. [Teszt- és átmeneti környezetek](web-sites-staged-publishing.md) segítségével küldheti ki a frissítéseket. [A/B tesztelést](app-service-web-test-in-production-get-start.md) végezhet. Alkalmazásait az App Service-ben az [Azure PowerShell](../powershell-install-configure.md) vagy a [többplatformos parancssori felület (CLI)](../xplat-cli-install.md) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](web-sites-scale.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS platformokhoz és helyszíni adatokhoz** - Több mint 50 [összekötő](../connectors/apis-list.md) közül választhat nagyvállalati rendszerekhez (például SAP, Siebel vagy Oracle), SaaS-szolgáltatásokhoz (például Salesforce vagy Office 365), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz és a Twitterhez). Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](../biztalk-services/integration-hybrid-connection-overview.md) és az [Azure virtuális hálózatok](web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/TrustCenter/).
* **Alkalmazássablonok** - Az [Azure Piactér](https://azure.microsoft.com/marketplace/) kiterjedt alkalmazássablon-listájáról választhat, ami lehetővé teszi, hogy egy varázsló segítségével telepítsen népszerű nyílt forráskódú szoftvereket, mint a WordPress, a Joomla vagy a Drupal.
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.

Ráadásul egy webalkalmazás kihasználhatja az [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) által kínált szolgáltatásokat (mint a CORS-támogatás), valamint a [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) által kínált szolgáltatásokat (mint a leküldéses értesítések). További információk az App Service alkalmazástípusairól: [Az Azure App Service áttekintése](../app-service/app-service-value-prop-what-is.md).

Az App Service-webalkalmazásokon kívül az Azure más szolgáltatásokat is kínál, amelyek használhatók webhelyek és webalkalmazások üzemeltetésére. A legtöbb forgatókönyvhöz a Webalkalmazások a legjobb választás.  Mikroszolgáltatási architektúra esetében érdemes megfontolni a [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) használatát, ha pedig nagyobb felügyeletre van szüksége a kódot futtató virtuális gépek fölött, akkor érdemes megfontolni az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát. További információ az Azure-szolgáltatások közötti választással kapcsolatban: [Az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services összehasonlítása](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Bevezetés
Ha kezdésként telepíteni szeretné egy új webalkalmazás mintakódját az App Service-ben, kövesse [Az első webalkalmazás telepítése az Azure-ba 5 perc alatt](app-service-web-get-started.md) című oktatóanyagot. Szüksége lesz egy ingyenes Azure-fiókra.

Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.




<!--HONumber=Nov16_HO2-->


