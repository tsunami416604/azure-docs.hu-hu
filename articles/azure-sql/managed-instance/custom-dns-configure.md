---
title: Egyéni DNS
titleSuffix: Azure SQL Managed Instance
description: Ez a témakör az Azure SQL felügyelt példányaival rendelkező egyéni DNS konfigurációs beállításait ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 6a6cca1228c9891c023ce96532dc89b2f527e9a3
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655528"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Egyéni DNS konfigurálása az Azure SQL felügyelt példányaihoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton (VNet)](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Van néhány forgatókönyv (például db posta, csatolt kiszolgálók a Felhőbeli vagy hibrid környezet más SQL Server példányaihoz), amelyekhez privát állomásnevek szükségesek az SQL felügyelt példányának feloldásához. Ebben az esetben egyéni DNS-t kell konfigurálnia az Azure-on belül. 

Mivel az SQL felügyelt példánya ugyanazt a DNS-t használja belső működéséhez, konfigurálja az egyéni DNS-kiszolgálót úgy, hogy az képes legyen a nyilvános tartománynevek feloldására.

> [!IMPORTANT]
> Mindig teljes tartománynevet (FQDN) használjon a levelezési kiszolgálóhoz, a SQL Server példányhoz és más szolgáltatásokhoz, még akkor is, ha azok a saját DNS-zónán belül vannak. Használhatja például a `smtp.contoso.com` levelezési kiszolgálóját, mert `smtp` nem oldja meg megfelelően a megoldást. Az ugyanazon a virtuális hálózaton belül SQL Server virtuális gépekre hivatkozó csatolt kiszolgáló vagy replikáció létrehozásakor a teljes tartománynevet és az alapértelmezett DNS-utótagot is meg kell adni. Például: `SQLVM.internal.cloudapp.net`. További információ: névfeloldás, [amely a saját DNS-kiszolgálóját használja](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A virtuális hálózati DNS-kiszolgálók frissítése nem befolyásolja azonnal az SQL felügyelt példányát. A felügyelt SQL-példány DNS-konfigurációja a DHCP-bérlet lejárta vagy a platform verziófrissítése után frissül, attól függően, hogy melyik történik először. **A felhasználóknak javasoljuk, hogy az első felügyelt példány létrehozása előtt állítsa be a virtuális hálózat DNS-konfigurációját.**

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Az új felügyelt példányok létrehozásával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A felügyelt példányok VNet konfigurálásával kapcsolatos információkért lásd: [VNet-konfiguráció a felügyelt](connectivity-architecture-overview.md)példányokhoz.
