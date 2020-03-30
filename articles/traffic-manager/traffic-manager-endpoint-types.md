---
title: Traffic Manager végponttípusok | Microsoft dokumentumok
description: Ez a cikk az Azure Traffic Manager rel használható különböző típusú végpontokat ismerteti
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rohink
ms.openlocfilehash: 3d8f899a7899243129d31c2620a51dc764a8e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250932"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

A Microsoft Azure Traffic Manager lehetővé teszi, hogy szabályozhatja, hogyan hálózati forgalom van elosztva a különböző adatközpontokban futó alkalmazás-központi telepítések. Az alkalmazások üzembe helyezést mindig „végpontként” kell konfigurálnia a Traffic Managerben. Amikor a Traffic Manager DNS-kérést kap, kiválaszt egy elérhető végpontot, melyet visszaad a DNS-válaszban. A Traffic Manager a választást az aktuális végpont állapotára és a forgalom-útválasztási módra alapozza. További információt a [Traffic Manager működése című témakörben talál.](traffic-manager-how-it-works.md)

A Traffic Manager három típusú végpontot támogat:

* Az **Azure-végpontok** az Azure-ban üzemeltetett szolgáltatásokhoz használatosak.
* A **külső végpontok** IPv4/IPv6-címekhez, teljes tartománynevekhez vagy az Azure-on kívül üzemeltetett olyan szolgáltatásokhoz használhatók, amelyek lehetnek a helyszínen vagy más szolgáltatónál is.
* **Beágyazott végpontokkal** Traffic Manager-profilok kombinálhatók rugalmasabb útválasztási sémák létrehozásához a nagyobb, összetettebb üzemelő példányok igényeinek megfelelően.

Nincs korlátozás arra vonatkozóan, hogy a különböző típusú végpontok hogyan kombinálhatók egyetlen Traffic Manager-profilban. Minden profil a végpontok bármilyen kombinációját tartalmazhatja.

A következő szakaszok részletesebben ismertetik az egyes végponttípusokat.

## <a name="azure-endpoints"></a>Azure-végpontok

Az Azure-végpontok azure-alapú szolgáltatások a Traffic Manager. A következő Azure-erőforrástípusok támogatottak:

* PaaS felhőszolgáltatások.
* Web Apps
* Webapp-tárolóhelyek
* PublicIPAddress erőforrások (amely közvetlenül vagy egy Azure Load Balancer-en keresztül csatlakoztatható a virtuális gépekhez). A publicIpAddress címnek rendelkeznie kell egy Traffic Manager-profilban használandó DNS-névvel.

A PublicIPAddress erőforrások az Azure Resource Manager erőforrásai. Nem léteznek a klasszikus üzembe helyezési modellben. Így csak a Traffic Manager Azure Resource Manager-élményei támogatják őket. A többi végponttípusok az Erőforrás-kezelő és a klasszikus üzembe helyezési modell segítségével támogatottak.

