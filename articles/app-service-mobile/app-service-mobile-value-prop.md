---
title: A Mobile Apps az Azure App Service-ben
description: Ismerje meg, milyen előnyökkel jár az App Service szolgáltatás bevezetése vállalati mobilalkalmazásai számára.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 46d6dfd27fb96dff49ea782f32655e1667011134
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542788"
---
# <a name="getting-started"></a>A Mobile Apps az Azure App Service-ben
Az Azure App Service egy teljes körűen felügyelt [platformszolgáltatás](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) professzionális fejlesztők számára. A szolgáltatás számos lehetőséget kínál webes, mobil- és integrációs feladatokhoz. 

Az Azure App Service-ben elérhető Mobile Apps egy rugalmasan méretezhető, globálisan elérhető mobilalkalmazás-fejlesztési platformot biztosít a nagyvállalati fejlesztőknek és rendszerintegrátoroknak.

![A Mobile Apps képességeinek vizuális áttekintése](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>A Mobile Apps szolgáltatás előnyei
A Mobile Apps szolgáltatás a következő feladatok elvégzésére alkalmas:

* **Natív és platformfüggetlen alkalmazások készítése**: Akár natív iOS, Android és Windows fejleszt alkalmazásokat vagy a platformfüggetlen Xamarin- vagy Cordova-(PhoneGap-) alkalmazások, kihasználhatja az App Service natív SDK-k használatával.
* **Csatlakozás vállalati rendszerekhez**: A Mobile Apps szolgáltatással a hozzáadhat vállalati bejelentkezési percek alatt, és csatlakozhat a vállalat helyszíni vagy felhőalapú erőforrásaihoz.
* **Az adatszinkronizálás offline használatra kész alkalmazások készítése**: Mobil munkaerejét hatékonyabbá alkalmazások készítésével, amelyek offline állapotban működjenek, és a Mobile Apps segítségével szinkronizálják az adatokat a háttérben internetkapcsolat esetén a bármely vállalati adatforrással vagy szoftverfrissítési szolgáltatásként (SaaS) API-k.
* **Leküldéses értesítések küldése másodpercek alatt milliók**: Azonnali leküldéses értesítésekkel bármilyen eszközön, személyre szabott felhasználva saját igényeinek megfelelő, és küldhet, amikor az idő a legmegfelelőbb az ügyfelek megszólítása.

## <a name="mobile-apps-features"></a>A Mobile Apps funkciói
A felhőalapú mobilalkalmazások fejlesztéséhez fontos funkciók a következők:

* **Hitelesítés és engedélyezés**: Identitás-szolgáltatóktól, beleértve az Azure Active Directory vállalati hitelesítési, valamint a közösségi szolgáltatók, mint a Facebook, Google, Twitter vagy Microsoft-fiókok támogatása. A Mobile Apps minden szolgáltató számára OAuth 2.0 protokoll szerinti engedélyezést biztosít. Integrálhatja az identitásszolgáltatók különféle speciális funkciókat biztosító SDK-it is.

    Részletesebben is tájékozódhat a [hitelesítési szolgáltatásokról].

* **Adatelérési**: A Mobile Apps egy mobilbarát OData v3 rendszerű adatforrást biztosít, amely Azure SQL Database és a egy helyszíni SQL-kiszolgálóhoz van csatolva. Mivel a szolgáltatás alapjául entitás-keretrendszer is használható, könnyen integrálható más NoSQL- és SQL-adatszolgáltatókkal, köztük az [Azure Table Storage] rendszerrel, a MongoDB-vel, az [Azure Cosmos DB]-vel, illetve olyan SaaS API-szolgáltatókkal, mint az Office 365 és a Salesforce.com.

* **Kapcsolat nélküli szinkronizálás –**: Az ügyfél SDK-k megkönnyítik hozhat létre hatékony és rugalmas mobilalkalmazások kapcsolat nélküli adatkészleteket. Az adatkészletek automatikusan, támogatott ütközésfeloldási megoldásokkal szinkronizálhatók a háttéradatokkal.

  Részletesebben is tájékozódhat [az adatokkal kapcsolatos funkciókról].

* **Leküldéses értesítések**: Az ügyfél SDK-k gond nélkül integrálhatók az Azure Notification Hubs regisztrációs szolgáltatásaival, leküldéses értesítéseket küldhet millió felhasználónak egyszerre.

  Részletesebben is tájékozódhat a [leküldéses értesítési szolgáltatásokról].

* **Ügyfél SDK-k**: Van egy teljes készletének ügyfél SDK-Ink választékából mind a natív fejlesztésekhez ([iOS], [Android], és [Windows]), platformfüggetlen fejlesztésekhez ([Xamarin.iOS és Xamarin.Android], [Xamarin.Forms]), és hibrid alkalmazásfejlesztésekhez ([Apache Cordova]). Minden ügyfél SDK MIT licenccel érhető el, és nyílt forráskódú.

## <a name="azure-app-service-features"></a>Azure App Service-szolgáltatások.
A mobilos webhelyek számára hasznos platformfunkciók a következők:

* **Az automatikus skálázás**: Az App Service-szel gyorsan vertikális és horizontális felskálázása a beérkező ügyfélterhelés kezelésére. A virtuális gépek száma és mérete manuálisan választható, illetve beállíthatja úgy az automatikus skálázást, hogy a mobil-háttéralkalmazást terhelés alapján vagy ütemezés szerint skálázza.

  További tudnivalók az [automatikus skálázásról].

* **Átmeneti környezetek**: App Service-ben több verziójának futtatására képes webhelyét, így is végezhet A / B tesztelést, éles üzemi környezetben, egy nagyobb fejlesztési és üzemeltetési terv részeként, valamint a történő előkészítését, egy új háttéralkalmazásnak.

  További tudnivalók az [átmeneti környezet] kapcsolatban.

* **Folyamatos üzembe helyezés**: Az App Service integrálható a közös _forráskezelési_ (SCM) rendszerekkel, így könnyedén telepítheti háttéralkalmazásának új verziója.

  Részletesebben is tájékozódhat a [telepítési lehetőségek](../app-service/deploy-local-git.md) kapcsolatban.

* **Virtuális hálózatkezelés**: Az App Service virtuális hálózaton, Azure ExpressRoute vagy hibrid kapcsolaton keresztül csatlakozhat a helyszíni erőforrásokhoz.

  Részletesebben is tájékozódhat a [hibrid kapcsolatok], a [virtuális hálózatokkal] és az [ExpressRoute]-tal kapcsolatban.

* **Elkülönített és dedikált környezetek**: Az Azure App Service-alkalmazások biztonságos futtatása az App Service egy teljesen elkülönített és dedikált környezetben futtathatja. Ez a környezet ideális a nagy skálázást, elkülönített vagy biztonságos hálózati hozzáférést igénylő alkalmazások és szolgáltatások számára.

  Részletesebben is tájékozódhat az [App Service-környezetek] kapcsolatban.

## <a name="next-steps"></a>További lépések

Az Azure App Service Mobile Apps szolgáltatásának megismeréséhez végezze el az [Első lépések] oktatóanyagot. Az oktatóanyagból megtanulhatja a mobil-háttéralkalmazások és az ügyfelek igény szerinti kialakításának alapjait. Emellett ismerteti a hitelesítés beépítésének, a kapcsolat nélküli szinkronizálásnak és a leküldéses értesítések küldésének részleteit is. Az oktatóanyag többször is elvégezhető, minden ügyfélalkalmazás esetében egyszer.

A Mobile Apps szolgáltatással kapcsolatos további információkért lásd a [tanulási térképet].
Az Azure App Service platformmal kapcsolatos további tudnivalókért lásd az [Azure App Service] szolgáltatást bemutató témakört.

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[első lépések]: app-service-mobile-ios-get-started.md
[Azure Table storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[hitelesítési szolgáltatásokról]: ./app-service-mobile-auth.md
[az adatokkal kapcsolatos funkciókról]: ./app-service-mobile-offline-data-sync.md
[leküldéses értesítési szolgáltatásokról]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS és Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatikus skálázásról]: ../app-service/web-sites-scale.md
[átmeneti környezet]: ../app-service/web-sites-staged-publishing.md
[hibrid kapcsolatok]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuális hálózatokkal]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-környezetek]: ../app-service/environment/intro.md
[tanulási térképet]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
