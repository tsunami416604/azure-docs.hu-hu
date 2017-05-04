---
title: "Mi a Mobile Apps szolgáltatás?"
description: "Ismerje meg, milyen előnyökkel jár az App Service szolgáltatás bevezetése vállalati mobilalkalmazásai számára."
services: app-service\mobile
documentationcenter: 
author: adrianhall
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 8ce0a0e7b06f85090c07052056ddd67b97b2ee8b
ms.lasthandoff: 04/29/2017


---
# <a name="getting-started"> </a>Mi a Mobile Apps szolgáltatás?
Az Azure App Service teljes körűen felügyelt, professzionális fejlesztők számára készült [platformszolgáltatás](https://azure.microsoft.com/overview/what-is-paas/), amely számos lehetőséget kínál webes, mobil- és integrációs feladatokhoz. Az *Azure App Service* *Mobile Apps* szolgáltatása jól skálázható, világszerte elérhető, vállalati fejlesztők és rendszerintegrátorok számára készült mobilalkalmazás-fejlesztő platform, amely számos lehetőséget kínál a mobilos fejlesztésekhez.

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>A Mobile Apps szolgáltatás előnyei
Az *Azure App Service* *Mobile Apps* szolgáltatása jól skálázható, világszerte elérhető, vállalati fejlesztők és rendszerintegrátorok számára készült mobilalkalmazás-fejlesztő platform, amely számos lehetőséget kínál a mobilos fejlesztésekhez. A Mobile Apps szolgáltatás a következőkre ad lehetőséget:

* **Natív és platformfüggetlen alkalmazások fejlesztése** – Akár natív iOS-, Android- vagy Windows-alkalmazást, akár platformfüggetlen Xamarin- vagy Cordova- (Phonegap-) alkalmazást készít, az App Service jól használható natív SDK-kat kínál.
* **Csatlakozás vállalati rendszerekhez** – A Mobile Apps segítségével percek alatt kialakíthat egy vállalati bejelentkezési felületet, és csatlakozhat a vállalat helyszíni vagy felhőalapú erőforrásaihoz.
* **Offline használatra kész alkalmazások fejlesztése adatszinkronizálással** – Mobil munkaerejét hatékonyabbá teheti olyan alkalmazások készítésével, amelyek offline is működnek, és internetkapcsolat esetén a Mobile Apps segítségével szinkronizálják az adatokat bármely vállalati adatforrással vagy SaaS API-val.
* **Leküldéses értesítések küldése másodpercek alatt milliók számára** – Azonnali leküldéses értesítésekkel bármilyen eszközön, személyre szabottan és a megfelelő pillanatban léphet kapcsolatba ügyfeleivel.

## <a name="mobile-app-features"></a>A Mobile Apps funkciói
A felhőalapú mobilalkalmazások fejlesztéséhez fontos funkciók a következők:

* **Hitelesítés és engedélyezés** – Folyamatosan bővülő identitásszolgáltatók listájából, az Azure Active Directory vállalati hitelesítési megoldását is beleértve, illetve olyan közösségi szolgáltatók közül választhat, mint a Facebook, a Google, a Twitter és a Microsoft-fiók.  Az Azure Mobile Apps minden szolgáltató számára OAuth 2.0 protokoll szerinti engedélyezést biztosít.  Integrálhatja az identitásszolgáltatók különféle speciális funkciókat biztosító SDK-it is.
  
  Részletesebben is tájékozódhat [hitelesítési szolgáltatások] kapcsolatban.
* **Adatelérés** – Az Azure Mobile Apps mobilbarát, az SQL Azure-hoz vagy egy helyi SQL-kiszolgálóhoz csatolt, OData v3 rendszerű adatforrást biztosít.  A szolgáltatás alapjául entitás-keretrendszer is használható, így könnyen integrálhatja más NoSQL- és SQL-adatszolgáltatókkal, az [Azure Table Storage], a MongoDB, a [DocumentDB], illetve az olyan SaaS API-szolgáltatókat is beleértve, mint az Office 365 és a Salesforce.com.
* **Kapcsolat nélküli szinkronizálás** – Ügyfél SDK-ink megkönnyítik a kapcsolat nélküli adatkészleteket használó, hatékony és rugalmas mobilalkalmazások készítését, melyek automatikusan, támogatott ütközésfeloldási megoldásokkal szinkronizálhatók a háttéradatokkal.
  
  Részletesebben is tájékozódhat [az adatokkal kapcsolatos funkciókkal] kapcsolatban.
* **Leküldéses értesítések** – Ügyfél SDK-ink tökéletesen integrálhatók az Azure Notification Hubs regisztrációs szolgáltatásaival, lehetővé téve a leküldéses értesítések több millió felhasználónak egyszerre történő elküldését.
  
  Részletesebben is tájékozódhat [leküldéses értesítési szolgáltatások] kapcsolatban.
* **Ügyfél SDK-k** – Ügyfél SDK-ink választékából mind a natív fejlesztésekhez ([iOS], [Android] és [Windows]), mind a platformfüggetlen fejlesztésekhez ([Xamarin iOS és Android rendszerhez], [Xamarin Forms]), illetve a hibrid alkalmazásfejlesztésekhez ([Apache Cordova]) is talál megfelelőt.  Minden ügyfél SDK MIT licenccel érhető el, és nyílt forráskódú.

## <a name="azure-app-service-features"></a>Azure App Service-szolgáltatások.
A mobilos webhelyek számára általában hasznos platformfunkciók a következők.

* **automatikus skálázással** – Az App Service bármilyen beérkező terhelés esetén gyors vertikális vagy horizontális skálázást tesz lehetővé. A virtuális gépek száma és mérete manuálisan választható, illetve beállíthatja úgy az automatikus skálázást, hogy a mobil-háttéralkalmazást terhelés alapján vagy ütemezés szerint skálázza.
  
  Részletesebben is tájékozódhat az [automatikus skálázással] kapcsolatban.
* **Átmeneti környezet** – Az App Service egy webhely több verziójának futtatására képes, így lehetővé teszi A/B tesztek végrehajtását, éles üzemi környezetben, egy nagyobb fejlesztési és üzemeltetési terv részeként elvégzett tesztelést, illetve egy új háttéralkalmazásnak a saját helyén történő előkészítését.
  
  További tudnivalók az [átmeneti környezet] kapcsolatban.
* **Folyamatos telepítés** – Az App Service integrálható a gyakran használt SCM-rendszerekkel, így automatikusan telepítheti háttéralkalmazásának új verzióit az SCM-rendszer egyik ágára való leküldés révén.
  
  Részletesebben is tájékozódhat a [telepítési lehetőségek] kapcsolatban.
* **Virtuális hálózatkezelés** – Az App Service képes virtuális hálózaton, ExpressRoute-on vagy hibrid kapcsolaton keresztül csatlakozni a helyi erőforrásokhoz.
  
  Részletesebben is tájékozódhat a [hibrid kapcsolatok], a [virtuális hálózatokkal] és az [ExpressRoute]-tal kapcsolatban.
* **Elkülönített/dedikált környezetek** – Az App Service képes az Azure App Service-alkalmazások teljesen elkülönített és dedikált környezetben történő biztonságos futtatására, nagy méretekben.  Ez ideális a rendkívül nagy skálázást, elkülönített vagy biztonságos hálózati hozzáférést igénylő alkalmazások és szolgáltatások számára.
  
  Részletesebben is tájékozódhat az [App Service-környezetek] kapcsolatban.

## <a name="getting-started"></a>Első lépések
A Mobile Apps szolgáltatás használatával az [Első lépések] oktatóanyag segítségével ismerkedhet meg.  Ebből megtanulhatja a mobil-háttéralkalmazások és az ügyfelek igény szerinti kialakításának, a hitelesítés beépítésének, a kapcsolat nélküli szinkronizálásnak és a leküldéses értesítések küldésének alapjait.  Az [Első lépések] oktatóanyag többször is – minden ügyfélalkalmazásnál elvégezhető.

Az Azure Mobile Apps szolgáltatással kapcsolatos további információkért tekintse át [tanulási térkép].
Az Azure App Service platformmal kapcsolatos további tudnivalókért lásd az [Azure App Service] szolgáltatást bemutató témakört.

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/mobile/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Első lépések]: app-service-mobile-ios-get-started.md
[Azure Table Storage]: ../storage/storage-dotnet-how-to-use-tables.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[hitelesítési szolgáltatások]: ./app-service-mobile-auth.md
[az adatokkal kapcsolatos funkciókkal]: ./app-service-mobile-offline-data-sync.md
[leküldéses értesítési szolgáltatások]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin iOS és Android rendszerhez]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatikus skálázással]: ../app-service-web/web-sites-scale.md
[átmeneti környezet]: ../app-service-web/web-sites-staged-publishing.md
[telepítési lehetőségek]: ../app-service-web/web-sites-deploy.md
[hibrid kapcsolatok]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[virtuális hálózatokkal]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-környezetek]: ../app-service-web/app-service-app-service-environment-intro.md
[tanulási térkép]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/

