---
title: A felügyelt példányok felügyeleti végpontjának felderítése Azure SQL Database | Microsoft Docs
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
ms.openlocfilehash: a0d47c61bf84cfb22c767fd09b3feed74f55b7fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567483"
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
