---
title: Az Azure API Management használata virtuális hálózatokkal
description: Ismerje meg, hogyan lehet kapcsolatot létesíteni az Azure-beli virtuális hálózattal API Management és hogyan érheti el a webszolgáltatásokat.
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
ms.openlocfilehash: d643f242182959f98fa9476f4a701b82cf1c2800
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509642"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Az Azure API Management használata virtuális hálózatokkal
Az Azure Virtual Networks (virtuális hálózatok) lehetővé teszi, hogy az Azure-erőforrások bármelyikét olyan, nem internetes útválasztású hálózatba helyezze, amelynek hozzáférését szabályozhatja. Ezek a hálózatok ezután különböző VPN-technológiákkal csatlakozhatnak a helyszíni hálózatokhoz. Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért tekintse meg az alábbi információkat: Az [Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

Az Azure API Management a virtuális hálózaton (VNET) belül üzembe helyezhető, így a hálózaton belül elérhetővé válik a háttér-szolgáltatások. A fejlesztői portál és az API-átjáró úgy konfigurálható, hogy az internetről vagy csak a virtuális hálózaton belül legyen elérhető.

> [!NOTE]
> Az Azure API Management a klasszikus és a Azure Resource Manager virtuális hálózatok is támogatja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Egy APIM-példány. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>VNET-kapcsolatok engedélyezése

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>VNET-kapcsolat engedélyezése a Azure Portal használatával

1. Navigáljon a [Azure Portal](https://portal.azure.com/)APIM-példányához.
2. Válassza a **Virtual Network**lehetőséget.
3. Konfigurálja a API Management példányt egy virtuális hálózaton belül üzembe helyezéshez.

    ![API Management virtuális hálózatának menüje][api-management-using-vnet-menu]
4. Válassza ki a kívánt hozzáférési típust:

   * Kikapcsolva: ez az alapértelmezett. API Management nincs üzembe helyezve virtuális hálózatban.

   * **Külső**: a API Management átjáró és a fejlesztői portál a nyilvános internetről külső terheléselosztó használatával érhető el. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

     ![Nyilvános társviszony-létesítés][api-management-vnet-public]

   * **Belső**: a API Management átjáró és a fejlesztői portál csak a virtuális hálózaton belülről érhető el belső terheléselosztó használatával. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

     ![Magánhálózati társviszony-létesítés][api-management-vnet-private]

     Ekkor megjelenik az összes olyan régió listája, ahol a API Management szolgáltatás kiépítve. Válasszon egy VNET és alhálózatot minden régióhoz. A lista az Azure-előfizetésekben elérhető klasszikus és Resource Manager-alapú virtuális hálózatokkal együtt van feltöltve, amelyek a konfigurált régióban vannak beállítva.

     > [!IMPORTANT]
     > Ha Azure API Management-példányt telepít egy Resource Manager-VNET, a szolgáltatásnak olyan dedikált alhálózaton kell lennie, amely nem tartalmaz más erőforrásokat, kivéve az Azure API Management-példányokat. Ha egy Azure API Management-példány üzembe helyezésére tett kísérletet egy olyan Resource Manager VNET-alhálózatra, amely más erőforrásokat is tartalmaz, a telepítés sikertelen lesz.
     >

     ![VPN kiválasztása][api-management-setup-vpn-select]

5. Kattintson a felső navigációs sávban a **Mentés** gombra.
6. A felső navigációs sávon kattintson a **hálózati konfiguráció alkalmazása** lehetőségre.

> [!NOTE]
> A API Management példány virtuális IP-címe minden alkalommal megváltozik, amikor a VNET engedélyezve vagy le van tiltva.
> A virtuális IP-cím akkor is megváltozik, ha a API Management külsőről **belsőre** vagy fordítva van áthelyezve
>

> [!IMPORTANT]
> Ha eltávolítja a API Management egy VNET, vagy megváltoztatja azt, akkor a korábban használt VNET legfeljebb két óráig maradhat. Ebben az időszakban nem lehet törölni a VNET, vagy új erőforrást telepíteni.

## <a name="enable-vnet-powershell"> </a>VNET-kapcsolatok engedélyezése a PowerShell-parancsmagok használatával
A VNET-kapcsolatot a PowerShell-parancsmagok használatával is engedélyezheti

* **Hozzon létre egy API Management szolgáltatást egy VNET belül**: A [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) parancsmag használatával hozzon létre egy Azure API Management szolgáltatást egy VNET belül.

* **Meglévő API Management-szolgáltatás üzembe helyezése egy VNET belül**: Az [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) parancsmag használatával helyezzen át egy meglévő Azure API Management-szolgáltatást egy Virtual Network belül.

## <a name="connect-vnet"> </a>Kapcsolódás virtuális hálózaton belül üzemeltetett webszolgáltatáshoz
Miután a API Management szolgáltatás csatlakozott a VNET, a háttérbeli szolgáltatások elérése nem különbözik a nyilvános szolgáltatásokhoz való hozzáféréstől. Csak írja be a helyi IP-címet vagy az állomásnevet (ha a DNS-kiszolgáló a VNET van konfigurálva) a webszolgáltatás **URL-** címe MEZŐBE új API létrehozásakor vagy meglévő szerkesztésekor.

![API hozzáadása VPN-ből][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Gyakori hálózati konfigurációs problémák
A következő lista felsorolja azokat a gyakori konfigurációs problémákat, amelyek a API Management szolgáltatás egy Virtual Network történő telepítésekor fordulnak elő.

* **Egyéni DNS-kiszolgáló beállítása**: A API Management szolgáltatás számos Azure-szolgáltatástól függ. Ha a API Management egy egyéni DNS-kiszolgálóval rendelkező VNET tárolja, az Azure-szolgáltatások állomásnevét fel kell oldania. Kérjük, kövesse [ezt az](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) útmutatót az egyéni DNS-telepítésre vonatkozóan. Tekintse meg az alábbi portok táblázatot, valamint az egyéb hálózati követelményeket a hivatkozáshoz.

> [!IMPORTANT]
> Ha egyéni DNS-kiszolgáló (ka) t szeretne használni a VNET, akkor a API Management szolgáltatás üzembe helyezése **előtt** állítsa be. Ellenkező esetben frissítenie kell a API Management szolgáltatást minden alkalommal, amikor módosítja a DNS-kiszolgáló (ka) t a [hálózati konfiguráció alkalmazása művelet](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) futtatásával.

* **A API Managementhoz szükséges portok**: A bejövő és a kimenő forgalom abba az alhálózatba, amelyben a API Management telepítve van, a [hálózati biztonsági csoport][Network Security Group]használatával vezérelhető. Ha a portok bármelyike nem érhető el, előfordulhat, hogy API Management nem működik megfelelően, és elérhetetlenné válhat. Ha a portok közül egy vagy több le van tiltva, akkor a API Management VNET való használatakor a rendszer egy másik gyakori konfigurációs problémát is jelent.

<a name="required-ports"></a> Ha egy API Management Service-példány egy VNET üzemel, a rendszer a következő táblázatban található portokat használja.

| Forrás/cél port (ok) | Direction          | Átviteli protokoll |   [Szolgáltatás címkéi](../virtual-network/security-overview.md#service-tags) <br> Forrás/cél   | Cél (*)                                                 | Virtual Network típusa |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Bejövő            | TCP                | INTERNET/VIRTUAL_NETWORK            | Ügyfél-kommunikáció API Management                      | Külső             |
| * / 3443                     | Bejövő            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Felügyeleti végpont a Azure Portal és a PowerShell számára         | Külső & belső  |
| * / 80, 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK/Storage             | **Függőség az Azure Storage-ban**                             | Külső & belső  |
| * / 80, 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Azure Active Directory (ha van ilyen)                   | Külső & belső  |
| * / 1433                     | Kimenő           | TCP                | VIRTUAL_NETWORK/SQL                 | **Hozzáférés az Azure SQL-végpontokhoz**                           | Külső & belső  |
| * / 5672                     | Kimenő           | TCP                | VIRTUAL_NETWORK/EventHub            | Az Event hub-házirend és a figyelési ügynök közötti függőség | Külső & belső  |
| * / 445                      | Kimenő           | TCP                | VIRTUAL_NETWORK/Storage             | Függőség a GIT-hez készült Azure-Fájlmegosztástól                      | Külső & belső  |
| * / 1886                     | Kimenő           | TCP                | VIRTUAL_NETWORK/INTERNET            | A Resource Health állapotának közzétételéhez szükséges.          | Külső & belső  |
| */443                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Diagnosztikai naplók és mérőszámok közzététele                        | Külső & belső  |
| */25                       | Kimenő           | TCP                | VIRTUAL_NETWORK/INTERNET            | Kapcsolódás az SMTP Relayhez az e-mailek küldéséhez                    | Külső & belső  |
| */587                      | Kimenő           | TCP                | VIRTUAL_NETWORK/INTERNET            | Kapcsolódás az SMTP Relayhez az e-mailek küldéséhez                    | Külső & belső  |
| * / 25028                    | Kimenő           | TCP                | VIRTUAL_NETWORK/INTERNET            | Kapcsolódás az SMTP Relayhez az e-mailek küldéséhez                    | Külső & belső  |
| * / 6381 - 6383              | Bejövő & kimenő | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Az Azure cache elérése a Redis-példányok között a RoleInstances között          | Külső & belső  |
| * / *                        | Bejövő            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure-infrastruktúra Load Balancer                          | Külső & belső  |

>[!IMPORTANT]
> Azok a portok, amelyekhez a *cél* **félkövér** , szükségesek ahhoz, hogy API Management szolgáltatás üzembe helyezése sikeres legyen. A többi port blokkolásával azonban romlást okozhat a futó szolgáltatás használatának és figyelésének képessége.

+ **SSL-funkciók**: Az SSL-tanúsítványlánc létrehozásának és ellenőrzésének engedélyezéséhez a API Management szolgáltatásnak kimenő hálózati kapcsolatra van szüksége a ocsp.msocsp.com, a mscrl.microsoft.com és a crl.microsoft.com. Ez a függőség nem szükséges, ha a API Management feltöltött bármelyik tanúsítvány teljes láncot tartalmaz a HITELESÍTÉSSZOLGÁLTATÓ gyökeréhez.

+ **DNS-hozzáférés**: A DNS-kiszolgálókkal folytatott kommunikációhoz a 53-es porton kimenő hozzáférés szükséges. Ha egy VPN-átjáró másik végén létezik egy egyéni DNS-kiszolgáló, a DNS-kiszolgálónak elérhetőnek kell lennie a API Management üzemeltető alhálózaton.

+ **Metrikák és állapot-figyelés**: Kimenő hálózati kapcsolat az Azure monitoring-végpontokkal, amelyek a következő tartományokban oldhatók fel:

    | Azure-környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. Warm.ingestion.msftcloudes.com, `East US 2` ahol a eastus2.Warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP-továbbító**: Az SMTP-továbbító kimenő hálózati kapcsolata, amely a `smtpi-co1.msn.com`gazdagép `smtpi-db3.msn.com`, `smtpi-ch1.msn.com` `smtpi-sin.msn.com` a, és a`ies.global.microsoft.com`

+ **Fejlesztői portál CAPTCHA**: A fejlesztői portál KAPITÁNYának kimenő hálózati kapcsolata, amely a gazdagép `client.hip.live.com`alatt oldódik fel.

+ **Azure Portal diagnosztika**: Ha a API Management bővítményt a Virtual Networkon belülről kívánja engedélyezni a diagnosztikai naplók Azure Portal, akkor a 443- `dc.services.visualstudio.com` as porton való kimenő hozzáférésre van szükség. Ez segít a bővítmények használata során felmerülő problémák elhárításában.

+ **A helyszíni tűzfal felé irányuló forgalom kényszerítése expressz útvonal vagy hálózati virtuális készülék használatával**: Egy közös ügyfél-konfiguráció a saját alapértelmezett útvonal (0.0.0.0/0) meghatározása, amely a API Management delegált alhálózatról érkező összes forgalmat egy helyszíni tűzfalon vagy egy hálózati virtuális berendezésen keresztüli folyamatnak kényszeríti. Ez a forgalmi folyamat mindig megszakítja az Azure API Management kapcsolatát, mivel a kimenő forgalom vagy a helyszínen van letiltva, vagy a NAT-t olyan, a különböző Azure-végpontokkal már nem működő címekből álló halmazba kívánja felismerni. A megoldáshoz pár dolgot kell tennie:

  * Engedélyezze a szolgáltatási végpontokat azon az alhálózaton, amelyben a API Management szolgáltatás telepítve van. A [szolgáltatási végpontokat][ServiceEndpoints] engedélyezni kell az Azure SQL, az Azure Storage, az Azure EventHub és az Azure ServiceBus számára. A végpontok közvetlenül a API Management delegált alhálózatról ezekbe a szolgáltatásokba való engedélyezése lehetővé teszi, hogy a Microsoft Azure gerinc hálózatot használják, amely optimális útválasztást biztosít a szolgáltatás forgalmához. Ha kényszerített bújtatású API-felügyelettel rendelkező szolgáltatási végpontokat használ, a fenti Azure-szolgáltatások forgalma nem kényszerített bújtatással. A másik API Management szolgáltatás függőségi forgalma kényszerített bújtatású, és nem lehet elveszett, vagy a API Management szolgáltatás nem fog megfelelően működni.
    
  * Az internetről a API Management szolgáltatás felügyeleti végpontja felé irányuló összes vezérlési sík a API Management által üzemeltetett bejövő IP-címekre irányítja át a forgalmat. A forgalom kényszerített bújtatása esetén a válaszok nem lesznek szimmetrikusan leképezve a bejövő forrás IP-címekre. A korlátozás leküzdéséhez hozzá kell adnia a következő, felhasználó által megadott útvonalakat ([UDR][UDRs]) a forgalom Azure-ba történő visszairányításához. ehhez a gazdagép-útvonalak célhelyét az "Internet" értékre kell állítani. A vezérlési sík forgalmához tartozó bejövő IP-címek készlete a következő:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * A kényszerített bújtatással rendelkező más API Management szolgáltatási függőségek esetében az állomásnév feloldására és a végpont elérésére van lehetőség. Ilyenek például a következők:
      - Metrikák és állapot-figyelés
      - Azure Portal diagnosztika
      - SMTP-továbbító
      - Fejlesztői portál CAPTCHA

## <a name="troubleshooting"> </a>Hibaelhárítás
* **Kezdeti beállítás**: Ha a API Management szolgáltatás központi telepítése egy alhálózatba nem sikerül, azt javasoljuk, hogy először telepítsen egy virtuális gépet ugyanabba az alhálózatba. A következő Távoli asztalt a virtuális gépre, és ellenőrizze, hogy van-e kapcsolat az Azure-előfizetésében található egyes erőforrások valamelyikével
    * Azure Storage blob
    * Azure SQL Database
    * Azure Storage-tábla

  > [!IMPORTANT]
  > A kapcsolat ellenőrzése után mindenképpen távolítsa el az alhálózaton üzembe helyezett összes erőforrást, mielőtt a API Management az alhálózatba telepítené.

* **Növekményes frissítések**: Ha módosítja a hálózatot, tekintse meg a [NETWORKSTATUS API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)-t annak ellenőrzéséhez, hogy a API Management szolgáltatás nem vesztett-e el semmilyen kritikus erőforráshoz való hozzáférést, amelytől függ. A kapcsolati állapotot 15 percenként kell frissíteni.

* **Erőforrás-navigációs hivatkozások**: Ha Resource Manager stílusú vnet alhálózatra telepíti, API Management az alhálózatot az erőforrás-navigációs hivatkozás létrehozásával. Ha az alhálózat már tartalmaz egy másik szolgáltatótól származó erőforrást, akkor a telepítés **sikertelen**lesz. Hasonlóképpen, ha egy API Management szolgáltatást egy másik alhálózatra helyez át, vagy törli azt, akkor az erőforrás-navigációs hivatkozás el lesz távolítva.

## <a name="subnet-size"></a> Alhálózat méretének követelménye
Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure VNET-infrastruktúra által használt IP-címeken kívül az alhálózat minden egyes API Management-példánya két IP-címet használ a prémium SKU vagy egy IP-cím alapján a fejlesztői SKU számára. Minden példány fenntart egy további IP-címet a külső terheléselosztó számára. Belső vnet való üzembe helyezéskor további IP-címet igényel a belső terheléselosztó számára.

Az alhálózat minimális mérete feletti számítás miatt, amelyben a API Management telepíthető a/29, amely három IP-címet ad meg.

## <a name="routing"></a> Útválasztás
+ Az elosztott terhelésű nyilvános IP-cím (VIP) le lesz foglalva az összes szolgáltatási végpont elérésének biztosításához.
+ A rendszer az alhálózati IP-címtartomány (DIP) IP-címét fogja használni a vnet található erőforrások eléréséhez, és a nyilvános IP-címet (VIP) használja a vnet kívüli erőforrások elérésére.
+ Az elosztott terhelésű nyilvános IP-cím a Azure Portal áttekintés/Essentials paneljén található.

## <a name="limitations"> </a>Korlátozások
* API Management példányokat tartalmazó alhálózat nem tartalmazhat más Azure-erőforrás-típusokat.
* Az alhálózatnak és a API Management szolgáltatásnak ugyanahhoz az előfizetéshez kell tartoznia.
* API Management példányokat tartalmazó alhálózat nem helyezhető át az előfizetések között.
* A belső virtuális hálózati módban konfigurált többrégiós API Management üzemelő példányok esetében a felhasználók feladata, hogy a terheléselosztást több régión keresztül kezelhesse, ahogy az Útválasztás tulajdonosa.
* Ha egy másik régióban lévő, globálisan összekötött VNET lévő erőforrással kapcsolódik, API Management a szolgáltatás belső módban, a platform korlátozása miatt nem fog működni. További információ: az [egyik virtuális hálózat erőforrásai nem tudnak kommunikálni az Azure belső terheléselosztó szolgáltatásával a társ virtuális hálózatban](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) .


## <a name="related-content"> </a>Kapcsolódó tartalom
* [Virtual Network csatlakoztatása a háttérben a VPN Gateway használatával](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Virtual Network csatlakoztatása különböző üzembe helyezési modellből](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Az API Inspector használata a hívások nyomkövetéséhez az Azure-ban API Management](api-management-howto-api-inspector.md)
* [Virtual Network GYIK](../virtual-network/virtual-networks-faq.md)
* [Szolgáltatás címkéi](../virtual-network/security-overview.md#service-tags)

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
