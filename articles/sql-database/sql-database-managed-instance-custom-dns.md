---
title: Azure SQL Database felügyelt példány egyéni DNS-je | Microsoft Docs
description: Ez a témakör egy Azure SQL Database felügyelt példánnyal rendelkező egyéni DNS konfigurációs beállításait ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 07/17/2019
ms.openlocfilehash: 674c5d48dad5d3cfd138853d7ea38ae4a216c93d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309865"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Egyéni DNS konfigurálása Azure SQL Database felügyelt példányhoz

Azure SQL Database felügyelt példányt egy Azure [-beli virtuális hálózaton (VNet)](../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Van néhány forgatókönyv (például db-posta, csatolt kiszolgálók a Felhőbeli vagy hibrid környezet más SQL-példányaihoz), amelyekhez privát állomásnevek szükségesek a felügyelt példányból. Ebben az esetben egyéni DNS-t kell konfigurálnia az Azure-on belül. 

Mivel a felügyelt példány ugyanazt a DNS-t használja belső működéséhez, konfigurálnia kell az egyéni DNS-kiszolgálót, hogy fel tudja oldani a nyilvános tartományneveket.

   > [!IMPORTANT]
   > Mindig teljes tartománynevet (FQDN) használjon a levelezési kiszolgálókhoz, az SQL-kiszolgálókhoz és más szolgáltatásokhoz, még akkor is, ha azok a saját DNS-zónán belül vannak. Például a levelezési `smtp.contoso.com` kiszolgáló használata, mert az `smtp` egyszerű nem lesz megfelelően feloldva.

   > [!IMPORTANT]
   > A virtuális hálózati DNS-kiszolgálók frissítése nem érinti azonnal a felügyelt példányt. A felügyelt példány DNS-konfigurációja a DHCP-bérlet lejárta után vagy a platform upgarade után lesz frissítve. **A felhasználóknak javasoljuk, hogy az első felügyelt példány létrehozása előtt állítsa be a virtuális hálózat DNS-konfigurációját.**

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az a felügyelt példány](sql-database-managed-instance.md) ?
- Az új felügyelt példányok létrehozásával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- A felügyelt példányok VNet konfigurálásával kapcsolatos információkért lásd: [VNet-konfiguráció felügyelt](sql-database-managed-instance-connectivity-architecture.md) példányokhoz
