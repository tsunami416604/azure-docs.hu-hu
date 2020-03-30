---
title: ILB ASE 1-es eszköz létrehozása
description: AsE létrehozása és használata ILB-vel. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294723"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Belső terheléselosztó használata App Service-környezettel

> [!NOTE] 
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól. Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).
>

Az App Service Environment (ASE) szolgáltatás az Azure App Service prémium szolgáltatása, amely olyan továbbfejlesztett konfigurációs képességet biztosít, amely nem érhető el a több-bérlős bélyegzőkben. Az ASE szolgáltatás lényegében telepíti az Azure App Service az Azure virtuális hálózat (Virtuális hálózat). Az App Service-környezetek által kínált képességek jobb megértéséhez olvassa el a [Mi az App Service-környezet][WhatisASE] dokumentációját. Ha nem ismeri a virtuális hálózatban való működés előnyeit, olvassa el az [Azure virtuális hálózattal kapcsolatos gyakori kérdéseket.][virtualnetwork] 

## <a name="overview"></a>Áttekintés
Az ASE telepíthető egy internetről elérhető végpont, vagy egy IP-címet a virtuális hálózatban. Annak érdekében, hogy az IP-címet egy virtuális hálózat címére állítsa be, az ASE-t belső terheléselosztóval(ILB) kell üzembe helyeznie. Ha az ASE-t ILB-vel konfigurálta, a következőket adja meg:

* saját tartományát vagy altartományát. A könnyebbik egyszerűség érdekében ez a dokumentum altartományt feltételez, de mindkét módon konfigurálhatja. 
* a HTTPS-hez használt tanúsítvány
* AZ altartomány DNS-kezelése. 

Cserébe többek között az alábbiakat teheti meg:

* intranetes alkalmazások, például üzletági alkalmazások biztonságos üzemeltetése a webhelyen keresztül a webhelyről a webhelyre vagy az ExpressRoute VPN-re keresztül elérendő felhőben
* olyan gazdaalkalmazások a felhőben, amelyek nem szerepelnek a nyilvános DNS-kiszolgálókon
* olyan, internetalapú háttéralkalmazásokat hozhat létre, amelyekkel az előtér-alkalmazások biztonságosan integrálhatók

#### <a name="disabled-functionality"></a>Letiltott funkciók
Vannak dolgok, amelyeket nem tehet meg, ha ilb ASE-t használ. Ezek a dolgok a következők:

* IPSSL használatával
* IP-címek hozzárendelése adott alkalmazásokhoz
* tanúsítvány vásárlása és használata egy alkalmazással a portálon keresztül. Természetesen továbbra is beszerezheti a tanúsítványokat közvetlenül a hitelesítésszolgáltatónál, és használhatja az okat az alkalmazásokkal, de nem az Azure Portalon keresztül.

## <a name="creating-an-ilb-ase"></a>ILB ASE létrehozása
Az ILB ASE létrehozása nem sokban különbözik az ASE létrehozásától. Az ASE létrehozásáról az [App Service-környezet létrehozása][HowtoCreateASE]című témakörben nyújt mélyebb vitát. Az ILB ASE létrehozásának folyamata megegyezik a virtuális hálózat létrehozása az ASE létrehozása során vagy egy már meglévő virtuális hálózat kiválasztása között. Az ILB ASE létrehozása: 

1. Az Azure Portalon válassza az **Erőforrás létrehozása -> Web + Mobile -> App Service Environment**lehetőséget.
2. Válassza ki előfizetését.
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
4. Válasszon ki vagy hozzon létre egy virtuális hálózatot.
5. Hozzon létre egy alhálózatot, ha virtuális hálózatot jelöl ki.
6. Válassza a **Virtuális hálózat/hely -> virtuális hálózat konfigurációja lehetőséget,** és állítsa a VIP-típust belsőre.
7. Adjon meg egy altartománynevet (ez a név az ASE-ben létrehozott alkalmazások altartománya).
8. Válassza **az OK gombot,** majd **a Létrehozást**lehetőséget.

![][1]

A virtuális hálózat ablaktáblán van egy virtuális hálózat konfigurációja lehetőség, amely lehetővé teszi, hogy válasszon egy külső VIP vagy belső VIP között. Az alapértelmezett érték a Külső. Ha külső re van állítva, az ASE egy internetre elérhető VIP-t használ. Ha a Belső lehetőséget választja, az ASE ILB-vel vagy virtuális hálózati IP-címmel lesz konfigurálva. 

