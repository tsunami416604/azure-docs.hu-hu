---
title: Gyakori biztonsági attribútumok az Azure Service Bus-üzenetkezelés
description: A gyakori biztonsági attribútumok értékeléséhez, az Azure Service Bus-üzenetkezelés ellenőrzőlista
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513520"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>Gyakori biztonsági attribútumok az Azure Service Bus-üzenetkezelés

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk a gyakori biztonsági attribútumok az Azure Service Bus-üzenetkezelés beépített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>|  Kiszolgálóoldali titkosítás inaktív alapértelmezés szerint az Igen gombra. | Az ügyfél által felügyelt kulcsokat és a BYOK még nem támogatottak. Az ügyfél feladata ügyféloldali titkosítása |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Támogatja a szabványos HTTPS/TLS-mechanizmust. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |   |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | API-hívás használatával jönnek létre [Azure Resource Manager](../azure-resource-manager/index.yml) és a HTTPS. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen (csak prémium szintű) | Virtuális hálózati Szolgáltatásvégpontok támogatottak [Service Bus prémium szintű](service-bus-premium-messaging.md) csak. |
| VNet-injektálási támogatás| Nem | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen (csak prémium szintű) |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Támogatott [az Azure Monitor és a riasztások](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Kezelhető [Azure Active Directory Felügyeltszolgáltatás-identitás](service-bus-managed-service-identity.md); lásd: [Service Bus-hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md).|
| Engedélyezés| Igen | Engedélyezés a következővel támogatja [RBAC](service-bus-role-based-access-control.md) (előzetes verzió) és a SAS jogkivonat-; lásd: [Service Bus-hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Műveleti naplók érhetők el; Lásd: [a Service Bus-diagnosztikai naplók](service-bus-diagnostic-logs.md).  |
| Adatsík naplózása és naplózása| Nem |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Erőforrás-szolgáltató versioning keresztül támogatja a [Azure Resource Manager API](/rest/api/resources/).|
