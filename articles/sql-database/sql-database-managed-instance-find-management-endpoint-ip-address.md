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
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359768"
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
