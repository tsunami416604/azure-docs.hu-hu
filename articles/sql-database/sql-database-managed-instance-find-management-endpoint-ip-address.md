---
title: Felügyelt példányfelügyeleti végpont felderítése
description: Megtudhatja, hogy miként szerezheti be az Azure SQL Database felügyelt példánykezelési végpont nyilvános IP-címét, és hogyan ellenőrizheti a beépített tűzfalvédelmet
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825713"
---
# <a name="determine-the-management-endpoint-ip-address"></a>A felügyeleti végpont IP-címének meghatározása

Az Azure SQL Database felügyelt példány virtuális fürt tartalmaz egy felügyeleti végpontot, amelyet a Microsoft a felügyeleti műveletekhez használ. A felügyeleti végpont hálózati szinten beépített tűzfallal és az alkalmazás szintjén kölcsönös tanúsítványellenőrzéssel védett. Meghatározhatja a felügyeleti végpont IP-címét, de nem férhet hozzá ehhez a végponthoz.

A felügyeleti IP-cím meghatározásához végezze el a DNS-keresmel a felügyelt példány fqdn-ját: `mi-name.zone_id.database.windows.net`. Ez olyan DNS-bejegyzést ad `trx.region-a.worker.vnet.database.windows.net`vissza, amely hasonló. Ezután dns-kereséset végezhet ezen a teljes tartománynnon a ".vnet" eltávolításával. Ez visszaadja a felügyeleti IP-címet. 

Ez a PowerShell mindent megtesz az \<Ön számára,\> ha lecseréli az `mi-name.zone_id.database.windows.net`MI FQDN-t a felügyelt példány DNS-bejegyzésére: :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

A felügyelt példányokról és a kapcsolatról az [Azure SQL Database Felügyelt példányok kapcsolatarchitektúrája című témakörben](sql-database-managed-instance-connectivity-architecture.md)talál további információt.
