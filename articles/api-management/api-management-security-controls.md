---
title: Biztonsági vezérlők az Azure API Managementhez
description: Az API Management kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751135"
---
# <a name="security-controls-for-api-management"></a>Az API Management biztonsági vezérlői

Ez a cikk az API Management beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| A szolgáltatás végpontjának támogatása| Nem | |  |
| A VNet injekciózás támogatása| Igen | |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | Hálózati biztonsági csoportok (NSG) és az Azure Application Gateway (vagy más szoftverszoftver-berendezés) használata. |  |
| Kényszerített bújtatástámogatása| Igen | Az Azure-hálózatkényszerített bújtatás. |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | | |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | [Az Azure Monitor tevékenységnaplói](../azure-monitor/platform/platform-logs-overview.md) | |
| Adatsík naplózása és naplózása| Igen | [Az Azure Monitor diagnosztikai naplói](../azure-monitor/platform/platform-logs-overview.md) és (opcionálisan) [az Azure Application Insights.](../azure-monitor/app/app-insights-overview.md)  | |


## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | |  |
| Engedélyezés| Igen | |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | A bizalmas adatok, például a tanúsítványok, kulcsok és titkos nevű értékek szolgáltatásáltal felügyelt, szolgáltatáspéldány-kulcsokonként titkosítva vannak. |  |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Minden titkosítási kulcs szolgáltatáspéldányonként és szolgáltatás felügyelt. |  |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | [Az Express Route](../expressroute/index.yml) és a VNet titkosítást az [Azure hálózati szolgáltatásbiztosítja.](../virtual-network/index.yml) |  |
| TITKOSÍTOTT API-hívások| Igen | A felügyeleti síkhívások az [Azure Resource Manager](../azure-resource-manager/index.yml) en keresztül történnek a TLS-en keresztül. Érvényes JSON-webtoken (JWT) szükséges.  Az adatsík-hívások tls-sel és a támogatott hitelesítési mechanizmusokkal (például ügyféltanúsítvánnyal vagy JWT-vel) biztosíthatók. |   |
 |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | Az [Azure API Management DevOps resource kit](https://aka.ms/apimdevops) használata |  |

## <a name="vulnerability-scans-false-positives"></a>A biztonsági rés hamis pozitív

Ez a szakasz az Azure API Managementet nem érintő gyakori biztonsági réseket dokumentálja.

| Biztonsági rés               | Leírás                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | A Ticketbleed biztonsági rést tartalmaz az Egyes F5 termékekben található TLS SessionTicket kiterjesztés megvalósításában. Lehetővé teszi akár 31 bájtnyi adat szivárgását ("vérzés") az inicializálatlan memóriából. Ennek oka az, hogy a TLS verem kitöltése egy munkamenet-azonosító, amelyet az ügyfél, az adatok, hogy ez 32 bit hosszú. |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)