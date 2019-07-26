---
title: Azure Service Bus üzenetkezelés biztonsági attribútumai
description: A Azure Service Bus üzenetkezelés kiértékeléséhez szükséges biztonsági attribútumok ellenőrzőlistája
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443899"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Azure Service Bus üzenetkezelés biztonsági attribútumai

Ez a cikk a Azure Service Bus Üzenetkezelésbe beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)|  Igen, alapértelmezés szerint a kiszolgálóoldali titkosításhoz. | Az ügyfél által felügyelt kulcsok és BYOK még nem támogatottak. Az ügyféloldali titkosítás az ügyfél felelőssége |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Támogatja a szabványos HTTPS/TLS-mechanizmust. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |   |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Az API-hívások [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül történnek. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen (csak prémium szint esetén) | A VNet szolgáltatási végpontok csak [Service Bus prémium szint](service-bus-premium-messaging.md) esetén támogatottak. |
| VNet-befecskendezés támogatása| Nem | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen (csak prémium szint esetén) |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | [Azure monitor és riasztások](service-bus-metrics-azure-monitor.md)használatával támogatott. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Felügyelt [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md); Lásd: [Service Bus hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md).|
| Authorization| Igen | A [RBAC](service-bus-role-based-access-control.md) (előzetes verzió) és az SAS-token használatával támogatja a hitelesítést. Lásd: [Service Bus hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Az operatív naplók elérhetők; Lásd: [Service Bus diagnosztikai naplók](service-bus-diagnostic-logs.md).  |
| Adatsíkok naplózása és naplózása| Nem |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Támogatja az erőforrás-szolgáltatói verziószámozást a [Azure Resource Manager API](/rest/api/resources/)-n keresztül.|
