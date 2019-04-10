---
title: Fedezze fel az Azure SQL Database felügyelt példányába beépített tűzfal |} A Microsoft Docs
description: Megtudhatja, hogyan ellenőrizheti a beépített tűzfalas védelem az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 774455a2901782ef52b213c6a13c17636e28b1a4
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59356698"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>A felügyelt példány beépített tűzfal ellenőrzése

A felügyelt példány [kötelező bejövő biztonsági szabályok](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) igényelnek a felügyeleti portokat 9000, 9003, 1438, 1440, nyissa meg a kívánt 1452 **bármilyen forrásból** lévő a hálózati biztonsági csoport (NSG), amely védelmet nyújt a felügyelt A példány. Bár az ezeket a portokat nyissa meg az NSG-t szintjén, védi azokat a hálózati szinten a beépített tűzfal.

## <a name="verify-firewall"></a>Ellenőrizze a tűzfal

Ezeket a portokat, eszközzel ellenőrizheti bármilyen biztonsági ellenőrzőeszköz teszteléséhez ezeket a portokat. A következő képernyőképen látható, hogyan használhatja az eszközöket.

![Beépített tűzfal ellenőrzése](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

További információ a felügyelt példányok és a kapcsolat: [Azure SQL Database felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).