---
title: Az Azure API Management biztonsági attribútumai
description: A API Management értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442282"
---
# <a name="security-attributes-for-api-management"></a>API Management biztonsági attribútumai

Ez a cikk a API Management beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)| Igen (csak a szolgáltatás oldali titkosítás) | A bizalmas adatokat, például a tanúsítványokat, a kulcsokat és a titkos névvel ellátott értékeket a szolgáltatás által felügyelt, a szolgáltatási példány kulcsainak megfelelően titkosítja a rendszer. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az [expressz útvonal](../expressroute/index.yml) -és VNet-titkosítást az [Azure Networking](../virtual-network/index.yml)szolgáltatja. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem | Az összes titkosítási kulcs egy szolgáltatási példányon, a szolgáltatás pedig felügyelt. |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | A felügyeleti sík hívások [Azure Resource Manager](../azure-resource-manager/index.yml) TLS protokollon keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges.  Az adatsík hívások a TLS-vel és az egyik támogatott hitelesítési mechanizmussal (például ügyféltanúsítvány vagy JWT) védhetők.
 |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG-EK) és Azure Application Gateway (vagy más szoftveres készülék) használata. |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | |
| Authorization| Igen | |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Monitor tevékenység naplói](../azure-monitor/platform/activity-logs-overview.md) |
| Adatsíkok naplózása és naplózása| Igen | [Azure monitor diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) és (opcionálisan) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Az [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) használata |

## <a name="vulnerability-scans-false-positives"></a>A biztonsági rés hamis pozitív eredményt vizsgál

Ez a szakasz az Azure API Managementt nem érintő általános biztonsági réseket dokumentálja.

| Biztonsági rés               | Leírás                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | A Ticketbleed a TLS SessionTicket-bővítmény néhány F5-termékben található implementációjában biztonsági réseket tartalmaz. Lehetővé teszi, hogy a kiszivárgás ("vérzés") legfeljebb 31 bájt adat legyen inicializálatlan memóriából. Ezt az okozza, hogy a TLS-verem kitöltése egy munkamenet-azonosító, amely az ügyféltől lett átadva, és az adatok alapján 32 bitet használ. |
