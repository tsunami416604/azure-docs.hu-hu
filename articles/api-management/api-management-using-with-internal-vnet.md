---
title: "Belső virtuális hálózatok az Azure API Management használata |} Microsoft Docs"
description: "Megtudhatja, hogyan beállítása és konfigurálása az Azure API Management belső virtuális hálózaton"
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: cf062cfcbbb2454adf20a06c31c81a60f6f5719f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Belső virtuális hálózat az Azure API Management szolgáltatással
Azure virtuális hálózatok, az Azure API Management kezelheti API-k nem érhető el, az interneten. A kapcsolat VPN technológiáin számos érhetők el. Az API Management olyan virtuális hálózaton belül két fő módban telepíthető:
* Külső
* Belső


Az API Management belső virtuális hálózat módban telepíti, ha a szolgáltatás végpontok (átjáró, a fejlesztői portálján, az Azure-portált, közvetlen felügyelet és Git) láthatók csak egy virtuális hálózatban, amely a elérését Ön szabályozza. A végpontok egyike a nyilvános DNS-kiszolgálón van regisztrálva.

Az API Management belső módban, érhet el a következő esetekben:
* Ellenőrizze a harmadik felek kívülről-webhelyek vagy Azure ExpressRoute VPN-kapcsolatok használatával biztonságosan elérhetik a saját adatközpont-környezetben üzemeltetett API.
* Engedélyezze a hibrid felhős rendszerekben jelentkezik, mintha a API-k felhőalapú és helyszíni API-k közös átjárón keresztül.
* A több földrajzi helyeken található egy átjáró egyetlen végpont-ás tárolt API-k kezelése. 


## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie kell:

+ **Aktív Azure-előfizetés**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Az Azure API Management példány**. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

## <a name="enable-vpn"></a>Belső virtuális hálózat létrehozása az API Management
Az API-kezelés szolgáltatás a belső virtuális hálózat belső terheléselosztón (ILB) mögött helyezkedik el.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Engedélyezze a virtuális hálózati kapcsolat az Azure portál használatával

1. Keresse meg az Azure API Management-példány a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **virtuális hálózati**.
3. A virtuális hálózaton belül telepítendő API Management-példány beállítása.

    ![Az Azure API Management a belső virtuális hálózat beállításának menü][api-management-using-internal-vnet-menu]

4. Kattintson a **Mentés** gombra.

Miután a telepítés sikeres, a szolgáltatás belső virtuális IP-címe megjelenik az irányítópulton.

![Az API Management irányítópult konfigurálva a belső virtuális hálózattal][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>A virtuális hálózati kapcsolat engedélyezése a PowerShell-parancsmagok használatával
Engedélyezheti a virtuális hálózati kapcsolat PowerShell-parancsmagok használatával.

* A virtuális hálózaton belül az API Management szolgáltatás létrehozása: parancsmag [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) hozzon létre egy Azure API Management szolgáltatást, a virtuális hálózaton belül, és konfigurálja úgy, hogy a belső virtuális hálózat típusával.

* A virtuális hálózaton belül az meglévő API Management-szolgáltatások üzembe: parancsmag [frissítés-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) helyezze át a meglévő API-kezelés szolgáltatás a virtuális hálózaton belül, és konfigurálja úgy, hogy a belső használatára virtuális hálózat típusát.

## <a name="apim-dns-configuration"></a>DNS-konfiguráció
Ha az API Management külső virtuális hálózat módban van, a DNS Azure kezeli. Belső virtuális hálózat mód felügyelni a saját útválasztási kell.

> [!NOTE]
> API-kezelés szolgáltatás nem figyel IP-címekről érkező kérésekre. Csak a szolgáltatás végpontját a konfigurált kérelmére reagálás. Ezeket a végpontokat a következők: átjáró, a fejlesztői portálján, a Azurethe portált, közvetlen felügyelet végpont és Git.

### <a name="access-on-default-host-names"></a>Az alapértelmezett állomásnevek hozzáférés
Az API Management szolgáltatásnak, például a "contoso" nevű létrehozásakor a következő végpontok alapértelmezés szerint vannak konfigurálva:

   * Az átjáróként vagy proxyként: contoso.azure-api.net

   * az Azure portál és a fejlesztői portálján: contoso.portal.azure-api.net

   * Közvetlen felügyelet végpont: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

API-kezelés szolgáltatás a végpontokkal eléréséhez hozzon létre egy virtuális gép a virtuális hálózathoz van telepítve az API Management alhálózat. Ha a szolgáltatás belső virtuális IP-cím 10.0.0.5, leképezheti a hosts fájl % SystemDrive%\drivers\etc\hosts, az alábbiak szerint:

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

Ezután hozzáférhetnek a Szolgáltatásvégpontok létrehozott virtuális gépről. Ha egy egyéni DNS-kiszolgáló egy virtuális hálózatot használ, is A DNS-rekordok létrehozása, és ezeket a végpontokat hozzáférni bárhonnan a virtuális hálózat. 

### <a name="access-on-custom-domain-names"></a>Egyéni tartománynevekkel hozzáférés

   1. Ha nem szeretné, az API Management szolgáltatás az alapértelmezett állomásneveket eléréséhez, a szolgáltatási végpont a következő ábrán látható módon egyéni tartományneveket állíthat be: 

   ![Az egyéni tartománynév beállítása az API Management][api-management-custom-domain-name]

   2. Akkor is létrehozhat a rekordokat a DNS-kiszolgáló eléréséhez a végpontok, amelyek csak a virtuális hálózaton belülről érhetők el.

## <a name="routing"></a> Útválasztás
+ Egy elosztott terhelésű titkos virtuális IP-cím az alhálózat címtartományából lesz fenntartva, és az API Management szolgáltatás végpontok a vneten belül eléréséhez használt.
+ Egy elosztott terhelésű nyilvános IP-cím (VIP) is lehet lefoglalni csak porton 3443 a felügyeleti szolgáltatás végpontjának eléréséhez.
+ Egy alhálózat IP-címtartomány (DIP) IP-címeit a vneten belül erőforrások eléréséhez használható, és egy nyilvános IP-cím (VIP) a virtuális hálózaton kívüli erőforrások eléréséhez használható.
+ Az elosztott terhelésű nyilvános és magánhálózati IP-címek az Áttekintés/Essentials panel az Azure portálon található.

## <a name="related-content"></a>Kapcsolódó tartalom
További tudnivalókért tekintse meg a következő cikkeket:
* [Általános hálózati konfigurációs problémák egy virtuális hálózatot az Azure API Management beállítása közben][Common network configuration problems]
* [Virtuális hálózat – gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [A DNS-rekord létrehozása](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

