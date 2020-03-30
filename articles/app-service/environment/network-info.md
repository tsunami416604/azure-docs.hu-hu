---
title: Hálózati megfontolások
description: Ismerje meg az ASE hálózati forgalmát, és hogyan állíthatja be a hálózati biztonsági csoportokat és a felhasználó által definiált útvonalakat az ASE-vel.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fb931c309b5f85902d8abc9cc6da45576bff4041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259824"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service-környezet hálózati megfontolásai #

## <a name="overview"></a>Áttekintés ##

 Az Azure [App Service Environment][Intro] az Azure App Service egy alhálózatba az Azure virtuális hálózat (VNet) üzembe helyezése. Az App Service-környezethez (ASE) két központi telepítési típus létezik:

- **Külső ASE:** Elérhetővé teszi az ASE által üzemeltetett alkalmazásokat egy interneten elérhető IP-címen. További információ: [Külső ASE létrehozása.][MakeExternalASE]
- **ILB ASE:** Az ASE által üzemeltetett alkalmazásokat a virtuális hálózaton belüli IP-címen teszi elérhetővé. A belső végpont egy belső terheléselosztó (ILB), ezért hívják ILB ASE. További információt az [ILB ASE létrehozása és használata][MakeILBASE]című témakörben talál.

Minden ASEs, külső és ILB, rendelkezik egy nyilvános virtuális ip-cím, amely a bejövő felügyeleti forgalom és a cím, amikor hívásokat az ASE az internetre. Az internetre érkező ASE-ből érkező hívások a virtuális hálózatot az ASE-hez rendelt VIP-n keresztül hagyják el. A nyilvános IP e VIP a forrás IP minden hívás az ASE, hogy megy az internetre. Ha az ASE-ben lévő alkalmazások hívásokat kezdeményeznek a virtuális hálózatban vagy egy VPN-ben lévő erőforrásokhoz, a forrás IP az ASE által használt alhálózat egyik IP-címe. Mivel az ASE a virtuális hálózaton belül van, a virtuális hálózaton belüli erőforrásokat is hozzáférhet további konfiguráció nélkül. Ha a virtuális hálózat csatlakozik a helyszíni hálózathoz, az ASE-ben lévő alkalmazások további konfiguráció nélkül is hozzáférhetnek az erőforrásokhoz.

![Külső ASE][1] 

Ha külső ASE-vel rendelkezik, a nyilvános VIRTUÁLIS IP-cím az a végpont is, amelyet az ASE-alkalmazások a következőkhöz határoznak meg:

* HTTP/S 
* FTP/S
* Webes telepítés
* Távoli hibakeresés

![ILB ASE][2]

Ha Rendelkezik ILB ASE-vel, az ILB-cím címe a HTTP/S, az FTP/S, a webes telepítés és a távoli hibakeresés végpontja.

## <a name="ase-subnet-size"></a>ASE-alhálózat mérete ##

Az ASE üzemeltetéséhez használt alhálózat mérete nem módosítható az ASE üzembe helyezése után.  Az ASE egy címet használ az egyes infrastruktúra-szerepkörhöz, valamint minden egyes Elkülönített App Service-csomagpéldányhoz.  Emellett az Azure Networking által használt öt cím minden létrehozott alhálózathoz.  Egy APP Service-csomagok nélküli ASE 12 címet fog használni az alkalmazás létrehozása előtt.  Ha ez egy ILB ASE, majd 13 címet fog használni, mielőtt létrehozna egy alkalmazást az ASE-ben. Az ASE horizontális felskálázása, infrastruktúra-szerepkörök hozzáadása az App Service-csomag példányainak 15 és 20 többszöröse hozzáadódik.

   > [!NOTE]
   > Semmi más nem lehet az alhálózatban, csak az ASE. Ügyeljen arra, hogy olyan címteret válasszon, amely lehetővé teszi a jövőbeli növekedést. Ezt a beállítást később nem módosíthatja. 256 címből álló `/24` méretet ajánlunk.

Ha felfelé vagy lefelé skálázódik, a megfelelő méretű új szerepkörök et ad hozzá, majd a számítási feladatokat az aktuális méretről a célméretre telepíti át. Az eredeti virtuális gépek csak a számítási feladatok áttelepítése után. Ha 100 ASP-példányt rendelkező ASE-vel rendelkezik, akkor lenne egy olyan időszak, amikor a virtuális gépek számának kétszeresére van szüksége.  Ez az oka annak, hogy javasoljuk a használatát a "/24" elhelyezésére bármilyen változtatást lehet szükség.  

