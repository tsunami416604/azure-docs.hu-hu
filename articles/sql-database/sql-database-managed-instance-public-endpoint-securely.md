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
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014023"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Azure SQL Database felügyelt példány nyilvános végponttal rendelkező biztonságos

Az Azure SQL Database felügyelt példány sikerült engedélyezni a felhasználói kapcsolatot biztosít keresztül [nyilvános végpontot](../virtual-network/virtual-network-service-endpoints-overview.md). Ez a cikk útmutatást nyújt hogyan még biztonságosabbá teheti ezt a konfigurációt.

## <a name="scenarios"></a>Forgatókönyvek

Felügyelt példány a kapcsolat a virtuális hálózatban lévő titkos végpontot biztosít. Az alapértelmezett beállítás maximális elkülönítéshez. Előfordulhatnak azonban olyan forgatókönyvek, ahol egy nyilvános végpontot kapcsolat van szükség:

- Több-bérlős csak PaaS-ajánlatok integrációja.
- Nagyobb átviteli sebességet adatcsere, mint a VPN használatával.
- Céges szabályzatok tiltják a PaaS vállalati hálózatokon belül.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>A nyilvános végpont hozzáféréshez felügyelt példány üzembe helyezése

Bár nem kötelező, a közös üzemi modell egy nyilvános végpontot hozzáféréssel rendelkező felügyelt példány az a példány létrehozása egy dedikált elkülönített virtuális hálózaton belül. Ebben a konfigurációban a virtuális hálózat csak a virtuális fürt elkülönítési szolgál. Esetén nem releváns, ha a felügyelt példány IP-címtér átfedésben van egy vállalati hálózat IP-címtér.

## <a name="securing-data-in-motion"></a>A mozgásban lévő adatok védelme

Felügyelt példány az adatforgalmat a rendszer mindig titkosítja, ha az ügyfél-illesztőprogram támogatja a titkosítást. A következő felügyelt példányt, és egyéb Azure Virtual Machines vagy Azure-szolgáltatások közötti sosem hagyja el az Azure gerinchálózatán keresztül. Ha egy felügyelt példány egy a helyszíni hálózati kapcsolat, ajánlott az Express Route használata a Microsoft társviszony-létesítés. Express Route segít, elkerülheti az adatok áthelyezését a nyilvános interneten keresztül (a felügyelt példány privát hálózati kapcsolatot, csak magánhálózati társviszony-létesítés használható).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Bejövő és kimenő kapcsolat sémákra

Az alábbi ábrán látható, ajánlott biztonsági konfigurációkat.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Felügyelt példány egy [dedikált nyilvános végpont címe](sql-database-managed-instance-find-management-endpoint-ip-address.md). Az IP-címet kell beállítani az ügyfél oldalán kimenő tűzfal és a hálózati biztonsági csoportszabályok kimenő kapcsolatok korlátozásához.

Annak érdekében, hogy a felügyelt példány forgalmát megbízható forrásból származik, azt javasoljuk, hogy csatlakozzon a jól ismert IP-címek olyan forrásból. A port 3342 a felügyelt példány nyilvános végponthoz való hozzáférést korlátozza a hálózati biztonsági csoport használatával.

Ha az ügyfél használatával kezdeményeznek kapcsolatot egy helyszíni hálózatról, ellenőrizze, hogy az eredeti címét egy jól ismert IP-címek készletét fordítja le. Ha, amely nem érhető el (például a mobil munkaerő igényeire szokás folyamatban), azt javasoljuk, hogy használjon [pont – hely VPN kapcsolatok és a egy privát végpontjaikat](sql-database-managed-instance-configure-p2s.md).

Ha kapcsolatok az Azure-ból elindulnak, javasoljuk, hogy forgalom származik, jól ismert hozzárendelt [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (például virtuális gépek). Könnyű VIP-címek kezelése ügyfeleknek érdemes megfontolni használatával [nyilvános IP-címelőtag](../virtual-network/public-ip-address-prefix.md).