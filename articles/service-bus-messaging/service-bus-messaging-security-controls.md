---
title: Azure Service Bus üzenetkezelés biztonsági vezérlői
description: A Azure Service Bus üzenetkezelés kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341627"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure Service Bus üzenetkezelés biztonsági vezérlői

Ez a cikk a Azure Service Bus Üzenetkezelésbe beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Igen (csak prémium szint esetén) | A VNet szolgáltatási végpontok csak [Service Bus prémium szint](service-bus-premium-messaging.md) esetén támogatottak. |  |
| VNet-befecskendezés támogatása| No | |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen (csak prémium szint esetén) |  |  |
| Kényszerített bújtatás támogatása| No |  |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | [Azure monitor és riasztások](service-bus-metrics-azure-monitor.md)használatával támogatott. |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes | Az operatív naplók elérhetők.  | [Diagnosztikai naplók Service Bus](service-bus-diagnostic-logs.md) |
| Adatsíkok naplózása és naplózása| No |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Yes | Felügyelt [Azure Active Directory Managed Service Identityon](service-bus-managed-service-identity.md)keresztül.| [Service Bus hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md). |
| Engedélyezés| Yes | A [RBAC](authenticate-application.md) -és Sas-tokenen keresztüli engedélyezést támogatja. | [Service Bus hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  Igen, alapértelmezés szerint a kiszolgálóoldali titkosításhoz. |  |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen. | Az Azure kulcstartóban lévő ügyfél által felügyelt kulcs segítségével titkosíthatja a Service Bus névtérben tárolt adatok inaktív állapotban lévő adattitkosítását. | [Ügyfél által felügyelt kulcsok konfigurálása a Azure Service Bus adatok titkosításához a Azure Portal használatával](configure-customer-managed-key.md)  |
| Oszlop szintű titkosítás (Azure Data Services)| N.A. | |   |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes | Támogatja a szabványos HTTPS/TLS-mechanizmust. |   |
| Titkosított API-hívások| Yes | Az API-hívások [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül történnek. |   |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Yes | Támogatja az erőforrás-szolgáltatói verziószámozást a [Azure Resource Manager API](/rest/api/resources/)-n keresztül.|   |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
