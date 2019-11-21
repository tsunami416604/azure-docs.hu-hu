---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214904"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Security control | Igen/nem | Megjegyzések |
|---|---|--|
| Service endpoint support| – | |
| VNet injection support| – | |
| Network Isolation and Firewalling support| – |  |
| Forced tunneling support| – | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Igen | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| Igen | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | – |  |

## <a name="identity"></a>Identitáskezelés

| Security control | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| – |  |
| Engedélyezés| – |  |

## <a name="data-protection"></a>Adatvédelem

| Security control | Igen/nem | Megjegyzések |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | – | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| – | |
| Server-side encryption at rest: customer-managed keys (BYOK) | – | |
| Column level encryption (Azure Data Services)| – | |
| API calls encrypted| Igen | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Konfigurációkezelés

| Security control | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| – |  | 

## <a name="next-steps"></a>Következő lépések

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).