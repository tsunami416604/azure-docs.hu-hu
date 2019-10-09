---
title: A Mobile Apps az Azure App Service-ben
description: Ismerje meg, milyen előnyökkel jár az App Service szolgáltatás bevezetése vállalati mobilalkalmazásai számára.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 48f7066f2db96165daa104dcf32fe8e2e2cd55cf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025226"
---
# <a name="getting-started"></a>A Mobile Apps az Azure App Service-ben

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

Az Azure App Service egy teljes körűen felügyelt [platformszolgáltatás](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) professzionális fejlesztők számára. A szolgáltatás számos lehetőséget kínál webes, mobil- és integrációs feladatokhoz. 

Az Azure App Service-ben elérhető Mobile Apps egy rugalmasan méretezhető, globálisan elérhető mobilalkalmazás-fejlesztési platformot biztosít a nagyvállalati fejlesztőknek és rendszerintegrátoroknak.

![A Mobile Apps képességeinek vizuális áttekintése](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>A Mobile Apps szolgáltatás előnyei
A Mobile Apps szolgáltatás a következő feladatok elvégzésére alkalmas:

* **Natív és platformfüggetlen alkalmazások készítése**: Akár natív iOS-, Android-és Windows-alkalmazásokat, akár platformfüggetlen Xamarin-vagy Cordova-(telefon-) alkalmazásokat fejleszt, a natív SDK-k használatával kihasználhatja a App Service előnyeit.
* **Kapcsolódjon a vállalati rendszerekhez**: A Mobile Apps funkcióval percek alatt hozzáadhat vállalati bejelentkezést, és csatlakozhat a vállalati helyszíni vagy Felhőbeli erőforrásokhoz.
* **Offline használatra kész alkalmazások készítése az adatszinkronizálással**: A mobil munkafolyamatok hatékonyabbá tétele a kapcsolat nélküli üzemmódban működő alkalmazások létrehozásával, valamint a Mobile Apps segítségével szinkronizálhatja az adatait a háttérben, ha a kapcsolat a vállalati adatforrások vagy a szolgáltatott szoftverek (SaaS) API-k bármelyikével fennáll.
* **Leküldéses értesítések milliók számára másodpercek alatt**: Azonnali leküldéses értesítéseket küldhet ügyfeleinek bármilyen eszközön, személyre szabottan, és elküldheti őket, ha az idő helyes.

## <a name="mobile-apps-features"></a>A Mobile Apps funkciói
A felhőalapú mobilalkalmazások fejlesztéséhez fontos funkciók a következők:

* **Hitelesítés és engedélyezés**: Az identitás-szolgáltatók támogatása, többek között a vállalati hitelesítés Azure Active Directory, valamint a közösségi szolgáltatók, például a Facebook, a Google, a Twitter és a Microsoft-fiókok. A Mobile Apps minden szolgáltató számára OAuth 2.0 protokoll szerinti engedélyezést biztosít. Integrálhatja az identitásszolgáltatók különféle speciális funkciókat biztosító SDK-it is.

    Részletesebben is tájékozódhat a [hitelesítési szolgáltatásokról].

* **Adathozzáférés**: A Mobile Apps egy olyan mobil OData v3-adatforrást biztosít, amely Azure SQL Databasehoz vagy helyszíni SQL Serverhez van csatolva. Mivel a szolgáltatás alapjául entitás-keretrendszer is használható, könnyen integrálható más NoSQL- és SQL-adatszolgáltatókkal, köztük az [Azure Table Storage] rendszerrel, a MongoDB-vel, az [Azure Cosmos DB]-vel, illetve olyan SaaS API-szolgáltatókkal, mint az Office 365 és a Salesforce.com.

* **Offline szinkronizálás**: Az ügyfél SDK-k megkönnyítik a kapcsolat nélküli adatkészlettel működő robusztus és rugalmas mobil alkalmazások kiépítését. Az adatkészletek automatikusan, támogatott ütközésfeloldási megoldásokkal szinkronizálhatók a háttéradatokkal.

  Részletesebben is tájékozódhat [az adatokkal kapcsolatos funkciókról].

* **Leküldéses értesítések**: Az ügyfél SDK-k zökkenőmentesen integrálhatók az Azure Notification Hubs regisztrációs képességeivel, így a leküldéses értesítéseket egyszerre több millió felhasználónak is elküldheti.

  Részletesebben is tájékozódhat a [leküldéses értesítési szolgáltatásokról].

* **Ügyfél SDK**-k: Az ügyfél-SDK-k teljes készlete kiterjed a natív fejlesztésre ([iOS], [Android]és [Windows]), platformfüggetlen fejlesztésre ([Xamarin.iOS és Xamarin.Android], [Xamarin.Forms]) és hibrid alkalmazásokra fejlesztés ([Apache Cordova]). Minden ügyfél SDK MIT licenccel érhető el, és nyílt forráskódú.

## <a name="azure-app-service-features"></a>Azure App Service-szolgáltatások.
A mobilos webhelyek számára hasznos platformfunkciók a következők:

* Automatikus **skálázás**: A App Service segítségével gyorsan méretezheti vagy felskálázással kezelheti a bejövő ügyfelek terhelését. A virtuális gépek száma és mérete manuálisan választható, illetve beállíthatja úgy az automatikus skálázást, hogy a mobil-háttéralkalmazást terhelés alapján vagy ütemezés szerint skálázza.

  További tudnivalók az [automatikus skálázásról].

* **Átmeneti környezetek**: App Service a webhely több verzióját is futtathatja, így elvégezheti A/B tesztelést, az éles környezetben történő tesztelést egy nagyobb DevOps-csomag részeként, és egy új háttérrendszer-előkészítést is végezhet.

  További tudnivalók az [átmeneti környezet] kapcsolatban.

* **Folyamatos üzembe helyezés**: A App Service integrálható az általános _verziókövetés-kezelési_ (SCM) rendszerekkel, így könnyedén üzembe helyezheti a háttér új verzióját.

  Részletesebben is tájékozódhat a [telepítési lehetőségek](../app-service/deploy-local-git.md) kapcsolatban.

* **Virtuális hálózatkezelés**: App Service a virtuális hálózat, az Azure ExpressRoute vagy a hibrid kapcsolatok használatával tud csatlakozni a helyszíni erőforrásokhoz.

  Részletesebben is tájékozódhat a [hibrid kapcsolatok], a [virtuális hálózatokkal] és az [ExpressRoute]-tal kapcsolatban.

* **Elkülönített és dedikált környezetek**: Azure App Service alkalmazások biztonságos futtatásához teljes mértékben elkülönített és dedikált környezetben futtathatja App Service. Ez a környezet ideális a nagy skálázást, elkülönített vagy biztonságos hálózati hozzáférést igénylő alkalmazások és szolgáltatások számára.

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
[automatikus skálázásról]: ../app-service/manage-scale-up.md
[átmeneti környezet]: ../app-service/deploy-staging-slots.md
[hibrid kapcsolatok]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuális hálózatokkal]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-környezetek]: ../app-service/environment/intro.md
[tanulási térképet]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure App Service]: ../app-service/overview.md
