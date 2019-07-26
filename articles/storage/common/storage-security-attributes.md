---
title: Az Azure Storage biztonsági attribútumai
description: Az Azure Storage kiértékeléséhez szükséges biztonsági attribútumok ellenőrzőlistája
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 16ec2757955b53a8bfa73ba724100f7fa61d2867
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444300"
---
# <a name="security-attributes-for-azure-storage"></a>Az Azure Storage biztonsági attribútumai

Ez a cikk az Azure Storage-ba beépített biztonsági attribútumokat dokumentálja. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)| Igen |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A szabványos HTTPS/TLS-mechanizmusok támogatása.  A felhasználók a szolgáltatásba való továbbítás előtt is titkosítani tudják az adatokat. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Lásd: [Storage Service encryption az ügyfél által felügyelt kulcsokkal Azure Key Vaultban](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Titkosított API-hívások| Igen |  |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| – |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | |
| Kényszerített bújtatás támogatása| – |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Azure Monitor a metrikák mostantól elérhetők, az előzetes verziójú naplók |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Azure Active Directory, megosztott kulcs, közös hozzáférési jogkivonat. |
| Authorization| Igen | Támogatás engedélyezése RBAC, POSIX ACL-eken és SAS-tokeneken keresztül |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása | Igen | Azure Resource Manager tevékenység naplója |
| Adatsíkok naplózása és naplózása| Igen | Szolgáltatás-diagnosztikai naplók és Azure Monitor naplózás indítása – előzetes verzió  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Azure Resource Manager API-k támogatása az erőforrás-szolgáltatónál |