A Belső kiválasztása után az ASE további IP-címek hozzáadásának lehetősége törlődik, és ehelyett meg kell adnia az ASE altartományát. Egy külső VIP-vel rendelkező ASE-ben az ASE nevét használja az altartományban az adott ASE-ben létrehozott alkalmazások. Ha az ASE neve ***contosotest,*** és az alkalmazás, hogy az ASE neve ***mytest,*** az altartomány a formátum ***contosotest.p.azurewebsites.net*** és az URL-t, hogy az alkalmazás ***mytest.contosotest.p.azurewebsites.net.*** Ha a VIP-típus belső, az ASE-név nem használja az ase altartományban. Az altartományt explicit módon kell megadni. Ha az altartomány ***contoso.corp.net,*** és az ASE nevű ***timereporting***nevű alkalmazásában készített egy alkalmazást, az alkalmazás URL-címe ***timereporting.contoso.corp.net.***

## <a name="apps-in-an-ilb-ase"></a>Alkalmazások az ILB ASE-ben
Egy alkalmazás létrehozása egy ILB ASE ugyanaz, mint egy alkalmazás létrehozása az ASE-ben általában. 

1. Az Azure Portalon válassza az **Erőforrás létrehozása -> Web + Mobile -> Web** vagy **Mobile** vagy **API App**lehetőséget.
2. Adja meg az alkalmazás nevét.
3. Válassza ki előfizetését.
4. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
5. Válasszon vagy hozzon létre egy App Service-csomag (ASP). Új ASP létrehozásakor válassza ki az ASE-t helyként, és válassza ki azt a munkavégző készletet, amelyben létre szeretné hozni az ASP-t. Az ASP létrehozásakor válassza ki az ASE-t helyként és a munkavégző készletként. Amikor megadja az alkalmazás nevét, látni fogja, hogy az alkalmazás neve alatti altartományt az ASE altartománya váltja fel. 
6. Kattintson a **Létrehozás** gombra. Ügyeljen arra, hogy jelölje be a **Pin to dashboard** jelölőnégyzetet, ha azt szeretné, hogy az alkalmazás megjelenjen az irányítópulton. 

![][2]

Az alkalmazás neve alatt az altartomány neve frissül, hogy tükrözze az ASE altartományát. 

## <a name="post-ilb-ase-creation-validation"></a>Az ILB ASE létrehozása utáni ellenőrzés
Az ILB ASE kissé különbözik az ILB nélküli ASE környezettől. Mint már említettük, saját DNS-t kell kezelnie, és meg kell adnia a saját tanúsítványát a HTTPS-kapcsolatokhoz. 

Az ASE létrehozása után észre fogja venni, hogy az altartomány a megadott altartományt jeleníti meg, és a **Beállítás** menüben van egy új elem, az **ILB tanúsítvány**. Az ASE egy önaláírt tanúsítvánnyal jön létre, amely megkönnyíti a HTTPS tesztelését. A portál azt mondja, hogy meg kell adnia a saját tanúsítványhttps, de ez arra ösztönzi, hogy egy tanúsítványt, amely megy az altartományhoz. 

![][3]

Ha egyszerűen csak kipróbálja a dolgokat, és nem tudja, hogyan hozhat létre tanúsítványt, az IIS MMC konzolalkalmazássegítségével önaláírt tanúsítványt hozhat létre. Létrehozása után .pfx fájlként exportálhatja, majd feltöltheti az ILB tanúsítvány felhasználói felületére. Ha önaláírt tanúsítvánnyal védett webhelyet használ, a böngésző figyelmeztetést ad arról, hogy a hozzáféréssel rendelkező webhely nem biztonságos, mivel nem tudja érvényesíteni a tanúsítványt. Ha el szeretné kerülni ezt a figyelmeztetést, olyan megfelelően aláírt tanúsítványra van szüksége, amely megfelel az altartománynak, és a böngésző által felismert megbízhatósági lánccal rendelkezik.

![][6]

Ha saját tanúsítványokkal szeretné kipróbálni a folyamatot, és http- és HTTPS-hozzáférést is tesztelni az ASE-hez:

