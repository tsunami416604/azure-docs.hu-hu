---
title: A mobilalkalmazásokról
description: Ismerje meg, milyen előnyökkel jár az App Service szolgáltatás bevezetése vállalati mobilalkalmazásai számára.
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.tgt_pltfrm: mobile-multiple
ms.topic: overview
ms.date: 06/25/2019
ms.openlocfilehash: 33548f202046310b91fc79d38ac7d8fb18a8727e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499426"
---
# <a name="about-mobile-apps-in-azure-app-service"></a><a name="getting-started"> </a>A Mobile Apps az Azure App Service-ben

Az Azure App Service egy teljes körűen felügyelt [platformszolgáltatás](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) professzionális fejlesztők számára. A szolgáltatás számos lehetőséget kínál webes, mobil- és integrációs feladatokhoz. 

Az Azure App Service-ben elérhető Mobile Apps egy rugalmasan méretezhető, globálisan elérhető mobilalkalmazás-fejlesztési platformot biztosít a nagyvállalati fejlesztőknek és rendszerintegrátoroknak.

![A Mobile Apps képességeinek vizuális áttekintése](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>A Mobile Apps szolgáltatás előnyei
A Mobile Apps szolgáltatás a következő feladatok elvégzésére alkalmas:

* **Natív és platformfüggetlen alkalmazások fejlesztése** – Akár natív iOS-, Android- vagy Windows-alkalmazást, akár platformfüggetlen Xamarin- vagy Cordova- (Phonegap-) alkalmazást készít, az App Service jól használható natív SDK-kat kínál.
* **Csatlakozás vállalati rendszerekhez** – A Mobile Apps szolgáltatás segítségével percek alatt kialakíthat egy vállalati bejelentkezési felületet, és csatlakozhat a vállalat helyszíni vagy felhőalapú erőforrásaihoz.
* **Offline használatra kész alkalmazások fejlesztése adatszinkronizálással** – Mobil munkaerejét hatékonyabbá teheti olyan alkalmazások készítésével, amelyek offline is működnek, és internetkapcsolat esetén a Mobile Apps segítségével szinkronizálják az adatokat bármely vállalati adatforrással vagy szolgáltatott szoftver (SaaS) API-val.
* **Leküldéses értesítések milliókhoz másodpercben:** Azonnali leküldéses értesítésekkel támadhatja meg ügyfeleit bármilyen eszközön, az igényeiknek megfelelően, és akkor küldhető el, amikor eljön az ideje.

## <a name="mobile-apps-features"></a>A Mobile Apps funkciói
A felhőalapú mobilalkalmazások fejlesztéséhez fontos funkciók a következők:

* **Hitelesítés és engedélyezés** – Az identitásszolgáltatók (az Azure Active Directory vállalati hitelesítési megoldását is beleértve), illetve olyan közösségi szolgáltatók támogatása, mint a Facebook, a Google, a Twitter és a Microsoft-fiókok. A Mobile Apps minden szolgáltató számára OAuth 2.0 protokoll szerinti engedélyezést biztosít. Integrálhatja az identitásszolgáltatók különféle speciális funkciókat biztosító SDK-it is.

    Részletesebben is tájékozódhat a [hitelesítési szolgáltatásokról].

* **Adatelérés** – A Mobile Apps egy mobilbarát, az Azure SQL Database-hez vagy egy helyszíni SQL-kiszolgálóhoz csatolt, OData v3 rendszerű adatforrást biztosít. Mivel a szolgáltatás alapjául entitás-keretrendszer is használható, könnyen integrálható más NoSQL- és SQL-adatszolgáltatókkal, köztük az [Azure Table Storage] rendszerrel, a MongoDB-vel, az [Azure Cosmos DB]-vel, illetve olyan SaaS API-szolgáltatókkal, mint az Office 365 és a Salesforce.com.

* **Kapcsolat nélküli szinkronizálás** – Az ügyfél SDK-k megkönnyítik a kapcsolat nélküli adathalmazokat használó, hatékony és rugalmas mobilalkalmazások készítését. Az adatkészletek automatikusan, támogatott ütközésfeloldási megoldásokkal szinkronizálhatók a háttéradatokkal.

  Részletesebben is tájékozódhat [az adatokkal kapcsolatos funkciókról].

* **Leküldéses értesítések** – Az ügyfél SDK-k tökéletesen integrálhatók az Azure Notification Hubs regisztrációs szolgáltatásaival, így több millió felhasználónak egyszerre küldhetők leküldéses értesítések.

  Részletesebben is tájékozódhat a [leküldéses értesítési szolgáltatásokról].

* **Ügyfél SDK-k** – Ügyfél SDK-ink választékából mind a natív fejlesztésekhez ([iOS], [Android] és [Windows]), mind a platformfüggetlen fejlesztésekhez ([Xamarin.iOS, Xamarin.Android], [Xamarin.Forms]) és hibrid alkalmazásfejlesztésekhez ([Apache Cordova]) talál megfelelőt. Minden ügyfél SDK MIT licenccel érhető el, és nyílt forráskódú.

## <a name="azure-app-service-features"></a>Azure App Service-szolgáltatások.
A mobilos webhelyek számára hasznos platformfunkciók a következők:

* **Automatikus skálázás** – Az App Service segítségével az ügyfelektől beérkező terhelés esetén gyors vertikális vagy horizontális felskálázást végezhet. A virtuális gépek száma és mérete manuálisan választható, illetve beállíthatja úgy az automatikus skálázást, hogy a mobil-háttéralkalmazást terhelés alapján vagy ütemezés szerint skálázza.

  További tudnivalók az [automatikus skálázásról].

* **Átmeneti környezet** – Az App Service egy webhely több verziójának futtatására képes, így lehetővé teszi A/B tesztek végrehajtását, éles üzemi környezetben, egy nagyobb fejlesztési és üzemeltetési terv részeként elvégzett tesztelést, illetve egy új háttéralkalmazásnak a saját helyén történő előkészítését.

  További tudnivalók az [átmeneti környezet] kapcsolatban.

* **Folyamatos üzembe helyezés** – Az App Service integrálható a gyakran használt _forráskezelési_ (SCM) rendszerekkel, így könnyedén üzembe helyezheti háttéralkalmazásának új verzióit.

  Részletesebben is tájékozódhat a [telepítési lehetőségek](../app-service/deploy-local-git.md) kapcsolatban.

* **Virtuális hálózatkezelés** – Az App Service képes virtuális hálózaton, Azure ExpressRoute-on vagy hibrid kapcsolaton keresztül csatlakozni a helyi erőforrásokhoz.

  Részletesebben is tájékozódhat a [hibrid kapcsolatok], a [virtuális hálózatokkal] és az [ExpressRoute]-tal kapcsolatban.

* **Elkülönített és dedikált környezetek** – Az Azure App Service-alkalmazások biztonságos futtatása érdekében az App Service egy teljesen elkülönített, dedikált környezetben futtatható. Ez a környezet ideális a nagy skálázást, elkülönített vagy biztonságos hálózati hozzáférést igénylő alkalmazások és szolgáltatások számára.

  További információk az [App Service-környezetekről.]

## <a name="next-steps"></a>További lépések

Az Azure App Service Mobile Apps szolgáltatásának megismeréséhez végezze el az [Első lépések] oktatóanyagot. Az oktatóanyagból megtanulhatja a mobil-háttéralkalmazások és az ügyfelek igény szerinti kialakításának alapjait. Emellett ismerteti a hitelesítés beépítésének, a kapcsolat nélküli szinkronizálásnak és a leküldéses értesítések küldésének részleteit is. Az oktatóanyag többször is elvégezhető, minden ügyfélalkalmazás esetében egyszer.

A Mobile Apps szolgáltatással kapcsolatos további információkért lásd a [tanulási térképet].
Az Azure App Service platformról az [Azure App Service című]témakörben talál további információt.

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Kezdetekhez]: app-service-mobile-ios-get-started.md
[Azure Table storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[hitelesítési szolgáltatások]: ./app-service-mobile-auth.md
[adatokkal kapcsolatos funkciók]: ./app-service-mobile-offline-data-sync.md
[leküldéses értesítési szolgáltatások]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS és Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatikus méretezés]: ../app-service/manage-scale-up.md
[átmeneti környezetek]: ../app-service/deploy-staging-slots.md
[hibrid kapcsolatok]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuális hálózatok]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-környezetek]: ../app-service/environment/intro.md
[tanulási térkép]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure App Service]: ../app-service/overview.md
