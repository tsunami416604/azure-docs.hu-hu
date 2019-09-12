---
title: Az Azure API Management biztonsági vezérlői
description: A API Management értékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: api-management
author: msmbaldwin
manager: rkarlin
ms.service: api-management
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e808f373ed3c977fb3263bc9e2e25bc602c7a7e1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886517"
---
# <a name="security-controls-for-api-management"></a>A API Management biztonsági vezérlői

Ez a cikk a API Management beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG-EK) és Azure Application Gateway (vagy más szoftveres készülék) használata. |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Monitor tevékenység naplói](../azure-monitor/platform/activity-logs-overview.md) |
| Adatsíkok naplózása és naplózása| Igen | [Azure monitor diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) és (opcionálisan) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | |
| Authorization| Igen | |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A bizalmas adatokat, például a tanúsítványokat, a kulcsokat és a titkos névvel ellátott értékeket a szolgáltatás által felügyelt, a szolgáltatási példány kulcsainak megfelelően titkosítja a rendszer. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Az összes titkosítási kulcs egy szolgáltatási példányon, a szolgáltatás pedig felügyelt. |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az [expressz útvonal](../expressroute/index.yml) -és VNet-titkosítást az [Azure Networking](../virtual-network/index.yml)szolgáltatja. |
| Titkosított API-hívások| Igen | A felügyeleti sík hívások [Azure Resource Manager](../azure-resource-manager/index.yml) TLS protokollon keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges.  Az adatsík hívások a TLS-vel és az egyik támogatott hitelesítési mechanizmussal (például ügyféltanúsítvány vagy JWT) védhetők.
 |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Az [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) használata |

## <a name="vulnerability-scans-false-positives"></a>A biztonsági rés hamis pozitív eredményt vizsgál

Ez a szakasz az Azure API Managementt nem érintő általános biztonsági réseket dokumentálja.

| Biztonsági rés               | Leírás                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | A Ticketbleed a TLS SessionTicket-bővítmény néhány F5-termékben található implementációjában biztonsági réseket tartalmaz. Lehetővé teszi, hogy a kiszivárgás ("vérzés") legfeljebb 31 bájt adat legyen inicializálatlan memóriából. Ezt az okozza, hogy a TLS-verem kitöltése egy munkamenet-azonosító, amely az ügyféltől lett átadva, és az adatok alapján 32 bitet használ. |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).