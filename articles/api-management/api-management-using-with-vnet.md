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
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: a8566e41934b5d78d8be60b385ea4148e1cb60c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087040"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>A virtuális hálózatok az Azure API Management használata
Az Azure virtuális hálózatok (Vnetek) helyezni, az Azure-erőforrások bármelyikét elérését Ön szabályozza a nem internet routeable hálózat teszi lehetővé. Ezek a hálózatok csatlakozhat a helyszíni hálózatokhoz való kapcsolódásának VPN különböző technológiákat. További információ az adatok itt az Azure-beli virtuális hálózatok kezdő: [Az Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

Az Azure API Management is üzembe helyezhetők a virtuális hálózaton (VNET), hogy hozzá tudjon férni a háttérszolgáltatások a hálózaton belül. A fejlesztői portál és API-átjáró, konfigurálható vagy az internetről, vagy csak a virtuális hálózaton belül elérhetők lesznek.

> [!NOTE]
> Az Azure API Management támogatja a klasszikus és Azure Resource Manager virtuális hálózatok.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

* **Hozzon létre egy virtuális hálózaton belül egy API Management szolgáltatás**: A parancsmag [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) egy virtuális hálózaton belül az Azure API Management szolgáltatás létrehozása.

* **Egy meglévő, egy virtuális hálózaton belül az API Management szolgáltatás üzembe helyezése**: A parancsmag [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) áthelyezése egy meglévő Azure API Management szolgáltatást egy virtuális hálózaton belül.

## <a name="connect-vnet"> </a>Csatlakozás egy virtuális hálózatban lévő üzemeltetett webes szolgáltatáshoz
Miután az API Management szolgáltatás a virtuális hálózathoz van csatlakoztatva, benne a háttérszolgáltatásokat fér hozzá semmiben nem különbözik nyilvános szolgáltatásokhoz való hozzáférését. Csak írja be a helyi IP-cím vagy a gazdagép neve (Ha a virtuális hálózat DNS-kiszolgáló van konfigurálva), a webszolgáltatás a **webszolgáltatás URL-címe** mezőt egy új API létrehozásakor, vagy egy meglévőt szerkeszt.

![API hozzáadása a VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Általános hálózati konfigurációs problémák
Az alábbiakban olyan gyakori, egy virtuális hálózatban az API Management szolgáltatás központi telepítése során előforduló helytelen konfiguráció problémák.

* **Az egyéni DNS-kiszolgáló telepítés**: Az API Management szolgáltatás számos Azure-szolgáltatások függ. Az API Management egy egyéni DNS-kiszolgáló rendelkező virtuális hálózaton jöhet szóba, ha szükséges az adott Azure-szolgáltatások állomásnevét feloldani. Kövesse az [ez](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egyéni DNS beállításával útmutatást. Tekintse meg az alábbi táblázat a portok és egyéb hálózati követelményeinek hivatkozást.

> [!IMPORTANT]
> Ha azt tervezi, egy egyéni DNS-kiszolgálók használata a virtuális hálózaton, akkor állítsa be **előtt** bele egy API Management szolgáltatás központi telepítése. Ellenkező esetben frissítenie kell az API Management szolgáltatás minden alkalommal, amikor a DNS-kiszolgálói módosításához futtassa a [alkalmazni a hálózati konfiguráció művelet](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Az API Management számára szükséges portok**: Bejövő és kimenő forgalmat az alhálózatban, amelyben az API Management üzembe van helyezve használatával lehet irányítani [hálózati biztonsági csoport][Network Security Group]. Ha bármelyik ezeket a portokat nem érhető el, az API Management esetleg nem működnek megfelelően, és nem elérhetők. Egy másik gyakori Virtual Network szolgáltatás hibás probléma egy vagy több letiltott portokon kellene akkor, ha az API Management használatával a virtuális hálózathoz.

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
| * / 443                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Közzététele a diagnosztikai naplók és mérőszámok                        | Külső és belső  |
| * / 25                       | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Az e-mailek küldéséhez SMTP-továbbítás használata csatlakozáshoz                    | Külső és belső  |
| * / 587                      | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Az e-mailek küldéséhez SMTP-továbbítás használata csatlakozáshoz                    | Külső és belső  |
| * / 25028                    | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Az e-mailek küldéséhez SMTP-továbbítás használata csatlakozáshoz                    | Külső és belső  |
| * / 6381 - 6383              | A bejövő és kimenő | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Az Azure Cache elérése a Redis-példány között RoleInstances          | Külső és belső  |
| * / *                        | Bejövő            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Az Azure infrastruktúra Terheléselosztóját                          | Külső és belső  |

>[!IMPORTANT]
> A portok, amelynek a *célú* van **félkövér** szükségesek az API Management-szolgáltatás telepítése sikeresen. A más portok blokkolása azonban okoz teljesítménycsökkenést, illetve figyelheti a futó szolgáltatás lehetővé teszi a.

+ **SSL-funkciók**: Ahhoz, hogy SSL-tanúsítvány láncot épület és az érvényesítés az API Management szolgáltatást kell ocsp.msocsp.com, mscrl.microsoft.com és crl.microsoft.com kimenő hálózati kapcsolat. Ezt a függőséget nem kötelező, ha bármely fel kell töltenie az API Management a tanúsítványnak tartalmaznia a teljes tanúsítványlánccal, a legfelső szintű.

+ **DNS-hozzáférési**: 53-as porton a kimenő hozzáférést a DNS-kiszolgálókkal való kommunikációhoz szükséges. Ha egyéni DNS-kiszolgálót a VPN-átjáró másik végén, a DNS-kiszolgáló elérhető, az alhálózatról, az API Management üzemeltető kell lennie.

+ **Metrikák és a Szolgáltatásállapot-figyelést**: Kimenő hálózati kapcsolat az Azure figyelési végpontok, amely alatt a következő tartományok feloldása:

    | Azure-környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com ahol `East US 2` eastus2.warm.ingestion.msftcloudes.com van</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP-továbbítás használata**: Az SMTP-továbbítás használata, amely alatt a fogadó oldja fel a kimenő hálózati kapcsolat `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` és `ies.global.microsoft.com`

+ **Fejlesztői portál CAPTCHA**: A fejlesztői portál CAPTCHA, amely alatt a fogadó oldja fel a kimenő hálózati kapcsolat `client.hip.live.com`.

+ **Az Azure portal diagnosztikai**: A folyamatot az Azure Portalról a diagnosztikai naplók engedélyezése, ha a virtuális hálózatokban, kimenő hozzáférést az API Management-bővítmény használata `dc.services.visualstudio.com` 443-as porton szükség. Ez segít bővítmény használatakor előfordulhat, hogy között kapcsolatos hibák elhárítása.

+ **Kényszerített bújtatás az Express Route vagy hálózati virtuális berendezés használata a helyszíni tűzfal-forgalom**: Egy közös ügyfél-konfigurációs, adja meg a saját alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a teljes forgalom az API Management által delegált alhálózati folyamat egy a helyszíni tűzfalon keresztül, vagy egy hálózati virtuális berendezésre. A forgalom áramlását tüntetnek működésképtelenné válik a kapcsolatot az Azure API Management szolgáltatással, mert a kimenő forgalmat a letiltott helyi vagy NAT-címek, amelyek többé nem működnek együtt a különböző Azure-beli felismerhetetlen készletéhez lenne. A megoldáshoz szükséges, hogy néhány dolgot:

  * Engedélyezze a szolgáltatásvégpontokat, amely az API Management szolgáltatás üzembe lesz helyezve az alhálózaton. [A Szolgáltatásvégpontok] [ ServiceEndpoints] engedélyeznie kell az Azure Sql, Azure Storage, Azure Event hubhoz és Azure Szolgáltatásbusz. Az API Management delegált alhálózatot, ezek a szolgáltatások lehetővé teszi, hogy az optimális útválasztást biztosít az szolgáltatás forgalma számára a Microsoft Azure gerinchálózatán használandó közvetlenül a végpontok engedélyezésére. A Szolgáltatásvégpontok használatakor a kényszerített bújtatásos Api Management szolgáltatással, a fenti Azure szolgáltatások forgalma nem kényszerítetten bújtatott. A többi API Management szolgáltatás függőségi forgalma kényszerítetten bújtatott, és nem lehet elveszett vagy az API Management szolgáltatás nem lenne megfelelő működéséhez.
    
  * A vezérlési sík érkező minden internetes felügyeleti végpontja, az API Management szolgáltatás a rendszer egy meghatározott készletének bejövő IP-címek az API Management által üzemeltetett keresztül irányítja át. Ha a forgalom kényszerített bújtatása a válaszok nem szimmetrikusan társítja vissza ezeket bejövő forrás IP-címek. A korlátozás áthidalható, hozzá kell adnunk a következő felhasználó által megadott útvonalak ([udr-EK][UDRs]), és ezeket az "Internet" gazdagép útvonalakat beállításával figyelmeztetik a forgalom Azure-bA. A vezérlési sík forgalom a bejövő IP-címek készletét a következőképpen történik:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * Az egyéb, az API Management szolgáltatás függőséggel, amelyek a kényszerített bújtatással kerül, azok az állomásnév feloldása, és keresse fel a végpontnak kell lennie. Ezek közé tartozik a
      - Metrikák és állapotfigyelés
      - Az Azure portal diagnosztikai
      - SMTP-továbbítás használata
      - Fejlesztői portál CAPTCHA

## <a name="troubleshooting"> </a>Hibaelhárítás
* **A kezdeti telepítés**: Ha az API Management-szolgáltatás egyik alhálózatában kezdeti telepítése nem sikerül, célszerű először helyezheti üzembe a virtuális gép ugyanazon az alhálózaton. Tovább a távoli asztal a virtuális géppel, és ellenőrizze, hogy nincs-e az egyes erőforrások alatt az azure-előfizetésében egyik kapcsolat
    * Azure Storage blob
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Miután ellenőrizte a kapcsolat, el kell távolítania az alhálózatban az API Management telepítése előtt üzembe helyezheti az alhálózaton, összes erőforrást.

* **Növekményes frissítések**: Ha módosítja a hálózathoz, tekintse meg [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), ellenőrizze, hogy az API Management szolgáltatás már nem fér sem függ a kiemelt fontosságú erőforrások. A kapcsolati állapot 15 percenként frissíteni kell.

* **Erőforrás-navigációs hivatkozások**: Resource Manager-stílus virtuális hálózat alhálózatában telepítésekor az API Management fenntartja az alhálózatot, hozzon létre egy erőforrás-navigációs hivatkozást. Ha az alhálózat már tartalmaz egy erőforrás más szolgáltatótól származó, központi telepítés rendszer **sikertelen**. Hasonlóképpen egy API Management szolgáltatás áthelyezése egy másik alhálózatot, vagy törölje azt, megszüntetjük, erőforrás-navigációs hivatkozást.

## <a name="subnet-size"> </a> Alhálózat Méretkövetelményt
Az Azure lefoglalja minden egyes alhálózaton belül néhány IP-cím, és ezek a címek nem használható. Az alhálózatok első és utolsó IP-címét a protokollok megfelelősége érdekében, az Azure-szolgáltatásokhoz használt három további címek számára vannak fenntartva. További információkért lásd: [vannak ezen alhálózatok belüli IP-címek használatával korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Mellett az Azure-beli virtuális infrastruktúra által használt IP-címeket Api Management minden példányához az alhálózat a fejlesztői Termékváltozat prémium Termékváltozatot egységére két IP-cím vagy egy IP-címet használ. Minden példány fenntartja a külső terheléselosztó egy további IP-címet. Belső virtuális hálózathoz történő telepítésekor a belső terheléselosztó számára szükséges további IP-címet.

Az alhálózat, amelyben az API Management is üzembe helyezhetők a minimális méret fent a számítás megadott van akár/29 méretű, ami három IP-címet biztosít.

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
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
