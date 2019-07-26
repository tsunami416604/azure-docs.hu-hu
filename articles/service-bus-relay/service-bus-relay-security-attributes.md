---
title: Azure Service Bus Relay biztonsági attribútumai
description: A Azure Service Bus Relay kiértékeléséhez szükséges biztonsági attribútumok ellenőrzőlistája
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443887"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Azure Service Bus Relay biztonsági attribútumai

Ez a cikk a Azure Service Bus Relaybe beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)|  – | A Relay egy webes szoftvercsatorna, és nem őrzi meg az adatmegőrzési időt. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A szolgáltatáshoz TLS szükséges. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem | Csak a Microsoft TLS-tanúsítványokat használja.  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |
| Kényszerített bújtatás támogatása| – | A Relay a TLS-alagút  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | SAS-n keresztül. |
| Authorization|  Igen | SAS-n keresztül. |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |
| Adatsíkok naplózása és naplózása| Igen | A sikeres és sikertelen kapcsolatok, valamint a naplózott hibák.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül.|
