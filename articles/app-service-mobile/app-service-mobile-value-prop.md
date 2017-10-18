---
title: A Mobile Apps az Azure App Service-ben
description: "Ismerje meg, milyen előnyökkel jár az App Service szolgáltatás bevezetése vállalati mobilalkalmazásai számára."
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 8576f2b6adce044d2060c3ba6d19ac330e3637c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started"></a>A Mobile Apps az Azure App Service-ben
Az Azure App Service egy teljes körűen felügyelt [platformszolgáltatás](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) professzionális fejlesztők számára. A szolgáltatás számos lehetőséget kínál webes, mobil- és integrációs feladatokhoz. 

Az Azure App Service-ben elérhető Mobile Apps egy rugalmasan méretezhető, globálisan elérhető mobilalkalmazás-fejlesztési platformot biztosít a nagyvállalati fejlesztőknek és rendszerintegrátoroknak.

![A Mobile Apps képességeinek vizuális áttekintése](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>A Mobile Apps szolgáltatás előnyei
A Mobile Apps szolgáltatás a következő feladatok elvégzésére alkalmas:

* **Natív és platformfüggetlen alkalmazások fejlesztése** – Akár natív iOS-, Android- vagy Windows-alkalmazást, akár platformfüggetlen Xamarin- vagy Cordova- (Phonegap-) alkalmazást készít, az App Service jól használható natív SDK-kat kínál.
* **Csatlakozás vállalati rendszerekhez** – A Mobile Apps szolgáltatás segítségével percek alatt kialakíthat egy vállalati bejelentkezési felületet, és csatlakozhat a vállalat helyszíni vagy felhőalapú erőforrásaihoz.
* **Offline használatra kész alkalmazások fejlesztése adatszinkronizálással** – Mobil munkaerejét hatékonyabbá teheti olyan alkalmazások készítésével, amelyek offline is működnek, és internetkapcsolat esetén a Mobile Apps segítségével szinkronizálják az adatokat bármely vállalati adatforrással vagy szolgáltatott szoftver (SaaS) API-val.
* **Leküldéses értesítések küldése másodpercek alatt milliók számára** – Azonnali leküldéses értesítésekkel bármilyen eszközön, személyre szabottan és a megfelelő pillanatban léphet kapcsolatba ügyfeleivel.

## <a name="mobile-apps-features"></a>A Mobile Apps funkciói
A felhőalapú mobilalkalmazások fejlesztéséhez fontos funkciók a következők:

* **Hitelesítés és engedélyezés** – Folyamatosan bővülő identitásszolgáltatók listájából (az Azure Active Directory vállalati hitelesítési megoldását is beleértve), illetve olyan közösségi szolgáltatók közül választhat, mint a Facebook, a Google, a Twitter és a Microsoft-fiókok. A Mobile Apps minden szolgáltató számára OAuth 2.0 protokoll szerinti engedélyezést biztosít. Integrálhatja az identitásszolgáltatók különféle speciális funkciókat biztosító SDK-it is.

    Részletesebben is tájékozódhat [hitelesítési szolgáltatások] kapcsolatban.

* **Adatelérés** – A Mobile Apps egy mobilbarát, az Azure SQL Database-hez vagy egy helyszíni SQL-kiszolgálóhoz csatolt, OData v3 rendszerű adatforrást biztosít. Mivel a szolgáltatás alapjául entitás-keretrendszer is használható, könnyen integrálható más NoSQL- és SQL-adatszolgáltatókkal, köztük az [Azure Table Storage] rendszerrel, a MongoDB-vel, az [Azure Cosmos DB]-vel, illetve olyan SaaS API-szolgáltatókkal, mint az Office 365 és a Salesforce.com.

* **Kapcsolat nélküli szinkronizálás** – Ügyfél SDK-ink megkönnyítik a kapcsolat nélküli adatkészleteket használó, hatékony és rugalmas mobilalkalmazások készítését. Az adatkészletek automatikusan, támogatott ütközésfeloldási megoldásokkal szinkronizálhatók a háttéradatokkal.

  Részletesebben is tájékozódhat [az adatokkal kapcsolatos funkciókkal] kapcsolatban.

* **Leküldéses értesítések** – Ügyfél SDK-ink tökéletesen integrálhatók az Azure Notification Hubs regisztrációs szolgáltatásaival, lehetővé téve a leküldéses értesítések több millió felhasználónak egyszerre történő elküldését.

  Részletesebben is tájékozódhat [leküldéses értesítési szolgáltatások] kapcsolatban.

* **Ügyfél SDK-k** – Ügyfél SDK-ink választékából mind a natív fejlesztésekhez ([iOS], [Android] és [Windows]), mind a platformfüggetlen fejlesztésekhez ([Xamarin.iOS és Xamarin.Android], [Xamarin.Forms]) és hibrid alkalmazásfejlesztésekhez ([Apache Cordova]) talál megfelelőt. Minden ügyfél SDK MIT licenccel érhető el, és nyílt forráskódú.

## <a name="azure-app-service-features"></a>Azure App Service-szolgáltatások.
A mobilos webhelyek számára hasznos platformfunkciók a következők:

* **Automatikus skálázás** – Az App Service segítségével az ügyfelektől beérkező terhelés esetén gyors vertikális vagy horizontális felskálázást végezhet. A virtuális gépek száma és mérete manuálisan választható, illetve beállíthatja úgy az automatikus skálázást, hogy a mobil-háttéralkalmazást terhelés alapján vagy ütemezés szerint skálázza.

  További tudnivalók az [automatikus skálázásról].

* **Átmeneti környezet** – Az App Service egy webhely több verziójának futtatására képes, így lehetővé teszi A/B tesztek végrehajtását, éles üzemi környezetben, egy nagyobb fejlesztési és üzemeltetési terv részeként elvégzett tesztelést, illetve egy új háttéralkalmazásnak a saját helyén történő előkészítését.

  További tudnivalók az [átmeneti környezet] kapcsolatban.

* **Folyamatos telepítés** – Az App Service integrálható a gyakran használt ellátásilánc-kezelési (SCM) rendszerekkel, így automatikusan telepítheti háttéralkalmazásának új verzióit az SCM-rendszer egyik ágára való leküldés révén.

  Részletesebben is tájékozódhat a [telepítési lehetőségek](../app-service/app-service-deploy-local-git.md) kapcsolatban.

* **Virtuális hálózatkezelés** – Az App Service képes virtuális hálózaton, Azure ExpressRoute-on vagy hibrid kapcsolaton keresztül csatlakozni a helyi erőforrásokhoz.

  Részletesebben is tájékozódhat a [hibrid kapcsolatok], a [virtuális hálózatokkal] és az [ExpressRoute]-tal kapcsolatban.

* **Elkülönített és dedikált környezetek** – Az App Service egy teljesen elkülönített és dedikált környezetben futtatható, az Azure App Service-alkalmazások biztonságos, nagy méretekben történő futtatása érdekében. Ez a környezet ideális a nagy skálázást, elkülönített vagy biztonságos hálózati hozzáférést igénylő alkalmazások és szolgáltatások számára.

  Részletesebben is tájékozódhat az [App Service-környezetek] kapcsolatban.

## <a name="next-steps"></a>Következő lépések

Az Azure App Service Mobile Apps szolgáltatásának megismeréséhez végezze el az [Első lépések] oktatóanyagot. Az oktatóanyagból megtanulhatja a mobil-háttéralkalmazások és az ügyfelek igény szerinti kialakításának alapjait. Emellett ismerteti a hitelesítés beépítésének, a kapcsolat nélküli szinkronizálásnak és a leküldéses értesítések küldésének részleteit is. Az oktatóanyag többször is elvégezhető, minden ügyfélalkalmazás esetében egyszer.

A Mobile Apps szolgáltatással kapcsolatos további információkért lásd a [tanulási térképet].
Az Azure App Service platformmal kapcsolatos további tudnivalókért lásd az [Azure App Service] szolgáltatást bemutató témakört.

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Első lépések]: app-service-mobile-ios-get-started.md
[Azure Table Storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/documentdb-get-started.md
[hitelesítési szolgáltatások]: ./app-service-mobile-auth.md
[az adatokkal kapcsolatos funkciókkal]: ./app-service-mobile-offline-data-sync.md
[leküldéses értesítési szolgáltatások]: ../notification-hubs/notification-hubs-push-notification-overview.md
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
[tanulási térképet]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
