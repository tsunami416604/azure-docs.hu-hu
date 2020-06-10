---
title: A port biztonságának ellenőrzése a beépített tűzfalon
description: Ismerje meg, hogyan ellenőrizheti az Azure SQL felügyelt példányain a beépített tűzfalszabályok védelmét.
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
ms.openlocfilehash: 561aba31bfd1cc37173f3ee41625ba3687f5e657
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660915"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>A felügyelt Azure SQL-példány beépített tűzfalának ellenőrzése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának [kötelező bejövő biztonsági szabályaihoz](connectivity-architecture-overview.md#mandatory-inbound-security-rules) a 9000, 9003, 1438, 1440 és 1452 felügyeleti portok szükségesek, hogy az SQL felügyelt példányát védő hálózati biztonsági csoport (NSG) **bármelyik forrásáról** meg lehessen nyitni. Habár ezek a portok a NSG szinten vannak megnyitva, a beépített tűzfal hálózati szinten védi azokat.

## <a name="verify-firewall"></a>Tűzfal ellenőrzése

A portok ellenőrzéséhez használja a bármely biztonsági ellenőrzőeszköz eszközt a portok teszteléséhez. Az alábbi képernyőképen az eszközök egyikét láthatja.

![Beépített tűzfal ellenőrzése](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Következő lépések

További információ az SQL felügyelt példányáról és kapcsolatáról: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
