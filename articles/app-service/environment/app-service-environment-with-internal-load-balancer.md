---
title: Létrehozásával és a egy belső terheléselosztó használata App Service-környezet |} A Microsoft Docs
description: Létrehozásával és a egy ILB ASE használata
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: fe447a655f579441be667d7555aa83faf2b9385d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957010"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Belső terheléselosztó használata App Service-környezet

> [!NOTE] 
> Ez a cikk az App Service Environment-környezet v1 szól. Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
>

Az App Service Environment (ASE) szolgáltatást az Azure App Service, amely egy továbbfejlesztett konfigurációs-képesség, amely nem érhető el a több-bérlős stampek biztosít prémium szintű szolgáltatás lehetőség. Az ASE funkció lényegében üzembe helyezi az Azure App Service az Azure virtuális hálózat (vnet). A részletesebb információhoz juthat az App Service Environment-környezetek olvasása által nyújtott képességeket a [Mi az App Service-környezet] [ WhatisASE] dokumentációját. Ha nem ismeri a működő virtuális hálózatok közötti olvasható előnyeit a [Azure virtuális hálózat – gyakori kérdések][virtualnetwork]. 

## <a name="overview"></a>Áttekintés
Az ASE is telepíthető, az internetről elérhető végpont vagy IP-címet a virtuális hálózatban található. Annak érdekében, hogy az IP-cím beállítása, üzembe kell helyeznie az ASE egy belső Load Balancer(ILB) a VNet-címre. Ha az ASE ilb van konfigurálva, akkor adja meg a következő műveleteket:

* your own domain or subdomain. Könnyen, a jelen dokumentum céljából feltételezzük altartomány, de mindkét módszer lehet állítani. 
* a HTTPS-hez használt tanúsítvány
* DNS management for your subdomain. 

Cserébe többek között az alábbiakat teheti meg:

* intranet-alkalmazásokat üzemeltethet, például az üzletági alkalmazások biztonságosan a felhőben, amelyek a helyet, hely- vagy ExpressRoute VPN használatával érhet el
* alkalmazásokat üzemeltethet a felhőben, amelyek nem szerepelnek a nyilvános DNS-kiszolgálók
* elkülönített internet háttérrendszer alkalmazásokat, amelyekkel az előtéri alkalmazások biztonságosan integrálható hozhat létre

#### <a name="disabled-functionality"></a>Letiltott funkciók
Néhány dolog, amely egy ILB ASE használata esetén nem hajtható végre. Ezeket a beállításokat a következők:

* IPSSL használatával
* IP-címek hozzárendelése meghatározott alkalmazások
* mentesülhet, és olyan tanúsítványt használ az alkalmazás a portálon keresztül. Természetesen továbbra is közvetlenül a hitelesítésszolgáltató tanúsítványokat szerezzenek be és használhatja az alkalmazásaihoz, de nem az Azure Portalon keresztül.

## <a name="creating-an-ilb-ase"></a>Az ILB ASE létrehozása
Az ILB ASE létrehozása eltér nem sokkal az ASE létrehozása általában. Az ASE létrehozásával alaposabb ismertetéséhez lásd: [App Service-környezet létrehozása][HowtoCreateASE]. Az ILB ASE létrehozásának folyamata megegyezik ASE létrehozása során egy virtuális hálózat létrehozása vagy kiválasztása egy már meglévő virtuális hálózat között. Az ILB ASE létrehozása: 

1. Az Azure Portalon válassza ki a **hozzon létre egy erőforrás -> Web + Mobile App Service Environment ->**.
2. Válassza ki előfizetését.
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
4. Válasszon ki vagy hozzon létre egy virtuális hálózatot.
5. Hozzon létre egy alhálózatot, ha a virtuális hálózat kiválasztása.
6. Válassza ki **-> virtuális hálózat/hely VNet-konfiguráció** , és állítsa a virtuális IP-cím típusa belső.
7. Adjon meg egy altartomány nevével együtt (Ez a név lesz a az ASE környezetben létrehozott alkalmazások használják az altartomány).
8. Válassza ki **OK** , majd **létrehozása**.

![][1]

A virtuális hálózat panelen nincs lehetőség, amely lehetővé teszi egy külső vagy belső virtuális IP-CÍMEK között válassza ki a VNet-konfiguráció. Az alapértelmezett érték a külső. Ha van állítva, a külső, az ASE használ az interneten elérhető VIP. Ha belső, az ASE ILB IP-címet a virtuális hálózaton belüli-vel van konfigurálva. 

