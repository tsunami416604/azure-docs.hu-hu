---
title: Azure App Service – hálózati konfiguráció szinkronizálása
description: Ez a cikk azt ismerteti, hogyan lehet szinkronizálni a hálózati konfigurációt Azure App Service üzemeltetési tervhez.
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687898"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Azure App Service üzemeltetési csomag hálózati konfigurációjának szinkronizálása

Előfordulhat, hogy bár az [alkalmazást Azure-Virtual Networkrel integrálta](../app-service/web-sites-integrate-with-vnet.md), nem tud kapcsolatot létesíteni a felügyelt példányokkal. Az egyik lehetőség, hogy megpróbáljuk frissíteni a szolgáltatási csomag hálózati konfigurációját.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>App Service üzemeltetési csomag hálózati konfigurációjának szinkronizálása

Ehhez kövesse az alábbi lépéseket:  

1. Nyissa meg a Web Apps App Service csomagot.

   ![App Service-csomag](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kattintson a **hálózatkezelés** elemre, majd **a kezeléshez kattintson ide**.

   ![szolgáltatási csomag kezelése](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Válassza ki a **VNet** , és kattintson a **hálózat szinkronizálása**elemre.

   ![hálózat szinkronizálása](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Várjon, amíg a szinkronizálás elkészült.
  
   ![szinkronizálás kész](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Most már készen áll arra, hogy megpróbálja újra létrehozni a kapcsolatot a felügyelt példánnyal.

## <a name="next-steps"></a>További lépések

- A felügyelt példányok VNet konfigurálásával kapcsolatos információkért lásd: a [felügyelt példány VNet architektúrája](sql-database-managed-instance-connectivity-architecture.md) és [a meglévő VNet konfigurálása](sql-database-managed-instance-configure-vnet-subnet.md).
