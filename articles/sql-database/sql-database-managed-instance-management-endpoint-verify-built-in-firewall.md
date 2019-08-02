---
title: A beépített tűzfal Azure SQL Database felügyelt példány felderítése | Microsoft Docs
description: Útmutató a beépített tűzfalbeállítások ellenőrzéséhez Azure SQL Database felügyelt példányban.
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
ms.openlocfilehash: c98b0fd5669140559b4840e157394c2e8c6086ae
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567427"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>A felügyelt példány beépített tűzfalának ellenőrzése

A felügyelt példányok [kötelező bejövő biztonsági szabályai](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) a 9000, 9003, 1438, 1440, 1452 felügyeleti portok megkövetelését igénylik a felügyelt példányt védő hálózati biztonsági csoport (NSG) **bármely forrásból** . Habár ezek a portok a NSG szinten vannak megnyitva, a beépített tűzfal hálózati szinten védi azokat.

## <a name="verify-firewall"></a>Tűzfal ellenőrzése

A portok ellenőrzéséhez használja a bármely biztonsági ellenőrzőeszköz eszközt a portok teszteléséhez. Az alábbi képernyőképen az eszközök egyikét láthatja.

![Beépített tűzfal ellenőrzése](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>További lépések

A felügyelt példányokkal és kapcsolatokkal kapcsolatos további információkért lásd: [Azure SQL Database felügyelt példány kapcsolati architektúrája](sql-database-managed-instance-connectivity-architecture.md).