Miután kiválasztotta a belső, lehetővé teszi, hogy további IP-címek az ASE-t, a rendszer eltávolítja, és ehelyett meg kell adnia az ASE az altartomány. Az ASE környezetben és a egy külső virtuális IP-cím az ASE neve szerepel az altartomány ASE környezetben létrehozott alkalmazások. Ha az ASE neve ***contosotest*** , és az alkalmazás az ASE neve ***mytest***, az altartomány van formátum ***contosotest.p.azurewebsites.net*** és, amelyet az alkalmazás URL-címe ***mytest.contosotest.p.azurewebsites.net***. Ha beállította a VIP típusa belső, az ASE neve nem szerepel az altartomány az ASE környezet. Az altartomány explicit módon adja meg. Ha az altartomány ***contoso.corp.net*** és abban, hogy az ASE nevű alkalmazás végzett ***timereporting***, amelyet az alkalmazás URL-címe ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Az ILB ASE alkalmazások
Alkalmazás létrehozása az ILB ASE környezetben ugyanaz, mint az alkalmazások általában az ASE környezetben létrehozásával. 

1. Az Azure Portalon válassza ki a **hozzon létre egy erőforrás -> Web + mobil -> Web** vagy **Mobile** vagy **API-alkalmazás**.
2. Adja meg az alkalmazás nevét.
3. Válassza ki előfizetését.
4. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
5. Válassza ki, vagy hozzon létre egy App Service Plan(ASP). Ha létrehoz egy új ASP, válassza az ASE Környezetet helyként, és válassza ki a feldolgozókészletet, azt szeretné, hogy az ASP kell létrehozni. Az ASP létrehozásakor válassza ki az ASE Környezetet helyként és feldolgozókészletként. Amikor megadja az alkalmazás nevét, látni fogja, hogy az alkalmazás-neve alatt az altartomány váltotta fel az altartomány az ASE. 
6. Kattintson a **Létrehozás** gombra. Ügyeljen arra, hogy válassza ki a **rögzítés az irányítópulton** jelölőnégyzetet, ha azt szeretné, hogy az alkalmazás az irányítópulton jelenik meg. 

![][2]

Az alkalmazás-neve alatt található az altartomány nevét az altartomány az ASE megfelelően frissül. 

## <a name="post-ilb-ase-creation-validation"></a>Közzététel ILB ASE létrehozásának érvényesítése
Az ILB ASE kissé különbözik az ILB nélküli ASE környezettől. Például már feljegyzett kell kezelnie a saját DNS- és is meg kell adnia a saját tanúsítványát a HTTPS-kapcsolatok. 

Az ASE létrehozását követően, megfigyelheti, hogy az altartomány jeleníti meg az altartomány megadott és a egy új elem a **beállítás** nevű menü **ILB-tanúsítvány**. Az ASE létrehozása egy önaláírt tanúsítványt, amely megkönnyíti a HTTPS tesztelését. A portál jelzi, hogy meg kell adnia a saját tanúsítványát a HTTPS, de azt javasoljuk, hogy rendelkezik egy tanúsítvánnyal, amely az altartomány együtt. 

![][3]

Ha dolgot egyszerűen próbálja ki, és nem tudom, hogyan hozzon létre egy tanúsítványt, az IIS MMC konzol alkalmazás használatával hozzon létre egy önaláírt tanúsítványt. A létrehozást követően egy .pfx fájlba exportálni, és ezután töltse fel az ILB-tanúsítvány felhasználói felületén. Amikor egy hely egy önaláírt tanúsítvánnyal biztosított fér hozzá, a böngésző lehetővé teszi egy figyelmeztetés, hogy a hely elérésére használata nem biztonságos miatt a tanúsítvány érvényesítése nem. Ha meg szeretné kerülni, hogy figyelmeztetés, szüksége lesz egy megfelelő aláírt tanúsítvány, amely megegyezik az altartomány, és ismeri fel a böngésző megbízhatósági lánca.

![][6]

Ha azt szeretné, próbálja ki a folyamatot a saját tanúsítványait, és tesztelje a HTTP és HTTPS-hozzáférést, az ASE:

