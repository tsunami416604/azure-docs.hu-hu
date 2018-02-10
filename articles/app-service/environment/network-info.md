---
title: "Hálózat az Azure App Service-környezetek megfontolások"
description: "Ismerteti a ASE hálózati forgalom és az NSG-ket és a mértékéig udr-EK beállítása"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.openlocfilehash: c4779ada60fab2db5249a107abfc7ca6f80cb16f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Az App Service-környezetek hálózati szempontjai #

## <a name="overview"></a>Áttekintés ##

 Azure [App Service Environment-környezet] [ Intro] egy alhálózatba az Azure virtuális hálózatban (VNet) az Azure App Service üzemelő példány. Nincsenek App Service-környezet (ASE) két központi telepítési típusokat:

- **Külső ASE**: elérhetővé teszi a ASE által kezelt alkalmazások az internetről elérhető IP-címet. További információkért lásd: [hozzon létre egy külső ASE][MakeExternalASE].
- **ILB ASE**: IP-címet a virtuális hálózaton belül a ASE által üzemeltetett alkalmazások közzététele. A belső végpont nem a belső terheléselosztók (ILB), ezért azt egy ILB ASE hívják. További információkért lásd: [létrehozása és használata egy ILB ASE][MakeILBASE].

Most már két verziója van App Service Environment-környezet: ASEv1 és ASEv2. A ASEv1 információkért lásd: [Bevezetés az App Service Environment-környezet v1][ASEv1Intro]. ASEv1 klasszikus vagy erőforrás-kezelő VNet is telepíthető. ASEv2 csak egy erőforrás-kezelő virtuális hálózaton történő telepíthetők.

Egy ASE összes hívásait az interneten keresztül a ASE hozzárendelt virtuális IP-címhez a VNet hagyja. A nyilvános IP-címe a VIP majd a ASE összes hívásait az interneten a forrás IP-címe. Ha az alkalmazásokat a ASE hívások erőforrásokhoz a virtuális hálózat vagy VPN-en keresztül, a forrás IP-címe az az IP-cím az alhálózat, a ASE által használt egyik. Mivel a ASE a Vneten belül, azt is elérhető erőforrások további konfiguráció nélkül a Vneten belül. Ha a virtuális hálózat a helyszíni hálózathoz csatlakozik, a ASE alkalmazások is erőforrásokhoz való hozzáférés van. Nem kell konfigurálni a ASE vagy az alkalmazás minden további.

![Külső ASE][1] 

Ha egy külső ASE, a nyilvános VIP egyben a végpont a feloldható ASE alkalmazásai:

* A HTTP/S 
* FTP/MP. 
* A webes telepítése.
* Távoli hibakeresés.

![ILB ASE][2]

Ha egy ILB ASE, a ILB IP-címe a HTTP/S, az FTP/S, a webes telepítési és a távoli hibakeresés végpontja.

A normál alkalmazás-hozzáférési portok a következők:

| Használat | Forrás | Művelet |
|----------|---------|-------------|
|  HTTP/HTTPS  | Felhasználó által konfigurálható |  80, 443 |
|  FTP/FTPS    | Felhasználó által konfigurálható |  21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresés  |  Felhasználó által konfigurálható |  4016, 4018, 4020, 4022 |

