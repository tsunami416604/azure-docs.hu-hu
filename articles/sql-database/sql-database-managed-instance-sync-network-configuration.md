---
title: Azure App Service – hálózati konfiguráció szinkronizálása
description: Ez a cikk bemutatja, hogyan szinkronizálhatja a hálózati konfigurációt az Azure App Service üzemeltetési csomag.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73687898"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Hálózati konfiguráció szinkronizálása az Azure App Service üzemeltetési csomagjához

Előfordulhat, hogy bár [integrálta az alkalmazást egy Azure virtuális hálózattal,](../app-service/web-sites-integrate-with-vnet.md)nem hozhat létre kapcsolatot a felügyelt példány. Az egyik dolog, amit megpróbálhat, hogy frissítse a hálózati konfigurációt a szolgáltatási csomaghoz.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Hálózati konfiguráció szinkronizálása az App Service üzemeltetési csomagjához

Ehhez kövesse az alábbi lépéseket:  

1. Nyissa meg a webalkalmazások App Service-csomagját.

   ![alkalmazásszolgáltatási csomag](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kattintson a Hálózat, majd **a Kattintásidea kezelés** **gombra.**

   ![szolgáltatási csomag kezelése](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Jelölje ki a **virtuális hálózatot,** és kattintson **a Hálózat szinkronizálása gombra.**

   ![hálózat szinkronizálása](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Várjon, amíg a szinkronizálás befejeződik.
  
   ![szinkronizálás kész](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Most már készen áll arra, hogy megpróbálja helyreállítani a kapcsolatot a felügyelt példánysal.

## <a name="next-steps"></a>További lépések

- A virtuális hálózat felügyelt példányhoz való konfigurálásáról a [Felügyelt példány virtuális hálózat architektúrája](sql-database-managed-instance-connectivity-architecture.md) és a meglévő virtuális hálózat [konfigurálása című](sql-database-managed-instance-configure-vnet-subnet.md)témakörben talál további információt.