## <a name="ase-dependencies"></a>ASE-függőségek ##

### <a name="ase-inbound-dependencies"></a>ASE bejövő függőségek ###

Csak az ASE működéséhez az ASE megköveteli, hogy a következő portok legyenek nyitva:

| Használat | Feladó | Művelet |
|-----|------|----|
| Kezelés | App-szolgáltatás felügyeleti címei | ASE alhálózat: 454, 455 |
|  ASE belső kommunikáció | ASE-alhálózat: Minden port | ASE-alhálózat: Minden port
|  Bejövő Azure-terheléselosztó engedélyezése | Azure Load Balancer | ASE alhálózat: 16001

2 másik port is megnyitható egy portvizsgálaton, 7654-es és 1221-es porton. Válaszolnak egy IP-címet, és semmi több. Szükség esetén blokkolhatók. 

A bejövő felügyeleti forgalom a rendszerfigyelés mellett az ASE vezérlését és vezérlését is biztosítja. A forgalom forráscímei az [ASE Management címek][ASEManagement] dokumentumban találhatók. A hálózati biztonsági konfigurációnak engedélyeznie kell a hozzáférést az ASE felügyeleti címeiből a 454-es és 455-ös portokon. Ha letiltja a hozzáférést ezekről a címekről, az ASE nem megfelelő állapotúvá válik, majd felfüggesztve lesz. A 454-es és 455-ös porton érkező TCP-forgalomnak vissza kell mennie ugyanarról a VIP-címről, különben aszimmetrikus útválasztási probléma lép fel. 

Az ASE alhálózaton belül számos port ot használnak a belső összetevők kommunikációjára, és ezek módosíthatók. Ehhez az ASE-alhálózat összes portjának elérhetővé kell tenni az ASE alhálózatról. 

Az Azure terheléselosztó és az ASE alhálózat közötti kommunikációhoz a megnyitáshoz szükséges minimális portok 454, 455 és 16001. Az 16001-es port a terheléselosztó és az ASE közötti forgalom életben tartására szolgál. Ha ILB ASE-t használ, akkor csak a 454, 455, 16001 portokra zárolhatja a forgalmat.  Ha külső ASE-t használ, akkor figyelembe kell vennie a normál alkalmazás-hozzáférési portokat.  

A többi port, amelyekkel foglalkoznia kell, az alkalmazásportok:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio távoli hibakeresés  |  4020, 4022, 4024 |
|  Webközponti telepítés szolgáltatás | 8172 |

Ha blokkolja az alkalmazásportokat, az ASE továbbra is működhet, de előfordulhat, hogy az alkalmazás nem.  Ha az alkalmazáshoz rendelt IP-címeket külső ASE-vel használja, engedélyeznie kell az alkalmazásokhoz rendelt IP-címekről az ASE alhálózatba irányuló forgalmat az ASE portálon > IP-címek lapon megjelenő portokon.

### <a name="ase-outbound-dependencies"></a>ASE kimenő függőségei ###

A kimenő hozzáférés esetén az ASE több külső rendszertől függ. A rendszerfüggőségek nagy része DNS-nevekkel van definiálva, és nem felel meg az IP-címek rögzített készletének. Így az ASE-nek kimenő hozzáférést kell biztosítania az ASE alhálózatáról az összes külső IP-szolgáltatóhoz a különböző portokon keresztül. 

Az ASE a következő portokon kommunikál internetes címekkel:

| Használat | Portok |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-frissítések, Linux-függőségek, Azure-szolgáltatások | 80/443 |
| Azure SQL | 1433 | 
| Figyelés | 12000 |

A kimenő függőségek az [App Service-környezet kimenő forgalmának zárolását ismertető](./firewall-integration.md)dokumentumban találhatók. Ha az ASE elveszíti a függőségeihez való hozzáférést, leáll. Ha ez elég hosszú ideig történik, az ASE fel van függesztve. 

### <a name="customer-dns"></a>Ügyfél DNS-e ###

Ha a virtuális hálózat ügyfél által definiált DNS-kiszolgálóval van konfigurálva, a bérlői számítási feladatok azt használják. Az ASE az Azure DNS-t használja felügyeleti célokra. Ha a virtuális hálózat ügyfél által kiválasztott DNS-kiszolgálóval van konfigurálva, a DNS-kiszolgálónak elérhetőnek kell lennie az ASE-t tartalmazó alhálózatról.

