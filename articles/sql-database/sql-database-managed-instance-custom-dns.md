---
title: Felügyelt példány – egyéni DNS
description: Ez a témakör egy Azure SQL Database felügyelt példánnyal rendelkező egyéni DNS konfigurációs beállításait ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 1b27d316114b84434a84fc8226eba9328a803dc8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774381"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Egyéni DNS konfigurálása egy felügyelt Azure SQL Database-példányhoz

Azure SQL Database felügyelt példányt egy Azure [-beli virtuális hálózaton (VNet)](../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Van néhány forgatókönyv (például db-posta, csatolt kiszolgálók a Felhőbeli vagy hibrid környezet más SQL-példányaihoz), amelyekhez privát állomásnevek szükségesek a felügyelt példányból. Ebben az esetben egyéni DNS-t kell konfigurálnia az Azure-on belül. 

Mivel a felügyelt példány ugyanazt a DNS-t használja belső működéséhez, konfigurálja az egyéni DNS-kiszolgálót úgy, hogy az képes legyen a nyilvános tartománynevek feloldására.

> [!IMPORTANT]
> Mindig teljes tartománynevet (FQDN) használjon a levelezési kiszolgálóhoz, a SQL Server példányhoz és más szolgáltatásokhoz, még akkor is, ha azok a saját DNS-zónán belül vannak. Használhatja például a `smtp.contoso.com` levelezési kiszolgálóját, mert `smtp` nem oldja meg megfelelően a megoldást. Az ugyanazon a virtuális hálózaton belüli SQL virtuális gépekre hivatkozó csatolt kiszolgáló vagy replikáció létrehozásakor a teljes tartománynevet és az alapértelmezett DNS-utótagot is meg kell adni. Például: `SQLVM.internal.cloudapp.net`. További információ: névfeloldás, [amely a saját DNS-kiszolgálóját használja](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A virtuális hálózati DNS-kiszolgálók frissítése nem befolyásolja azonnal a felügyelt példányt. A felügyelt példány DNS-konfigurációja a DHCP-bérlet lejárta vagy a platform verziófrissítése után frissül, attól függően, hogy melyik történik először. **A felhasználóknak javasoljuk, hogy az első felügyelt példány létrehozása előtt állítsa be a virtuális hálózat DNS-konfigurációját.**

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az a felügyelt példány](sql-database-managed-instance.md) ?
- Az új felügyelt példányok létrehozásával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- A felügyelt példányok VNet konfigurálásával kapcsolatos információkért lásd: [VNet-konfiguráció felügyelt](sql-database-managed-instance-connectivity-architecture.md) példányokhoz
