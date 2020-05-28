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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045073"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>A felügyelt Azure SQL-példány beépített tűzfalának ellenőrzése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának [kötelező bejövő biztonsági szabályai](connectivity-architecture-overview.md#mandatory-inbound-security-rules) a 9000, 9003, 1438, 1440, 1452 felügyeleti portok megkövetelését igénylik a hálózati biztonsági csoporton (NSG) található, az SQL által felügyelt példány védelmét biztosító **forrástól** . Habár ezek a portok a NSG szinten vannak megnyitva, a beépített tűzfal hálózati szinten védi azokat.

## <a name="verify-firewall"></a>Tűzfal ellenőrzése

A portok ellenőrzéséhez használja a bármely biztonsági ellenőrzőeszköz eszközt a portok teszteléséhez. Az alábbi képernyőképen az eszközök egyikét láthatja.

![Beépített tűzfal ellenőrzése](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

A felügyelt SQL-példányokkal és-kapcsolatokkal kapcsolatos további információkért lásd: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
