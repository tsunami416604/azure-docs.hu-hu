---
title: "Létrehozásával és a belső Terheléselosztók használatával az App Service-környezetek |} Microsoft Docs"
description: "Létrehozása és egy ASE használata egy ILB"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: f7c94b790c6aa7c75c62fd05671f016b7185b2a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>A belső Terheléselosztók használatával az App Service-környezet

> [!NOTE] 
> Ez a cikk az App Service Environment-környezet v1 tárgya. Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
>

Az App Service-környezet (ASE) funkció az Azure App Service egy továbbfejlesztett konfigurációs funkció, amely nem érhető el a több-bérlős bélyegzők letölti a Premium szolgáltatás lehetőség. A ASE funkció tulajdonképpen telepíti az Azure App Service szolgáltatásban az Azure virtuális Network(VNet). Ahhoz, hogy az App Service Environment-környezetek olvasási által biztosított képességeket megértése a [Mi az az App Service-környezetek] [ WhatisASE] dokumentációját. Ha nem ismeri a működő virtuális hálózat olvasható előnyeit a [Azure virtuális hálózat – gyakori kérdések][virtualnetwork]. 

## <a name="overview"></a>Áttekintés
Egy ASE is telepíthető, az interneten elérhető végpontok vagy a virtuális IP-címmel. Ahhoz, hogy az IP-cím beállítása kell telepítenie a ASE egy belső terheléselosztási Balancer(ILB) a VNet-címhez. Amikor a ASE egy ILB van beállítva, akkor adja meg:

* your own domain or subdomain. Könnyen, a jelen dokumentum céljából feltételezzük altartomány, de akár lehet állítani. 
* a HTTPS-hez használt tanúsítvány
* DNS management for your subdomain. 

Cserébe többek között az alábbiakat teheti meg:

* állomás intranetes alkalmazások, például az üzletági alkalmazások, biztonságosan a felhőben, amelyek egy helyet, hogy a hely vagy ExpressRoute VPN keresztül érhető el
* állomás alkalmazások a felhőben, amelyek nem szerepelnek a nyilvános DNS-kiszolgálók
* amellyel az előtér-alkalmazások biztonságosan integrálhatja elkülönített internet háttér-alkalmazásai létrehozására

#### <a name="disabled-functionality"></a>Letiltott funkciók
Néhány dolgot, amely egy ILB ASE használatakor nem hajtható végre. Ezeket a beállításokat a következők:

* IPSSL használatával
* IP-címek hozzárendelése a megadott alkalmazások
* vételi, és a portálon keresztül egy alkalmazás olyan tanúsítványt használ. Természetesen is közvetlenül a hitelesítésszolgáltatót a tanúsítványok beszerzése és használja azt az alkalmazásokat, de nem az Azure portálon keresztül.

## <a name="creating-an-ilb-ase"></a>Egy ILB ASE létrehozása
Egy ILB ASE létrehozása nincs hozzon létre egy ASE általában sokkal különböző. Egy ASE létrehozásával mélyebb tárgyalását lásd: [egy App Service Environment-környezet létrehozása][HowtoCreateASE]. Egy ILB ASE létrehozásának folyamata megegyezik egy virtuális hálózat létrehozása ASE létrehozása során, vagy jelöljön ki egy már meglévő virtuális hálózat között. Az ILB ASE létrehozása: 

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrás -> Web + mobil -> az App Service Environment-környezet**.
2. Válassza ki előfizetését.
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
4. Válasszon ki vagy hozzon létre egy virtuális hálózatot.
5. Hozzon létre egy alhálózatot, ha egy virtuális hálózat kiválasztása.
6. Válassza ki **virtuális hálózati/hely VNet konfigurációja ->** , és a VIP-típus belső.
7. Adja meg (Ez a név az e ASE létrehozott alkalmazások használt altartomány) altartomány nevét.
8. Válassza ki **OK** , majd **létrehozása**.

![][1]

A virtuális hálózati ablaktáblán van egy virtuális hálózat konfigurációs beállítás, amely lehetővé teszi, hogy egy külső VIP vagy VIP belső között válassza ki. Az alapértelmezett érték a külső. Ha van állítva, külső, a ASE használ az interneten elérhető VIP. Ha belső, a ASE egy ILB a Vneten belül egy IP-cím van konfigurálva. 

