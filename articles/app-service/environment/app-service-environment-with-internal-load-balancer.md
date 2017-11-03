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
ms.openlocfilehash: c0f942cb11edc6d6212914b3134e25c3fbc3d3ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>A belső Terheléselosztók használatával az App Service-környezet

> [!NOTE] 
> Ez a cikk az App Service Environment-környezet v1 tárgya. Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
>

Az App Service-környezet (ASE) funkció az Azure App Service egy továbbfejlesztett konfigurációs funkció, amely nem érhető el a több-bérlős bélyegzők letölti a Premium szolgáltatás lehetőség. A ASE funkció tulajdonképpen telepíti az Azure App Service szolgáltatásban az Azure virtuális Network(VNet). Ahhoz, hogy az App Service Environment-környezetek olvasási által biztosított képességeket megértése a [Mi az az App Service-környezetek] [ WhatisASE] dokumentációját. Ha nem ismeri a működő virtuális hálózat olvasható előnyeit a [Azure virtuális hálózat – gyakori kérdések][virtualnetwork]. 

## <a name="overview"></a>Áttekintés
Egy ASE is telepíthető, az interneten elérhető végpontok vagy a virtuális IP-címmel. Ahhoz, hogy az IP-cím beállítása kell telepítenie a ASE egy belső terheléselosztási Balancer(ILB) a VNet-címhez. Ha a ASE van adva egy ILB az megadnia:

* saját tartomány és altartomány. Könnyen, a jelen dokumentum céljából feltételezzük altartomány, de akár lehet állítani. 
* a HTTPS-hez használt tanúsítvány
* DNS-kezelési a altartomány. 

Ismét műveleteket végezheti el, mint:

* intranetes alkalmazások üzemeltetését, például az üzletági alkalmazások biztonságosan a felhőben, amelyek egy helyet, hogy a hely vagy ExpressRoute VPN keresztül érhető el
* állomás alkalmazások a felhőben, amelyek nem szerepelnek a nyilvános DNS-kiszolgálók
* az előtér-alkalmazások biztonságosan integrálható elkülönített internet háttér alkalmazások létrehozása

#### <a name="disabled-functionality"></a>Letiltott funkció
Néhány dolgot, amely egy ILB ASE használatakor nem hajtható végre. Ezeket a beállításokat a következők:

* IPSSL használatával
* IP-címek hozzárendelése a megadott alkalmazások
* vételi, és a portálon keresztül egy alkalmazás olyan tanúsítványt használ. Természetesen továbbra is közvetlenül a hitelesítésszolgáltatót a tanúsítványok beszerzése, és az alkalmazásokat, nem csupán az Azure portálon keresztül együtt használja azt.

## <a name="creating-an-ilb-ase"></a>Egy ILB ASE létrehozása
Egy ILB ASE létrehozása nincs hozzon létre egy ASE általában sokkal különböző. Egy ASE létrehozása mélyebb döntéseken olvasási [egy App Service Environment-környezet létrehozása][HowtoCreateASE]. Egy ILB ASE létrehozásának folyamata megegyezik egy virtuális hálózat létrehozása ASE létrehozása során, vagy jelöljön ki egy már meglévő virtuális hálózat között. Egy ILB ASE létrehozása: 

1. Az Azure portálon válassza a **új -> Web + mobil -> az App Service Environment-környezet**
2. Jelölje ki az előfizetését
3. Válassza ki vagy hozzon létre egy erőforráscsoportot
4. Válasszon vagy hozzon létre egy Vnetet
5. Hozzon létre egy alhálózatot, ha egy virtuális hálózat kiválasztása
6. Válassza ki **virtuális hálózati/hely VNet konfigurációja ->** , és a VIP-típus belső
7. Adja meg (Ez lesz az alkalmazások a ASE létrehozott használt altartomány) altartománynév
8. Kattintson az OK gombra, majd hozza létre

![][1]

A virtuális hálózat panel belül nincs a VNet konfigurációját beállítás. Ez lehetővé teszi egy külső VIP vagy VIP belső között választja. Az alapértelmezett érték a külső. Ha van állítva, a külső a ASE az interneten elérhető VIP fogja használni. Ha belső, a ASE egy ILB a Vneten belül egy IP-cím van konfigurálva. 

