---
title: Felügyelt példány egyéni DNS-e
description: Ez a témakör az Azure SQL Database felügyelt példányokkal rendelkező egyéni DNS konfigurációs beállításait ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247078"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Egyéni DNS konfigurálása egy felügyelt Azure SQL Database-példányhoz

Az Azure SQL Database felügyelt példányát egy Azure [virtuális hálózaton (VNet)](../virtual-network/virtual-networks-overview.md)kell telepíteni. Van néhány forgatókönyv (például db mail, összekapcsolt kiszolgálók más SQL-példányokhoz a felhőben vagy hibrid környezetben), amelyek megkövetelik a magánállomásnevek feloldását a felügyelt példányból. Ebben az esetben egyéni DNS-t kell konfigurálnia az Azure-on belül. 

Mivel a Felügyelt példány ugyanazt a DNS-t használja a belső működéséhez, konfigurálja az egyéni DNS-kiszolgálót úgy, hogy fel tudja oldani a nyilvános tartományneveket.

> [!IMPORTANT]
> Mindig használjon teljesen minősített tartománynevet (FQDN) a levelezési kiszolgálóhoz, az SQL Server-példányhoz és más szolgáltatásokhoz, még akkor is, ha azok a privát DNS-zónán belül vannak. Használja például `smtp.contoso.com` a levelezési `smtp` kiszolgálót, mert nem oldja fel megfelelően. Az ugyanazon a virtuális hálózaton belüli SQL virtuális gépekre hivatkozó csatolt kiszolgáló vagy replikáció létrehozásához fqdn-re és alapértelmezett DNS-utótagra is szükség van. Például: `SQLVM.internal.cloudapp.net`. További információt a [Saját DNS-kiszolgálót használó Névfeloldás](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)című témakörben talál.

> [!IMPORTANT]
> A virtuális hálózati DNS-kiszolgálók frissítése nem befolyásolja azonnal a felügyelt példányt. A felügyelt példány DNS-konfigurációja a DHCP-címbérlet lejárta után vagy a platformfrissítés után frissül , attól függően, hogy melyik következik be előbb. **A felhasználóknak azt tanácsoljuk, hogy az első felügyelt példány létrehozása előtt állítsa be a virtuális hálózati DNS-konfigurációt.**

## <a name="next-steps"></a>További lépések

- Áttekintést a [Mi a felügyelt példány című témakörben talál.](sql-database-managed-instance.md)
- Az új felügyelt példány okainak létrehozásáról a [Felügyelt példány létrehozása című](sql-database-managed-instance-get-started.md)témakörben látható.
- A felügyelt példányok virtuális hálózatának konfigurálásáról a [Felügyelt példányok virtuális hálózatkonfigurációja](sql-database-managed-instance-connectivity-architecture.md) című témakörben talál további információt.