Miután kiválasztotta a belső, képes több IP-címek hozzáadása a ASE törlődik, és ehelyett meg kell adnia a hajlamosnak altartomány. Egy külső virtuális IP-címre-környezetben a ASE neve szerepel az altartomány adott ASE létrehozott alkalmazások. Ha a ASE neve ***contosotest*** és az alkalmazás az adott ASE nevű ***mytest***, az altartomány van formátumú ***contosotest.p.azurewebsites.net*** és az alkalmazás URL-je ***mytest.contosotest.p.azurewebsites.net***. A VIP-típus megadásával belső, a ÁZIS neve nem szolgál a altartományában a ASE. Az altartomány explicit módon adja meg. Ha a altartomány ***contoso.corp.net*** és abban a ASE nevű alkalmazás végrehajtott ***timereporting***, az alkalmazás URL-je ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Alkalmazás-Példánynak környezetben
Alkalmazás létrehozása-Példánynak környezetben megegyezik a általában létrehozása a egy app Service-környezetben. 

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrás -> Web + mobil -> webkiszolgáló** vagy **Mobile** vagy **API-alkalmazás**.
2. Adja meg az alkalmazás nevét.
3. Válassza ki előfizetését.
4. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
5. Válassza ki, vagy hozzon létre egy App Service Plan(ASP). Ha hoz létre egy új ASP, jelölje ki a ASE helyeként, és válassza ki az ASP a létrehozni kívánt feldolgozókészletek. Az ASP létrehozásakor kiválasztható a ASE a hely és a munkavégző készletét. Ha megadja az alkalmazás nevét, látni fogja, hogy az alkalmazás neve alatt altartomány helyébe a altartomány a ASE. 
6. Kattintson a **Létrehozás** gombra. Ügyeljen arra, hogy válassza ki a **rögzítés az irányítópulton** jelölőnégyzetet, ha azt szeretné, hogy az alkalmazás jelenik meg az irányítópulton. 

![][2]

Az alkalmazás neve alatt a altartománynév lekérdezi a frissített a hajlamosnak altartomány. 

## <a name="post-ilb-ase-creation-validation"></a>POST ILB ASE létrehozásának ellenőrzése
Az ILB ASE kissé különbözik az ILB nélküli ASE környezettől. Már nincs jelezve kell kezelni a saját DNS és a HTTPS-kapcsolatok adja meg a tanúsítvány van. 

Miután létrehozta a ASE, megfigyelheti, hogy a altartomány jeleníti meg az altartományt megadott, és egy új elemet a **beállítás** nevű menü **ILB tanúsítvány**. A ASE jön létre egy önaláírt tanúsítványt, így azokat könnyebben HTTPS teszteléséhez. A portál jelzi, hogy meg kell adnia a saját tanúsítványt a HTTPS-hez, ez azonban bátorítva, hogy a tanúsítványa van, a altartomány együtt. 

![][3]

Ha dolgot egyszerűen próbál, és nem tudja, hogyan hozható létre tanúsítvány, az IIS MMC konzol alkalmazás segítségével létrehozhat egy önaláírt tanúsítványt. A már létrehozott, egy .pfx fájlba exportálni, és majd töltse fel a ILB tanúsítvány felhasználói felületén. Amikor egy önaláírt tanúsítvánnyal biztonságos hely fér hozzá, a böngésző lehetővé teszi, hogy a hely elérésére nincs-e biztonságos miatt a nem tudja érvényesíteni a tanúsítványt a rendszer figyelmeztetést. Ha el szeretné kerülni, hogy figyelmeztetés, szüksége lesz egy megfelelően aláírt tanúsítványt, amely megfelel a altartomány, és nem ismeri fel a böngésző megbízhatósági láncot.

![][6]

Ha azt szeretné, próbálja meg a folyamatot a saját tanúsítványait, és a HTTP és HTTPS a hozzáférést a ASE teszteléséhez:

