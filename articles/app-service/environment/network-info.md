---
title: Hálózati megfontolások az App Service-környezet – Azure
description: Ismerteti az ASE-hálózati forgalom és NSG-ket és az ASE az udr-EK beállítása
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
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3f80f3c6be747cf84aa9d8b2c386c0568a7511ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069381"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment hálózati szempontjai #

## <a name="overview"></a>Áttekintés ##

 Azure [App Service Environment-környezet] [ Intro] üzembe helyezése az Azure App Service-ben az Azure virtuális hálózat (VNet) lévő egyik alhálózatában van. Az App Service environment (ASE) két központi telepítési típusa van:

- **Külső ASE**: Az ASE-ban üzemeltetett alkalmazásokat az internetről elérhető IP-címet tesz elérhetővé. További információkért lásd: [külső ASE létrehozása][MakeExternalASE].
- **ILB ASE**: Az IP-címet a virtuális hálózaton belül az ASE-ban üzemeltetett alkalmazásokat tesz elérhetővé. A belső végpont egy belső terheléselosztó (ILB), ezért az ILB ASE neve. További információkért lásd: [létrehozása és használata az ILB ASE][MakeILBASE].

Az összes ASE, a külső és a ILB, van egy nyilvános VIP-t, amely a bejövő felügyeleti forgalmak és, a feladó címe az interneten hívása az ASE-ről esetén. Az ASE hívást, amely az interneten hagyja meg a virtuális hálózat és az ASE hozzárendelt virtuális IP-címén. A nyilvános IP-címét a virtuális IP-cím a forrás IP-címe összes híváshoz az ASE-ről, amely az interneten. Ha az alkalmazások az ASE környezetben hívásokat erőforrások a virtuális hálózat vagy virtuális Magánhálózatok, a forrás IP-címe az az alhálózatot az ASE által használt IP-egyik. Mivel az ASE-t a virtuális hálózaton belül, azt is elérhető erőforrások további konfiguráció nélkül a virtuális hálózaton belül. Ha a virtuális hálózat a helyszíni hálózathoz van csatlakoztatva, az ASE-alkalmazások is erőforrásokhoz további konfiguráció nélkül való hozzáférést.

![Külső ASE][1] 

Ha rendelkezik külső ASE környezetben, a nyilvános virtuális IP-cím is a végpontot, hogy az ASE-alkalmazások esetében:

* HTTP/S 
* FTP/S
* Web deployment
* Távoli hibakeresés

![ILB ASE][2]

Ha az ILB ASE rendelkezik, a az ILB-címmel címe HTTP-vagy Https, FTP/S, webes telepítési és távoli hibakeresés végpontját.

## <a name="ase-subnet-size"></a>ASE alhálózat mérete ##

Az ASE üzemeltetéséhez használt alhálózat mérete nem módosítható, az ASE üzembe helyezése után.  Az ASE minden infrastruktúra-szerepkör, valamint meghajtóbetűjeleket izolált App Service-csomag mindegyiknek egy címet használ.  Emellett nincsenek igénylő összes alhálózat létrehozott Azure-hálózat által használt öt címet.  Nincsenek App Service-csomagok egy ASE minden 12-címeket fogják használni az alkalmazás létrehozása előtt.  Ha az ILB ASE, majd fogja használni 13 címek ahhoz, hogy az ASE környezetben létrehoz egy alkalmazást. Horizontális felskálázás az ASE-t, infrastruktúra-szerepkörök kerülnek minden 15 – 20 az App Service-csomag üzemelő példányok többszöröse.

   > [!NOTE]
   > Semmi más lehet az alhálózaton, de az ASE-t. Mindenképp válassza ki a jövőbeli növekedést is lehetővé teszi egy címtér. Ezt a beállítást később nem módosítható. Azt javasoljuk, hogy egy méretű `/24` 256-címekkel.

Kisebbre vagy nagyobbra méretezhetők, ha hozzáadja az új szerepköröket a megfelelő méretű, és ezután a számítási feladatokhoz való migrálásakor a jelenlegi mérete a cél méretét. Az eredeti virtuális gépeket távolítja el, csak azt követően a munkaterhelések áttelepítése megtörtént. Ha az ASE-100 ASP-példányok, lenne egy ideig kell double a virtuális gépek száma.  Emiatt, amelyek használatát javasoljuk, hogy van egy (/ 24) befogadásához a szükséges módosításokat.  

