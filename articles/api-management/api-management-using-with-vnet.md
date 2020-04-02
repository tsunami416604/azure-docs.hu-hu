---
title: Az Azure API Management használata virtuális hálózatokkal
description: Ismerje meg, hogyan lehet beállítani egy kapcsolatot egy virtuális hálózathoz az Azure API Management ben, és hogyan érheti el a webszolgáltatásokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 462a44f7766e0ec52ba7156d6de5ae5261e21376
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547363"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Az Azure API Management használata virtuális hálózatokkal
Az Azure-beli virtuális hálózatokkal (VNET-ekkel) olyan nem internetalapú, irányítható hálózatokra helyezheti át Azure-erőforrásait, amelyekhez való hozzáférést Ön szabályozza. Ezek a hálózatok ezután különböző VPN-technológiák használatával csatlakoztathatók a helyszíni hálózatokhoz. Ha többet szeretne megtudni az Azure virtuális hálózatokról, kezdje az itt található információkkal: [Azure virtuális hálózat áttekintése](../virtual-network/virtual-networks-overview.md).

Az Azure API Management a virtuális hálózaton (VNET) belül is telepíthető, így hozzáférhet a hálózaton belüli háttérszolgáltatásokhoz. A fejlesztői portál és az API-átjáró beállítható úgy, hogy elérhető legyen az internetről vagy csak a virtuális hálózaton belül.

> [!NOTE]
> Az API-importálási dokumentum URL-címét nyilvánosan elérhető internetcímen kell üzemeltetni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőkre van szüksége:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Egy APIM-példány. További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET-kapcsolat engedélyezése

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Virtuális hálózat-kapcsolat engedélyezése az Azure Portalon

