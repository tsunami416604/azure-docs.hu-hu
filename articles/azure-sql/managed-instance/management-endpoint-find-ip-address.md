---
title: Felügyeleti végpont IP-címének felderítése
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg, hogyan kérheti le az Azure SQL felügyelt példányok felügyeleti végpontjának nyilvános IP-címét, és ellenőrizze a beépített tűzfal védelmét
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: f63167824a06a877456db4a18622710922e026ff
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659261"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>A felügyeleti végpont IP-címének meghatározása – az Azure SQL felügyelt példánya 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának virtuális fürtje olyan felügyeleti végpontot tartalmaz, amelyet az Azure a felügyeleti műveletekhez használ. A felügyeleti végpontot a rendszer egy beépített tűzfallal védi a hálózati szinten, valamint a kölcsönös tanúsítvány-ellenőrzést az alkalmazás szintjén. Meghatározhatja a felügyeleti végpont IP-címét, de ehhez a végponthoz nem férhet hozzá.

A felügyeleti IP-cím meghatározásához hajtson végre [DNS-lekérdezést](/windows-server/administration/windows-commands/nslookup) az SQL felügyelt PÉLDÁNYÁNAK teljes tartománynevén: `mi-name.zone_id.database.windows.net` . Ez egy olyan DNS-bejegyzést ad vissza, amely hasonló `trx.region-a.worker.vnet.database.windows.net` . Ezt követően a ". vnet" nevű DNS-lekérdezést törölheti a teljes tartománynévre. Ekkor a rendszer visszaküldi a felügyeleti IP-címet. 

Ez a PowerShell-kód mindent megtesz, ha \<MI FQDN\> az SQL felügyelt példányának DNS-bejegyzésével helyettesíti a következőt: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

További információ az SQL felügyelt példányáról és kapcsolatáról: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