A DNS-feloldás webalkalmazásból történő teszteléséhez használhatja a konzolparancs *nameresolver*. Nyissa meg az alkalmazás scm-webhelyének hibakeresési ablakát, vagy nyissa meg az alkalmazást a portálon, és válassza a konzolt. A rendszerhéj ból kiadhatja a parancs *névfeloldóját* a kikeresni kívánt DNS-névvel együtt. A visszakapó eredmény megegyezik azzal, amit az alkalmazás kapna, miközben ugyanazt a keresést végezné. Ha nslookup-ot használ, helyette az Azure DNS-t fogja keresni.

Ha módosítja a VIRTUÁLIS HÁLÓZAT DNS-beállítását, amelyben az ASE van, újra kell indítania az ASE-t. Az ASE újraindításának elkerülése érdekében erősen ajánlott konfigurálni a DNS-beállításokat a virtuális hálózathoz az ASE létrehozása előtt.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portálfüggőségek ##

Az ASE funkcionális függőségein kívül van néhány további elem a portálélményhez kapcsolódó. Az Azure Portal egyes képességei az _SCM-webhelyközvetlen_elérésétől függenek. Az Azure App Service minden alkalmazásához két URL van. Az első URL-cím az alkalmazás elérésére szolgál. A második URL-cím az SCM webhely elérése, amelyet _Kudu konzolnak_is neveznek. Az SCM webhelyet használó szolgáltatások a következők:

-   WebJobs
-   Functions
-   Naplóstreamelés
-   Kudu
-   Bővítmények
-   Folyamatkezelő
-   Konzol

Ha egy ILB ASE használata esetén az SCM-hely nem érhető el a virtuális hálózaton kívülről. Egyes képességek nem fognak működni az alkalmazásportálon, mert hozzáférést igényelnek az alkalmazás SCM-webhelyéhez. A portál használata helyett közvetlenül is csatlakozhat az SCM-webhelyhez. 

Ha az ILB ASE a tartománynév *contoso.appserviceenvironment.net* és az alkalmazás neve *testapp,* az alkalmazás elérése *testapp.contoso.appserviceenvironment.net*. Az SCM oldalon, hogy megy vele érhető el *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>ASE IP-címek ##

Az ASE néhány IP-címmel rendelkezik, amelyeket figyelembe kell venni. Ezek a következők:

- **Nyilvános bejövő IP-cím:** Egy külső ASE-ben az alkalmazásforgalomhoz, valamint egy külső ASE-ben és egy ILB ASE-ben is használható.
- **Kimenő nyilvános IP:**"from" IP-címként használatos az ASE-ből érkező kimenő kapcsolatokhoz, amelyek elhagyják a virtuális hálózatot, amelyek nem továbbítanak egy VPN-t.
- **ILB IP-cím**: Az ILB IP-cím csak iLB ASE-ben létezik.
- **Alkalmazáshoz rendelt IP-alapú SSL-címek**: Csak külső ASE-vel lehetséges, és ip-alapú SSL-kapcsolat ban.

Ezek az IP-címek az ASE felhasználói felületéről láthatók az Azure Portalon. Ha rendelkezik ILB ASE-vel, az ILB IP-címe szerepel a listában.

   > [!NOTE]
   > Ezek az IP-címek nem változnak mindaddig, amíg az ASE működik.  Ha az ASE felfüggesztésre kerül, és visszaállítja, az ASE által használt címek megváltoznak. Az ASE felfüggesztésének szokásos oka az, ha blokkolja a bejövő felügyeleti hozzáférést, vagy blokkolja az ASE-függőséghez való hozzáférést. 

![IP-címek][3]

### <a name="app-assigned-ip-addresses"></a>Alkalmazáshoz rendelt IP-címek ###

Egy külső ASE segítségével IP-címeket rendelhet az egyes alkalmazásokhoz. Ezt nem teheted meg egy ILB ASE-vel. Ha többet szeretne tudni arról, hogy miként állíthatja be az alkalmazást saját IP-címmel, olvassa el [az Egyéni DNS-név biztonságossá tétele SSL-kötéssel az Azure App Service szolgáltatásban című témakört.](../configure-ssl-bindings.md)

