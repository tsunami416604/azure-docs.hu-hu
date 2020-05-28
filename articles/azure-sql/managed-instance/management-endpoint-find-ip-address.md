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
ms.openlocfilehash: 88965c25702917f17a226cfa51de662703136aae
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045080"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>A felügyeleti végpont IP-címének meghatározása – az Azure SQL felügyelt példánya 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának virtuális fürtje olyan felügyeleti végpontot tartalmaz, amelyet az Azure a felügyeleti műveletekhez használ. A felügyeleti végpontot a rendszer egy beépített tűzfallal védi a hálózati szinten, valamint a kölcsönös tanúsítvány-ellenőrzést az alkalmazás szintjén. Meghatározhatja a felügyeleti végpont IP-címét, de ehhez a végponthoz nem férhet hozzá.

A felügyeleti IP-cím meghatározásához hajtson végre [DNS-lekérdezést](/windows-server/administration/windows-commands/nslookup) az SQL felügyelt PÉLDÁNYÁNAK teljes tartománynevén: `mi-name.zone_id.database.windows.net` . Ez egy olyan DNS-bejegyzést ad vissza, amely hasonló `trx.region-a.worker.vnet.database.windows.net` . Ezt követően a ". vnet" nevű DNS-lekérdezést törölheti a teljes tartománynévre. Ekkor a rendszer visszaküldi a felügyeleti IP-címet. 

Ez a PowerShell mindent megtesz, ha \<MI FQDN\> az SQL felügyelt példányának DNS-bejegyzésével helyettesíti: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

A felügyelt SQL-példányokkal és-kapcsolatokkal kapcsolatos további információkért lásd: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
