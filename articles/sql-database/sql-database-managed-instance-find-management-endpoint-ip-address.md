---
title: Felügyelt példányok felügyeleti végpontjának felderítése
description: Ismerje meg, hogyan kérhet Azure SQL Database felügyelt példányok felügyeleti végpontjának nyilvános IP-címét, és ellenőrizheti a beépített tűzfal-védelmet
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: ac4675be25cb5f2f65cc444e65bd25d65d3c3856
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770040"
---
# <a name="determine-the-management-endpoint-ip-address"></a>A felügyeleti végpont IP-címének meghatározása

A Azure SQL Database felügyelt példány virtuális fürt felügyeleti végpontot tartalmaz, amelyet a Microsoft a felügyeleti műveletekhez használ. A felügyeleti végpontot a rendszer egy beépített tűzfallal védi a hálózati szinten, valamint a kölcsönös tanúsítvány-ellenőrzést az alkalmazás szintjén. Meghatározhatja a felügyeleti végpont IP-címét, de ehhez a végponthoz nem férhet hozzá.

A felügyeleti IP-cím meghatározásához hajtson végre DNS-lekérdezést a felügyelt példány teljes tartománynevén: `mi-name.zone_id.database.windows.net` . Ez egy olyan DNS-bejegyzést ad vissza, amely hasonló `trx.region-a.worker.vnet.database.windows.net` . Ezt követően a ". vnet" nevű DNS-lekérdezést törölheti a teljes tartománynévre. Ekkor a rendszer visszaküldi a felügyeleti IP-címet. 

Ez a PowerShell mindent megtesz, ha \< a mi FQDN \> -t a felügyelt példány DNS-bejegyzésével helyettesíti: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

A felügyelt példányokkal és kapcsolatokkal kapcsolatos további információkért lásd: [Azure SQL Database felügyelt példány kapcsolati architektúrája](sql-database-managed-instance-connectivity-architecture.md).