## <a name="ase-dependencies"></a>ASE függőségek ##

### <a name="ase-inbound-dependencies"></a>ASE bejövő függőségek ###

Csak a működéséhez az ASE az ASE-t a következő portokat kell nyitva van szükség:

| Használat | Forrás | Cél |
|-----|------|----|
| Kezelés | App Service management addresses | ASE alhálózat: 454, 455 |
|  Belső ASE-kommunikáció | ASE alhálózat: Minden port | ASE alhálózat: Minden port
|  Lehetővé teszi az Azure load balancer bejövő | Azure Load Balancer | ASE alhálózat: 16001

Nincsenek 2 más portok port beolvasási, 7654 és 1221, nyissa meg tud jeleníteni. Az IP-címet, semmi további válaszoljon rá. Ha megfelelő blokkolható. 

A bejövő felügyeleti forgalmak parancs és vezérlés figyelése mellett az ASE biztosít. Ezt a forgalmat a forrás-címek szerepelnek a [ASE felügyeleti címek] [ ASEManagement] dokumentumot. A hálózati biztonsági beállításokat kell engedélyezze a hozzáférést a 454 és a 455 portot az ASE kezelési címeit. Ha letiltja a hozzáférést a ezt a címet, az ASE nem megfelelő állapotú lesz, és ezután lesz felfüggesztve. A TCP-forgalom, amely porton 454 és a 455 kell lépjen vissza az azonos virtuális IP-cím a, vagy az aszimmetrikus útválasztás problémát kell. 

Az ASE-alhálózatra belül számos, a belső összetevő folytatott kommunikációhoz használt portokat és módosíthatják. Ehhez szükséges összes port az ASE alhálózat elérni az ASE-alhálózatra. 

Az Azure load balancer és az ASE-alhálózatra közötti kommunikációhoz a minimálisan portokat kell megnyitni a 454, 455 és 16001. Életben tartási forgalom között a terheléselosztó és az ASE a 16001 port szolgál. ILB ASE környezetben használja, akkor csak a 454, 455, 16001 lefelé forgalom zárolhatja portokat.  Ha külső ASE környezetben használja, majd meg kell figyelembe kell venni a szokásos alkalmazások hozzáférési portok.  

Szüksége van ahhoz, hogy az a többi porton legyenek az alkalmazás portok:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresés  |  4020, 4022, 4024 |
|  Webszolgáltatás üzembe helyezése | 8172 |

Ha az alkalmazás portokat letiltja, az ASE-t továbbra is működőképesek, de az alkalmazás nem lehet.  Ha a külső ASE app rendelt IP-címeket használ, szüksége lesz az alkalmazások az ASE-alhálózattal, az ASE portál látható portokon rendelt IP-címekről érkező forgalom engedélyezéséhez > IP-címek lapra.

### <a name="ase-outbound-dependencies"></a>Kimenő ASE-függőségek ###

A kimenő hozzáféréshez az ASE több külső rendszerek függ. Ezeket a rendszer függőségeket számos DNS-nevük van definiálva, és nem leképezése az IP-címek készletét. Így az ASE portok többféle minden külső IP-címet az ASE alhálózatról kimenő hozzáférésre van szüksége. 

Az ASE ki internetes elérhető címek a következő portokon kommunikál:

| Felhasználások | Portok |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL, a Windows-frissítéseket, a Linux-függőségeket, a Azure-szolgáltatások | 80/443 |
| Azure SQL | 1433 | 
| Figyelés | 12000 |

A kimenő függőségek szerepelnek a dokumentum leírja [App Service Environment-környezet kimenő forgalom meghatározott sémákra kelljen](./firewall-integration.md). Ha az ASE elveszítette a hozzáférését annak függőségeit, a rendszer nem működik. Ebben az esetben elég hosszú az ASE fel van függesztve. 

### <a name="customer-dns"></a>Customer DNS ###

Ha egy felhasználó által meghatározott DNS-kiszolgáló a virtuális hálózaton van beállítva, a bérlők munkaterheléseihez használhatja. Az ASE az Azure DNS-felügyeleti célokra használja. Ha egy ügyfél által kiválasztott DNS-kiszolgáló a virtuális hálózaton van beállítva, a DNS-kiszolgáló az ASE Környezetet tartalmazó alhálózat elérhetőnek kell lennie.

