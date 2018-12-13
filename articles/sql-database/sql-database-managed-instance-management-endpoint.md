---
title: Fedezze fel az Azure SQL Database felügyelt példányába felügyeleti végpontja |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database felügyelt példányába felügyeleti végpont nyilvános IP-címét, és a beépített tűzfal általi védelem ellenőrzése'
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 45ddf1c75dd22f5074c2017185bc0ed3be0b2a80
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872696"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Az Azure SQL Database felügyelt példányain felügyeleti végpont felderítése

Az Azure SQL Database felügyelt példányába [virtuális fürt](sql-database-managed-instance-connectivity-architecture.md) , amely a felügyelt példány kezeléséhez használja a Microsoft felügyeleti végpontot tartalmaz. A hálózati szintű és kölcsönös tanúsítvány-ellenőrzés az alkalmazás szintjén beépített tűzfal védi a felügyeleti végponthoz.

Kapcsolat kezdeményezése a felügyelt példány (a biztonsági mentés, a napló) belül jelenik meg, hogy a forgalom származik az felügyeleti végpont nyilvános IP-címét. A felügyelt példány nyilvános szolgáltatásokhoz való hozzáférés sikerült korlátozza tűzfalszabályokat, hogy csak a felügyelt példány IP-cím engedélyezése beállítást.

> [!NOTE]
> Ez nem vonatkozik a tűzfalszabályok az Azure-szolgáltatásokhoz, amelyek a felügyelt példány ugyanabban a régióban, mivel az Azure-platform-optimalizálást a szolgáltatásokat, amelyek közös elhelyezésű közötti forgalmat a beállítást.

Ez a cikk bemutatja, hogyan sikerült beolvasni a felügyeleti végpont nyilvános IP-címét és a beépített tűzfal védelmének ellenőrzése.

## <a name="finding-the-management-endpoint-public-ip-address"></a>A felügyeleti végponthoz nyilvános IP-cím megkeresése

Tegyük fel, hogy felügyelt példány állomás `mi-demo.xxxxxx.database.windows.net`. Futtatás `nslookup` név használatával.

![Belső állomásnév feloldása](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Most már egy másik tegye `nslookup` kiemelt neve eltávolítását a `.vnet.` szegmens. Ez a parancs végrehajtása eredményeként nyilvános IP-cím jelenik meg.

![Nyilvános IP-cím feloldása](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-the-managed-instance-built-in-firewall"></a>A felügyelt példány beépített tűzfal ellenőrzése

A felügyelt példány [kötelező bejövő biztonsági szabályok](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) igényelnek a felügyeleti portokat 9000, 9003, 1438, 1440, nyissa meg a kívánt 1452 **bármilyen forrásból** lévő a hálózati biztonsági csoport (NSG), amely védelmet nyújt a felügyelt A példány. Bár az ezeket a portokat nyissa meg az NSG-t szintjén, védi azokat a hálózati szinten a beépített tűzfal.

Ezeket a portokat, eszközzel ellenőrizheti bármilyen biztonsági ellenőrzőeszköz teszteléséhez ezeket a portokat. A következő képernyőképen látható, hogyan használhatja az eszközöket.

![Beépített tűzfal ellenőrzése](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

További információ a felügyelt példányok és a kapcsolat: [Azure SQL Database felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).