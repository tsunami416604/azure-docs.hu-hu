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
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3939d8dce641d066a2470612068df7102b317a70
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630461"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment hálózati szempontjai #

## <a name="overview"></a>Áttekintés ##

 Azure [App Service Environment-környezet] [ Intro] üzembe helyezése az Azure App Service-ben az Azure virtuális hálózat (VNet) lévő egyik alhálózatában van. Az App Service environment (ASE) két központi telepítési típusa van:

- **Külső ASE**: Az ASE-ban üzemeltetett alkalmazásokat az internetről elérhető IP-címet tesz elérhetővé. További információkért lásd: [külső ASE létrehozása][MakeExternalASE].
- **ILB ASE**: Az IP-címet a virtuális hálózaton belül az ASE-ban üzemeltetett alkalmazásokat tesz elérhetővé. A belső végpont egy belső terheléselosztó (ILB), ezért az ILB ASE neve. További információkért lásd: [létrehozása és használata az ILB ASE][MakeILBASE].

Nincsenek App Service Environment-környezet két verziója: Az ASEv1 és ASEv2. Az ASEv1 információkért lásd: [App Service Environment v1 bemutatása][ASEv1Intro]. Az ASEv1 is üzembe helyezhetők a klasszikus vagy Resource Manager. Csak telepíthető, az Resource Manager VNet ASEv2 is.

Az ASE összes hívásait az interneten keresztül egy hozzárendelt az ASE virtuális IP-CÍMEK a virtuális hálózat hagyja üresen. A nyilvános IP-címét a virtuális IP-cím a forrás IP-címe összes híváshoz az ASE-ről, amely az interneten. Ha az alkalmazások az ASE környezetben hívásokat erőforrások a virtuális hálózat vagy virtuális Magánhálózatok, a forrás IP-címe az az alhálózatot az ASE által használt IP-egyik. Mivel az ASE-t a virtuális hálózaton belül, azt is elérhető erőforrások további konfiguráció nélkül a virtuális hálózaton belül. Ha a virtuális hálózat a helyszíni hálózathoz van csatlakoztatva, az ASE-alkalmazások is erőforrásokhoz további konfiguráció nélkül való hozzáférést.

![Külső ASE][1] 

Ha rendelkezik külső ASE környezetben, a nyilvános virtuális IP-cím is a végpontot, hogy az ASE-alkalmazások esetében:

* HTTP/S 
* FTP/S 
* A webes telepítése.
* Távoli hibakeresés.

![ILB ASE KÖRNYEZETBEN][2]

Az ILB ASE rendelkezik, a cím az ilb-e a végpont HTTP-vagy Https, FTP/S, webes telepítési és távoli hibakeresés.

A szokásos alkalmazások hozzáférési portok a következők:

| Használat | Ettől: | Művelet |
|----------|---------|-------------|
|  HTTP/HTTPS  | Felhasználó által konfigurálható |  80, 443 |
|  FTP/FTPS    | Felhasználó által konfigurálható |  21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresés  |  Felhasználó által konfigurálható |  4020, 4022, 4024 |