Ha egy alkalmazás saját IP-alapú SSL-címmel rendelkezik, az ASE két portot foglal le az adott IP-címre való leképezéshez. Az egyik port http-forgalomhoz, a másik port pedig HTTPS-hez. Ezek a portok az IP-címek szakaszban az ASE felhasználói felületén találhatók. A forgalomnak képesnek kell lennie arra, hogy elérje ezeket a portokat a VIP-ből, különben az alkalmazások elérhetetlenek. Ezt a követelményt fontos megjegyezni a hálózati biztonsági csoportok konfigurálásakor.

## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok) ##

[A hálózati biztonsági csoportok][NSGs] lehetővé teszik a hálózati hozzáférés vezérlését a virtuális hálózaton belül. A portál használatakor van egy implicit megtagadási szabály a legalacsonyabb prioritású, hogy mindent megtagadjon. Mit épít az ön lehetővé teszi a szabályokat.

Egy ASE-ben nem rendelkezik hozzáféréssel az ASE üzemeltetéséhez használt virtuális gépekhez. Microsoft által felügyelt előfizetésben vannak. Ha korlátozni szeretné a hozzáférést az ASE-n lévő alkalmazásokhoz, állítsa be az NSG-ket az ASE alhálózaton. Ennek során fordítson különös figyelmet az ASE-függőségek. Ha blokkolja a függőségek, az ASE leáll.

Az NSG-k konfigurálhatók az Azure Portalon vagy a PowerShellen keresztül. Az itt található információk az Azure Portalt jelenítik meg. Az NSG-ket a portálon a **Hálózatkezelés**csoport legfelső szintű erőforrásaként hozza létre és kezelheti.

Az ASE működéséhez szükséges nsg-bejegyzések a következők:

**Bejövő**
* az AppServiceManagement IP-szolgáltatáscímkétől a 454 455-ös portokon
* az 16001-es port terheléselosztójától
* az ASE alhálózatról az ASE alhálózatra az összes porton

**Kimenő**
* a 123-as port összes IP-jéhez
* a 80, 443-as portok on-k összes IP-jéhez
* az AzureSQL IP-szolgáltatáscímkéhez az 1433-as portokon
* az 12000-es port összes IP-jéhez
* az ASE alhálózathoz az összes porton

A DNS-portot nem kell hozzáadni, mivel a DNS-re irányuló forgalmat nem érintik az NSG-szabályok. Ezek a portok nem tartalmazzák azokat a portokat, amelyeket az alkalmazások nak a sikeres használathoz szükségük van. A normál alkalmazás-hozzáférési portok a következők:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio távoli hibakeresés  |  4020, 4022, 4024 |
|  Webközponti telepítés szolgáltatás | 8172 |

A bejövő és kimenő követelmények figyelembevételével az NSG-knek hasonlóan kell kinézniük az ebben a példában bemutatott NSG-khez. 

![Bejövő biztonsági szabály][4]

Az alapértelmezett szabály lehetővé teszi, hogy a virtuális hálózat IP-címezési címeket az ASE alhálózat. Egy másik alapértelmezett szabály lehetővé teszi, hogy a terheléselosztó, más néven a nyilvános VIP, az ASE-vel kommunikáljon. Az alapértelmezett szabályok megtekintéséhez válassza az **Alapértelmezett szabályok** lehetőséget a **Hozzáadás** ikon mellett. Ha az alapértelmezett szabályok előtt minden más megtagadási szabályt helyez el, megakadályozza a virtuális ip-cím és az ASE közötti forgalmat. A virtuális hálózaton belüli forgalom megakadályozása érdekében adja hozzá a saját szabályát a bejövő forgalom engedélyezéséhez. Használjon az AzureLoadBalancer-rel egyenlő forrást, **amelynek** bármely célállomása és porttartománya. **\*** Mivel az NSG-szabály az ASE alhálózatra van alkalmazva, nem kell specifikusnak lennie a célhelyen.

Ha IP-címet rendelt az alkalmazáshoz, győződjön meg arról, hogy a portok nyitva maradnak. A portok megtekintéséhez válassza az **App Service-környezet** > **IP-címeit.**  

Az utolsó elem kivételével a következő kimenő szabályokban látható összes elemre szükség van. Lehetővé teszik a hálózati hozzáférést az ASE-függőségek, amelyek korábban megjegyezte, ebben a cikkben. Ha bármelyiket letiltja, az ASE nem működik. A lista utolsó eleme lehetővé teszi, hogy az ASE kommunikáljon a virtuális hálózat más erőforrásaival.