1. Az ASE-felhasználói felületre az ASE létrehozása után **> Beállítások -> ILB-tanúsítványok**.
2. Állítsa be az ILB-tanúsítványt a tanúsítvány pfx fájljának kiválasztásával és a jelszó megadásával. Ez a lépés feldolgozása egy kis időt vesz igénybe, és megjelenik az üzenet, hogy egy skálázási művelet folyamatban van.
3. Az ASE **(ASE -> tulajdonság -> virtuális IP-cím) ILB-címének beszereznie.**
4. Hozzon létre egy webalkalmazást az ASE-ben a létrehozás után. 
5. Hozzon létre egy virtuális gép, ha nem rendelkezik ilyen virtuális hálózat (Nem ugyanabban az alhálózatban, mint az ASE vagy a dolgok szünet).
6. Állítsa be a DNS-t az altartományhoz. Használhatja a helyettesítő az altartomány a DNS-ben, vagy ha szeretne néhány egyszerű tesztet, szerkesztheti a hosts fájlt a virtuális gép beállítani webapp nevét VIP IP-cím. Ha az ASE volt az aldomain neve .ilbase.com, és tette a web app mytestapp úgy, hogy lenne foglalkozni mytestapp.ilbase.com, állítsa be, hogy a hosts fájlt. (Windows rendszerben a hosts fájl a Következő helyen található: C:\Windows\System32\drivers\etc\)
7. Használjon böngészőt, hogy a `https://mytestapp.ilbase.com` virtuális gép, és megy (vagy bármi legyen is a webapp neve az aldomain).
8. A virtuális gép böngészőjével keresse fel a következő oldalt: `https://mytestapp.ilbase.com`. Önaláírt tanúsítvány használata esetén el kell fogadnia a biztonság hiányát. 

Az ILB IP-címe virtuális IP-címként szerepel a Tulajdonságok között.

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE használata
#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
Az ILB ASE lehetővé teszi az alkalmazások hálózati elkülönítését. Az alkalmazások nem érhetők el, sőt ismert az interneten. Ez a megközelítés kiválóan alkalmas intranetes webhelyek, például üzletági alkalmazások üzemeltetésére. Ha tovább kell korlátoznia a hozzáférést, továbbra is használhatja a hálózati biztonsági csoportokat a hozzáférés hálózati szintű szabályozásához. 

Ha nsg-ket kíván használni a hozzáférés további korlátozására, győződjön meg arról, hogy nem szakítja meg az ASE működéséhez szükséges kommunikációt. Annak ellenére, hogy a HTTP/HTTPS-hozzáférés csak az ASE által használt ILB-n keresztül történik, az ASE továbbra is a virtuális hálózaton kívüli erőforrásoktól függ. Ha meg szeretné tudni, hogy milyen hálózati hozzáférésre van még szükség, olvassa el [az App Service-környezet bejövő forgalmának szabályozása][ControlInbound] és [az ExpressRoute-környezetekkel rendelkező App Service-környezetek hálózati konfigurációs részletei című témakört.][ExpressRoute] 

Az NSG-k konfigurálásához ismernie kell az IP-címet, amelyet az Azure az ASE kezelésére használ. Ez az IP-cím egyben a kimenő IP-címet az ASE, ha internetes kéréseket tesz. Az ASE kimenő IP-címe statikus marad az ASE élettartama alatt. Ha törli, és újra létrehozza az ASE, kap egy új IP-címet. Az IP-cím megkereséséhez nyissa meg a **Beállítások -> tulajdonságok lapot,** és keresse meg a **kimenő IP-címet.** 

![][5]

#### <a name="general-ilb-ase-management"></a>Általános ILB ASE-kezelés
Az ILB ASE kezelése nagyjából megegyezik az ASE szokásos kezelésével. A feldolgozókészletek et több ASP-példány üzemeltetéséhez kell felskáláznia, és fel kell skáláznia az előtér-kiszolgálókat a http/HTTPS-forgalom megnövekedett mennyiségének kezeléséhez. Az ASE konfigurációjának kezeléséről az [App Service-környezet konfigurálása][ASEConfig]című témakörben talál általános tudnivalókat. 

A további felügyeleti elemek a tanúsítványkezelés és a DNS-kezelés. Az ILB ASE létrehozása után be kell szereznie és fel kell töltenie a HTTPS-hez használt tanúsítványt, és le kell cserélnie, mielőtt lejár. Mivel az Azure birtokolja az alaptartományt, külső virtuális ip-címekkel biztosíthat tanúsítványokat az ase-k számára. Mivel az ILB ASE által használt altartomány bármi lehet, meg kell adnia a saját tanúsítványát a HTTPS-hez. 

#### <a name="dns-configuration"></a>DNS-konfiguráció
Külső VIP használata esetén a DNS-t az Azure kezeli. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. Egy adott altartomány, például contoso.corp.net esetén létre kell hoznia a DNS A rekordokat, amelyek az Ön ILB-címére mutatnak:

    * 
    *.scm ftp közzététel 


## <a name="getting-started"></a>Első lépések
Az App Service-környezetek ismerkedése az [App Service-környezetek bemutatása című témakörben][WhatisASE]

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
