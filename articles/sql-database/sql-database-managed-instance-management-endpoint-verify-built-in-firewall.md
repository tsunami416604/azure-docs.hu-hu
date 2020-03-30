---
title: Felügyelt példány beépített tűzfalának felderítése
description: Ismerje meg, hogyan ellenőrizheti a beépített tűzfalvédelmet az Azure SQL Database felügyelt példányában.
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
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821804"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>A felügyelt példány beépített tűzfalának ellenőrzése

A felügyelt példány [kötelező bejövő biztonsági szabályai](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) megkövetelik, hogy a 9000, 9003, 1438, 1440, 1452 felügyeleti portok a felügyelt példányt védő hálózati biztonsági csoport (NSG) bármely **forrásából** nyitva legyenek. Bár ezek a portok az NSG szintjén vannak nyitva, a beépített tűzfal hálózati szinten védi őket.

## <a name="verify-firewall"></a>Tűzfal ellenőrzése

A portok ellenőrzéséhez használjon bármilyen biztonsági lapolvasó eszközt a portok teszteléséhez. Az alábbi képernyőkép bemutatja, hogyan használhatja ezeket az eszközöket.

![Beépített tűzfal ellenőrzése](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

A felügyelt példányokról és a kapcsolatról az [Azure SQL Database Felügyelt példányok kapcsolatarchitektúrája című témakörben](sql-database-managed-instance-connectivity-architecture.md)talál további információt.