1. ASE UI ASE létrehozását követően navigáljon **ASE -> Beállítások -> ILB tanúsítványok**.
2. Állítsa be a ILB tanúsítvány tanúsítvány pfx-fájljának kiválasztásával, és adja meg a jelszót. Ebben a lépésben feldolgozni egy kis ideig tart, és a skálázási művelet folyamatban van, az üzenet jelenik meg.
3. A ILB cím lekérése a ASE (**ASE Tulajdonságok ->-a virtuális IP-cím >**).
4. A webalkalmazás létrehozása az ASE létrehozása után. 
5. Hozzon létre egy virtuális Gépet, ha még nem rendelkezik az adott virtuális hálózaton (nem található a ASE vagy dolgot break azonos alhálózaton).
6. Set DNS for your subdomain. Helyettesítő karakter használata a altartomány a DNS-ben, vagy szeretné egyszerű tesztek, ha a virtuális gép beállítása a webalkalmazás nevének VIP IP-címre a gazdafájl szerkesztése. Ha a ASE altartománynév. ilbase.com és a webes alkalmazás mytestapp történtek, így lenne figyelembe venni, mytestapp.ilbase.com, állítsa be, amely a hosts fájl. (A Windows, a hosts fájl jelenleg C:\Windows\System32\drivers\etc\)
7. Használja ezt a virtuális Gépet egy böngészőt, és navigáljon a http://mytestapp.ilbase.com (vagy bármilyen a webes alkalmazás neve a altartomány rendelkező).
8. A virtuális gép böngészőjével keresse fel a https://mytestapp.ilbase.com oldalt. Ha önaláírt tanúsítványt használ, el kell fogadnia a biztonsági hiánya. 

A Példánynak az IP-cím van megadva a tulajdonságai, a virtuális IP-címet.

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE használata
#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
Egy ILB ASE lehetővé teszi, hogy a hálózati elkülönítés az alkalmazásokhoz. Az alkalmazások nem elérhetők, és akkor is ismert által az interneten. Ezt a módszert használja az intranetes webhelyek, például az üzletági alkalmazások tárolásához kiváló. Ha még tovább hozzáférés korlátozása van szüksége, a hálózati szintű hozzáférés-vezérlésének továbbra is használhatja a hálózati biztonsági Groups(NSGs). 

Ha szeretné használni az NSG-k tovább korlátozhatja a hozzáférést, meg kell győződnie arról nem megszakítja a kommunikációt, amelyet a ASE működés érdekében. Annak ellenére, hogy a HTTP/HTTPS-hozzáférés csak a Példánynak a ASE által használt keresztül, a ASE továbbra is függ, a virtuális hálózaton kívüli erőforrásokhoz. Milyen hálózati hozzáférési továbbra is szükség, olvassa el [bejövő forgalom szabályozása az App Service-környezetek] [ ControlInbound] és [hálózati konfigurációs adatait az App Service-környezetek ExpressRoute][ExpressRoute]. 

Konfigurálja az NSG-k, ismernie kell a ASE kezelése az Azure-ban használt IP-címét. Az IP-címet esetén is a kimenő IP-címet a ASE internetes kérelmek teszi. A kimenő IP-címet a ASE marad a ASE élettartamára statikus. Ha töröl, és hozza létre újra a ASE, elérhetővé válik egy új IP-címet. Az IP-cím találja, **beállítások -> Tulajdonságok** keresse meg a **kimenő IP-cím**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Általános ILB ASE kezelése
Egy ILB ASE kezelése megegyezik nagyjából egy ASE általában kezelése. A feldolgozókészletek ismételt ASP és a megnövekedett mennyiségű HTTP/HTTPS-forgalom kezelésére előtér-kiszolgálók méretezése kell növelheti. A egy ASE konfigurációjának kezelésére általános információkért lásd: [az App Service-környezetek konfigurálása][ASEConfig]. 

A további felügyeleti cikkeket tanúsítványkezelés és a DNS-kezelés. Beszerzése és feltöltése ILB ASE létrehozása után a HTTPS-hez használt tanúsítvány és cserélje le az Érvényesség lejárata előtt. Azure az alap tartománnyal rendelkezik, mert az egy külső virtuális IP-címre ASEs nyújthat tanúsítványok. Mivel az egy ILB ASE által használt altartomány bármi lehet, a HTTPS-hez adja meg a saját tanúsítványt. 

#### <a name="dns-configuration"></a>DNS-konfiguráció
Ha egy külső VIP, a DNS Azure kezeli. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. For a given subdomain, such as contoso.corp.net, you must create DNS A records that point to your ILB address for:

    * 
    *.SCM ftp-közzététel 


## <a name="getting-started"></a>Első lépések
App Service Environment-környezetek megkezdéséhez, lásd: [App Service Environment-környezetek bemutatása][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
