---
title: Privát végpontok használata az Azure app Configuration szolgáltatáshoz
description: Az alkalmazás konfigurációs tárolójának védelme privát végpontok használatával
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366768"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Privát végpontok használata az Azure app Configuration szolgáltatáshoz

Az Azure-alkalmazás konfigurálásához [privát végpontokat](../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik, hogy a virtuális hálózat (VNet) ügyfelei biztonságosan hozzáférjenek az [adatkapcsolatokhoz](../private-link/private-link-overview.md). A privát végpont IP-címet használ az alkalmazás konfigurációs tárolójának VNet. A VNet lévő ügyfelek és az alkalmazás konfigurációs tárolója közötti hálózati forgalom a VNet keresztül halad át a Microsoft gerinc hálózatán lévő privát kapcsolaton keresztül, ami kiküszöböli a nyilvános internetre való kitettséget.

Az alkalmazás konfigurációs tárolójához saját végpontok használata lehetővé teszi a következőket:
- Gondoskodjon az alkalmazás konfigurációjának részleteiről, ha úgy konfigurálja a tűzfalat, hogy a nyilvános végponton az alkalmazás konfigurációjának minden kapcsolatát letiltsa.
- A virtuális hálózat (VNet) biztonságának növelésével biztosítható, hogy az adatok ne legyenek a VNet.
- Biztonságosan csatlakozhat az alkalmazás-konfigurációs tárolóhoz a helyszíni hálózatokról, amelyek a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) -en vagy a [expressroute](../expressroute/expressroute-locations.md) -en keresztül csatlakoznak a VNet.

> [!NOTE]
> Az Azure-alkalmazás konfigurációja nyilvános előzetes verzióként kínálja a privát végpontok használatát. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot az alkalmazás konfigurációs tárolójához, biztonságos kapcsolatot biztosít a VNet és a konfigurációs tároló ügyfelei között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a konfigurációs tároló közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások a privát végponton keresztül csatlakozhatnak a konfigurációs tárolóhoz **ugyanazon kapcsolati karakterláncok és engedélyezési mechanizmusok használatával, amelyeket egyébként használni**fognak. A magánhálózati végpontok az alkalmazás-konfigurációs tároló által támogatott összes protokollal használhatók.

Noha az alkalmazás konfigurációja nem támogatja a szolgáltatási végpontokat, a magánhálózati végpontok olyan alhálózatokban hozhatók létre, amelyek [szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)használnak. Az alhálózatban lévő ügyfelek biztonságosan kapcsolódhatnak az alkalmazás konfigurációs tárolójához a privát végpont használatával, miközben a szolgáltatás-végpontokat használják mások eléréséhez.  

Amikor saját VNet hoz létre egy szolgáltatáshoz egy magánhálózati végpontot, a rendszer jóváhagyásra vonatkozó kérést küld a szolgáltatásfiók tulajdonosának. Ha a privát végpont létrehozását kérő felhasználó a fiók tulajdonosa is, a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A szolgáltatásfiók tulajdonosai a [Azure Portal](https://portal.azure.com)konfigurációs tárolójának `Private Endpoints` lapján kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

### <a name="private-endpoints-for-app-configuration"></a>Saját végpontok az alkalmazás konfigurálásához 

Privát végpont létrehozásakor meg kell adnia azt az alkalmazás-konfigurációs tárolót, amelyhez csatlakozik. Ha egy fiókban több alkalmazás-konfigurációs példány is található, minden egyes tárolóhoz külön privát végpontra van szükség.

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

Az Azure DNS-feloldásra támaszkodik, hogy a VNet és a konfigurációs tároló közötti kapcsolatokat egy privát kapcsolaton keresztül irányítsa. A Azure Portal található kapcsolati karakterláncok gyorsan megtalálhatók az alkalmazás konfigurációs tárolójának kiválasztásával, majd a **beállítások** > **hozzáférési kulcsok lehetőség**kiválasztásával.  

> [!IMPORTANT]
> Ugyanazzal a kapcsolati karakterlánccal csatlakozhat az alkalmazás konfigurációs tárolójához privát végpontok használatával, ahogyan azt egy nyilvános végponthoz kívánja használni. Ne kapcsolódjon a Storage-fiókhoz a `privatelink` altartományának URL-címével.

## <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Amikor létrehoz egy privát végpontot, a konfigurációs tárolóhoz tartozó DNS CNAME-erőforrásrekord a `privatelink`előtaggal rendelkező altartományban található aliasra frissül. Az Azure emellett létrehoz egy [saját DNS-zónát](../dns/private-dns-overview.md) is, amely a `privatelink` altartományhoz tartozik, és a DNS a saját végpontokhoz tartozó erőforrásrekordokat.

Ha a végponti URL-címet a VNet kívülről oldja fel, az a tároló nyilvános végpontját oldja fel. Ha a privát végpontot futtató VNet megoldódik, a végpont URL-címe feloldódik a magánhálózati végpontra.

A VNet kívüli ügyfelek hozzáférését a Azure Firewall szolgáltatás használatával szabályozhatja a nyilvános végponton keresztül.

Ez a megközelítés lehetővé teszi, hogy az áruházhoz **ugyanazt a kapcsolati karakterláncot használja** , mint a privát végpontokat üzemeltető VNet és a VNet kívüli ügyfelek számára.

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek képesnek kell lenniük a szolgáltatási végpont teljes tartománynevének (FQDN) feloldására a magánhálózati végpont IP-címére. Konfigurálja a DNS-kiszolgálót úgy, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a `AppConfigInstanceA.privatelink.azconfig.io` a magánhálózati végpont IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használatakor a DNS-kiszolgálót úgy kell konfigurálni, hogy az `privatelink` altartományban lévő tároló nevét a magánhálózati végpont IP-címére oldja fel. Ezt úgy teheti meg, hogy delegálja a `privatelink` altartományt a VNet magánhálózati DNS-zónájához, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja a DNS-rekordot.

## <a name="pricing"></a>Díjszabás

A privát végpontok engedélyezéséhez [standard szintű](https://azure.microsoft.com/pricing/details/app-configuration/) alkalmazás-konfigurációs tárolóra van szükség.  A privát hivatkozások díjszabásával kapcsolatos további információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Következő lépések

További információ az alkalmazás-konfigurációs tároló privát végpontjának létrehozásáról:

- [Privát végpont létrehozása a Azure Portal privát kapcsolati központjának használatával](../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI-vel](../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../private-link/create-private-endpoint-powershell.md)

Ismerje meg, hogyan konfigurálhatja a DNS-kiszolgálót magánhálózati végpontokkal:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](/azure/private-link/private-endpoint-overview#dns-configuration)