1. ASE felhasználói felület ASE létrehozása után nyissa meg **ASE -> Beállítások -> ILB-tanúsítvány**.
2. Állítsa be az ILB-tanúsítvány pfx tanúsítványfájl kiválasztásával, és adja meg a jelszót. Ezt a lépést tart egy kis ideig feldolgozásához, és egy skálázási művelet van folyamatban az üzenet jelenik meg.
3. Az ASE ILB-címének lekéréséhez (**ASE -> Properties -> virtuális IP-cím**).
4. Webalkalmazás létrehozása az ASE létrehozását követően. 
5. Hozzon létre egy virtuális Gépet, ha nem rendelkezik ilyennel, az adott virtuális Hálózatban (nem az azonos alhálózatban az ASE vagy dolgot break).
6. Set DNS for your subdomain. Helyettesítő karakter használata a altartomány a DNS-ben, vagy ha azt szeretné, hogy pár egyszerű teszt végrehajtásához állítsa be a webalkalmazás-nevet a virtuális IP-címére a virtuális gép hosts fájljának szerkesztésével. Ha az ASE az altartomány nevével együtt. ilbase.com, és a web app mytestapp arról, hogy szeretne foglalkozni, mytestapp.ilbase.com, állítsa be, amely a hosts fájl. (A Windows, a gazdagépek fájl a _c C:\Windows\System32\drivers\etc\)
7. A virtuális gép böngészőt használ, és nyissa meg https://mytestapp.ilbase.com (vagy bármilyen a webalkalmazás neve az a altartomány van).
8. A virtuális gép böngészőjével keresse fel a következő oldalt: https://mytestapp.ilbase.com. Ha önaláírt tanúsítványt használ, el kell fogadnia a biztonsági hiányosságot. 

Az ILB IP-címét a virtuális IP-cím szerepel a tulajdonságai.

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE használata
#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
ILB ASE környezetben lehetővé teszi, hogy az alkalmazások a hálózatok elkülönítését. Az alkalmazások nincsenek elérhető vagy az interneten is ismert. Ez a megközelítés akkor kiváló intranethelyekkel, mint például az üzleti alkalmazások üzemeltetéséhez. Hozzáférés még tovább korlátozni kell, ha a hálózati szintű hozzáférés vezérléséhez továbbra is használhatja a hálózati biztonsági Groups(NSGs). 

Ha szeretné az NSG-k használata a hozzáférés további korlátozását, biztosítsa, nem hibásodik meg, amely az ASE van szüksége, hogy működik a kommunikáció. Annak ellenére, hogy a HTTP/HTTPS-hozzáférés csak az használják az ASE ILB keresztül, az ASE-t továbbra is függ a virtuális hálózaton kívüli erőforrásokhoz. Milyen hálózati hozzáférés továbbra is szükség, olvassa el [bejövő forgalom szabályozása az App Service Environment] [ ControlInbound] és [az App Service-környezetek hálózati konfigurációjának részletei Az ExpressRoute][ExpressRoute]. 

Az NSG-k konfigurálásához ismernie kell az ASE kezelése az Azure által használt IP-címét. Az IP-cím akkor is, a kimenő IP-címét az ASE Ha lehetővé teszi, internetes kérelmeket. A kimenő IP-cím, az ASE az ASE élettartamára statikus marad. Ha törli, majd hozza létre újból az ASE-t, az új IP-címet kap. Az IP-cím megkereséséhez nyissa meg **beállítások -> Tulajdonságok** , és keresse meg a **kimenő IP-cím**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Általános ILB ASE kezelése
Az ILB ASE kezelése megegyezik nagyrészt az ASE általában kezelése. A feldolgozókészletek ASP több példány üzemeltetéséhez és méretezéséhez nagyobb mennyiségű, HTTP/HTTPS-forgalom kezelésére az előtér-kiszolgálókat kell vertikális. Egy ASE környezethez a konfiguráció kezelésével általános információkért lásd: [App Service Environment konfigurálása][ASEConfig]. 

A további felügyeleti elemek tanúsítványkezelés és a DNS-kezelési rendszer. Be kell szerezniük, és az ILB ASE létrehozása után a HTTPS-hez használt tanúsítvány feltöltése és cserélje le a lejárata előtt. Az Azure az alap tartománnyal rendelkezik, mert azt egy külső virtuális IP-CÍMEK az ASE tanúsítványok biztosíthatja. Mivel az ILB ASE által használt altartomány bármi lehet, meg kell adnia a saját tanúsítvány HTTPS. 

#### <a name="dns-configuration"></a>DNS-konfiguráció
Egy külső virtuális IP-cím használata esetén az Azure kezeli a DNS-ben. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. For a given subdomain, such as contoso.corp.net, you must create DNS A records that point to your ILB address for:

    * 
    *.SCM ftp-közzététel 


## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [App Service Environment bemutatása][WhatisASE]

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
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