Miután kiválasztotta a belső, képes több IP-címek hozzáadása a ASE törlődik, és ehelyett meg kell adnia a hajlamosnak altartomány. Egy külső virtuális IP-címre Service-környezetben a ASE neve szerepel az altartomány adott ASE létrehozott alkalmazások. Ha a ASE lett meghívva ***contosotest*** és az alkalmazás az adott ASE hívták ***mytest*** akkor válassza az altartomány formátumú ***contosotest.p.azurewebsites.net*** és az URL-cím, az adott alkalmazáshoz lenne ***mytest.contosotest.p.azurewebsites.net***. A VIP-típus megadásával belső, a ÁZIS neve nem szolgál a altartományában a ASE. Az altartomány explicit módon adja meg. Ha a altartomány ***contoso.corp.net*** és abban a ASE nevű alkalmazás végrehajtott ***timereporting*** akkor válassza az URL-cím, az adott alkalmazáshoz ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Alkalmazás-Példánynak környezetben
Alkalmazás létrehozása-Példánynak környezetben megegyezik a általában létrehozása a egy app Service-környezetben. 

1. Az Azure portálon válassza a **új -> Web + mobil -> webkiszolgáló** vagy **Mobile** vagy **API-alkalmazás**
2. Adja meg az alkalmazás nevét
3. Előfizetés kiválasztása
4. Válassza ki vagy erőforráscsoport létrehozása
5. Válassza ki, vagy hozzon létre az App Service Plan(ASP). Ha egy új ASP létrehozása ezután jelölje ki a ASE helyeként, és válassza a feldolgozókészleten azt szeretné, hogy az ASP lesznek létrehozva. Az ASP létrehozásakor kiválasztható a ASE a hely és a munkavégző készletét. Ha megadja az alkalmazás neve jelenik meg, hogy az alkalmazás neve alatt altartomány helyébe a altartomány a ASE. 
6. Válassza ki a létrehozása. Ki kell jelölni a **rögzítés az irányítópulton** jelölőnégyzetet, ha azt szeretné, hogy az alkalmazás jelenik meg az irányítópulton. 

![][2]

Az alkalmazás neve alatt a altartománynév lekérdezi a frissített a hajlamosnak altartomány. 

## <a name="post-ilb-ase-creation-validation"></a>POST ILB ASE létrehozásának ellenőrzése
Egy ILB ASE mint a nem - Példánynak ASE némileg eltérő. Már nincs jelezve kell kezelni a saját DNS és a HTTPS-kapcsolatok adja meg a tanúsítvány van. 

Miután létrehozta a ASE láthatja, hogy a altartomány jeleníti meg az altartományt megadott, és egy új elemet a **beállítás** nevű menü **ILB tanúsítvány**. A ASE jön létre egy önaláírt tanúsítványt, így azokat könnyebben HTTPS teszteléséhez. A portál jelzi, hogy meg kell adnia a saját tanúsítványt a HTTPS-hez, de ez az a meghajtó, hogy a tanúsítványa van, a altartomány együtt. 

![][3]

Ha dolgot egyszerűen próbál, és nem tudja, hogyan hozható létre tanúsítvány, az IIS MMC konzol alkalmazás segítségével hozzon létre egy önaláírt tanúsítványt. A már létrehozott egy .pfx fájlba exportálni, és majd töltse fel a ILB tanúsítvány felhasználói felületén. Amikor egy önaláírt tanúsítvánnyal biztonságos hely fér hozzá, a böngésző meg figyelmeztetést ad, hogy a hely elérésére nincs-e biztonságos miatt a nem sikerült a tanúsítvány érvényesítéséhez. Ha el szeretné kerülni, hogy a megfelelő aláírt tanúsítvány, amely megfelel a altartomány, és nem ismeri fel a böngésző megbízhatósági láncot kell figyelmeztetés.

![][6]

Ha azt szeretné, próbálja meg a folyamatot a saját tanúsítványait, és a HTTP és HTTPS a hozzáférést a ASE teszteléséhez:

