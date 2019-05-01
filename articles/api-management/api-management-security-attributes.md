---
title: Gyakori biztonsági attribútumok az Azure API Management
description: A gyakori biztonsági attribútumok értékeléséhez az API Management ellenőrzőlista
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508137"
---
# <a name="common-security-attributes-for-api-management"></a>Gyakori biztonsági attribútumok az API Management szolgáltatáshoz

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk a gyakori biztonsági attribútumok az API Management épített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen (csak a Szolgáltatásoldali titkosítás) | Bizalmas adatok, például a tanúsítványok, kulcsok és titkos kulcs nevű értékek van titkosítva, szolgáltatás által kezelt, kiszolgálónként kulcsai példánya. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | [Express Route](../expressroute/index.yml) és a virtuális hálózat a titkosítást [az Azure-hálózatok](../virtual-network/index.yml). |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem | Az összes titkosítási kulcs szolgáltatás példányonként és -szolgáltatások által. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Felügyeleti sík hívások használatával jönnek létre [Azure Resource Manager](../azure-resource-manager/index.yml) TLS protokollon keresztüli. Egy érvényes JSON webes jogkivonat (JWT) megadása kötelező.  Adatsík-hívások adatok védve legyenek a TLS és a egy támogatott hitelesítési mechanizmusok (pl. ügyféltanúsítványt vagy JWT).
 |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem | |
| VNet-injektálási támogatás| Igen | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Hálózati biztonsági csoportokkal (NSG) és az Azure Application Gateway (vagy más szoftveralapú berendezés) jelölik. |
| Kényszerített bújtatás támogatása| Igen | Azure-hálózatok biztosítják a kényszerített bújtatás. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | |
| Engedélyezés| Igen | |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | [Az Azure Monitor-Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md) |
| Adatsík naplózása és naplózása| Igen | [Az Azure Monitor-diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) és (opcionálisan) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Használatával a [az Azure API Management fejlesztési és üzemeltetési Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Biztonsági rés megvizsgálja a hamis pozitív jelzések elkerülése

Ez a szakasz gyakori biztonsági rések, amely nem befolyásolja az Azure API Management-dokumentumok.

| Biztonsági rés               | Leírás                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE – 2016-9244) | Ticketbleed a sebezhetőség a TLS SessionTicket kiterjesztés található az egyes F5 termékek végrehajtása során. Lehetővé teszi a ("elvéreztetés") akár 31 bájt nem inicializált memória származó adatok kiszivárgását. Ennek oka a TLS-verem a munkamenet-azonosító, az ügyfél az adatokkal, győződjön meg arról, hogy 32 bites átadott padding hosszú. |