Ez akkor igaz, ha Ön a külső ASE vagy ILB ASE környezetben. Ha Ön a külső ASE környezetben, ezeket a portokat a nyilvános VIP-címet a eléri. Ha Ön az ILB ASE, nyomja le az ILB meg ezeket a portokat. 443-as porton zárolását, ha a portálon elérhető egyes funkciók hatással lehet. További információkért lásd: [portál függőségek](#portaldep).

## <a name="ase-subnet-size"></a>ASE alhálózat mérete ##

Az ASE-környezetek üzemeltetéséhez használt alhálózatok mérete az ASE-környezet üzembe helyezése után már nem módosítható.  Az ASE minden infrastruktúra-szerepkör, valamint meghajtóbetűjeleket izolált App Service-csomag mindegyiknek egy címet használ.  Emellett nincsenek igénylő összes alhálózat létrehozott Azure-hálózat által használt 5 címeket.  Nincsenek App Service-csomagok egy ASE minden 12-címeket fogják használni az alkalmazás létrehozása előtt.  Ha az ILB ASE majd fogja használni 13 címek ahhoz, hogy az ASE környezetben létrehoz egy alkalmazást. Horizontális felskálázás az ASE-t, infrastruktúra-szerepkörök kerülnek minden 15 – 20 az App Service-csomag üzemelő példányok többszöröse.

   > [!NOTE]
   > Semmi más lehet az alhálózaton, de az ASE-t. Mindenképp válassza ki a jövőbeli növekedést is lehetővé teszi egy címtér. Ezt a beállítást később nem módosítható. Azt javasoljuk, hogy egy méretű `/24` 256-címekkel.

Kisebbre vagy nagyobbra méretezhetők, ha hozzáadja az új szerepköröket a megfelelő méretű, és ezután a számítási feladatokhoz való migrálásakor a jelenlegi mérete a cél méretét. Csak azt követően az alkalmazások áttelepítése az eredeti virtuális törlődnek. Ez azt jelenti, hogy ha egy 100 ASP-példányok az ASE lenne egy ideig kell double a virtuális gépek száma.  Emiatt, amelyek használatát javasoljuk, hogy van egy (/ 24) befogadásához a szükséges módosításokat.  

## <a name="ase-dependencies"></a>ASE függőségek ##

### <a name="ase-inbound-dependencies"></a>ASE bejövő függőségek ###

Az ASE bejövő hozzáférést függőségei vannak:

| Használat | Ettől: | Művelet |
|-----|------|----|
| Kezelés | App Service felügyeleti címek | ASE alhálózat: a 454, 455 |
|  Belső ASE-kommunikáció | ASE alhálózat: Minden port | ASE alhálózat: Minden port
|  Lehetővé teszi az Azure load balancer bejövő | Azure Load Balancer | ASE alhálózat: Minden port
|  Az alkalmazás IP-címek | Alkalmazás-címek | ASE alhálózat: Minden port

A bejövő felügyeleti forgalmak parancs és vezérlés figyelése mellett az ASE biztosít. Ezt a forgalmat a forrás-címek szerepelnek a [ASE felügyeleti címek] [ ASEManagement] dokumentumot. A hálózati biztonsági konfiguráció engedélyezi a hozzáférést a 454 és a 455 portot az összes IP-címekről származó kell. Ha letiltja a hozzáférést a ezt a címet, az ASE nem megfelelő állapotú lesz, és ezután lesz felfüggesztve.

Az ASE-alhálózatra belső összetevő használt számos port belüli kommunikációt, és módosíthatja.  Ehhez szükséges összes port az ASE alhálózat elérni az ASE-alhálózatra. 

Az Azure load balancer és az ASE-alhálózatra közötti kommunikációhoz a minimálisan portokat kell megnyitni a 454, 455 és 16001. Életben tartási forgalom között a terheléselosztó és az ASE a 16001 port szolgál. Ha ILB ASE környezetben használja, akkor le csak a 454, 455, 16001 forgalom zárolhatja portokat.  Ha külső ASE használ, akkor figyelembe kell venni a szokásos alkalmazások hozzáférési portok.  Alkalmazáshoz hozzárendelt címek használatakor meg kell nyitnia azt összes portja.  Amikor egy cím hozzá van rendelve egy adott alkalmazást, a terheléselosztó portok nem ismert, előzetesen HTTP és HTTPS-forgalom küldéséhez az ASE-t fogja használni.

Ha az alkalmazások az ASE-alhálózathoz rendelt IP-címekről érkező forgalom engedélyezésére kell IP-címek alkalmazás használ.

A TCP-forgalom, amely porton 454 és a 455 kell lépjen vissza az azonos virtuális IP-cím a, vagy az aszimmetrikus útválasztás problémát kell. 

### <a name="ase-outbound-dependencies"></a>Kimenő ASE-függőségek ###

A kimenő hozzáféréshez az ASE több külső rendszerek függ. Ezeket a rendszer függőségeket számos DNS-nevük van definiálva, és nem leképezése az IP-címek készletét. Így az ASE portok többféle minden külső IP-címet az ASE alhálózatról kimenő hozzáférésre van szüksége. 

Kimenő függőségek teljes listáját, amely leírja a dokumentumban felsorolt [App Service Environment-környezet kimenő forgalom meghatározott sémákra kelljen](./firewall-integration.md). Ha az ASE elveszítette a hozzáférését annak függőségeit, a rendszer nem működik. Ebben az esetben elég hosszú az ASE fel van függesztve. 

### <a name="customer-dns"></a>Ügyfél DNS ###

Ha egy felhasználó által meghatározott DNS-kiszolgáló a virtuális hálózaton van beállítva, a bérlők munkaterheléseihez használhatja. Az ASE-t továbbra is az Azure DNS használata felügyeleti célokra közötti kommunikációhoz szükséges. 

Ha a virtuális hálózat úgy van konfigurálva, a DNS, a másik oldalon egy VPN-ügyfél, a DNS-kiszolgáló az ASE Környezetet tartalmazó alhálózat elérhetőnek kell lennie.

A webalkalmazás megoldás teszteléséhez használhatja a konzol parancs *nameresolver*. Az scm-webhelyen, az alkalmazás hibakeresési ablakában nyissa meg vagy nyissa meg az alkalmazás a portálon, és válassza a konzolon. A rendszerhéj parancssorában adja ki a parancsot *nameresolver* a cím megtekinteni kívánt együtt. Újból eredménye ugyanaz, mint amit az alkalmazás kap az azonos keresés közben. Ha az nslookup fogja végrehajtani a keresést, az Azure DNS használata esetén inkább használja.

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

Az ILB ASE használata esetén az SCM helyet nem a virtuális hálózaton kívülről elérhető az internetről. Az alkalmazás az ILB ASE jöhet szóba, ha bizonyos funkciók nem működnek a portálról.  

Ezen képességek, amelyek az SCM helyet számos is elérhetők közvetlenül a Kudu konzolhoz. A portál használata helyett közvetlenül is csatlakoztathatja. Ha az alkalmazás üzemel az ILB ASE környezetben, jelentkezzen be a közzétételi hitelesítő adatai segítségével. Az SCM helyet az ILB ASE környezetben üzemeltetett alkalmazás elérésére az URL-cím formátuma a következő: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Ha az ILB ASE tartománynév *contoso.net* és az alkalmazás neve *testapp*, az alkalmazás elérése a *testapp.contoso.net*. Az SCM helyet, amely vele együtt áthelyeződik elérésekor *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Functions és Webjobs-feladatok ##

Funkciók és a webes feladatok az SCM helyet függ, de akkor is, ha alkalmazásait az ILB ASE, mindaddig, amíg a böngésző elérheti az SCM helyet a portál használata támogatott.  Ha önaláírt tanúsítványt használ az ILB ASE környezetnek, szüksége lesz ahhoz, hogy a böngészőben, hogy bízzon meg a tanúsítvány.  Az Internet Explorer és a peremhálózati, amely azt jelenti, hogy a tanúsítvány nem megbízható számítógép tárolójában lehet.  Ha a Chrome böngészőt használ, akkor azt jelenti, hogy Ön elfogadta a tanúsítványt a böngészőben korábban elvileg lenyomásával közvetlenül az scm helyet.  A legjobb megoldás, ha szerepel a böngészője megbízhatósági láncának egyik kereskedelmi tanúsítványát használja.  

## <a name="ase-ip-addresses"></a>ASE IP-címek ##

Az ASE érdemes figyelembe vennie néhány IP-címmel rendelkezik. Ezek a következők:

- **Nyilvános bejövő IP-cím**: Külső ASE app-forgalom és felügyeleti forgalom külső ASE környezetben és a egy ILB ASE környezetben is használható.
- **Kimenő nyilvános IP-cím**: Használja a kimenő kapcsolatok számára az ASE-ről, amely a virtuális hálózathoz, hagyja le a VPN nem irányítja, amely a "feladó" IP-cím.
- **ILB IP-cím**: Ha az ILB ASE használata.
- **Alkalmazáshoz hozzárendelt IP-alapú SSL-címeket**: Csak akkor lehetséges a külső ASE környezetben, és ha IP-alapú SSL van konfigurálva.

Ezen IP-címek az Azure Portalon az ASEv2 jól látható, az ASE felhasználói felületen. Ha az ILB ASE rendelkezik, az ILB-hez a IP-cím szerepel a listán.

   > [!NOTE]
   > Ezen IP-címek mindaddig, amíg az ASE marad, és nem változik.  Ha az ASE lesz felfüggesztve, és visszaállítani, a címeket, az ASE által használt változik. A normál az ASE fel lesz függesztve, oka a Ha a bejövő felügyeleti hozzáférés letiltása, vagy blokkolhatja az ASE függ. 

![IP-címek][3]

### <a name="app-assigned-ip-addresses"></a>Alkalmazás által kiosztott IP-címek ###

Külső ASE környezetben, az IP-címeket rendelhet az egyes alkalmazások. ILB ASE környezetben, nem tudja megtenni. Az alkalmazás a saját IP-cím konfigurálása a további információkért lásd: [meglévő egyéni SSL-tanúsítvány kötése az Azure App Service-](../app-service-web-tutorial-custom-ssl.md).

Amikor egy alkalmazást a saját IP-alapú SSL-címmel rendelkezik, az ASE fenntartja két port adott IP-címére való leképezéséhez. Egy port a HTTP-forgalom, és a más port HTTPS-hez van. Ezeket a portokat az ASE UI, az IP-címek szakaszban szerepelnek. Forgalom elérhetik ezeket a portokat a virtuális IP-cím a kell lennie, vagy az alkalmazások elérhetetlenné válnak. Ez a követelmény nem szabad elfelejteni, amikor konfigurálja a hálózati biztonsági csoportok (NSG-k).

## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok) ##

[Hálózati biztonsági csoportok] [ NSGs] lehetővé teszi, hogy a virtuális hálózaton belüli hálózati hozzáférést szabályozzák. Ha a portál, az implicit Megtagadás szabály jelenleg a legalacsonyabb prioritású megtagadni minden. Állít össze vannak az engedélyezési szabály.

Az ASE környezetben nincs hozzáférése a virtuális gépekhez az ASE-t üzemeltetik. Jelenleg a Microsoft által felügyelt előfizetés. Ha azt szeretné, az ASE az alkalmazásokhoz való hozzáférés korlátozásához, beállítani az NSG-ket az ASE-alhálózatra. Ennek során az ASE függőségek alapos figyelmet fordítania. Ha letiltja a függőségek, akkor az ASE nem működik.

Az NSG-k konfigurálható az Azure Portalon vagy a Powershellen keresztül. Az adatok itt látható az Azure Portalon. Hozzon létre, és NSG-k kezelése a portálon, a legfelső szintű erőforrásként **hálózatkezelés**.

A bejövő és kimenő követelményeket figyelembe kell venni, amikor az NSG-k az NSG-t az ebben a példában is látható hasonlóan kell kinéznie. A virtuális hálózaton címtartomány _192.168.250.0/23_, és az alhálózatot, amelyet a rendszer az ASE _192.168.251.128/25_.

Az első két bejövő követelményei az ASE függvény ebben a példában a lista tetején jelennek meg. Azok ASE felügyeletének engedélyezése, és lehetővé teszi az ASE kommunikálni magát. A többi bejegyzések konfigurálható minden bérlő, és szabályozhatja a hálózati hozzáférés az ASE-ban üzemeltetett alkalmazások számára. 

![Bejövő biztonsági szabály][4]

Alapértelmezett szabály lehetővé teszi, hogy kommunikáljon az ASE-alhálózattal a virtuális hálózaton az IP-címek. Egy másik alapértelmezett szabály lehetővé teszi, hogy a terheléselosztó, más néven a nyilvános VIP az ASE folytatott kommunikációhoz. Az alapértelmezett szabályok megtekintéséhez válasszon **alapértelmezett szabályokkal** mellett a **Hozzáadás** ikonra. Ha minden más szabály, miután az NSG-szabályok megjelenített megtagadási helyezi, megakadályozható, hogy a virtuális IP-cím és az ASE közötti adatforgalmat. A virtuális hálózaton belül érkező forgalom elkerülése érdekében adja hozzá a saját szabályt, amely engedélyezi a bejövő. Egy adatforrás AzureLoadBalancer egyenlő használata a célhelyre **bármely** és a egy porttartományt, **\***. Az ASE-alhálózatra alkalmazott NSG-szabályt, mert nem kell lennie a cél adott.

Ha az alkalmazáshoz rendelt IP-címet, ellenőrizze, hogy a portok nyitva hagyja. A portok megtekintéséhez válasszon **App Service Environment-környezet** > **IP-címek**.  

A következő kimenő szabályok látható összes elem van szükség, az utolsó elem kivételével. A cikk korábbi részeiben észleltek ASE függőségek való hálózati hozzáférés lehetővé teszik. Ha ezek közül bármelyik letiltja, az ASE működése leáll. Az utolsó elem a lista lehetővé teszi, hogy az ASE-t a virtuális hálózatában lévő más erőforrásokkal kommunikálni.

![Kimenő biztonsági szabályok][5]

Az NSG-k meghatározása után hozzárendelheti azokat az alhálózatot, amelyet a rendszer az ASE-t. Ha nem emlékszik az ASE Vnetben vagy alhálózatban, az ASE portál lapján láthatja. Az NSG hozzárendelése az alhálózat, nyissa meg az alhálózat felhasználói felületén, és válassza az NSG-t.

## <a name="routes"></a>Útvonalak ##

Kényszerített bújtatás az útvonalakat a virtuális hálózat beállításakor, így a kimenő forgalom nem lépjen közvetlenül az internethez, de valahol máshol, például egy ExpressRoute-átjáróval vagy virtuális berendezésre.  Ha szeretné az ASE konfigurálása oly módon, majd olvassa el a dokumentumot a [az App Service Environment konfigurálása kényszerített bújtatással][forcedtunnel].  Ez a dokumentum jelzi az ExpressRoute és a kényszerített bújtatás használata lehetőségekről.

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

Amikor engedélyezi a szolgáltatásvégpontokat egy erőforráson, az összes többi útvonalhoz képest nagyobb prioritású útvonalak jönnek létre. Ha kényszerítetten bújtatott ASE-vel használ szolgáltatásvégpontokat, az Azure SQL és az Azure Storage felügyeleti forgalma nem kényszerítetten bújtatott. 

Amikor a szolgáltatásvégpontok engedélyezettek egy Azure SQL-példánnyal rendelkező alhálózaton, akkor az erről az alhálózatról elért összes Azure SQL-példányhoz engedélyezve kell lennie a szolgáltatásvégpontoknak. Ha több Azure SQL-példányt szeretne elérni ugyanarról az alhálózatról, nem engedélyezheti a szolgáltatásvégpontokat csak az egyik Azure SQL-példányon, egy másikon pedig nem. Az Azure Storage nem úgy viselkedik, mint az Azure SQL. Amikor az Azure Storage szolgáltatáshoz engedélyezi a szolgáltatásvégpontokat, azzal zárolja az erőforráshoz való hozzáférést az alhálózatról, de továbbra is elérhet más Azure Storage-fiókokat, még akkor is, ha azokon nincsenek engedélyezve a szolgáltatásvégpontok.  

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