1. ASE UI ASE létrehozását követően navigáljon **ASE -> Beállítások -> ILB tanúsítványok**
2. Állítsa be a ILB tanúsítvány tanúsítvány pfx-fájljának kiválasztásával, és adja meg a jelszót. Ez a lépés feldolgozni egy kis ideig tart, és jelenik meg, hogy a méretezési művelet van folyamatban.
3. A ILB cím lekérése a ASE (**ASE Tulajdonságok ->-a virtuális IP-cím >**)
4. A webalkalmazás létrehozása az ASE létrehozása után 
5. Virtuális gép létrehozása, ha még nem rendelkezik az adott virtuális hálózaton (nem található a ASE vagy dolgot break azonos alhálózaton)
6. Állítsa be a DNS a altartomány. Helyettesítő karakter használata a altartomány a DNS-ben, vagy szeretné egyszerű tesztek, ha a virtuális gép beállítása a webalkalmazás nevének VIP IP-címre a gazdafájl szerkesztése. Ha a ASE altartománynév. ilbase.com, és hogy végzett a webes alkalmazás mytestapp, hogy a volna kell címzett mytestapp.ilbase.com, majd állítsa be, amely a hosts fájl. (A Windows a hosts fájl jelenleg C:\Windows\System32\drivers\etc\)
7. Használja ezt a virtuális Gépet egy böngészőt, és navigáljon a http://mytestapp.ilbase.com (vagy bármilyen a webes alkalmazás neve a altartomány rendelkező)
8. Használja ezt a virtuális Gépet egy böngészőt, és navigáljon a https://mytestapp.ilbase.com, el kell fogadniuk biztonsági hiánya, ha egy önaláírt tanúsítványt használ. 

A Példánynak az IP-cím van megadva a tulajdonságai, a virtuális IP-cím

![][4]

## <a name="using-an-ilb-ase"></a>Egy ILB ASE használatával
#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
Egy ILB ASE lehetővé teszi, hogy az alkalmazások hálózati elkülönítési, az alkalmazások nem elérhetők, és akkor is ismert által az interneten. Ez a kiváló, például az üzletági alkalmazásokat az intranetes helyek üzemeltetéséhez. Ha még akkor is korlátozza a hozzáférést kell további továbbra is használhatja hálózati biztonsági Groups(NSGs) hálózati szintű hozzáférés vezérlése érdekében. 

Ha szeretné használni az NSG-k tovább korlátozhatja a hozzáférést, akkor ellenőrizze, hogy nem megszakítja a kommunikációt, amelyet a ASE működéshez szükséges. Annak ellenére, hogy a HTTP/HTTPS-hozzáférés csak a Példánynak a ASE által használt keresztül a ASE továbbra is függ, a virtuális hálózaton kívül erőforrás. Milyen hálózati hozzáférési továbbra is szükség keresse meg a részleteket a dokumentum megtekintéséhez [bejövő forgalom szabályozása az App Service-környezetek] [ ControlInbound] és a dokumentum [hálózati konfiguráció részletei az App Service-környezetek ExpressRoute][ExpressRoute]. 

Az NSG-k az IP-cím, amellyel az Azure-ban kezelheti a ASE tudnia kell konfigurálni. Az IP-címet esetén is a kimenő IP-címet a ASE internetes kérelmek teszi. A kimenő IP-címet a ASE statikus a ASE élettartamára marad. Ha töröl, és hozza létre újra a ASE, elérhetővé válik egy új IP-címet. Található IP-cím Ugrás **beállítások -> Tulajdonságok** keresse meg a **kimenő IP-cím**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Általános ILB ASE kezelése
Egy ILB ASE kezelése megegyezik nagyjából egy ASE általában kezelése. Vertikális felskálázás ismételt ASP és a megnövekedett mennyiségű HTTP/HTTPS-forgalom kezelésére előtér-kiszolgálókhoz növelheti a feldolgozókészletek kell. Az egy ASE konfigurációjának kezelésére vonatkozó általános információk a dokumentum olvasásához a [az App Service-környezetek konfigurálása][ASEConfig]. 

A további felügyeleti cikkeket tanúsítványkezelés és a DNS-kezelés. Meg kell szereznie és ILB ASE létrehozása után a HTTPS-hez használt tanúsítvány feltöltése, és cserélje le az Érvényesség lejárata előtt. Mivel Azure az alap tartománnyal rendelkezik tanúsítványok is nyújtunk a ASEs egy külső virtuális IP-címre. Mivel az egy ILB ASE által használt altartomány bármi lehet, meg kell adnia a saját tanúsítvány HTTPS-hez. 

#### <a name="dns-configuration"></a>DNS-konfiguráció
Ha egy külső VIP a DNS használatával kezeli az Azure-ban. Bármely alkalmazás a ASE létre a rendszer automatikusan az Azure DNS-, amely egy nyilvános DNS-ben. -Példánynak környezetben felügyelni a saját DNS kell. Egy adott altartomány például kell létrehoznia a DNS A rekordokat, amelyek a ILB contoso.corp.net címe:

    * 
    *.SCM ftp-közzététel 


## <a name="getting-started"></a>Bevezetés
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
