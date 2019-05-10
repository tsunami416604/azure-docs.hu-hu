---
title: Biztonságos felügyelt példány nyilvános végpontok – Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Biztonságosan használata a nyilvános végpontokat az Azure-ban felügyelt példány
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470306"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Egy Azure SQL Database felügyelt példány nyilvános végpontok biztonságosan használata

Az Azure SQL Database felügyelt példány keresztül tud biztosítani felhasználói kapcsolat [nyilvános végpontok](../virtual-network/virtual-network-service-endpoints-overview.md). Ez a cikk ismerteti, hogyan teheti biztonságosabbá ezt a konfigurációt.

## <a name="scenarios"></a>Alkalmazási helyzetek

Egy SQL Database felügyelt példány biztosít, amely engedélyezi a csatlakozást a saját virtuális hálózaton belül egy privát végpontjaikat. Az alapértelmezett beállítás maximális elkülönítéshez. Előfordulhatnak azonban olyan forgatókönyvek, ahol meg kell adnia egy nyilvános végpontot kapcsolatot:

- A következő felügyelt példányt a több-tenant csak platform--szolgáltatásként (PaaS) ajánlatok kell integrálni.
- Szüksége lesz az adatcsere során használ a VPN lehet, nagyobb átviteli sebességet.
- Céges szabályzatok tiltják a PaaS vállalati hálózatokon belül.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>A nyilvános végponthoz hozzáféréshez egy felügyelt példány üzembe helyezése

Bár nem kötelező, a közös üzemi modell egy nyilvános végpontot hozzáféréssel rendelkező felügyelt példány az a példány létrehozása egy dedikált elkülönített virtuális hálózaton belül. Ebben a konfigurációban a virtuális hálózat kizárólag használatos virtuális fürt elkülönítés. Nem számít, hogy a felügyelt példány IP-címtartomány átfedésben van egy vállalati hálózat IP-címtér.

## <a name="secure-data-in-motion"></a>A mozgásban lévő adatok védelme

Felügyelt példány az adatforgalmat a rendszer mindig titkosítja, ha az ügyfél-illesztőprogram támogatja a titkosítást. A következő felügyelt példányt és egyéb Azure-beli virtuális gépek vagy az Azure-szolgáltatások közötti adatforgalom sosem hagyja el az Azure gerinchálózatán keresztül. Ha a felügyelt példány és a egy helyszíni hálózat közötti kapcsolat, azt javasoljuk, Azure ExpressRoute használata a Microsoft társviszony-létesítés. Az ExpressRoute segít elkerülni a adatok áthelyezése a nyilvános interneten keresztül. A felügyelt példány privát hálózati kapcsolatot csak magánhálózati társviszony-létesítés is használható.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bejövő és kimenő kapcsolat zárolását

Az alábbi ábrán látható, az ajánlott biztonsági konfigurációk:

![A bejövő és kimenő kapcsolat sémákra biztonsági konfigurációk](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

A felügyelt példányokra egy [dedikált nyilvános végpont címe](sql-database-managed-instance-find-management-endpoint-ip-address.md). Az ügyféloldali kimenő tűzfal és a hálózati biztonsági csoport szabályait állítsa be a nyilvános végpont IP-címet a kimenő kapcsolatok korlátozásához.

Annak érdekében, hogy a felügyelt példány forgalmát megbízható forrásból származik, ajánlott források jól ismert IP-címmel csatlakozik. A hálózati biztonsági csoport használatával hozzáférést csak a felügyelt példány nyilvános végpontot a portra 3342.

Ha az ügyfél használatával kezdeményeznek kapcsolatot egy helyszíni hálózatról, ellenőrizze, hogy az eredeti címét egy jól ismert IP-címek készletét fordítja le. Ha nem hajtható végre, így (például a mobil munkaerő igényeire szokás folyamatban), azt javasoljuk, használjon [pont – hely VPN-kapcsolatok és a egy privát végpontjaikat](sql-database-managed-instance-configure-p2s.md).

Ha kapcsolatok vannak lépései az Azure-ból, azt javasoljuk, hogy forgalmat származnia egy jól ismert hozzárendelt [virtuális IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md) (például egy virtuális gép). Ahhoz, hogy a virtuális IP-cím (VIP) címek egyszerűbb felügyelete, előfordulhat, hogy használni kívánt [nyilvános IP-címelőtagokat](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan konfigurálhatja a kezelés példányok nyilvános végpontot: [Konfigurálja a nyilvános végponthoz](sql-database-managed-instance-public-endpoint-configure.md)
