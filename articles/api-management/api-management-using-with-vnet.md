---
title: A virtuális hálózatok az Azure API Management használata
description: Megtudhatja, hogyan állíthatja be az Azure API Management- és hozzáférés webszolgáltatásokban történő egy virtuális hálózati kapcsolatot.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: dd876462559ac10fc0463c64413bf11eabbc88a1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443525"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>A virtuális hálózatok az Azure API Management használata
Az Azure virtuális hálózatok (Vnetek) helyezni, az Azure-erőforrások bármelyikét elérését Ön szabályozza a nem internet routeable hálózat teszi lehetővé. Ezek a hálózatok csatlakozhat a helyszíni hálózatokhoz való kapcsolódásának VPN különböző technológiákat. További információ az Azure-beli virtuális hálózatok Kezdje itt az adatokat további: [Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

Az Azure API Management is üzembe helyezhetők a virtuális hálózaton (VNET), hogy hozzá tudjon férni a háttérszolgáltatások a hálózaton belül. A fejlesztői portál és API-átjáró, konfigurálható vagy az internetről, vagy csak a virtuális hálózaton belül elérhetők lesznek.

> [!NOTE]
> Az Azure API Management támogatja a klasszikus és Azure Resource Manager virtuális hálózatok.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM-példány. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Virtuális hálózatok közötti kapcsolat engedélyezése

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Virtuális hálózatok közötti kapcsolat az Azure portal használatával

1. Keresse meg az APIM-példányra, az a [az Azure portal](https://portal.azure.com/).
2. Válassza ki **virtuális hálózat**.
3. Konfigurálja az API Management-példány üzembe helyezni egy virtuális hálózaton belül.

    ![Az API Management a virtuális hálózat menü][api-management-using-vnet-menu]
4. Válassza ki a kívánt hozzáférési típusa:

    * **Külső**: az API Management-átjáró és fejlesztői portálon keresztül egy külső terheléselosztó a nyilvános interneten keresztül érhetők el. Az átjáró a virtuális hálózatban lévő erőforrások eléréséhez.

    ![Nyilvános társviszony-létesítés][api-management-vnet-public]

    * **Belső**: az API Management-átjáró és fejlesztői portálon érhetők el, csak a belső terheléselosztót a virtuális hálózaton belül. Az átjáró a virtuális hálózatban lévő erőforrások eléréséhez.

    ![Magánhálózati társviszony-létesítés][api-management-vnet-private]`

    Megjelenik az összes olyan régióban, ahol az API Management szolgáltatás ki van építve listáját. Válasszon egy VNETET és alhálózatot minden régióhoz. A lista elkészült, a klasszikus és a Resource Manager virtuális hálózatot az Azure-előfizetésekre, amelyek a telepítő konfigurálja a régióban érhető el.

    > [!NOTE]
    > **Szolgáltatásvégpont** a fenti ábrán magában foglalja az átjáró /-Proxy, az Azure Portalon, a fejlesztői portál, a GIT és a közvetlen felügyeleti végponthoz.
    > **Felügyeleti végpont** a fenti ábrán az a végpont, üzemeltetett a szolgáltatáson keresztül az Azure portal, Powershell konfigurációjának kezelése.
    > Megjegyzés:, bár az ábrán látható IP-címek a különböző végpontokhoz elérhető, az API Management szolgáltatás **csak** reagál a beállított gazdanév található.

    > [!IMPORTANT]
    > Ha a virtuális hálózathoz a Resource Manager üzembe helyezése az Azure API Management-példány, a szolgáltatás, amely nem tartalmaz más erőforrásokat kivéve az Azure API Management példányok dedikált alhálózatán kell lennie. Ha kísérlet történik az Azure API Management-példány üzembe helyezése a Resource Manager virtuális hálózat alhálózatához, amely más erőforrásokat tartalmaz, a telepítés meghiúsul.
    >

    ![Jelölje ki a VPN][api-management-setup-vpn-select]

5. Kattintson a **mentése** a képernyő tetején.

> [!NOTE]
> Az API Management-példány VIP-címet minden alkalommal, amikor virtuális hálózatok közötti engedélyezett vagy letiltott változik.
> A virtuális IP-címet is módosíthatja, ha az API Management áthelyezik az **külső** való **belső** vagy fordítva
>

> [!IMPORTANT]
> Ha távolítsa el az API Management a virtuális hálózaton, vagy módosítsa a terjesztve van egy, a korábban használt virtuális hálózat is zároltak maradhatnak akár két óráig. Ebben az időszakban, nem lesz lehetséges a virtuális hálózat törlése, és helyezhet üzembe egy új erőforrást.

## <a name="enable-vnet-powershell"> </a>PowerShell-parancsmagok használatával virtuális hálózatok közötti kapcsolat engedélyezése
Is engedélyezheti a PowerShell-parancsmagok használatával virtuális hálózatok közötti kapcsolat

* **Hozzon létre egy virtuális hálózaton belül egy API Management szolgáltatás**: a parancsmag [New-azurermapimanagement parancsmagok](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) egy virtuális hálózaton belül az Azure API Management szolgáltatás létrehozása.

* **Egy meglévő, egy virtuális hálózaton belül az API Management szolgáltatás üzembe helyezése**: a parancsmag [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) áthelyezése egy meglévő Azure API Management szolgáltatást egy virtuális hálózaton belül.

## <a name="connect-vnet"> </a>Csatlakozás egy virtuális hálózatban lévő üzemeltetett webes szolgáltatáshoz
Miután az API Management szolgáltatás a virtuális hálózathoz van csatlakoztatva, benne a háttérszolgáltatásokat fér hozzá semmiben nem különbözik nyilvános szolgáltatásokhoz való hozzáférését. Csak írja be a helyi IP-cím vagy a gazdagép neve (Ha a virtuális hálózat DNS-kiszolgáló van konfigurálva), a webszolgáltatás a **webszolgáltatás URL-címe** mezőt egy új API létrehozásakor, vagy egy meglévőt szerkeszt.

![API hozzáadása a VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Általános hálózati konfigurációs problémák
Az alábbiakban olyan gyakori, egy virtuális hálózatban az API Management szolgáltatás központi telepítése során előforduló helytelen konfiguráció problémák.

* **Az egyéni DNS-kiszolgáló telepítés**: az API Management szolgáltatás számos Azure-szolgáltatások függ. Az API Management egy egyéni DNS-kiszolgáló rendelkező virtuális hálózaton jöhet szóba, ha szükséges az adott Azure-szolgáltatások állomásnevét feloldani. Kövesse az [ez](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egyéni DNS beállításával útmutatást. Tekintse meg az alábbi táblázat a portok és egyéb hálózati követelményeinek hivatkozást.

> [!IMPORTANT]
> Ha azt tervezi, egy egyéni DNS-kiszolgálók használata a virtuális hálózaton, akkor állítsa be **előtt** bele egy API Management szolgáltatás központi telepítése. Ellenkező esetben frissítenie kell az API Management szolgáltatás minden alkalommal, amikor a DNS-kiszolgálói módosításához futtassa a [alkalmazni a hálózati konfiguráció művelet](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Az API Management számára szükséges portok**: bejövő és kimenő forgalmat az alhálózatban, amelyben az API Management üzembe van helyezve használatával lehet irányítani [hálózati biztonsági csoport][Network Security Group]. Ha bármelyik ezeket a portokat nem érhető el, az API Management esetleg nem működnek megfelelően, és nem elérhetők. Egy másik gyakori Virtual Network szolgáltatás hibás probléma egy vagy több letiltott portokon kellene akkor, ha az API Management használatával a virtuális hálózathoz.

Az API Management-szolgáltatáspéldány egy virtuális hálózaton jöhet szóba, ha a rendszer az alábbi táblázatban a portokat használja.

| Forrás / cél port(ok) | Irány          | Átviteli protokoll |   [Szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) <br> Forrás és cél   | Cél (*)                                                 | Virtuális hálózat típusa |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Bejövő            | TCP                | AZ INTERNET / VIRTUAL_NETWORK            | Ügyfél-kommunikációt és az API Management                      | Külső             |
| * / 3443                     | Bejövő            | TCP                | Az ApiManagement / VIRTUAL_NETWORK       | Az Azure portal, Powershell felügyeleti végponthoz         | Külső és belső  |
| * / 80, 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK / Storage             | **Az Azure Storage függőség**                             | Külső és belső  |
| * / 80, 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Az Azure Active Directory (ha vannak ilyenek)                   | Külső és belső  |
| * / 1433                     | Kimenő           | TCP                | VIRTUAL_NETWORK / SQL                 | **Hozzáférés az Azure SQL-végpontokra**                           | Külső és belső  |
| * / 5672                     | Kimenő           | TCP                | VIRTUAL_NETWORK / EventHub            | Eseményközpont-szabályzat és a monitorozási ügynök a napló függőséget | Külső és belső  |
| * / 445                      | Kimenő           | TCP                | VIRTUAL_NETWORK / Storage             | A git Azure-fájlmegosztás függőség                      | Külső és belső  |
| * / 1886                     | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | A Resource Health állapot közzététele szükséges          | Külső és belső  |
| * / 443-as                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Közzététele a diagnosztikai naplók és mérőszámok                        | Külső és belső  |
| * / 25                       | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Az e-mailek küldéséhez SMTP-továbbítás használata csatlakozáshoz                    | Külső és belső  |
| * / 587                      | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Az e-mailek küldéséhez SMTP-továbbítás használata csatlakozáshoz                    | Külső és belső  |
| * / 25028                    | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Az e-mailek küldéséhez SMTP-továbbítás használata csatlakozáshoz                    | Külső és belső  |
| * / 6381 - 6383              | A bejövő és kimenő | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Hozzáférés a Redis Cache-példányokban RoleInstances között          | Külső és belső  |
| * / *                        | Bejövő            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Az Azure infrastruktúra Terheléselosztóját                          | Külső és belső  |

>[!IMPORTANT]
> A portok, amelynek a *célú* van **félkövér** szükségesek az API Management-szolgáltatás telepítése sikeresen. A más portok blokkolása azonban okoz teljesítménycsökkenést, illetve figyelheti a futó szolgáltatás lehetővé teszi a.

* **SSL-funkciók**: ahhoz, hogy SSL-tanúsítvány láncot épület és az érvényesítés az API Management szolgáltatást kell ocsp.msocsp.com, mscrl.microsoft.com és crl.microsoft.com kimenő hálózati kapcsolat. Ezt a függőséget nem kötelező, ha bármely fel kell töltenie az API Management a tanúsítványnak tartalmaznia a teljes tanúsítványlánccal, a legfelső szintű.

* **DNS-hozzáférési**: 53-as porton a kimenő hozzáférést a DNS-kiszolgálókkal való kommunikációhoz szükséges. Ha egyéni DNS-kiszolgálót a VPN-átjáró másik végén, a DNS-kiszolgáló elérhető, az alhálózatról, az API Management üzemeltető kell lennie.

* **Metrikák és a Szolgáltatásállapot-figyelést**: az Azure figyelési végpontok, amely alatt a következő tartományok feloldani a kimenő hálózati kapcsolat: 

    | Azure-környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com ahol `East US 2` eastus2.warm.ingestion.msftcloudes.com van</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.NET</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.CN</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li></ul>                                                                                                                                                                                                                                                |

* **SMTP-továbbítás használata**: az SMTP-továbbítás használata, amely alatt a fogadó oldja fel a kimenő hálózati kapcsolat `ies.global.microsoft.com`.

* **Az Azure portal diagnosztikai**: a folyamatot az Azure Portalról a diagnosztikai naplók engedélyezése, ha a virtuális hálózatokban, kimenő hozzáférést az API Management-bővítmény használata `dc.services.visualstudio.com` 443-as porton szükség. Ez segít bővítmény használatakor előfordulhat, hogy között kapcsolatos hibák elhárítása.

* **Express Route-telepítő**: egy közös ügyfél-konfigurációs a saját alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő internetes forgalmat inkább a helyi meghatározásához. A forgalom áramlását tüntetnek működésképtelenné válik a kapcsolatot az Azure API Management szolgáltatással, mert a kimenő forgalmat a letiltott helyi vagy NAT-címek, amelyek többé nem működnek együtt a különböző Azure-beli felismerhetetlen készletéhez lenne. A megoldás az, hogy egy (vagy több) felhasználó által megadott útvonalak megadása ([udr-EK][UDRs]), amely tartalmazza az Azure API Management az alhálózaton. Egy UDR határozza meg az alhálózat-specifikus útvonalakat, amely az alapértelmezett útvonal helyett lesznek érvényesek.
  Ha lehetséges javasoljuk, hogy az alábbi konfigurációt használja:
 * Az ExpressRoute-konfiguráció hirdet 0.0.0.0/0, és alapértelmezés szerint kényszerített alagutak minden kimenő forgalmat a helyszíni.
 * Az udr-t az alhálózathoz, amely tartalmazza az Azure API Management a alkalmazni a 0.0.0.0/0, Internet következő ugrási típusú az határozza meg.
 A rendszer ezeket a lépéseket a kombinált hatását, hogy az alhálózat-szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatást végez, ezáltal biztosítható a kimenő Internet-hozzáférést az Azure API Management által.

* **Hálózati virtuális berendezések keresztül útválasztási**: letiltja a konfigurációk, amelyek segítségével egy udr-t olyan alapértelmezett útvonalat (0.0.0.0/0) az API Management alhálózatból az internet felé irányuló forgalom irányítása az Azure-ban futó hálózati virtuális készüléken keresztül felügyeleti forgalom érkezik az Internet az API Management-szolgáltatáspéldány érvényesekre, amelyeket a virtuális hálózat alhálózatához. Ez a konfiguráció nem támogatott.

>[!WARNING]
>Az Azure API Management nem támogatott olyan ExpressRoute-konfigurációk, amelyek **helytelenül keresztbe hirdetnek útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonal**. ExpressRoute-konfigurációk, amelyek rendelkeznek, a konfigurált nyilvános társviszonyt fog útvonalhirdetéseket kapnak a Microsoft számára a Microsoft Azure IP-címtartományok nagy készletét. Ha a címtartományokat helytelenül keresztbe hirdetik a privát társviszony-létesítési útvonal, a végeredmény, hogy az összes, az Azure API Management-példány alhálózatából származó kimenő hálózati csomag-e helytelenül kényszerített bújtatással jut el az ügyfél helyszíni hálózati infrastruktúra. A hálózati forgalom működésképtelenné válik az Azure API Management. A megoldás erre a problémára, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési elérési utat.


## <a name="troubleshooting"> </a>Hibaelhárítás
* **A kezdeti telepítés**: az API Management szolgáltatás egyik alhálózatában kezdeti telepítése nem sikerül, ha először helyezheti üzembe a virtuális gép ugyanazon az alhálózaton javasolt. Tovább a távoli asztal a virtuális géppel, és ellenőrizze, hogy nincs-e az egyes erőforrások alatt az azure-előfizetésében egyik kapcsolat
    * Azure Storage blob
    * Azure SQL Database
    * Az Azure Storage-táblából

 > [!IMPORTANT]
 > Miután ellenőrizte a kapcsolat, el kell távolítania az alhálózatban az API Management telepítése előtt üzembe helyezheti az alhálózaton, összes erőforrást.

* **Növekményes frissítések**: Ha módosítja a hálózathoz, tekintse meg [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), ellenőrizze, hogy az API Management szolgáltatás nem elvesztette a a kritikus fontosságú erőforrások függ, amely a hozzáférést. A kapcsolati állapot 15 percenként frissíteni kell.

* **Erőforrás-navigációs hivatkozások**: Resource Manager-stílus virtuális hálózat alhálózatában telepítésekor az API Management az alhálózat fenntart egy erőforrás-navigációs hivatkozás létrehozásával. Ha az alhálózat már tartalmaz egy erőforrás más szolgáltatótól származó, központi telepítés rendszer **sikertelen**. Hasonlóképpen egy API Management szolgáltatás áthelyezése egy másik alhálózatot, vagy törölje azt, megszüntetjük, erőforrás-navigációs hivatkozást.

## <a name="subnet-size"> </a> Alhálózat Méretkövetelményt
Az Azure lefoglalja minden egyes alhálózaton belül néhány IP-cím, és ezek a címek nem használható. Az alhálózatok első és utolsó IP-címét a protokollok megfelelősége érdekében, az Azure-szolgáltatásokhoz használt három további címek számára vannak fenntartva. További információkért lásd: [vannak ezen alhálózatok belüli IP-címek használatával korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Mellett az Azure-beli virtuális infrastruktúra által használt IP-címeket Api Management minden példányához az alhálózat a fejlesztői Termékváltozat prémium Termékváltozatot egységére két IP-cím vagy egy IP-címet használ. Minden példány fenntartja a külső terheléselosztó egy további IP-címet. Belső virtuális hálózathoz történő telepítésekor a belső terheléselosztó számára szükséges további IP-címet.

Az alhálózat, amelyben az API Management is üzembe helyezhetők a minimális méret fent a számítás megadott van akár/29 méretű, ami biztosít az IP-címek 3.

## <a name="routing"> </a> Útválasztás
+ Elosztott terhelésű nyilvános IP-cím (VIP) fog lefoglalt összes Szolgáltatásvégpontok eléréséhez.
+ Egy alhálózat IP-címtartomány (DIP) egy IP-címet a virtuális hálózaton belüli erőforrások eléréséhez használható, és a virtuális hálózaton kívüli erőforrások eléréséhez használható nyilvános IP-cím (VIP).
+ Az elosztott terhelésű nyilvános IP-cím az Azure portal áttekintés/Essentials panelén található.

## <a name="limitations"> </a>Korlátozások
* Az API Management példányok tartalmazó alhálózat bármely más Azure-erőforrástípus nem tartalmazhat.
* Az alhálózat és az API Management szolgáltatás ugyanabban az előfizetésben kell lennie.
* Egy alhálózatot, amely tartalmazza az API Management példányok több előfizetés között nem lehet áthelyezni.
* A belső virtuális hálózathoz módban konfigurált többrégiós az API Management üzembe helyezési felhasználók felelőssége saját útválasztását kezelése több régióban, a terheléselosztási.
* Kapcsolat egy erőforrást egy másik régióban globálisan társított virtuális hálózaton az API Management szolgáltatás belső módban és platformkorlátozásai miatt nem működik. További információkért lásd: [egy virtuális hálózatban lévő erőforrásokra nem tud kommunikálni a virtuális társhálózatban működő Azure belső terheléselosztó](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Kapcsolódó tartalom
* [Két virtuális hálózat Vpn-átjáró használatával háttérrendszerhez](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Különböző üzemi modellekből származó virtuális hálózat csatlakoztatása](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Az Azure API Management meghívja az API Inspectorral nyomkövetési használata](api-management-howto-api-inspector.md)
* [Virtuális hálózat – gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [Szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