Az Azure-végpontok használatakor a Traffic Manager észleli, ha egy webalkalmazás leáll, és elindult. Ez az állapot a végpont állapotában tükröződik. A [traffic manager végpontfigyelés](traffic-manager-monitoring.md#endpoint-and-profile-status) a részletekért. Az alapul szolgáló szolgáltatás leállításakor a Traffic Manager nem végez végpont állapot-ellenőrzéseket, és nem irányítja a forgalmat a végpontra. Nem történik a Traffic Manager számlázási esemény a leállított példányhoz. A szolgáltatás újraindításakor a számlázás folytatódik, és a végpont jogosult a forgalom fogadására. Ez az észlelés nem vonatkozik a PublicIpAddress végpontokra.

## <a name="external-endpoints"></a>Külső végpontok

A külső végpontok iPv4/IPv6-címekhez, teljes tartománynevekhez vagy az Azure-on kívüli szolgáltatásokhoz használatosak. Az IPv4/IPv6-címvégpontok használata lehetővé teszi a forgalomkezelő számára, hogy dns-név nélkül ellenőrizze a végpontok állapotát. Ennek eredményeképpen a Traffic Manager válaszolhat az A/AAAA rekordokkal kapcsolatos lekérdezésekre, amikor a végpontot egy válaszban adja vissza. Az Azure-on kívüli szolgáltatások tartalmazhatnak egy helyszíni vagy egy másik szolgáltatóval üzemeltetett szolgáltatást. A külső végpontok használhatók külön-külön vagy kombinálva azure-végpontok ugyanabban a Traffic Manager-profilban, kivéve az IPv4- vagy IPv6-címekként megadott végpontokat, amelyek csak külső végpontok lehetnek. Az Azure-végpontok külső végpontokkal való kombinálása különböző forgatókönyveket tesz lehetővé:

* Nagyobb redundancia biztosítása egy meglévő helyszíni alkalmazáshoz egy aktív-aktív vagy aktív-passzív feladatátvételi modellben az Azure használatával. 
* Irányítsa a forgalmat olyan végpontokra, amelyekhez nincs DNS-név társítva. Emellett csökkentse a DNS-keresési késés tanát azáltal, hogy nem kell egy második DNS-lekérdezést futtatnia a DNS-név IP-címének visszaolvasásához.
* Csökkentse az alkalmazások késését a felhasználók számára világszerte, kiterjesztheti a meglévő helyszíni alkalmazást az Azure további földrajzi helyekre. További információ: [Traffic Manager "Performance" forgalom útválasztás.](traffic-manager-routing-methods.md#performance)
* További kapacitásbiztosítása egy meglévő helyszíni alkalmazáshoz, akár folyamatosan, akár "burst-to-cloud" megoldásként, hogy megfeleljen az Azure-ban az igények megugrásának.

Bizonyos esetekben hasznos külső végpontok használatával hivatkozni az Azure-szolgáltatásokra (például a [GYIK- ben).](traffic-manager-faqs.md#traffic-manager-endpoints) Ebben az esetben az állapot-ellenőrzések számlázása az Azure-végpontok díja, nem a külső végpontok aránya. Azonban az Azure-végpontokkal ellentétben, ha leállítja vagy törli az alapul szolgáló szolgáltatást, állapot-ellenőrzés számlázás folytatódik, amíg le nem tiltja vagy törli a végpontot a Traffic Manager.

## <a name="nested-endpoints"></a>Egymásba ágyazott végpontok

A beágyazott végpontok több Traffic Manager-profilt kombinálnak a rugalmas forgalom-útválasztási sémák létrehozásához és a nagyobb, összetett telepítések igényeinek támogatásához. Beágyazott végpontok, a "gyermek" profil hozzáadása végpontként egy "szülő" profil. A gyermek- és szülőprofilok bármilyen típusú végpontot tartalmazhatnak, beleértve a többi beágyazott profilt is. További információt a [Beágyazott Traffic Manager-profilok című témakörben talál.](traffic-manager-nested-profiles.md)

## <a name="web-apps-as-endpoints"></a>Webalkalmazások végpontként

Néhány további szempont a WebApps végpontként való konfigurálásakor a Traffic Managerben:

1. Csak a "Standard" termékváltozatban vagy annál magasabb szinten lévő webalkalmazások használhatók a Traffic Manager rel. Az alacsonyabb termékváltozatú webalkalmazás hozzáadására tett kísérletek sikertelenek. Egy meglévő WebApp termékváltozatának visszaminősítése azt eredményezi, hogy a Traffic Manager már nem küld forgalmat az adott webalkalmazásnak. A támogatott csomagokról az [App Service-csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/) című témakörben talál további információt.
2. Amikor egy végpont HTTP-kérelmet kap, a kérelem "host" fejlécét használja annak meghatározására, hogy melyik webalkalmazásnak kell kiszolgálnia a kérést. Az állomásfejléc tartalmazza a kérés kezdeményezéséhez használt DNS-nevet, például "contosoapp.azurewebsites.net". Ha másik DNS-nevet szeretne használni a Web App alkalmazással, a DNS-nevet egyéni tartománynévként kell regisztrálniaz alkalmazáshoz. Amikor egy Web App-végpontot ad hozzá Azure-végpontként, a Traffic Manager profil DNS-neve automatikusan regisztrálva lesz az alkalmazáshoz. Ez a regisztráció automatikusan törlődik a végpont törlésekor.
3. Minden Traffic Manager-profil rendelkezhet legbőlegegy Web App-végpont minden Azure-régióból. A megkötés megkerüléséhez külső végpontként konfigurálhat egy webalkalmazást. További információt a [GYAKORI KÉRDÉSEK című témakörben talál.](traffic-manager-faqs.md#traffic-manager-endpoints)

## <a name="enabling-and-disabling-endpoints"></a>Végpontok engedélyezése és letiltása

Egy végpont letiltása a Traffic Manager hasznos lehet ideiglenesen távolítsa el a forgalmat egy végpont, amely karbantartási módban van, vagy újraüzembe helyezett. Ha a végpont újra fut, újra engedélyezhető.

A végpontok engedélyezhetők és letilthatók a Traffic Manager portálon, a PowerShellen, a CLI-n vagy a REST API-n keresztül.

> [!NOTE]
> Az Azure-végpont letiltásának semmi köze az Azure-beli üzembe helyezési állapotához. Egy Azure-szolgáltatás (például egy virtuális gép vagy webalkalmazás továbbra is fut, és képes fogadni a forgalmat akkor is, ha le van tiltva a Traffic Manager. A forgalom közvetlenül a szolgáltatáspéldányhoz címezhető, nem pedig a Traffic Manager profil DNS-nevén keresztül. További információt a [Traffic Manager működéséről](traffic-manager-how-it-works.md)talál.

Az egyes végpontok aktuális jogosultsága a forgalom fogadására a következő tényezőktől függ:

* A profil állapota (engedélyezve/letiltva)
* A végpont állapota (engedélyezve/letiltva)
* Az adott végpont állapotvizsgálatainak eredményei

További információt a [Traffic Manager végpontfigyelése](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Mivel a Traffic Manager a DNS szintjén működik, nem tudja befolyásolni a meglévő kapcsolatokat egyetlen végponthoz sem. Ha egy végpont nem érhető el, a Traffic Manager új kapcsolatokat irányít egy másik elérhető végpontra. A letiltott vagy nem megfelelő állapotú végpont mögötti állomás azonban továbbra is fogadhat forgalmat a meglévő kapcsolatokon keresztül, amíg ezek a munkamenetek le nem állnak. Az alkalmazásoknak korlátozniuk kell a munkamenet időtartamát, hogy a forgalom lemerüljön a meglévő kapcsolatokból.

Ha egy profil összes végpontja le van tiltva, vagy maga a profil le van tiltva, akkor a Traffic Manager "NXDOMAIN" választ küld egy új DNS-lekérdezésre.

## <a name="faqs"></a>Gyakori kérdések

* [Használhatom a Traffic Managert több előfizetésvégpontjával?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Használhatom a Traffic Managert a Cloud Service "Staging" bővítőhelyekkel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Támogatja a Traffic Manager az IPv6-végpontokat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Használhatom a Traffic Manager-t egynél több webalkalmazással ugyanabban a régióban?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Hogyan helyezhetem át a Traffic Manager-profil Azure-végpontjait egy másik erőforráscsoportba?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>További lépések

* További információ [a Traffic Manager működéséről.](traffic-manager-how-it-works.md)
* Információ a Traffic Manager [végpontfigyelésről és az automatikus feladatátvételről.](traffic-manager-monitoring.md)
* További információ a Traffic Manager [forgalomirányítási módszereiről.](traffic-manager-routing-methods.md)
