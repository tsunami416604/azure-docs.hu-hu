---
title: A port biztonságának ellenőrzése a beépített tűzfalon
description: Ismerje meg, hogyan ellenőrizheti az Azure SQL felügyelt példányain a beépített tűzfalszabályok védelmét.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708705"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>A felügyelt Azure SQL-példány beépített tűzfalának ellenőrzése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának [kötelező bejövő biztonsági szabályaihoz](connectivity-architecture-overview.md#mandatory-inbound-security-rules) a 9000, 9003, 1438, 1440 és 1452 felügyeleti portok szükségesek, hogy az SQL felügyelt példányát védő hálózati biztonsági csoport (NSG) **bármelyik forrásáról** meg lehessen nyitni. Habár ezek a portok a NSG szinten vannak megnyitva, a beépített tűzfal hálózati szinten védi azokat.

## <a name="verify-firewall"></a>Tűzfal ellenőrzése

A portok ellenőrzéséhez használja a bármely biztonsági ellenőrzőeszköz eszközt a portok teszteléséhez. Az alábbi képernyőképen az eszközök egyikét láthatja.

![Beépített tűzfal ellenőrzése](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

További információ az SQL felügyelt példányáról és kapcsolatáról: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
