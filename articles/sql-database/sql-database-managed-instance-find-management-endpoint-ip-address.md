---
title: Felügyelt példányok felügyeleti végpontjának felderítése
description: Ismerje meg, hogyan kérhet Azure SQL Database felügyelt példányok felügyeleti végpontjának nyilvános IP-címét, és ellenőrizheti a beépített tűzfal-védelmet
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73825713"
---
# <a name="determine-the-management-endpoint-ip-address"></a>A felügyeleti végpont IP-címének meghatározása

A Azure SQL Database felügyelt példány virtuális fürt felügyeleti végpontot tartalmaz, amelyet a Microsoft a felügyeleti műveletekhez használ. A felügyeleti végpontot a rendszer egy beépített tűzfallal védi a hálózati szinten, valamint a kölcsönös tanúsítvány-ellenőrzést az alkalmazás szintjén. Meghatározhatja a felügyeleti végpont IP-címét, de ehhez a végponthoz nem férhet hozzá.

A felügyeleti IP-cím meghatározásához hajtson végre DNS-lekérdezést a felügyelt példány `mi-name.zone_id.database.windows.net`teljes tartománynevén:. Ez egy olyan DNS-bejegyzést ad vissza, `trx.region-a.worker.vnet.database.windows.net`amely hasonló. Ezt követően a ". vnet" nevű DNS-lekérdezést törölheti a teljes tartománynévre. Ekkor a rendszer visszaküldi a felügyeleti IP-címet. 

Ez a PowerShell mindent megtesz, ha a mi FQDN \<\> -t a felügyelt példány DNS-bejegyzésével helyettesíti `mi-name.zone_id.database.windows.net`::
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

A felügyelt példányokkal és kapcsolatokkal kapcsolatos további információkért lásd: [Azure SQL Database felügyelt példány kapcsolati architektúrája](sql-database-managed-instance-connectivity-architecture.md).