Ez érvényét veszti, ha egy külső ASE vagy egy ILB ASE. Ha a számítógép egy külső ASE, ezeket a portokat, a nyilvános VIP kattint. Ha a számítógép egy ILB ASE, ezeket a portokat a Példánynak a kattint. Ha zárolását a 443-as porton, néhány funkció, a portálon hatással lehet. További információkért lásd: [Portal függőségek](#portaldep).

## <a name="ase-subnet-size"></a>ASE alhálózat mérete ##

Nem módosítható egy ASE üzemeltetni az alhálózat méretét, a ASE telepítése után.  A ASE egy címet használ az egyes infrastruktúra szerepkörökhöz, valamint minden egyes elkülönített App Service csomag példány esetében.  Emellett nincsenek miden alhálózatában létrehozott Azure-hálózat által használt 5 címek.  Nincsenek App Service-csomagokról és egy ASE minden címeket fogja használni 12-alkalmazás létrehozása előtt.  Ha egy ILB ASE majd fogja használni 13 címek ahhoz, hogy ASE alkalmazást hoz létre. Mivel az alkalmazás állapotát tervek a horizontális azt minden hozzáadott előtér további címeket igényel.  Alapértelmezés szerint minden 15 teljes App Service-csomag példányok előtér-kiszolgáló hozzáadva. 

   > [!NOTE]
   > Nincs más lehet az alhálózat, de a ASE. Ne válasszon, amely lehetővé teszi a jövőbeli növekedésre címteret. Később Ez a beállítás nem módosítható. Azt javasoljuk, hogy a méretet `/25` 128-címekkel.

## <a name="ase-dependencies"></a>ASE függőségek ##

Egy ASE befelé függőség:

| Használat | Forrás | Művelet |
|-----|------|----|
| Kezelés | App Service management címek | ASE alhálózati: 454, 455 |
|  Belső kommunikációs ASE | ASE alhálózati: minden port | ASE alhálózati: minden port
|  Engedélyezi az Azure terheléselosztó bejövő | Azure Load Balancer | ASE alhálózati: minden port
|  IP-címek hozzárendelt alkalmazás | Címek hozzárendelt alkalmazás | ASE alhálózati: minden port

A bejövő forgalom biztosít a parancs és a rendszer figyelése mellett ASE irányítását. Az ilyen típusú adatforgalom a forrás IP-címek jelennek meg a [ASE felügyeleti címek] [ ASEManagement] dokumentum. A hálózati biztonsági beállításokat kell engedélyezi a hozzáférést a 454 és a 455 portokat összes IP-címekről.

A belső összetevő használt sok port ASE alhálózaton belüli kommunikációhoz és azok módosíthatja.  Ehhez minden port ASE az alhálózaton az ASE alhálózatból elérhetőnek kell lennie. 

Az Azure load balancer és a ASE alhálózati közötti kommunikációhoz a minimális portok nyitva kell lennie a 454, 455 és 16001. A 16001 port használatban van a terheléselosztó és a ASE közötti megtartása életben adatforgalomhoz. Ha egy ILB ASE használ, akkor csak a 454, 455, 16001 le forgalom zárolhatja portok.  Ha egy külső ASE használ akkor kell figyelembe venni a normál alkalmazás-hozzáférési portok.  Hozzárendelt alkalmazás címek használata szüksége nyissa meg az összes porton.  Amikor egy cím hozzá van rendelve egy adott alkalmazást, majd a terheléselosztó nem ismert az előzetesen HTTP és HTTPS-forgalmat küldeni a ASE portok fogja használni.

Alkalmazás-kezelési forgalom engedélyezése a az alkalmazások ASE az alhálózathoz hozzárendelt IP-címekről kell IP-címek használata.

A kimenő hozzáférés érdekében egy ASE több külső rendszer függ. E rendszer függőségek rendelkező DNS-nevek, és nem feleltethetők meg az IP-címek készletét. Ebből kifolyólag a ASE portok számos minden külső IP-címek a ASE alhálózatból kimenő hozzáférésre van szüksége. Egy ASE a következő kimenő függőségekkel rendelkezik:

| Használat | Forrás | Művelet |
|-----|------|----|
| Azure Storage | ASE alhálózati | TABLE.Core.Windows.NET, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80-as, a 443-as, a 445-ös (445-ös csak szükséges ASEv1.) |
| Azure SQL Database | ASE alhálózati | Database.Windows.NET: 1433-as számú 11000-11999, 14000-14999 (további információkért lásd: [SQL Database 12-es port használati](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).)|
| Azure felügyelet | ASE alhálózati | management.core.windows.net, management.azure.com: 443 
| SSL-tanúsítvány ellenőrzése |  ASE alhálózati            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | ASE alhálózati            |  Internet: 443
| Az alkalmazásszolgáltatási management        | ASE alhálózati            |  Internet: 443
| Azure DNS                     | ASE alhálózati            |  Internet: 53
| Belső kommunikációs ASE    | ASE alhálózati: minden port |  ASE alhálózati: minden port

Ha a ASE elveszítette a hozzáférését ezeket a függőségeket, a rendszer nem működik. Ebben az esetben elég hosszú a ASE fel van függesztve.

### <a name="customer-dns"></a>Ügyfél DNS ###

Ha a virtuális hálózat DNS-ügyfél által megadott kiszolgáló van konfigurálva, a bérlők munkaterheléseihez használhatja. A ASE továbbra is felügyelet céljából Azure DNS-sel való kommunikációhoz szükséges. 

Ha a virtuális hálózat DNS, a másik oldalon az egy VPN-ügyfél van beállítva, a DNS-kiszolgáló az alhálózatot, amely tartalmazza a ASE elérhetőnek kell lennie.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portál függőségek ##

A ASE működési függőségek kívül a portál élmény kapcsolatos néhány további elemek. Néhány olyan funkciója, az Azure-portálon való közvetlen hozzáférés függő _SCM hely_. Minden az Azure App Service-alkalmazást a két URL-címek vannak. Az első URL-címe, az alkalmazás eléréséhez. A második URL-je néven is ismert SCM webhely eléréséhez a _Kudu konzol_. Az SCM helyet használó szolgáltatások a következők:

-   Webes feladatok
-   Functions
-   Naplózási streaming
-   Kudu
-   Bővítmények
-   Process Explorer
-   Konzol

Egy ILB ASE használatakor az SCM-hely nem található a virtuális hálózaton kívülről hozzáférhető internet. Ha az alkalmazás egy ILB ASE üzemelteti, bizonyos funkciók nem működnek a portálról.  

Ezekre a képességekre, amelyek az SCM hely számos érhető el közvetlenül a Kudu konzolon. A portál használata helyett közvetlenül is elérheti. Ha az app Service-Példánynak környezetben, a közzétételi hitelesítő adatok használatával jelentkezzen be. Egy alkalmazás-Példánynak környezetben üzemeltetett SCM webhely eléréséhez az URL-cím formátuma a következő: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

A tartománynév esetén a ILB ASE *contoso.net* és az alkalmazás neve *testapp*, az alkalmazás elérése *testapp.contoso.net*. Az SCM-helyet, amely vele együtt áthelyeződik éri el a *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Függvények és a webes feladatok ##

Függvények és a webes feladatok az SCM hely függ, de akkor is, ha az alkalmazások-Példánynak környezetben, feltéve, hogy a böngésző el lehet-e érni az SCM hely a portál használata támogatott.  Ha önaláírt tanúsítványt használ a ILB mértékéig, szüksége lesz ahhoz, hogy a böngészőt, hogy a tanúsítvány megbízható.  Az Internet Explorer és a peremhálózati, amely azt jelenti, hogy a tanúsítvány-nak kell lennie a számítógép megbízható tárolja.  Ha Chrome használja majd, amely azt jelenti, hogy elfogadott a böngészőben a tanúsítvány korábban által a vélhetően elérés közvetlenül az scm-hely.  A legjobb megoldás, hogy a böngésző lánc megbízhatósági egy kereskedelmi tanúsítványt használja.  

## <a name="ase-ip-addresses"></a>ASE IP-címek ##

Egy ASE ismernie néhány IP-címmel rendelkezik. Ezek a következők:

- **Nyilvános bejövő IP-cím**: app Service-külső környezetben, és felügyeleti forgalom egy külső ASE és egy ILB ASE is használatos.
- **Kimenő nyilvános IP-cím**: az "feladó" IP-cím kimenő kapcsolatok a ASE a, amely hagyja, a virtuális hálózat, amely VPN le nem útválasztásos használt.
- **ILB IP-cím**: Ha egy ILB ASE használja.
- **Alkalmazás által hozzárendelt IP-alapú SSL-címek**: csak akkor lehetséges, egy külső mértékéig és IP-alapú SSL be van állítva.

A következő IP-címek láthatók egyszerűen egy ASEv2 az Azure-portálon a ASE felhasználói felületen. Ha egy ILB ASE, az IP-Címek használhatók a ILB szerepel.

![IP-címek][3]

### <a name="app-assigned-ip-addresses"></a>Alkalmazás által hozzárendelt IP-címek ###

Egy külső mértékéig IP-címek rendelhet az egyes alkalmazásokhoz. Nem hajthatja végre, amely egy ILB mértékéig. Az alkalmazásnak a saját IP-cím konfigurálásával kapcsolatos további információkért lásd: [egy meglévő egyéni SSL-tanúsítvány kötését az Azure-webalkalmazásokban](../app-service-web-tutorial-custom-ssl.md).

Ha egy alkalmazás a saját IP-alapú SSL-címmel rendelkezik, a ASE fenntartja két portok leképezése az IP-címet. Egy port a HTTP-forgalom, és az egyéb portot a HTTPS-hez. Ezeket a portokat a ASE felhasználói felületén, az IP-címek szakaszban találhatók. Forgalom kell elérni ezeket a portokat a a VIP vagy azok nem érhetők el. Ez a követelmény fontos, hogy ne feledje, hogy a hálózati biztonsági csoportokkal (NSG-k) konfigurálásakor.

## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok) ##

[Hálózati biztonsági csoportok] [ NSGs] lehetővé teszik a hálózati hozzáférést egy Vneten belül. A portál használata esetén nincs egy implicit Megtagadás szabály minden visszautasítja a legalacsonyabb prioritással. Állít össze vannak a szabályokat.

-Környezetben nincs a virtuális gépek üzemeltetni a ASE magát a hozzáférést. Is a Microsoft által kezelt előfizetést. Ha azt szeretné, az alkalmazások a ASE való hozzáférésének korlátozásához, állítsa be az NSG-k ASE az alhálózaton. Ennek során a ASE függőségek alapos figyelmet fordítania. Ha letiltja az összes függőséget, az ASE működése leáll.

Az NSG-k az Azure portálon keresztül vagy a PowerShell segítségével konfigurálható. Itt tartalmazza az Azure-portálon. Létrehozásához és kezeléséhez az NSG-ket a portálon, a legfelső szintű erőforrásként **hálózati**.

A bejövő és kimenő követelmények figyelembe kell venni, amikor az NSG-ket az NSG-ket ebben a példában látható módon hasonlóan kell kinéznie. A virtuális hálózat címtartomány _192.168.250.0/23_, és az alhálózatot, amely a ASE _192.168.251.128/25_.

A függvény ASE első két bejövő követelményei ebben a példában a lista tetején látható. Ezek ASE és felügyeletét teszi lehetővé maga kommunikálni ASE engedélyezése. A többi bejegyzés összes bérlői konfigurálható és képesek felügyelni a hálózati hozzáférést a ASE állomásokon tárolt alkalmazásokhoz. 

![Bejövő biztonsági szabályok][4]

Alapértelmezett szabály lehetővé teszi, hogy az IP-címek a ASE alhálózati felvegye a Vneten belül. Egy másik alapértelmezett szabály lehetővé teszi, hogy a terheléselosztó, más néven a nyilvános VIP, a mértékéig kommunikációhoz. Az alapértelmezett szabályok megtekintéséhez válasszon **alapértelmezett szabályok** mellett a **Hozzáadás** ikonra. Ha a Megtagadás, minden más szabály, miután az NSG-szabályok látható, hogy akadályozza meg a forgalmat a VIP és a ASE között. A virtuális hálózaton belül érkező forgalom elkerülése érdekében adja hozzá a saját szabály, amely engedélyezi a bejövő. A forrás AzureLoadBalancer egyenlőnek használata a cél **bármely** és egy porttartomány  **\*** . Az NSG-szabály ASE van alkalmazva, mert nem kell a cél adott lehet.

Ha az alkalmazáshoz rendelt IP-címet, győződjön meg arról, hogy a portok nyitva tartása. A portok megtekintéséhez válasszon **App Service Environment-környezet** > **IP-címek**.  

A következő kimenő szabályok látható elemeinek van szükség, az utolsó elem kivételével. Az ebben a cikkben észleltek ASE függőségek való hálózati hozzáférés lehetővé teszik. Ha letiltja az e valamelyiket, a ASE működése leáll. A lista utolsó elemének lehetővé teszi, hogy a ASE kommunikálni a Vneten található más erőforrásokat.

![Kimenő biztonsági szabályok][5]

Az NSG-k meghatározása után rendelje hozzá őket az alhálózatot, amely a ASE a. Ha nem emlékszik a ASE virtuális hálózat vagy az alhálózat, megtekintheti az ASE portál oldalról. Az NSG-t rendel az alhálózat, nyissa meg a felhasználói felület alhálózathoz, és válassza ki az NSG-t.

## <a name="routes"></a>Útvonalak ##

Az útvonalak a kényszerített bújtatás és a kényszerített bújtatással folytatott munka kulcsfontosságú elemei. Egy Azure-beli virtuális hálózatban az útválasztás a leghosszabb előtag-megfeleltetés (LPM) alapján történik. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, akkor a rendszer az útvonalat a kiindulás alapján választja ki, az alábbi sorrendben:

- Felhasználó által meghatározott útvonal (UDR)
- BGP-útvonal (ExpressRoute használatánál)
- Rendszerútvonal

Ha többet szeretne megtudni a virtuális hálózatokban történő útválasztásról, olvassa el a [felhasználó által megadott útvonalakat és IP-továbbítást][UDRs] ismertető cikket.

Az Azure SQL-adatbázis, amely a ASE használja a rendszer egy tűzfal van. Kommunikáció a ASE nyilvános VIP oly módon, hogy ez igényli. Az SQL adatbázishoz a ASE kapcsolódásának elutasításra kerülne, ha elküldi őket egy másik IP-cím és az ExpressRoute-kapcsolatot.

Ha a bejövő felügyeleti kérelmekre adott válaszokat le az ExpressRoute, a válaszcímet eltér az eredeti célra. Ez az eltérés megsérti a TCP-kommunikációt.

A ASE működjön, miközben a virtuális hálózat egy ExpressRoute van konfigurálva a legegyszerűbb is van:

-   Konfigurálja az ExpressRoute hivatkozik _0.0.0.0/0_. Ez alapértelmezés szerint kényszerített bújtatás mellett irányít minden kimenő forgalmat a helyszíni hálózatba.
-   Hozzon létre egy UDR-t. Alkalmazza azt az alhálózatot, amely tartalmazza a egy címelőtagot ASE _0.0.0.0/0_ és következőugrás-típusú _Internet_.

Ha a módosítások két, a ASE alhálózatról érkező forgalmat az internet felé nem működik az ExpressRoute- és a ASE le kényszerített. 

> [!IMPORTANT]
> Az UDR-ben meghatározott útvonalaknak annyira pontosnak kell lenniük, hogy prioritást kapjanak az ExpressRoute-konfiguráció által meghirdetett bármely útvonallal szemben. Az előző példa a széles 0.0.0.0/0 címtartományt használja. Ezt véletlenül felülírhatják olyan útvonalhirdetések, amelyek pontosabb címtartományokat használnak.
>
> ASEs cross-hirdetményt a magánfelhő-társviszony létesítése – elérési utat a nyilvános társviszony elérési útvonalak ExpressRoute beállításokkal nem támogatottak. A konfigurált nyilvános társviszonyt létesítő ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsofttól. A meghirdetések Microsoft Azure IP-címtartományok nagy készletét tartalmazzák. Ha a címtartomány határokon meghirdetett privát társviszony elérési útján, minden kimenő hálózati csomagokat a ASE alhálózatból egy ügyfél a helyi hálózati infrastruktúra bújtatott hatályba. A hálózati folyamat jelenleg nem támogatott a ASEs. Egy megoldás erre a problémára az, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra.

Hozzon létre egy UDR, kövesse az alábbi lépéseket:

1. Ugrás az Azure-portálon. Válassza ki **hálózati** > **útvonaltáblát**.

2. Hozzon létre egy új útvonaltábla és a virtuális hálózat ugyanabban a régióban.

3. Az útvonaltábla felhasználói felületén belül jelölje ki **útvonalak** > **Hozzáadás**.

4. Állítsa be a **a következő ugrás típusa** való **Internet** és a **címelőtag** való **0.0.0.0/0**. Kattintson a **Mentés** gombra.

    Ekkor megjelenik egy, a következőhöz hasonló:

    ![Funkcionális útvonalak][6]

5. Új útvonaltábla létrehozása után nyissa meg az alhálózatot, amely tartalmazza a ASE. Válassza ki az útválasztási táblázatot a portálon a listából. A módosítás mentése után majd megtekintheti az NSG-ket és ezekkel az alhálózati útvonalakat.

    ![Az NSG-k és útvonalak][7]

### <a name="deploy-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>Üzembe helyezés meglévő Azure virtuális hálózataihoz ExpressRoute integrált ###

A ASE egy Vnetet, amely integrálva van a ExpressRoute történő központi telepítéséhez adja meg az alhálózatot, ha azt szeretné, hogy a telepített ASE előre. A Resource Manager-sablon segítségével telepítheti azt. Hozzon létre egy ASE a Vneten belül, amely már rendelkezik konfigurált ExpressRoute:

- Hozzon létre egy alhálózatot a ASE üzemeltetéséhez.

    > [!NOTE]
    > Nincs más lehet az alhálózat, de a ASE. Ne válasszon, amely lehetővé teszi a jövőbeli növekedésre címteret. Később Ez a beállítás nem módosítható. Azt javasoljuk, hogy a méretet `/25` 128-címekkel.

- Hozzon létre udr-EK (például az útvonaltáblák), a fentebb leírt módon, és állítsa be, amely az alhálózaton.
- A ASE létrehozása a Resource Manager sablonnal [egy ASE létrehozása egy Resource Manager-sablon használatával][MakeASEfromTemplate].

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
