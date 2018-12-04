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
ms.openlocfilehash: 99ec559429d66becc20e038e43349f5369afac39
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855958"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Az Azure SQL Database felügyelt példányain felügyeleti végpont felderítése 

## <a name="overview"></a>Áttekintés
Az Azure SQL felügyelt példánya [virtuális fürt](sql-database-managed-instance-connectivity-architecture.md) tartalmazza a felügyeleti végponthoz, amely a Microsoft használ a példány kezeléséhez.  

Felügyeleti végpont hálózati szintű és kölcsönös tanúsítvány-ellenőrzés az alkalmazás szintjén beépített tűzfal védi. 

Amikor kapcsolatokat a felügyelt példányon belül kezdeményezett (CLR-beli, a csatolt kiszolgáló, a biztonsági mentés, naplójának stb.) úgy tűnik, hogy a forgalom származik az felügyeleti végpont nyilvános IP-címét. A felügyelt példány nyilvános szolgáltatásokhoz való hozzáférés sikerült korlátozza tűzfalszabályokat, hogy csak az IP-cím engedélyezése beállítást.

> [!NOTE]
> Ez nem vonatkozik a tűzfalszabályok az Azure-szolgáltatásokhoz, amelyek a felügyelt példány ugyanabban a régióban, mert a platform-optimalizálást a szolgáltatásokat, amelyek közös elhelyezésű közötti forgalmat a beállítást. 

Ez a cikk bemutatja, hogyan sikerült beolvasni a felügyeleti végpont nyilvános IP-címét és a beépített tűzfal védelmének ellenőrzése.

## <a name="finding-management-endpoint-public-ip-address"></a>Felügyeleti végpont nyilvános IP-cím megkeresése
Tegyük fel, hogy MI állomás _mi-demo.xxxxxx.database.windows.net_. Futtatás _nslookup_ név használatával.

![Belső állomásnév feloldása](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Most már egy másik tegye _nslookup_ removed_.vnet._segment a kiemelt neveként. Ez a parancs végrehajtása eredményeként nyilvános IP-cím jelenik meg.

![Nyilvános IP-cím feloldása](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-managed-instance-built-in-firewall"></a>Felügyelt példány beépített tűzfal ellenőrzése
Felügyelt példány [kötelező bejövő biztonsági szabályok](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) igényelnek a felügyeleti portokat 9000, 9003, 1438, 1440, 1452 kell nyissa meg a hálózati biztonsági csoport, amely védelmet nyújt a felügyelt példány bármilyen forrásból. Bár az ezeket a portokat nyissa meg az NSG-szinten, a védelmükhöz hálózati szinten beépített tűzfal.

Annak ellenőrzéséhez, ezeket a portokat, minden olyan biztonsági ellenőrzőeszköz eszköz segítségével tesztelje ezeket a portokat. A következő képernyőképen látható, hogyan használhatja az eszközöket.

![Beépített tűzfal ellenőrzése](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)
