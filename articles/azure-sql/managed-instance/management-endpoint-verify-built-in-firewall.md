---
title: A port biztonságának ellenőrzése a beépített tűzfalon
description: Ismerje meg, hogyan ellenőrizheti az Azure SQL felügyelt példányain a beépített tűzfalszabályok védelmét.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 35d0053d8c45547eaa5e89e6da15fcaf0343d6c5
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617298"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>A felügyelt Azure SQL-példány beépített tűzfalának ellenőrzése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának [kötelező bejövő biztonsági szabályaihoz](connectivity-architecture-overview.md#mandatory-inbound-security-rules) a 9000, 9003, 1438, 1440 és 1452 felügyeleti portok szükségesek, hogy az SQL felügyelt példányát védő hálózati biztonsági csoport (NSG) **bármelyik forrásáról** meg lehessen nyitni. Habár ezek a portok a NSG szinten vannak megnyitva, a beépített tűzfal hálózati szinten védi azokat.

## <a name="verify-firewall"></a>Tűzfal ellenőrzése

A portok ellenőrzéséhez használja a bármely biztonsági ellenőrzőeszköz eszközt a portok teszteléséhez. Az alábbi képernyőképen az eszközök egyikét láthatja.

![Beépített tűzfal ellenőrzése](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

További információ az SQL felügyelt példányáról és kapcsolatáról: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
