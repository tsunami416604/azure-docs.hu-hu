---
title: Egyéni DNS
titleSuffix: Azure SQL Managed Instance
description: Ez a témakör az Azure SQL felügyelt példányaival rendelkező egyéni DNS konfigurációs beállításait ismerteti.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: 017698975706aa8501cd059351cf9a9d88594f77
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779900"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Egyéni DNS konfigurálása az Azure SQL felügyelt példányaihoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton (VNet)](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Egyes esetekben (például db mail – más SQL Server-példányokhoz társított kiszolgálók a felhőben vagy hibrid környezetben), a felügyelt SQL-példányról kell feloldani a privát gazdagépneveket. Ebben az esetben konfigurálni kell egy egyéni DNS-t az Azure-ban. 

Mivel az SQL felügyelt példánya ugyanazt a DNS-t használja belső működéséhez, konfigurálja az egyéni DNS-kiszolgálót úgy, hogy az képes legyen a nyilvános tartománynevek feloldására.

> [!IMPORTANT]
> Mindig teljes tartománynevet (FQDN) használjon a levelezési kiszolgálóhoz, a SQL Server példányhoz és más szolgáltatásokhoz, még akkor is, ha azok a saját DNS-zónán belül vannak. Használhatja például a `smtp.contoso.com` levelezési kiszolgálóját, mert `smtp` nem oldja meg megfelelően a megoldást. Az ugyanazon a virtuális hálózaton belül SQL Server virtuális gépekre hivatkozó csatolt kiszolgáló vagy replikáció létrehozásakor a teljes tartománynevet és az alapértelmezett DNS-utótagot is meg kell adni. Például: `SQLVM.internal.cloudapp.net`. További információ: névfeloldás, [amely a saját DNS-kiszolgálóját használja](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A virtuális hálózati DNS-kiszolgálók frissítése nem befolyásolja azonnal az SQL felügyelt példányát. A felügyelt SQL-példány DNS-konfigurációja a DHCP-bérlet lejárta vagy a platform verziófrissítése után frissül, attól függően, hogy melyik történik először. **A felhasználóknak javasoljuk, hogy az első felügyelt példány létrehozása előtt állítsa be a virtuális hálózat DNS-konfigurációját.**

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Az új felügyelt példányok létrehozásával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A felügyelt példányok VNet konfigurálásával kapcsolatos információkért lásd: [VNet-konfiguráció a felügyelt](connectivity-architecture-overview.md)példányokhoz.