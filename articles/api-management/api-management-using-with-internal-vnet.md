---
title: Az Azure API Management használata belső virtuális hálózatokkal
titleSuffix: Azure API Management
description: Az Azure API Management beállítása és konfigurálása belső virtuális hálózaton
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841863"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Az Azure API Management használata belső virtuális hálózattal
Az Azure virtuális hálózatok segítségével az Azure API Management képes kezelni az interneten nem elérhető API-kat. Számos VPN-technológia áll rendelkezésre a kapcsolat hozására. Az API Management két fő módban is telepíthető egy virtuális hálózaton belül:
* Külső
* Belső

Amikor az API Management belső virtuális hálózati módban telepíti a központi telepítést, az összes szolgáltatásvégpont (a proxyátjáró, a fejlesztői portál, a közvetlen felügyelet és a Git) csak egy olyan virtuális hálózaton belül látható, amelyhez ön szabályozza a hozzáférést. Egyik szolgáltatásvégpont sincs regisztrálva a nyilvános DNS-kiszolgálón.

> [!NOTE]
> Mivel a szolgáltatásvégpontokhoz nincsenek DNS-bejegyzések, ezek a végpontok nem lesznek elérhetők, amíg [a DNS nincs konfigurálva](#apim-dns-configuration) a virtuális hálózathoz.

Az API Management belső módban történő használatával a következő eseteket érheti el:

* A saját adatközpontjában üzemeltetett API-kat biztonságosan elérhetővé teheti harmadik felek számára a helyek közötti vagy az Azure ExpressRoute VPN-kapcsolatok használatával.
* Hibrid felhőbeli forgatókönyvek engedélyezése a felhőalapú API-k és a helyszíni API-k közös átjárón keresztül történő engedélyezéséhez.
* Egyetlen átjáróvégpont használatával kezelheti a több földrajzi helyen üzemeltetett API-kat.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőkre van szüksége:

+ **Aktív Azure-előfizetés**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Egy Azure API Management példány.** További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).
+ Ha egy API Management szolgáltatás telepítve van egy virtuális hálózatban, [a portok listáját](./api-management-using-with-vnet.md#required-ports) használja a rendszer, és meg kell nyitni. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>API-kezelés létrehozása belső virtuális hálózatban
A belső virtuális hálózat API Management szolgáltatása egy [belső terheléselosztó (klasszikus)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)mögött található. Ez az egyetlen elérhető lehetőség, és nem módosítható.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Virtuális hálózati kapcsolat engedélyezése az Azure Portalhasználatával

1. Tallózással keresse meg az Azure API Management-példányt az [Azure Portalon.](https://portal.azure.com/)
2. Válassza **a Virtuális hálózat lehetőséget**.
3. Konfigurálja az API Management példányt a virtuális hálózaton belül telepítendő.

    ![Az Azure API Management belső virtuális hálózatban való beállításának menüje][api-management-using-internal-vnet-menu]

4. Kattintson a **Mentés** gombra.

Miután a központi telepítés sikeres, meg kell jelennie **a privát** virtuális IP-cím és az API Management szolgáltatás **nyilvános** virtuális IP-címe az áttekintő panelen. A **privát** virtuális IP-cím egy terheléselosztásos IP-cím az API `gateway` `portal`Management `management` delegált alhálózaton belülről, amelyen keresztül a , és `scm` a végpontok elérhetők. A **nyilvános** virtuális IP-cím **csak** a `management` vezérlősík-forgalmat a 3443-as porton keresztül, és le zárható az [ApiManagement][ServiceTags] servicetag.

![API Management irányítópult belső virtuális hálózattal konfigurálva][api-management-internal-vnet-dashboard]

> [!NOTE]
> Az Azure Portalon elérhető tesztkonzol nem fog működni a **belső** virtuális hálózat által telepített szolgáltatás, mivel az átjáró URL-címe nincs regisztrálva a nyilvános DNS-en. Ehelyett a **fejlesztői portálon**biztosított tesztkonzolt kell használnia.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Virtuális hálózati kapcsolat engedélyezése PowerShell-parancsmagok használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A virtuális hálózati kapcsolatot powershell-parancsmagok használatával is engedélyezheti.

* Hozzon létre egy API Management szolgáltatást egy virtuális hálózaton belül: A [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) parancsmag használatával hozzon létre egy Azure API Management szolgáltatást egy virtuális hálózaton belül, és konfigurálja a belső virtuális hálózat típusának használatára.

* Egy API Management szolgáltatás meglévő központi telepítésének frissítése egy virtuális hálózaton belül: Az [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) parancsmag segítségével helyezzen át egy meglévő API Management szolgáltatást egy virtuális hálózaton belül, és konfigurálja a belső virtuális hálózat típusának használatára.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-konfiguráció
Ha az API Management külső virtuális hálózati módban van, a DNS-t az Azure kezeli. A belső virtuális hálózati mód, akkor kell kezelni a saját útválasztás.

> [!NOTE]
> Az API Management szolgáltatás nem figyeli az IP-címekről érkező kéréseket. Csak a szolgáltatásvégpontokon konfigurált állomásnévre vonatkozó kérésekre válaszol. Ezek a végpontok közé tartozik az átjáró, az Azure Portal és a fejlesztői portál, a közvetlen felügyeleti végpont és a Git.

### <a name="access-on-default-host-names"></a>Hozzáférés az alapértelmezett állomásnevekhez
Amikor például "contosointernalvnet" nevű API Management szolgáltatást hoz létre, alapértelmezés szerint a következő szolgáltatásvégpontok vannak konfigurálva:

   * Átjáró vagy proxy: contosointernalvnet.azure-api.net

   * A fejlesztői portál: contosointernalvnet.portal.azure-api.net

   * Az új fejlesztői portál: contosointernalvnet.developer.azure-api.net

   * Közvetlen felügyeleti végpont: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Ezekhez az API Management szolgáltatás végpontokhoz való hozzáféréshez létrehozhat egy virtuális gépet egy olyan alhálózatban, amely csatlakozik ahhoz a virtuális hálózathoz, amelyben az API Management telepítve van. Feltételezve, hogy a szolgáltatás belső virtuális IP-címe 10.1.0.5, a hosts fájlt (%SystemDrive%\drivers\etc\hosts) az alábbiak szerint képezheti le:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Ezután elérheti az összes szolgáltatás végpontok a létrehozott virtuális gép.
Ha egyéni DNS-kiszolgálót használ egy virtuális hálózatban, a virtuális hálózat bármely pontjáról létrehozhat A DNS-rekordokat, és elérheti ezeket a végpontokat.

### <a name="access-on-custom-domain-names"></a>Hozzáférés egyéni tartománynevekhez

1. Ha nem szeretné elérni az API Management szolgáltatást az alapértelmezett állomásnevekkel, egyéni tartományneveket állíthat be az összes szolgáltatásvégponthoz az alábbi képen látható módon:

   ![Egyéni tartomány beállítása az API Management számára][api-management-custom-domain-name]

2. Ezután rekordokat hozhat létre a DNS-kiszolgálón, hogy hozzáférjen a végpontok, amelyek csak a virtuális hálózaton belül érhető el.

## <a name="routing"></a><a name="routing"> </a> Útválasztás

* Az alhálózati tartományból származó terheléselosztásos *privát* virtuális IP-cím lesz fenntartva, és az API Management szolgáltatás végpontjainak elérésére szolgál a virtuális hálózaton belül. Ez a *privát* IP-cím megtalálható a szolgáltatás áttekintése panelen az Azure Portalon. Ezt a címet regisztrálni kell a virtuális hálózat által használt DNS-kiszolgálókkal.
* A terheléselosztásos *nyilvános* IP-cím (VIP) is fenntartva lesz, hogy hozzáférést biztosítson a felügyeleti szolgáltatás végpontjához a 3443-as porton keresztül. Ez a *nyilvános* IP-cím megtalálható a szolgáltatás áttekintése panelen az Azure Portalon. A *nyilvános* IP-cím csak a 3443-as porton keresztül i `management` végpontra irányuló vezérlősík-forgalomhoz használatos, és az [ApiManagement][ServiceTags] servicetag-hoz zárolható.
* Az alhálózati IP-tartományból (DIP) származó IP-címek a szolgáltatás minden virtuális gépéhez hozzá lesznek rendelve, és a virtuális hálózaton belüli erőforrások elérésére szolgálnak. A nyilvános IP-cím (VIP) a virtuális hálózaton kívüli erőforrások elérésére szolgál. Ha az IP-korlátozási listák at a virtuális hálózaton belüli erőforrások védelmére használják, meg kell adni annak az alhálózatnak a teljes tartományát, ahol az API Management szolgáltatás telepítve van, a szolgáltatáshoz való hozzáférés megadásához vagy korlátozásához.
* A terheléselosztásos nyilvános és privát IP-címek az Azure Portalon az Áttekintés panelen találhatók.
* A nyilvános és privát hozzáféréshez rendelt IP-címek változhatnak, ha a szolgáltatást eltávolítják, majd hozzáadják a virtuális hálózathoz. Ebben az esetben szükség lehet a DNS-regisztrációk, útválasztási szabályok és IP-korlátozási listák frissítésére a virtuális hálózaton belül.

## <a name="related-content"></a><a name="related-content"> </a>Kapcsolódó tartalom
További információ: a következő cikkek:
* [Gyakori hálózati konfigurációs problémák az Azure API Management virtuális hálózatban való beállítása közben][Common network configuration problems]
* [Virtuális hálózat – gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [Rekord létrehozása a DNS-ben](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

