---
title: Fedezze fel az Azure SQL Database felügyelt példányába felügyeleti végpontja |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database felügyelt példányába felügyeleti végpont nyilvános IP-címét, és a beépített tűzfal általi védelem ellenőrzése'
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 542db0200229b4fea4ac67ad19c468f2b9dc67d1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772779"
---
# <a name="determine-the-management-endpoint-ip-address"></a>A felügyeleti végpont IP-cím meghatározása

Az Azure SQL Database felügyelt példánya virtuális fürt tartalmaz egy felügyeleti végpontot, a Microsoft által a felügyeleti műveleteket. A hálózati szintű és kölcsönös tanúsítvány-ellenőrzés az alkalmazás szintjén a beépített tűzfal védi a felügyeleti végponthoz. Megadhatja, hogy a felügyeleti végpont IP-címe, de nem érheti el ezt a végpontot.

## <a name="determine-ip-address"></a>IP-cím meghatározása

Tegyük fel, hogy felügyelt példány állomás `mi-demo.xxxxxx.database.windows.net`. Futtatás `nslookup` név használatával.

![Belső állomásnév feloldása](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Most már egy másik tegye `nslookup` kiemelt neve eltávolítását a `.vnet.` szegmens. A nyilvános IP-címet kap, ha a parancs végrehajtása.

![Nyilvános IP-cím feloldása](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>További lépések

További információ a felügyelt példányok és a kapcsolat: [Azure SQL Database felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