A webalkalmazás DNS-névfeloldás tesztelése, használhatja a konzol parancs *nameresolver*. Az scm-webhelyen, az alkalmazás hibakeresési ablakában nyissa meg vagy nyissa meg az alkalmazás a portálon, és válassza a konzolon. A rendszerhéj parancssorában adja ki a parancsot *nameresolver* együtt keresse ki szeretné a DNS-név. Újból eredménye ugyanaz, mint amit az alkalmazás kap az azonos keresés közben. Ha használja az nslookup, használja helyette az Azure DNS-keresési fogja végrehajtani.

Ha módosítja a DNS-beállításainak a virtuális hálózat, amely az ASE-t, szüksége lesz az ASE újraindítás. Az ASE azzal elkerülheti, azt javasoljuk, hogy az ASE létrehozása előtt a virtuális hálózat DNS-beállításainak konfigurálása.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portál függőségek ##

Az ASE működési függőségek kívül a portál használatával kapcsolatos néhány további elemek. Az Azure Portalon funkciói függenek közvetlen hozzáférést _SCM helyet_. Minden alkalmazás Azure App Service-ben a két URL-címek vannak. Az első URL-címet, hogy az alkalmazás eléréséhez. A második URL-je eléréséhez az SCM helyet, más néven a _Kudu konzol_. Az SCM helyet használó szolgáltatások a következők:

-   Webjobs-feladatok
-   Functions
-   Naplózási streaming
-   Kudu
-   Bővítmények
-   Process Explorer
-   Konzol

Az ILB ASE használata esetén az SCM helyet nem érhetők el a virtuális hálózaton kívülről. Bizonyos funkciók nem fog működni az alkalmazás portálról, mert ilyen esetben az SCM helyet, az alkalmazás elérését. Az SCM helyet, a portál helyett közvetlenül csatlakozhat. 

Ha az ILB ASE tartománynév *contoso.appserviceenvironnment.net* és az alkalmazás neve *testapp*, az alkalmazás elérése a *testapp.contoso.appserviceenvironment.net*. Az SCM helyet, amely vele együtt áthelyeződik elérésekor *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>ASE IP addresses ##

An ASE has a few IP addresses to be aware of. Ezek a következők:

- **Nyilvános bejövő IP-cím**: Külső ASE app-forgalom és felügyeleti forgalom külső ASE környezetben és a egy ILB ASE környezetben is használható.
- **Kimenő nyilvános IP-cím**: Használja a kimenő kapcsolatok számára az ASE-ről, amely a virtuális hálózathoz, hagyja le a VPN nem irányítja, amely a "feladó" IP-cím.
- **ILB IP-cím**: Az ILB IP-cím csak létezik az ILB ASE környezetben.
- **Alkalmazáshoz hozzárendelt IP-alapú SSL-címeket**: Csak akkor lehetséges a külső ASE környezetben, és ha IP-alapú SSL van konfigurálva.

Ezen IP-címek láthatók az Azure Portalon az ASE felhasználói felületen. Ha az ILB ASE rendelkezik, az ILB-hez a IP-cím szerepel a listán.

   > [!NOTE]
   > Ezen IP-címek mindaddig, amíg az ASE marad, és nem változik.  Ha az ASE lesz felfüggesztve, és visszaállítani, a címeket, az ASE által használt változik. A normál az ASE fel lesz függesztve, oka a Ha a bejövő felügyeleti hozzáférés letiltása, vagy blokkolhatja az ASE függ. 

![IP-címek][3]

### <a name="app-assigned-ip-addresses"></a>App-assigned IP addresses ###

Külső ASE környezetben, az IP-címeket rendelhet az egyes alkalmazások. ILB ASE környezetben, nem tudja megtenni. Az alkalmazás a saját IP-cím konfigurálása a további információkért lásd: [meglévő egyéni SSL-tanúsítvány kötése az Azure App Service-](../app-service-web-tutorial-custom-ssl.md).

