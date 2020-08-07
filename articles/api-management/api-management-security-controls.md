---
title: Az Azure API Management biztonsági vezérlői
description: Tekintse át a API Management kiértékeléséhez szükséges biztonsági ellenőrzési ellenőrzőlistát. Ezek közé tartozik a hálózat, az identitás és az adatvédelmi szabályozás.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: a147179f7b55e43379b3c3fa3a7a0767cc97b198
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902575"
---
# <a name="security-controls-for-api-management"></a>A API Management biztonsági vezérlői

Ez a cikk a API Management beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Hálózat

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Nem | |  |
| VNet-befecskendezés támogatása| Igen | |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG-EK) és Azure Application Gateway (vagy más szoftveres készülék) használata. |  |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | | |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Monitor tevékenység naplói](../azure-monitor/platform/platform-logs-overview.md) | |
| Adatsíkok naplózása és naplózása| Igen | [Azure monitor diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md) és (opcionálisan) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | |  |
| Engedélyezés| Igen | |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A bizalmas adatokat, például a tanúsítványokat, a kulcsokat és a titkos névvel ellátott értékeket a szolgáltatás által felügyelt, a szolgáltatási példány kulcsainak megfelelően titkosítja a rendszer. |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Az összes titkosítási kulcs egy szolgáltatási példányon, a szolgáltatás pedig felügyelt. |  |
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az [expressz útvonal](../expressroute/index.yml) -és VNet-titkosítást az [Azure Networking](../virtual-network/index.yml)szolgáltatja. |  |
| Titkosított API-hívások| Igen | A felügyeleti sík hívások [Azure Resource Manager](../azure-resource-manager/index.yml) TLS protokollon keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges.  Az adatsík hívások a TLS-vel és az egyik támogatott hitelesítési mechanizmussal (például ügyféltanúsítvány vagy JWT) védhetők. |   |
 |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Az [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) használata |  |

## <a name="vulnerability-scans-false-positives"></a>A biztonsági rés hamis pozitív eredményt vizsgál

Ez a szakasz az Azure API Managementt nem érintő általános biztonsági réseket dokumentálja.

| Biztonsági rés               | Leírás                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | A Ticketbleed a TLS SessionTicket-bővítmény néhány F5-termékben található implementációjában biztonsági réseket tartalmaz. Lehetővé teszi, hogy a kiszivárgás ("vérzés") legfeljebb 31 bájt adat legyen inicializálatlan memóriából. Ezt az okozza, hogy a TLS-verem kitöltése egy munkamenet-azonosító, amely az ügyféltől lett átadva, és az adatok alapján 32 bitet használ. |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).