![Kimenő biztonsági szabályok][5]

Az NSG-k definiálása után rendelje hozzá őket ahhoz az alhálózathoz, amelyen az ASE van. Ha nem emlékszik az ASE virtuális hálózatvagy alhálózat, láthatja azt az ASE portál lap. Az NSG hozzárendelése az alhálózathoz, nyissa meg az alhálózati felhasználói felületet, és válassza ki az NSG-t.

## <a name="routes"></a>Útvonalak ##

Kényszerített bújtatás, amikor a virtuális hálózatban útvonalakat állít be, így a kimenő forgalom nem közvetlenül az internetre, hanem valahol máshol, például egy ExpressRoute-átjáró vagy egy virtuális berendezés.  Ha ilyen módon kell konfigurálnia az ASE-t, olvassa el [az App Service-környezet konfigurálásáról kényszerített bújtatással című dokumentumot.][forcedtunnel]  Ez a dokumentum az ExpressRoute és a kényszerített bújtatás számára elérhető lehetőségeket tartalmazza.

Amikor létrehoz egy ASE-t a portálon, hozzon létre egy útvonaltáblákat az alhálózaton, amely az ASE-vel jön létre.  Ezek az útvonalak egyszerűen azt mondják, hogy küldjön kimenő forgalmat közvetlenül az internetre.  
Ha ugyanazokat az útvonalakat manuálisan szeretné létrehozni, kövesse az alábbi lépéseket:

1. Nyissa meg az Azure Portalt. Válassza **a** > Hálózati**útvonaltáblák lehetőséget**.

2. Hozzon létre egy új útvonaltáblát ugyanabban a régióban, mint a virtuális hálózat.

3. Az útvonaltábla felhasználói felületén válassza az **Útvonalak** > **hozzáadása lehetőséget.**

4. Állítsa a **Következő ugrás típust** **internetre,** a **Cím előtagot** pedig **0.0.0.0/0-ra.** Kattintson a **Mentés** gombra.

    Ezután a következőhez hasonló tetszőséget láthat:

    ![Funkcionális útvonalak][6]

5. Az új útvonaltábla létrehozása után nyissa meg az ASE-t tartalmazó alhálózatot. Válassza ki az útvonaltáblát a portál listájából. A módosítás mentése után ekkor meg kell jelennie az alhálózattal feljegyzett NSG-knek és útvonalaknak.

    ![NSG-k és útvonalak][7]

## <a name="service-endpoints"></a>Szolgáltatásvégpontok ##

A szolgáltatásvégpontokkal Azure-beli virtuális hálózatok és alhálózatok készletére korlátozhatja a több-bérlős szolgáltatásokhoz való hozzáférést. A szolgáltatásvégpontokról bővebben a [virtuális hálózatok szolgáltatásvégpontjaival][serviceendpoints] kapcsolatos dokumentációban olvashat. 

Amikor engedélyezi a szolgáltatásvégpontokat egy erőforráson, az összes többi útvonalhoz képest nagyobb prioritású útvonalak jönnek létre. Ha bármely Azure-szolgáltatásban használja a szolgáltatásvégpontokat, kényszerített bújtatott ASE-vel, a szolgáltatások forgalma nem lesz kényszerített bújtatása. 

Amikor a szolgáltatásvégpontok engedélyezettek egy Azure SQL-példánnyal rendelkező alhálózaton, akkor az erről az alhálózatról elért összes Azure SQL-példányhoz engedélyezve kell lennie a szolgáltatásvégpontoknak. Ha több Azure SQL-példányt szeretne elérni ugyanarról az alhálózatról, nem engedélyezheti a szolgáltatásvégpontokat csak az egyik Azure SQL-példányon, egy másikon pedig nem. Nincs más Azure-szolgáltatás viselkedik, mint az Azure SQL a service endpoints tekintetében. Amikor az Azure Storage szolgáltatáshoz engedélyezi a szolgáltatásvégpontokat, azzal zárolja az erőforráshoz való hozzáférést az alhálózatról, de továbbra is elérhet más Azure Storage-fiókokat, még akkor is, ha azokon nincsenek engedélyezve a szolgáltatásvégpontok.  

![Szolgáltatásvégpontok][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