Amikor egy alkalmazást a saját IP-alapú SSL-címmel rendelkezik, az ASE fenntartja két port adott IP-címére való leképezéséhez. Egy port a HTTP-forgalom, és a más port HTTPS-hez van. Ezeket a portokat az ASE UI, az IP-címek szakaszban szerepelnek. Forgalom elérhetik ezeket a portokat a virtuális IP-cím a kell lennie, vagy az alkalmazások elérhetetlenné válnak. Ez a követelmény nem szabad elfelejteni, amikor konfigurálja a hálózati biztonsági csoportok (NSG-k).

## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok) ##

[Hálózati biztonsági csoportok] [ NSGs] lehetővé teszi, hogy a virtuális hálózaton belüli hálózati hozzáférést szabályozzák. Ha a portál, az implicit Megtagadás szabály jelenleg a legalacsonyabb prioritású megtagadni minden. Állít össze vannak az engedélyezési szabály.

Az ASE környezetben nincs hozzáférése a virtuális gépekhez az ASE-t üzemeltetik. Jelenleg a Microsoft által felügyelt előfizetés. Ha azt szeretné, az ASE az alkalmazásokhoz való hozzáférés korlátozásához, beállítani az NSG-ket az ASE-alhálózatra. Ennek során az ASE függőségek alapos figyelmet fordítania. Ha letiltja a függőségek, akkor az ASE nem működik.

Az NSG-k konfigurálható az Azure Portalon vagy a Powershellen keresztül. Az adatok itt látható az Azure Portalon. Hozzon létre, és NSG-k kezelése a portálon, a legfelső szintű erőforrásként **hálózatkezelés**.

A szükséges NSG-t, az ASE függvénynek, bejegyzései adatforgalom engedélyezéséhez:

**Bejövő**
* az IP-címről szolgáltatáscímke AppServiceManagement 454,455 portokon.
* 16001 porton a terheléselosztóból
* az összes porton az ASE-alhálózatra az ASE alhálózat

**Kimenő**
* az összes IP-címek 123 porton
* a portok: 80-as, 443-as porton lévő összes IP-címek
* az IP-címhez szolgáltatáscímke AzureSQL 1433-as porton
* az összes IP-címek 12000 port
* az összes porton az ASE-alhálózattal

A DNS-port nem kell hozzáadni, a DNS-forgalom NSG-szabályok nem érvényesek. Ezeket a portokat nem tartalmazzák a portokkal, amelyeken az alkalmazások sikeres használata szükséges. A szokásos alkalmazások hozzáférési portok a következők:

| Használat | Portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresés  |  4020, 4022, 4024 |
|  Webszolgáltatás üzembe helyezése | 8172 |

A bejövő és kimenő követelményeket figyelembe kell venni, amikor az NSG-k az NSG-t az ebben a példában is látható hasonlóan kell kinéznie. 

![Bejövő biztonsági szabály][4]

Alapértelmezett szabály lehetővé teszi, hogy kommunikáljon az ASE-alhálózattal a virtuális hálózaton az IP-címek. Egy másik alapértelmezett szabály lehetővé teszi, hogy a terheléselosztó, más néven a nyilvános VIP az ASE folytatott kommunikációhoz. Az alapértelmezett szabályok megtekintéséhez válasszon **alapértelmezett szabályokkal** mellett a **Hozzáadás** ikonra. Ha minden más szabály, mielőtt az alapértelmezett szabályok megtagadási helyezi, megakadályozható, hogy a virtuális IP-cím és az ASE közötti adatforgalmat. A virtuális hálózaton belül érkező forgalom elkerülése érdekében adja hozzá a saját szabályt, amely engedélyezi a bejövő. Egy adatforrás AzureLoadBalancer egyenlő használata a célhelyre **bármely** és a egy porttartományt, **\*** . Az ASE-alhálózatra alkalmazott NSG-szabályt, mert nem kell lennie a cél adott.

Ha az alkalmazáshoz rendelt IP-címet, ellenőrizze, hogy a portok nyitva hagyja. A portok megtekintéséhez válasszon **App Service Environment-környezet** > **IP-címek**.  

A következő kimenő szabályok látható összes elem van szükség, az utolsó elem kivételével. A cikk korábbi részeiben észleltek ASE függőségek való hálózati hozzáférés lehetővé teszik. Ha ezek közül bármelyik letiltja, az ASE működése leáll. Az utolsó elem a lista lehetővé teszi, hogy az ASE-t a virtuális hálózatában lévő más erőforrásokkal kommunikálni.