1. Az [Azure-portálon](https://portal.azure.com) megkeresheti az API-felügyeleti példányt. Keresse meg és válassza ki **az API Management szolgáltatásokat.**

2. Válassza ki az API Management-példányt.

3. Válassza **a Virtuális hálózat lehetőséget**.
4. Konfigurálja az API Management példányt egy virtuális hálózaton belül telepítendő.

    ![Az API Management virtuális hálózati menüje][api-management-using-vnet-menu]
5. Válassza ki a kívánt hozzáférési típust:

    * **Ki**: Ez az alapértelmezett beállítás. Az API Management nincs telepítve virtuális hálózatba.

    * **Külső**: Az API Management átjáró és fejlesztői portál a nyilvános internetről érhető el egy külső terheléselosztón keresztül. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

        ![Nyilvános társviszony-létesítés][api-management-vnet-public]

    * **Belső**: Az API Management átjáró és fejlesztői portál csak a virtuális hálózaton belül érhető el egy belső terheléselosztón keresztül. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

        ![Magánhálózati társviszony-létesítés][api-management-vnet-private]

6. Ha **a Külső** vagy **belső**lehetőséget választotta, megjelenik az összes olyan régió listája, ahol az API Management szolgáltatás ki van építve. Válasszon **egy helyet**, majd válassza ki a virtuális **hálózatot** és **az Alhálózatot.** A virtuális hálózatok listája a klasszikus és a Resource Manager virtuális hálózatok érhetők el az Azure-előfizetések, amelyek a konfigurált régióban vannak beállítva.

    > [!IMPORTANT]
    > Egy Azure API Management-példány üzembe helyezésekor egy Erőforrás-kezelő virtuális hálózat, a szolgáltatás nak egy dedikált alhálózat, amely nem tartalmaz más erőforrásokat, kivéve az Azure API Management-példányok. Ha egy Azure API Management-példány üzembe helyezésére tesz kísérletet egy erőforrás-kezelő i.

    Ezután válassza az **Alkalmaz** lehetőséget. Az API Management-példány **virtuális hálózati** lapja frissül az új virtuális hálózati és alhálózati választási lehetőségekkel.

    ![VPN kiválasztása][api-management-setup-vpn-select]

7. A felső navigációs sávon válassza a **Mentés**lehetőséget, majd a **Hálózati konfiguráció alkalmazása**lehetőséget.

> [!NOTE]
> Az API Management-példány VIP-címe minden alkalommal megváltozik, amikor a virtuális hálózat engedélyezve van vagy le van tiltva.
> A VIP-cím akkor is megváltozik, ha az API Management **külsőről** **belsőre**kerül, vagy fordítva.
>

> [!IMPORTANT]
> Ha eltávolítja az API Managementet egy virtuális hálózatról, vagy módosítja azt, amelyben telepítve van, a korábban használt virtuális hálózat legfeljebb hat órán keresztül zárolva maradhat. Ebben az időszakban nem lehet törölni a virtuális hálózatot, és nem lehet új erőforrást telepíteni hozzá. Ez a viselkedés a 2018-01-01-es és korábbi api-verziót használó ügyfelekre igaz. Az api-version 2019-01-01-es és újabb verzióját használó ügyfelek a virtuális hálózat felszabadul, amint a társított API Management szolgáltatás törlődik.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>VNET-kapcsolat engedélyezése PowerShell-parancsmagokkal
A Virtuális hálózat-kapcsolatot a PowerShell-parancsmagokkal is engedélyezheti.

* **Hozzon létre egy API Management szolgáltatást egy virtuális hálózaton belül:** A [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) parancsmag használatával hozzon létre egy Azure API Management szolgáltatást egy virtuális hálózaton belül.

* **Meglévő API-felügyeleti szolgáltatás telepítése egy virtuális hálózaton belül:** Az [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) parancsmag használatával helyezzen át egy meglévő Azure API Management szolgáltatást egy virtuális hálózaton belül.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Csatlakozás virtuális hálózaton belül üzemeltetett webszolgáltatáshoz
Miután az API Management szolgáltatás csatlakozik a virtuális hálózathoz, a háttérszolgáltatások eléréséhez belül nem különbözik a nyilvános szolgáltatások eléréséhez. Csak írja be a webszolgáltatás helyi IP-címét vagy állomásnevét (ha a DNS-kiszolgáló a virtuális hálózathoz van konfigurálva) a **webszolgáltatás URL-mezőjébe,** amikor új API-t hoz létre, vagy egy meglévőt szerkeszt.

![API hozzáadása vpn-ből][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Gyakori hálózati konfigurációs problémák
Az alábbiakban az API Management szolgáltatás virtuális hálózatra történő üzembe helyezése során előforduló gyakori helytelen konfigurációs problémák listája látható.

* **Egyéni DNS-kiszolgáló beállítása:** Az API Management szolgáltatás több Azure-szolgáltatástól függ. Ha az API Management egy virtuális hálózatban található egy egyéni DNS-kiszolgálóval, fel kell oldania az Azure-szolgáltatások állomásneveit. Kérjük, kövesse [ezt az](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) útmutatót az egyéni DNS-beállításokról. Lásd az alábbi portok táblázatát és az egyéb hálózati követelményeket.

> [!IMPORTANT]
> Ha egyéni DNS-kiszolgáló(ka)t kíván használni a virtuális hálózathoz, állítsa be, **mielőtt** egy API Management szolgáltatást telepítene hozzá. Ellenkező esetben frissítenie kell az API Management szolgáltatást minden alkalommal, amikor módosítja a DNS-kiszolgáló(ka)t a [Hálózati konfiguráció alkalmazása művelet futtatásával](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Az API-kezeléshez szükséges portok:** Az API-kezelést alkalmazó alhálózatba irányuló bejövő és kimenő forgalom a [Hálózati biztonsági csoport][Network Security Group]használatával vezérelhető. Ha ezen portok bármelyike nem érhető el, előfordulhat, hogy az API Management nem működik megfelelően, és elérhetetlenné válhat. Egy vagy több ilyen port blokkolása egy másik gyakori helytelen konfigurációs probléma, ha az API Management et használja a virtuális hálózattal.

<a name="required-ports"> </a> Ha egy API Management szolgáltatáspéldány egy virtuális hálózatban van tárolva, a következő táblázatban lévő portok használatosak.

| Forrás / Célport(ok) | Irány          | Átviteli protokoll |   [Szolgáltatási címkék](../virtual-network/security-overview.md#service-tags) <br> Forrás / Rendeltetési hely   | Cél (*)                                                 | Virtuális hálózat típusa |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Bejövő            | TCP                | INTERNET / VIRTUAL_NETWORK            | Ügyfélkommunikáció az API Management szolgáltatással                      | Külső             |
| * / 3443                     | Bejövő            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Felügyeleti végpont az Azure Portalhoz és a Powershellhez         | Külső & belső  |
| * / 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK / Tárolás             | **Az Azure Storage-tól való függőség**                             | Külső & belső  |
| * / 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) (adott esetben)                   | Külső & belső  |
| * / 1433                     | Kimenő           | TCP                | VIRTUAL_NETWORK / SQL                 | **Hozzáférés az Azure SQL-végpontokhoz**                           | Külső & belső  |
| * / 5671, 5672, 443          | Kimenő           | TCP                | VIRTUAL_NETWORK / EventHub            | Függőség az [Event Hub naplója házirendjéhez](api-management-howto-log-event-hubs.md) és figyelési ügynökhöz | Külső & belső  |
| * / 445                      | Kimenő           | TCP                | VIRTUAL_NETWORK / Tárolás             | Függőség az Azure fájlmegosztástól a [GIT-hez](api-management-configuration-repository-git.md)                      | Külső & belső  |
| * / 1886                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Az állapot nak az erőforrás-állapotban való közzétételéhez szükséges          | Külső & belső  |
| * / 443                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | [Diagnosztikai naplók és metrikák](api-management-howto-use-azure-monitor.md) közzététele                       | Külső & belső  |
| * / 25                       | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Csatlakozás az SMTP-továbbítóhoz e-mailek küldéséhez                    | Külső & belső  |
| * / 587                      | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Csatlakozás az SMTP-továbbítóhoz e-mailek küldéséhez                    | Külső & belső  |
| * / 25028                    | Kimenő           | TCP                | VIRTUAL_NETWORK / INTERNET            | Csatlakozás az SMTP-továbbítóhoz e-mailek küldéséhez                    | Külső & belső  |
| * / 6381 - 6383              | Bejövő & kimenő | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | A Redis szolgáltatás elérése a gépek közötti [díjkorlátozási](api-management-access-restriction-policies.md#LimitCallRateByKey) házirendekhez         | Külső & belső  |
| * / *                        | Bejövő            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure infrastruktúra terheléselosztó                          | Külső & belső  |

>[!IMPORTANT]
> Az API Management szolgáltatás sikeres üzembe helyezéséhez azok a portok szükségesek, *amelyeknél* a cél **félkövér.** A többi port blokkolása azonban a futó szolgáltatás használatának és figyelésének leépüléséhez vezet.

+ **TLS-funkció:** A TLS/SSL tanúsítványlánc-építés és -érvényesítés engedélyezéséhez az API Management szolgáltatásnak kimenő hálózati kapcsolatra van szüksége a ocsp.msocsp.com, mscrl.microsoft.com és crl.microsoft.com. Ez a függőség nem szükséges, ha az API Management szolgáltatásba feltöltött tanúsítványok tartalmazzák a hitelesítésszolgáltató gyökéréhez vezető teljes láncot.

+ **DNS-hozzáférés:** Kimenő hozzáférés szükséges az 53-as porton a DNS-kiszolgálókkal való kommunikációhoz. Ha egy VPN-átjáró másik végén létezik egyéni DNS-kiszolgáló, a DNS-kiszolgálónak elérhetőnek kell lennie az alhálózat üzemeltetési API Management szolgáltatásából.

+ **Metrikák és állapotfigyelés:** Kimenő hálózati kapcsolat az Azure Monitoring végpontok, amely a következő tartományokban oldható fel:

+ **Regionális szolgáltatáscímkék**": NSG-szabályok, amelyek lehetővé teszik a kimenő kapcsolatot a storage, SQL és EventHubs szolgáltatás címkék használhatják a regionális verziók e címkék megfelelő régióban az API Management-példány (például Storage.WestUS egy API Management-példány az USA nyugati régiójában). A több régiós telepítések, az NSG minden régióban lehetővé kell tennie a forgalmat a szolgáltatás címkék az adott régióban.

    | Azure környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net(**új**)</li><li>prod.warmpath.msftcloudes.com(**elavult )**</li><li>shoebox2.metrics.microsoftmetrics.com(**új**)</li><li>shoebox2.metrics.nsatc.net(**elavult )**</li><li>prod3.metrics.microsoftmetrics.com(**új**)</li><li>prod3.metrics.nsatc.net(**elavult)**</li><li>prod3-black.prod3.metrics.microsoftmetrics.com(**új**)</li><li>prod3-black.prod3.metrics.nsatc.net(**elavult )**</li><li>prod3-red.prod3.metrics.microsoftmetrics.com(**új**)</li><li>prod3-red.prod3.metrics.nsatc.net(**elavult )**</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com `East US 2` hol van eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com(**új**)</li><li>shoebox2.metrics.nsatc.net(**elavult )**</li><li>prod3.metrics.microsoftmetrics.com(**új**)</li><li>prod3.metrics.nsatc.net(**elavult)**</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com(**új**)</li><li>shoebox2.metrics.nsatc.net(**elavult )**</li><li>prod3.metrics.microsoftmetrics.com(**új**)</li><li>prod3.metrics.nsatc.net(**elavult)**</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> A fenti fürtök módosítása a dns zónával **.nsatc.net** -ről **.microsoftmetrics.com-re** többnyire DNS-módosítás. A fürt IP-címe nem változik.

+ **SMTP-továbbítás**: Kimenő hálózati kapcsolat az SMTP-továbbítóhoz, `smtpi-db3.msn.com` `smtpi-sin.msn.com` amely az állomás `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, és`ies.global.microsoft.com`

+ **Fejlesztői portál CAPTCHA**: Kimenő hálózati kapcsolat a fejlesztői portál CAPTCHA, amely feloldódik a házigazdák `client.hip.live.com` és `partner.hip.live.com`.

+ **Azure portal diagnosztika:** Az Api Management bővítmény virtuális hálózaton belüli használata esetén az `dc.services.visualstudio.com` Azure-kezelés bővítmény használatával a diagnosztikai naplók áramlásának engedélyezéséhez kimenő hozzáférésszükséges a 443-as porton. Ez segít a bővítmények használata során felmerülő problémák elhárításában.

+ **Bújtatási forgalom kényszerítése az on-prem tűzfalra express route vagy hálózati virtuális berendezés használatával:** A közös ügyfélkonfiguráció a saját alapértelmezett útvonaluk (0.0.0/0) meghatározása, amely az API Management delegált alhálózatából származó összes forgalmat a helyszíni tűzfalon vagy egy hálózati virtuális berendezésbe kényszeríti. Ez a forgalmi folyamat mindig megszakítja a kapcsolatot az Azure API Management, mert a kimenő forgalom vagy blokkolva van a helyszínen, vagy NAT'd egy felismerhetetlen címkészlet, amely már nem működik a különböző Azure-végpontok. A megoldás megköveteli, hogy nem egy pár dolgot:

  * Engedélyezze a szolgáltatásvégpontokat azon az alhálózaton, amelyben az API Management szolgáltatás telepítve van. [A szolgáltatásvégpontokat][ServiceEndpoints] engedélyezni kell az Azure Sql, az Azure Storage, az Azure EventHub és az Azure ServiceBus számára. Ha a végpontokat közvetlenül az API Management delegált alhálózatról engedélyezi ezekre a szolgáltatásokra, akkor a Microsoft Azure gerinchálózatát használhatják, amely optimális útválasztást biztosít a szolgáltatásforgalom számára. Ha a szolgáltatásvégpontok egy kényszerített bújtatott Api Management, a fenti Azure-szolgáltatások forgalma nem kényszerített bújtatás. A másik API Management szolgáltatás függőségi forgalma kényszerített bújtatott, és nem vész el, vagy az API Management szolgáltatás nem működik megfelelően.
    
  * Az API Management szolgáltatás felügyeleti szolgáltatásának felügyeleti végpontja között az internetről az API Management szolgáltatás felügyeleti végpontjáig irányuló összes vezérlősík-forgalmat az API Management által üzemeltetett bejövő IP-k egy adott készletén keresztül továbbítja. Ha a forgalom a bújtatott forgalom, a válaszok nem szimmetrikusan leképezése vissza ezeket a bejövő forrás IP-k. A korlátozás leküzdése érdekében hozzá kell adnunk a következő, felhasználó által definiált útvonalakat[(UdRs),][UDRs]hogy a forgalmat visszairányítsa az Azure-ba, és ezeket a gazdaútvonalakat "Internet" értékre állítja. A vezérlősík-forgalom bejövő IP-címkészlete dokumentálva van [a vezérlősík IP-címei között](#control-plane-ips)

  * Más API Management szolgáltatás függőségek, amelyek a kényszerítve bújtatott, kell lennie egy módja annak, hogy feloldja a gazdagépnév, és elérje a végpontot. Ezek közé tartozik a
      - Mérőszámok és állapotfigyelés
      - Az Azure Portal diagnosztikája
      - SMTP-váltó
      - Fejlesztői portál CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Hibaelhárítás
* **Kezdeti telepítés:** Ha az API Management szolgáltatás első telepítése egy alhálózatba nem sikerül, javasoljuk, hogy először telepítsen egy virtuális gépet ugyanabba az alhálózatba. Következő távoli asztal a virtuális gépre, és ellenőrizze, hogy van-e kapcsolat az egyik az egyes erőforrások az Azure-előfizetés
    * Azure Storage blob
    * Azure SQL Database
    * Azure storage-tábla

  > [!IMPORTANT]
  > Miután érvényesítette a kapcsolatot, győződjön meg arról, hogy távolítsa el az alhálózatban üzembe helyezett összes erőforrást, mielőtt az API Managementet az alhálózatba telepítené.

* **Növekményes frissítések:** A hálózat módosításakor tekintse meg a [NetworkStatus API című hivatkozást](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus)annak ellenőrzéséhez, hogy az API Management szolgáltatás nem veszítette-e el a hozzáférést a kritikus erőforrásokhoz, amelyektől függ. A kapcsolat állapotát 15 percenként frissíteni kell.

* **Erőforrás-navigációs hivatkozások:** Az Erőforrás-kezelő stílusú virtuális hálózatalhálózatba való üzembe helyezésekor az API Management leszervezi az alhálózatot egy erőforrás-navigációs hivatkozás létrehozásával. Ha az alhálózat már tartalmaz egy másik szolgáltatótól származó erőforrást, a telepítés **sikertelen**lesz. Hasonlóképpen, ha egy API Management szolgáltatást áthelyez egy másik alhálózatra, vagy törli, eltávolítjuk az erőforrás-navigációs hivatkozást.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Alhálózat méretére vonatkozó követelmény
Az Azure lefoglal néhány IP-címet az egyes alhálózatokon belül, és ezek a címek nem használhatók. Az alhálózatok első és utolsó IP-címei a protokollmegfelelőség hez vannak fenntartva, valamint további három cím az Azure-szolgáltatásokhoz. További információ: [Vannak-e korlátozások az IP-címek használatára ezeken az alhálózatokon belül?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure VNET-infrastruktúra által használt IP-címek mellett az alhálózat minden Api Management-példánya két IP-címet használ a prémium szintű termékváltozat egységenként, vagy egy IP-címet a fejlesztői termékváltozathoz. Minden példány egy további IP-címet foglal le a külső terheléselosztó számára. Belső virtuális hálózatra való üzembe helyezéskor a belső terheléselosztóhoz további IP-cím szükséges.

Mivel a számítás meghaladja a minimális mérete az alhálózat, amelyben API Management telepíthető /29, amely három használható IP-címeket ad.

Az API Management minden további méretezési egysége további két IP-címet igényel.

## <a name="routing"></a><a name="routing"> </a> Útválasztás
+ A terheléselosztásos nyilvános IP-cím (VIP) lesz fenntartva, hogy hozzáférést biztosítson az összes szolgáltatás végpontok.
+ Egy alhálózati IP-tartományból (DIP) származó IP-cím lesz használva a virtuális hálózaton belüli erőforrások eléréséhez, és egy nyilvános IP-cím (VIP) a virtuális hálózaton kívüli erőforrások eléréséhez.
+ A terheléselosztásos nyilvános IP-cím az Azure Portal on Overview/Essentials panelen található.

## <a name="limitations"></a><a name="limitations"> </a>Korlátozások
* AZ API Management-példányokat tartalmazó alhálózat nem tartalmazhat más Azure-erőforrástípusokat.
* Az alhálózatnak és az API Management szolgáltatásnak ugyanabban az előfizetésben kell lennie.
* Az API Management-példányokat tartalmazó alhálózat nem helyezhető át az előfizetések között.
* A belső virtuális hálózati módban konfigurált többrégiós API Management telepítések esetében a felhasználók felelősek a terheléselosztás kezeléséért több régióban, mivel ők az útválasztás.
* Egy másik régióban lévő globális társviszony-létesítési virtuális hálózat ban lévő erőforrásból származó kapcsolat belső módban nem fog működni a platform korlátozása miatt. További információ: [Erőforrások egy virtuális hálózatban nem tud kommunikálni az Azure belső terheléselosztó társviszony-létesített virtuális hálózat](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Vezérlősík IP-címei

Az IP-címeket az **Azure Environment**osztja el. A globális ként megjelölt bejövő kérelmek engedélyezésekor a **globális** ként megjelölt IP-címet a **régióspecifikus** IP-címmel együtt kell szerepelnie.

| **Azure környezet**|   **Régió**|  **IP-cím**|
|-----------------|-------------------------|---------------|
| Azure Public| USA déli középső régiója (globális)| 104.214.19.224|
| Azure Public| USA északi középső régiója (globális)| 52.162.110.80|
| Azure Public| USA nyugati középső régiója| 52.253.135.58|
| Azure Public| Dél-Korea középső régiója| 40.82.157.167|
| Azure Public| Az Egyesült Királyság nyugati régiója| 51.137.136.0|
| Azure Public| Nyugat-Japán| 40.81.185.8|
| Azure Public| USA északi középső régiója| 40.81.47.216|
| Azure Public| Az Egyesült Királyság déli régiója| 51.145.56.125|
| Azure Public| Nyugat-India| 40.81.89.24|
| Azure Public| USA keleti régiója| 52.224.186.99|
| Azure Public| Nyugat-Európa| 51.145.179.78|
| Azure Public| Kelet-Japán| 52.140.238.179|
| Azure Public| Közép-Franciaország| 40.66.60.111|
| Azure Public| Kelet-Kanada| 52.139.80.117|
| Azure Public| Egyesült Arab Emírségek északi| 20.46.144.85|
| Azure Public| Dél-Brazília| 191.233.24.179|
| Azure Public| Délkelet-Ázsia| 40.90.185.46|
| Azure Public| Dél-Afrika Észak-Afrika| 102.133.130.197|
| Azure Public| Közép-Kanada| 52.139.20.34|
| Azure Public| Dél-Korea déli régiója| 40.80.232.185|
| Azure Public| Közép-India| 13.71.49.1|
| Azure Public| USA nyugati régiója| 13.64.39.16|
| Azure Public| Délkelet-Ausztrália| 20.40.160.107|
| Azure Public| Ausztrália középső régiója| 20.37.52.67|
| Azure Public| Dél-India| 20.44.33.246|
| Azure Public| USA középső régiója| 13.86.102.66|
| Azure Public| Kelet-Ausztrália| 20.40.125.155|
| Azure Public| USA nyugati régiója, 2.| 51.143.127.203|
| Azure Public| USA keleti része 2 EUAP| 52.253.229.253|
| Azure Public| USA középső régiója| 52.253.159.160|
| Azure Public| USA déli középső régiója| 20.188.77.119|
| Azure Public| USA 2. keleti régiója| 20.44.72.3|
| Azure Public| Észak-Európa| 52.142.95.35|
| Azure Public| Kelet-Ázsia| 52.139.152.27|
| Azure Public| Dél-Franciaország| 20.39.80.2|
| Azure Public| Svájc Nyugati| 51.107.96.8|
| Azure Public| Ausztrália 2. középső régiója| 20.39.99.81|
| Azure Public| Egyesült Arab Emírségek központi| 20.37.81.41|
| Azure Public| Észak-Svájc| 51.107.0.91|
| Azure Public| Dél-Afrika Nyugati| 102.133.0.79|
| Azure Public| Németország Nyugat-Közép| 51.116.96.0|
| Azure Public| Németország Észak| 51.116.0.0|
| Azure Public| Norvégia Kelet| 51.120.2.185|
| Azure Public| Norvégia Nyugati| 51.120.130.134|
| Azure China 21Vianet| Kína Észak (Globális)| 139.217.51.16|
| Azure China 21Vianet| Kína Keleti (Globális)| 139.217.171.176|
| Azure China 21Vianet| Észak-Kína| 40.125.137.220|
| Azure China 21Vianet| Kelet-Kína| 40.126.120.30|
| Azure China 21Vianet| Kína Észak 2| 40.73.41.178|
| Azure China 21Vianet| Kína Keleti 2| 40.73.104.4|
| Azure Government| USGov Virginia (globális)| 52.127.42.160|
| Azure Government| USGov Texas (globális)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD Közép| 52.182.32.132|
| Azure Government| USDoD Kelet| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Kapcsolódó tartalom
* [Virtuális hálózat csatlakoztatása háttérrendszerhez vpn átjáró használatával](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Virtuális hálózat csatlakoztatása különböző telepítési modellekből](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Hívások nyomon követése az Azure API-kezelésben az API-felügyelő vel](api-management-howto-api-inspector.md)
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
