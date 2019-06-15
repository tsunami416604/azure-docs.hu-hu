---
title: Gyakori biztonsági attribútumok az Azure Service Bus Relay használatával
description: A gyakori biztonsági attribútumok értékeléséhez az Azure Service Bus Relay ellenőrzőlista
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000147"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Az Azure Service Bus Relay biztonsági attribútumok

Ez a cikk az Azure Service Bus Relay beépített biztonsági attribútumok dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>|  – | Relay egy web socket, és nem marad meg adatokat. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Szolgáltatás szükséges a TLS. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem | Csak a Microsoft a TLS-tanúsítványokat használ.  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Nem |  |
| Kényszerített bújtatás támogatása| – | -Továbbító egy olyan, a TLS-alagút  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Via SAS. |
| Engedélyezés|  Igen | Via SAS. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Keresztül [az Azure Resource Manager](../azure-resource-manager/index.yml). |
| Adatsík naplózása és naplózása| Igen | Kapcsolat sikeres / sikertelen és a hibák és a naplóba.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Keresztül [az Azure Resource Manager](../azure-resource-manager/index.yml).|