![Kimenő biztonsági szabályok][5]

Az NSG-k meghatározása után hozzárendelheti azokat az alhálózatot, amelyet a rendszer az ASE-t. Ha nem emlékszik az ASE Vnetben vagy alhálózatban, az ASE portál lapján láthatja. Az NSG hozzárendelése az alhálózat, nyissa meg az alhálózat felhasználói felületén, és válassza az NSG-t.

## <a name="routes"></a>Útvonalak ##

Kényszerített bújtatás az útvonalakat a virtuális hálózat beállításakor, így a kimenő forgalom nem lépjen közvetlenül az internethez, de valahol máshol, például egy ExpressRoute-átjáróval vagy virtuális berendezésre.  Ha szeretné az ASE konfigurálása oly módon, majd olvassa be a dokumentumot a [az App Service Environment konfigurálása kényszerített bújtatással][forcedtunnel].  Ez a dokumentum jelzi az ExpressRoute és a kényszerített bújtatás használata lehetőségekről.

Az ASE létrehozásakor a portálon is létrehozunk útvonal táblák egy készlete, amely az ASE-t a rendszer létrehozza az alhálózaton.  Útvonalakat egyszerűen mondja ki a kimenő forgalom küldése közvetlenül az internethez.  
Az azonos útvonalakat manuális létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg az Azure Portalt. Válassza ki **hálózatkezelés** > **útválasztási táblázatok**.

2. Hozzon létre egy új útvonaltábla a virtuális hálózat ugyanabban a régióban.

3. Az útvonaltábla felhasználói felületén belül válassza **útvonalak** > **Hozzáadás**.

4. Állítsa be a **következő ugrás típusa** való **Internet** és a **címelőtag** való **0.0.0.0/0**. Kattintson a **Mentés** gombra.

    Ekkor megjelenik a következőhöz hasonló:

    ![Funkcionális útvonalak][6]

5. Miután létrehozta az új útvonaltábla, nyissa meg az alhálózatot, amelyet az ASE-t tartalmaz. Válassza ki az útvonaltábla a portálon a listából. Miután a módosítás mentéséhez, majd megtekintheti az NSG-ket és a kéréssel együtt az alhálózati útvonalakat.

    ![NSG-ket és útvonalak][7]

## <a name="service-endpoints"></a>Service Endpoints – szolgáltatásvégpont ##

A szolgáltatásvégpontokkal Azure-beli virtuális hálózatok és alhálózatok készletére korlátozhatja a több-bérlős szolgáltatásokhoz való hozzáférést. A szolgáltatásvégpontokról bővebben a [virtuális hálózatok szolgáltatásvégpontjaival][serviceendpoints] kapcsolatos dokumentációban olvashat. 

Amikor engedélyezi a szolgáltatásvégpontokat egy erőforráson, az összes többi útvonalhoz képest nagyobb prioritású útvonalak jönnek létre. Ha a Szolgáltatásvégpontok bármely Azure-szolgáltatás, amelynek kényszerítetten bújtatott ASE, használja ezeket a szolgáltatásokat a forgalom nem kényszeríti a bújtatott. 

Amikor a szolgáltatásvégpontok engedélyezettek egy Azure SQL-példánnyal rendelkező alhálózaton, akkor az erről az alhálózatról elért összes Azure SQL-példányhoz engedélyezve kell lennie a szolgáltatásvégpontoknak. Ha több Azure SQL-példányt szeretne elérni ugyanarról az alhálózatról, nem engedélyezheti a szolgáltatásvégpontokat csak az egyik Azure SQL-példányon, egy másikon pedig nem. Nincs más Azure-szolgáltatás garanciát a Szolgáltatásvégpontok Azure SQL viselkedik. Amikor az Azure Storage szolgáltatáshoz engedélyezi a szolgáltatásvégpontokat, azzal zárolja az erőforráshoz való hozzáférést az alhálózatról, de továbbra is elérhet más Azure Storage-fiókokat, még akkor is, ha azokon nincsenek engedélyezve a szolgáltatásvégpontok.  

![Service Endpoints – szolgáltatásvégpont][8]

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
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
