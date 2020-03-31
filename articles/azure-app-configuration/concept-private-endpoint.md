---
title: Privát végpontok használata az Azure App konfigurációjához
description: Az alkalmazáskonfigurációs áruház biztonságossá tétele privát végpontokkal
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366768"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Privát végpontok használata az Azure App konfigurációjához

Az Azure App Configuration [privát végpontjainak](../private-link/private-endpoint-overview.md) használatával engedélyezheti a virtuális hálózaton (VNet) lévő ügyfelek számára az adatok biztonságos elérését egy [privát kapcsolaton](../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ az alkalmazáskonfigurációs tároló virtuális hálózat címterületéről. A virtuális hálózaton lévő ügyfelek és az alkalmazáskonfigurációs tároló közötti hálózati forgalom a Virtuális hálózaton keresztül halad át a Microsoft gerinchálózatának privát kapcsolatán keresztül, így kiküszöbölve a nyilvános internetnek való kitettséget.

Az alkalmazáskonfigurációs tároló privát végpontjainak használata lehetővé teszi a következőket:
- Az alkalmazás konfigurációs adatait úgy védje meg, hogy a tűzfal letiltja az alkalmazáskonfigurációval létesített összes kapcsolatot a nyilvános végponton.
- Növelje a virtuális hálózat (VNet) biztonságát, biztosítva, hogy az adatok ne szökjenek meg a virtuális hálózatból.
- Biztonságosan csatlakozhat az App Configuration Store-hoz a helyszíni hálózatokról, amelyek [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoutes](../expressroute/expressroute-locations.md) használatával csatlakoznak a virtuális hálózathoz, privát társviszony-létesítéssel.

> [!NOTE]
> Az Azure App Configuration a privát végpontok nyilvános előzetes verzióként való használatát kínálja. A nyilvános előzetes verziólehetővé teszi az ügyfelek számára, hogy a hivatalos kiadás előtt kísérletezzenek az új funkciókkal.  A nyilvános előzetes verziójú funkciók és szolgáltatások nem éles környezetben való használatra valók.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpont egy speciális hálózati felület egy Azure-szolgáltatás a [virtuális hálózat](../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot az App Config Store számára, biztonságos kapcsolatot biztosít a virtuális hálózaton lévő ügyfelek és a konfigurációs tároló között. A privát végpont hoz egy IP-címet a virtuális hálózat IP-címtartományából. A privát végpont és a konfigurációs tároló közötti kapcsolat biztonságos privát kapcsolatot használ.

A virtuális hálózatban lévő alkalmazások a saját végponton keresztül csatlakozhatnak a konfigurációs tárolóhoz **ugyanazokat a kapcsolati karakterláncokat és engedélyezési mechanizmusokat használva, amelyeket egyébként használnának.** A privát végpontok az alkalmazáskonfigurációs tároló által támogatott összes protokollhoz használhatók.

Bár az alkalmazáskonfiguráció nem támogatja a szolgáltatásvégpontokat, privát végpontok hozhatók létre a [szolgáltatásvégpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)használó alhálózatokban. Az alhálózatban lévő ügyfelek biztonságosan csatlakozhatnak egy alkalmazáskonfigurációs tárolóhoz a privát végpont használatával, miközben szolgáltatásvégpontokat használnak mások eléréséhez.  

Amikor létrehoz egy privát végpontot egy szolgáltatáshoz a virtuális hálózatban, a rendszer jóváhagyásra jóváhagyást küld jóváhagyásra a szolgáltatásfiók tulajdonosának. Ha a privát végpont létrehozását kérő felhasználó egyben a fiók tulajdonosa is, a hozzájárulási kérelem automatikusan jóváhagyásra kerül.

A szolgáltatásfiók-tulajdonosok az Azure `Private Endpoints` [Portalon](https://portal.azure.com)található konfigurációs tároló fülén keresztül kezelhetik a hozzájárulási kérelmeket és a privát végpontokat.

### <a name="private-endpoints-for-app-configuration"></a>Privát végpontok az alkalmazáskonfigurációhoz 

Privát végpont létrehozásakor meg kell adnia azt az alkalmazáskonfigurációs tárolót, amelyhez csatlakozik. Ha egy fiókon belül több alkalmazáskonfigurációs példány is található, minden egyes üzlethez külön privát végpontra van szükség.

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

Az Azure a DNS-feloldásra támaszkodva továbbítja a kapcsolatot a virtuális hálózatról a konfigurációs tárolóba egy privát kapcsolaton keresztül. Az Azure Portalon gyorsan megtalálhatja a kapcsolati karakterláncokat, ha kiválasztja az alkalmazáskonfigurációs áruházat, majd a **Beállítások** > **hozzáférési kulcsok lehetőséget.**  

> [!IMPORTANT]
> Használja ugyanazt a kapcsolati karakterláncot az alkalmazáskonfigurációs tárolóhoz való csatlakozáshoz a nyilvános végpontokhoz használt privát végpontok használatával. Ne csatlakozzon a tárfiókhoz `privatelink` az altartomány URL-címével.

## <a name="dns-changes-for-private-endpoints"></a>DNS-módosítások a magánvégpontokhoz

Privát végpont létrehozásakor a konfigurációs tároló DNS CNAME erőforrásrekordja egy altartományban lévő `privatelink`aliasra frissül. Az Azure is létrehoz egy `privatelink` [privát DNS-zóna](../dns/private-dns-overview.md) az altartománynak megfelelő, a DNS A erőforrásrekordok a magán-végpontok.

Amikor a virtuális hálózaton kívülről oldja fel a végpont URL-címét, az a tároló nyilvános végpontjára oldódik fel. Ha a privát végpontot üzemeltető virtuális hálózatból oldódik fel, a végpont URL-címe a privát végpontra oldódik fel.

A virtuális hálózaton kívüli ügyfelek hozzáférését az Azure Firewall szolgáltatás használatával szabályozhatja a nyilvános végponton keresztül.

Ez a megközelítés lehetővé teszi a hozzáférést az **üzlethez ugyanazt a kapcsolati karakterláncot a** privát végpontokat üzemeltető virtuális hálózaton lévő ügyfelek, valamint a virtuális hálózaton kívüli ügyfelek számára.

Ha egyéni DNS-kiszolgálót használ a hálózaton, az ügyfeleknek képesnek kell lenniük a szolgáltatásvégpont teljesen minősített tartománynevét (FQDN) feloldani a privát végpont IP-címére. Konfigurálja úgy a DNS-kiszolgálót, hogy delegálja a privát hivatkozás altartományát a virtuális hálózat privát DNS-zónájának, vagy konfigurálja az A rekordokat a privát végpont `AppConfigInstanceA.privatelink.azconfig.io` IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használataesetén konfigurálja úgy a DNS-kiszolgálót, `privatelink` hogy az altartományban lévő tárolónevet a privát végpont IP-címére oldja fel. Ezt úgy teheti meg, hogy `privatelink` az altartományt delegálja a virtuális hálózat privát DNS-zónájába, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja az A DNS-rekordokat.

## <a name="pricing"></a>Díjszabás

A privát végpontok engedélyezéséhez [standard szintű](https://azure.microsoft.com/pricing/details/app-configuration/) alkalmazáskonfigurációs tárolóra van szükség.  Ha többet szeretne megtudni a privát hivatkozásdíjszabásrészleteiről, olvassa el az [Azure Private Link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>További lépések

További információ arról, hogy miként hozhat létre privát végpontot az alkalmazáskonfigurációs tárolóhoz, olvassa el az alábbi cikkeket:

- [Privát végpont létrehozása az Azure Portal privát kapcsolati központjával](../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI használatával](../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása az Azure PowerShell használatával](../private-link/create-private-endpoint-powershell.md)

Ismerje meg a DNS-kiszolgáló konfigurálását privát végpontokkal:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [A MAGÁNVÉGPONTOK DNS-konfigurációja](/azure/private-link/private-endpoint-overview#dns-configuration)
