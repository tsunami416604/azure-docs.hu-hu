---
title: Azure Service Bus üzenetkezelés biztonsági vezérlői
description: A Azure Service Bus üzenetkezelés kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903250"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure Service Bus üzenetkezelés biztonsági vezérlői

Ez a cikk a Azure Service Bus Üzenetkezelésbe beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Igen (csak prémium szint esetén) | A VNet szolgáltatási végpontok csak [Service Bus prémium szint](service-bus-premium-messaging.md) esetén támogatottak. |  |
| VNet-befecskendezés támogatása| Nem | |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen (csak prémium szint esetén) |  |  |
| Kényszerített bújtatás támogatása| Nem |  |  |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | [Azure monitor és riasztások](service-bus-metrics-azure-monitor.md)használatával támogatott. |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Az operatív naplók elérhetők.  | [Diagnosztikai naplók Service Bus](service-bus-diagnostic-logs.md) |
| Adatsíkok naplózása és naplózása| Nem |  |

## <a name="identity"></a>Identitáskezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | Felügyelt [Azure Active Directory Managed Service Identityon](service-bus-managed-service-identity.md)keresztül.| [Service Bus hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md). |
| Engedélyezés| Igen | A [RBAC](authenticate-application.md) -és Sas-tokenen keresztüli engedélyezést támogatja. | [Service Bus hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  Igen, alapértelmezés szerint a kiszolgálóoldali titkosításhoz. |  |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen. | Az Azure kulcstartóban lévő ügyfél által felügyelt kulcs segítségével titkosíthatja a Service Bus névtérben tárolt adatok inaktív állapotban lévő adattitkosítását. | [Ügyfél által felügyelt kulcsok konfigurálása a Azure Service Bus adatok titkosításához a Azure Portal használatával](configure-customer-managed-key.md)  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |   |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Támogatja a szabványos HTTPS/TLS-mechanizmust. |   |
| Titkosított API-hívások| Igen | Az API-hívások [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül történnek. |   |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Támogatja az erőforrás-szolgáltatói verziószámozást a [Azure Resource Manager API](/rest/api/resources/)-n keresztül.|   |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
