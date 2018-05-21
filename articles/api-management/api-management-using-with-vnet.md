---
title: Virtuális hálózatok az Azure API Management használata
description: Útmutató a kapcsolatot az Azure API Management és az access webszolgáltatások rajta egy virtuális hálózathoz.
services: api-management
documentationcenter: ''
author: antonba
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 223fa9bc4a19264cc1dcba9830726b30b0f7446c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Virtuális hálózatok az Azure API Management használata
Az Azure virtuális hálózatokról (Vnetekről) helyezze el az Azure-erőforrások bármelyike nem internetes routeable hálózati hozzáférést szabályozó teszik lehetővé. Ezek a hálózatok csatlakozhatnak különböző VPN technológiáin a helyszíni hálózatokhoz. További információk az Azure virtuális hálózatok indítsa el az adatok Itt további: [Azure virtuális hálózat áttekintése](../virtual-network/virtual-networks-overview.md).

Az Azure API Management telepítheti a virtuális hálózaton (VNET), így hozzáférhet a hálózaton belüli háttér-szolgáltatások. A fejlesztői portálján és API-átjárón beállítható úgy, hogy érhető el, vagy az internetről, vagy csak a virtuális hálózaton belül.

> [!NOTE]
> Az Azure API Management támogatja a classic és az Azure Resource Manager Vnetek.
>

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie kell:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM példánya. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Virtuális hálózat kapcsolat csak a prémium és fejlesztői rétegek érhető el. A riasztásban megjelenő utasításokat követve ezek a rétegek egyik váltani a [frissítése és méretezése](upgrade-and-scale.md#upgrade-and-scale) témakör.

## <a name="enable-vpn"> </a>VNET-kapcsolat engedélyezése

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Engedélyezze a VNET-kapcsolatot az Azure portál használatával

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **virtuális hálózati**.
3. Konfigurálja az API Management telepíthető egy virtuális hálózaton belül.

    ![Virtuális hálózati menü API Management][api-management-using-vnet-menu]
4. Válassza ki a kívánt hozzáférés típusát:
    
    * **Külső**: az API Management-átjáró és a fejlesztői portálon keresztül egy külső terheléselosztó a nyilvános interneten keresztül érhetők el. Az átjáró a virtuális hálózaton lévő erőforrások eléréséhez.
    
    ![Nyilvános társviszony-létesítés][api-management-vnet-public]
    
    * **Belső**: az API Management-átjáró és a fejlesztői portálon csak érhetők el a belső terheléselosztók használatával a virtuális hálózaton belül. Az átjáró a virtuális hálózaton lévő erőforrások eléréséhez.
    
    ![Magánhálózati társviszony-létesítés][api-management-vnet-private]`

    Ekkor minden régióban, ahol az API Management szolgáltatás ki van építve listáját. Válassza ki a virtuális hálózat és alhálózat minden régióhoz. A lista elkészült a klasszikus és Resource Manager virtuális hálózatok az Azure-előfizetések, amelyek a telepítő konfigurálja a régióban elérhető.
    
    > [!NOTE]
    > **Szolgáltatásvégpont** a fenti ábrán magában foglalja az átjáró /-Proxy, az Azure-portálon, a fejlesztői portálján, GIT és a közvetlen felügyeleti végpontja.
    > **Felügyeleti végpont** a fenti ábrán a végpont-konfiguráció Azure-portál és a Powershell segítségével kezelheti a szolgáltatásban üzemeltetett.
    > Vegye figyelembe azt is, amely akkor is, ha az ábrán látható IP-címek a különböző végpontokhoz, az API Management szolgáltatás **csak** reagál a beállított állomásnevek a.
    
    > [!IMPORTANT]
    > Az Azure API Management-példány egy erőforrás-kezelő virtuális hálózatba való telepítésekor a szolgáltatás az Azure API Management példányok kivételével nincs más erőforrásokat tartalmazó dedikált alhálózat kell lennie. Ha az Azure API Management-példányt telepítése egy erőforrás-kezelő virtuális hálózat alhálózathoz tett kísérlet, amely más erőforrások, a telepítés meghiúsul.
    >

    ![Jelölje ki a VPN][api-management-setup-vpn-select]

5. Kattintson a **mentése** a képernyő tetején.

> [!NOTE]
> Az API Management-példány a címe változik minden alkalommal, amikor virtuális hálózat engedélyezve vagy letiltva.  
> A virtuális IP-cím is megváltozik az API Management mozgatásakor **külső** való **belső** vagy fordítva
>

> [!IMPORTANT]
> Ha az API Management eltávolítása egy VNETET, vagy módosítsa a telepítik egy, a korábban használt virtuális hálózat maradjanak zárolt akár két óráig. Ebben az időszakban, nem lesz lehetséges a virtuális hálózat törlése vagy a központi telepítése egy új erőforrást.

## <a name="enable-vnet-powershell"> </a>PowerShell-parancsmagok használatával VNET-kapcsolat engedélyezése
Engedélyezheti a VNET-kapcsolatot a PowerShell-parancsmagok használatával

* **A VNETEN belül az API Management szolgáltatás létrehozása**: parancsmag [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) egy VNETEN belül Azure API Management szolgáltatás létrehozása.

* **A VNETEN belül az meglévő API Management-szolgáltatások üzembe**: parancsmag [frissítés-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) helyezhető át egy meglévő Azure API Management szolgáltatást egy virtuális hálózaton belül.

## <a name="connect-vnet"> </a>A virtuális hálózaton belül futó a webszolgáltatáshoz csatlakozni
Az API-kezelés szolgáltatás a virtuális hálózatba csatlakoztatása után belül háttér-szolgáltatások eléréséhez szükséges eltér nem nyilvános szolgáltatások eléréséhez szükséges. Csak a helyi IP-cím vagy a webszolgáltatás a állomásneve (Ha a virtuális hálózat DNS-kiszolgáló van konfigurálva) írja be a **webszolgáltatás URL-címe** mezőben egy új API létrehozásakor vagy egy meglévőt.

![A VPN API hozzáadása][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Általános hálózati konfigurációs problémák
Az alábbiakban az API-kezelés szolgáltatás telepítése virtuális hálózatba során előforduló gyakori helytelen konfiguráció-problémák listáját.

* **Egyéni DNS-kiszolgáló beállításainak**: az API Management szolgáltatás több Azure-szolgáltatások függ. Amikor az API Management egyéni DNS-kiszolgáló a VNETEN belül üzemel kell feloldani a gazdagép neve az Azure szolgáltatások. Kövesse az [ez](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egyéni DNS-beállításainak útmutatást. Tekintse meg a portokat az alábbi táblázat és az egyéb hálózati követelmények hivatkozás.

> [!IMPORTANT]
> Ha egy egyéni DNS-kiszolgálói, használja a vnet tervez, érdemes beállítania, **előtt** bele az API Management szolgáltatás telepítéséhez. Ellenkező esetben frissítenie kell az API Management szolgáltatás minden alkalommal, amikor a DNS-kiszolgálói módosításához futtassa a [alkalmazni a hálózati konfigurációs műveletet](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Az API Management szükséges portok**: az API Management telepítése alhálózat a bejövő és kimenő forgalom vezérelhető [hálózati biztonsági csoport][Network Security Group]. Ha ezeket a portokat bármelyike nem érhető el, az API Management nem fog megfelelően működni, és lehetnek nem elérhetők. Rendelkezik egy vagy több ezeket a portokat blokkolva helytelen konfigurálása más gyakori problémák használata esetén az API Management olyan virtuális hálózaton.

Ha egy API-kezelés szolgáltatás példányát a VNETEN belül üzemel a portokat az alábbi táblázatban használ.

| Forrás / cél port(ok) | Irány | Átviteli protokoll | Forrás / cél | Cél (*) | Virtuális hálózat típusa |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Bejövő |TCP |INTERNET / VIRTUAL_NETWORK|Ügyfél-kommunikációt kíván API Management|Külső |
| * / 3443 |Bejövő |TCP |INTERNET / VIRTUAL_NETWORK|Az Azure portál és a Powershell felügyeleti végpont |Belső |
| * / 80, 443 |Kimenő |TCP |VIRTUAL_NETWORK / INTERNET|**Azure Storage függőség**, Azure Service Bus és az Azure Active Directory (ha alkalmazható).|Külső és belső | 
| * / 1433 |Kimenő |TCP |VIRTUAL_NETWORK / INTERNET|**Hozzáférés az Azure SQL-végpontok** |Külső és belső |
| * / 5672 |Kimenő |TCP |VIRTUAL_NETWORK / INTERNET|Az Event Hubs házirend- és figyelési ügynök napló függőség |Külső és belső |
| * / 445 |Kimenő |TCP |VIRTUAL_NETWORK / INTERNET|Azure-fájlmegosztáshoz git függőség |Külső és belső |
| * / 1886 |Kimenő |TCP |VIRTUAL_NETWORK / INTERNET|A Resource Health állapot közzétételéhez szükséges |Külső és belső |
| * / 25028 |Kimenő |TCP |VIRTUAL_NETWORK / INTERNET|Csatlakozzon az SMTP-továbbító az e-mailek küldésekor |Külső és belső |
| * / 6381 - 6383 |Bejövő és kimenő |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Hozzáférés a Redis gyorsítótár példányok RoleInstances között |Külső és belső |
| * / * | Bejövő |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Az Azure infrastruktúra Terheléselosztóját |Külső és belső |

>[!IMPORTANT]
> A portok, amelynek a *célú* van **félkövér** szükséges API-kezelés szolgáltatás sikeresen telepíthető. A más portok blokkolása azonban miatt teljesítménycsökkenést használja, és figyelje a futó szolgáltatás az lehetősége.

* **SSL-funkció**: SSL-tanúsítvány láncának felépítése és -ellenőrzés az API Management engedélyezéséhez szolgáltatásnak kell a kimenő hálózati kapcsolat ocsp.msocsp.com, mscrl.microsoft.com és crl.microsoft.com. A függőség nincs szükség, ha bármely olyan tanúsítvány, az API Management-be feltölteni a teljes lánc és a legfelső szintű tartalmaz.

* **DNS hozzáférési**: 53-as port kimenő hozzáférést kell DNS-kiszolgálókkal való kommunikációhoz. Ha egy egyéni DNS-kiszolgáló létezik a VPN-átjáró másik végén, a DNS-kiszolgáló elérhető-e az API Management üzemeltető alhálózatból kell lennie.

* **Metrikák és az állapotfigyelés**: Azure figyelési végpontok, amelyek oldja meg a következő tartományokkal a kimenő hálózati kapcsolat: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net, prod.warmpath.msftcloudes.com, prod3-black.prod3.metrics.nsatc.net és prod3-red.prod3.metrics.nsatc.net.

* **Útvonal gyorstelepítése**: egy közös felhasználói konfigurálás az, hogy a saját alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő Internet forgalmat inkább a helyszínen. A forgalom áramlását tüntetnek megsérti kapcsolat az Azure API Management szolgáltatással, mert a kimenő adatforgalmat a letiltott helyi, vagy NAT-címek, amelyek már nem használhatók a különböző Azure-végpontok egy felismerhetetlen készletéhez lenne. A megoldás, hogy egy (vagy több) felhasználó által definiált útvonalak megadása ([udr-EK][UDRs]), amely tartalmazza az Azure API Management az alhálózaton. Egy UDR helyett az alapértelmezett útvonal szembeni szerződéses kötelezettségeket vonatkozó alhálózati útvonalakat határozza meg.
  Ha lehetséges javasoljuk, hogy az alábbi konfigurációt használja:
 * Az ExpressRoute konfigurációs hirdeti 0.0.0.0/0, és alapértelmezés szerint kényszerített bújtatja minden kimenő forgalom helyszíni.
 * Az Azure API Management tartalmazó alkalmazva UDR 0.0.0.0/0 az Internet egy következő ugrás típusa határozza meg.
 A kombinált hatását, hogy ezeket a lépéseket az, hogy az alhálózat-szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatás, biztosítva ezzel az Azure API Management a kimenő Internet-hozzáféréssel.

* **Virtuális hálózati berendezések keresztül útválasztási**: konfigurációkat, amelyekkel egy olyan alapértelmezett útvonalat (0.0.0.0/0) UDR szánt internetes forgalmat az API Management alhálózatból egy hálózati Azure-beli virtuális készüléken keresztül blokkolja. felügyeleti forgalom Internet származik a virtuális hálózati alhálózat belül telepített API Management szolgáltatáspéldányra. Ez a konfiguráció nem támogatott.

>[!WARNING]  
>Az Azure API Management használata nem támogatott az ExpressRoute-konfigurációkat, amelyek **helytelenül kereszt-hirdetményt a magánhálózati társviszony-létesítési elérési utat a nyilvános társviszony-létesítési elérési útvonalak**. ExpressRoute-konfigurációk, amelyek rendelkeznek a nyilvános társviszony konfigurálva, a Microsoft Azure IP-címtartományok számos útvonal-hirdetéseinek kap Microsoft. Ha ezen címtartomány helytelenül határokon meghirdetett a magánhálózati társviszony-létesítési elérési úton, a záró eredménye, hogy minden kimenő hálózati rendszer érkező csomagokat, az Azure API Management példány alhálózati helytelenül kényszerített-tunneled az ügyfél a helyi hálózati infrastruktúra. Hálózati folyamatot az Azure API Management megszakítja. Ez a probléma megoldása, hogy állítsa le a kereszt-hirdetési útvonalak a nyilvános társviszony-létesítési elérési útról a magánhálózati társviszony-létesítési elérési utat.


## <a name="troubleshooting"> </a>Hibaelhárítás
* **A kezdeti telepítés**: egy alhálózatba API-kezelés szolgáltatás a kezdeti telepítés nem sikerül, ha először központi telepítése egy virtuális gép ugyanazon az alhálózaton történő javasoljuk. Tovább távoli asztali kapcsolatot a virtuális gép, és ellenőrizze, hogy nincs-e az egyes erőforrások alatt az azure-előfizetéshez az egyik kapcsolata 
    * Azure Storage blob
    * Azure SQL Database

 > [!IMPORTANT]
 > Után a kapcsolat ellenőrzése, akkor távolítsa el az alhálózatot az API Management telepítése előtt az alhálózaton, telepített erőforrások mindegyikének.

* **Növekményes frissítések**: Ha módosítja a hálózathoz, tekintse meg [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), ellenőrizheti, hogy az API Management szolgáltatás még nem már nem fér hozzá a kritikus erőforrásokat, amelyek azt határozzák meg. A kapcsolati állapot 15 percenként frissíteni kell.

* **Erőforrás-navigációs hivatkozásokkal**: az erőforrás-kezelő stílus vnet alhálózati telepítésekor API Management az alhálózat fenntart egy erőforrás-navigációs hivatkozás létrehozásával. Ha az alhálózat már tartalmaz egy erőforrás más szolgáltatótól származó, központi telepítési program **sikertelen**. Hasonlóképpen amikor egy API-kezelés szolgáltatás áthelyezése egy másik alhálózat vagy törölni, megszüntetjük adott erőforrás-navigációs hivatkozás. 

## <a name="subnet-size"> </a> Alhálózati Méretkövetelményt
Azure fenntartja az egyes IP-címek minden alhálózaton belül, és ezeknél a címeknél nem használható. Az első és utolsó IP-címek alhálózatok protokoll megfelelési, valamint három további címek az Azure-szolgáltatásokhoz használt számára vannak fenntartva. További információkért lásd: [vannak-e bármilyen korlátozás belül ezek alhálózatok IP-címeket használnak?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Mellett az IP-címeit az Azure virtuális hálózat infrastruktúra az Api Management feltünteti az alhálózat a fejlesztői SKU Premium Termékváltozat egységenként két IP-címet vagy egy IP-címet használ. Minden példány fenntartja a külső terheléselosztó további IP-címet. Ha olyan belső virtuális hálózat üzembe helyezve, egy további IP-címet igényel a a belső terheléselosztó.

Az alhálózat, ahol az API Management is telepíthető a minimális méret fent a számítást megadott van /29, amely 3 IP-címeket.

## <a name="routing"> </a> Útválasztás
+ Hozzáférést biztosít minden Szolgáltatásvégpontok fog foglalható le egy elosztott terhelésű nyilvános IP-cím (VIP).
+ Egy alhálózat IP-címtartomány (DIP) IP-címeit a vneten belül erőforrások eléréséhez használható, és egy nyilvános IP-cím (VIP) a virtuális hálózaton kívüli erőforrások eléréséhez használható.
+ Az elosztott terhelésű nyilvános IP-cím címnek az Áttekintés/Essentials panel az Azure portálon.

## <a name="limitations"> </a>Korlátozások
* Az API Management-példányokat tartalmazó alhálózat bármely más Azure-erőforrás típusa nem tartalmazhat.
* Az alhálózat és az API Management szolgáltatás ugyanahhoz az előfizetéshez kell lennie.
* Az API Management-példányokat tartalmazó alhálózat előfizetésekhez nem helyezhetők.
* A belső virtuális hálózat üzemmód több területi API Management telepítéseknél felhasználók különféle régiókban, a terheléselosztás kezeléséért, mivel az útvonal saját.
* Platform korlátai miatt nem működik, és az API Management szolgáltatás belső módban egy erőforrást egy másik régióban globálisan peered vnet közötti kapcsolatot. További információkért lásd: [egy virtuális hálózatán lévő erőforrásokat nem tud kommunikálni az Azure belső terheléselosztó virtuális hálózaton nincsenek társviszonyban](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Kapcsolódó tartalom
* [A virtuális hálózati kapcsolódás kiszolgáló VPN-átjáró használatával](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Egy virtuális hálózathoz csatlakozó különböző üzembe helyezési modellel](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [A követni kívánt API-Inspector használatával meghívja az Azure API Management](api-management-howto-api-inspector.md)
* [Virtuális hálózat – gyakori kérdések](../virtual-network/virtual-networks-faq.md)

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
